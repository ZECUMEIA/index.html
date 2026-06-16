// Configuração básica do Phaser 3
const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#81c784',
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};

const game = new Phaser.Game(config);

// Variáveis de estado do jogo
let produtividade = 50;
let sustentabilidade = 50;
let rodada = 0;

let textoProdutividade;
let textoSustentabilidade;
let textoPergunta;
let botoesOpcao = [];

// Banco de dados de perguntas do Agrinho 2026
const perguntas = [
    {
        pergunta: "Como preparar o solo para o plantio de forma sustentável?",
        opcoes: [
            { texto: "A) Plantio Direto (palhada protege o solo)", prod: 15, sust: 25 },
            { texto: "B) Arar intensamente revirando a terra", prod: 10, sust: -15 }
        ]
    },
    {
        pergunta: "Sua fazenda precisa monitorar pragas. O que fazer?",
        opcoes: [
            { texto: "A) Aplicar defensivos químicos em toda a área", prod: 20, sust: -20 },
            { texto: "B) Usar Drones e Manejo Integrado (MIP)", prod: 15, sust: 25 }
        ]
    },
    {
        pergunta: "Como gerenciar a energia e a água da propriedade?",
        opcoes: [
            { texto: "A) Instalar painéis solares e irrigação gota a gota", prod: 20, sust: 30 },
            { texto: "B) Utilizar geradores a diesel e irrigação comum", prod: 15, sust: -15 }
        ]
    },
    {
        pergunta: "O que fazer com as áreas de preservação (APP) da fazenda?",
        opcoes: [
            { texto: "A) Reflorestar as margens dos rios e gerar créditos de carbono", prod: 10, sust: 35 },
            { texto: "B) Desmatar para aumentar a área de pastagem", prod: 25, sust: -40 }
        ]
    }
];

function preload() {
    // Aqui você pode carregar imagens futuramente usando:
    // this.load.image('fazenda', 'caminho/da/imagem.png');
}

function create() {
    // Título do Jogo
    this.add.text(400, 40, 'AGRINHO 2026: AGRO FORTE E SUSTENTÁVEL', {
        fontSize: '28px',
        fill: '#ffffff',
        fontStyle: 'bold'
    }).setOrigin(0.5);

    // Painel de Status
    textoProdutividade = this.add.text(50, 100, '', { fontSize: '20px', fill: '#0d47a1', fontStyle: 'bold' });
    textoSustentabilidade = this.add.text(450, 100, '', { fontSize: '20px', fill: '#1b5e20', fontStyle: 'bold' });

    // Caixa de fundo para a pergunta
    let fundoPergunta = this.add.graphics();
    fundoPergunta.fillStyle(0xffffff, 0.9);
    fundoPergunta.fillRoundedRect(50, 160, 700, 120, 10);

    // Texto da Pergunta
    textoPergunta = this.add.text(70, 180, '', {
        fontSize: '22px',
        fill: '#212121',
        wordWrap: { width: 660, useAdvancedWrap: true }
    });

    atualizarInterface();
    proximaPergunta(this);
}

function update() {
    // Atualização em tempo real se necessário
}

function atualizarInterface() {
    textoProdutividade.setText(`📈 Produtividade: ${produtividade}%`);
    textoSustentabilidade.setText(`🌱 Sustentabilidade: ${sustentabilidade}%`);
}

function proximaPergunta(cena) {
    // Remove botões anteriores
    botoesOpcao.forEach(b => b.destroy());
    botoesOpcao = [];

    if (rodada < perguntas.length) {
        let dadosPergunta = perguntas[rodada];
        textoPergunta.setText(dadosPergunta.pergunta);

        dadosPergunta.opcoes.forEach((opcao, index) => {
            // Desenha o botão de opção
            let yPos = 320 + (index * 90);
            
            let botaoFundo = cena.add.graphics();
            botaoFundo.fillStyle(0x1b5e20, 1);
            botaoFundo.fillRoundedRect(50, yPos, 700, 70, 8);
            
            let botaoTexto = cena.add.text(70, yPos + 20, opcao.texto, {
                fontSize: '18px',
                fill: '#ffffff',
                fontStyle: 'bold'
            });

            // Torna a área interativa
            let zonaInterativa = cena.add.zone(400, yPos + 35, 700, 70).setInteractive();
            
            zonaInterativa.on('pointerover', () => botaoFundo.setAlpha(0.8));
            zonaInterativa.on('pointerout', () => botaoFundo.setAlpha(1));
            
            zonaInterativa.on('pointerdown', () => {
                // Aplica os impactos das escolhas do jogador
                produtividade = Math.max(0, Math.min(100, produtividade + opcao.prod));
                sustentabilidade = Math.max(0, Math.min(100, sustentabilidade + opcao.sust));
                
                rodada++;
                atualizarInterface();
                proximaPergunta(cena);
            });

            // Guarda referências para limpar na próxima rodada
            botoesOpcao.push(botaoFundo, botaoTexto, zonaInterativa);
        });
    } else {
        mostrarFinal(cena);
    }
}

function mostrarFinal(cena) {
    textoPergunta.setText("Simulação Concluída! Veja o resultado da sua gestão:");
    
    let mensagemFinal = "";
    let corFinal = "#ffffff";

    if (sustentabilidade >= 70 && produtividade >= 70) {
        mensagemFinal = "🏆 FAZENDA NOTA 10! Você provou que o Agro Forte e o Futuro Sustentável caminham juntos com tecnologia e respeito ao meio ambiente!";
        corFinal = "#1b5e20";
    } else if (sustentabilidade < 50) {
        mensagemFinal = "⚠️ ALERTA: Sua produtividade foi boa, mas a falta de práticas sustentáveis esgotou o solo e os recursos. O futuro exige preservação!";
        corFinal = "#b71c1c";
    } else {
        mensagemFinal = "👍 BOM TRABALHO! Sua fazenda é equilibrada, mas você ainda pode usar mais tecnologia (como drones e energia solar) para evoluir!";
        corFinal = "#e65100";
    }

    cena.add.text(400, 400, mensagemFinal, {
        fontSize: '22px',
        fill: corFinal,
        fontStyle: 'bold',
        align: 'center',
        wordWrap: { width: 700 }
    }).setOrigin(0.5);
}
