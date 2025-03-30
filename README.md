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
        // URL do seu Web App
        const url = "https://script.google.com/macros/s/AKfycbz3YS7AFhRjA9QX8I2v05_5NCo8D-R5RlR7AnZAmz9QMknX4auK0kWOInwPtSPpOVv6FA/exec";

        // Função para buscar os dados do Google Sheets via Web App
        fetch(url)
            .then(response => response.json())
            .then(data => {
                if (data && data.length) {
                    const colecoes = data; // Dados da planilha do Google

                    // Atualizar as opções de coleções para comparação
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

                    // Criar a tabela de preços para as coleções selecionadas
                    function criarTabelaPrecoPorReferencia() {
                        // Cabeçalho da tabela
                        let tabelaHTML = `<h3>Tabela de Preços por Referência</h3><table><thead><tr><th>Referência</th>`;

                        // Adiciona os nomes das coleções como colunas
                        colecoes.forEach(colecao => {
                            tabelaHTML += `<th>${colecao.nome}</th>`;
                        });

                        tabelaHTML += `</tr></thead><tbody>`;

                        // Encontra todas as referências únicas de produtos
                        const referencias = [];
                        colecoes.forEach(colecao => {
                            colecao.dados.forEach(item => {
                                if (!referencias.includes(item.referencia)) {
                                    referencias.push(item.referencia);
                                }
                            });
                        });

                        // Para cada referência, exibe o preço em cada coleção
                        referencias.forEach(referencia => {
                            tabelaHTML += `<tr><td>${referencia}</td>`;

                            colecoes.forEach(colecao => {
                                const item = colecao.dados.find(d => d.referencia === referencia);
                                const preco = item ? item.preco : '0'; // Substitui "Não disponível" por "0"
                                tabelaHTML += `<td>${preco}</td>`;
                            });

                            tabelaHTML += `</tr>`;
                        });

                        tabelaHTML += `</tbody></table>`;
                        document.getElementById("tabelas").innerHTML = tabelaHTML;
                    }

                    // Comparar os preços das duas coleções selecionadas
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
                                // Substitui "Não disponível" por 0 antes de calcular a diferença
                                const preco1 = item1.preco ? parseFloat(item1.preco) : 0;
                                const preco2 = item2.preco ? parseFloat(item2.preco) : 0;
                                const diferenca = (preco1 - preco2).toFixed(2);
                                resultadoHTML += `<tr><td>${item1.referencia}</td><td>${preco1}</td><td>${preco2}</td><td>${diferenca}</td></tr>`;
                            }
                        });

                        resultadoHTML += `</tbody></table>`;
                        document.getElementById("resultado").innerHTML = resultadoHTML;
                    }

                    // Chama a função para atualizar as seleções das coleções
                    atualizarSelecaoColecoes();

                    // Cria a tabela de preços por referência
                    criarTabelaPrecoPorReferencia();
                } else {
                    console.error("Não há dados ou os dados estão em formato inesperado.");
                }
            })
            .catch(error => {
                console.error("Erro ao carregar os dados da planilha:", error);
            });
    </script>
</body>
</html>
