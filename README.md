<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BBP 每日食堂與團購 (雲端協作版)</title>
    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-database-compat.js"></script>
    <style>
        /* 這裡貼上你原本的 CSS 樣式，保持不變以維持美觀 */
        :root { --bg-color: #F4EBE4; --card-bg: #FFFFFF; --primary-color: #C0B0A2; --accent-color: #EBD8C9; --text-color: #5A5048; }
        body { font-family: "PingFang TC", sans-serif; background-color: var(--bg-color); color: var(--text-color); margin: 0; padding: 20px; display: flex; flex-direction: column; align-items: center; }
        .panel { background: var(--card-bg); padding: 20px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); width: 100%; max-width: 800px; margin-bottom: 20px; }
        .tab-btn { padding: 10px 24px; cursor: pointer; border-radius: 25px; border: none; background: var(--accent-color); }
        .tab-btn.active { background-color: var(--primary-color); color: white; }
    </style>
</head>
<body>

    <header><h1>🍽️ BBP 每日食堂 (雲端版)</h1></header>

    <div class="tab-container">
        <button class="tab-btn active" id="menuTab">🍽️ 每日菜單</button>
        <button class="tab-btn" id="groupTab">🛒 團購專區</button>
    </div>

    <div id="content" class="panel">載入中...</div>

    <script>
        // 1. 初始化 Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyCvhZUEhX0MiVSy84TTRluVHN_QgBVaBkg",
            databaseURL: "https://bbp-food-default-rtdb.firebaseio.com",
            projectId: "bbp-food",
            // ...其他參數保持不變
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // 2. 即時監聽數據庫，當有人修改，這裡會自動重新渲染畫面
        db.ref('bbp_data').on('value', (snapshot) => {
            const data = snapshot.val();
            if (data) {
                renderFullPage(data);
            }
        });

        // 3. 核心邏輯：把原本的函數改寫為讀寫 Firebase
        function joinMeal(dayKey) {
            const name = prompt("請輸入你的名字：");
            if (!name) return;
            const ref = db.ref(`bbp_data/menu/${dayKey}/attendees`);
            ref.once('value', (snap) => {
                let list = snap.val() || [];
                if (!list.includes(name)) {
                    list.push(name);
                    ref.set(list); // 寫入雲端，全體同步
                }
            });
        }

        function renderFullPage(data) {
            // 這裡整合你原本的渲染邏輯，讀取 data 而不是靜態變數
            document.getElementById('content').innerHTML = `
                <h2>今日菜單：${data.menu.day1.dish}</h2>
                <p>參加人數：${data.menu.day1.attendees.length} 人</p>
                <button onclick="joinMeal('day1')">我想吃 +1</button>
            `;
        }
    </script>
</body>
</html>
