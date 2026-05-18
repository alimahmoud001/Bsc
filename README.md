
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>محفظة BSC - بناء المعاملات</title>
    <script src="https://cdn.jsdelivr.net/npm/buffer@6.0.3/index.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/ethers@6.11.0/dist/ethers.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
        }

        .header {
            text-align: center;
            color: white;
            margin-bottom: 30px;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .card {
            background: white;
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
        }

        .card h2 {
            color: #667eea;
            margin-bottom: 20px;
            font-size: 1.4em;
            border-bottom: 2px solid #667eea;
            padding-bottom: 10px;
        }

        .form-group {
            margin-bottom: 18px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #333;
        }

        input, textarea, select {
            width: 100%;
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 1em;
            font-family: 'Courier New', monospace;
            transition: border-color 0.3s;
        }

        input:focus, textarea:focus, select:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        textarea {
            resize: vertical;
            min-height: 80px;
        }

        button {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            width: 100%;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
        }

        .btn-secondary {
            background: #f0f0f0;
            color: #333;
            border: 2px solid #ddd;
            width: 100%;
            margin-top: 10px;
        }

        .btn-secondary:hover {
            background: #e0e0e0;
        }

        .btn-small {
            padding: 8px 15px;
            font-size: 0.9em;
            width: auto;
            margin-top: 10px;
        }

        .balance-box {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
            text-align: center;
        }

        .balance-box h3 {
            font-size: 0.9em;
            opacity: 0.9;
            margin-bottom: 8px;
        }

        .balance-box .amount {
            font-size: 1.6em;
            font-weight: bold;
            font-family: 'Courier New', monospace;
        }

        .hidden {
            display: none !important;
        }

        .alert {
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 15px;
            display: none;
        }

        .alert.show {
            display: block;
        }

        .alert.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .alert.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .alert.info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }

        .qr-container {
            text-align: center;
            padding: 20px;
            background: #f9f9f9;
            border-radius: 8px;
            margin: 15px 0;
        }

        .qr-container canvas {
            max-width: 100%;
            border: 2px solid #ddd;
            border-radius: 8px;
            padding: 10px;
            background: white;
        }

        .grid-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        @media (max-width: 768px) {
            .grid-2 {
                grid-template-columns: 1fr;
            }

            .header h1 {
                font-size: 1.8em;
            }

            input, textarea, select {
                font-size: 16px;
            }
        }

        .info-box {
            background: #e3f2fd;
            border-left: 4px solid #2196F3;
            padding: 12px;
            margin-bottom: 15px;
            border-radius: 4px;
            color: #1565c0;
            font-size: 0.95em;
        }

        .token-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .token-btn {
            flex: 1;
            padding: 10px;
            border: 2px solid #ddd;
            background: white;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s;
        }

        .token-btn.active {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border-color: #667eea;
        }

        .slider-container {
            margin: 15px 0;
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            border-radius: 5px;
            background: #ddd;
            outline: none;
            -webkit-appearance: none;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #667eea;
            cursor: pointer;
        }

        input[type="range"]::-moz-range-thumb {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #667eea;
            cursor: pointer;
            border: none;
        }

        .slider-value {
            display: flex;
            justify-content: space-between;
            margin-top: 8px;
            padding: 8px;
            background: #f5f5f5;
            border-radius: 6px;
            font-weight: 600;
            color: #667eea;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>💰 محفظة BSC</h1>
            <p>بناء وإرسال المعاملات على Binance Smart Chain</p>
        </div>

        <!-- Login Section -->
        <div class="card" id="loginSection">
            <h2>🔐 تسجيل الدخول</h2>
            <div class="info-box">
                أدخل عنوان محفظتك (المفتاح العام) - لا تدخل المفتاح الخاص
            </div>
            <div class="form-group">
                <label>عنوان المحفظة (0x...):</label>
                <input type="text" id="walletAddress" placeholder="0x..." />
            </div>
            <button class="btn-primary" onclick="connectWallet()">دخول</button>
            <div id="loginAlert" class="alert"></div>
        </div>

        <!-- Main Section -->
        <div class="card hidden" id="mainSection">
            <h2>📊 معلومات المحفظة</h2>
            
            <div class="balance-box">
                <h3>رصيد BNB</h3>
                <div class="amount" id="bnbBalance">0.0000</div>
            </div>

            <div class="balance-box">
                <h3>رصيد USDT</h3>
                <div class="amount" id="usdtBalance">0.00</div>
            </div>

            <button class="btn-secondary" onclick="refreshBalances()">🔄 تحديث الأرصدة</button>
        </div>

        <!-- Transaction Builder -->
        <div class="card hidden" id="txBuilderSection">
            <h2>📝 بناء المعاملة</h2>

            <div class="token-selector">
                <button class="token-btn active" onclick="selectToken('BNB', this)">BNB</button>
                <button class="token-btn" onclick="selectToken('USDT', this)">USDT</button>
            </div>

            <div class="form-group">
                <label>عنوان المستقبل (To):</label>
                <input type="text" id="toAddress" placeholder="0x..." />
            </div>

            <div class="form-group">
                <label>المبلغ:</label>
                <input type="number" id="amount" placeholder="0.00" step="0.0001" min="0" />
            </div>

            <div class="slider-container">
                <label>نسبة من الرصيد:</label>
                <input type="range" id="percentSlider" min="0" max="100" value="0" />
                <div class="slider-value">
                    <span id="percentLabel">0%</span>
                    <span id="percentAmount">0.00</span>
                </div>
            </div>

            <div class="grid-2">
                <div class="form-group">
                    <label>Gas Limit:</label>
                    <input type="number" id="gasLimit" value="21000" min="21000" />
                </div>
                <div class="form-group">
                    <label>Gas Price (Gwei):</label>
                    <input type="number" id="gasPrice" value="5" step="0.1" min="0" />
                </div>
            </div>

            <button class="btn-primary" onclick="buildTransaction()">بناء المعاملة</button>
            <div id="buildAlert" class="alert"></div>
        </div>

        <!-- Unsigned Transaction Display -->
        <div class="card hidden" id="txDisplaySection">
            <h2>✍️ المعاملة غير الموقعة</h2>

            <div class="info-box">
                انسخ البيانات أدناه وأرسلها إلى جهاز التوقيع
            </div>

            <div class="form-group">
                <label>بيانات المعاملة (JSON):</label>
                <textarea id="txData" readonly></textarea>
                <button class="btn-small" onclick="copyToClipboard('txData')">📋 نسخ</button>
            </div>

            <div class="form-group">
                <label>QR Code:</label>
                <div class="qr-container" id="qrContainer"></div>
                <button class="btn-small" onclick="generateQR()">📱 إنشاء QR Code</button>
            </div>

            <button class="btn-secondary" onclick="resetAll()">إعادة تعيين</button>
        </div>

        <!-- Signed Transaction Input -->
        <div class="card hidden" id="signedTxSection">
            <h2>✅ المعاملة الموقعة</h2>

            <div class="info-box">
                الصق المعاملة الموقعة من جهاز التوقيع
            </div>

            <div class="form-group">
                <label>المعاملة الموقعة:</label>
                <textarea id="signedTx" placeholder="الصق المعاملة الموقعة هنا..."></textarea>
            </div>

            <button class="btn-primary" onclick="broadcastTx()">📤 إرسال المعاملة</button>
            <div id="broadcastAlert" class="alert"></div>
        </div>
    </div>

    <script>
        const BSC_RPC = 'https://bsc-dataseed1.binance.org:443';
        const USDT_ADDRESS = '0x55d398326f99059fF775485246999027B3197955';
        const USDT_ABI = [
            'function balanceOf(address owner) view returns (uint256)',
            'function decimals() view returns (uint8)'
        ];

        let provider = null;
        let currentAddress = null;
        let selectedToken = 'BNB';
        let bnbBalanceValue = '0';
        let usdtBalanceValue = '0';
        let currentTx = null;

        // Initialize provider
        async function initProvider() {
            try {
                provider = new ethers.JsonRpcProvider(BSC_RPC);
                console.log('Provider initialized');
            } catch (error) {
                console.error('Provider error:', error);
                showAlert('loginAlert', 'خطأ في الاتصال بالشبكة', 'error');
            }
        }

        // Connect wallet
        async function connectWallet() {
            const address = document.getElementById('walletAddress').value.trim();
            
            if (!address.startsWith('0x') || address.length !== 42) {
                showAlert('loginAlert', 'عنوان غير صحيح (يجب أن يبدأ بـ 0x ويكون 42 حرف)', 'error');
                return;
            }

            if (!ethers.isAddress(address)) {
                showAlert('loginAlert', 'عنوان غير صحيح', 'error');
                return;
            }

            try {
                await initProvider();
                currentAddress = address;
                document.getElementById('walletAddress').value = '';

                // Show main sections
                document.getElementById('loginSection').classList.add('hidden');
                document.getElementById('mainSection').classList.remove('hidden');
                document.getElementById('txBuilderSection').classList.remove('hidden');
                document.getElementById('signedTxSection').classList.remove('hidden');

                showAlert('loginAlert', 'تم الدخول بنجاح!', 'success');
                
                // Refresh balances
                setTimeout(() => refreshBalances(), 500);
            } catch (error) {
                showAlert('loginAlert', 'خطأ: ' + error.message, 'error');
            }
        }

        // Refresh balances
        async function refreshBalances() {
            if (!provider || !currentAddress) return;

            try {
                // Get BNB balance
                const bnbBal = await provider.getBalance(currentAddress);
                bnbBalanceValue = ethers.formatEther(bnbBal);
                document.getElementById('bnbBalance').textContent = parseFloat(bnbBalanceValue).toFixed(4);

                // Get USDT balance
                try {
                    const usdtContract = new ethers.Contract(USDT_ADDRESS, USDT_ABI, provider);
                    const usdtBal = await usdtContract.balanceOf(currentAddress);
                    const decimals = await usdtContract.decimals();
                    usdtBalanceValue = ethers.formatUnits(usdtBal, decimals);
                    document.getElementById('usdtBalance').textContent = parseFloat(usdtBalanceValue).toFixed(2);
                } catch (e) {
                    console.error('USDT error:', e);
                    document.getElementById('usdtBalance').textContent = '0.00';
                }

                console.log('Balances updated');
            } catch (error) {
                console.error('Balance error:', error);
            }
        }

        // Select token
        function selectToken(token, btn) {
            selectedToken = token;
            document.querySelectorAll('.token-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');

            // Update gas limit
            document.getElementById('gasLimit').value = token === 'USDT' ? '65000' : '21000';
        }

        // Handle slider
        document.addEventListener('DOMContentLoaded', function() {
            const slider = document.getElementById('percentSlider');
            slider.addEventListener('input', function() {
                const percent = this.value;
                const balance = selectedToken === 'BNB' ? bnbBalanceValue : usdtBalanceValue;
                const amount = (parseFloat(balance) * percent / 100).toFixed(4);
                
                document.getElementById('percentLabel').textContent = percent + '%';
                document.getElementById('percentAmount').textContent = amount;
                document.getElementById('amount').value = amount;
            });
        });

        // Build transaction
        async function buildTransaction() {
            const to = document.getElementById('toAddress').value.trim();
            const amount = document.getElementById('amount').value;
            const gasLimit = document.getElementById('gasLimit').value;
            const gasPrice = document.getElementById('gasPrice').value;

            if (!to.startsWith('0x') || to.length !== 42) {
                showAlert('buildAlert', 'عنوان المستقبل غير صحيح', 'error');
                return;
            }

            if (!amount || parseFloat(amount) <= 0) {
                showAlert('buildAlert', 'المبلغ يجب أن يكون أكبر من صفر', 'error');
                return;
            }

            try {
                let txData;

                if (selectedToken === 'BNB') {
                    txData = {
                        to: to,
                        from: currentAddress,
                        value: ethers.parseEther(amount).toString(),
                        gasLimit: gasLimit,
                        gasPrice: ethers.parseUnits(gasPrice, 'gwei').toString(),
                        chainId: 56,
                        nonce: await provider.getTransactionCount(currentAddress),
                        type: 2
                    };
                } else {
                    // USDT transfer
                    const usdtContract = new ethers.Contract(USDT_ADDRESS, USDT_ABI, provider);
                    const iface = new ethers.Interface(['function transfer(address to, uint256 amount)']);
                    const data = iface.encodeFunctionData('transfer', [to, ethers.parseUnits(amount, 18)]);

                    txData = {
                        to: USDT_ADDRESS,
                        from: currentAddress,
                        data: data,
                        value: '0',
                        gasLimit: gasLimit,
                        gasPrice: ethers.parseUnits(gasPrice, 'gwei').toString(),
                        chainId: 56,
                        nonce: await provider.getTransactionCount(currentAddress),
                        type: 2
                    };
                }

                currentTx = txData;
                document.getElementById('txData').value = JSON.stringify(txData, null, 2);
                document.getElementById('txDisplaySection').classList.remove('hidden');
                showAlert('buildAlert', 'تم بناء المعاملة بنجاح!', 'success');
            } catch (error) {
                showAlert('buildAlert', 'خطأ: ' + error.message, 'error');
            }
        }

        // Generate QR
        function generateQR() {
            const txJson = document.getElementById('txData').value;
            const qrContainer = document.getElementById('qrContainer');
            qrContainer.innerHTML = '';

            QRCode.toCanvas(document.createElement('canvas'), txJson, {
                errorCorrectionLevel: 'H',
                type: 'image/png',
                width: 300,
                margin: 2,
                color: { dark: '#000000', light: '#ffffff' }
            }, function(error, canvas) {
                if (error) {
                    console.error('QR error:', error);
                    return;
                }
                qrContainer.appendChild(canvas);
            });
        }

        // Copy to clipboard
        function copyToClipboard(elementId) {
            const element = document.getElementById(elementId);
            element.select();
            document.execCommand('copy');
            alert('تم النسخ إلى الحافظة!');
        }

        // Broadcast transaction
        async function broadcastTx() {
            const signedTx = document.getElementById('signedTx').value.trim();

            if (!signedTx) {
                showAlert('broadcastAlert', 'الرجاء إدخال المعاملة الموقعة', 'error');
                return;
            }

            try {
                const tx = await provider.broadcastTransaction(signedTx);
                showAlert('broadcastAlert', 'تم إرسال المعاملة! Hash: ' + tx.hash, 'success');
                document.getElementById('signedTx').value = '';
            } catch (error) {
                showAlert('broadcastAlert', 'خطأ: ' + error.message, 'error');
            }
        }

        // Reset all
        function resetAll() {
            currentAddress = null;
            currentTx = null;
            document.getElementById('walletAddress').value = '';
            document.getElementById('toAddress').value = '';
            document.getElementById('amount').value = '';
            document.getElementById('signedTx').value = '';

            document.getElementById('loginSection').classList.remove('hidden');
            document.getElementById('mainSection').classList.add('hidden');
            document.getElementById('txBuilderSection').classList.add('hidden');
            document.getElementById('txDisplaySection').classList.add('hidden');
            document.getElementById('signedTxSection').classList.add('hidden');
        }

        // Show alert
        function showAlert(elementId, message, type) {
            const element = document.getElementById(elementId);
            element.textContent = message;
            element.className = 'alert show ' + type;
            setTimeout(() => {
                element.className = 'alert';
            }, 5000);
        }

        // Initialize on load
        window.addEventListener('load', initProvider);
    </script>
</body>
</html>
