<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>訂餐系統</title>
  <style>
    table{
      BORDER-COLLAPSE: COLLAPSE;
      WIDTH: 100%;
    }  
      th, td {
        border: 1px solid black;
        pading: 8px;
        text-align: left;
      }
  </style>
</head>
<body>
  <h1>訂餐系統</h1>
  <form action="#">
    <label for="name">姓名：</label>
    <input type="text" id="name" name="name" required>
    <br>

    <label for="order">訂購餐點：</label>
    <input type="text" id="order" name="order" required>
    <br>

    <button type="submit">送出</button>
  </form>

  <table id="result">
    <thead>
      <tr>
        <th>編號</th>
        <th>姓名</th>
        <th>訂購餐點</th>
        <th>訂購時間</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <script>
    const form = document.querySelector('form');
    const resultTable = document.getElementById('result').querySelector('tbody');
    let orderCount = 0; // Start order number from 1
    
    const today = new Date();
    const todayDate = `${today.getFullYear()}-${today.getMonth() + 1}-${today.getDate()}`;

    form.addEventListener('submit', (event) => {
      event.preventDefault();

      const name = document.getElementById('name').value;
      const order = document.getElementById('order').value;
      const orderTime = new Date().toLocaleString(); // Get current time in formatted string

      orderCount++; // Increment order number

      const resultRow = `
        <tr>
          <td>No.${orderCount}</td>
          <td>${name}</td>
          <td>${order}</td>
          <td>${orderTime}</td>
        </tr>
      `;

      resultTable.innerHTML += resultRow;
    });

    // 在當日15:00清除資訊、轉存至.txt檔案、寄送郵件
    setInterval(() => {
      const now = new Date();
      if (now.getHours() === 15 && now.getMinutes() === 0) {
        const resultRows = resultTable.querySelectorAll('tr');
        const resultContent = `訂餐系統訂購資訊 (${todayDate})\n\n`;

        for (let i = 1; i < resultRows.length; i++) {
          const row = resultRows[i];
          const orderNumber = row.querySelector('td:nth-child(1)').textContent;
          const name = row.querySelector('td:nth-child(2)').textContent;
          const order = row.querySelector('td:nth-child(3)').textContent;

          resultContent += `${orderNumber}. ${name}：${order}\n`;
        }

        // 清除表格內容
        resultTable.innerHTML = '';
        orderCount = 0;

        // 將訂購資訊轉存至.txt檔案
        const fileName = `訂餐系統訂購資訊 (${todayDate}).txt`;
        const fileContent = new Blob([resultContent], { type: 'text/plain' });
        const fileURL = URL.createObjectURL(fileContent);

        // 寄送郵件
        Email.send({
          To: 'chenyw@brother-tw.com',
          Subject: `訂餐系統訂購資訊 (${todayDate})`,
          Attachments: [{
            name: fileName,
            link: fileURL
          }]
        });
      }
    }, 60000); // 每分鐘檢查一次

    // Email.send() 函數需安裝 Email.js 函式庫
    // 安裝方式：npm install emailjs-dom
  </script>
</body>
</html>
