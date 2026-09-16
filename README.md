# Qu-mica
Matéria e energia 
[materia-energia-sort (1).html](https://github.com/user-attachments/files/32293082/materia-energia-sort.1.html)
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Matéria ou Energia?</title>
<style>
  :root{
    --bg:#f4f7fb;
    --card-bg:#ffffff;
    --card-border:#d7dee8;
    --accent:#3d5a80;
    --accent-2:#98c1d9;
    --correct:#2ecc71;
    --incorrect:#e74c3c;
    --text:#293241;
  }
  @media (prefers-color-scheme: dark){
    :root:not([data-theme="light"]){
      --bg:#1b1f27;
      --card-bg:#252a34;
      --card-border:#3a4150;
      --accent:#98c1d9;
      --accent-2:#3d5a80;
      --text:#e7ecf3;
    }
  }
  :root[data-theme="dark"]{
    --bg:#1b1f27;
    --card-bg:#252a34;
    --card-border:#3a4150;
    --accent:#98c1d9;
    --accent-2:#3d5a80;
    --text:#e7ecf3;
  }
  *{box-sizing:border-box;}
  body{
    margin:0;
    font-family:'Segoe UI', Roboto, Arial, sans-serif;
    background:var(--bg);
    color:var(--text);
    min-height:100vh;
    padding:20px;
    display:flex;
    flex-direction:column;
    align-items:center;
    gap:16px;
  }
  h1{
    font-size:1.5rem;
    margin:0;
    text-align:center;
    color:var(--accent);
  }
  p.subtitle{
    margin:0;
    text-align:center;
    max-width:600px;
    font-size:0.95rem;
    opacity:0.85;
  }
  #pool{
    width:100%;
    max-width:900px;
    min-height:70px;
    background:var(--card-bg);
    border:2px dashed var(--card-border);
    border-radius:12px;
    padding:12px;
    display:flex;
    flex-wrap:wrap;
    gap:10px;
    justify-content:center;
  }
  .card{
    background:var(--accent-2);
    color:#0b1220;
    padding:10px 18px;
    border-radius:10px;
    font-weight:600;
    cursor:grab;
    user-select:none;
    box-shadow:0 2px 4px rgba(0,0,0,0.15);
    transition:transform 0.15s ease, box-shadow 0.15s ease;
    touch-action:none;
  }
  .card:active{cursor:grabbing;}
  .card.dragging{
    opacity:0.5;
  }
  #zones{
    display:flex;
    gap:16px;
    width:100%;
    max-width:900px;
    flex-wrap:wrap;
  }
  .zone{
    flex:1 1 260px;
    min-height:220px;
    background:var(--card-bg);
    border:3px solid var(--card-border);
    border-radius:14px;
    padding:14px;
    display:flex;
    flex-direction:column;
    gap:10px;
    transition:border-color 0.2s ease, background 0.2s ease;
  }
  .zone h2{
    margin:0 0 4px 0;
    font-size:1.05rem;
    color:var(--accent);
    text-align:center;
  }
  .zone .desc{
    font-size:0.8rem;
    text-align:center;
    opacity:0.75;
    margin:0 0 6px 0;
  }
  .zone-cards{
    flex:1;
    display:flex;
    flex-wrap:wrap;
    gap:8px;
    align-content:flex-start;
    min-height:60px;
  }
  .zone.dragover{
    border-color:var(--accent);
    background:color-mix(in srgb, var(--accent) 10%, var(--card-bg));
  }
  .card.correct{
    background:var(--correct) !important;
    color:#fff;
    cursor:default;
    animation:pop 0.3s ease;
  }
  .card.incorrect{
    animation:shake 0.4s ease;
  }
  @keyframes pop{
    0%{transform:scale(1);}
    50%{transform:scale(1.15);}
    100%{transform:scale(1);}
  }
  @keyframes shake{
    0%,100%{transform:translateX(0);}
    25%{transform:translateX(-6px);}
    75%{transform:translateX(6px);}
  }
  #status{
    min-height:24px;
    font-weight:600;
    text-align:center;
  }
  #status.wrong{ color:var(--incorrect); }
  #status.ok{ color:var(--correct); }
  #congrats{
    display:none;
    text-align:center;
    background:var(--correct);
    color:#fff;
    padding:16px 24px;
    border-radius:14px;
    font-size:1.3rem;
    font-weight:700;
    box-shadow:0 4px 12px rgba(0,0,0,0.2);
    animation:pop 0.4s ease;
  }
  #resetBtn{
    margin-top:6px;
    padding:8px 18px;
    border:none;
    border-radius:8px;
    background:var(--accent);
    color:#fff;
    font-weight:600;
    cursor:pointer;
    font-size:0.9rem;
  }
  #resetBtn:hover{ opacity:0.9; }
  #scoreBar{
    font-size:0.85rem;
    opacity:0.8;
  }
