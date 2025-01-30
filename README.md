<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>SIMULADOR DE FINANCIAMENTO</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f4f4f9;
      margin: 0;
      padding: 0;
      color: #333;
    }

    .container {
      max-width: 600px;
      margin: 50px auto;
      padding: 30px;
      background-color: white;
      border-radius: 12px;
      box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    }

    h1 {
      text-align: center;
      color: #2c3e50;
      text-transform: uppercase;
      font-size: 24px;
      margin-bottom: 20px;
      letter-spacing: 1px;
    }

    .input-group {
      margin-bottom: 25px;
    }

    .input-group label {
      display: block;
      font-size: 14px;
      font-weight: 600;
      margin-bottom: 8px;
      color: #555;
    }

    .input-group input, .input-group select {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: 2px solid #ddd;
      border-radius: 6px;
      box-sizing: border-box;
      transition: border-color 0.3s ease;
    }

    .input-group input:focus, .input-group select:focus {
      border-color: #4CAF50;
      outline: none;
    }

    .input-group button {
      width: 100%;
      padding: 14px;
      background-color: #4CAF50;
      color: white;
      font-size: 16px;
      font-weight: bold;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    .input-group button:hover {
      background-color: #45a049;
    }

    .result {
      margin-top: 30px;
      font-size: 16px;
    }

    .result-box {
      padding: 20px;
      border-radius: 8px;
      background-color: #f0f8ff;
      margin-bottom: 20px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
    }

    .highlight {
      font-weight: bold;
      color: #2c3e50;
    }

    .warning {
      color: red;
      font-weight: bold;
    }

    .footer {
      font-size: 14px;
      color: #777;
      text-align: center;
      margin-top: 25px;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>SIMULADOR DE FINANCIAMENTO</h1>

  <div class="input-group">
    <label for="fullName">Nome Completo:</label>
    <input type="text" id="fullName" placeholder="Digite seu nome completo" required oninput="convertNameToUpper()">
  </div>

  <div class="input-group">
    <label for="cpf">CPF:</label>
    <input type="text" id="cpf" placeholder="Digite seu CPF" required oninput="formatCPF()">
  </div>

  <div class="input-group">
    <label for="loanAmount">Valor do Empréstimo (R$):</label>
    <input type="number" id="loanAmount" placeholder="Exemplo: 3000" required>
  </div>

  <div class="input-group">
    <label for="installments">Número de Parcelas:</label>
    <select id="installments">
      <option value="3">1 a 3</option>
      <option value="6">4 a 6</option>
      <option value="9">7 a 9</option>
      <option value="12">10 a 12</option>
      <option value="15">13 a 15</option>
      <option value="18">16 a 18</option>
    </select>
  </div>

  <div class="input-group">
    <button onclick="calculateFinancing()">Gerar Proposta</button>
  </div>

  <div class="result" id="result"></div>
</div>

<script>
  function formatCurrency(value) {
    return value.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
  }

  function convertNameToUpper() {
    document.getElementById("fullName").value = document.getElementById("fullName").value.toUpperCase();
  }

  function formatCPF() {
    let cpf = document.getElementById("cpf").value.replace(/\D/g, '');
    cpf = cpf.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, '$1.$2.$3-$4');
    document.getElementById("cpf").value = cpf;
  }

  function getCurrentDateTime() {
    const now = new Date();
    return now.toLocaleString('pt-BR', { dateStyle: 'short', timeStyle: 'short' });
  }

  function calculateFinancing() {
    const fullName = document.getElementById("fullName").value.trim();
    const cpf = document.getElementById("cpf").value.trim();
    const loanAmount = parseFloat(document.getElementById("loanAmount").value);
    const installments = parseInt(document.getElementById("installments").value);

    if (!fullName || !cpf || isNaN(loanAmount) || loanAmount <= 0) {
      alert("Por favor, preencha todos os campos corretamente.");
      return;
    }

    // Juros de 30% para 100% em 18 parcelas
    const interestRates = {
      3: 0.30,
      6: 0.40,
      9: 0.50,
      12: 0.60,
      15: 0.75,
      18: 1.00
    };

    const selectedRate = interestRates[installments]; // Taxa de juros de acordo com o número de parcelas

    // Verificando se a taxa de juros foi encontrada corretamente
    if (!selectedRate) {
      alert("Número de parcelas inválido.");
      return;
    }

    const interestAmount = loanAmount * selectedRate;  // Calculando o valor dos juros
    const finalAmount = loanAmount + interestAmount;   // Valor total com juros
    const installmentValue = finalAmount / installments; // Valor de cada parcela

    const legalInterest = 0.01;  // Juros legais de 1% ao mês
    const totalWithLegalInterest = finalAmount + (finalAmount * legalInterest); // Total com juros legais

    const proposalDate = getCurrentDateTime();

    document.getElementById("result").innerHTML = 
      `<div class="result-box">
        <p><span class="highlight">Proposta gerada em:</span> ${proposalDate}</p>
        <p><span class="highlight">Nome:</span> ${fullName}</p>
        <p><span class="highlight">CPF:</span> ${cpf}</p>
        <p><span class="highlight">Valor do Empréstimo:</span> ${formatCurrency(loanAmount)}</p>
        <p><span class="highlight">Parcelas:</span> ${installments}/18</p>
        <p><span class="highlight">Taxa de Juros (${selectedRate * 100}%):</span> ${formatCurrency(interestAmount)}</p>
        <p><span class="highlight">Valor de cada parcela:</span> ${formatCurrency(installmentValue)}</p>
        <p><span class="highlight">Valor total com juros:</span> ${formatCurrency(finalAmount)}</p>
        <p><span class="warning">⚠️ Em caso de atraso, o desconto será perdido e será cobrado juros legais de 1% ao mês.</span></p>
        <p><span class="highlight">Total com juros legais em caso de atraso:</span> ${formatCurrency(totalWithLegalInterest)}</p>
      </div>`;
  }
</script>

</body>
</html>
