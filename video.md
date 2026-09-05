realizar ajustes nos arquivos index.html e style.css


Analisando o seu script, a lógica de alternar entre play e pause no JavaScript está correta, mas a interface não está mudando porque falta o ajuste no CSS do botão e o evento ended está forçando um loop manual que impede a pausa natural.

Os 2 Pontos de Problema e Como Corrigir
1. A classe .playing no CSS precisa esconder o ícone de Play e mostrar o de Pause
No JS você usa playBtn.classList.add('playing') e playBtn.classList.remove('playing'). Se no seu CSS não houver uma regra definindo o que a classe .playing faz, o botão vai continuar exibindo a mesma imagem/ícone de play o tempo todo.

Ajuste no seu CSS:
/* Estado padrão (Vídeo pausado -> Mostra Play) */
#videoPlayBtn .icon-play {
  display: block;
}
#videoPlayBtn .icon-pause {
  display: none;
}

/* Estado em execução (Vídeo rodando -> Mostra Pause) */
#videoPlayBtn.playing .icon-play {
  display: none;
}
#videoPlayBtn.playing .icon-pause {
  display: block;
}

2. O evento ended está forçando o Play manual
No final do script existe este trecho:

video.addEventListener('ended', () => {
    video.currentTime = 0;
    video.play();
});

Se você tem a tag HTML <video loop>, o próprio navegador já faz o loop nativo. Ter esse ouvinte de ended no JS pode gerar conflito com o estado do botão de pausar no exato milissegundo em que o vídeo reinicia.

Script Corrigido e Otimizado
Substitua a parte do vídeo do seu <script> por esta versão simplificada:

const video = document.getElementById('appVideo');
const playBtn = document.getElementById('videoPlayBtn');

// Alterna play/pause ao clicar no botão ou no próprio vídeo
function togglePlay() {
    if (video.paused) {
        video.play();
    } else {
        video.pause();
    }
}

// Atualiza a classe visual do botão de acordo com o estado do vídeo
function updatePlayBtn() {
    if (video.paused) {
        playBtn.classList.remove('playing');
    } else {
        playBtn.classList.add('playing');
    }
}

// Eventos
playBtn.addEventListener('click', togglePlay);
video.addEventListener('click', togglePlay); // Permite clicar no próprio vídeo para pausar
video.addEventListener('play', updatePlayBtn);
video.addEventListener('pause', updatePlayBtn);

Ajuste Complementar no HTML
Garantas que a sua tag <video> no HTML tenha o atributo loop direto na tag (assim não precisa do listener ended no JS):

<video id="appVideo" loop playsinline poster="img/capa-video.jpg">
  <source src="video/roteiro01_landpage_final.mp4" type="video/mp4">
</video>