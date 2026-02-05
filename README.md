<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>دردشة نصرو المطورة</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { background: #000; font-family: sans-serif; color: white; }
        .header { background: #075e54; padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }
        .call-icons { display: flex; gap: 20px; font-size: 22px; cursor: pointer; }
        #msgs { height: calc(100vh - 120px); background: #e5ddd5; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; color: black; }
        .bubble { padding: 8px 12px; border-radius: 10px; background: white; max-width: 85%; align-self: flex-start; }
        .me { align-self: flex-end; background: #dcf8c6; }
        .footer { height: 60px; background: #f0f0f0; display: flex; align-items: center; padding: 0 10px; gap: 10px; }
        .input-box { flex: 1; background: white; border-radius: 20px; padding: 5px 15px; display: flex; align-items: center; }
        input { border: none; flex: 1; outline: none; font-size: 16px; }
        .btn-send { background: #075e54; color: white; border: none; border-radius: 50%; width: 45px; height: 45px; cursor: pointer; font-size: 20px; }
    </style>
</head>
<body>

<div class="header">
    <span>👤 دردشة نصرو السرية</span>
    <div class="call-icons">
        <span onclick="callNow()">📞</span>
        <span onclick="callNow()">📹</span>
    </div>
</div>

<div id="msgs"></div>

<div class="footer">
    <div class="input-box">
        <label style="cursor:pointer">📎<input type="file" hidden accept="image/*" onchange="upImg(this)"></label>
        <input type="text" id="mInp" placeholder="مراسلة">
    </div>
    <button class="btn-send" onclick="send()">➤</button>
</div>

<script>
    const config = {
        apiKey: "AIzaSyCz9vKS16q0bk7fMe7QqAdRdTd8_xj3i0A",
        databaseURL: "https://calcchat-b10d1-default-rtdb.firebaseio.com",
        projectId: "calcchat-b10d1"
    };
    firebase.initializeApp(config);
    const db = firebase.database();

    // وظيفة المكالمة: تفتح غرفة فيديو وصوت فوراً
    function callNow() {
        const roomName = "NasroSecretRoom_" + Math.floor(Math.random() * 1000);
        const callUrl = "https://meet.jit.si/" + roomName;
        window.open(callUrl, '_blank');
        db.ref("chat").push({ u: "نظام", t: 'txt', v: "📞 بدأ نصرو مكالمة، اضغط هنا للانضمام: " + callUrl });
    }

    function send() {
        let i = document.getElementById('mInp');
        if(i.value) { db.ref("chat").push({ u: "نصرو", t: 'txt', v: i.value }); i.value = ""; }
    }

    async function upImg(el) {
        let f = el.files[0]; if(!f) return;
        let fd = new FormData(); fd.append("image", f);
        let res = await fetch("https://api.imgbb.com/1/upload?key=7db3e3713063f6390176466f286b2401", { method: "POST", body: fd });
        let d = await res.json();
        db.ref("chat").push({ u: "نصرو", t: 'img', v: d.data.url });
    }

    db.ref("chat").on("child_added", s => {
        let m = s.val();
        let d = document.createElement("div");
        d.className = "bubble " + (m.u === "نصرو" ? "me" : "");
        d.innerHTML = m.t === 'txt' ? m.v : `<img src="${m.v}" width="100%">`;
        document.getElementById("msgs").appendChild(d);
        document.getElementById("msgs").scrollTop = document.getElementById("msgs").scrollHeight;
    });
</script>
</body>
</html>
