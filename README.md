<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Impostos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            padding: 8px;
            text-align: center;
        }
        th {
            background-color: #f2f2f2;
        }
        input {
            width: 100%;
            padding: 5px;
            box-sizing: border-box;
        }
        .button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 20px;
            cursor: pointer;
        }
        .button:hover {
            background-color: #45a049;
        }
        #resultado {
            margin-top: 20px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h1>Calculadora de Impostos</h1>
    
    <!-- Tabela para entrada de produtos -->
    <table>
        <thead>
            <tr>
                <th>Nome do Produto</th>
                <th>Preço (R$)</th>
                <th>Quantidade</th>
            </tr>
        </thead>
        <tbody id="produtos">
            <tr>
                <td><input type="text" class="nome"></td>
                <td><input type="number" class="preco" step="0.01" min="0"></td>
                <td><input type="number" class="quantidade" step="1" min="1"></td>
            </tr>
        </tbody>
    </table>
    
    <!-- Botão para adicionar mais produtos -->
    <button class="button" onclick="adicionarProduto()">Adicionar Produto</button>

    <!-- Entrada para valores totais -->
    <div>
        <label for="total-compra">Total da compra (R$):</label>
        <input type="number" id="total-compra" step="0.01" min="0"><br><br>

        <label for="valor-imposto">Valor total do imposto (R$):</label>
        <input type="number" id="valor-imposto" step="0.01" min="0"><br><br>

        <label for="total-cartao">Total no cartão (opcional, R$):</label>
        <input type="number" id="total-cartao" step="0.01" min="0">
    </div><br>

    <!-- Botão para calcular -->
    <button class="button" onclick="calcularImpostos()">Calcular</button>

    <!-- Resultado -->
    <div id="resultado"></div>

    <script>
        // Função para adicionar uma nova linha na tabela de produtos
        function adicionarProduto() {
            const tabela = document.getElementById('produtos');
            const novaLinha = document.createElement('tr');
            novaLinha.innerHTML = `
                <td><input type="text" class="nome"></td>
                <td><input type="number" class="preco" step="0.01" min="0"></td>
                <td><input type="number" class="quantidade" step="1" min="1"></td>
            `;
            tabela.appendChild(novaLinha);
        }

        // Função para calcular impostos e taxas
        function calcularImpostos() {
            const nomes = Array.from(document.querySelectorAll('.nome')).map(input => input.value.trim());
            const precos = Array.from(document.querySelectorAll('.preco')).map(input => parseFloat(input.value) || 0);
            const quantidades = Array.from(document.querySelectorAll('.quantidade')).map(input => parseInt(input.value) || 0);

            const totalCompra = parseFloat(document.getElementById('total-compra').value) || 0;
            const valorImposto = parseFloat(document.getElementById('valor-imposto').value) || 0;
            const totalCartao = parseFloat(document.getElementById('total-cartao').value) || 0;

            // Validação dos dados
            if (nomes.length === 0 || precos.some(p => p <= 0) || quantidades.some(q => q <= 0)) {
                alert('Preencha todos os campos corretamente!');
                return;
            }
            if (totalCompra <= 0 || valorImposto <= 0) {
                alert('Informe o total da compra e o valor do imposto!');
                return;
            }

            // Cálculo
            const resultadoDiv = document.getElementById('resultado');
            resultadoDiv.innerHTML = ''; // Limpa resultados anteriores
            let totalProdutos = 0;

            // Calcula o valor total dos produtos
            precos.forEach((preco, i) => {
                totalProdutos += preco * quantidades[i];
            });

            // Processa cada produto
            nomes.forEach((nome, i) => {
                const preco = precos[i];
                const quantidade = quantidades[i];
                const totalProduto = preco * quantidade;
                const porcentagem = totalProduto / totalProdutos;

                // Cálculo do imposto e taxas
                const impostoTotalProduto = porcentagem * valorImposto;
                const impostoPorUnidade = impostoTotalProduto / quantidade;
                let resultado = `${nome} (por unidade): Imposto R$ ${impostoPorUnidade.toFixed(2)}`;

                if (totalCartao > 0) {
                    const taxaCartao = totalCartao - totalCompra;
                    const taxaProduto = porcentagem * taxaCartao;
                    const taxaPorUnidade = taxaProduto / quantidade;
                    resultado += `, Taxa Cartão R$ ${taxaPorUnidade.toFixed(2)}`;
                }

                resultadoDiv.innerHTML += `<p>${resultado}</p>`;
            });
        }
    </script>
</body>
</html>
