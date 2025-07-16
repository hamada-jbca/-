<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>警備業システム デモ</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* Custom styles */
        body {
            font-family: 'Inter', sans-serif; /* Prioritize Inter as per instructions */
            background-color: #f0f2f5; /* Light gray background */
            color: #333;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 2rem;
        }
        .sidebar {
            width: 240px;
            background-color: #2c3e50; /* Dark blue-gray */
            color: #ecf0f1;
            padding: 1.5rem 0;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            border-radius: 0.75rem; /* Rounded corners */
            position: fixed;
            height: calc(100vh - 4rem); /* Adjust height for padding */
            top: 2rem;
            left: 2rem;
            display: flex;
            flex-direction: column;
        }
        .sidebar-item {
            display: flex;
            align-items: center;
            padding: 0.75rem 1.5rem;
            cursor: pointer;
            transition: background-color 0.3s ease, color 0.3s ease;
            border-radius: 0.5rem;
            margin: 0.25rem 1rem;
        }
        .sidebar-item:hover, .sidebar-item.active {
            background-color: #34495e; /* Slightly lighter blue-gray on hover/active */
            color: #3498db; /* Accent blue */
        }
        .sidebar-item i {
            margin-right: 0.75rem;
            font-size: 1.1rem;
        }
        .content-area {
            margin-left: 280px; /* Space for sidebar + padding */
            padding: 2rem;
            min-height: calc(100vh - 4rem);
            background-color: #ffffff;
            border-radius: 0.75rem;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            position: relative; /* For dashboard animation */
            overflow: hidden; /* For dashboard animation */
        }
        /* Card styles */
        .card {
            background-color: #ffffff;
            border-radius: 0.75rem;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            padding: 1.5rem;
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .card:hover {
            transform: translateY(-3px);
            box-shadow: 0 4px 16px rgba(0,0,0,0.1);
        }
        /* Button styles */
        .btn {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.3s ease, box-shadow 0.3s ease, transform 0.1s ease;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .btn-primary {
            background-color: #3498db; /* Accent blue */
            color: #ffffff;
        }
        .btn-primary:hover {
            background-color: #2980b9; /* Darker blue on hover */
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            transform: translateY(-1px);
        }
        .btn-secondary {
            background-color: #ecf0f1; /* Light gray */
            color: #34495e;
            border: 1px solid #bdc3c7;
        }
        .btn-secondary:hover {
            background-color: #bdc3c7;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            transform: translateY(-1px);
        }
        .btn-danger {
            background-color: #e74c3c; /* Red */
            color: #ffffff;
        }
        .btn-danger:hover {
            background-color: #c0392b;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            transform: translateY(-1px);
        }

        /* Form input focus animation */
        input:focus, select:focus, textarea:focus {
            outline: none;
            border-color: transparent; /* Hide default border */
            box-shadow: 0 0 0 2px #3498db; /* Blue ring on focus */
            transition: box-shadow 0.3s ease;
        }

        /* Table styles */
        .table-auto {
            width: 100%;
            border-collapse: collapse;
        }
        .table-auto th, .table-auto td {
            padding: 0.75rem;
            border-bottom: 1px solid #e0e0e0;
            text-align: left;
        }
        .table-auto th {
            background-color: #f5f7fa;
            font-weight: 600;
            color: #4a4a4a;
        }
        .table-auto tbody tr:hover {
            background-color: #f9f9f9;
        }

        /* Modal styles */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.4); /* Lightly transparent background */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.open {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            background-color: #ffffff;
            padding: 2rem;
            border-radius: 0.75rem;
            box-shadow: 0 8px 24px rgba(0,0,0,0.2);
            max-width: 500px;
            width: 90%;
            transform: translateY(-20px) scale(0.95);
            opacity: 0;
            transition: transform 0.3s ease-out, opacity 0.3s ease-out;
        }
        .modal-overlay.open .modal-content {
            transform: translateY(0) scale(1);
            opacity: 1;
        }

        /* Screen transition (fade) */
        .view-transition {
            animation: fade-in 0.3s ease-out;
        }
        @keyframes fade-in {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Badge/Chip styles */
        .badge {
            display: inline-block;
            padding: 0.3rem 0.7rem;
            border-radius: 0.5rem;
            font-size: 0.8rem;
            font-weight: 600;
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .badge:hover {
            transform: translateY(-1px);
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .badge-blue { background-color: #e0f2fe; color: #2196f3; } /* Light blue for day shift */
        .badge-purple { background-color: #ede7f6; color: #673ab7; } /* Light purple for night shift */
        .badge-green { background-color: #e8f5e9; color: #4caf50; } /* Light green for traffic */
        .badge-orange { background-color: #fff3e0; color: #ff9800; } /* Light orange for facility */

        /* Graph placeholder styles */
        .graph-bar {
            background-color: #3498db;
            height: 20px;
            border-radius: 0.25rem;
            transition: width 0.5s ease-out;
        }
        .graph-pie-container {
            position: relative;
            width: 120px;
            height: 120px;
            border-radius: 50%;
            background: conic-gradient(#3498db 0% var(--fill-percentage), #e0e0e0 var(--fill-percentage) 100%);
            transition: background 1s ease-out;
        }
        .graph-pie-center {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            width: 80px;
            height: 80px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 0.9rem;
        }

        /* Dashboard Mascot Animation */
        .mascot-container {
            position: absolute;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 100%;
            height: 150px; /* Adjust height as needed */
            overflow: hidden;
            z-index: 0; /* Behind content */
            pointer-events: none; /* Do not interfere with clicks */
        }
        .mascot-animation {
            position: absolute;
            bottom: 10px;
            left: -100px; /* Start off-screen */
            font-size: 80px; /* Adjust size of emoji */
            animation: walk-across 20s linear infinite; /* Adjust duration for speed */
            white-space: nowrap; /* Prevent emoji from wrapping */
            filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.2));
        }

        @keyframes walk-across {
            0% { left: -100px; transform: scaleX(1); } /* Start left, facing right */
            49% { transform: scaleX(1); }
            50% { left: calc(100% + 100px); transform: scaleX(-1); } /* Move right, flip, move off-screen */
            99% { transform: scaleX(-1); }
            100% { left: -100px; transform: scaleX(1); } /* Move left, flip, back to start */
        }
    </style>
</head>
<body class="flex min-h-screen">
    <!-- Sidebar -->
    <aside class="sidebar">
        <div class="px-6 py-4 text-2xl font-bold text-center text-white">
            警備システム
        </div>
        <nav class="flex-grow mt-4">
            <div class="sidebar-item active" data-view="dashboard">
                <i class="fas fa-tachometer-alt"></i>
                <span>ダッシュボード</span>
            </div>
            <div class="sidebar-item" data-view="master-management">
                <i class="fas fa-cogs"></i>
                <span>マスタ管理</span>
            </div>
            <div class="sidebar-item" data-view="assignment-daily">
                <i class="fas fa-calendar-day"></i>
                <span>配置管理 (日別)</span>
            </div>
            <div class="sidebar-item" data-view="assignment-weekly">
                <i class="fas fa-calendar-week"></i>
                <span>配置管理 (週別)</span>
            </div>
            <div class="sidebar-item" data-view="performance-input">
                <i class="fas fa-clipboard-check"></i>
                <span>実績入力</span>
            </div>
            <div class="sidebar-item" data-view="guard-ledger">
                <i class="fas fa-book"></i>
                <span>勤務台帳</span>
            </div>
            <div class="sidebar-item" data-view="backup">
                <i class="fas fa-save"></i>
                <span>バックアップ</span>
            </div>
        </nav>
        <div class="mt-auto px-6 py-4 text-sm text-gray-400 text-center">
            &copy; 2025 警備システムデモ
        </div>
    </aside>

    <!-- Main Content Area -->
    <main id="content-area" class="content-area flex-grow">
        <!-- コンテンツを読み込み中...またはJavaScriptが機能しない場合の代替メッセージ -->
        <p class="text-gray-500 text-center py-8">コンテンツを読み込み中...</p>
    </main>

    <!-- Modal Container -->
    <div id="modal-overlay" class="modal-overlay">
        <div id="modal-content" class="modal-content">
            <div class="flex justify-between items-center mb-4">
                <h3 id="modal-title" class="text-xl font-semibold text-gray-800"></h3>
                <button id="modal-close-btn" class="text-gray-500 hover:text-gray-700">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div id="modal-body" class="text-gray-700"></div>
            <div id="modal-footer" class="mt-6 flex justify-end space-x-3"></div>
        </div>
    </div>

    <script>
        console.log("スクリプトが読み込まれました。"); // スクリプト読み込み確認用ログ

        // --- Core Application State and Utilities ---
        const appState = {
            currentView: 'dashboard',
            data: {
                clients: [],
                sites: [],
                guards: [],
                workTypes: [],
                assignments: [], // { id, date, clientId, siteId, workTypeId, requiredGuards, assignedGuards: [{ guardId, actualStartTime, actualEndTime }] }
                notifications: [],
            },
            // For editing/adding master data
            editingMaster: null, // 'clients', 'sites', 'guards', 'workTypes'
            editingRecord: null, // The actual record object
            currentAssignmentInModal: null, // For daily/weekly assignment editing in modal
        };

        const LOCAL_STORAGE_KEY = 'securitySystemData';

        // Global constants for common CSS classes
        const inputClass = "mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 focus:ring-blue-500 focus:border-blue-500 sm:text-sm";
        const labelClass = "block text-sm font-medium text-gray-700";

        // Global arrays for master data options (used in forms)
        const qualifications = ["なし", "交通誘導2級", "施設警備2級", "雑踏警備1級", "指導教育責任者"];
        const employmentTypes = ["正社員", "アルバイト", "契約社員"];
        const prefectures = [
            "北海道", "青森県", "岩手県", "宮城県", "秋田県", "山形県", "福島県", "茨城県", "栃木県", "群馬県",
            "埼玉県", "千葉県", "東京都", "神奈川県", "新潟県", "富山県", "石川県", "福井県", "山梨県", "長野県",
            "岐阜県", "静岡県", "愛知県", "三重県", "滋賀県", "京都府", "大阪府", "兵庫県", "奈良県", "和歌山県",
            "鳥取県", "島根県", "岡山県", "広島県", "山口県", "徳島県", "香川県", "愛媛県", "高知県", "福岡県",
            "佐賀県", "長崎県", "熊本県", "大分県", "宮崎県", "鹿児島県", "沖縄県"
        ];


        /**
         * データをローカルストレージに保存する
         */
        function saveData() {
            try {
                localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(appState.data));
                console.log('データがローカルストレージに保存されました。');
            } catch (e) {
                console.error('データの保存中にエラーが発生しました:', e);
                showModal('エラー', 'データの保存中にエラーが発生しました。ストレージ容量を確認してください。');
            }
        }

        /**
         * ローカルストレージからデータをロードする
         */
        function loadData() {
            try {
                const storedData = localStorage.getItem(LOCAL_STORAGE_KEY);
                if (storedData) {
                    appState.data = JSON.parse(storedData);
                    console.log('データがローカルストレージからロードされました。');
                } else {
                    console.log('ローカルストレージにデータが見つかりませんでした。初期データを生成します。');
                    generateInitialData();
                }
            } catch (e) {
                console.error('データのロード中にエラーが発生しました:', e);
                showModal('エラー', 'データのロード中にエラーが発生しました。データが破損している可能性があります。');
                // Fallback to initial data if loading fails
                generateInitialData();
            }
        }

        /**
         * モーダルを表示する
         * @param {string} title - モーダルのタイトル
         * @param {string} bodyHtml - モーダルのコンテンツ (HTML文字列)
         * @param {Array<Object>} buttons - フッターボタンの配列 [{ text, className, onClick }]
         */
        function showModal(title, bodyHtml, buttons = []) {
            const modalOverlay = document.getElementById('modal-overlay');
            const modalTitle = document.getElementById('modal-title');
            const modalBody = document.getElementById('modal-body');
            const modalFooter = document.getElementById('modal-footer');

            modalTitle.textContent = title;
            modalBody.innerHTML = bodyHtml;
            modalFooter.innerHTML = ''; // Clear previous buttons

            buttons.forEach(btnConfig => {
                const button = document.createElement('button');
                button.textContent = btnConfig.text;
                button.className = `btn ${btnConfig.className || 'btn-secondary'}`;
                button.onclick = () => {
                    if (btnConfig.onClick) btnConfig.onClick();
                    hideModal();
                };
                modalFooter.appendChild(button);
            });

            // Add a default close button if no custom buttons are provided and it's not a confirmation
            if (buttons.length === 0) {
                const closeBtn = document.createElement('button');
                closeBtn.textContent = '閉じる';
                closeBtn.className = 'btn btn-secondary';
                closeBtn.onclick = hideModal;
                modalFooter.appendChild(closeBtn);
            }

            modalOverlay.classList.add('open');
        }

        /**
         * モーダルを非表示にする
         */
        function hideModal() {
            document.getElementById('modal-overlay').classList.remove('open');
            appState.currentAssignmentInModal = null; // Clear modal state on close
        }

        // モーダルクローズボタンのイベントリスナー
        document.getElementById('modal-close-btn').addEventListener('click', hideModal);
        // モーダルオーバーレイをクリックで閉じる（コンテンツはクリックしても閉じない）
        document.getElementById('modal-overlay').addEventListener('click', (e) => {
            if (e.target === document.getElementById('modal-overlay')) {
                hideModal();
            }
        });

        /**
         * 日付を 'YYYY-MM-DD' 形式でフォーマットする
         * @param {Date} date - フォーマットするDateオブジェクト
         * @returns {string} フォーマットされた日付文字列
         */
        function formatDate(date) {
            const year = date.getFullYear();
            const month = String(date.getMonth() + 1).padStart(2, '0');
            const day = String(date.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        /**
         * 日付文字列を 'YYYY年MM月DD日' 形式でフォーマットする
         * @param {string} dateString - 'YYYY-MM-DD' 形式の日付文字列
         * @returns {string} フォーマットされた日付文字列
         */
        function formatJapaneseDate(dateString) {
            const [year, month, day] = dateString.split('-');
            return `${year}年${month}月${day}日`;
        }

        /**
         * 時間を 'HH:MM' 形式でフォーマットする
         * @param {Date} date - フォーマットするDateオブジェクト
         * @returns {string} フォーマットされた時間文字列
         */
        function formatTime(date) {
            const hours = String(date.getHours()).padStart(2, '0');
            const minutes = String(date.getMinutes()).padStart(2, '0');
            return `${hours}:${minutes}`;
        }

        /**
         * 時間差を時間と分で計算する
         * @param {string} startTimeStr - 'HH:MM' 形式の開始時間
         * @param {string} endTimeStr - 'HH:MM' 形式の終了時間
         * @returns {number} 時間差（分単位）
         */
        function calculateMinutesDifference(startTimeStr, endTimeStr) {
            if (!startTimeStr || !endTimeStr) return 0;
            const [startH, startM] = startTimeStr.split(':').map(Number);
            const [endH, endM] = endTimeStr.split(':').map(Number);

            let startMinutes = startH * 60 + startM;
            let endMinutes = endH * 60 + endM;

            // Handle overnight shifts (end time is on the next day)
            if (endMinutes < startMinutes) {
                endMinutes += 24 * 60;
            }
            return endMinutes - startMinutes;
        }

        /**
         * 分を 'H時間M分' 形式でフォーマットする
         * @param {number} totalMinutes - 合計分数
         * @returns {string} フォーマットされた時間文字列
         */
        function formatMinutesToHoursMinutes(totalMinutes) {
            if (totalMinutes < 0) return `-${formatMinutesToHoursMinutes(Math.abs(totalMinutes))}`;
            const hours = Math.floor(totalMinutes / 60);
            const minutes = totalMinutes % 60;
            return `${hours}時間${minutes}分`;
        }

        /**
         * ユニークなIDを生成する
         * @returns {string} ユニークなID
         */
        function generateUniqueId() {
            return Date.now().toString(36) + Math.random().toString(36).substring(2);
        }

        /**
         * 初期データを生成する
         */
        function generateInitialData() {
            appState.data.clients = [];
            appState.data.sites = [];
            appState.data.guards = [];
            appState.data.workTypes = [];
            appState.data.assignments = [];
            appState.data.notifications = [
                { id: generateUniqueId(), date: '2025-07-15', title: 'システムメンテナンスのお知らせ', content: '7月20日 0:00-2:00 にシステムメンテナンスを実施します。' },
                { id: generateUniqueId(), date: '2025-07-10', title: '新機能追加のお知らせ', content: '実績入力画面に自動計算機能が追加されました。' },
            ];

            // 得意先マスタ (30件)
            const clientNames = [
                "株式会社セキュアジャパン", "日本警備保障株式会社", "東都セキュリティサービス", "中央安全警備", "西日本ガードシステム",
                "北日本警備隊", "南国セキュリティ", "大和警備保障", "富士安全警備", "桜セキュリティ",
                "旭日警備", "平和ガード", "未来セキュリティ", "希望警備", "安心ガード",
                "日本総合警備", "東京セキュリティ", "大阪警備保障", "名古屋安全警備", "福岡ガードシステム",
                "北海道警備隊", "沖縄セキュリティ", "横浜警備保障", "京都安全警備", "神戸ガードシステム",
                "広島警備", "仙台セキュリティ", "札幌警備保障", "千葉安全警備", "埼玉ガードシステム"
            ];
            let clientNoCounter = 1;
            for (let i = 0; i < clientNames.length; i++) {
                const client = {
                    id: generateUniqueId(),
                    clientNo: String(clientNoCounter++).padStart(3, '0'), // 001, 002...
                    name: clientNames[i],
                    address: `東京都新宿区西新宿${i + 1}-1-1`,
                    phone: `03-${String(1234 + i).padStart(4, '0')}-${String(5678 + i).padStart(4, '0')}`,
                    prefecture: prefectures[i % prefectures.length] // Assign a prefecture
                };
                appState.data.clients.push(client);
            }

            // 配置先マスタ (各得意先に2件ずつ、計60件)
            let siteNoCounter = 1;
            appState.data.clients.forEach(client => {
                const siteType1 = Math.random() < 0.5 ? '交通誘導' : '施設警備';
                appState.data.sites.push({
                    id: generateUniqueId(),
                    siteNo: String(siteNoCounter++).padStart(4, '0'), // 0001, 0002...
                    clientId: client.id,
                    name: `${client.name} ${siteType1 === '交通誘導' ? '工事現場' : 'ビル警備'}`,
                    address: `${client.address} 付近`,
                    closingDay: Math.floor(Math.random() * 28) + 1, // 1-28日
                    dailyRateDay: siteType1 === '交通誘導' ? 15000 : 18000,
                    dailyRateNight: siteType1 === '交通誘導' ? 18000 : 22000,
                    overtimeRateDay: siteType1 === '交通誘導' ? 2000 : 2500,
                    overtimeRateNight: siteType1 === '交通誘導' ? 2500 : 3000,
                    monthlyRate: Math.random() < 0.3 ? 300000 + Math.floor(Math.random() * 10) * 10000 : null,
                    type: siteType1,
                    prefecture: client.prefecture // Inherit prefecture from client
                });
                const siteType2 = siteType1 === '交通誘導' ? '施設警備' : '交通誘導'; // Alternate type for second site
                appState.data.sites.push({
                    id: generateUniqueId(),
                    siteNo: String(siteNoCounter++).padStart(4, '0'),
                    clientId: client.id,
                    name: `${client.name} ${siteType2 === '交通誘導' ? 'イベント会場' : '商業施設警備'}`,
                    address: `${client.address} 別館`,
                    closingDay: Math.floor(Math.random() * 28) + 1,
                    dailyRateDay: siteType2 === '交通誘導' ? 16000 : 19000,
                    dailyRateNight: siteType2 === '交通誘導' ? 19000 : 23000,
                    overtimeRateDay: siteType2 === '交通誘導' ? 2200 : 2700,
                    overtimeRateNight: siteType2 === '交通誘導' ? 2700 : 3200,
                    monthlyRate: Math.random() < 0.3 ? 320000 + Math.floor(Math.random() * 10) * 10000 : null,
                    type: siteType2,
                    prefecture: client.prefecture // Inherit prefecture from client
                });
            });


            // 隊員マスタ (50名)
            let guardNoCounter = 1;
            const lastNames = ["田中", "佐藤", "鈴木", "高橋", "渡辺", "伊藤", "中村", "小林", "加藤", "吉田"];
            const firstNames = ["太郎", "花子", "一郎", "美咲", "健太", "由美", "大輔", "彩", "拓海", "陽菜"];
            const furiganaLastNames = ["タナカ", "サトウ", "スズキ", "タカハシ", "ワタナベ", "イトウ", "ナカムラ", "コバヤシ", "カトウ", "ヨシダ"];
            const furiganaFirstNames = ["タロウ", "ハナコ", "イチロウ", "ミサキ", "ケンタ", "ユミ", "ダイスケ", "アヤ", "タクミ", "ハルナ"];

            for (let i = 0; i < 50; i++) {
                const birthYear = 1970 + Math.floor(Math.random() * 30); // 1970-1999
                const birthMonth = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
                const birthDay = String(Math.floor(Math.random() * 28) + 1).padStart(2, '0');
                const lastNameIndex = i % lastNames.length;
                const firstNameIndex = i % firstNames.length;

                appState.data.guards.push({
                    id: generateUniqueId(),
                    guardNo: String(guardNoCounter++).padStart(4, '0'), // 0001, 0002...
                    name: `${lastNames[lastNameIndex]}${firstNames[firstNameIndex]}`,
                    furigana: `${furiganaLastNames[lastNameIndex]}${furiganaFirstNames[firstNameIndex]}`, // Add furigana
                    dob: `${birthYear}-${birthMonth}-${birthDay}`,
                    phone: `090-${String(1111 + i).padStart(4, '0')}-${String(2222 + i).padStart(4, '0')}`,
                    address: `神奈川県横浜市港北区${i + 1}番地`,
                    qualification: qualifications[Math.floor(Math.random() * qualifications.length)],
                    employmentType: employmentTypes[Math.floor(Math.random() * employmentTypes.length)],
                    dailyWage: 12000 + Math.floor(Math.random() * 5) * 1000, // 日当
                    overtimeWagePerHour: 1500 + Math.floor(Math.random() * 5) * 100, // 残業時給
                    monthlySalary: Math.random() < 0.2 ? 250000 + Math.floor(Math.random() * 5) * 10000 : null // 月給
                });
            }

            // 勤務マスタ
            let workTypeNoCounter = 1;
            appState.data.workTypes.push({ id: generateUniqueId(), workTypeNo: String(workTypeNoCounter++).padStart(2, '0'), name: '日勤', defaultStartTime: '09:00', defaultEndTime: '18:00', breakMinutes: 60 });
            appState.data.workTypes.push({ id: generateUniqueId(), workTypeNo: String(workTypeNoCounter++).padStart(2, '0'), name: '夜勤', defaultStartTime: '22:00', defaultEndTime: '07:00', breakMinutes: 60 });
            appState.data.workTypes.push({ id: generateUniqueId(), workTypeNo: String(workTypeNoCounter++).padStart(2, '0'), name: '半日勤', defaultStartTime: '09:00', defaultEndTime: '13:00', breakMinutes: 0 });
            appState.data.workTypes.push({ id: generateUniqueId(), workTypeNo: String(workTypeNoCounter++).padStart(2, '0'), name: '半夜勤', defaultStartTime: '18:00', defaultEndTime: '22:00', breakMinutes: 0 });

            // ダミーの配置データ (直近1週間分)
            const today = new Date();
            for (let i = 0; i < 7; i++) {
                const date = new Date(today);
                date.setDate(today.getDate() - i); // Past 7 days
                const formattedDate = formatDate(date);

                if (appState.data.clients.length > 0 && appState.data.sites.length > 0 && appState.data.guards.length > 0 && appState.data.workTypes.length > 0) {
                    const randomClient = appState.data.clients[Math.floor(Math.random() * appState.data.clients.length)];
                    const sitesForClient = appState.data.sites.filter(s => s.clientId === randomClient.id);
                    if (sitesForClient.length > 0) {
                        const randomSite = sitesForClient[Math.floor(Math.random() * sitesForClient.length)];
                        const randomWorkType = appState.data.workTypes[Math.floor(Math.random() * appState.data.workTypes.length)];
                        const requiredGuards = Math.floor(Math.random() * 3) + 1; // 1-3 guards

                        const assignedGuards = [];
                        for (let j = 0; j < requiredGuards; j++) {
                            const randomGuard = appState.data.guards[Math.floor(Math.random() * appState.data.guards.length)];
                            assignedGuards.push({
                                guardId: randomGuard.id,
                                actualStartTime: null, // Initially null for performance input
                                actualEndTime: null
                            });
                        }

                        appState.data.assignments.push({
                            id: generateUniqueId(),
                            date: formattedDate,
                            clientId: randomClient.id,
                            siteId: randomSite.id,
                            workTypeId: randomWorkType.id,
                            requiredGuards: requiredGuards,
                            assignedGuards: assignedGuards
                        });
                    }
                }
            }
            saveData(); // Save initial data
        }

        /**
         * 日付文字列から年と月を取得する (YYYY-MM)
         * @param {string} dateString - 'YYYY-MM-DD' 形式の日付文字列
         * @returns {string} 'YYYY-MM' 形式の年月文字列
         */
        function getYearMonth(dateString) {
            return dateString.substring(0, 7);
        }

        /**
         * 指定された年月の最終日を取得する
         * @param {number} year
         * @param {number} month (1-12)
         * @returns {number} 最終日
         */
        function getLastDayOfMonth(year, month) {
            return new Date(year, month, 0).getDate();
        }

        // --- View Rendering Functions ---
        const contentArea = document.getElementById('content-area');

        /**
         * 指定されたビューをレンダリングする
         * @param {string} viewName - レンダリングするビューの名前
         */
        function renderView(viewName) {
            console.log(`Attempting to render view: ${viewName}`); // Added logging
            contentArea.innerHTML = ''; // Clear current content
            contentArea.classList.add('view-transition'); // Add transition class
            setTimeout(() => contentArea.classList.remove('view-transition'), 300); // Remove after animation

            appState.currentView = viewName;
            updateSidebarActiveState(viewName);

            try { // Added try-catch for rendering logic
                switch (viewName) {
                    case 'dashboard':
                        renderDashboard();
                        break;
                    case 'master-management':
                        renderMasterManagement();
                        break;
                    case 'assignment-daily':
                        renderAssignmentDaily();
                        break;
                    case 'assignment-weekly':
                        renderAssignmentWeekly();
                        break;
                    case 'performance-input':
                        renderPerformanceInput();
                        break;
                    case 'guard-ledger': // New view for guard ledger
                        renderGuardWorkLedger();
                        break;
                    case 'backup':
                        renderBackup();
                        break;
                    default:
                        contentArea.innerHTML = '<h2 class="text-2xl font-bold text-gray-800">ページが見つかりません</h2><p class="mt-2 text-gray-600">指定されたページは存在しません。</p>';
                }
            } catch (error) {
                console.error(`Error rendering view "${viewName}":`, error);
                contentArea.innerHTML = `<h2 class="text-2xl font-bold text-red-600">表示エラー</h2><p class="mt-2 text-red-500">このページの表示中にエラーが発生しました。詳細: ${error.message}</p>`;
            }
            window.scrollTo(0, 0); // Scroll to top on view change
        }

        /**
         * サイドバーのアクティブ状態を更新する
         * @param {string} activeView - 現在アクティブなビューの名前
         */
        function updateSidebarActiveState(activeView) {
            document.querySelectorAll('.sidebar-item').forEach(item => {
                if (item.dataset.view === activeView) {
                    item.classList.add('active');
                } else {
                    item.classList.remove('active');
                }
            });
        }

        /**
         * ダッシュボードビューをレンダリングする
         */
        function renderDashboard() {
            const today = new Date();
            const startOfWeek = new Date(today);
            startOfWeek.setDate(today.getDate() - today.getDay()); // Sunday
            const endOfWeek = new Date(startOfWeek);
            endOfWeek.setDate(startOfWeek.getDate() + 6); // Saturday

            const assignmentsThisWeek = appState.data.assignments.filter(assignment => {
                const assignDate = new Date(assignment.date);
                return assignDate >= startOfWeek && assignDate <= endOfWeek;
            });

            const totalPlannedAssignmentsThisWeek = assignmentsThisWeek.length;
            const totalPlannedGuardsThisWeek = assignmentsThisWeek.reduce((sum, assign) => sum + assign.requiredGuards, 0);

            let totalOvertimeMinutesLast7Days = 0;
            const sevenDaysAgo = new Date(today);
            sevenDaysAgo.setDate(today.getDate() - 7);

            appState.data.assignments.forEach(assignment => {
                const assignDate = new Date(assignment.date);
                if (assignDate >= sevenDaysAgo && assignDate <= today) {
                    assignment.assignedGuards.forEach(assignedGuard => {
                        if (assignedGuard.actualStartTime && assignedGuard.actualEndTime) {
                            const workType = appState.data.workTypes.find(wt => wt.id === assignment.workTypeId);
                            if (workType) {
                                const plannedMinutes = calculateMinutesDifference(workType.defaultStartTime, workType.defaultEndTime);
                                const actualMinutes = calculateMinutesDifference(assignedGuard.actualStartTime, assignedGuard.actualEndTime);
                                const overtime = actualMinutes - plannedMinutes - workType.breakMinutes;
                                if (overtime > 0) {
                                    totalOvertimeMinutesLast7Days += overtime;
                                }
                            }
                        }
                    });
                }
            });

            const unassignedCount = appState.data.assignments.filter(a => a.assignedGuards.some(g => !g.guardId)).length;
            const scheduledCount = appState.data.assignments.length;

            let clientOrderCounts = {};
            appState.data.assignments.forEach(assign => {
                const client = appState.data.clients.find(c => c.id === assign.clientId);
                if (client) {
                    clientOrderCounts[client.name] = (clientOrderCounts[client.name] || 0) + 1;
                }
            });

            // Sort clients by order count for display
            const sortedClientOrders = Object.entries(clientOrderCounts).sort(([, a], [, b]) => b - a);

            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">ダッシュボード</h2>

                <!-- Alerts & Summary Cards -->
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
                    <div class="card bg-blue-50 text-blue-800 border border-blue-200">
                        <div class="flex items-center mb-2">
                            <i class="fas fa-exclamation-triangle text-2xl mr-3"></i>
                            <h3 class="text-xl font-semibold">現場アラート</h3>
                        </div>
                        <p class="text-4xl font-bold">${unassignedCount}</p>
                        <p class="text-sm mt-1">未配置件数</p>
                    </div>
                    <div class="card bg-green-50 text-green-800 border border-green-200">
                        <div class="flex items-center mb-2">
                            <i class="fas fa-calendar-alt text-2xl mr-3"></i>
                            <h3 class="text-xl font-semibold">予定件数</h3>
                        </div>
                        <p class="text-4xl font-bold">${scheduledCount}</p>
                        <p class="text-sm mt-1">全現場の予定件数</p>
                    </div>
                    <div class="card bg-purple-50 text-purple-800 border border-purple-200">
                        <div class="flex items-center mb-2">
                            <i class="fas fa-users text-2xl mr-3"></i>
                            <h3 class="text-xl font-semibold">今週の予定</h3>
                        </div>
                        <p class="text-2xl font-bold mb-1">配置予定数: ${totalPlannedAssignmentsThisWeek} 件</p>
                        <p class="text-2xl font-bold mb-1">隊員出勤予定: ${totalPlannedGuardsThisWeek} 人</p>
                        <p class="text-sm mt-1 text-gray-600">(${formatDate(startOfWeek)} 〜 ${formatDate(endOfWeek)})</p>
                    </div>
                    <div class="card bg-yellow-50 text-yellow-800 border border-yellow-200 col-span-full md:col-span-1">
                        <div class="flex items-center mb-2">
                            <i class="fas fa-hourglass-half text-2xl mr-3"></i>
                            <h3 class="text-xl font-semibold">直近7日の残業予定</h3>
                        </div>
                        <p class="text-4xl font-bold">${formatMinutesToHoursMinutes(totalOvertimeMinutesLast7Days)}</p>
                        <p class="text-sm mt-1">合計残業時間</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                    <!-- 受注状況 -->
                    <div class="card">
                        <h3 class="text-xl font-semibold text-gray-800 mb-4 flex items-center">
                            <i class="fas fa-chart-bar mr-2 text-blue-500"></i> 受注状況 (配置先別)
                        </h3>
                        <div class="space-y-3">
                            ${sortedClientOrders.map(([clientName, count]) => `
                                <div class="flex justify-between items-center">
                                    <span class="text-gray-700">${clientName}</span>
                                    <span class="font-semibold text-gray-900">${count} 件</span>
                                </div>
                                <div class="w-full bg-gray-200 rounded-full h-2.5">
                                    <div class="graph-bar" style="width: ${Math.min(100, (count / Math.max(...Object.values(clientOrderCounts))) * 100)}%;"></div>
                                </div>
                            `).join('')}
                        </div>
                    </div>

                    <!-- 運営からのお知らせ -->
                    <div class="card">
                        <h3 class="text-xl font-semibold text-gray-800 mb-4 flex items-center">
                            <i class="fas fa-bullhorn mr-2 text-orange-500"></i> 運営からのお知らせ
                        </h3>
                        <div class="space-y-4">
                            ${appState.data.notifications.length > 0 ?
                                appState.data.notifications.map(note => `
                                    <div class="border-b border-gray-200 pb-3 last:border-b-0">
                                        <p class="text-sm text-gray-500">${formatJapaneseDate(note.date)}</p>
                                        <h4 class="font-semibold text-gray-800">${note.title}</h4>
                                        <p class="text-sm text-gray-600">${note.content}</p>
                                    </div>
                                `).join('')
                                : '<p class="text-gray-600">現在、新しいお知らせはありません。</p>'
                            }
                        </div>
                    </div>
                </div>
                <!-- Mascot Animation Container -->
                <div class="mascot-container">
                    <div class="mascot-animation">👷</div> <!-- Security guard emoji -->
                </div>
            `;
        }

        /**
         * マスタ管理ビューをレンダリングする
         */
        function renderMasterManagement() {
            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">マスタ管理</h2>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                    <button class="card btn btn-secondary flex flex-col items-center justify-center p-6" onclick="showMasterList('clients')">
                        <i class="fas fa-building text-4xl mb-3 text-blue-600"></i>
                        <span class="text-xl font-semibold">得意先マスタ</span>
                    </button>
                    <button class="card btn btn-secondary flex flex-col items-center justify-center p-6" onclick="showMasterList('sites')">
                        <i class="fas fa-map-marker-alt text-4xl mb-3 text-green-600"></i>
                        <span class="text-xl font-semibold">配置先マスタ</span>
                    </button>
                    <button class="card btn btn-secondary flex flex-col items-center justify-center p-6" onclick="showMasterList('guards')">
                        <i class="fas fa-users text-4xl mb-3 text-purple-600"></i>
                        <span class="text-xl font-semibold">隊員マスタ</span>
                    </button>
                    <button class="card btn btn-secondary flex flex-col items-center justify-center p-6" onclick="showMasterList('workTypes')">
                        <i class="fas fa-clock text-4xl mb-3 text-orange-600"></i>
                        <span class="text-xl font-semibold">勤務マスタ</span>
                    </button>
                </div>
                <div id="master-list-container" class="card p-6">
                    <p class="text-gray-600">上記から管理したいマスタを選択してください。</p>
                </div>
            `;
        }

        /**
         * 選択されたマスタの一覧を表示する
         * @param {string} masterType - 管理するマスタのタイプ ('clients', 'sites', 'guards', 'workTypes')
         */
        function showMasterList(masterType) {
            appState.editingMaster = masterType;
            appState.editingRecord = null; // Clear any editing record

            const container = document.getElementById('master-list-container');
            let title = '';
            let headers = [];
            let data = appState.data[masterType];
            let searchFieldsHtml = '';
            let filterFunction = (record) => true; // Default no filter

            switch (masterType) {
                case 'clients':
                    title = '得意先マスタ';
                    headers = ['No.', '得意先名', '住所', '電話番号', '都道府県', '操作'];
                    searchFieldsHtml = `
                        <input type="text" id="clientSearchName" placeholder="得意先名で検索" class="${inputClass} w-full md:w-1/3 mr-2 mb-2 md:mb-0">
                        <input type="text" id="clientSearchNo" placeholder="No.で検索" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                        <select id="clientSearchPrefecture" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                            <option value="">都道府県で検索</option>
                            ${prefectures.map(p => `<option value="${p}">${p}</option>`).join('')}
                        </select>
                    `;
                    filterFunction = (record) => {
                        const name = document.getElementById('clientSearchName')?.value.toLowerCase() || '';
                        const no = document.getElementById('clientSearchNo')?.value || '';
                        const prefecture = document.getElementById('clientSearchPrefecture')?.value || '';
                        return (record.name.toLowerCase().includes(name) || name === '') &&
                               (record.clientNo.includes(no) || no === '') &&
                               (record.prefecture === prefecture || prefecture === '');
                    };
                    break;
                case 'sites':
                    title = '配置先マスタ';
                    headers = ['No.', '得意先名', '配置先名', '現場住所', '締め日', '種別', '都道府県', '操作'];
                    searchFieldsHtml = `
                        <input type="text" id="siteSearchName" placeholder="配置先名で検索" class="${inputClass} w-full md:w-1/3 mr-2 mb-2 md:mb-0">
                        <input type="text" id="siteSearchNo" placeholder="No.で検索" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                        <select id="siteSearchPrefecture" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                            <option value="">都道府県で検索</option>
                            ${prefectures.map(p => `<option value="${p}">${p}</option>`).join('')}
                        </select>
                    `;
                    filterFunction = (record) => {
                        const name = document.getElementById('siteSearchName')?.value.toLowerCase() || '';
                        const no = document.getElementById('siteSearchNo')?.value || '';
                        const prefecture = document.getElementById('siteSearchPrefecture')?.value || '';
                        return (record.name.toLowerCase().includes(name) || name === '') &&
                               (record.siteNo.includes(no) || no === '') &&
                               (record.prefecture === prefecture || prefecture === '');
                    };
                    break;
                case 'guards':
                    title = '隊員マスタ';
                    headers = ['No.', '氏名', 'フリガナ', '生年月日', '電話番号', '資格', '雇用形態', '操作'];
                    searchFieldsHtml = `
                        <input type="text" id="guardSearchName" placeholder="氏名で検索" class="${inputClass} w-full md:w-1/3 mr-2 mb-2 md:mb-0">
                        <input type="text" id="guardSearchFurigana" placeholder="フリガナで検索" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                        <input type="text" id="guardSearchNo" placeholder="No.で検索" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                    `;
                    filterFunction = (record) => {
                        const name = document.getElementById('guardSearchName')?.value.toLowerCase() || '';
                        const furigana = document.getElementById('guardSearchFurigana')?.value.toLowerCase() || '';
                        const no = document.getElementById('guardSearchNo')?.value || '';
                        return (record.name.toLowerCase().includes(name) || name === '') &&
                               (record.furigana.toLowerCase().includes(furigana) || furigana === '') &&
                               (record.guardNo.includes(no) || no === '');
                    };
                    break;
                case 'workTypes':
                    title = '勤務マスタ';
                    headers = ['No.', '勤務名', '開始時間', '終了時間', '休憩時間', '操作'];
                     searchFieldsHtml = `
                        <input type="text" id="workTypeSearchName" placeholder="勤務名で検索" class="${inputClass} w-full md:w-1/3 mr-2 mb-2 md:mb-0">
                        <input type="text" id="workTypeSearchNo" placeholder="No.で検索" class="${inputClass} w-full md:w-1/4 mr-2 mb-2 md:mb-0">
                    `;
                    filterFunction = (record) => {
                        const name = document.getElementById('workTypeSearchName')?.value.toLowerCase() || '';
                        const no = document.getElementById('workTypeSearchNo')?.value || '';
                        return (record.name.toLowerCase().includes(name) || name === '') &&
                               (record.workTypeNo.includes(no) || no === '');
                    };
                    break;
            }

            // Apply filter
            const filteredData = data.filter(filterFunction);

            let tableRowsHtml = '';
            if (filteredData.length === 0) {
                tableRowsHtml = `<tr><td colspan="${headers.length}" class="text-center text-gray-500 py-4">データがありません。</td></tr>`;
            } else {
                tableRowsHtml = filteredData.map(record => {
                    let cells = '';
                    switch (masterType) {
                        case 'clients':
                            cells = `
                                <td>${record.clientNo}</td>
                                <td>${record.name}</td>
                                <td>${record.address}</td>
                                <td>${record.phone}</td>
                                <td>${record.prefecture}</td>
                            `;
                            break;
                        case 'sites':
                            const clientName = appState.data.clients.find(c => c.id === record.clientId)?.name || '不明';
                            cells = `
                                <td>${record.siteNo}</td>
                                <td>${clientName}</td>
                                <td>${record.name}</td>
                                <td>${record.address}</td>
                                <td>${record.closingDay}日</td>
                                <td><span class="badge ${record.type === '交通誘導' ? 'badge-green' : 'badge-orange'}">${record.type}</span></td>
                                <td>${record.prefecture}</td>
                            `;
                            break;
                        case 'guards':
                            cells = `
                                <td>${record.guardNo}</td>
                                <td>${record.name}</td>
                                <td>${record.furigana}</td>
                                <td>${record.dob}</td>
                                <td>${record.phone}</td>
                                <td>${record.qualification}</td>
                                <td>${record.employmentType}</td>
                            `;
                            break;
                        case 'workTypes':
                            cells = `
                                <td>${record.workTypeNo}</td>
                                <td>${record.name}</td>
                                <td>${record.defaultStartTime}</td>
                                <td>${record.defaultEndTime}</td>
                                <td>${record.breakMinutes}分</td>
                            `;
                            break;
                    }
                    return `
                        <tr>
                            ${cells}
                            <td class="whitespace-nowrap">
                                <button class="text-blue-600 hover:text-blue-800 mr-3" onclick="editMasterRecord('${masterType}', '${record.id}')">
                                    <i class="fas fa-edit"></i> 編集
                                </button>
                                <button class="text-red-600 hover:text-red-800" onclick="deleteMasterRecord('${masterType}', '${record.id}')">
                                    <i class="fas fa-trash-alt"></i> 削除
                                </button>
                            </td>
                        </tr>
                    `;
                }).join('');
            }

            container.innerHTML = `
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-2xl font-semibold text-gray-800">${title} 一覧</h3>
                    <button class="btn btn-primary" onclick="addMasterRecord('${masterType}')">
                        <i class="fas fa-plus mr-2"></i> 新規追加
                    </button>
                </div>
                <div class="mb-4 flex flex-wrap items-center">
                    ${searchFieldsHtml}
                    <button class="btn btn-secondary mt-2 md:mt-0" onclick="showMasterList('${masterType}')">
                        <i class="fas fa-filter mr-2"></i> 検索/絞り込み
                    </button>
                </div>
                <div class="overflow-x-auto rounded-lg border border-gray-200">
                    <table class="table-auto min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                ${headers.map(header => `<th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">${header}</th>`).join('')}
                            </tr>
                        </thead>
                        <tbody class="bg-white divide-y divide-gray-200">
                            ${tableRowsHtml}
                        </tbody>
                    </table>
                </div>
            `;
        }

        /**
         * マスタレコードの追加フォームを表示する
         * @param {string} masterType - 管理するマスタのタイプ
         */
        function addMasterRecord(masterType) {
            appState.editingRecord = null; // Clear for new record
            showMasterForm(masterType);
        }

        /**
         * マスタレコードの編集フォームを表示する
         * @param {string} masterType - 管理するマスタのタイプ
         * @param {string} recordId - 編集するレコードのID
         */
        function editMasterRecord(masterType, recordId) {
            appState.editingRecord = appState.data[masterType].find(r => r.id === recordId);
            showMasterForm(masterType);
        }

        /**
         * マスタレコードのフォームを表示する (新規/編集共通)
         * @param {string} masterType - 管理するマスタのタイプ
         */
        function showMasterForm(masterType) {
            let title = '';
            let formHtml = '';
            let record = appState.editingRecord || {}; // Use empty object for new record

            switch (masterType) {
                case 'clients':
                    title = appState.editingRecord ? '得意先編集' : '得意先新規追加';
                    const clientPrefectureOptions = prefectures.map(p => `<option value="${p}" ${record.prefecture === p ? 'selected' : ''}>${p}</option>`).join('');
                    formHtml = `
                        <div class="mb-4">
                            <label for="clientName" class="${labelClass}">得意先名</label>
                            <input type="text" id="clientName" value="${record.name || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="clientAddress" class="${labelClass}">住所</label>
                            <input type="text" id="clientAddress" value="${record.address || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="clientPhone" class="${labelClass}">電話番号</label>
                            <input type="text" id="clientPhone" value="${record.phone || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="clientPrefecture" class="${labelClass}">都道府県</label>
                            <select id="clientPrefecture" class="${inputClass}" required>
                                <option value="">選択してください</option>
                                ${clientPrefectureOptions}
                            </select>
                        </div>
                    `;
                    break;
                case 'sites':
                    title = appState.editingRecord ? '配置先編集' : '配置先新規追加';
                    const clientOptions = appState.data.clients.map(c => `<option value="${c.id}" ${record.clientId === c.id ? 'selected' : ''}>${c.name}</option>`).join('');
                    const siteTypes = ['交通誘導', '施設警備'];
                    const siteTypeOptions = siteTypes.map(type => `<option value="${type}" ${record.type === type ? 'selected' : ''}>${type}</option>`).join('');
                    const sitePrefectureOptions = prefectures.map(p => `<option value="${p}" ${record.prefecture === p ? 'selected' : ''}>${p}</option>`).join('');

                    formHtml = `
                        <div class="mb-4">
                            <label for="siteClient" class="${labelClass}">得意先</label>
                            <select id="siteClient" class="${inputClass}" required>
                                <option value="">選択してください</option>
                                ${clientOptions}
                            </select>
                        </div>
                        <div class="mb-4">
                            <label for="siteName" class="${labelClass}">配置先名 (現場名)</label>
                            <input type="text" id="siteName" value="${record.name || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="siteAddress" class="${labelClass}">現場住所</label>
                            <input type="text" id="siteAddress" value="${record.address || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="siteClosingDay" class="${labelClass}">締め日</label>
                            <input type="number" id="siteClosingDay" value="${record.closingDay || ''}" min="1" max="31" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="siteType" class="${labelClass}">種別</label>
                            <select id="siteType" class="${inputClass}" required>
                                ${siteTypeOptions}
                            </select>
                        </div>
                        <div class="mb-4">
                            <label for="sitePrefecture" class="${labelClass}">都道府県</label>
                            <select id="sitePrefecture" class="${inputClass}" required>
                                <option value="">選択してください</option>
                                ${sitePrefectureOptions}
                            </select>
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="mb-4">
                                <label for="siteDailyRateDay" class="${labelClass}">請求単価 (日勤)</label>
                                <input type="number" id="siteDailyRateDay" value="${record.dailyRateDay || ''}" class="${inputClass}" required>
                            </div>
                            <div class="mb-4">
                                <label for="siteDailyRateNight" class="${labelClass}">請求単価 (夜勤)</label>
                                <input type="number" id="siteDailyRateNight" value="${record.dailyRateNight || ''}" class="${inputClass}" required>
                            </div>
                            <div class="mb-4">
                                <label for="siteOvertimeRateDay" class="${labelClass}">残業請求単価 (日勤)</label>
                                <input type="number" id="siteOvertimeRateDay" value="${record.overtimeRateDay || ''}" class="${inputClass}" required>
                            </div>
                            <div class="mb-4">
                                <label for="siteOvertimeRateNight" class="${labelClass}">残業請求単価 (夜勤)</label>
                                <input type="number" id="siteOvertimeRateNight" value="${record.overtimeRateNight || ''}" class="${inputClass}" required>
                            </div>
                        </div>
                        <div class="mb-4">
                            <label for="siteMonthlyRate" class="${labelClass}">月極請求単価 (任意)</label>
                            <input type="number" id="siteMonthlyRate" value="${record.monthlyRate || ''}" class="${inputClass}">
                        </div>
                    `;
                    break;
                case 'guards':
                    title = appState.editingRecord ? '隊員編集' : '隊員新規追加';
                    const qualOptions = qualifications.map(q => `<option value="${q}" ${record.qualification === q ? 'selected' : ''}>${q}</option>`).join('');
                    const empOptions = employmentTypes.map(e => `<option value="${e}" ${record.employmentType === e ? 'selected' : ''}>${e}</option>`).join('');
                    formHtml = `
                        <div class="mb-4">
                            <label for="guardName" class="${labelClass}">氏名</label>
                            <input type="text" id="guardName" value="${record.name || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="guardFurigana" class="${labelClass}">フリガナ</label>
                            <input type="text" id="guardFurigana" value="${record.furigana || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="guardDob" class="${labelClass}">生年月日</label>
                            <input type="date" id="guardDob" value="${record.dob || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="guardPhone" class="${labelClass}">電話番号</label>
                            <input type="text" id="guardPhone" value="${record.phone || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="guardAddress" class="${labelClass}">住所</label>
                            <input type="text" id="guardAddress" value="${record.address || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="guardQualification" class="${labelClass}">保有資格</label>
                            <select id="guardQualification" class="${inputClass}" required>
                                ${qualOptions}
                            </select>
                        </div>
                        <div class="mb-4">
                            <label for="guardEmploymentType" class="${labelClass}">雇用形態</label>
                            <select id="guardEmploymentType" class="${inputClass}" required>
                                ${empOptions}
                            </select>
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="mb-4">
                                <label for="guardDailyWage" class="${labelClass}">給与単価 (日当)</label>
                                <input type="number" id="guardDailyWage" value="${record.dailyWage || ''}" class="${inputClass}" required>
                            </div>
                            <div class="mb-4">
                                <label for="guardOvertimeWagePerHour" class="${labelClass}">残業単価 (時給)</label>
                                <input type="number" id="guardOvertimeWagePerHour" value="${record.overtimeWagePerHour || ''}" class="${inputClass}" required>
                            </div>
                        </div>
                        <div class="mb-4">
                            <label for="guardMonthlySalary" class="${labelClass}">月給 (任意)</label>
                            <input type="number" id="guardMonthlySalary" value="${record.monthlySalary || ''}" class="${inputClass}">
                        </div>
                    `;
                    break;
                case 'workTypes':
                    title = appState.editingRecord ? '勤務編集' : '勤務新規追加';
                    formHtml = `
                        <div class="mb-4">
                            <label for="workTypeName" class="${labelClass}">勤務名</label>
                            <input type="text" id="workTypeName" value="${record.name || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="workTypeDefaultStartTime" class="${labelClass}">所定開始時間</label>
                            <input type="time" id="workTypeDefaultStartTime" value="${record.defaultStartTime || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="workTypeDefaultEndTime" class="${labelClass}">所定終了時間</label>
                            <input type="time" id="workTypeDefaultEndTime" value="${record.defaultEndTime || ''}" class="${inputClass}" required>
                        </div>
                        <div class="mb-4">
                            <label for="workTypeBreakMinutes" class="${labelClass}">所定休憩時間 (分)</label>
                            <input type="number" id="workTypeBreakMinutes" value="${record.breakMinutes || ''}" class="${inputClass}" required>
                        </div>
                    `;
                    break;
            }

            showModal(title, formHtml, [
                { text: '保存', className: 'btn-primary', onClick: () => saveMasterRecord(masterType) },
                { text: 'キャンセル', className: 'btn-secondary', onClick: () => showMasterList(masterType) }
            ]);
        }

        /**
         * マスタレコードを保存する (新規/編集共通)
         * @param {string} masterType - 管理するマスタのタイプ
         */
        function saveMasterRecord(masterType) {
            let newRecord = {};
            let isValid = true;

            switch (masterType) {
                case 'clients':
                    newRecord = {
                        name: document.getElementById('clientName').value,
                        address: document.getElementById('clientAddress').value,
                        phone: document.getElementById('clientPhone').value,
                        prefecture: document.getElementById('clientPrefecture').value
                    };
                    if (!newRecord.name || !newRecord.address || !newRecord.phone || !newRecord.prefecture) {
                        isValid = false;
                        showModal('入力エラー', '得意先名、住所、電話番号、都道府県は必須です。');
                    }
                    break;
                case 'sites':
                    newRecord = {
                        clientId: document.getElementById('siteClient').value,
                        name: document.getElementById('siteName').value,
                        address: document.getElementById('siteAddress').value,
                        closingDay: parseInt(document.getElementById('siteClosingDay').value),
                        dailyRateDay: parseFloat(document.getElementById('siteDailyRateDay').value),
                        dailyRateNight: parseFloat(document.getElementById('siteDailyRateNight').value),
                        overtimeRateDay: parseFloat(document.getElementById('siteOvertimeRateDay').value),
                        overtimeRateNight: parseFloat(document.getElementById('siteOvertimeRateNight').value),
                        monthlyRate: parseFloat(document.getElementById('siteMonthlyRate').value) || null,
                        type: document.getElementById('siteType').value,
                        prefecture: document.getElementById('sitePrefecture').value
                    };
                    if (!newRecord.clientId || !newRecord.name || !newRecord.address || isNaN(newRecord.closingDay) || isNaN(newRecord.dailyRateDay) || isNaN(newRecord.dailyRateNight) || isNaN(newRecord.overtimeRateDay) || isNaN(newRecord.overtimeRateNight) || !newRecord.type || !newRecord.prefecture) {
                        isValid = false;
                        showModal('入力エラー', 'すべての必須項目を入力してください。');
                    }
                    break;
                case 'guards':
                    newRecord = {
                        name: document.getElementById('guardName').value,
                        furigana: document.getElementById('guardFurigana').value,
                        dob: document.getElementById('guardDob').value,
                        phone: document.getElementById('guardPhone').value,
                        address: document.getElementById('guardAddress').value,
                        qualification: document.getElementById('guardQualification').value,
                        employmentType: document.getElementById('guardEmploymentType').value,
                        dailyWage: parseFloat(document.getElementById('guardDailyWage').value),
                        overtimeWagePerHour: parseFloat(document.getElementById('guardOvertimeWagePerHour').value),
                        monthlySalary: parseFloat(document.getElementById('guardMonthlySalary').value) || null,
                    };
                    if (!newRecord.name || !newRecord.furigana || !newRecord.dob || !newRecord.phone || !newRecord.address || !newRecord.qualification || !newRecord.employmentType || isNaN(newRecord.dailyWage) || isNaN(newRecord.overtimeWagePerHour)) {
                        isValid = false;
                        showModal('入力エラー', 'すべての必須項目を入力してください。');
                    }
                    break;
                case 'workTypes':
                    newRecord = {
                        name: document.getElementById('workTypeName').value,
                        defaultStartTime: document.getElementById('workTypeDefaultStartTime').value,
                        defaultEndTime: document.getElementById('workTypeDefaultEndTime').value,
                        breakMinutes: parseInt(document.getElementById('workTypeBreakMinutes').value),
                    };
                    if (!newRecord.name || !newRecord.defaultStartTime || !newRecord.defaultEndTime || isNaN(newRecord.breakMinutes)) {
                        isValid = false;
                        showModal('入力エラー', 'すべての必須項目を入力してください。');
                    }
                    break;
            }

            if (!isValid) return;

            if (appState.editingRecord) {
                // Update existing record
                const index = appState.data[masterType].findIndex(r => r.id === appState.editingRecord.id);
                if (index !== -1) {
                    appState.data[masterType][index] = { ...appState.editingRecord, ...newRecord };
                }
            } else {
                // Add new record with new number
                if (masterType === 'clients') newRecord.clientNo = String(appState.data.clients.length + 1).padStart(3, '0');
                if (masterType === 'sites') newRecord.siteNo = String(appState.data.sites.length + 1).padStart(4, '0');
                if (masterType === 'guards') newRecord.guardNo = String(appState.data.guards.length + 1).padStart(4, '0');
                if (masterType === 'workTypes') newRecord.workTypeNo = String(appState.data.workTypes.length + 1).padStart(2, '0');

                newRecord.id = generateUniqueId();
                appState.data[masterType].push(newRecord);
            }
            saveData();
            showMasterList(masterType); // Re-render list
            hideModal(); // Close modal
        }

        /**
         * マスタレコードを削除する
         * @param {string} masterType - 管理するマスタのタイプ
         * @param {string} recordId - 削除するレコードのID
         */
        function deleteMasterRecord(masterType, recordId) {
            showModal('確認', '本当にこのレコードを削除しますか？', [
                {
                    text: '削除',
                    className: 'btn-danger',
                    onClick: () => {
                        appState.data[masterType] = appState.data[masterType].filter(r => r.id !== recordId);
                        saveData();
                        showMasterList(masterType); // Re-render list
                    }
                },
                { text: 'キャンセル', className: 'btn-secondary' }
            ]);
        }

        // --- Assignment Management (Daily) ---
        let currentAssignmentDate = formatDate(new Date());

        /**
         * 配置管理 (日別) ビューをレンダリングする
         */
        function renderAssignmentDaily() {
            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">配置管理 (日別)</h2>
                <div class="card p-6 mb-6">
                    <div class="flex items-center space-x-4 mb-6">
                        <label for="assignmentDate" class="text-lg font-semibold text-gray-700">日付選択:</label>
                        <input type="date" id="assignmentDate" value="${currentAssignmentDate}" class="flex-grow max-w-xs ${inputClass}">
                        <button class="btn btn-primary" onclick="loadAssignmentsForDate()">
                            <i class="fas fa-search mr-2"></i> 検索
                        </button>
                    </div>
                    <div id="daily-assignment-list">
                        <!-- Assignments for the selected date will be rendered here -->
                    </div>
                    <button class="btn btn-primary mt-6" onclick="addDailyAssignment()">
                        <i class="fas fa-plus mr-2"></i> 新規配置追加
                    </button>
                </div>
            `;
            document.getElementById('assignmentDate').addEventListener('change', (e) => {
                currentAssignmentDate = e.target.value;
                loadAssignmentsForDate();
            });
            loadAssignmentsForDate(); // Initial load
        }

        /**
         * 選択された日付の配置一覧をロードして表示する
         */
        function loadAssignmentsForDate() {
            const assignments = appState.data.assignments.filter(a => a.date === currentAssignmentDate);
            const listContainer = document.getElementById('daily-assignment-list');

            if (assignments.length === 0) {
                listContainer.innerHTML = `<p class="text-gray-600">この日付の配置予定はありません。</p>`;
                return;
            }

            listContainer.innerHTML = assignments.map(assign => {
                const client = appState.data.clients.find(c => c.id === assign.clientId);
                const site = appState.data.sites.find(s => s.id === assign.siteId);
                const workType = appState.data.workTypes.find(wt => wt.id === assign.workTypeId);

                const assignedGuardsHtml = assign.assignedGuards.map(ag => {
                    const guard = appState.data.guards.find(g => g.id === ag.guardId);
                    return `<span class="badge badge-blue mr-2 mb-1">${guard ? guard.name : '未割当'}</span>`;
                }).join('');

                return `
                    <div class="border border-gray-200 rounded-lg p-4 mb-4 flex justify-between items-center shadow-sm">
                        <div>
                            <p class="text-sm text-gray-500">${client ? client.name : '不明な得意先'}</p>
                            <h4 class="text-lg font-semibold text-gray-800">${site ? site.name : '不明な配置先'}</h4>
                            <p class="text-md text-gray-700 mt-1">
                                勤務: ${workType ? workType.name : '不明'} (${workType ? workType.defaultStartTime + '-' + workType.defaultEndTime : ''})
                            </p>
                            <p class="text-sm text-gray-600">必要人数: ${assign.requiredGuards}名</p>
                            <div class="mt-2">
                                ${assignedGuardsHtml}
                            </div>
                        </div>
                        <div class="flex space-x-2">
                            <button class="btn btn-secondary btn-sm" onclick="editDailyAssignment('${assign.id}')">
                                <i class="fas fa-edit mr-1"></i> 編集
                            </button>
                            <button class="btn btn-danger btn-sm" onclick="deleteDailyAssignment('${assign.id}')">
                                <i class="fas fa-trash-alt mr-1"></i> 削除
                            </button>
                        </div>
                    </div>
                `;
            }).join('');
        }

        /**
         * 日別配置の追加/編集フォームを表示する
         * @param {string} [assignmentId] - 編集する場合の配置ID
         * @param {string} [prefillDate] - 新規追加時に日付を事前入力する場合
         */
        function addEditDailyAssignment(assignmentId = null, prefillDate = null) {
            const isEditing = assignmentId !== null;
            if (isEditing) {
                // 既存の割り当てをディープコピーしてモーダル用データとして設定
                appState.currentAssignmentInModal = JSON.parse(JSON.stringify(appState.data.assignments.find(a => a.id === assignmentId)));
            } else {
                // 新規割り当ての初期データを設定
                appState.currentAssignmentInModal = {
                    id: generateUniqueId(),
                    date: prefillDate || currentAssignmentDate, // 週別から来た場合はprefillDateを優先
                    clientId: '',
                    siteId: '',
                    workTypeId: '',
                    requiredGuards: 1,
                    assignedGuards: [{ guardId: '', actualStartTime: null, actualEndTime: null }]
                };
            }

            const assignment = appState.currentAssignmentInModal; // モーダル内の操作はこのオブジェクトに対して行う

            const clientOptions = appState.data.clients.map(c => `<option value="${c.id}" ${assignment.clientId === c.id ? 'selected' : ''}>${c.name}</option>`).join('');
            const workTypeOptions = appState.data.workTypes.map(wt => `<option value="${wt.id}" ${assignment.workTypeId === wt.id ? 'selected' : ''}>${wt.name}</option>`).join('');

            let guardAssignmentRows = assignment.assignedGuards.map((ag, index) => {
                const guard = appState.data.guards.find(g => g.id === ag.guardId);
                const guardOptions = appState.data.guards.map(g => `<option value="${g.id}" ${ag.guardId === g.id ? 'selected' : ''}>${g.name}</option>`).join('');
                return `
                    <div class="flex items-center space-x-2 mb-2">
                        <label class="w-20 text-gray-700 text-sm">隊員 ${index + 1}:</label>
                        <select id="assignedGuard-${index}" class="${inputClass} flex-grow" onchange="updateAssignedGuard(${index}, this.value)">
                            <option value="">未割当</option>
                            ${guardOptions}
                        </select>
                        <button type="button" class="text-red-500 hover:text-red-700 text-lg" onclick="removeAssignedGuard(${index})">
                            <i class="fas fa-minus-circle"></i>
                        </button>
                    </div>
                `;
            }).join('');

            const formHtml = `
                <div class="mb-4">
                    <label for="assignDate" class="${labelClass}">日付</label>
                    <input type="date" id="assignDate" value="${assignment.date}" class="${inputClass}" ${isEditing ? 'disabled' : ''}>
                </div>
                <div class="mb-4">
                    <label for="assignClient" class="${labelClass}">得意先</label>
                    <select id="assignClient" class="${inputClass}" onchange="updateSiteOptions(this.value)" required>
                        <option value="">選択してください</option>
                        ${clientOptions}
                    </select>
                </div>
                <div class="mb-4">
                    <label for="assignSite" class="${labelClass}">配置先</label>
                    <select id="assignSite" class="${inputClass}" required>
                        <option value="">得意先を選択してください</option>
                        ${assignment.clientId ? appState.data.sites.filter(s => s.clientId === assignment.clientId).map(s => `<option value="${s.id}" ${assignment.siteId === s.id ? 'selected' : ''}>${s.name}</option>`).join('') : ''}
                    </select>
                </div>
                <div class="mb-4">
                    <label for="assignWorkType" class="${labelClass}">勤務</label>
                    <select id="assignWorkType" class="${inputClass}" onchange="updatePlannedTime()" required>
                        <option value="">選択してください</option>
                        ${workTypeOptions}
                    </select>
                    <p id="plannedTimeDisplay" class="text-sm text-gray-500 mt-1">予定時間: --:-- ~ --:--</p>
                </div>
                <div class="mb-4">
                    <label for="requiredGuards" class="${labelClass}">必要人数</label>
                    <input type="number" id="requiredGuards" value="${assignment.requiredGuards}" min="1" class="${inputClass}" onchange="updateGuardSlots(this.value)" required>
                </div>
                <div id="guard-assignment-slots" class="border border-gray-300 rounded-md p-4 bg-gray-50">
                    <h5 class="font-semibold mb-2">隊員配置:</h5>
                    ${guardAssignmentRows}
                    <button type="button" class="btn btn-secondary btn-sm mt-2" onclick="addAssignedGuard()">
                        <i class="fas fa-plus mr-1"></i> 隊員追加
                    </button>
                </div>
            `;

            showModal(isEditing ? '配置編集' : '新規配置追加', formHtml, [
                { text: '保存', className: 'btn-primary', onClick: () => saveDailyAssignment() },
                { text: 'キャンセル', className: 'btn-secondary', onClick: () => hideModal() }
            ]);

            // Initialize site options and planned time display
            setTimeout(() => {
                if (assignment.clientId) {
                    // モーダル表示後にサイトオプションを更新し、現在の選択を反映
                    updateSiteOptions(assignment.clientId, assignment.siteId);
                }
                updatePlannedTime();
            }, 100);
        }

        /**
         * 日別配置の新規追加ボタンクリックハンドラ
         */
        function addDailyAssignment() {
            addEditDailyAssignment(null, currentAssignmentDate);
        }

        /**
         * 日別配置の編集ボタンクリックハンドラ
         * @param {string} assignmentId - 編集する配置のID
         */
        function editDailyAssignment(assignmentId) {
            addEditDailyAssignment(assignmentId);
        }

        /**
         * 得意先選択に基づいて配置先ドロップダウンを更新する
         * @param {string} clientId - 選択された得意先のID
         * @param {string} [selectedSiteId] - 初期選択するサイトID (編集時用)
         */
        function updateSiteOptions(clientId, selectedSiteId = null) {
            const siteSelect = document.getElementById('assignSite');
            siteSelect.innerHTML = '<option value="">選択してください</option>';
            const sitesForClient = appState.data.sites.filter(s => s.clientId === clientId);

            sitesForClient.forEach(site => {
                const option = document.createElement('option');
                option.value = site.id;
                option.textContent = site.name;
                if (selectedSiteId && site.id === selectedSiteId) {
                    option.selected = true;
                }
                siteSelect.appendChild(option);
            });

            // モーダル内のappState.currentAssignmentInModalのsiteIdも更新
            if (appState.currentAssignmentInModal) {
                appState.currentAssignmentInModal.clientId = clientId;
                // 得意先が変わったら、配置先はリセットされるべき
                if (!selectedSiteId || !sitesForClient.some(s => s.id === selectedSiteId)) {
                     appState.currentAssignmentInModal.siteId = '';
                } else {
                     appState.currentAssignmentInModal.siteId = selectedSiteId;
                }
            }
        }

        /**
         * 勤務選択に基づいて予定時間を表示する
         */
        function updatePlannedTime() {
            const workTypeSelect = document.getElementById('assignWorkType');
            const plannedTimeDisplay = document.getElementById('plannedTimeDisplay');
            const selectedWorkTypeId = workTypeSelect.value;
            const workType = appState.data.workTypes.find(wt => wt.id === selectedWorkTypeId);

            if (workType) {
                plannedTimeDisplay.textContent = `予定時間: ${workType.defaultStartTime} ~ ${workType.defaultEndTime}`;
                if (appState.currentAssignmentInModal) {
                    appState.currentAssignmentInModal.workTypeId = selectedWorkTypeId;
                }
            } else {
                plannedTimeDisplay.textContent = `予定時間: --:-- ~ --:--`;
                if (appState.currentAssignmentInModal) {
                    appState.currentAssignmentInModal.workTypeId = '';
                }
            }
        }

        /**
         * 必要人数に基づいて隊員割り当てスロットを更新する
         * @param {number} newRequiredGuards - 新しい必要人数
         */
        function updateGuardSlots(newRequiredGuards) {
            const assignment = appState.currentAssignmentInModal;
            if (!assignment) return;

            newRequiredGuards = parseInt(newRequiredGuards);
            if (isNaN(newRequiredGuards) || newRequiredGuards < 1) newRequiredGuards = 1;

            // Adjust assignedGuards array size
            while (assignment.assignedGuards.length < newRequiredGuards) {
                assignment.assignedGuards.push({ guardId: '', actualStartTime: null, actualEndTime: null });
            }
            while (assignment.assignedGuards.length > newRequiredGuards) {
                assignment.assignedGuards.pop();
            }
            assignment.requiredGuards = newRequiredGuards; // Update the requiredGuards in the modal object

            // Re-render guard assignment slots
            const guardSlotsContainer = document.getElementById('guard-assignment-slots');
            let guardAssignmentRows = assignment.assignedGuards.map((ag, index) => {
                const guardOptions = appState.data.guards.map(g => `<option value="${g.id}" ${ag.guardId === g.id ? 'selected' : ''}>${g.name}</option>`).join('');
                return `
                    <div class="flex items-center space-x-2 mb-2">
                        <label class="w-20 text-gray-700 text-sm">隊員 ${index + 1}:</label>
                        <select id="assignedGuard-${index}" class="${inputClass} flex-grow" onchange="updateAssignedGuard(${index}, this.value)">
                            <option value="">未割当</option>
                            ${guardOptions}
                        </select>
                        <button type="button" class="text-red-500 hover:text-red-700 text-lg" onclick="removeAssignedGuard(${index})">
                            <i class="fas fa-minus-circle"></i>
                        </button>
                    </div>
                `;
            }).join('');
            guardSlotsContainer.innerHTML = `
                <h5 class="font-semibold mb-2">隊員配置:</h5>
                ${guardAssignmentRows}
                <button type="button" class="btn btn-secondary btn-sm mt-2" onclick="addAssignedGuard()">
                    <i class="fas fa-plus mr-1"></i> 隊員追加
                </button>
            `;
        }

        /**
         * 隊員割り当てを追加する
         */
        function addAssignedGuard() {
            const assignment = appState.currentAssignmentInModal;
            if (assignment) {
                assignment.assignedGuards.push({ guardId: '', actualStartTime: null, actualEndTime: null });
                assignment.requiredGuards = assignment.assignedGuards.length; // Update required guards count
                updateGuardSlots(assignment.requiredGuards); // Re-render slots
            }
        }

        /**
         * 隊員割り当てを削除する
         * @param {number} index - 削除する隊員のインデックス
         */
        function removeAssignedGuard(index) {
            const assignment = appState.currentAssignmentInModal;
            if (assignment && assignment.assignedGuards.length > 1) { // Ensure at least one guard remains
                assignment.assignedGuards.splice(index, 1);
                assignment.requiredGuards = assignment.assignedGuards.length; // Update required guards count
                updateGuardSlots(assignment.requiredGuards); // Re-render slots
            } else if (assignment && assignment.assignedGuards.length === 1) {
                showModal('警告', '配置には少なくとも1人の隊員が必要です。');
            }
        }

        /**
         * 割り当てられた隊員を更新する
         * @param {number} index - 隊員のインデックス
         * @param {string} guardId - 割り当てる隊員のID
         */
        function updateAssignedGuard(index, guardId) {
            const assignment = appState.currentAssignmentInModal;
            if (assignment && assignment.assignedGuards[index]) {
                assignment.assignedGuards[index].guardId = guardId;
            }
        }

        /**
         * 日別配置を保存する
         */
        function saveDailyAssignment() {
            const assignment = appState.currentAssignmentInModal;
            if (!assignment) {
                showModal('エラー', '保存する配置データが見つかりません。');
                return;
            }

            // DOMから最新のフォーム値を取得し、assignmentオブジェクトに反映
            assignment.clientId = document.getElementById('assignClient').value;
            assignment.siteId = document.getElementById('assignSite').value;
            assignment.workTypeId = document.getElementById('assignWorkType').value;
            assignment.requiredGuards = parseInt(document.getElementById('requiredGuards').value);
            // dateは新規作成時のみ変更可能なので、ここではDOMから再取得しない

            // 隊員割り当ては既にappState.currentAssignmentInModal.assignedGuardsに反映されているはず

            if (!assignment.clientId || !assignment.siteId || !assignment.workTypeId || isNaN(assignment.requiredGuards) || assignment.requiredGuards < 1) {
                showModal('入力エラー', 'すべての必須項目を正しく入力してください。');
                return;
            }

            // 割り当てられた隊員が未割当でないかチェック
            const hasUnassignedGuards = assignment.assignedGuards.some(g => !g.guardId);
            if (hasUnassignedGuards) {
                showModal('入力エラー', '割り当てられていない隊員がいます。すべての隊員を割り当てるか、必要人数を調整してください。');
                return;
            }


            const existingIndex = appState.data.assignments.findIndex(a => a.id === assignment.id);
            if (existingIndex !== -1) {
                // Update existing
                appState.data.assignments[existingIndex] = assignment;
            } else {
                // Add new
                appState.data.assignments.push(assignment);
            }
            saveData();
            loadAssignmentsForDate(); // Re-render daily list (if on daily view)
            loadWeeklyAssignments(); // Re-render weekly list (if on weekly view)
            hideModal();
        }

        /**
         * 日別配置を削除する
         * @param {string} assignmentId - 削除する配置のID
         */
        function deleteDailyAssignment(assignmentId) {
            showModal('確認', '本当にこの配置を削除しますか？', [
                {
                    text: '削除',
                    className: 'btn-danger',
                    onClick: () => {
                        appState.data.assignments = appState.data.assignments.filter(a => a.id !== assignmentId);
                        saveData();
                        loadAssignmentsForDate();
                        loadWeeklyAssignments(); // Also update weekly view
                    }
                },
                { text: 'キャンセル', className: 'btn-secondary' }
            ]);
        }

        // --- Assignment Management (Weekly) ---
        let currentWeeklyStartDate = formatDate(new Date());

        /**
         * 配置管理 (週別) ビューをレンダリングする
         */
        function renderAssignmentWeekly() {
            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">配置管理 (週別)</h2>
                <div class="card p-6 mb-6">
                    <div class="flex items-center space-x-4 mb-6">
                        <label for="weeklyStartDate" class="text-lg font-semibold text-gray-700">週の開始日:</label>
                        <input type="date" id="weeklyStartDate" value="${currentWeeklyStartDate}" class="flex-grow max-w-xs ${inputClass}">
                        <button class="btn btn-primary" onclick="loadWeeklyAssignments()">
                            <i class="fas fa-search mr-2"></i> 検索
                        </button>
                    </div>
                    <div id="weekly-assignment-table-container" class="overflow-x-auto">
                        <!-- Weekly assignments table will be rendered here -->
                    </div>
                    <button class="btn btn-primary mt-6" onclick="addEditDailyAssignment(null, currentWeeklyStartDate)">
                        <i class="fas fa-plus mr-2"></i> 新規配置追加
                    </button>
                </div>
            `;
            document.getElementById('weeklyStartDate').addEventListener('change', (e) => {
                currentWeeklyStartDate = e.target.value;
                loadWeeklyAssignments();
            });
            loadWeeklyAssignments(); // Initial load
        }

        /**
         * 週別配置一覧をロードして表示する
         */
        function loadWeeklyAssignments() {
            const startDate = new Date(currentWeeklyStartDate);
            const datesOfWeek = [];
            for (let i = 0; i < 7; i++) {
                const date = new Date(startDate);
                date.setDate(startDate.getDate() + i);
                datesOfWeek.push(formatDate(date));
            }

            const assignmentsOfWeek = appState.data.assignments.filter(a => datesOfWeek.includes(a.date));

            // Group assignments by site and then by date
            const groupedAssignments = {}; // { siteId: { date: [assignments] } }
            assignmentsOfWeek.forEach(assign => {
                if (!groupedAssignments[assign.siteId]) {
                    groupedAssignments[assign.siteId] = {};
                }
                if (!groupedAssignments[assign.siteId][assign.date]) {
                    groupedAssignments[assign.siteId][assign.date] = [];
                }
                groupedAssignments[assign.siteId][assign.date].push(assign);
            });

            let tableHtml = `
                <table class="table-auto min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">得意先 / 配置先</th>
                            ${datesOfWeek.map(date => `
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    ${formatJapaneseDate(date).substring(5)} (${['日', '月', '火', '水', '木', '金', '土'][new Date(date).getDay()]})
                                </th>
                            `).join('')}
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-gray-200">
            `;

            if (Object.keys(groupedAssignments).length === 0) {
                tableHtml += `<tr><td colspan="8" class="text-center text-gray-500 py-4">この週の配置予定はありません。</td></tr>`;
            } else {
                // Get all unique site IDs for sorting and consistent rows
                const allUniqueSiteIds = Array.from(new Set(assignmentsOfWeek.map(a => a.siteId)))
                    .sort((idA, idB) => {
                        const siteA = appState.data.sites.find(s => s.id === idA);
                        const siteB = appState.data.sites.find(s => s.id === idB);
                        return (siteA?.name || '').localeCompare(siteB?.name || '');
                    });

                allUniqueSiteIds.forEach(siteId => {
                    const site = appState.data.sites.find(s => s.id === siteId);
                    const client = site ? appState.data.clients.find(c => c.id === site.clientId) : null;
                    const siteName = site ? site.name : '不明な配置先';
                    const clientName = client ? client.name : '不明な得意先';

                    tableHtml += `
                        <tr>
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                <p class="text-xs text-gray-500">${clientName}</p>
                                ${siteName}
                            </td>
                            ${datesOfWeek.map(date => {
                                const dailyAssignments = groupedAssignments[siteId]?.[date] || [];
                                let cellContent = '';
                                if (dailyAssignments.length > 0) {
                                    cellContent = dailyAssignments.map(assign => {
                                        const workType = appState.data.workTypes.find(wt => wt.id === assign.workTypeId);
                                        const assignedGuards = assign.assignedGuards.map(ag => appState.data.guards.find(g => g.id === ag.guardId)?.name || '未割当').join(', ');
                                        return `
                                            <div class="mb-2 p-2 bg-blue-50 rounded-md border border-blue-200 text-sm">
                                                <p class="font-semibold">${workType ? workType.name : '不明'}</p>
                                                <p>人数: ${assign.requiredGuards}名</p>
                                                <p>隊員: ${assignedGuards}</p>
                                                <div class="flex space-x-1 mt-1">
                                                    <button class="text-blue-600 hover:text-blue-800 text-xs" onclick="editDailyAssignment('${assign.id}')">
                                                        <i class="fas fa-edit"></i> 編集
                                                    </button>
                                                    <button class="text-red-600 hover:text-red-800 text-xs" onclick="deleteDailyAssignment('${assign.id}')">
                                                        <i class="fas fa-trash-alt"></i> 削除
                                                    </button>
                                                </div>
                                            </div>
                                        `;
                                    }).join('');
                                } else {
                                    // 新規追加ボタンは、その日の日付を渡すように変更
                                    cellContent = `
                                        <button class="btn btn-secondary btn-sm" onclick="addEditDailyAssignment(null, '${date}')">
                                            <i class="fas fa-plus mr-1"></i> 追加
                                        </button>
                                    `;
                                }
                                return `<td class="px-6 py-4 align-top">${cellContent}</td>`;
                            }).join('')}
                        </tr>
                    `;
                });
            }

            tableHtml += `
                    </tbody>
                </table>
            `;
            document.getElementById('weekly-assignment-table-container').innerHTML = tableHtml;
        }

        // --- Performance Input ---
        let currentPerformanceDate = formatDate(new Date());
        let performanceFilterClient = '';
        let performanceFilterSite = '';
        let performanceFilterGuard = '';

        /**
         * 実績入力ビューをレンダリングする
         */
        function renderPerformanceInput() {
            const clientOptions = appState.data.clients.map(c => `<option value="${c.id}" ${performanceFilterClient === c.id ? 'selected' : ''}>${c.name}</option>`).join('');
            const siteOptions = appState.data.sites.map(s => `<option value="${s.id}" ${performanceFilterSite === s.id ? 'selected' : ''}>${s.name}</option>`).join('');
            const guardOptions = appState.data.guards.map(g => `<option value="${g.id}" ${performanceFilterGuard === g.id ? 'selected' : ''}>${g.name}</option>`).join('');

            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">実績入力</h2>
                <div class="card p-6 mb-6">
                    <div class="flex flex-wrap items-center space-x-4 mb-6">
                        <label for="performanceDate" class="text-lg font-semibold text-gray-700">日付選択:</label>
                        <input type="date" id="performanceDate" value="${currentPerformanceDate}" class="flex-grow max-w-[180px] ${inputClass}">
                        
                        <label for="performanceClientFilter" class="text-lg font-semibold text-gray-700 ml-4">得意先:</label>
                        <select id="performanceClientFilter" class="flex-grow max-w-[180px] ${inputClass}">
                            <option value="">全て</option>
                            ${clientOptions}
                        </select>

                        <label for="performanceSiteFilter" class="text-lg font-semibold text-gray-700 ml-4">配置先:</label>
                        <select id="performanceSiteFilter" class="flex-grow max-w-[180px] ${inputClass}">
                            <option value="">全て</option>
                            ${siteOptions}
                        </select>

                        <label for="performanceGuardFilter" class="text-lg font-semibold text-gray-700 ml-4">隊員:</label>
                        <select id="performanceGuardFilter" class="flex-grow max-w-[180px] ${inputClass}">
                            <option value="">全て</option>
                            ${guardOptions}
                        </select>

                        <button class="btn btn-primary mt-4 md:mt-0" onclick="applyPerformanceFilters()">
                            <i class="fas fa-search mr-2"></i> 検索/絞り込み
                        </button>
                    </div>
                    <div id="performance-list-container" class="overflow-x-auto">
                        <!-- Performance data will be rendered here -->
                    </div>
                </div>
            `;
            document.getElementById('performanceDate').addEventListener('change', (e) => {
                currentPerformanceDate = e.target.value;
                loadPerformanceData(); // Reload data based on date change
            });
            // Initial load with current filters
            loadPerformanceData();
        }

        /**
         * 実績入力画面のフィルターを適用してデータを再ロードする
         */
        function applyPerformanceFilters() {
            performanceFilterClient = document.getElementById('performanceClientFilter').value;
            performanceFilterSite = document.getElementById('performanceSiteFilter').value;
            performanceFilterGuard = document.getElementById('performanceGuardFilter').value;
            loadPerformanceData();
        }

        /**
         * 選択された日付の実績データをロードして表示する
         */
        function loadPerformanceData() {
            let assignmentsForDate = appState.data.assignments.filter(a => a.date === currentPerformanceDate);
            const container = document.getElementById('performance-list-container');

            // Apply filters
            if (performanceFilterClient) {
                assignmentsForDate = assignmentsForDate.filter(a => a.clientId === performanceFilterClient);
            }
            if (performanceFilterSite) {
                assignmentsForDate = assignmentsForDate.filter(a => a.siteId === performanceFilterSite);
            }

            let filteredAssignmentsWithGuards = [];
            assignmentsForDate.forEach(assign => {
                assign.assignedGuards.forEach(assignedGuard => {
                    if (!performanceFilterGuard || assignedGuard.guardId === performanceFilterGuard) {
                        filteredAssignmentsWithGuards.push({
                            assignment: assign,
                            assignedGuard: assignedGuard,
                            guardIndex: assign.assignedGuards.indexOf(assignedGuard)
                        });
                    }
                });
            });


            if (filteredAssignmentsWithGuards.length === 0) {
                container.innerHTML = `<p class="text-gray-600">この日付、または選択されたフィルター条件に合致する配置予定はありません。</p>`;
                return;
            }

            let tableHtml = `
                <table class="table-auto min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">得意先 / 配置先</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">隊員 / 勤務</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">予定時間</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">実績開始</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">実績終了</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">休憩時間</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">残業時間</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">早出時間</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">操作</th>
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-gray-200">
            `;

            filteredAssignmentsWithGuards.forEach(item => {
                const assign = item.assignment;
                const assignedGuard = item.assignedGuard;
                const guardIndex = item.guardIndex;

                const client = appState.data.clients.find(c => c.id === assign.clientId);
                const site = appState.data.sites.find(s => s.id === assign.siteId);
                const workType = appState.data.workTypes.find(wt => wt.id === assign.workTypeId);
                const guard = appState.data.guards.find(g => g.id === assignedGuard.guardId);

                // Calculate differences
                const plannedStartTime = workType ? workType.defaultStartTime : '00:00';
                const plannedEndTime = workType ? workType.defaultEndTime : '00:00';
                const plannedBreakMinutes = workType ? workType.breakMinutes : 0;

                let actualStartTime = assignedGuard.actualStartTime || '';
                let actualEndTime = assignedGuard.actualEndTime || '';

                let actualWorkMinutes = 0;
                let overtimeMinutes = 0;
                let earlyStartMinutes = 0;
                let calculatedBreakMinutes = 0; // For actual break

                if (actualStartTime && actualEndTime) {
                    actualWorkMinutes = calculateMinutesDifference(actualStartTime, actualEndTime);
                    // Simple calculation for now, can be improved with specific break logic
                    calculatedBreakMinutes = plannedBreakMinutes; // Assume planned break for simplicity, or add input
                    const netActualWorkMinutes = actualWorkMinutes - calculatedBreakMinutes;

                    const plannedTotalMinutes = calculateMinutesDifference(plannedStartTime, plannedEndTime) - plannedBreakMinutes;

                    if (netActualWorkMinutes > plannedTotalMinutes) {
                        overtimeMinutes = netActualWorkMinutes - plannedTotalMinutes;
                    }

                    // Calculate early start (if actual start is before planned start)
                    const [pSH, pSM] = plannedStartTime.split(':').map(Number);
                    const [aSH, aSM] = actualStartTime.split(':').map(Number);
                    const plannedStartTotalMinutes = pSH * 60 + pSM;
                    const actualStartTotalMinutes = aSH * 60 + aSM;

                    if (actualStartTotalMinutes < plannedStartTotalMinutes) {
                        earlyStartMinutes = plannedStartTotalMinutes - actualStartTotalMinutes;
                    }
                }


                tableHtml += `
                    <tr>
                        <td class="px-6 py-4 whitespace-nowrap">
                            <p class="text-sm text-gray-500">${client ? client.name : '不明'}</p>
                            <p class="font-medium text-gray-900">${site ? site.name : '不明'}</p>
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap">
                            <p class="font-medium text-gray-900">${guard ? guard.name : '未割当'}</p>
                            <p class="text-sm text-gray-500">${workType ? workType.name : '不明'}</p>
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                            ${plannedStartTime} ~ ${plannedEndTime}
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap">
                            <input type="time" id="actualStart-${assign.id}-${guardIndex}" value="${actualStartTime}"
                                class="${inputClass} w-32"
                                onchange="updatePerformance('${assign.id}', ${guardIndex}, 'start', this.value)">
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap">
                            <input type="time" id="actualEnd-${assign.id}-${guardIndex}" value="${actualEndTime}"
                                class="${inputClass} w-32"
                                onchange="updatePerformance('${assign.id}', ${guardIndex}, 'end', this.value)">
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                            ${formatMinutesToHoursMinutes(calculatedBreakMinutes)}
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                            ${formatMinutesToHoursMinutes(overtimeMinutes)}
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                            ${formatMinutesToHoursMinutes(earlyStartMinutes)}
                        </td>
                        <td class="px-6 py-4 whitespace-nowrap flex items-center space-x-2">
                            <button class="btn btn-primary btn-sm" onclick="savePerformance('${assign.id}', ${guardIndex})">
                                <i class="fas fa-save mr-1"></i> 保存
                            </button>
                            <button class="btn btn-secondary btn-sm" onclick="fillPlannedTime('${assign.id}', ${guardIndex})">
                                <i class="fas fa-clock mr-1"></i> 予定時間入力
                            </button>
                        </td>
                    </tr>
                `;
            });

            tableHtml += `
                    </tbody>
                </table>
            `;
            container.innerHTML = tableHtml;
        }

        /**
         * 実績入力フィールドの値を一時的に更新する
         * @param {string} assignmentId - 配置ID
         * @param {number} guardIndex - 隊員インデックス
         * @param {'start'|'end'} type - 開始時間か終了時間か
         * @param {string} value - 時間文字列 (HH:MM)
         */
        function updatePerformance(assignmentId, guardIndex, type, value) {
            const assignment = appState.data.assignments.find(a => a.id === assignmentId);
            if (assignment && assignment.assignedGuards[guardIndex]) {
                if (type === 'start') {
                    assignment.assignedGuards[guardIndex].actualStartTime = value;
                } else {
                    assignment.assignedGuards[guardIndex].actualEndTime = value;
                }
                // Re-render the table to show updated calculations immediately
                loadPerformanceData();
            }
        }

        /**
         * 実績データを保存する
         * @param {string} assignmentId - 配置ID
         * @param {number} guardIndex - 隊員インデックス
         */
        function savePerformance(assignmentId, guardIndex) {
            const assignment = appState.data.assignments.find(a => a.id === assignmentId);
            if (assignment && assignment.assignedGuards[guardIndex]) {
                const actualStartInput = document.getElementById(`actualStart-${assignmentId}-${guardIndex}`);
                const actualEndInput = document.getElementById(`actualEnd-${assignmentId}-${guardIndex}`);

                assignment.assignedGuards[guardIndex].actualStartTime = actualStartInput.value;
                assignment.assignedGuards[guardIndex].actualEndTime = actualEndInput.value;

                saveData();
                showModal('保存完了', '実績データが保存されました。');
                loadPerformanceData(); // Re-render to ensure calculations are up-to-date
            }
        }

        /**
         * 実績入力で予定時間を自動入力する
         * @param {string} assignmentId - 配置ID
         * @param {number} guardIndex - 隊員インデックス
         */
        function fillPlannedTime(assignmentId, guardIndex) {
            const assignment = appState.data.assignments.find(a => a.id === assignmentId);
            if (!assignment) return;

            const workType = appState.data.workTypes.find(wt => wt.id === assignment.workTypeId);
            if (!workType) {
                showModal('エラー', 'この勤務タイプの予定時間が見つかりません。');
                return;
            }

            const plannedStartTime = workType.defaultStartTime;
            const plannedEndTime = workType.defaultEndTime;

            const actualStartInput = document.getElementById(`actualStart-${assignmentId}-${guardIndex}`);
            const actualEndInput = document.getElementById(`actualEnd-${assignmentId}-${guardIndex}`);

            if (actualStartInput) actualStartInput.value = plannedStartTime;
            if (actualEndInput) actualEndInput.value = plannedEndTime;

            // Update appState and re-render/save
            assignment.assignedGuards[guardIndex].actualStartTime = plannedStartTime;
            assignment.assignedGuards[guardIndex].actualEndTime = plannedEndTime;

            saveData();
            loadPerformanceData(); // Re-render to show updated calculations
            showModal('自動入力完了', '予定時間が実績入力欄に自動入力されました。');
        }

        // --- Guard Work Ledger ---
        let currentLedgerGuardId = '';
        let currentLedgerYearMonth = getYearMonth(formatDate(new Date()));

        /**
         * 勤務台帳ビューをレンダリングする
         */
        function renderGuardWorkLedger() {
            const guardOptions = appState.data.guards.map(g => `<option value="${g.id}" ${currentLedgerGuardId === g.id ? 'selected' : ''}>${g.name} (${g.guardNo})</option>`).join('');

            // Generate year-month options for the last 12 months and next 12 months
            const today = new Date();
            let yearMonthOptions = '';
            for (let i = -12; i <= 12; i++) {
                const date = new Date(today.getFullYear(), today.getMonth() + i, 1);
                const ym = getYearMonth(formatDate(date));
                yearMonthOptions += `<option value="${ym}" ${currentLedgerYearMonth === ym ? 'selected' : ''}>${ym.replace('-', '年')}月</option>`;
            }

            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">勤務台帳</h2>
                <div class="card p-6 mb-6">
                    <div class="flex flex-wrap items-center space-x-4 mb-6">
                        <label for="ledgerGuardSelect" class="text-lg font-semibold text-gray-700">隊員選択:</label>
                        <select id="ledgerGuardSelect" class="flex-grow max-w-xs ${inputClass}">
                            <option value="">隊員を選択してください</option>
                            ${guardOptions}
                        </select>

                        <label for="ledgerYearMonthSelect" class="text-lg font-semibold text-gray-700 ml-4">年月選択:</label>
                        <select id="ledgerYearMonthSelect" class="flex-grow max-w-xs ${inputClass}">
                            ${yearMonthOptions}
                        </select>

                        <button class="btn btn-primary mt-4 md:mt-0" onclick="loadGuardWorkLedger()">
                            <i class="fas fa-search mr-2"></i> 表示
                        </button>
                    </div>
                    <div id="guard-ledger-output" class="overflow-x-auto">
                        <p class="text-gray-600">隊員と年月を選択して勤務台帳を表示してください。</p>
                    </div>
                    <button class="btn btn-secondary mt-6" onclick="printGuardLedger()">
                        <i class="fas fa-print mr-2"></i> 印刷
                    </button>
                </div>
            `;
            // Set initial values for selects if they are empty
            if (!currentLedgerGuardId && appState.data.guards.length > 0) {
                document.getElementById('ledgerGuardSelect').value = appState.data.guards[0].id;
                currentLedgerGuardId = appState.data.guards[0].id; // Update state
            }
            if (currentLedgerGuardId) {
                loadGuardWorkLedger(); // Initial load if a guard is selected
            }
        }

        /**
         * 選択された隊員と年月の勤務台帳をロードして表示する
         */
        function loadGuardWorkLedger() {
            const guardId = document.getElementById('ledgerGuardSelect').value;
            const yearMonth = document.getElementById('ledgerYearMonthSelect').value;

            if (!guardId || !yearMonth) {
                document.getElementById('guard-ledger-output').innerHTML = `<p class="text-gray-600">隊員と年月を選択してください。</p>`;
                return;
            }

            currentLedgerGuardId = guardId;
            currentLedgerYearMonth = yearMonth;

            const selectedGuard = appState.data.guards.find(g => g.id === guardId);
            if (!selectedGuard) {
                document.getElementById('guard-ledger-output').innerHTML = `<p class="text-red-600">選択された隊員が見つかりません。</p>`;
                return;
            }

            const [year, month] = yearMonth.split('-').map(Number);
            const lastDay = getLastDayOfMonth(year, month);

            const assignmentsForGuardMonth = appState.data.assignments.filter(assign => {
                const assignYearMonth = getYearMonth(assign.date);
                return assignYearMonth === yearMonth && assign.assignedGuards.some(ag => ag.guardId === guardId);
            }).sort((a, b) => new Date(a.date) - new Date(b.date)); // Sort by date

            let tableHtml = `
                <h3 class="text-xl font-semibold text-gray-800 mb-4">
                    ${selectedGuard.name} (${selectedGuard.guardNo}) の勤務台帳 - ${year}年${month}月
                </h3>
                <table class="table-auto min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日付</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">得意先</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">配置先</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">勤務内容</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">予定時間</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">実績時間</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">残業/早出</th>
                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日当</th>
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-gray-200">
            `;

            if (assignmentsForGuardMonth.length === 0) {
                tableHtml += `<tr><td colspan="8" class="text-center text-gray-500 py-4">${selectedGuard.name} の ${year}年${month}月 の勤務予定はありません。</td></tr>`;
            } else {
                let monthlyTotalWage = 0;
                assignmentsForGuardMonth.forEach(assign => {
                    const client = appState.data.clients.find(c => c.id === assign.clientId);
                    const site = appState.data.sites.find(s => s.id === assign.siteId);
                    const workType = appState.data.workTypes.find(wt => wt.id === assign.workTypeId);
                    const assignedGuardEntry = assign.assignedGuards.find(ag => ag.guardId === guardId);

                    if (!assignedGuardEntry) return; // Should not happen if filtered correctly

                    const plannedStartTime = workType ? workType.defaultStartTime : '00:00';
                    const plannedEndTime = workType ? workType.defaultEndTime : '00:00';
                    const plannedBreakMinutes = workType ? workType.breakMinutes : 0;

                    const actualStartTime = assignedGuardEntry.actualStartTime;
                    const actualEndTime = assignedGuardEntry.actualEndTime;

                    let dailyWage = 0;
                    let overtimeMinutes = 0;
                    let earlyStartMinutes = 0;
                    let actualWorkMinutesNet = 0; // 実績から休憩を引いた純粋な労働時間

                    if (actualStartTime && actualEndTime) {
                        const actualTotalMinutes = calculateMinutesDifference(actualStartTime, actualEndTime);
                        actualWorkMinutesNet = actualTotalMinutes - plannedBreakMinutes; // 実績労働時間から所定休憩を引く

                        const plannedTotalMinutes = calculateMinutesDifference(plannedStartTime, plannedEndTime) - plannedBreakMinutes;

                        // 残業時間の計算
                        if (actualWorkMinutesNet > plannedTotalMinutes) {
                            overtimeMinutes = actualWorkMinutesNet - plannedTotalMinutes;
                        }

                        // 早出時間の計算
                        const [pSH, pSM] = plannedStartTime.split(':').map(Number);
                        const [aSH, aSM] = actualStartTime.split(':').map(Number);
                        const plannedStartTotalMinutes = pSH * 60 + pSM;
                        const actualStartTotalMinutes = aSH * 60 + aSM;

                        if (actualStartTotalMinutes < plannedStartTotalMinutes) {
                            earlyStartMinutes = plannedStartTotalMinutes - actualStartTotalMinutes;
                        }

                        // 日当計算
                        // 基本日当
                        dailyWage = selectedGuard.dailyWage || 0;
                        // 残業代加算 (分を時間に戻して時給をかける)
                        dailyWage += (overtimeMinutes / 60) * (selectedGuard.overtimeWagePerHour || 0);
                        // 早出代加算 (早出も残業単価で計算すると仮定)
                        dailyWage += (earlyStartMinutes / 60) * (selectedGuard.overtimeWagePerHour || 0);

                    } else {
                        // 実績が未入力の場合は予定時間で日当を計算
                        dailyWage = selectedGuard.dailyWage || 0;
                    }
                    monthlyTotalWage += dailyWage;


                    tableHtml += `
                        <tr>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${formatJapaneseDate(assign.date).substring(5)}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${client ? client.name : '不明'}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${site ? site.name : '不明'}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${workType ? workType.name : '不明'}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${plannedStartTime} ~ ${plannedEndTime}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">${actualStartTime || '--:--'} ~ ${actualEndTime || '--:--'}</td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">
                                残業: ${formatMinutesToHoursMinutes(overtimeMinutes)}<br>
                                早出: ${formatMinutesToHoursMinutes(earlyStartMinutes)}
                            </td>
                            <td class="px-4 py-2 whitespace-nowrap text-sm text-gray-700">¥${dailyWage.toLocaleString()}</td>
                        </tr>
                    `;
                });
                tableHtml += `
                    <tr class="bg-gray-100 font-bold">
                        <td colspan="7" class="px-4 py-2 text-right">月間合計日当:</td>
                        <td class="px-4 py-2 whitespace-nowrap">¥${monthlyTotalWage.toLocaleString()}</td>
                    </tr>
                `;
            }

            tableHtml += `
                    </tbody>
                </table>
            `;
            document.getElementById('guard-ledger-output').innerHTML = tableHtml;
        }

        /**
         * 勤務台帳を印刷する
         */
        function printGuardLedger() {
            const printContent = document.getElementById('guard-ledger-output').innerHTML;
            const originalBody = document.body.innerHTML;

            document.body.innerHTML = `
                <style>
                    body { font-family: 'Inter', sans-serif; margin: 20px; }
                    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
                    th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
                    th { background-color: #f0f0f0; }
                    .no-print { display: none; }
                </style>
                ${printContent}
            `;
            window.print();
            document.body.innerHTML = originalBody; // Restore original content
            location.reload(); // Reload to ensure all event listeners are re-attached
        }


        // --- Backup Function ---
        /**
         * バックアップビューをレンダリングする
         */
        function renderBackup() {
            contentArea.innerHTML = `
                <h2 class="text-3xl font-bold text-gray-800 mb-8">データバックアップ・復元</h2>
                <div class="card p-6 mb-6">
                    <h3 class="text-xl font-semibold text-gray-800 mb-4">データのバックアップ</h3>
                    <p class="text-gray-600 mb-4">現在のシステムデータをJSONファイルとしてダウンロードします。</p>
                    <button class="btn btn-primary" onclick="downloadBackup()">
                        <i class="fas fa-download mr-2"></i> バックアップをダウンロード
                    </button>
                </div>
                <div class="card p-6">
                    <h3 class="text-xl font-semibold text-gray-800 mb-4">データの復元</h3>
                    <p class="text-gray-600 mb-4">JSONファイルをアップロードしてシステムデータを復元します。現在のデータは上書きされますのでご注意ください。</p>
                    <input type="file" id="backupFileInput" accept=".json" class="${inputClass} mb-4">
                    <button class="btn btn-danger" onclick="restoreBackup()">
                        <i class="fas fa-upload mr-2"></i> バックアップを復元
                    </button>
                </div>
            `;
        }

        /**
         * データをJSONファイルとしてダウンロードする
         */
        function downloadBackup() {
            const dataStr = JSON.stringify(appState.data, null, 2);
            const blob = new Blob([dataStr], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `security_system_backup_${formatDate(new Date())}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            showModal('バックアップ完了', 'データが正常にダウンロードされました。');
        }

        /**
         * JSONファイルからデータを復元する
         */
        function restoreBackup() {
            const fileInput = document.getElementById('backupFileInput');
            const file = fileInput.files[0];

            if (!file) {
                showModal('ファイル未選択', '復元するJSONファイルを選択してください。');
                return;
            }

            const reader = new FileReader();
            reader.onload = (e) => {
                try {
                    const restoredData = JSON.parse(e.target.result);
                    showModal('復元確認', '本当にデータを復元しますか？現在のデータはすべて上書きされます。', [
                        {
                            text: '復元する',
                            className: 'btn-danger',
                            onClick: () => {
                                appState.data = restoredData;
                                saveData(); // Save restored data to localStorage
                                showModal('復元完了', 'データが正常に復元されました。ダッシュボードに戻ります。');
                                renderView('dashboard'); // Go back to dashboard
                            }
                        },
                        { text: 'キャンセル', className: 'btn-secondary' }
                    ]);
                } catch (error) {
                    showModal('復元エラー', 'ファイルの読み込みまたは解析に失敗しました。有効なJSONファイルか確認してください。<br>' + error.message);
                }
            };
            reader.onerror = () => {
                showModal('ファイル読み込みエラー', 'ファイルの読み込み中にエラーが発生しました。');
            };
            reader.readAsText(file);
        }

        // --- Initial Setup ---
        document.addEventListener('DOMContentLoaded', () => {
            try {
                loadData(); // Load data or generate initial data
                renderView(appState.currentView); // Render initial view
            } catch (error) {
                console.error("Error during initial application setup:", error);
                document.getElementById('content-area').innerHTML = '<h2 class="text-2xl font-bold text-red-600">システム初期化エラー</h2><p class="mt-2 text-red-500">システムの読み込み中にエラーが発生しました。コンソールを確認してください。</p>';
            }

            // Add event listeners for sidebar navigation
            document.querySelectorAll('.sidebar-item').forEach(item => {
                item.addEventListener('click', () => {
                    renderView(item.dataset.view);
                });
            });
        });

    </script>
</body>
</html>
