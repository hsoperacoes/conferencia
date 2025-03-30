<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Comparação de Preços - Coleções</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        table, th, td {
            border: 1px solid black;
        }
        th, td {
            padding: 8px;
            text-align: center;
        }
        select, button {
            padding: 10px;
            margin: 10px;
        }
    </style>
</head>
<body>
    <h1>Comparação de Preços entre Coleções</h1>

    <h2>Selecione duas coleções para comparar</h2>

    <label for="colecao1">Coleção 1:</label>
    <select id="colecao1">
        <!-- As opções serão preenchidas dinamicamente -->
    </select>

    <label for="colecao2">Coleção 2:</label>
    <select id="colecao2">
        <!-- As opções serão preenchidas dinamicamente -->
    </select>

    <button onclick="compararColecoes()">Comparar</button>

    <h2>Diferença de Preço</h2>
    <div id="resultado">
        <!-- O resultado da comparação será exibido aqui -->
    </div>

    <h2>Tabelas de Preços</h2>
    <div id="tabelas"></div>

    <script>
        // Função para criar as tabelas e popular as opções
        const colecoes = [];

        function adicionarColecao(nomeColecao, dados) {
            colecoes.push({ nome: nomeColecao, dados: dados });
            atualizarSelecaoColecoes();
            criarTabela(nomeColecao, dados);
        }

        function atualizarSelecaoColecoes() {
            const colecao1Select = document.getElementById("colecao1");
            const colecao2Select = document.getElementById("colecao2");

            colecao1Select.innerHTML = '';
            colecao2Select.innerHTML = '';

            colecoes.forEach((colecao, index) => {
                const option1 = document.createElement("option");
                option1.value = index;
                option1.textContent = colecao.nome;
                colecao1Select.appendChild(option1);

                const option2 = document.createElement("option");
                option2.value = index;
                option2.textContent = colecao.nome;
                colecao2Select.appendChild(option2);
            });
        }

        function criarTabela(nomeColecao, dados) {
            let tabelaHTML = `<h3>${nomeColecao}</h3><table><thead><tr><th>Referência</th><th>Preço</th></tr></thead><tbody>`;
            
            dados.forEach(item => {
                tabelaHTML += `<tr><td>${item.referencia}</td><td>${item.preco}</td></tr>`;
            });

            tabelaHTML += `</tbody></table>`;
            document.getElementById("tabelas").innerHTML += tabelaHTML;
        }

        function compararColecoes() {
            const colecao1Index = document.getElementById("colecao1").value;
            const colecao2Index = document.getElementById("colecao2").value;

            if (colecao1Index === colecao2Index) {
                alert("Escolha duas coleções diferentes!");
                return;
            }

            const colecao1 = colecoes[colecao1Index].dados;
            const colecao2 = colecoes[colecao2Index].dados;

            let resultadoHTML = `<h3>Diferença de Preço entre ${colecoes[colecao1Index].nome} e ${colecoes[colecao2Index].nome}</h3>`;
            resultadoHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th><th>Diferença</th></tr></thead><tbody>`;

            colecao1.forEach(item1 => {
                const item2 = colecao2.find(item => item.referencia === item1.referencia);
                if (item2) {
                    const diferenca = (parseFloat(item1.preco) - parseFloat(item2.preco)).toFixed(2);
                    resultadoHTML += `<tr><td>${item1.referencia}</td><td>${item1.preco}</td><td>${item2.preco}</td><td>${diferenca}</td></tr>`;
                }
            });

            resultadoHTML += `</tbody></table>`;
            document.getElementById("resultado").innerHTML = resultadoHTML;
        }
    </script>
</body>
</html>
