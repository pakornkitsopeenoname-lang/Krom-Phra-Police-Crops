<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบจัดการยศและแต้ม - กรมพระตำรวจ</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #0f111a;
            color: #ffffff;
            padding: 20px;
            margin: 0;
        }
        .container {
            max-width: 1000px;
            margin: 30px auto;
            background: #161925;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.5);
            border: 1px solid #23293f;
        }
        h1 { text-align: center; color: #0084ff; margin-bottom: 5px; font-size: 28px; }
        p.subtitle { text-align: center; color: #8e9aa8; margin-bottom: 30px; font-size: 15px; }
        .form-section {
            background: #1f2335;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 30px;
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: center;
            align-items: center;
            border: 1px solid #2e344f;
        }
        input, select, button {
            padding: 10px 14px;
            border-radius: 6px;
            border: 1px solid #3b4261;
            background: #1a1c28;
            color: #fff;
            font-size: 15px;
            outline: none;
        }
        input:focus, select:focus {
            border-color: #0084ff;
        }
        button {
            background: #10b981;
            cursor: pointer;
            border: none;
            font-weight: bold;
            transition: 0.2s;
        }
        button:hover { background: #059669; }
        table {
            width: 100%;
            border-collapse: collapse;
            background-color: #1f2335;
            border-radius: 8px;
            overflow: hidden;
            border: 1px solid #2e344f;
        }
        th, td {
            padding: 14px 15px;
            text-align: center;
            border-bottom: 1px solid #2e344f;
        }
        th { background-color: #0084ff; font-weight: bold; color: white; }
        tr:hover { background-color: #24293e; }
        .point-badge {
            background-color: #f59e0b;
            color: #111827;
            padding: 4px 14px;
            border-radius: 20px;
            font-weight: bold;
            display: inline-block;
        }
        .btn-update { background: #3b82f6; padding: 6px 12px; font-size: 14px; }
        .btn-update:hover { background: #2563eb; }
        .btn-del { background: #4b5563; padding: 6px 12px; font-size: 14px; }
        .btn-del:hover { background: #374151; }
        .select-rank-table { padding: 6px; background: #1a1c28; font-size: 14px; width: 100%; max-width: 220px; color: #fff; border: 1px solid #3b4261; border-radius: 4px; }
    </style>
</head>
<body>

    <div class="container">
        <h1>𐙚 ระบบจัดการยศและแต้มรายบุคคล 𖦹</h1>
        <p class="subtitle">ระเบียบและเกณฑ์การเลื่อนยศ กรมพระตำรวจ</p>

        <!-- ส่วนเพิ่มผู้เล่นใหม่ -->
        <div class="form-section">
            <b style="color: #0084ff;">เพิ่มสมาชิกใหม่:</b>
            <input type="text" id="username" placeholder="ชื่อผู้เล่น Roblox">
            <select id="rank">
                <option value="เจ้ากรม">เจ้ากรม</option>
                <option value="ปลัดกรม">ปลัดกรม</option>
                <option value="หลวงผู้กำกับ">หลวงผู้กำกับ (350 แต้ม)</option>
                <option value="หลวงรองผู้กำกับ">หลวงรองผู้กำกับ (300 แต้ม)</option>
                <option value="ขุนบรรเทาทุกข์">ขุนบรรเทาทุกษ์ (250 แต้ม)</option>
                <option value="หมื่นตระเวร">หมื่นตระเวร (200 แต้ม)</option>
                <option value="พันตระเวน">พันตระเวน (150 แต้ม)</option>
                <option value="หมู่ตระเวน">หมู่ตระเวน (70 แต้ม)</option>
                <option value="พลตระเวนอาวุโส">พลตระเวนอาวุโส (25 แต้ม)</option>
                <option value="พลตระเวน">พลตระเวน (10 แต้ม)</option>
            </select>
            <input type="number" id="points" placeholder="แต้มเริ่มต้น" value="0" style="width: 100px;">
            <button onclick="addPlayer()">➕ เพิ่มรายชื่อ</button>
        </div>

        <!-- ตารางรายชื่อทั้งหมด -->
        <table>
            <thead>
                <tr>
                    <th>ชื่อผู้เล่น (Roblox)</th>
                    <th>ยศปัจจุบัน (ปรับเปลี่ยนได้)</th>
                    <th>แต้มสะสม</th>
                    <th>จัดการแต้ม / ลบรายชื่อ</th>
                </tr>
            </thead>
            <tbody id="player-table-body">
                <!-- ข้อมูลจะถูกแสดงที่นี่ด้วย JavaScript -->
            </tbody>
        </table>
    </div>

    <script>
        // ระบบเก็บข้อมูลไว้ในคอมพิวเตอร์ (ไม่หายเมื่อปิดหน้าเว็บ)
        let players = JSON.parse(localStorage.getItem('roblox_police_data')) || [
            { name: "ตัวอย่าง_พลตำรวจ", rank: "พลตระเวน", points: 10 }
        ];

        // รายชื่อลำดับยศทั้งหมดสำหรับใช้เปลี่ยนในตาราง
        const allRanks = [
            "เจ้ากรม", 
            "ปลัดกรม", 
            "หลวงผู้กำกับ", 
            "หลวงรองผู้กำกับ", 
            "ขุนบรรเทาทุกข์", 
            "หมื่นตระเวร", 
            "พันตระเวน", 
            "หมู่ตระเวน", 
            "พลตระเวนอาวุโส", 
            "พลตระเวน"
        ];

        function saveAndRender() {
            localStorage.setItem('roblox_police_data', JSON.stringify(players));
            const tbody = document.getElementById('player-table-body');
            tbody.innerHTML = "";

            players.forEach((player, index) => {
                let rankOptions = "";
                allRanks.forEach(r => {
                    rankOptions += `<option value="${r}" ${player.rank === r ? 'selected' : ''}>${r}</option>`;
                });

                tbody.innerHTML += `
                    <tr>
                        <td><b>${player.name}</b></td>
                        <td>
                            <select class="select-rank-table" onchange="changeRank(${index}, this.value)">
                                ${rankOptions}
                            </select>
                        </td>
                        <td><span class="point-badge">${player.points} แต้ม</span></td>
                        <td>
                            <input type="number" id="input-${index}" placeholder="แต้ม" style="width: 70px; padding: 5px; font-size:14px; background:#1a1c28; color:#fff; border:1px solid #3b4261; border-radius:4px;" value="1">
                            <button class="btn-update" onclick="adjustPoint(${index}, true)">+ เพิ่ม</button>
                            <button class="btn-update" onclick="adjustPoint(${index}, false)" style="background:#ef4444;">- ลด</button>
                            <button class="btn-del" onclick="deletePlayer(${index})">ลบ</button>
                        </td>
                    </tr>
                `;
            });
        }

        // ฟังก์ชันเพิ่มรายชื่อ
        function addPlayer() {
            const name = document.getElementById('username').value.trim();
            const rank = document.getElementById('rank').value;
            const points = parseInt(document.getElementById('points').value) || 0;

            if (name === "") {
                alert("กรุณากรอกชื่อผู้เล่นด้วยครับ");
                return;
            }

            players.push({ name, rank, points });
            document.getElementById('username').value = "";
            document.getElementById('points').value = "0";
            saveAndRender();
        }

        // ฟังก์ชันเปลี่ยนยศ
        function changeRank(index, newRank) {
            players[index].rank = newRank;
            localStorage.setItem('roblox_police_data', JSON.stringify(players));
        }

        // ฟังก์ชันอัปเดตบวก/ลดแต้ม
        function adjustPoint(index, isPlus) {
            const inputVal = parseInt(document.getElementById(`input-${index}`).value) || 0;
            
            if (isPlus) {
                players[index].points += inputVal;
            } else {
                players[index].points -= inputVal;
                if(players[index].points < 0) players[index].points = 0; // ป้องกันแต้มติดลบ
            }

            saveAndRender();
        }

        // ฟังก์ชันลบผู้เล่น
        function deletePlayer(index) {
            if(confirm("คุณต้องการลบรายชื่อผู้เล่นคนนี้ใช่หรือไม่?")) {
                players.splice(index, 1);
                saveAndRender();
            }
        }

        // รันระบบแสดงผลเมื่อโหลดหน้าเว็บ
        saveAndRender();
    </script>
</body>
</html>
