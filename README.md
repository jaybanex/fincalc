<!DOCTYPE html>
<html>
<head>
    <title>Financial Calculator</title>
    <style>
        body {
            background-color: #f5deb3; /* Light Golden */
            font-family: Arial, sans-serif;
        }
        .container {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
        }
        .box {
            display: flex;
            flex-direction: column;
            background-color: #fff; /* Light background for boxes */
            padding: 10px;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        .blue-label {
            color: blue;
        }
        .red-label {
            color: red;
        }
        #deposit, #withdrawal, #bonus, #equity, #accountNumber {
            background-color: #fff;
            color: black;
            border: 2px solid #ccc;
            padding: 5px;
            border-radius: 5px;
        }
        #deposit:focus, #bonus:focus, #equity:focus, #accountNumber:focus {
            border-color: blue;
            outline: none;
        }
        #withdrawal:focus {
            border-color: red;
        }
        #logo {
            display: block;
            margin: 0 auto;
            width: 150px;
        }
        .transaction-history {
            margin-top: 20px;
            padding: 10px;
            background-color: #f1f1f1;
            border-radius: 5px;
            max-height: 400px;
            overflow-y: auto;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        .transaction-box {
            background-color: #fff;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            display: grid;
            grid-template-columns: 1fr 1fr 1fr 1fr;
            gap: 10px;
        }
        .withdrawal-box {
            background-color: red;
            color: white;
            padding: 5px;
            border-radius: 5px;
        }
        .send-box {
            background-color: red;
            color: white;
            padding: 5px;
            border-radius: 5px;
        }
        .difference-box {
            background-color: blue;
            color: white;
            padding: 5px;
            border-radius: 5px;
        }
        .account-box {
            background-color: lightgray;
            color: black;
            padding: 5px;
            border-radius: 5px;
        }
    </style>
    <script>
        let transactions = [];
        let totalWithdrawal = 0;
        let totalSendAmount = 0;
        let totalDifference = 0;

        function calculateBonus() {
            let deposit = parseFloat(document.getElementById("deposit").value) || 0;
            let bonus = deposit * 0.20;
            document.getElementById("bonus").value = bonus.toFixed(2);
            calculateEquity();
        }

        function calculateEquity() {
            let deposit = parseFloat(document.getElementById("deposit").value) || 0;
            let withdrawal = parseFloat(document.getElementById("withdrawal").value) || 0;
            let bonus = parseFloat(document.getElementById("bonus").value) || 0;
            let equity = deposit + withdrawal + bonus;
            document.getElementById("equity").value = equity.toFixed(2);
        }

        function calculateWithdrawal() {
            let withdrawal = parseFloat(document.getElementById("withdrawal").value) || 0;
            document.getElementById("withdrawalAmount").value = withdrawal.toFixed(2);
            
            let afterDeduction = withdrawal - (withdrawal * 0.01);
            document.getElementById("after1Percent").value = afterDeduction.toFixed(2);
            
            let difference = withdrawal - afterDeduction;
            document.getElementById("difference").value = difference.toFixed(2);
            
            calculateEquity();
        }

        function saveTransaction() {
            let deposit = parseFloat(document.getElementById("deposit").value) || 0;
            let withdrawal = parseFloat(document.getElementById("withdrawal").value) || 0;
            let bonus = parseFloat(document.getElementById("bonus").value) || 0;
            let equity = parseFloat(document.getElementById("equity").value) || 0;
            let accountNumber = document.getElementById("accountNumber").value || "N/A";

            let withdrawalAmount = withdrawal;
            let afterDeduction = withdrawal - (withdrawal * 0.01);
            let difference = withdrawal - afterDeduction;

            totalWithdrawal += withdrawalAmount;
            totalSendAmount += afterDeduction;
            totalDifference += difference;

            let transaction = { deposit, withdrawal, bonus, equity, accountNumber, date: new Date().toLocaleString(),
                                withdrawalAmount, afterDeduction, difference };

            // Add transaction to the array
            transactions.push(transaction);

            // Keep only the last 100 transactions
            if (transactions.length > 100) {
                transactions.shift(); // Remove the first transaction (oldest)
            }

            displayTransactions();
        }

        function displayTransactions() {
            let historyDiv = document.getElementById("transaction-history");

            // Display totals at the top of the transaction history box
            historyDiv.innerHTML = `
                <div class="transaction-box">
                    <div class="account-box">Total Withdrawal Amount: ${totalWithdrawal.toFixed(2)}</div>
                    <div class="send-box">Total Send Amount: ${totalSendAmount.toFixed(2)}</div>
                    <div class="difference-box">Net Charge (Difference): ${totalDifference.toFixed(2)}</div>
                </div>
                <h3>Last 100 Transactions</h3>`;

            // Display each individual transaction
            transactions.forEach(trx => {
                historyDiv.innerHTML += `<div class="transaction-box">
                    <div class="account-box">A/c No.: ${trx.accountNumber}</div>
                    <div class="withdrawal-box">Original Withdrawal Amount: ${trx.withdrawalAmount}</div>
                    <div class="send-box">After 1% Deduction: ${trx.afterDeduction.toFixed(2)}</div>
                    <div class="difference-box">Difference (1% Deduction): ${trx.difference.toFixed(2)}</div>
                </div>`;
            });
        }
    </script>
</head>
<body>
    <h2>Financial Calculator</h2>
    
    <div class="container">
        <div class="box">
            <label for="accountNumber" class="blue-label">Account Number:</label>
            <input type="text" id="accountNumber" placeholder="Enter Account Number">
        </div>
    </div>

    <div class="container">
        <div class="box">
            <label for="deposit" class="blue-label">Enter Deposit Amount:</label>
            <input type="number" id="deposit" placeholder="Enter deposit amount" oninput="calculateBonus()">
        </div>
        
        <div class="box">
            <label for="withdrawal" class="red-label">Enter Withdrawal Amount:</label>
            <input type="number" id="withdrawal" placeholder="Enter withdrawal amount" oninput="calculateWithdrawal()">
        </div>
    </div>
    
    <div class="container">
        <div class="box">
            <label for="bonus" class="red-label">Bonus (20% of Deposit):</label>
            <input type="number" id="bonus" placeholder="Bonus amount" oninput="calculateEquity()">
        </div>
        
        <div class="box">
            <label for="equity" class="blue-label">Total Equity (Deposit + Withdrawal + Bonus):</label>
            <input type="number" id="equity" placeholder="Equity amount" readonly>
        </div>
    </div>
    
    <h3>Withdrawal Breakdown</h3>
    <label for="withdrawalAmount">Original Withdrawal Amount:</label>
    <input type="number" id="withdrawalAmount" readonly>
    
    <label for="after1Percent">After 1% Deduction:</label>
    <input type="number" id="after1Percent" readonly>
    
    <label for="difference">Difference (1% Deduction):</label>
    <input type="number" id="difference" readonly>
    
    <button onclick="saveTransaction()">Save Transaction</button>
    
    <div id="transaction-history" class="transaction-history"></div>
</body>
</html>