</style>
</head>
<body>

<h1>🔬 Matéria ou Energia?</h1>
<p class="subtitle">Arraste cada item para a caixa correta. Acertos ficam verdes, erros voltam para o início!</p>
<div id="scoreBar">Classificados: <span id="scoreCount">0</span> / <span id="totalCount">8</span></div>

<div id="pool"></div>

<div id="zones">
  <div class="zone" id="zone-materia" data-type="materia">
    <h2>🪨 Matéria</h2>
    <p class="desc">Ocupa espaço e tem peso</p>
    <div class="zone-cards" id="cards-materia"></div>
  </div>
  <div class="zone" id="zone-energia" data-type="energia">
    <h2>⚡ Energia</h2>
    <p class="desc">Causa transformação</p>
    <div class="zone-cards" id="cards-energia"></div>
  </div>
</div>

<div id="status"></div>
<div id="congrats">🎉 Parabéns! Você classificou tudo corretamente! 🎉</div>
<button id="resetBtn">Reiniciar jogo</button>

<script>
const items = [
  { name: "Água", type: "materia" },
  { name: "Ar", type: "materia" },
  { name: "Luz", type: "energia" },
  { name: "Som", type: "energia" },
  { name: "Calor", type: "energia" },
  { name: "Carro", type: "materia" },
  { name: "Árvore", type: "materia" },
  { name: "Eletricidade", type: "energia" }
];

const pool = document.getElementById('pool');
const status = document.getElementById('status');
const congrats = document.getElementById('congrats');
const scoreCountEl = document.getElementById('scoreCount');
const totalCountEl = document.getElementById('totalCount');
const resetBtn = document.getElementById('resetBtn');

let correctCount = 0;
let draggedCard = null;

totalCountEl.textContent = items.length;

function shuffle(arr){
  const a = [...arr];
  for(let i = a.length - 1; i > 0; i--){
    const j = Math.floor(Math.random() * (i+1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

function createCard(item){
  const card = document.createElement('div');
  card.className = 'card';
  card.textContent = item.name;
  card.draggable = true;
  card.dataset.type = item.type;
  card.dataset.name = item.name;

  card.addEventListener('dragstart', (e) => {
    draggedCard = card;
    card.classList.add('dragging');
    e.dataTransfer.effectAllowed = 'move';
    e.dataTransfer.setData('text/plain', item.name);
  });

  card.addEventListener('dragend', () => {
    card.classList.remove('dragging');
  });

  return card;
}

function buildGame(){
  pool.innerHTML = '';
  document.getElementById('cards-materia').innerHTML = '';
  document.getElementById('cards-energia').innerHTML = '';
  correctCount = 0;
  scoreCountEl.textContent = 0;
  status.textContent = '';
  status.className = '';
  congrats.style.display = 'none';

  const shuffled = shuffle(items);
  shuffled.forEach(item => {
    pool.appendChild(createCard(item));
  });
}

function setupZone(zoneEl){
  const type = zoneEl.dataset.type;
  const container = zoneEl.querySelector('.zone-cards');

  zoneEl.addEventListener('dragover', (e) => {
    e.preventDefault();
    e.dataTransfer.dropEffect = 'move';
    zoneEl.classList.add('dragover');
  });

  zoneEl.addEventListener('dragleave', () => {
    zoneEl.classList.remove('dragover');
  });

  zoneEl.addEventListener('drop', (e) => {
    e.preventDefault();
    zoneEl.classList.remove('dragover');
    if(!draggedCard) return;

    const cardType = draggedCard.dataset.type;

    if(cardType === type){
      // correct
      draggedCard.classList.remove('incorrect');
      draggedCard.classList.add('correct');
      draggedCard.draggable = false;
      container.appendChild(draggedCard);
      correctCount++;
      scoreCountEl.textContent = correctCount;
      status.textContent = `✅ "${draggedCard.dataset.name}" está correto!`;
      status.className = 'ok';

      if(correctCount === items.length){
        setTimeout(() => {
          congrats.style.display = 'block';
          status.textContent = '';
        }, 300);
      }
    } else {
      // incorrect - shake and return to pool
      draggedCard.classList.add('incorrect');
      status.textContent = `❌ "${draggedCard.dataset.name}" não pertence a essa caixa. Tente novamente!`;
      status.className = 'wrong';
      setTimeout(() => {
        draggedCard.classList.remove('incorrect');
      }, 400);
    }

    draggedCard = null;
  });
}

setupZone(document.getElementById('zone-materia'));
setupZone(document.getElementById('zone-energia'));

resetBtn.addEventListener('click', buildGame);

buildGame();
</script>

</body>
</html>
