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
    <select id="colecao1"></select>

    <label for="colecao2">Coleção 2:</label>
    <select id="colecao2"></select>

    <button onclick="compararColecoes()">Comparar</button>

    <h2>Diferença de Preço</h2>
    <div id="resultado"></div>

    <button onclick="filtrarAlteracao()">Mostrar Itens com Alteração de Preço</button>

    <h2>Tabelas de Preços</h2>
    <div id="tabelas"></div>

    <script>
        const colecoes = [
            {
                nome: "OUTONO 2024",
                dados: [
                    { referencia: "5000", preco: "0,00" },
                    { referencia: "5118", preco: "0,00" },
                    { referencia: "5515", preco: "0,00" },
                    { referencia: "5530", preco: "0,00" },
                    { referencia: "5588", preco: "0,00" },
                    { referencia: "5741", preco: "0,00" },
                    { referencia: "5767", preco: "0,00" },
                    { referencia: "5768", preco: "0,00" },
                    { referencia: "5769", preco: "0,00" },
                    { referencia: "5770", preco: "0,00" },
                    { referencia: "5771", preco: "0,00" },
                    { referencia: "5779", preco: "0,00" },
                    { referencia: "7604", preco: "0,00" },
                    { referencia: "7613", preco: "0,00" }
                ]
            },
            {
                nome: "ALTO VERÃO 2021",
                dados: [
                    { referencia: "5118", preco: "29,99" },
                    { referencia: "5728", preco: "39,99" },
                    { referencia: "5738", preco: "19,99" },
                    { referencia: "5741", preco: "19,99" },
                    { referencia: "5746", preco: "10,99" },
                    { referencia: "7613", preco: "69,99" }
                ]
            },
            {
                nome: "OUTONO 2022",
                dados: [
                    { referencia: "5118", preco: "35,99" },
                    { referencia: "5728", preco: "39,99" },
                    { referencia: "5767", preco: "35,99" },
                    { referencia: "5768", preco: "35,99" },
                    { referencia: "5769", preco: "49,99" },
                    { referencia: "5588", preco: "69,99" },
                    { referencia: "7604", preco: "139,99" },
                    { referencia: "7613", preco: "89,99" }
                ]
            },
            {
                nome: "INVERNO 2022",
                dados: [
                    { referencia: "5118", preco: "39,99" },
                    { referencia: "5728", preco: "39,99" },
                    { referencia: "5741", preco: "29,99" },
                    { referencia: "5746", preco: "12,99" },
                    { referencia: "5767", preco: "35,99" },
                    { referencia: "5768", preco: "35,99" },
                    { referencia: "5769", preco: "49,99" },
                    { referencia: "5588", preco: "79,99" },
                    { referencia: "7604", preco: "139,99" },
                    { referencia: "7613", preco: "89,99" }
                ]
            },
            {
                nome: "VERÃO 2022",
                dados: [
                    { referencia: "5118", preco: "39,99" },
                    { referencia: "5728", preco: "39,99" },
                    { referencia: "5741", preco: "29,99" },
                    { referencia: "5767", preco: "35,99" },
                    { referencia: "5768", preco: "35,99" },
                    { referencia: "5769", preco: "49,99" },
                    { referencia: "5588", preco: "79,99" },
                    { referencia: "5600", preco: "99,99" },
                    { referencia: "7613", preco: "99,99" }
                ]
            }
        ];

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

        function criarTabela() {
            let tabelasHTML = '';
            colecoes.forEach(colecao => {
                let tabelaHTML = `<h3>${colecao.nome}</h3><table><thead><tr><th>Referência</th>`;
                colecoes.forEach(c => tabelaHTML += `<th>${c.nome}</th>`);
                tabelaHTML += `</tr></thead><tbody>`;
                colecao.dados.forEach(item => {
                    tabelaHTML += `<tr><td>${item.referencia}</td>`;
                    colecoes.forEach(c => {
                        const preco = c.dados.find(d => d.referencia === item.referencia)?.preco || "0,00";
                        tabelaHTML += `<td>${preco}</td>`;
                    });
                    tabelaHTML += `</tr>`;
                });
                tabelaHTML += `</tbody></table>`;
                tabelasHTML += tabelaHTML;
            });
            document.getElementById("tabelas").innerHTML = tabelasHTML;
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
            resultadoHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th><th>Maior Preço</th><th>Alteração de Preço</th></tr></thead><tbody>`;

            colecao1.forEach(item1 => {
                const item2 = colecao2.find(item => item.referencia === item1.referencia);
                if (item2) {
                    const preco1 = parseFloat(item1.preco.replace(',', '.'));
                    const preco2 = parseFloat(item2.preco.replace(',', '.'));
                    const maiorPreco = Math.max(preco1, preco2).toFixed(2);
                    const alteracao = preco1 !== preco2 ? "Sim" : "Não";

                    resultadoHTML += `<tr><td>${item1.referencia}</td><td>${item1.preco}</td><td>${item2.preco}</td><td>${maiorPreco}</td><td>${alteracao}</td></tr>`;
                }
            });

            resultadoHTML += `</tbody></table>`;
            document.getElementById("resultado").innerHTML = resultadoHTML;
        }

        function filtrarAlteracao() {
            const colecao1Index = document.getElementById("colecao1").value;
            const colecao2Index = document.getElementById("colecao2").value;

            if (colecao1Index === colecao2Index) {
                alert("Escolha duas coleções diferentes!");
                return;
            }

            const colecao1 = colecoes[colecao1Index].dados;
            const colecao2 = colecoes[colecao2Index].dados;

            let itensAlteradosHTML = `<h3>Itens com Alteração de Preço entre ${colecoes[colecao1Index].nome} e ${colecoes[colecao2Index].nome}</h3>`;
            itensAlteradosHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th><th>Maior Preço</th></tr></thead><tbody>`;

            colecao1.forEach(item1 => {
                const item2 = colecao2.find(item => item.referencia === item1.referencia);
                if (item2) {
                    const preco1 = parseFloat(item1.preco.replace(',', '.'));
                    const preco2 = parseFloat(item2.preco.replace(',', '.'));
                    if (preco1 !== preco2) {
                        const maiorPreco = Math.max(preco1, preco2).toFixed(2);
                        itensAlteradosHTML += `<tr><td>${item1.referencia}</td><td>${item1.preco}</td><td>${item2.preco}</td><td>${maiorPreco}</td></tr>`;
                    }
                }
            });

            itensAlteradosHTML += `</tbody></table>`;
            document.getElementById("resultado").innerHTML = itensAlteradosHTML;
        }

        // Inicialização
        atualizarSelecaoColecoes();
        criarTabela();
    </script>
</body>
</html>
