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
        #tabelas {
            display: none;
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
    <button onclick="voltarInicio()" style="display:none" id="voltarButton">Voltar ao Início</button>

    <h2>Diferença de Preço</h2>
    <div id="resultado">
        <!-- O resultado da comparação será exibido aqui -->
    </div>

    <h2>Tabelas de Preços</h2>
    <div id="tabelas">
        <!-- As tabelas de preços das coleções serão exibidas aqui -->
    </div>

    <button onclick="mostrarAlterados()" style="display:none" id="alteradosButton">Mostrar Itens Alterados</button>

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

                    // Exibir as tabelas de preços de cada coleção
                    function exibirTabelas() {
                        let tabelasHTML = "";
                        colecoes.forEach(colecao => {
                            let tabelaHTML = `<h3>${colecao.nome}</h3><table><thead><tr><th>Referência</th><th>Preço</th></tr></thead><tbody>`;
                            colecao.dados.forEach(item => {
                                tabelaHTML += `<tr><td>${item.referencia}</td><td>${item.preco || 'Não disponível'}</td></tr>`;
                            });
                            tabelaHTML += `</tbody></table>`;
                            tabelasHTML += tabelaHTML;
                        });
                        document.getElementById("tabelas").innerHTML = tabelasHTML;
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
                        resultadoHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th><th>Maior Valor</th><th>Alteração de Preço</th></tr></thead><tbody>`;

                        // Filtra e compara os preços, mostrando os maiores valores e se houve alteração de preço
                        let alterados = [];

                        colecao1.forEach(item1 => {
                            const item2 = colecao2.find(item => item.referencia === item1.referencia);
                            if (item2) {
                                // Garantir que o preço seja numérico, mesmo que esteja vazio
                                const preco1 = item1.preco ? parseFloat(item1.preco) : 0;
                                const preco2 = item2.preco ? parseFloat(item2.preco) : 0;

                                // Se o preço for válido, processa a comparação
                                if (!isNaN(preco1) && !isNaN(preco2)) {
                                    const maiorValor = Math.max(preco1, preco2);
                                    const alteracao = preco1 !== preco2 ? "Sim" : "Não";

                                    resultadoHTML += `<tr><td>${item1.referencia}</td><td>${item1.preco || 'Não disponível'}</td><td>${item2.preco || 'Não disponível'}</td><td>${maiorValor}</td><td>${alteracao}</td></tr>`;

                                    if (alteracao === "Sim") {
                                        alterados.push({ referencia: item1.referencia, preco1: item1.preco, preco2: item2.preco });
                                    }
                                }
                            }
                        });

                        resultadoHTML += `</tbody></table>`;
                        document.getElementById("resultado").innerHTML = resultadoHTML;

                        // Exibe o botão para mostrar apenas os itens alterados
                        document.getElementById("alteradosButton").style.display = "inline-block";

                        // Armazena os itens alterados para filtrar depois
                        window.itensAlterados = alterados;

                        // Exibe os dados da comparação
                        document.getElementById("tabelas").style.display = "block";
                        document.getElementById("voltarButton").style.display = "inline-block";
                    }

                    // Mostrar apenas itens com alteração de preço
                    function mostrarAlterados() {
                        let resultadoHTML = `<h3>Itens com Alteração de Preço</h3>`;
                        resultadoHTML += `<table><thead><tr><th>Referência</th><th>Preço Coleção 1</th><th>Preço Coleção 2</th></tr></thead><tbody>`;

                        window.itensAlterados.forEach(item => {
                            resultadoHTML += `<tr><td>${item.referencia}</td><td>${item.preco1}</td><td>${item.preco2}</td></tr>`;
                        });

                        resultadoHTML += `</tbody></table>`;
                        document.getElementById("resultado").innerHTML = resultadoHTML;
                    }

                    // Função para voltar ao início
                    function voltarInicio() {
                        document.getElementById("resultado").innerHTML = "";
                        document.getElementById("tabelas").style.display = "none";
                        document.getElementById("voltarButton").style.display = "none";
                        document.getElementById("alteradosButton").style.display = "none";
                    }

                    // Chama a função para atualizar as seleções das coleções
                    atualizarSelecaoColecoes();

                    // Exibir as tabelas de preços ao carregar
                    exibirTabelas();
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
