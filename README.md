<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>BBP 每日食堂與團購 (雲端協作版)</title>
    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-database-compat.js"></script>
    <style>
        /* 保留你原本的 CSS 樣式 */
        :root { --bg-color: #F4EBE4; --card-bg: #FFFFFF; --primary-color: #C0B0A2; --accent-color: #EBD8C9; --text-color: #5A5048; --active-color: #E6DEDC; --progress-bar: #D9C5B2; }
        body { font-family: "PingFang TC", sans-serif; background-color: var(--bg-color); padding: 20px; display: flex; flex-direction: column; align-items: center; }
        .panel { background: var(--card-bg); padding: 20px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); margin-bottom: 20px; width: 100%; max-width: 800px; }
    </style>
</head>
<body>
    <header><h1>🍽️ BBP 雲端食堂</h1></header>
    <div id="app" class="panel">載入中...</div>

    <script>
        // 初始化 Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyCvhZUEhX0MiVSy84TTRluVHN_QgBVaBkg",
            authDomain: "bbp-food.firebaseapp.com",
            databaseURL: "https://bbp-food-default-rtdb.firebaseio.com",
            projectId: "bbp-food",
            storageBucket: "bbp-food.firebasestorage.app",
            messagingSenderId: "596590257801",
            appId: "1:596590257801:web:9cdcb3fb97acbb849f0858"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // 即時監聽：一旦 Firebase 資料變動，全體用戶網頁自動刷新
        db.ref('bbp_data').on('value', (snapshot) => {
            const data = snapshot.val();
            if (data) {
                renderApp(data);
            }
        });

        // 核心渲染邏輯
        function renderApp(data) {
            const app = document.getElementById('app');
            app.innerHTML = `<h2>今日菜單：${data.menu.day1.dish}</h2>
                             <p>參加人數：${data.menu.day1.attendees.length} 人</p>
                             <button onclick="joinMeal()">我想吃 +1</button>`;
        }

        // 雲端寫入邏輯
        function joinMeal() {
            const name = prompt("請輸入你的名字：");
            if (!name) return;
            const ref = db.ref('bbp_data/menu/day1/attendees');
            ref.once('value', (snap) => {
                let list = snap.val() || [];
                if (!list.includes(name)) {
                    list.push(name);
                    ref.set(list); // 直接存入雲端，所有人會同步看到
                }
            });
        }
    </script>
</body>
</html>
