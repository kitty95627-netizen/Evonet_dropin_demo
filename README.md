<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Evonet Drop-in Payment Demo</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            font-family: system-ui, -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: #F5F7FA;
            margin: 0;
            padding: 24px;
            color: #1a1f36;
        }
        .container {
            max-width: 1800px;
            margin: 0 auto;
        }
        .brand-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            margin-bottom: 16px;
            padding-bottom: 12px;
            border-bottom: 1px solid #e2e8f0;
        }
        .brand-logo {
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .brand-logo img {
            height: 44px;
            width: auto;
            object-fit: contain;
        }
        .brand-logo .brand-name {
            font-size: 1rem;
            font-weight: 500;
            color: #1a1f36;
        }
        .brand-logo .brand-name .env-tag {
            font-weight: 600;
            padding: 2px 8px;
            border-radius: 20px;
            font-size: 0.7rem;
            margin-left: 8px;
        }
        .brand-logo .brand-name .env-tag.uat { background: #fff3e0; color: #b45309; }
        .brand-logo .brand-name .env-tag.prod { background: #e0f2fe; color: #0284c7; }
        .brand-logo a { text-decoration: none; display: flex; align-items: center; gap: 12px; }
        .lang-switch {
            display: flex;
            gap: 6px;
            background: white;
            padding: 4px 6px;
            border-radius: 40px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.05);
        }
        .lang-btn {
            background: transparent;
            border: none;
            color: #336DF3;
            padding: 5px 14px;
            font-size: 0.75rem;
            cursor: pointer;
            border-radius: 30px;
        }
        .lang-btn.active { background: #336DF3; color: white; }
        .sub {
            color: #4b5563;
            margin-bottom: 1.5rem;
            border-left: 4px solid #336DF3;
            padding-left: 1rem;
            font-size: 0.75rem;
        }
        .dashboard {
            display: flex;
            flex-wrap: wrap;
            gap: 24px;
            margin-bottom: 24px;
        }
        .panel {
            background: white;
            border-radius: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            padding: 18px 22px;
        }
        .panel-left { flex: 0 0 560px; }
        .panel-right { flex: 1; min-width: 620px; }
        .panel h2 {
            font-size: 1rem;
            margin-top: 0;
            margin-bottom: 0.8rem;
            border-bottom: 2px solid #eef2ff;
            padding-bottom: 8px;
        }
        .scrollable-content-left, .scrollable-content-right {
            max-height: calc(100vh - 200px);
            overflow-y: auto;
            padding-right: 6px;
        }
        .two-columns {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px 20px;
        }
        .three-columns {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 12px 16px;
        }
        .four-columns {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px 16px;
        }
        .form-group {
            margin-bottom: 9px; /* 缩小间距 */
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            gap: 12px;
        }
        .form-group label {
            width: 170px;
            font-weight: 500;
            font-size: 0.75rem;
            color: #2c3e66;
            flex-shrink: 0;
        }
        .full-width { grid-column: span 2; }
        .required-star { color: #e53e3e; margin-left: 2px; }
        /* 输入框长度调整 */
        .input-normal {
            flex: 1.2; /* 一行一个参数时增加20% */
        }
        .input-double {
            flex: 1.3; /* 一行两个参数时增加30% */
        }
        input, select, .editable-input {
            flex: 1.2;
            padding: 6px 10px;
            border: 1px solid #cbd5e1;
            border-radius: 8px;
            background: white;
            min-width: 0;
            font-size: 0.75rem !important;
        }
        /* 两列布局中的输入框使用双倍宽度 */
        .two-columns .form-group input,
        .two-columns .form-group select,
        .two-columns .form-group .editable-input {
            flex: 1.3;
        }
        .medium-select {
            flex: 1.3;
            max-width: 180px;
        }
        .key-group {
            display: flex;
            gap: 8px;
            flex: 1.2;
        }
        .key-group input { flex: 1; }
        .key-group button {
            flex: 0 0 60px;
            padding: 6px 8px;
            background: #f0f2f5;
            border: 1px solid #cbd5e1;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.7rem;
            color: #336DF3;
        }
        .merchant-id {
            font-size: 0.7rem;
            padding: 2px 8px;
            border-radius: 12px;
            margin-left: 8px;
        }
        .merchant-id.uat { background: #fff3e0; color: #b45309; }
        .merchant-id.prod { background: #e0f2fe; color: #0284c7; }
        .currency-group {
            display: flex;
            gap: 8px;
            flex: 1.2;
            align-items: center;
        }
        .currency-group select { flex: 0 0 80px; width: 80px; }
        .currency-group input { flex: 2; }
        .order-input-wrapper {
            position: relative;
            flex: 1.2;
        }
        .order-input-wrapper input {
            width: 100%;
            padding: 6px 32px 6px 10px;
        }
        .random-order-btn {
            position: absolute;
            right: 6px;
            top: 50%;
            transform: translateY(-50%);
            background: transparent;
            border: none;
            font-size: 1rem;
            cursor: pointer;
            padding: 0;
            width: 24px;
            height: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #336DF3;
        }
        input[type="color"] {
            flex: 0 0 36px;
            height: 32px;
            padding: 2px;
            border-radius: 8px;
        }
        .color-row {
            display: flex;
            align-items: center;
            gap: 8px;
            flex: 1;
        }
        .color-row input[type="color"] { flex: 0 0 36px; height: 32px; }
        .color-row input[type="text"] {
            flex: 0 0 140px;
            width: 140px;
            padding: 5px 8px;
            font-family: monospace;
            text-align: center;
        }
        .btn-primary {
            background: #336DF3;
            color: white;
            font-weight: 600;
            padding: 10px 18px;
            border-radius: 40px;
            cursor: pointer;
            font-size: 0.75rem;
            border: none;
        }
        .btn-primary:hover { background: #1E88E5; }
        .btn-secondary {
            background: #00ACC1;
            color: white;
            font-weight: 600;
            padding: 8px 16px;
            border-radius: 40px;
            cursor: pointer;
            font-size: 0.75rem;
            border: none;
        }
        .btn-secondary:hover { background: #0097a7; }
        .btn-icon {
            background: #f0f2f5;
            border: 1px solid #cbd5e1;
            color: #336DF3;
            padding: 4px 10px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.7rem;
        }
        .code-block {
            background: #F5F7FA;
            border-radius: 10px;
            padding: 8px 10px;
            font-size: 0.7rem;
            border: 1px solid #e2e8f0;
            margin: 10px 0;
        }
        .session-display {
            display: flex;
            align-items: center;
            gap: 12px;
            background: #F5F7FA;
            border-radius: 10px;
            padding: 8px 12px;
            border: 1px solid #e2e8f0;
            margin: 10px 0;
        }
        .session-id-value {
            font-family: monospace;
            font-size: 0.7rem;
            word-break: break-all;
            flex: 1;
        }
        .log-area {
            background: white;
            border-radius: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            margin-bottom: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        .log-header {
            padding: 16px 22px 12px 22px;
            border-bottom: 1px solid #eef2ff;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
            background: white;
            flex-shrink: 0;
        }
        .log-header h2 {
            margin: 0;
            font-size: 1rem;
        }
        .search-box {
            display: flex;
            gap: 8px;
            align-items: center;
        }
        .search-box input {
            padding: 5px 10px;
            width: 220px;
        }
        .log-scroll-container {
            height: 140px;
            overflow-y: auto;
            padding: 12px 22px;
        }
        .result-area {
            background: #0f172a;
            color: #e2e8f0;
            border-radius: 14px;
            padding: 12px;
            font-family: monospace;
            font-size: 0.7rem;
        }
        .log-footer {
            padding: 12px 22px 18px 22px;
            border-top: 1px solid #eef2ff;
            flex-shrink: 0;
        }
        .section-title {
            font-weight: 700;
            font-size: 0.85rem;
            margin: 16px 0 12px 0;
            color: #2c3e66;
            border-left: 3px solid #336DF3;
            padding-left: 8px;
            cursor: pointer;
            user-select: none;
        }
        .section-title.collapsible {
            cursor: pointer;
        }
        .section-title.collapsible::before {
            content: "▶ ";
            font-size: 0.7rem;
            display: inline-block;
            transition: transform 0.2s;
        }
        .section-title.collapsible.open::before {
            content: "▼ ";
        }
        .section-content {
            margin-left: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
        }
        .section-content.collapsed {
            display: none;
        }
        .divider {
            border-top: 1px solid #e2e8f0;
            margin: 12px 0;
        }
        .color-grid-3col {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 8px;
            margin-top: 6px;
        }
        .color-item {
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .color-item label {
            width: 110px;
            font-size: 0.65rem;
            font-family: monospace;
        }
        .bin-panel {
            background: #F5F7FA;
            border-radius: 10px;
            padding: 12px;
            margin-top: 8px;
            border: 1px solid #e2e8f0;
        }
        .warning-box, .info-box {
            padding: 8px 12px;
            border-radius: 10px;
            margin-bottom: 12px;
            font-size: 0.7rem;
        }
        .warning-box { background: #fff3e3; border-left: 4px solid #f59e0b; }
        .info-box { background: #e6f0ff; border-left: 4px solid #336DF3; }
        .session-box { background: #fef9e3; border: 1px solid #fde68a; border-radius: 10px; padding: 10px; margin: 10px 0; }
        .hidden { display: none; }
        .inline-buttons { display: flex; gap: 10px; }
        .render-section { margin-top: 20px; scroll-margin-top: 20px; }
        .render-area { min-height: 450px; background: white; border-radius: 20px; padding: 18px 22px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); margin-bottom: 20px; }
        .render-area h2, .step3-panel h2 { font-size: 1rem; margin-top: 0; margin-bottom: 0.8rem; border-bottom: 2px solid #eef2ff; padding-bottom: 8px; }
        .step3-panel { background: white; border-radius: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); padding: 18px 22px; margin-bottom: 20px; cursor: pointer; }
        .step3-header { display: flex; justify-content: space-between; align-items: center; }
        .step3-content { margin-top: 12px; }
        .collapse-icon { font-size: 1.2rem; transition: transform 0.2s; }
        .response-detail { background: #f8fafc; border-radius: 8px; padding: 10px; margin-top: 10px; font-family: monospace; font-size: 0.7rem; border: 1px solid #e2e8f0; }
        .mt-1 { margin-top: 4px; }
        .mt-2 { margin-top: 8px; }
        .mb-1 { margin-bottom: 4px; }
        .mb-2 { margin-bottom: 8px; }
        .btn-small {
            padding: 4px 12px;
            font-size: 0.7rem;
        }
    </style>
</head>
<body>
<div class="container">
    <div class="brand-header">
        <div class="brand-logo">
            <a href="https://evonetglobal.com/" target="_blank">
                <img src="https://evonetglobal.com/wp-content/uploads/2025/07/evonet.png" alt="Evonet" onerror="this.src='https://evonetglobal.com/wp-content/uploads/2022/05/logo.svg'">
                <span class="brand-name">
                    <span id="brandDemoText">Drop-in 效果演示</span>
                    <span id="envTag" class="env-tag uat">UAT</span>
                </span>
            </a>
        </div>
        <div class="lang-switch">
            <button id="langZhBtn" class="lang-btn active">中文</button>
            <button id="langEnBtn" class="lang-btn">English</button>
        </div>
    </div>
    <div class="sub" id="subText">Step 1: 获取 Session ID (通过本地代理) → Step 2: 渲染 SDK → Step 3: 处理事件响应</div>
    <div class="warning-box" id="warningBox">
        ⚠️ 重要提示：<br>
        • 请先启动本地代理服务器: <code>cd ~/evonet-proxy && node server.js</code><br>
        • KeyID 和 SignKey 已根据环境自动填充，点击输入框可修改，点击重置恢复默认
    </div>

    <div class="dashboard">
        <!-- Step 1 面板 -->
        <div class="panel panel-left">
            <h2 id="step1Title">🔑 Step 1 · 获取 Session ID</h2>
            <div class="info-box" id="step1Hint">💡 通过本地代理调用 Interaction API</div>
            <div class="scrollable-content-left">
                <!-- API Authentication 标题（必要，带星号） -->
                <div class="section-title" id="authTitle">API Authentication <span class="required-star">*</span></div>
                <div class="form-group">
                    <label id="keyIdLabel">KeyID <span class="required-star">*</span></label>
                    <div class="key-group">
                        <input type="text" id="keyId" placeholder="KeyID" value="********">
                        <button id="resetKeyBtn" class="btn-icon">重置</button>
                    </div>
                </div>
                <div class="form-group">
                    <label id="signKeyLabel">SignKey <span class="required-star">*</span></label>
                    <div class="key-group">
                        <input type="text" id="signKey" placeholder="SignKey" value="********">
                        <button id="resetSignKeyBtn" class="btn-icon">重置</button>
                    </div>
                </div>
                <div class="form-group mt-2" id="merchantIdGroup" style="display: none;">
                    <label>商户编号</label>
                    <span id="merchantIdDisplay" class="merchant-id"></span>
                </div>
                
                <!-- 交易金额部分：去掉标题，Currency 和 Value 两列布局，均加必填星号 -->
                <div class="two-columns" style="margin-top: 8px;">
                    <div class="form-group" style="margin-bottom: 0;">
                        <label>currency <span class="required-star">*</span></label>
                        <input list="currencyList" id="currency" class="editable-input" value="THB">
                        <datalist id="currencyList">
                            <option>THB</option><option>HKD</option><option>SGD</option><option>USD</option><option>EUR</option><option>JPY</option><option>KRW</option>
                        </datalist>
                    </div>
                    <div class="form-group" style="margin-bottom: 0;">
                        <label>Value <span class="required-star">*</span></label>
                        <input type="number" id="amount" value="299">
                    </div>
                </div>
                
                <!-- 订单信息标题（必要，带星号） -->
                <div class="section-title" id="orderInfoTitle">订单信息 <span class="required-star">*</span></div>
                <div class="form-group"><label>merchantOrderID <span class="required-star">*</span></label><div class="order-input-wrapper"><input type="text" id="merchantOrderId"><button id="randomOrderBtn" class="random-order-btn">🔄</button></div></div>
                <div class="two-columns mt-2">
                    <div class="form-group"><label>merchantName</label><input type="text" id="merchantName" placeholder="商户名称"></div>
                    <div class="form-group"><label>merchantDescription</label><input type="text" id="merchantDescription" placeholder="商户描述"></div>
                    <div class="form-group"><label>merchantCategoryCode</label><input type="text" id="merchantCategoryCode" placeholder="MCC 码"></div>
                    <!-- 用户信息收集字段 -->
                    <div class="form-group"><label>isCollectEmail</label>
                        <input list="boolOpts" id="isCollectEmail" class="editable-input" value="">
                        <datalist id="boolOpts"><option>true</option><option>false</option></datalist>
                    </div>
                    <div class="form-group"><label>isCollectPhoneNumber</label>
                        <input list="boolOpts" id="isCollectPhoneNumber" class="editable-input" value="">
                    </div>
                    <div class="form-group"><label>isCollectBillingAddress</label>
                        <input list="boolOpts" id="isCollectBillingAddress" class="editable-input" value="">
                    </div>
                    <div class="form-group"><label>isCollectShippingAddress</label>
                        <input list="boolOpts" id="isCollectShippingAddress" class="editable-input" value="">
                    </div>
                </div>
                
                <!-- 可折叠区域：支付方式配置 -->
                <div class="section-title collapsible" id="paymentMethodTitle">支付方式配置</div>
                <div class="section-content" id="paymentMethodContent">
                    <div class="form-group"><label>recurringProcessingModel</label>
                        <input list="recurringModels" id="recurringModel" class="editable-input" value="">
                        <datalist id="recurringModels"><option>Subscription</option><option>Unscheduled</option></datalist>
                    </div>
                </div>
                
                <!-- 可折叠区域：用户信息 -->
                <div class="section-title collapsible" id="userInfoTitle">用户信息</div>
                <div class="section-content" id="userInfoContent">
                    <div class="form-group" id="referenceGroup">
                        <label id="referenceLabel">reference</label>
                        <input type="text" id="userReference" placeholder="用户唯一标识">
                    </div>
                    <div class="two-columns">
                        <div class="form-group"><label>email</label><input type="email" id="userEmail" placeholder="user@example.com"></div>
                        <div class="form-group"><label>phone</label><input type="text" id="userPhone" placeholder="电话号码"></div>
                    </div>
                    <div class="two-columns">
                        <div class="form-group"><label>firstName</label><input type="text" id="userFirstName" placeholder="名"></div>
                        <div class="form-group"><label>lastName</label><input type="text" id="userLastName" placeholder="姓"></div>
                    </div>
                    <div class="form-group"><label>address</label><input type="text" id="userAddress" placeholder="地址"></div>
                    <div class="three-columns">
                        <div class="form-group"><label>city</label><input type="text" id="userCity" placeholder="城市"></div>
                        <div class="form-group"><label>state</label><input type="text" id="userState" placeholder="州/省"></div>
                        <div class="form-group"><label>country</label><input type="text" id="userCountry" placeholder="国家代码 (如: TH)"></div>
                    </div>
                    <div class="form-group"><label>postalCode</label><input type="text" id="userPostalCode" placeholder="邮编"></div>
                </div>
                
                <!-- 可折叠区域：其他交易参数 -->
                <div class="section-title collapsible" id="otherParamsTitle">其他交易参数</div>
                <div class="section-content" id="otherParamsContent">
                    <div class="form-group"><label>expireTime</label><input type="number" id="expiryMinutes" value="40000" placeholder="5-43200分钟"></div>
                    <div class="form-group"><label>returnUrl</label><input type="text" id="returnUrl" value="https://example.com/success" placeholder="支付完成跳转地址"></div>
                    <div class="form-group"><label>webhookUrl</label><input type="text" id="webhookUrl" value="https://example.com/webhook" placeholder="异步通知地址"></div>
                </div>
                
                <!-- 可折叠区域：其他参数 -->
                <div class="section-title collapsible" id="miscParamsTitle">其他参数</div>
                <div class="section-content" id="miscParamsContent">
                    <div class="two-columns">
                        <div class="form-group"><label>avs</label>
                            <input list="boolOpts" id="avs" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label>requestId</label><input type="text" id="requestId" placeholder="UUID / GUID"></div>
                        <div class="form-group full-width"><label>tradeInfo</label><input type="text" id="tradeInfo" placeholder='{"key":"value"}'></div>
                    </div>
                </div>
                
                <button id="callApiBtn" class="btn-secondary" style="width: 100%; margin-top: 8px;">🚀 调用 Interaction API (通过本地代理)</button>
                <div class="code-block"><strong id="apiSessionLabel">📦 Session ID：</strong><br><span id="apiSessionDisplay">(未获取)</span></div>
            </div>
        </div>

        <!-- Step 2 面板 -->
        <div class="panel panel-right">
            <h2 id="step2Title">⚙️ Step 2 · 渲染 SDK</h2>
            <div class="scrollable-content-right">
                <!-- 基本参数标题（必要，带星号） -->
                <div class="section-title" id="basicParamsTitle">基本参数 <span class="required-star">*</span></div>
                <!-- Session ID 单独一行，加星号 -->
                <div class="form-group">
                    <label id="sessionIdLabel">Session ID <span class="required-star">*</span></label>
                    <input type="text" id="manualSessionId" placeholder="Create session ID by interaction API">
                </div>
                <!-- type 单独一行，加星号 -->
                <div class="form-group">
                    <label>type <span class="required-star">*</span></label>
                    <input type="text" value="payment" disabled>
                </div>
                <!-- 其他基本参数两列布局 -->
                <div class="two-columns">
                    <div class="form-group"><label id="envLabel">environment <span class="required-star">*</span></label><input list="envList" id="env" value="UAT"><datalist id="envList"><option>UAT</option><option>HKG_prod</option><option>BKK_prod</option><option>TYO_prod</option></datalist></div>
                    <div class="form-group"><label id="modeLabel">mode <span class="required-star">*</span></label><input list="modeList" id="mode" value="embedded"><datalist id="modeList"><option>embedded</option><option>fullPage</option><option>bottomUp</option></datalist></div>
                    <div class="form-group"><label id="localeLabel">locale</label><input list="localeList" id="locale" value=""><datalist id="localeList"><option>en-US</option><option>zh-CN</option><option>zh-TW</option><option>ja-JP</option><option>ko-KR</option><option>th-TH</option></datalist></div>
                </div>
                <div class="divider"></div>
                
                <!-- 可折叠区域：卡片支付选项 -->
                <div class="section-title collapsible" id="cardOptionsTitle">卡片支付选项</div>
                <div class="section-content" id="cardOptionsContent">
                    <div class="two-columns">
                        <div class="form-group"><label id="showCardHolderNameLabel">showCardHolderName</label>
                            <input list="boolOpts" id="showCardHolderName" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label id="cvvSavedCardLabel">CVVForSavedCard</label>
                            <input list="boolOpts" id="cvvSavedCard" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label id="showScanCardLabel">showScanCardButton</label>
                            <input list="boolOpts" id="showScanCard" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label id="autoInvokeLabel">autoInvokeCardScanner</label>
                            <input list="boolOpts" id="autoInvokeScanner" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label id="showSaveImageLabel">showSaveImage</label>
                            <input list="boolOpts" id="showSaveImage" class="editable-input" value="">
                        </div>
                    </div>
                </div>
                
                <!-- 可折叠区域：条款与条件 -->
                <div class="section-title collapsible" id="tncTitle">条款与条件 (TnC)</div>
                <div class="section-content" id="tncContent">
                    <div class="form-group"><label id="showTnCLabel">showTnC</label>
                        <input list="boolOpts" id="showTnC" class="editable-input" value="">
                    </div>
                    <div id="tncFields" class="hidden">
                        <div class="two-columns">
                            <div class="form-group"><label id="tncModeLabel">TnC mode <span class="required-star">*</span></label>
                                <input list="tncModeList" id="tncMode" class="editable-input" value="click2accept">
                                <datalist id="tncModeList"><option>click2accept</option><option>checkbox</option></datalist>
                            </div>
                            <div class="form-group"><label id="tncUrlLabel">TnC Url <span class="required-star">*</span></label><input type="text" id="tncUrl" value="https://example.com/terms" placeholder="https://example.com/terms"></div>
                        </div>
                    </div>
                </div>
                
                <!-- 可折叠区域：Card BIN 验证 -->
                <div class="section-title collapsible" id="binTitle">Card BIN 验证</div>
                <div class="section-content" id="binContent">
                    <div class="form-group"><label id="verifyBrandLabel">isVerifyPaymentBrand</label>
                        <input list="boolOpts" id="verifyBrand" class="editable-input" value="">
                    </div>
                    <div id="maxWaitGroup" class="hidden"><div class="form-group"><label id="maxWaitLabel">maxWaitTime (秒)</label><input type="number" id="maxWaitTime" value="10"></div></div>
                </div>
                
                <!-- 可折叠区域：布局控制 -->
                <div class="section-title collapsible" id="layoutTitle">布局控制</div>
                <div class="section-content" id="layoutContent">
                    <div class="two-columns">
                        <div class="form-group"><label id="columnsLabel">Columns</label>
                            <input list="boolOpts" id="columns" class="editable-input" value="">
                        </div>
                        <div class="form-group"><label id="logoPositionLabel">logoPosition</label>
                            <input list="logoList" id="logoPosition" class="editable-input" value="">
                            <datalist id="logoList"><option>left</option><option>middle</option><option>right</option></datalist>
                        </div>
                        <div class="form-group full-width"><label id="borderRadiusLabel">borderRadius</label><input id="borderRadius" placeholder="8,8,8,8 或 16"></div>
                    </div>
                </div>
                
                <!-- 可折叠区域：颜色主题 -->
                <div class="section-title collapsible" id="colorTitle">颜色主题</div>
                <div class="section-content" id="colorContent">
                    <div class="color-grid-3col">
                        <div class="color-item"><label>colorBackground</label><div class="color-row"><input type="color" id="colorBgPicker" value="#ffffff"><input type="text" id="colorBg" value="#ffffff"></div></div>
                        <div class="color-item"><label>colorPrimary</label><div class="color-row"><input type="color" id="colorPrimaryPicker" value="#1a1f36"><input type="text" id="colorPrimary" value="#1a1f36"></div></div>
                        <div class="color-item"><label>colorAction</label><div class="color-row"><input type="color" id="colorActionPicker" value="#336DF3"><input type="text" id="colorAction" value="#336DF3"></div></div>
                        <div class="color-item"><label>colorBoxStroke</label><div class="color-row"><input type="color" id="colorBoxStrokePicker" value="#e2e8f0"><input type="text" id="colorBoxStroke" value="#e2e8f0"></div></div>
                        <div class="color-item"><label>colorDisabled</label><div class="color-row"><input type="color" id="colorDisabledPicker" value="#cbd5e1"><input type="text" id="colorDisabled" value="#cbd5e1"></div></div>
                        <div class="color-item"><label>colorError</label><div class="color-row"><input type="color" id="colorErrorPicker" value="#e53e3e"><input type="text" id="colorError" value="#e53e3e"></div></div>
                        <div class="color-item"><label>colorFormBackground</label><div class="color-row"><input type="color" id="colorFormBgPicker" value="#ffffff"><input type="text" id="colorFormBackground" value="#ffffff"></div></div>
                        <div class="color-item"><label>colorFormBorder</label><div class="color-row"><input type="color" id="colorFormBorderPicker" value="#cbd5e1"><input type="text" id="colorFormBorder" value="#cbd5e1"></div></div>
                        <div class="color-item"><label>colorInverse</label><div class="color-row"><input type="color" id="colorInversePicker" value="#ffffff"><input type="text" id="colorInverse" value="#ffffff"></div></div>
                        <div class="color-item"><label>colorBoxFillingOutline</label><div class="color-row"><input type="color" id="colorBoxFillingPicker" value="#336DF3"><input type="text" id="colorBoxFillingOutline" value="#336DF3"></div></div>
                        <div class="color-item"><label>colorPlaceholder</label><div class="color-row"><input type="color" id="colorPlaceholderPicker" value="#94a3b8"><input type="text" id="colorPlaceholder" value="#94a3b8"></div></div>
                        <div class="color-item"><label>colorSecondary</label><div class="color-row"><input type="color" id="colorSecondaryPicker" value="#64748b"><input type="text" id="colorSecondary" value="#64748b"></div></div>
                    </div>
                </div>
                
                <div class="session-display"><span id="currentSessionHintLabel">💡 Session ID：</span><span id="currentSessionHintFull" class="session-id-value">(未设置)</span><button id="copySessionBtn" class="btn-icon" style="display:none;">📋 复制</button></div>
                <button id="initSdkBtn" class="btn-primary" style="width: 100%;">✨ 初始化 SDK</button>
                <div style="text-align: center; margin-top: 8px;">
                    <button id="resetAllBtn" class="btn-secondary btn-small">重置所有参数</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Step 3 - 可折叠 -->
    <div class="step3-panel" id="step3Panel">
        <div class="step3-header" onclick="toggleStep3()">
            <h2 id="step3Title">🎯 Step 3 · Event Response (BIN 验证响应)</h2>
            <span class="collapse-icon" id="step3Icon">▼</span>
        </div>
        <div id="step3Content" class="step3-content">
            <div class="bin-panel">
                <div class="form-group"><label id="isValidLabel">isValid <span class="required-star">*</span></label><select id="binIsValid" class="medium-select"><option>true</option><option>false</option></select></div>
                <div id="binMsgGroup" class="hidden"><div class="form-group"><label id="msgLabel">msg <span class="required-star">*</span></label><input type="text" id="binMsg" placeholder="拒绝原因"></div></div>
                <div id="binEventStatus" class="code-block">⏳ 等待验证事件...</div>
                <div id="binResponseDetail" class="response-detail hidden"></div>
            </div>
        </div>
    </div>

    <!-- 日志区域 -->
    <div class="log-area">
        <div class="log-header">
            <h2 id="logTitle">📡 事件日志</h2>
            <div class="search-box">
                <input type="text" id="searchLogInput" placeholder="🔍 搜索日志...">
                <button id="searchLogBtn" class="btn-icon">搜索</button>
                <button id="clearSearchBtn" class="btn-icon">清除</button>
            </div>
        </div>
        <div class="log-scroll-container">
            <div class="result-area" id="logPanel">🟢 系统就绪 - 请先启动本地代理服务器 (端口 3001)</div>
        </div>
        <div class="log-footer">
            <div class="inline-buttons">
                <button id="clearLogBtn" class="btn-secondary btn-small">清空日志</button>
            </div>
        </div>
    </div>

    <!-- 渲染区域 -->
    <div class="render-section" id="renderSection">
        <div class="render-area"><h2 id="renderTitle">📱 Drop-in 渲染区域</h2><div id="dropInApp" style="min-height: 450px;"></div></div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/cil-dropin-components@latest/dist/index.min.js"></script>
<script>
    // ==================== 版本 v5.0-final ====================
    let apiSessionId = null, sdkInstance = null, pendingVerificationId = null;
    const containerId = '#dropInApp';
    let currentLang = 'zh';
    let allLogs = [];

    const API_PROXY_URL = 'http://localhost:3001/api/interaction';

    const defaultKeys = {
        UAT: { keyId: '630805e2d532478aba9cedb9cea14397', signKey: 'd5e2c210d2114b1993ee68244ed88fce', merchantId: 'S005188' },
        PROD: { keyId: 'kid_8ba2ead667f04f26a73b1b8b80ac10db', signKey: 'sk_5df02a02fdf744dc8b419bdf40c93c3e', merchantId: 'S309925' }
    };

    let currentRealKeyId = defaultKeys.UAT.keyId;
    let currentRealSignKey = defaultKeys.UAT.signKey;
    let isKeyUserModified = false;
    let isSignKeyUserModified = false;

    // 存储初始状态
    let initialState = {};

    function captureInitialState() {
        initialState = {
            keyId: currentRealKeyId,
            signKey: currentRealSignKey,
            isKeyUserModified: false,
            isSignKeyUserModified: false,
            amount: document.getElementById('amount').value,
            currency: document.getElementById('currency').value,
            merchantOrderId: document.getElementById('merchantOrderId').value,
            merchantName: document.getElementById('merchantName').value,
            merchantDescription: document.getElementById('merchantDescription').value,
            merchantCategoryCode: document.getElementById('merchantCategoryCode').value,
            isCollectEmail: document.getElementById('isCollectEmail').value,
            isCollectPhoneNumber: document.getElementById('isCollectPhoneNumber').value,
            isCollectBillingAddress: document.getElementById('isCollectBillingAddress').value,
            isCollectShippingAddress: document.getElementById('isCollectShippingAddress').value,
            recurringModel: document.getElementById('recurringModel').value,
            userReference: document.getElementById('userReference').value,
            userEmail: document.getElementById('userEmail').value,
            userPhone: document.getElementById('userPhone').value,
            userFirstName: document.getElementById('userFirstName').value,
            userLastName: document.getElementById('userLastName').value,
            userAddress: document.getElementById('userAddress').value,
            userCity: document.getElementById('userCity').value,
            userState: document.getElementById('userState').value,
            userCountry: document.getElementById('userCountry').value,
            userPostalCode: document.getElementById('userPostalCode').value,
            expiryMinutes: document.getElementById('expiryMinutes').value,
            returnUrl: document.getElementById('returnUrl').value,
            webhookUrl: document.getElementById('webhookUrl').value,
            avs: document.getElementById('avs').value,
            requestId: document.getElementById('requestId').value,
            tradeInfo: document.getElementById('tradeInfo').value,
            manualSessionId: document.getElementById('manualSessionId').value,
            env: document.getElementById('env').value,
            mode: document.getElementById('mode').value,
            locale: document.getElementById('locale').value,
            showCardHolderName: document.getElementById('showCardHolderName').value,
            cvvSavedCard: document.getElementById('cvvSavedCard').value,
            showScanCard: document.getElementById('showScanCard').value,
            autoInvokeScanner: document.getElementById('autoInvokeScanner').value,
            showSaveImage: document.getElementById('showSaveImage').value,
            showTnC: document.getElementById('showTnC').value,
            tncMode: document.getElementById('tncMode').value,
            tncUrl: document.getElementById('tncUrl').value,
            verifyBrand: document.getElementById('verifyBrand').value,
            maxWaitTime: document.getElementById('maxWaitTime').value,
            columns: document.getElementById('columns').value,
            logoPosition: document.getElementById('logoPosition').value,
            borderRadius: document.getElementById('borderRadius').value,
            colorBg: document.getElementById('colorBg').value,
            colorPrimary: document.getElementById('colorPrimary').value,
            colorAction: document.getElementById('colorAction').value,
            colorBoxStroke: document.getElementById('colorBoxStroke').value,
            colorDisabled: document.getElementById('colorDisabled').value,
            colorError: document.getElementById('colorError').value,
            colorFormBackground: document.getElementById('colorFormBackground').value,
            colorFormBorder: document.getElementById('colorFormBorder').value,
            colorInverse: document.getElementById('colorInverse').value,
            colorBoxFillingOutline: document.getElementById('colorBoxFillingOutline').value,
            colorPlaceholder: document.getElementById('colorPlaceholder').value,
            colorSecondary: document.getElementById('colorSecondary').value
        };
    }

    function resetAllParameters() {
        currentRealKeyId = defaultKeys.UAT.keyId;
        currentRealSignKey = defaultKeys.UAT.signKey;
        isKeyUserModified = false;
        isSignKeyUserModified = false;
        updateKeyInputDisplay();
        updateSignKeyInputDisplay();
        updateMerchantDisplay();

        document.getElementById('amount').value = initialState.amount;
        document.getElementById('currency').value = initialState.currency;
        document.getElementById('merchantOrderId').value = initialState.merchantOrderId;
        document.getElementById('merchantName').value = initialState.merchantName;
        document.getElementById('merchantDescription').value = initialState.merchantDescription;
        document.getElementById('merchantCategoryCode').value = initialState.merchantCategoryCode;
        document.getElementById('isCollectEmail').value = initialState.isCollectEmail;
        document.getElementById('isCollectPhoneNumber').value = initialState.isCollectPhoneNumber;
        document.getElementById('isCollectBillingAddress').value = initialState.isCollectBillingAddress;
        document.getElementById('isCollectShippingAddress').value = initialState.isCollectShippingAddress;
        document.getElementById('recurringModel').value = initialState.recurringModel;
        document.getElementById('userReference').value = initialState.userReference;
        document.getElementById('userEmail').value = initialState.userEmail;
        document.getElementById('userPhone').value = initialState.userPhone;
        document.getElementById('userFirstName').value = initialState.userFirstName;
        document.getElementById('userLastName').value = initialState.userLastName;
        document.getElementById('userAddress').value = initialState.userAddress;
        document.getElementById('userCity').value = initialState.userCity;
        document.getElementById('userState').value = initialState.userState;
        document.getElementById('userCountry').value = initialState.userCountry;
        document.getElementById('userPostalCode').value = initialState.userPostalCode;
        document.getElementById('expiryMinutes').value = initialState.expiryMinutes;
        document.getElementById('returnUrl').value = initialState.returnUrl;
        document.getElementById('webhookUrl').value = initialState.webhookUrl;
        document.getElementById('avs').value = initialState.avs;
        document.getElementById('requestId').value = initialState.requestId;
        document.getElementById('tradeInfo').value = initialState.tradeInfo;

        document.getElementById('manualSessionId').value = initialState.manualSessionId;
        document.getElementById('env').value = initialState.env;
        document.getElementById('mode').value = initialState.mode;
        document.getElementById('locale').value = initialState.locale;
        document.getElementById('showCardHolderName').value = initialState.showCardHolderName;
        document.getElementById('cvvSavedCard').value = initialState.cvvSavedCard;
        document.getElementById('showScanCard').value = initialState.showScanCard;
        document.getElementById('autoInvokeScanner').value = initialState.autoInvokeScanner;
        document.getElementById('showSaveImage').value = initialState.showSaveImage;
        document.getElementById('showTnC').value = initialState.showTnC;
        document.getElementById('tncMode').value = initialState.tncMode;
        document.getElementById('tncUrl').value = initialState.tncUrl;
        document.getElementById('verifyBrand').value = initialState.verifyBrand;
        document.getElementById('maxWaitTime').value = initialState.maxWaitTime;
        document.getElementById('columns').value = initialState.columns;
        document.getElementById('logoPosition').value = initialState.logoPosition;
        document.getElementById('borderRadius').value = initialState.borderRadius;

        document.getElementById('colorBg').value = initialState.colorBg;
        document.getElementById('colorPrimary').value = initialState.colorPrimary;
        document.getElementById('colorAction').value = initialState.colorAction;
        document.getElementById('colorBoxStroke').value = initialState.colorBoxStroke;
        document.getElementById('colorDisabled').value = initialState.colorDisabled;
        document.getElementById('colorError').value = initialState.colorError;
        document.getElementById('colorFormBackground').value = initialState.colorFormBackground;
        document.getElementById('colorFormBorder').value = initialState.colorFormBorder;
        document.getElementById('colorInverse').value = initialState.colorInverse;
        document.getElementById('colorBoxFillingOutline').value = initialState.colorBoxFillingOutline;
        document.getElementById('colorPlaceholder').value = initialState.colorPlaceholder;
        document.getElementById('colorSecondary').value = initialState.colorSecondary;

        document.getElementById('colorBgPicker').value = initialState.colorBg;
        document.getElementById('colorPrimaryPicker').value = initialState.colorPrimary;
        document.getElementById('colorActionPicker').value = initialState.colorAction;
        document.getElementById('colorBoxStrokePicker').value = initialState.colorBoxStroke;
        document.getElementById('colorDisabledPicker').value = initialState.colorDisabled;
        document.getElementById('colorErrorPicker').value = initialState.colorError;
        document.getElementById('colorFormBgPicker').value = initialState.colorFormBackground;
        document.getElementById('colorFormBorderPicker').value = initialState.colorFormBorder;
        document.getElementById('colorInversePicker').value = initialState.colorInverse;
        document.getElementById('colorBoxFillingPicker').value = initialState.colorBoxFillingOutline;
        document.getElementById('colorPlaceholderPicker').value = initialState.colorPlaceholder;
        document.getElementById('colorSecondaryPicker').value = initialState.colorSecondary;

        toggleTnCFields();
        toggleMaxWaitGroup();
        updateReferenceRequired();
        updateSessionHintFull();

        addLog(currentLang === 'zh' ? '所有参数已重置' : 'All parameters reset', 'info');
    }

    function isDefaultKey(value) { return value === defaultKeys.UAT.keyId || value === defaultKeys.PROD.keyId; }
    function isDefaultSignKey(value) { return value === defaultKeys.UAT.signKey || value === defaultKeys.PROD.signKey; }

    function updateMerchantDisplay() {
        let keyId = currentRealKeyId;
        let signKey = currentRealSignKey;
        let merchantGroup = document.getElementById('merchantIdGroup');
        let merchantSpan = document.getElementById('merchantIdDisplay');
        if ((keyId === defaultKeys.UAT.keyId && signKey === defaultKeys.UAT.signKey) ||
            (keyId === defaultKeys.PROD.keyId && signKey === defaultKeys.PROD.signKey)) {
            let merchantId = (keyId === defaultKeys.UAT.keyId) ? defaultKeys.UAT.merchantId : defaultKeys.PROD.merchantId;
            merchantSpan.innerText = merchantId;
            merchantSpan.className = 'merchant-id ' + ((keyId === defaultKeys.UAT.keyId) ? 'uat' : 'prod');
            merchantGroup.style.display = 'flex';
        } else {
            merchantGroup.style.display = 'none';
        }
    }

    function updateEnvTag() {
        let env = document.getElementById('env').value;
        let envTag = document.getElementById('envTag');
        let isProd = env.includes('_prod');
        envTag.innerText = env;
        envTag.className = 'env-tag ' + (isProd ? 'prod' : 'uat');
    }

    function updateKeysFromEnv() {
        let env = document.getElementById('env').value;
        let isProd = env.includes('_prod');
        let defaultKey = isProd ? defaultKeys.PROD : defaultKeys.UAT;
        currentRealKeyId = defaultKey.keyId;
        currentRealSignKey = defaultKey.signKey;
        isKeyUserModified = false;
        isSignKeyUserModified = false;
        updateKeyInputDisplay();
        updateSignKeyInputDisplay();
        updateMerchantDisplay();
        updateEnvTag();
    }

    function resetKey() {
        let env = document.getElementById('env').value;
        let isProd = env.includes('_prod');
        let defaultKey = isProd ? defaultKeys.PROD : defaultKeys.UAT;
        currentRealKeyId = defaultKey.keyId;
        isKeyUserModified = false;
        updateKeyInputDisplay();
        updateMerchantDisplay();
        addLog(currentLang === 'zh' ? 'KeyID 已重置' : 'KeyID reset', 'info');
    }
    
    function resetSignKey() {
        let env = document.getElementById('env').value;
        let isProd = env.includes('_prod');
        let defaultKey = isProd ? defaultKeys.PROD : defaultKeys.UAT;
        currentRealSignKey = defaultKey.signKey;
        isSignKeyUserModified = false;
        updateSignKeyInputDisplay();
        updateMerchantDisplay();
        addLog(currentLang === 'zh' ? 'SignKey 已重置' : 'SignKey reset', 'info');
    }

    function getRealKeyId() { return currentRealKeyId; }
    function getRealSignKey() { return currentRealSignKey; }

    function updateKeyInputDisplay() {
        let keyInput = document.getElementById('keyId');
        if (isKeyUserModified) {
            keyInput.value = currentRealKeyId;
        } else {
            keyInput.value = '********';
        }
    }

    function updateSignKeyInputDisplay() {
        let signInput = document.getElementById('signKey');
        if (isSignKeyUserModified) {
            signInput.value = currentRealSignKey;
        } else {
            signInput.value = '********';
        }
    }

    function setupKeyInputs() {
        let keyInput = document.getElementById('keyId');
        let signInput = document.getElementById('signKey');
        
        keyInput.addEventListener('focus', function() {
            if (!isKeyUserModified && isDefaultKey(currentRealKeyId)) {
                this.value = '';
            } else if (isKeyUserModified) {
                this.value = currentRealKeyId;
            }
        });
        keyInput.addEventListener('blur', function() {
            if (this.value === '') {
                let env = document.getElementById('env').value;
                let isProd = env.includes('_prod');
                let defaultKey = isProd ? defaultKeys.PROD : defaultKeys.UAT;
                currentRealKeyId = defaultKey.keyId;
                isKeyUserModified = false;
            } else if (this.value !== currentRealKeyId) {
                currentRealKeyId = this.value;
                isKeyUserModified = true;
            }
            updateKeyInputDisplay();
            updateMerchantDisplay();
        });
        
        signInput.addEventListener('focus', function() {
            if (!isSignKeyUserModified && isDefaultSignKey(currentRealSignKey)) {
                this.value = '';
            } else if (isSignKeyUserModified) {
                this.value = currentRealSignKey;
            }
        });
        signInput.addEventListener('blur', function() {
            if (this.value === '') {
                let env = document.getElementById('env').value;
                let isProd = env.includes('_prod');
                let defaultKey = isProd ? defaultKeys.PROD : defaultKeys.UAT;
                currentRealSignKey = defaultKey.signKey;
                isSignKeyUserModified = false;
            } else if (this.value !== currentRealSignKey) {
                currentRealSignKey = this.value;
                isSignKeyUserModified = true;
            }
            updateSignKeyInputDisplay();
            updateMerchantDisplay();
        });
    }

    function addLogToPanel(msg, type) {
        let panel = document.getElementById('logPanel');
        let icons = { error: '❌', success: '✅', warning: '⚠️', info: '📢', request: '📤', response: '📥' };
        let time = new Date().toLocaleTimeString();
        let logEntry = `<div>${icons[type] || '📢'} [${time}] ${msg}</div>`;
        panel.innerHTML += logEntry;
        panel.scrollTop = panel.scrollHeight;
        allLogs.push({ time, msg, type, html: logEntry });
    }
    function addLog(msg, type) { addLogToPanel(msg, type); console.log(`[${type}] ${msg}`); }
    function logRequest(url, body) { addLogToPanel(`📤 API 请求: ${url}\nBody: ${JSON.stringify(body, null, 2)}`, 'request'); }
    function logResponse(url, data) { addLogToPanel(`📥 API 响应: ${url}\nResponse: ${JSON.stringify(data, null, 2)}`, 'response'); }
    function logSdkRequest(config) { addLogToPanel(`📤 SDK 初始化配置:\n${JSON.stringify(config, null, 2)}`, 'request'); }
    function logSdkEvent(event, data) { addLogToPanel(`📥 SDK 事件: ${event}\n${JSON.stringify(data, null, 2)}`, 'response'); }

    function searchLogs() {
        let keyword = document.getElementById('searchLogInput').value.toLowerCase();
        let panel = document.getElementById('logPanel');
        if (!keyword) {
            panel.innerHTML = allLogs.map(l => l.html).join('');
        } else {
            let filtered = allLogs.filter(l => l.msg.toLowerCase().includes(keyword));
            panel.innerHTML = filtered.map(l => l.html).join('');
            if (filtered.length === 0) panel.innerHTML = '<div>🔍 没有找到匹配的日志</div>';
        }
        panel.scrollTop = panel.scrollHeight;
    }

    function toggleStep3() {
        let content = document.getElementById('step3Content');
        let icon = document.getElementById('step3Icon');
        if (content.classList.contains('hidden')) {
            content.classList.remove('hidden');
            icon.innerHTML = '▼';
        } else {
            content.classList.add('hidden');
            icon.innerHTML = '▶';
        }
    }

    function expandStep3WithData(data) {
        let content = document.getElementById('step3Content');
        let icon = document.getElementById('step3Icon');
        if (content.classList.contains('hidden')) {
            content.classList.remove('hidden');
            icon.innerHTML = '▼';
        }
        let detailDiv = document.getElementById('binResponseDetail');
        detailDiv.innerHTML = `<strong>收到 payment_method_selected 事件:</strong><br>${JSON.stringify(data, null, 2)}`;
        detailDiv.classList.remove('hidden');
    }

    function updateReferenceRequired() {
        let recurringModel = document.getElementById('recurringModel').value;
        let isSubscription = (recurringModel === 'Subscription');
        let refLabel = document.getElementById('referenceLabel');
        if (isSubscription) {
            if (!refLabel.innerHTML.includes('*')) {
                refLabel.innerHTML = 'reference <span class="required-star">*</span>';
            }
        } else {
            refLabel.innerHTML = 'reference';
        }
    }

    // 折叠功能
    function initCollapsibles() {
        const collapsibleTitles = document.querySelectorAll('.section-title.collapsible');
        collapsibleTitles.forEach(title => {
            const content = title.nextElementSibling;
            if (content && content.classList.contains('section-content')) {
                // 默认折叠
                content.classList.add('collapsed');
                title.classList.add('open');
                title.addEventListener('click', () => {
                    content.classList.toggle('collapsed');
                    title.classList.toggle('open');
                });
            }
        });
    }

    document.getElementById('env').addEventListener('change', updateKeysFromEnv);
    document.getElementById('env').addEventListener('input', updateKeysFromEnv);
    document.getElementById('resetKeyBtn').addEventListener('click', resetKey);
    document.getElementById('resetSignKeyBtn').addEventListener('click', resetSignKey);
    document.getElementById('searchLogBtn').addEventListener('click', searchLogs);
    document.getElementById('clearSearchBtn').addEventListener('click', () => { document.getElementById('searchLogInput').value = ''; searchLogs(); });
    document.getElementById('randomOrderBtn').addEventListener('click', () => {
        let n = new Date();
        document.getElementById('merchantOrderId').value = `Demo_${n.getFullYear()}${String(n.getMonth()+1).padStart(2,'0')}${String(n.getDate()).padStart(2,'0')}${String(n.getHours()).padStart(2,'0')}${String(n.getMinutes()).padStart(2,'0')}${String(n.getSeconds()).padStart(2,'0')}${String(n.getMilliseconds()).padStart(3,'0')}`;
        addLog(`随机订单号: ${document.getElementById('merchantOrderId').value}`, 'success');
    });
    document.getElementById('recurringModel').addEventListener('change', updateReferenceRequired);
    document.getElementById('recurringModel').addEventListener('input', updateReferenceRequired);

    function toggleMsgField() {
        let isValid = document.getElementById('binIsValid').value === 'true';
        let msgGroup = document.getElementById('binMsgGroup');
        if (isValid) msgGroup.classList.add('hidden');
        else msgGroup.classList.remove('hidden');
    }
    document.getElementById('binIsValid').addEventListener('change', toggleMsgField);
    
    function toggleTnCFields() {
        let showTnCVal = document.getElementById('showTnC').value;
        let tncFields = document.getElementById('tncFields');
        if (showTnCVal === 'true') {
            tncFields.classList.remove('hidden');
        } else {
            tncFields.classList.add('hidden');
        }
    }
    document.getElementById('showTnC').addEventListener('change', toggleTnCFields);
    document.getElementById('showTnC').addEventListener('input', toggleTnCFields);
    
    function toggleMaxWaitGroup() {
        let val = document.getElementById('verifyBrand').value;
        let maxWaitGroup = document.getElementById('maxWaitGroup');
        if (val === 'true') {
            maxWaitGroup.classList.remove('hidden');
        } else {
            maxWaitGroup.classList.add('hidden');
        }
    }
    document.getElementById('verifyBrand').addEventListener('change', toggleMaxWaitGroup);
    document.getElementById('verifyBrand').addEventListener('input', toggleMaxWaitGroup);

    function getBooleanOrUndefined(inputId) {
        let val = document.getElementById(inputId).value;
        if (val === '') return undefined;
        return val === 'true';
    }

    async function callInteractionApi() {
        let keyId = getRealKeyId(), signKey = getRealSignKey();
        if (!keyId || !signKey) { addLog(currentLang === 'zh' ? '❌ 请填写有效的 KeyID 和 SignKey' : '❌ Please enter valid KeyID and SignKey', 'error'); return; }
        
        try {
            const healthCheck = await fetch('http://localhost:3001/health');
            if (!healthCheck.ok) {
                addLog('⚠️ 代理服务器未响应，请确认已启动: cd ~/evonet-proxy && node server.js', 'warning');
            }
        } catch (e) {
            addLog('❌ 无法连接到代理服务器 (http://localhost:3001)，请先启动代理服务器', 'error');
            return;
        }
        
        let amount = parseFloat(document.getElementById('amount').value);
        let currency = document.getElementById('currency').value;
        
        let merchantOrderInfo = {
            merchantOrderID: document.getElementById('merchantOrderId').value
        };
        let merchantName = document.getElementById('merchantName').value.trim();
        if (merchantName) merchantOrderInfo.merchantName = merchantName;
        let merchantDescription = document.getElementById('merchantDescription').value.trim();
        if (merchantDescription) merchantOrderInfo.merchantDescription = merchantDescription;
        let merchantCategoryCode = document.getElementById('merchantCategoryCode').value.trim();
        if (merchantCategoryCode) merchantOrderInfo.merchantCategoryCode = merchantCategoryCode;
        
        let isCollectEmail = getBooleanOrUndefined('isCollectEmail');
        if (isCollectEmail !== undefined) merchantOrderInfo.isCollectEmail = isCollectEmail;
        let isCollectPhoneNumber = getBooleanOrUndefined('isCollectPhoneNumber');
        if (isCollectPhoneNumber !== undefined) merchantOrderInfo.isCollectPhoneNumber = isCollectPhoneNumber;
        let isCollectBillingAddress = getBooleanOrUndefined('isCollectBillingAddress');
        if (isCollectBillingAddress !== undefined) merchantOrderInfo.isCollectBillingAddress = isCollectBillingAddress;
        let isCollectShippingAddress = getBooleanOrUndefined('isCollectShippingAddress');
        if (isCollectShippingAddress !== undefined) merchantOrderInfo.isCollectShippingAddress = isCollectShippingAddress;
        
        let payload = {
            transAmount: { Value: amount.toString(), currency: currency },
            merchantOrderInfo: merchantOrderInfo,
            expireTime: parseInt(document.getElementById('expiryMinutes').value),
            returnUrl: document.getElementById('returnUrl').value,
            webhookUrl: document.getElementById('webhookUrl').value
        };
        
        let userInfo = {};
        let userRef = document.getElementById('userReference').value.trim();
        if (userRef) userInfo.reference = userRef;
        let userEmail = document.getElementById('userEmail').value.trim();
        if (userEmail) userInfo.email = userEmail;
        let userPhone = document.getElementById('userPhone').value.trim();
        if (userPhone) userInfo.phone = userPhone;
        let userFirstName = document.getElementById('userFirstName').value.trim();
        if (userFirstName) userInfo.firstName = userFirstName;
        let userLastName = document.getElementById('userLastName').value.trim();
        if (userLastName) userInfo.lastName = userLastName;
        let userAddress = document.getElementById('userAddress').value.trim();
        if (userAddress) userInfo.address = userAddress;
        let userCity = document.getElementById('userCity').value.trim();
        if (userCity) userInfo.city = userCity;
        let userState = document.getElementById('userState').value.trim();
        if (userState) userInfo.state = userState;
        let userPostalCode = document.getElementById('userPostalCode').value.trim();
        if (userPostalCode) userInfo.postalCode = userPostalCode;
        let userCountry = document.getElementById('userCountry').value.trim();
        if (userCountry) userInfo.country = userCountry;
        if (Object.keys(userInfo).length > 0) payload.userInfo = userInfo;
        
        let recurringModel = document.getElementById('recurringModel').value;
        if (recurringModel) {
            payload.paymentMethod = { recurringProcessingModel: recurringModel };
        }
        
        let avsVal = getBooleanOrUndefined('avs');
        if (avsVal !== undefined) payload.avs = avsVal;
        
        let requestId = document.getElementById('requestId').value.trim();
        if (requestId) payload.requestId = requestId;
        
        let tradeInfo = document.getElementById('tradeInfo').value.trim();
        if (tradeInfo) {
            try { payload.tradeInfo = JSON.parse(tradeInfo); } 
            catch(e) { addLog('tradeInfo JSON 格式错误', 'error'); return; }
        }
        
        let timestamp = new Date().toISOString().slice(0, 19).replace('T', 'T') + '+08:00';
        
        logRequest(API_PROXY_URL, payload);
        
        try {
            const response = await fetch(API_PROXY_URL, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'KeyID': keyId,
                    'Authorization': signKey,
                    'TimeStamp': timestamp,
                    'Accept': 'application/json'
                },
                body: JSON.stringify(payload)
            });
            
            const data = await response.json();
            logResponse(API_PROXY_URL, data);
            
            if (response.ok && data.sessionID) {
                apiSessionId = data.sessionID;
                document.getElementById('apiSessionDisplay').innerText = apiSessionId;
                document.getElementById('manualSessionId').value = apiSessionId;
                updateSessionHintFull();
                addLog(currentLang === 'zh' ? `✅ Session ID: ${apiSessionId}` : `✅ Session ID: ${apiSessionId}`, 'success');
            } else {
                addLog(currentLang === 'zh' ? `❌ API 失败: ${JSON.stringify(data)}` : `❌ API failed: ${JSON.stringify(data)}`, 'error');
            }
        } catch (err) {
            addLog(currentLang === 'zh' ? `❌ 请求异常: ${err.message}` : `❌ Request error: ${err.message}`, 'error');
        }
    }

    function handlePaymentMethodSelected(data) {
        if (getBooleanOrUndefined('verifyBrand') !== true) return;
        pendingVerificationId = data.verificationID;
        logSdkEvent('payment_method_selected', data);
        expandStep3WithData(data);
        let sd = document.getElementById('binEventStatus');
        sd.innerHTML = `⏳ ${currentLang === 'zh' ? '等待响应...' : 'Waiting...'} ID: ${data.verificationID}<br>💡 ${currentLang === 'zh' ? '选择后点击下方按钮' : 'Select and click button'}`;
        if (!document.getElementById('sendBinBtn')) {
            let btn = document.createElement('button');
            btn.id = 'sendBinBtn';
            btn.textContent = currentLang === 'zh' ? '📤 发送响应' : '📤 Send Response';
            btn.style.cssText = 'margin-top:6px;width:100%;padding:5px;font-size:0.7rem;background:#336DF3;color:white;border:none;border-radius:40px;cursor:pointer';
            btn.onclick = sendBinResponse;
            sd.appendChild(btn);
        }
    }
    
    function sendBinResponse() {
        if (!pendingVerificationId) { addLog(currentLang === 'zh' ? '无待响应请求' : 'No pending request', 'warning'); return; }
        let isValid = document.getElementById('binIsValid').value === 'true', msg = document.getElementById('binMsg').value.trim();
        if (!isValid && !msg) { addLog(currentLang === 'zh' ? '❌ 拒绝时 msg 为必填' : '❌ msg required when rejecting', 'error'); return; }
        if (isValid) msg = '';
        let response = { isValid, msg, id: pendingVerificationId };
        logSdkEvent('callbackVerification', response);
        if (sdkInstance && typeof sdkInstance.callbackVerification === 'function') {
            sdkInstance.callbackVerification(response);
            addLog(`✅ ${currentLang === 'zh' ? '响应已发送' : 'Response sent'}: isValid=${isValid}`, isValid ? 'success' : 'warning');
            pendingVerificationId = null;
            document.getElementById('binEventStatus').innerHTML = '⏳ ' + (currentLang === 'zh' ? '等待验证事件...' : 'Waiting for event...');
            let b = document.getElementById('sendBinBtn'); if (b) b.remove();
        } else { addLog(currentLang === 'zh' ? '❌ 无法发送响应' : '❌ Cannot send response', 'error'); }
    }

    function buildSdkConfig() {
        let sid = document.getElementById('manualSessionId').value.trim() || apiSessionId;
        if (!sid) { addLog(currentLang === 'zh' ? '❌ 无 Session ID' : '❌ No Session ID', 'error'); return null; }
        let showTnC = getBooleanOrUndefined('showTnC');
        if (showTnC === true) {
            let tncUrl = document.getElementById('tncUrl').value.trim();
            if (!tncUrl) { addLog(currentLang === 'zh' ? '❌ TnC Url 为必填' : '❌ TnC Url required', 'error'); return null; }
        }
        let appearance = {};
        ['colorBg','colorPrimary','colorAction','colorBoxStroke','colorDisabled','colorError','colorFormBackground','colorFormBorder','colorInverse','colorBoxFillingOutline','colorPlaceholder','colorSecondary'].forEach(f => { appearance[f] = document.getElementById(f).value; });
        let br = document.getElementById('borderRadius').value; if (br) { appearance.borderRadius = br.includes(',') ? br.split(',').map(v=>parseInt(v.trim())) : parseInt(br); }
        
        let uiOption = {
            card: {
                showCardHolderName: getBooleanOrUndefined('showCardHolderName'),
                CVVForSavedCard: getBooleanOrUndefined('cvvSavedCard'),
                showScanCardButton: getBooleanOrUndefined('showScanCard'),
                autoInvokeCardScanner: getBooleanOrUndefined('autoInvokeScanner')
            },
            showSaveImage: getBooleanOrUndefined('showSaveImage'),
            TnC: showTnC !== undefined ? { showTnC: showTnC, mode: document.getElementById('tncMode').value, url: document.getElementById('tncUrl').value.trim() } : { showTnC: false }
        };
        Object.keys(uiOption.card).forEach(k => uiOption.card[k] === undefined && delete uiOption.card[k]);
        if (uiOption.showSaveImage === undefined) delete uiOption.showSaveImage;
        
        let verifyOption = { isVerifyPaymentBrand: getBooleanOrUndefined('verifyBrand') };
        if (verifyOption.isVerifyPaymentBrand === true && document.getElementById('maxWaitTime').value) {
            verifyOption.maxWaitTime = document.getElementById('maxWaitTime').value;
        } else if (verifyOption.isVerifyPaymentBrand === undefined) {
            delete verifyOption.isVerifyPaymentBrand;
        }
        
        let columnsVal = getBooleanOrUndefined('columns');
        let logoPos = document.getElementById('logoPosition').value.trim();
        let config = {
            id: containerId,
            type: 'payment',
            sessionID: sid,
            locale: document.getElementById('locale').value,
            mode: document.getElementById('mode').value,
            environment: document.getElementById('env').value,
            appearance: appearance,
            uiOption: uiOption,
            verifyOption: verifyOption,
            payment_method_selected: handlePaymentMethodSelected,
            payment_completed: (r) => { logSdkEvent('payment_completed', r); addLog(`${currentLang === 'zh' ? '✅ 支付完成' : '✅ Payment completed'} | merchantTransID: ${r?.merchantTransID || 'N/A'}`, 'success'); },
            payment_failed: (r) => { logSdkEvent('payment_failed', r); addLog(`${currentLang === 'zh' ? '❌ 支付失败' : '❌ Payment failed'}: ${r?.message || ''}`, 'error'); },
            payment_not_preformed: (r) => { logSdkEvent('payment_not_preformed', r); addLog(`${currentLang === 'zh' ? '⚠️ 支付未完成' : '⚠️ Not performed'}: ${r?.message || ''}`, 'warning'); },
            payment_cancelled: () => { addLog(`${currentLang === 'zh' ? '🚫 已取消' : '🚫 Cancelled'}`, 'info'); }
        };
        if (columnsVal !== undefined) config.Columns = columnsVal;
        if (logoPos !== '') config.logoPosition = logoPos;
        
        logSdkRequest(config);
        return config;
    }
    
    async function initSdk() {
        addLog(currentLang === 'zh' ? '🚀 初始化 SDK...' : '🚀 Initializing SDK...', 'info');
        if (sdkInstance && typeof sdkInstance.unmount === 'function') { sdkInstance.unmount(); sdkInstance = null; }
        let config = buildSdkConfig(); if (!config) return;
        let c = document.querySelector(containerId); if (c) c.innerHTML = '';
        if (typeof DropInSDK === 'undefined') { addLog(currentLang === 'zh' ? '❌ SDK 库未加载' : '❌ SDK not loaded', 'error'); return; }
        try { sdkInstance = new DropInSDK(config); addLog(currentLang === 'zh' ? '✨ SDK 初始化成功' : '✨ SDK initialized', 'success'); document.getElementById('renderSection')?.scrollIntoView({ behavior: 'smooth', block: 'start' }); }
        catch (err) { addLog(`${currentLang === 'zh' ? '❌ 初始化失败' : '❌ Init failed'}: ${err.message}`, 'error'); }
    }
    
    function updateSessionHintFull() { let manual = document.getElementById('manualSessionId').value.trim(); let current = manual || apiSessionId; let span = document.getElementById('currentSessionHintFull'); let btn = document.getElementById('copySessionBtn'); if (current) { span.innerText = current; btn.style.display = 'inline-flex'; } else { span.innerText = '(未设置)'; btn.style.display = 'none'; } return current; }
    function copySessionId() { let sid = document.getElementById('currentSessionHintFull').innerText; if (sid && sid !== '(未设置)') { navigator.clipboard.writeText(sid); addLog(currentLang === 'zh' ? '✅ Session ID 已复制' : '✅ Session ID copied', 'success'); } }

    const translations = {
        zh: {
            subText: 'Step 1: 获取 Session ID (通过本地代理) → Step 2: 渲染 SDK → Step 3: 处理事件响应',
            warningBox: '⚠️ 重要提示：请先启动本地代理服务器: cd ~/evonet-proxy && node server.js',
            step1Title: '🔑 Step 1 · 获取 Session ID', step1Hint: '💡 通过本地代理调用 Interaction API',
            keyIdLabel: 'KeyID *', signKeyLabel: 'SignKey *',
            step2Title: '⚙️ Step 2 · 渲染 SDK',
            sessionIdLabel: 'Session ID *',
            basicParamsTitle: '基本参数', envLabel: 'environment *',
            modeLabel: 'mode *', localeLabel: 'locale',
            cardOptionsTitle: '卡片支付选项', showCardHolderNameLabel: 'showCardHolderName',
            cvvSavedCardLabel: 'CVVForSavedCard', showScanCardLabel: 'showScanCardButton',
            autoInvokeLabel: 'autoInvokeCardScanner', showSaveImageLabel: 'showSaveImage',
            tncTitle: '条款与条件 (TnC)', showTnCLabel: 'showTnC',
            tncModeLabel: 'TnC mode *', tncUrlLabel: 'TnC Url *',
            binTitle: 'Card BIN 验证', verifyBrandLabel: 'isVerifyPaymentBrand', maxWaitLabel: 'maxWaitTime (秒)',
            layoutTitle: '布局控制', columnsLabel: 'Columns', logoPositionLabel: 'logoPosition',
            borderRadiusLabel: 'borderRadius', colorTitle: '颜色主题',
            currentSessionHintLabel: '💡 Session ID：',
            step3Title: '🎯 Step 3 · Event Response (BIN 验证响应)', isValidLabel: 'isValid *', msgLabel: 'msg *',
            renderTitle: '📱 Drop-in 渲染区域', logTitle: '📡 事件日志', clearLog: '清空日志',
            initSdkBtn: '✨ 初始化 SDK', brandDemoText: 'Drop-in 效果演示',
            apiSessionLabel: '📦 Session ID：', resetAllBtn: '重置所有参数',
            searchBtn: '搜索', clearSearchBtn: '清除', copyBtn: '复制',
            placeholderMerchantName: '商户名称', placeholderMerchantDesc: '商户描述', placeholderMCC: 'MCC 码',
            placeholderReturnUrl: '支付完成跳转地址', placeholderWebhookUrl: '异步通知地址',
            placeholderUserRef: '用户唯一标识', placeholderUserEmail: 'user@example.com', placeholderUserPhone: '电话号码',
            placeholderFirstName: '名', placeholderLastName: '姓', placeholderAddress: '地址', placeholderCity: '城市',
            placeholderState: '州/省', placeholderCountry: '国家代码 (如: TH)', placeholderPostalCode: '邮编',
            placeholderRequestId: 'UUID / GUID', placeholderTradeInfo: '{"key":"value"}',
            placeholderTncUrl: 'https://example.com/terms', placeholderBorderRadius: '8,8,8,8 或 16',
            placeholderSessionId: 'Create session ID by interaction API', placeholderExpiry: '5-43200分钟',
            placeholderBinMsg: '拒绝原因', placeholderSearchLog: '🔍 搜索日志...',
            resetKeyBtn: '重置', resetSignKeyBtn: '重置', randomOrderBtn: '🔄',
            authTitle: 'API Authentication *', orderInfoTitle: '订单信息 *',
            paymentMethodTitle: '支付方式配置', userInfoTitle: '用户信息', otherParamsTitle: '其他交易参数',
            miscParamsTitle: '其他参数', referenceLabel: 'reference'
        },
        en: {
            subText: 'Step 1: Get Session ID (via local proxy) → Step 2: Render SDK → Step 3: Handle Event Response',
            warningBox: '⚠️ Important: Please start the local proxy server first: cd ~/evonet-proxy && node server.js',
            step1Title: '🔑 Step 1 · Get Session ID', step1Hint: '💡 Call Interaction API via local proxy',
            keyIdLabel: 'KeyID *', signKeyLabel: 'SignKey *',
            step2Title: '⚙️ Step 2 · Render SDK',
            sessionIdLabel: 'Session ID *',
            basicParamsTitle: 'Basic Parameters', envLabel: 'environment *',
            modeLabel: 'mode *', localeLabel: 'locale',
            cardOptionsTitle: 'Card Payment Options', showCardHolderNameLabel: 'showCardHolderName',
            cvvSavedCardLabel: 'CVVForSavedCard', showScanCardLabel: 'showScanCardButton',
            autoInvokeLabel: 'autoInvokeCardScanner', showSaveImageLabel: 'showSaveImage',
            tncTitle: 'Terms & Conditions (TnC)', showTnCLabel: 'showTnC',
            tncModeLabel: 'TnC mode *', tncUrlLabel: 'TnC Url *',
            binTitle: 'Card BIN Verification', verifyBrandLabel: 'isVerifyPaymentBrand', maxWaitLabel: 'maxWaitTime (sec)',
            layoutTitle: 'Layout Control', columnsLabel: 'Columns', logoPositionLabel: 'logoPosition',
            borderRadiusLabel: 'borderRadius', colorTitle: 'Color Theme',
            currentSessionHintLabel: '💡 Session ID:',
            step3Title: '🎯 Step 3 · Event Response (BIN Verification)', isValidLabel: 'isValid *', msgLabel: 'msg *',
            renderTitle: '📱 Drop-in Render Area', logTitle: '📡 Event Log', clearLog: 'Clear Log',
            initSdkBtn: '✨ Initialize SDK', brandDemoText: 'Drop-in payment Demo',
            apiSessionLabel: '📦 Session ID:', resetAllBtn: 'Reset All Parameters',
            searchBtn: 'Search', clearSearchBtn: 'Clear', copyBtn: 'Copy',
            placeholderMerchantName: 'Merchant Name', placeholderMerchantDesc: 'Merchant Description', placeholderMCC: 'MCC Code',
            placeholderReturnUrl: 'Payment completion redirect URL', placeholderWebhookUrl: 'Webhook notification URL',
            placeholderUserRef: 'Unique user identifier', placeholderUserEmail: 'user@example.com', placeholderUserPhone: 'Phone number',
            placeholderFirstName: 'First Name', placeholderLastName: 'Last Name', placeholderAddress: 'Address', placeholderCity: 'City',
            placeholderState: 'State/Province', placeholderCountry: 'Country code (e.g., TH)', placeholderPostalCode: 'Postal Code',
            placeholderRequestId: 'UUID / GUID', placeholderTradeInfo: '{"key":"value"}',
            placeholderTncUrl: 'https://example.com/terms', placeholderBorderRadius: '8,8,8,8 or 16',
            placeholderSessionId: 'Create session ID by interaction API', placeholderExpiry: '5-43200 minutes',
            placeholderBinMsg: 'Rejection reason', placeholderSearchLog: '🔍 Search logs...',
            resetKeyBtn: 'Reset', resetSignKeyBtn: 'Reset', randomOrderBtn: '🔄',
            authTitle: 'API Authentication *', orderInfoTitle: 'Order Information *',
            paymentMethodTitle: 'Payment Method Config', userInfoTitle: 'User Info', otherParamsTitle: 'Other Transaction Parameters',
            miscParamsTitle: 'Other Parameters', referenceLabel: 'reference'
        }
    };
    
    function updateLanguage() {
        let t = translations[currentLang];
        document.getElementById('subText').innerText = t.subText;
        document.getElementById('warningBox').innerHTML = t.warning;
        document.getElementById('step1Title').innerHTML = t.step1Title;
        document.getElementById('step1Hint').innerHTML = t.step1Hint;
        document.getElementById('keyIdLabel').innerHTML = t.keyIdLabel;
        document.getElementById('signKeyLabel').innerHTML = t.signKeyLabel;
        document.getElementById('step2Title').innerHTML = t.step2Title;
        document.getElementById('sessionIdLabel').innerHTML = t.sessionIdLabel;
        document.getElementById('basicParamsTitle').innerHTML = t.basicParamsTitle + ' <span class="required-star">*</span>';
        document.getElementById('envLabel').innerHTML = t.envLabel;
        document.getElementById('modeLabel').innerHTML = t.modeLabel;
        document.getElementById('localeLabel').innerHTML = t.localeLabel;
        document.getElementById('cardOptionsTitle').innerHTML = t.cardOptionsTitle;
        document.getElementById('showCardHolderNameLabel').innerHTML = t.showCardHolderNameLabel;
        document.getElementById('cvvSavedCardLabel').innerHTML = t.cvvSavedCardLabel;
        document.getElementById('showScanCardLabel').innerHTML = t.showScanCardLabel;
        document.getElementById('autoInvokeLabel').innerHTML = t.autoInvokeLabel;
        document.getElementById('showSaveImageLabel').innerHTML = t.showSaveImageLabel;
        document.getElementById('tncTitle').innerHTML = t.tncTitle;
        document.getElementById('showTnCLabel').innerHTML = t.showTnCLabel;
        document.getElementById('tncModeLabel').innerHTML = t.tncModeLabel;
        document.getElementById('tncUrlLabel').innerHTML = t.tncUrlLabel;
        document.getElementById('binTitle').innerHTML = t.binTitle;
        document.getElementById('verifyBrandLabel').innerHTML = t.verifyBrandLabel;
        document.getElementById('maxWaitLabel').innerHTML = t.maxWaitLabel;
        document.getElementById('layoutTitle').innerHTML = t.layoutTitle;
        document.getElementById('columnsLabel').innerHTML = t.columnsLabel;
        document.getElementById('logoPositionLabel').innerHTML = t.logoPositionLabel;
        document.getElementById('borderRadiusLabel').innerHTML = t.borderRadiusLabel;
        document.getElementById('colorTitle').innerHTML = t.colorTitle;
        document.getElementById('currentSessionHintLabel').innerHTML = t.currentSessionHintLabel;
        document.getElementById('renderTitle').innerHTML = t.renderTitle;
        document.getElementById('logTitle').innerHTML = t.logTitle;
        document.getElementById('clearLogBtn').innerHTML = t.clearLog;
        document.getElementById('initSdkBtn').innerHTML = t.initSdkBtn;
        document.getElementById('resetAllBtn').innerHTML = t.resetAllBtn;
        document.getElementById('searchLogBtn').innerHTML = t.searchBtn;
        document.getElementById('clearSearchBtn').innerHTML = t.clearSearchBtn;
        document.getElementById('copySessionBtn').innerHTML = t.copyBtn;
        document.getElementById('resetKeyBtn').innerHTML = t.resetKeyBtn;
        document.getElementById('resetSignKeyBtn').innerHTML = t.resetSignKeyBtn;
        document.getElementById('randomOrderBtn').innerHTML = t.randomOrderBtn;
        document.getElementById('brandDemoText').innerText = t.brandDemoText;
        document.getElementById('apiSessionLabel').innerHTML = t.apiSessionLabel;
        document.getElementById('step3Title').innerHTML = t.step3Title;
        document.getElementById('isValidLabel').innerHTML = t.isValidLabel;
        document.getElementById('msgLabel').innerHTML = t.msgLabel;
        document.getElementById('authTitle').innerHTML = t.authTitle;
        document.getElementById('orderInfoTitle').innerHTML = t.orderInfoTitle;
        document.getElementById('paymentMethodTitle').innerHTML = t.paymentMethodTitle;
        document.getElementById('userInfoTitle').innerHTML = t.userInfoTitle;
        document.getElementById('otherParamsTitle').innerHTML = t.otherParamsTitle;
        document.getElementById('miscParamsTitle').innerHTML = t.miscParamsTitle;
        document.getElementById('referenceLabel').innerHTML = t.referenceLabel;

        // 更新 placeholder
        document.getElementById('merchantName').placeholder = t.placeholderMerchantName;
        document.getElementById('merchantDescription').placeholder = t.placeholderMerchantDesc;
        document.getElementById('merchantCategoryCode').placeholder = t.placeholderMCC;
        document.getElementById('returnUrl').placeholder = t.placeholderReturnUrl;
        document.getElementById('webhookUrl').placeholder = t.placeholderWebhookUrl;
        document.getElementById('userReference').placeholder = t.placeholderUserRef;
        document.getElementById('userEmail').placeholder = t.placeholderUserEmail;
        document.getElementById('userPhone').placeholder = t.placeholderUserPhone;
        document.getElementById('userFirstName').placeholder = t.placeholderFirstName;
        document.getElementById('userLastName').placeholder = t.placeholderLastName;
        document.getElementById('userAddress').placeholder = t.placeholderAddress;
        document.getElementById('userCity').placeholder = t.placeholderCity;
        document.getElementById('userState').placeholder = t.placeholderState;
        document.getElementById('userCountry').placeholder = t.placeholderCountry;
        document.getElementById('userPostalCode').placeholder = t.placeholderPostalCode;
        document.getElementById('requestId').placeholder = t.placeholderRequestId;
        document.getElementById('tradeInfo').placeholder = t.placeholderTradeInfo;
        document.getElementById('tncUrl').placeholder = t.placeholderTncUrl;
        document.getElementById('borderRadius').placeholder = t.placeholderBorderRadius;
        document.getElementById('manualSessionId').placeholder = t.placeholderSessionId;
        document.getElementById('expiryMinutes').placeholder = t.placeholderExpiry;
        document.getElementById('binMsg').placeholder = t.placeholderBinMsg;
        document.getElementById('searchLogInput').placeholder = t.placeholderSearchLog;

        document.getElementById('langZhBtn').classList.toggle('active', currentLang === 'zh');
        document.getElementById('langEnBtn').classList.toggle('active', currentLang === 'en');
        document.getElementById('callApiBtn').innerHTML = '🚀 ' + (currentLang === 'zh' ? '调用 Interaction API (通过本地代理)' : 'Call Interaction API (via local proxy)');
    }
    
    document.getElementById('langZhBtn').addEventListener('click', () => { currentLang = 'zh'; updateLanguage(); addLog('语言: 中文', 'info'); });
    document.getElementById('langEnBtn').addEventListener('click', () => { currentLang = 'en'; updateLanguage(); addLog('Language: English', 'info'); });
    document.getElementById('callApiBtn').addEventListener('click', callInteractionApi);
    document.getElementById('initSdkBtn').addEventListener('click', initSdk);
    document.getElementById('resetAllBtn').addEventListener('click', resetAllParameters);
    document.getElementById('clearLogBtn').addEventListener('click', () => { allLogs = []; document.getElementById('logPanel').innerHTML = '🟢 ' + (currentLang === 'zh' ? '日志已清空' : 'Log cleared') + '<br>'; });
    document.getElementById('copySessionBtn').addEventListener('click', copySessionId);
    document.getElementById('manualSessionId').addEventListener('input', updateSessionHintFull);
    document.getElementById('merchantOrderId').value = `Demo_${new Date().getFullYear()}${String(new Date().getMonth()+1).padStart(2,'0')}${String(new Date().getDate()).padStart(2,'0')}${String(new Date().getHours()).padStart(2,'0')}${String(new Date().getMinutes()).padStart(2,'0')}${String(new Date().getSeconds()).padStart(2,'0')}${String(new Date().getMilliseconds()).padStart(3,'0')}`;
    updateKeysFromEnv(); setupKeyInputs(); toggleMsgField(); toggleTnCFields(); toggleMaxWaitGroup(); updateReferenceRequired(); initCollapsibles(); captureInitialState(); updateLanguage(); addLog('✅ Drop-in Demo 已就绪 - 请启动代理服务器 (端口 3001)', 'success');
</script>
</body>
</html>
