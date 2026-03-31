<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title> Lista lanche EBD-31/03/2026 - Atualização Automática</title>
    <style>
        body { font-family: sans-serif; background-color: #f4f7f6; display: flex; justify-content: center; padding: 20px; }
        .container { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); width: 100%; max-width: 450px; }
        h2 { color: #2c3e50; text-align: center; border-bottom: 2px solid #3498db; padding-bottom: 10px; }
        .item-row { display: flex; align-items: center; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #eee; gap: 10px; }
        .item-name { font-weight: bold; color: #34495e; flex: 1; font-size: 14px; }
        .status-doado { color: #e74c3c; font-size: 12px; font-weight: bold; background: #fdf2f2; padding: 5px 10px; border-radius: 20px; border: 1px solid #fab1b1; min-width: 140px; text-align: center; }
        input[type="text"] { padding: 8px; border: 1px solid #ddd; border-radius: 4px; width: 110px; outline: none; }
        .btn-confirm { background: #27ae60; color: white; border: none; padding: 8px 15px; border-radius: 4px; cursor: pointer; font-weight: bold; }
        .btn-confirm:disabled { background: #bdc3c7; cursor: not-allowed; }
        #loading { text-align: center; color: #3498db; font-weight: bold; padding: 10px; }
    </style>
</head>
<body>

<div class="container">
    <div style="text-align:center;">
    <img width="200" height="80" alt="MORADA PRETO" src="https://github.com/user-attachments/assets/bfe3d15b-4a75-4f34-a7e0-98cd00ee3643" />
    </div>
    <h2>🛒 Lista do Lanche EBD- 31/03/2026 </h2>
    <p style="text-align:center; color:#666;">Escreva seu nome no item que deseja doar e clique em OK.</p>
    <div id="loading">Sincronizando com a planilha...</div>
    <div id="lista-alimentos"></div>
</div>

<script>
    // COLE O LINK DA SUA NOVA IMPLANTAÇÃO AQUI:
    const URL_PLANILHA = "https://script.google.com/macros/s/AKfycbzsXwfeS4JcsXm6sjhlUcbeWlX0Ou7TItklUFG26apTZWmP8R8hkwyLclDKO9cg1Oqq/exec";

    const alimentos = [
        "FLOCÃO (2 PACOTES)", "FLOCÃO (1 PACOTES)", "FLOCÃO (1 PACOTES)","CALABRESA (4 UNIDADES)","OVOS (15 UNIDADES)",
        "OVOS (15 UNIDADES)","MAGARINA (500g)","MAGARINA (500g)","PÃO DE LEITE (1 PACOTE)",
        "PÃO DE LEITE (1 PACOTE)","PÃO DE LEITE (1 PACOTE)","PÃO DE LEITE (1 PACOTE)",
        "MAIONESE (1 UNIDADE)","SALSICHA (12 UNIDADES)","TAPIOCA DE COCO (1 UNIDADE)",
        "BOLO FOFO (1 UNIDADE)","BOLO FOFO (1 UNIDADE)","BOLO FOFO (1 UNIDADE)",
        "BOLO FOFO (1 UNIDADE)","BOLO FOFO (1 UNIDADE)","BOLO LISO (1 UNIDADE)",
        "BOLO LISO (1 UNIDADE)","BOLO LISO (1 UNIDADE)","BOLO LISO (1 UNIDADE)",
        "GARRAFA DE CAFÉ (1 GARRAFA)","GARRAFA DE CAFÉ (1 GARRAFA)",
        "GARRAFA DE CAFÉ (1 GARRAFA)","GARRAFA DE CAFÉ (1 GARRAFA)",
        "GARRAFA DE CHÁ (1 GARRAFA)","LEITE (2 LITROS OU 2 CAIXA)",
        "SUCO DE GOIABA (2 LITROS)","SUCO DE ACELORA (2 LITROS)",
        "R$ 10,00 (REAIS)","R$ 10,00 (REAIS)","R$ 10,00 (REAIS)",
        "R$ 10,00 (REAIS)","R$ 10,00 (REAIS)","R$ 15,00 (REAIS)",
        "R$ 15,00 (REAIS)","R$ 15,00 (REAIS)","R$ 15,00 (REAIS)",
        //"Arroz (1kg)", "Arroz (1kg)", "Arroz (1kg)", "Arroz (1kg)", 
        //"Feijão (1kg)", "1 Macarrão", "1 Macarrão", "1 Macarrão", 
       // "Açúcar (1kg)", "Açúcar (1kg)", "Farinha (1kg)", 
       // "Farinha de Trigo (1kg)", "GOMA (1kg)", "Flocão (1 unid)", 
       // "Flocão (1 unid)", "Bolacha (1 pacote)", "Bolacha (1 pacote)", 
       // "Macarrão Instantâneo (2 unid)", "Óleo (1 unid)", 
       // "Margarina (1 unid)", "Café (1 unid)", "Sardinha (1 unid)", 
        //"Sardinha (1 unid)", "Carne de lata (1 unid)", 
        //"Mortadela (1 unid)", "Doce (1 unid)"
    ];

    async function carregarDados() {
        try {
            // O "?t=" + Date.now() força o navegador a buscar dados novos sempre
            const resp = await fetch(URL_PLANILHA + "?t=" + Date.now());
            const jaDoados = await resp.json();
            renderizar(jaDoados);
            document.getElementById('loading').style.display = 'none';
        } catch (e) {
            document.getElementById('loading').innerText = "Erro ao carregar dados.";
            renderizar({});
        }
    }

    function renderizar(jaDoados) {
        const container = document.getElementById('lista-alimentos');
        container.innerHTML = "";
        let controle = JSON.parse(JSON.stringify(jaDoados));

        alimentos.forEach((item, index) => {
            const row = document.createElement('div');
            row.className = 'item-row';
            
            // Verifica se este item específico já tem um doador na planilha
            if (controle[item] && controle[item].length > 0) {
                const doador = controle[item].shift(); // Pega o nome e remove da lista temporária
                row.innerHTML = `<div class="item-name">${item}</div><span class="status-doado">✓ Doador: ${doador}</span>`;
            } else {
                row.innerHTML = `
                    <div class="item-name">${item}</div>
                    <input type="text" id="input-${index}" placeholder="Seu nome">
                    <button class="btn-confirm" id="btn-${index}" onclick="confirmar('${item}', ${index})">OK</button>
                `;
            }
            container.appendChild(row);
        });
    }

    function confirmar(nomeItem, index) {
        const input = document.getElementById(`input-${index}`);
        const btn = document.getElementById(`btn-${index}`);
        const nomeDoador = input.value.trim();

        if (nomeDoador === "") return alert("Por favor, digite seu nome!");

        btn.innerText = "...";
        btn.disabled = true;

        fetch(URL_PLANILHA, {
            method: "POST",
            mode: "no-cors",
            body: JSON.stringify({ item: nomeItem, doador: nomeDoador })
        }).then(() => {
            alert("Obrigado, " + nomeDoador + "! Sua doação foi registrada.");
            location.reload(); // Recarrega para bloquear o item imediatamente
        });
    }

    carregarDados();
</script>
</body>
</html>
