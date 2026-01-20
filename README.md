<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>แอปตัดสินใจซื้อสินค้า</title>
    <style>
        body { font-family: 'Tahoma', sans-serif; background-color: #f0f2f5; display: flex; justify-content: center; padding: 20px; }
        .card { background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 100%; max-width: 450px; }
        h2 { color: #1a73e8; text-align: center; margin-bottom: 25px; }
        .input-group { margin-bottom: 18px; }
        label { display: block; margin-bottom: 8px; font-weight: bold; color: #444; }
        
        /* สไตล์สำหรับช่องกรอกที่มีหน่วยบาท */
        .input-container { position: relative; display: flex; align-items: center; }
        input[type="number"], select { width: 100%; padding: 12px; border: 1px solid #ccc; border-radius: 10px; font-size: 16px; outline: none; }
        input:focus { border-color: #1a73e8; }
        .unit { margin-left: -45px; width: 40px; color: #777; font-weight: normal; }

        button { width: 100%; padding: 15px; background-color: #1a73e8; color: white; border: none; border-radius: 10px; font-size: 18px; font-weight: bold; cursor: pointer; margin-top: 15px; transition: 0.3s; }
        button:hover { background-color: #1557b0; }
        
        #result { margin-top: 25px; padding: 20px; border-radius: 12px; display: none; background: #f8f9fa; border: 1px dashed #ccc; }
        .res-row { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 16px; }
        .conclusion { text-align: center; font-size: 22px; font-weight: bold; margin-top: 15px; padding-top: 15px; border-top: 1px solid #eee; }
        .buy { color: #28a745; }
        .dont-buy { color: #dc3545; }
    </style>
</head>
<body>

<div class="card">
    <h2>🛍️ วิเคราะห์การซื้อสินค้า</h2>
    
    <div class="input-group">
        <label>0. ราคาสินค้า</label>
        <div class="input-container">
            <input type="number" id="price" placeholder="0.00">
            <span class="unit">บาท</span>
        </div>
    </div>

    <div class="input-group">
        <label>1. งบประมาณที่คุณมี</label>
        <div class="input-container">
            <input type="number" id="budget" placeholder="0.00">
            <span class="unit">บาท</span>
        </div>
    </div>

    <div class="input-group">
        <label>2. หมวดหมู่สินค้า</label>
        <select id="category">
            <option value="90">ยาและสุขภาพ (90%)</option>
            <option value="80">อาหารและเครื่องดื่ม (80%)</option>
            <option value="80">ของใช้ส่วนตัวและสุขอนามัย (80%)</option>
            <option value="70">ของใช้ในบ้าน (70%)</option>
            <option value="70">อุปกรณ์การเรียน/สำนักงาน (70%)</option>
            <option value="65">เสื้อผ้าและเครื่องแต่งกาย (65%)</option>
            <option value="55">เครื่องใช้ไฟฟ้า (55%)</option>
            <option value="45">อื่นๆ (45%)</option>
            <option value="40">การเดินทางและยานพาหนะ (40%)</option>
            <option value="20">ความบันเทิง (20%)</option>
        </select>
    </div>

    <div class="input-group">
        <label>3. ตรวจสอบสต็อก/ความจำเป็น</label>
        <select id="stock">
            <option value="85">ไม่มีสินค้า/ไม่เพียงพอ (85%)</option>
            <option value="60">สินค้าใกล้หมด (60%)</option>
            <option value="20">มีสินค้าอยู่แล้ว (20%)</option>
        </select>
    </div>

    <button onclick="calculate()">วิเคราะห์ผลลัพธ์</button>

    <div id="result">
        <div class="res-row">
            <span>ราคาสินค้า:</span> <span id="displayPrice">0</span> บาท
        </div>
        <div class="res-row">
            <span>งบประมาณของคุณ:</span> <span id="displayBudget">0</span> บาท
        </div>
        <hr>
        <div class="res-row">
            <span><b>ควรซื้อ:</b></span> <b id="percentBuy" class="buy">0%</b>
        </div>
        <div class="res-row">
            <span><b>ไม่ควรซื้อ:</b></span> <b id="percentDontBuy" class="dont-buy">0%</b>
        </div>
        <div id="conclusion" class="conclusion"></div>
    </div>
</div>

<script>
function calculate() {
    const pInput = document.getElementById('price');
    const bInput = document.getElementById('budget');
    const price = parseFloat(pInput.value);
    const budget = parseFloat(bInput.value);
    const score2 = parseFloat(document.getElementById('category').value);
    const score3 = parseFloat(document.getElementById('stock').value);

    if (isNaN(price) || isNaN(budget) || price <= 0) {
        alert("กรุณากรอกราคาสินค้าและงบประมาณให้ถูกต้อง");
        return;
    }

    // 1. คำนวณคะแนนงบประมาณ (Score 1)
    let score1 = 0;
    const ratio = (budget / price) * 100;
    if (ratio >= 131) score1 = 95;
    else if (ratio >= 101) score1 = 70;
    else if (ratio == 100) score1 = 55;
    else score1 = 20;

    // 2. คำนวณค่าเฉลี่ย
    const shouldBuy = (score1 + score2 + score3) / 3;
    const dontBuy = 100 - shouldBuy;

    // 3. แสดงผลลัพธ์
    document.getElementById('result').style.display = 'block';
    document.getElementById('displayPrice').innerText = price.toLocaleString();
    document.getElementById('displayBudget').innerText = budget.toLocaleString();
    document.getElementById('percentBuy').innerText = shouldBuy.toFixed(2) + "%";
    document.getElementById('percentDontBuy').innerText = dontBuy.toFixed(2) + "%";

    const conclusion = document.getElementById('conclusion');
    if (shouldBuy > dontBuy) {
        conclusion.innerHTML = '<span class="buy">สรุป: ควรซื้อ ✅</span>';
    } else {
        conclusion.innerHTML = '<span class="dont-buy">สรุป: ไม่ควรซื้อ ❌</span>';
    }
}
</script>

</body>
</html>
