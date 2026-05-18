
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>محفظة BSC - إنشاء المعاملات</title>
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
            color: #333;
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
            text-shadow: 2px 2px 4px rgba(0,0,0,0.2);
        }

        .header p {
            font-size: 1.1em;
            opacity: 0.9;
        }

        .card {
            background: white;
            border-radius: 15px;
            padding: 30px;
            margin-bottom: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 50px rgba(0,0,0,0.3);
        }

        .card h2 {
            color: #667eea;
            margin-bottom: 20px;
            font-size: 1.5em;
            border-bottom: 2px solid #667eea;
            padding-bottom: 10px;
        }

        .form-group {
            margin-bottom: 20px;
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
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 1em;
            transition: border-color 0.3s ease;
            font-family: 'Courier New', monospace;
        }

        input:focus, textarea:focus, select:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        textarea {
            resize: vertical;
            min-height: 100px;
            font-family: 'Courier New', monospace;
        }

        .button-group {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 20px;
        }

        button {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
        }

        .btn-primary:active {
            transform: translateY(0);
        }

        .btn-secondary {
            background: #f0f0f0;
            color: #333;
            border: 2px solid #e0e0e0;
        }

        .btn-secondary:hover {
            background: #e0e0e0;
            border-color: #667eea;
        }

        .balance-display {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
        }

        .balance-card {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
        }

        .balance-card h3 {
            font-size: 0.9em;
            opacity: 0.9;
            margin-bottom: 10px;
        }

        .balance-card .amount {
            font-size: 1.8em;
            font-weight: bold;
            font-family: 'Courier New', monospace;
        }

        .slider-container {
            margin: 20px 0;
        }

        .slider-container label {
            margin-bottom: 15px;
        }

        input[type="range"] {
            width: 100%;
            height: 8px;
            border-radius: 5px;
            background: #e0e0e0;
            outline: none;
            -webkit-appearance: none;
            appearance: none;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 25px;
            height: 25px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            cursor: pointer;
            box-shadow: 0 2px 10px rgba(102, 126, 234, 0.4);
        }

        input[type="range"]::-moz-range-thumb {
            width: 25px;
            height: 25px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            cursor: pointer;
            border: none;
            box-shadow: 0 2px 10px rgba(102, 126, 234, 0.4);
        }

        .slider-value {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 10px;
            padding: 10px;
            background: #f5f5f5;
            border-radius: 8px;
        }

        .slider-value span {
            font-weight: bold;
            color: #667eea;
            font-size: 1.1em;
        }

        .alert {
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: none;
        }

        .alert.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
            display: block;
        }

        .alert.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
            display: block;
        }

        .alert.info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
            display: block;
        }

        .transaction-data {
            background: #f5f5f5;
            padding: 15px;
            border-radius: 8px;
            margin-top: 15px;
            font-family: 'Courier New', monospace;
            font-size: 0.9em;
            word-break: break-all;
            max-height: 200px;
            overflow-y: auto;
        }

        .copy-button {
            background: #667eea;
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 0.9em;
            margin-top: 10px;
        }

        .copy-button:hover {
            background: #764ba2;
        }

        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-left: 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .hidden {
            display: none;
        }

        @media (max-width: 768px) {
            .header h1 {
                font-size: 1.8em;
            }

            .card {
                padding: 20px;
            }

            .balance-display {
                grid-template-columns: 1fr;
            }

            .button-group {
                grid-template-columns: 1fr;
            }

            input, textarea, select {
                font-size: 16px; /* Prevents zoom on iOS */
            }
        }

        .info-box {
            background: #e3f2fd;
            border-left: 4px solid #2196F3;
            padding: 15px;
            margin-bottom: 20px;
            border-radius: 4px;
            color: #1565c0;
        }

        .token-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .token-btn {
            flex: 1;
            padding: 12px;
            border: 2px solid #e0e0e0;
            background: white;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        .token-btn.active {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border-color: #667eea;
        }

        .token-btn:hover {
            border-color: #667eea;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>💰 محفظة BSC</h1>
            <p>إدارة BNB و USDT على شبكة Binance Smart Chain</p>
        </div>

        <!-- Login Card -->
        <div class="card" id="loginCard">
            <h2>🔐 تسجيل الدخول</h2>
            <div class="info-box">
                ⚠️ أدخل المفتاح العام الفرعي (Derived Public Key) فقط - لا تدخل المفتاح الخاص الرئيسي
            </div>
            <div class="form-group">
                <label for="publicKey">المفتاح العام الفرعي (Derived Public Key):</label>
                <input type="text" id="publicKey" placeholder="0x..." required>
            </div>
            <button class="btn-primary" onclick="loginWallet()">دخول المحفظة</button>
            <div id="loginAlert" class="alert"></div>
        </div>

        <!-- Main Wallet Card -->
        <div class="card hidden" id="walletCard">
            <h2>📊 معلومات المحفظة</h2>
            
            <div class="form-group">
                <label>عنوان المحفظة:</label>
                <input type="text" id="walletAddress" readonly>
            </div>

            <div class="balance-display">
                <div class="balance-card">
                    <h3>رصيد BNB</h3>
                    <div class="amount" id="bnbBalance">0.00</div>
                </div>
                <div class="balance-card">
                    <h3>رصيد USDT</h3>
                    <div class="amount" id="usdtBalance">0.00</div>
                </div>
            </div>

            <button class="btn-secondary" onclick="refreshBalance()">🔄 تحديث الأرصدة</button>
        </div>

        <!-- Transaction Builder Card -->
        <div class="card hidden" id="transactionCard">
            <h2>📝 بناء المعاملة</h2>

            <div class="token-selector">
                <button class="token-btn active" onclick="selectToken('BNB')">BNB</button>
                <button class="token-btn" onclick="selectToken('USDT')">USDT</button>
            </div>

            <div class="form-group">
                <label for="recipientAddress">عنوان المستقبل:</label>
                <input type="text" id="recipientAddress" placeholder="0x..." required>
            </div>

            <div class="form-group">
                <label for="amount">المبلغ:</label>
                <input type="number" id="amount" placeholder="0.00" step="0.0001" min="0" required>
            </div>

            <!-- Slider for percentage -->
            <div class="slider-container">
                <label for="percentageSlider">اختر نسبة مئوية من الرصيد:</label>
                <input type="range" id="percentageSlider" min="0" max="100" value="0" step="1">
                <div class="slider-value">
                    <span id="percentageValue">0%</span>
                    <span id="calculatedAmount">0.00</span>
                </div>
            </div>

            <div class="form-group">
                <label for="gasLimit">حد الغاز (Gas Limit):</label>
                <input type="number" id="gasLimit" value="21000" min="21000" required>
            </div>

            <div class="form-group">
                <label for="gasPrice">سعر الغاز (Gwei):</label>
                <input type="number" id="gasPrice" placeholder="5" step="0.1" min="0" required>
            </div>

            <div class="button-group">
                <button class="btn-primary" onclick="buildTransaction()">بناء المعاملة</button>
                <button class="btn-secondary" onclick="clearTransaction()">مسح</button>
            </div>

            <div id="transactionAlert" class="alert"></div>
        </div>

        <!-- Unsigned Transaction Display -->
        <div class="card hidden" id="unsignedCard">
            <h2>✍️ المعاملة غير الموقعة</h2>
            <div class="info-box">
                💡 انسخ البيانات أدناه وأرسلها إلى جهاز التوقيع (الملف الثاني) عبر QR Code
            </div>
            
            <div class="form-group">
                <label>بيانات المعاملة (JSON):</label>
                <textarea id="unsignedTransaction" readonly></textarea>
                <button class="copy-button" onclick="copyToClipboard('unsignedTransaction')">📋 نسخ البيانات</button>
            </div>

            <div class="form-group">
                <label>أو كنص مشفر:</label>
                <textarea id="encodedTransaction" readonly></textarea>
                <button class="copy-button" onclick="copyToClipboard('encodedTransaction')">📋 نسخ النص المشفر</button>
            </div>

            <div class="button-group">
                <button class="btn-primary" onclick="generateQRCode()">📱 إنشاء QR Code</button>
                <button class="btn-secondary" onclick="resetWallet()">🚪 تسجيل الخروج</button>
            </div>
        </div>

        <!-- QR Code Display -->
        <div class="card hidden" id="qrCard">
            <h2>📲 رمز QR للمعاملة</h2>
            <div style="text-align: center; margin: 20px 0;">
                <div id="qrCode" style="display: inline-block; padding: 20px; background: white; border-radius: 10px;"></div>
            </div>
            <p style="text-align: center; color: #666; margin-top: 15px;">
                امسح هذا الرمز من جهاز التوقيع لتوقيع المعاملة
            </p>
            <button class="btn-secondary" onclick="document.getElementById('qrCard').classList.add('hidden')">إغلاق</button>
        </div>

        <!-- Signed Transaction Input -->
        <div class="card hidden" id="signedInputCard">
            <h2>✅ إدخال المعاملة الموقعة</h2>
            <div class="info-box">
                💡 الصق المعاملة الموقعة من جهاز التوقيع هنا
            </div>
            
            <div class="form-group">
                <label for="signedTransaction">المعاملة الموقعة:</label>
                <textarea id="signedTransaction" placeholder="الصق المعاملة الموقعة هنا..."></textarea>
            </div>

            <div class="button-group">
                <button class="btn-primary" onclick="broadcastTransaction()">📤 إرسال المعاملة</button>
                <button class="btn-secondary" onclick="clearSignedTransaction()">مسح</button>
            </div>

            <div id="broadcastAlert" class="alert"></div>
        </div>
    </div>

    <script>
        // Constants
        const BSC_RPC = 'https://bsc-dataseed1.binance.org:443';
        const USDT_ADDRESS = '0x55d398326f99059fF775485246999027B3197955';
        const USDT_ABI = [
            'function balanceOf(address owner) view returns (uint256)',
            'function transfer(address to, uint256 amount) returns (bool)',
            'function decimals() view returns (uint8)'
        ];

        let provider;
        let currentAddress = null;
        let currentToken = 'BNB';
        let currentBalance = '0';

        // Initialize provider
        async function initProvider() {
            try {
                provider = new ethers.JsonRpcProvider(BSC_RPC);
                console.log('Provider تم تهيئته بنجاح');
            } catch (error) {
                console.error('خطأ في تهيئة Provider:', error);
                throw error;
            }
        }

        // Login to wallet
        async function loginWallet() {
            const publicKey = document.getElementById('publicKey').value.trim();
            const loginAlert = document.getElementById('loginAlert');

            if (!publicKey.startsWith('0x') || publicKey.length !== 42) {
                showAlert(loginAlert, 'عنوان غير صحيح. يجب أن يبدأ بـ 0x ويكون 42 حرف', 'error');
                return;
            }

            try {
                await initProvider();
                currentAddress = publicKey;

                // Verify address is valid
                if (!ethers.isAddress(currentAddress)) {
                    throw new Error('عنوان غير صحيح');
                }

                document.getElementById('publicKey').value = '';
                document.getElementById('walletAddress').value = currentAddress;
                
                showAlert(loginAlert, 'تم تسجيل الدخول بنجاح!', 'success');
                
                // Hide login card and show wallet cards
                document.getElementById('loginCard').classList.add('hidden');
                document.getElementById('walletCard').classList.remove('hidden');
                document.getElementById('transactionCard').classList.remove('hidden');
                document.getElementById('signedInputCard').classList.remove('hidden');

                // Refresh balance
                setTimeout(async () => {
                    await refreshBalance();
                }, 500);
            } catch (error) {
                showAlert(loginAlert, 'خطأ: ' + error.message, 'error');
            }
        }

        // Refresh balance
        async function refreshBalance() {
            try {
                if (!provider || !currentAddress) {
                    console.error('Provider أو العنوان غير مهيأ');
                    return;
                }

                // Get BNB balance
                const bnbBalance = await provider.getBalance(currentAddress);
                const bnbFormatted = ethers.formatEther(bnbBalance);
                console.log('BNB Balance:', bnbFormatted);

                // Get USDT balance
                const usdtContract = new ethers.Contract(USDT_ADDRESS, USDT_ABI, provider);
                const usdtBalance = await usdtContract.balanceOf(currentAddress);
                const decimals = await usdtContract.decimals();
                const usdtFormatted = ethers.formatUnits(usdtBalance, decimals);
                console.log('USDT Balance:', usdtFormatted);

                const bnbElement = document.getElementById('bnbBalance');
                const usdtElement = document.getElementById('usdtBalance');
                
                if (bnbElement) bnbElement.textContent = parseFloat(bnbFormatted).toFixed(4);
                if (usdtElement) usdtElement.textContent = parseFloat(usdtFormatted).toFixed(2);

                currentBalance = currentToken === 'BNB' ? bnbFormatted : usdtFormatted;
                
                console.log('تم تحديث الأرصدة بنجاح');
            } catch (error) {
                console.error('خطأ في تحديث الرصيد:', error);
                alert('خطأ في تحديث الأرصدة: ' + error.message);
            }
        }

        // Select token
        function selectToken(token) {
            currentToken = token;
            
            // Update button styles
            document.querySelectorAll('.token-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');

            // Update gas limit for USDT
            if (token === 'USDT') {
                document.getElementById('gasLimit').value = '65000';
            } else {
                document.getElementById('gasLimit').value = '21000';
            }

            // Update balance display
            const bnbBalance = document.getElementById('bnbBalance').textContent;
            const usdtBalance = document.getElementById('usdtBalance').textContent;
            currentBalance = token === 'BNB' ? bnbBalance : usdtBalance;
        }

        // Slider handler
        document.addEventListener('DOMContentLoaded', function() {
            const slider = document.getElementById('percentageSlider');
            slider.addEventListener('input', function() {
                const percentage = this.value;
                const calculated = (currentBalance * percentage / 100).toFixed(4);
                
                document.getElementById('percentageValue').textContent = percentage + '%';
                document.getElementById('calculatedAmount').textContent = calculated + ' ' + currentToken;
                document.getElementById('amount').value = calculated;
            });
        });

        // Build transaction
        async function buildTransaction() {
            const recipient = document.getElementById('recipientAddress').value.trim();
            const amount = document.getElementById('amount').value;
            const gasLimit = document.getElementById('gasLimit').value;
            const gasPrice = document.getElementById('gasPrice').value;
            const transactionAlert = document.getElementById('transactionAlert');

            if (!recipient.startsWith('0x') || recipient.length !== 42) {
                showAlert(transactionAlert, 'عنوان المستقبل غير صحيح', 'error');
                return;
            }

            if (!amount || amount <= 0) {
                showAlert(transactionAlert, 'المبلغ يجب أن يكون أكبر من صفر', 'error');
                return;
            }

            try {
                let txData;

                if (currentToken === 'BNB') {
                    // BNB transfer
                    txData = {
                        to: recipient,
                        value: ethers.parseEther(amount).toString(),
                        gasLimit: gasLimit,
                        gasPrice: ethers.parseUnits(gasPrice, 'gwei').toString(),
                        from: currentAddress,
                        chainId: 56,
                        nonce: await provider.getTransactionCount(currentAddress),
                        type: 2
                    };
                } else {
                    // USDT transfer
                    const usdtContract = new ethers.Contract(USDT_ADDRESS, USDT_ABI, provider);
                    const iface = new ethers.Interface(USDT_ABI);
                    const data = iface.encodeFunctionData('transfer', [recipient, ethers.parseUnits(amount, 18)]);

                    txData = {
                        to: USDT_ADDRESS,
                        data: data,
                        gasLimit: gasLimit,
                        gasPrice: ethers.parseUnits(gasPrice, 'gwei').toString(),
                        from: currentAddress,
                        value: '0',
                        chainId: 56,
                        nonce: await provider.getTransactionCount(currentAddress),
                        type: 2
                    };
                }

                // Display unsigned transaction
                document.getElementById('unsignedTransaction').value = JSON.stringify(txData, null, 2);
                document.getElementById('encodedTransaction').value = Buffer.from(JSON.stringify(txData)).toString('base64');

                document.getElementById('unsignedCard').classList.remove('hidden');
                showAlert(transactionAlert, 'تم بناء المعاملة بنجاح!', 'success');
            } catch (error) {
                showAlert(transactionAlert, 'خطأ: ' + error.message, 'error');
            }
        }

        // Generate QR Code
        function generateQRCode() {
            const unsignedTx = document.getElementById('unsignedTransaction').value;
            const qrContainer = document.getElementById('qrCode');
            qrContainer.innerHTML = '';

            QRCode.toCanvas(document.createElement('canvas'), unsignedTx, {
                errorCorrectionLevel: 'H',
                type: 'image/png',
                quality: 0.95,
                margin: 1,
                width: 300,
                color: {
                    dark: '#000000',
                    light: '#ffffff'
                }
            }, function(error, canvas) {
                if (error) {
                    console.error('خطأ في إنشاء QR Code:', error);
                    return;
                }
                qrContainer.appendChild(canvas);
                document.getElementById('qrCard').classList.remove('hidden');
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
        async function broadcastTransaction() {
            const signedTx = document.getElementById('signedTransaction').value.trim();
            const broadcastAlert = document.getElementById('broadcastAlert');

            if (!signedTx) {
                showAlert(broadcastAlert, 'الرجاء إدخال المعاملة الموقعة', 'error');
                return;
            }

            try {
                const tx = await provider.broadcastTransaction(signedTx);
                showAlert(broadcastAlert, 'تم إرسال المعاملة! Hash: ' + tx.hash, 'success');
                document.getElementById('signedTransaction').value = '';
            } catch (error) {
                showAlert(broadcastAlert, 'خطأ: ' + error.message, 'error');
            }
        }

        // Clear transaction
        function clearTransaction() {
            document.getElementById('recipientAddress').value = '';
            document.getElementById('amount').value = '';
            document.getElementById('gasPrice').value = '5';
            document.getElementById('percentageSlider').value = '0';
            document.getElementById('percentageValue').textContent = '0%';
            document.getElementById('calculatedAmount').textContent = '0.00';
        }

        // Clear signed transaction
        function clearSignedTransaction() {
            document.getElementById('signedTransaction').value = '';
        }

        // Reset wallet
        function resetWallet() {
            currentAddress = null;
            currentToken = 'BNB';
            
            document.getElementById('loginCard').classList.remove('hidden');
            document.getElementById('walletCard').classList.add('hidden');
            document.getElementById('transactionCard').classList.add('hidden');
            document.getElementById('unsignedCard').classList.add('hidden');
            document.getElementById('signedInputCard').classList.add('hidden');
            document.getElementById('qrCard').classList.add('hidden');
            
            clearTransaction();
            clearSignedTransaction();
        }

        // Show alert
        function showAlert(element, message, type) {
            element.textContent = message;
            element.className = 'alert ' + type;
            setTimeout(() => {
                element.className = 'alert';
            }, 5000);
        }

        // Initialize on page load
        window.addEventListener('load', async () => {
            console.log('الصفحة تم تحميلها');
            try {
                await initProvider();
                console.log('تم تهيئة Provider بنجاح');
            } catch (error) {
                console.error('خطأ في تهيئة Provider:', error);
                alert('خطأ في الاتصال بالشبكة: ' + error.message);
            }
        });

        // Global error handler
        window.addEventListener('error', (event) => {
            console.error('خطأ عام:', event.error);
        });

        // Unhandled promise rejection handler
        window.addEventListener('unhandledrejection', (event) => {
            console.error('Promise rejection غير معالج:', event.reason);
        });
    </script>
</body>
</html>
