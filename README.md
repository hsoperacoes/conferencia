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

    <button onclick="voltarInicio()">Voltar ao Início</button>

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

                        let resultadoHTML = `<h3>Comparação de Preço entre ${colecoes[colecao1Index].nome} e ${colecoes[colecao2Index].nome}</h3>`;
                        resultadoHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th><th>Maior Preço</th><th>Alteração de Preço</th></tr></thead><tbody>`;

                        colecao1.forEach(item1 => {
                            const item2 = colecao2.find(item => item.referencia === item1.referencia);
                            if (item2) {
                                const preco1 = parseFloat(item1.preco) || 0;
                                const preco2 = parseFloat(item2.preco) || 0;
                                const maiorPreco = Math.max(preco1, preco2).toFixed(2);
                                const alteracao = preco1 !== preco2 ? 'Sim' : 'Não';

                                resultadoHTML += `<tr><td>${item1.referencia}</td><td>${item1.preco}</td><td>${item2.preco}</td><td>${maiorPreco}</td><td>${alteracao}</td></tr>`;
                            }
                        });

                        resultadoHTML += `</tbody></table>`;
                        document.getElementById("resultado").innerHTML = resultadoHTML;

                        // Mostrar apenas as duas coleções selecionadas na tabela
                        let tabelaHTML = `<h3>Tabela de Preços: ${colecoes[colecao1Index].nome} vs ${colecoes[colecao2Index].nome}</h3><table><thead><tr><th>Referência</th><th>${colecoes[colecao1Index].nome}</th><th>${colecoes[colecao2Index].nome}</th></tr></thead><tbody>`;

                        // Exibe apenas as referências que estão nas duas coleções selecionadas
                        const referencias = [];

                        colecoes[colecao1Index].dados.forEach(item => {
                            if (!referencias.includes(item.referencia)) {
                                referencias.push(item.referencia);
                            }
                        });

                        colecoes[colecao2Index].dados.forEach(item => {
                            if (!referencias.includes(item.referencia)) {
                                referencias.push(item.referencia);
                            }
                        });

                        // Exibe os preços das coleções selecionadas
                        referencias.forEach(referencia => {
                            tabelaHTML += `<tr><td>${referencia}</td>`;

                            const item1 = colecoes[colecao1Index].dados.find(d => d.referencia === referencia);
                            const item2 = colecoes[colecao2Index].dados.find(d => d.referencia === referencia);
                            
                            tabelaHTML += `<td>${item1 ? item1.preco : '0'}</td>`;
                            tabelaHTML += `<td>${item2 ? item2.preco : '0'}</td>`;

                            tabelaHTML += `</tr>`;
                        });

                        tabelaHTML += `</tbody></table>`;
                        document.getElementById("tabelas").innerHTML = tabelaHTML;
                    }

                    // Voltar para o início
                    function voltarInicio() {
                        document.getElementById("resultado").innerHTML = '';
                        document.getElementById("tabelas").innerHTML = '';
                        document.getElementById("colecao1").value = '';
                        document.getElementById("colecao2").value = '';
                    }

                    // Chama a função para atualizar as seleções das coleções
                    atualizarSelecaoColecoes();
                } else {
                    console.error("Não há dados ou os dados estão em formato inesperado.");
                }
            })
            .catch(error => {
                console.error('Erro ao carregar dados:', error);
            });
    </script>
</body>
</html>
