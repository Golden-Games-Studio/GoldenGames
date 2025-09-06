<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>GoldenGames — Mini Arcade</title>
<style>
  :root{
    --bg:#0b0f14;
    --card:#0f1720;
    --muted:#9aa8b2;
    --accent:#f6c865; /* gold */
    --accent-2:#6be1ff;
    --glass: rgba(255,255,255,0.03);
    --neon: 0 0 18px rgba(246,200,101,0.15), 0 0 80px rgba(107,225,255,0.02);
    --radius:14px;
    font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0;
    background:
      radial-gradient(ellipse at 10% 10%, rgba(107,225,255,0.03), transparent 10%),
      radial-gradient(ellipse at 90% 80%, rgba(246,200,101,0.02), transparent 10%),
      linear-gradient(180deg,#05070a 0%,var(--bg) 100%);
    color:#e6eef6;
    -webkit-font-smoothing:antialiased;
    -moz-osx-font-smoothing:grayscale;
    padding:28px;
    display:flex;
    flex-direction:column;
    gap:20px;
  }

  header{
    display:flex;
    align-items:center;
    justify-content:space-between;
    gap:24px;
    max-width:1200px;
    margin:0 auto;
    width:100%;
  }

  .brand{
    display:flex;
    align-items:center;
    gap:14px;
    user-select:none;
  }
  .gold-logo{
    width:62px;height:62px;border-radius:12px;
    background:linear-gradient(135deg,#ffd77a,#f6c865 40%, #d9a51a);
    box-shadow: var(--neon);
    display:flex;align-items:center;justify-content:center;
    font-weight:800;color:#1b1b1b;font-size:18px;
    transform:rotate(-6deg);
  }
  h1{margin:0;font-size:20px;letter-spacing:0.6px}
  p.lead{margin:0;color:var(--muted);font-size:13px}

  .controls{display:flex;gap:10px;align-items:center}
  .btn{
    background:linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));
    border:1px solid rgba(255,255,255,0.04);
    padding:8px 12px;border-radius:10px;color:var(--accent);
    cursor:pointer;font-weight:600;font-size:13px;
    box-shadow: 0 6px 18px rgba(2,6,10,0.6);
    transition:transform .16s ease, box-shadow .16s ease;
  }
  .btn:hover{transform:translateY(-3px); box-shadow: 0 18px 40px rgba(2,6,10,0.8);}
  .btn.secondary{color:var(--muted); border-color:rgba(255,255,255,0.03)}
  main{flex:1;max-width:1200px;margin:0 auto;width:100%}

  .grid{
    display:grid;
    grid-template-columns: repeat(auto-fit,minmax(300px,1fr));
    gap:18px;
  }

  .card{
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border:1px solid rgba(255,255,255,0.03);
    border-radius:var(--radius);
    padding:16px;
    min-height:140px;
    position:relative;
    overflow:hidden;
    transition:transform .24s cubic-bezier(.2,.9,.2,1), box-shadow .24s;
  }
  .card:hover{transform:translateY(-8px); box-shadow:0 24px 60px rgba(2,6,10,0.7)}
  .card h3{margin:0 0 8px 0;font-size:15px}
  .meta{color:var(--muted);font-size:13px;margin-bottom:12px}

  .card .open{
    position:absolute;right:14px;top:14px;
    background:linear-gradient(90deg,var(--accent),#ffd77a);
    color:#111;padding:8px 10px;border-radius:10px;font-weight:700;border:none;cursor:pointer;
  }

  /* Animated decorative shapes */
  .card::after{
    content:"";
    position:absolute;right:-60px;bottom:-60px;
    width:220px;height:220px;border-radius:50%;
    background:linear-gradient(135deg, rgba(107,225,255,0.06), rgba(246,200,101,0.06));
    transform:rotate(26deg);
    pointer-events:none;
  }

  /* Expanded game modal (in-place) */
  .game-area{
    margin-top:12px;padding:12px;background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02));
    border-radius:12px;border:1px solid rgba(255,255,255,0.03);
    display:none;
  }
  .card.expanded .game-area{display:block;animation:pop .28s cubic-bezier(.2,.9,.2,1)}
  @keyframes pop{from{opacity:0;transform:scale(.985) translateY(6px)}to{opacity:1;transform:scale(1) translateY(0)}}

  /* small controls inside */
  .game-row{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
  .small{padding:6px 8px;border-radius:8px;background:var(--glass);color:var(--muted);border:1px solid rgba(255,255,255,0.02);cursor:pointer}
  .counter{font-weight:800;color:var(--accent)}

  /* Canvas styles */
  canvas{background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02));width:100%;border-radius:10px;display:block}

  /* Memory cards */
  .mem-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px}
  .mem-card{background:linear-gradient(180deg,#111827,#0b1220);height:60px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:22px;color:transparent;cursor:pointer;user-select:none;transition:transform .18s}
  .mem-card.revealed{color:#001; background:linear-gradient(135deg,#6be1ff,#f6c865);transform:rotateY(0deg)}

  /* small responsive */
  @media (max-width:420px){
    body{padding:14px}
    .gold-logo{width:52px;height:52px}
  }

  footer{max-width:1200px;margin:0 auto;color:var(--muted);font-size:13px}
  /* little glow animation for the title */
  .gold-title{display:inline-block;padding:6px 10px;border-radius:10px;background:linear-gradient(90deg, rgba(246,200,101,0.07), rgba(107,225,255,0.03));box-shadow:0 8px 24px rgba(246,200,101,0.06)}
  .spark{display:inline-block;margin-left:8px;opacity:.85;transform:translateY(-1px)}
</style>
</head>
<body>
  <header>
    <div class="brand">
      <div class="gold-logo">GG</div>
      <div>
        <h1>GoldenGames <span class="spark">✨</span></h1>
        <p class="lead">A tiny arcade — 10 mini games, dark theme & smooth animations.</p>
      </div>
    </div>
    <div class="controls">
      <button class="btn" id="randomGame">Surprise me</button>
      <button class="btn secondary" id="resetAll">Reset all scores</button>
    </div>
  </header>

  <main>
    <section class="grid">
      <!-- 1 Reaction Time -->
      <article class="card" id="card-reaction">
        <h3>Reaction Time</h3>
        <div class="meta">Click when the color turns gold. Tests reflexes.</div>
        <button class="open" data-open="reaction">Open</button>
        <div class="game-area">
          <div class="game-row">
            <div id="reaction-box" style="flex:1;padding:20px;border-radius:10px;text-align:center;cursor:pointer;background:#06121a;border:2px dashed rgba(255,255,255,0.02);">
              <div id="reaction-text">Click to start</div>
              <div style="margin-top:8px;color:var(--muted);font-size:13px">Best: <span id="reaction-best">—</span> ms</div>
            </div>
            <button class="small" id="reaction-reset">Reset</button>
          </div>
        </div>
      </article>

      <!-- 2 Clicker -->
      <article class="card" id="card-clicker">
        <h3>Speed Clicker</h3>
        <div class="meta">Click the big button fast for 10 seconds.</div>
        <button class="open" data-open="clicker">Open</button>
        <div class="game-area">
          <div class="game-row">
            <div style="flex:1">
              <div style="font-size:22px;font-weight:800" id="clicker-count">0</div>
              <div style="color:var(--muted);font-size:13px">Time left: <span id="clicker-time">10</span>s</div>
            </div>
            <div>
              <button id="clicker-btn" class="btn">CLICK</button>
              <div style="margin-top:8px"><button class="small" id="clicker-start">Start</button></div>
            </div>
          </div>
        </div>
      </article>

      <!-- 3 Memory -->
      <article class="card" id="card-memory">
        <h3>Memory Match</h3>
        <div class="meta">Find pairs. 8 cards.</div>
        <button class="open" data-open="memory">Open</button>
        <div class="game-area">
          <div class="mem-grid" id="memGrid" style="margin-bottom:10px"></div>
          <div class="game-row">
            <div class="counter">Moves: <span id="mem-moves">0</span></div>
            <button class="small" id="mem-reset">Shuffle</button>
          </div>
        </div>
      </article>

      <!-- 4 Whack-a-mole -->
      <article class="card" id="card-whack">
        <h3>Whack-a-Mole</h3>
        <div class="meta">Tap moles as they pop up!</div>
        <button class="open" data-open="whack">Open</button>
        <div class="game-area">
          <div style="display:flex;gap:8px;align-items:center;margin-bottom:8px">
            <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:8px;flex:1" id="holes">
              <!-- holes injected -->
            </div>
            <div style="min-width:110px;text-align:center">
              <div style="font-size:20px;font-weight:800" id="whack-score">0</div>
              <div style="color:var(--muted);font-size:13px">Score</div>
              <div style="margin-top:8px"><button class="small" id="whack-start">Start</button></div>
            </div>
          </div>
        </div>
      </article>

      <!-- 5 Tic Tac Toe -->
      <article class="card" id="card-ttt">
        <h3>Tic-Tac-Toe</h3>
        <div class="meta">Beat a simple AI.</div>
        <button class="open" data-open="ttt">Open</button>
        <div class="game-area">
          <div style="display:grid;grid-template-columns:repeat(3,80px);gap:8px;margin-bottom:10px" id="ttt-board"></div>
          <div class="game-row">
            <div id="ttt-status" style="flex:1;color:var(--muted)">Your turn: X</div>
            <button class="small" id="ttt-reset">Reset</button>
          </div>
        </div>
      </article>

      <!-- 6 Simon Says -->
      <article class="card" id="card-simon">
        <h3>Simon Says</h3>
        <div class="meta">Repeat the color sequence.</div>
        <button class="open" data-open="simon">Open</button>
        <div class="game-area">
          <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:8px;margin-bottom:10px">
            <div id="simon-0" style="padding:20px;border-radius:10px;cursor:pointer;background:#0b1b27;text-align:center">A</div>
            <div id="simon-1" style="padding:20px;border-radius:10px;cursor:pointer;background:#170b1f;text-align:center">B</div>
            <div id="simon-2" style="padding:20px;border-radius:10px;cursor:pointer;background:#0b1710;text-align:center">C</div>
            <div id="simon-3" style="padding:20px;border-radius:10px;cursor:pointer;background:#14100b;text-align:center">D</div>
          </div>
          <div class="game-row">
            <div id="simon-level" style="flex:1;color:var(--muted)">Level: 0</div>
            <button class="small" id="simon-start">Start</button>
          </div>
        </div>
      </article>

      <!-- 7 Pong -->
      <article class="card" id="card-pong">
        <h3>Pong (single player)</h3>
        <div class="meta">Use mouse to move paddle. Beat the CPU.</div>
        <button class="open" data-open="pong">Open</button>
        <div class="game-area">
          <canvas id="pong-canvas" width="600" height="300"></canvas>
          <div class="game-row">
            <div style="flex:1">Score: <span id="pong-score">0</span></div>
            <button class="small" id="pong-start">Start</button>
          </div>
        </div>
      </article>

      <!-- 8 Snake -->
      <article class="card" id="card-snake">
        <h3>Snake</h3>
        <div class="meta">Arrow keys to move. Grow and avoid walls.</div>
        <button class="open" data-open="snake">Open</button>
        <div class="game-area">
          <canvas id="snake-canvas" width="360" height="360" style="height:240px"></canvas>
          <div class="game-row">
            <div style="flex:1">Score: <span id="snake-score">0</span></div>
            <button class="small" id="snake-start">Start</button>
          </div>
        </div>
      </article>

      <!-- 9 Sliding Puzzle -->
      <article class="card" id="card-slide">
        <h3>Sliding Puzzle (3x3)</h3>
        <div class="meta">Arrange numbers 1–8.</div>
        <button class="open" data-open="slide">Open</button>
        <div class="game-area">
          <div style="display:grid;grid-template-columns:repeat(3,80px);gap:6px;margin-bottom:10px" id="slide-board"></div>
          <div class="game-row">
            <div class="counter">Moves: <span id="slide-moves">0</span></div>
            <button class="small" id="slide-shuffle">Shuffle</button>
          </div>
        </div>
      </article>

      <!-- 10 Dodge -->
      <article class="card" id="card-dodge">
        <h3>Dodge</h3>
        <div class="meta">Move the player to avoid falling blocks. Arrow keys or A/D.</div>
        <button class="open" data-open="dodge">Open</button>
        <div class="game-area">
          <canvas id="dodge-canvas" width="420" height="300"></canvas>
          <div class="game-row">
            <div style="flex:1">Score: <span id="dodge-score">0</span></div>
            <button class="small" id="dodge-start">Start</button>
          </div>
        </div>
      </article>

    </section>
  </main>

  <footer>Made with 💛 — GoldenGames. (Single-file HTML demo)</footer>

<script>
(function(){
  // Utility: persist best scores in localStorage under "gg:best"
  const store = {
    get(key, d){try{const s=localStorage.getItem('gg:'+key);return s?JSON.parse(s):d}catch(e){return d}},
    set(key,val){try{localStorage.setItem('gg:'+key,JSON.stringify(val))}catch(e){}},
    clearAll(){Object.keys(localStorage).forEach(k => { if(k.startsWith('gg:')) localStorage.removeItem(k) })}
  };

  // UI open handlers
  document.querySelectorAll('.open').forEach(btn=>{
    btn.addEventListener('click',()=> {
      const root = btn.closest('.card');
      const all = document.querySelectorAll('.card');
      all.forEach(c=>c.classList.remove('expanded'));
      root.classList.toggle('expanded');
    });
  });

  document.getElementById('resetAll').addEventListener('click', ()=>{
    if(confirm('Reset all GoldenGames saved scores?')) { store.clearAll(); location.reload(); }
  });

  // Surprise me
  document.getElementById('randomGame').addEventListener('click', ()=>{
    const cards = Array.from(document.querySelectorAll('.card'));
    const choice = cards[Math.floor(Math.random()*cards.length)];
    document.querySelectorAll('.card').forEach(c=>c.classList.remove('expanded'));
    choice.classList.add('expanded');
    choice.scrollIntoView({behavior:'smooth', block:'center'});
  });


  /* ----------------------------
     1. Reaction Time
     ---------------------------- */
  (function(){
    const box = document.getElementById('reaction-box');
    const text = document.getElementById('reaction-text');
    const bestEl = document.getElementById('reaction-best');
    let waiting=false,t0=0;
    let best = store.get('reactionBest', null);
    if(best) bestEl.textContent = best;

    function reset(){
      waiting=false; text.textContent='Click to start'; box.style.background='#06121a';
      box.style.borderColor='rgba(255,255,255,0.02)';
    }
    reset();
    box.addEventListener('click',()=>{
      if(waiting) return;
      // start: random timer then color change
      text.textContent='Get ready...';
      waiting = true;
      box.style.background='#071827';
      const delay = 800 + Math.random()*2000;
      setTimeout(()=>{
        // go!
        t0 = performance.now();
        box.style.background = 'linear-gradient(90deg,#f6c865,#ffd77a)';
        text.textContent = 'CLICK!';
        box.style.borderColor = '#b0832f';
      }, delay);
    });

    // detect click for measuring reaction when it's in GO state
    box.addEventListener('mousedown', ()=>{
      // only record if in GO color
      if(!waiting) return;
      // check if it was GO by reading text
      const now = performance.now();
      if(text.textContent === 'CLICK!'){
        const dt = Math.round(now - t0);
        text.textContent = dt + ' ms';
        waiting = false;
        if(best===null || dt < best){ best = dt; bestEl.textContent = dt; store.set('reactionBest', dt) }
        setTimeout(reset, 900);
      } else {
        // too early - penalty
        text.textContent = 'Too early!';
        waiting=false;
        setTimeout(reset,900);
      }
    });

    document.getElementById('reaction-reset').addEventListener('click', ()=>{ store.set('reactionBest', null); best=null; bestEl.textContent='—'; reset(); });
  })();

  /* ----------------------------
     2. Speed Clicker
     ---------------------------- */
  (function(){
    const btn = document.getElementById('clicker-btn');
    const start = document.getElementById('clicker-start');
    const countEl = document.getElementById('clicker-count');
    const timeEl = document.getElementById('clicker-time');
    let count=0, timeLeft=10, timer=null;
    btn.addEventListener('click', ()=>{ if(timer) { count++; countEl.textContent=count }});
    start.addEventListener('click', ()=>{
      clearInterval(timer); count=0; timeLeft=10; countEl.textContent='0'; timeEl.textContent='10';
      timer = setInterval(()=>{
        timeLeft-=1;
        timeEl.textContent = timeLeft;
        if(timeLeft<=0){ clearInterval(timer); timer=null; // finish
          const best = store.get('clickerBest', 0);
          if(count>best){ store.set('clickerBest', count); alert('New best: '+count) } else alert('Score: '+count+' | Best: '+best);
        }
      },1000);
    });
  })();

  /* ----------------------------
     3. Memory match (8 cards)
     ---------------------------- */
  (function(){
    const grid = document.getElementById('memGrid');
    const movesEl = document.getElementById('mem-moves');
    const resetBtn = document.getElementById('mem-reset');
    let board = [], revealed = [], first=null, second=null, moves=0, matches=0;
    const icons = ['★','▲','●','■','◆','✿','♣','♪'];
    function shuffle(){
      const arr = icons.slice(0,8);
      const deck = arr.concat(arr).sort(()=>Math.random()-0.5);
      board = deck;
      revealed = board.map(()=>false);
      first=second=null; moves=0; matches=0;
      render();
      movesEl.textContent = moves;
    }
    function render(){
      grid.innerHTML='';
      board.forEach((v,i)=>{
        const d = document.createElement('div');
        d.className='mem-card';
        d.dataset.i=i;
        if(revealed[i]){ d.classList.add('revealed'); d.textContent=v } else { d.textContent=''; }
        d.addEventListener('click', onClick);
        grid.appendChild(d);
      });
    }
    function onClick(e){
      const i = +e.currentTarget.dataset.i;
      if(revealed[i]) return;
      revealed[i]=true; render();
      if(first===null){ first=i; return; }
      if(second===null){ second=i; moves++; movesEl.textContent=moves;
        if(board[first] === board[second]){ matches++; first=second=null; if(matches===8){ setTimeout(()=>alert('You matched all cards in '+moves+' moves!'),200) } }
        else {
          // hide after short delay
          setTimeout(()=>{
            revealed[first]=revealed[second]=false; first=second=null; render();
          },700);
        }
      }
    }
    resetBtn.addEventListener('click', shuffle);
    shuffle();
  })();

  /* ----------------------------
     4. Whack-a-mole
     ---------------------------- */
  (function(){
    const holesDiv = document.getElementById('holes');
    const startBtn = document.getElementById('whack-start');
    const scoreEl = document.getElementById('whack-score');
    let holes=[], score=0, running=false, t=null;
    function setup(){
      holesDiv.innerHTML='';
      for(let i=0;i<9;i++){
        const h=document.createElement('div');
        h.style.height='64px'; h.style.background='#0b1216'; h.style.borderRadius='8px';
        h.style.display='flex'; h.style.alignItems='center'; h.style.justifyContent='center';
        h.style.cursor='pointer'; h.dataset.index=i;
        holesDiv.appendChild(h);
        h.addEventListener('click', ()=>{ if(h.dataset.up==='1'){ score++; scoreEl.textContent=score; h.dataset.up='0'; h.innerHTML=''; h.style.transform='translateY(8px)'; } });
        holes.push(h);
      }
    }
    function pop(){
      if(!running) return;
      const idx = Math.floor(Math.random()*holes.length);
      const h = holes[idx];
      if(h.dataset.up==='1'){ setTimeout(pop,100); return;}
      h.dataset.up='1'; h.innerHTML = '<div style="padding:6px 8px;border-radius:6px;background:linear-gradient(90deg,#ffd77a,#f6c865);color:#1b1000;font-weight:800">M</div>';
      h.style.transform='translateY(-6px)';
      // hide after random short time
      setTimeout(()=>{
        h.dataset.up='0'; h.innerHTML=''; h.style.transform='translateY(0)';
        pop();
      }, 700 + Math.random()*800);
    }
    startBtn.addEventListener('click', ()=>{
      if(running){ running=false; startBtn.textContent='Start' } else {
        running=true; score=0; scoreEl.textContent=0; startBtn.textContent='Stop'; pop();
      }
    });
    setup();
  })();

  /* ----------------------------
     5. Tic Tac Toe (simple AI)
     ---------------------------- */
  (function(){
    const boardEl = document.getElementById('ttt-board');
    const status = document.getElementById('ttt-status');
    const reset = document.getElementById('ttt-reset');
    let board = Array(9).fill(null), human='X', ai='O', turn='human', over=false;

    function render(){
      boardEl.innerHTML='';
      board.forEach((v,i)=>{
        const b = document.createElement('div');
        b.style.width='80px'; b.style.height='80px'; b.style.display='flex'; b.style.alignItems='center'; b.style.justifyContent='center';
        b.style.background='#081018'; b.style.borderRadius='10px'; b.style.fontSize='32px'; b.style.cursor='pointer';
        b.textContent = v || '';
        b.addEventListener('click', ()=>{ if(over || board[i]) return; if(turn!=='human') return; board[i]=human; turn='ai'; render(); check(); if(!over) setTimeout(aiMove,300); });
        boardEl.appendChild(b);
      });
      status.textContent = over ? 'Game over' : (turn==='human' ? 'Your turn: X' : 'AI thinking...');
    }
    function aiMove(){
      // simple AI: win if possible, block if needed, else random
      const win = findWin(ai) || findWin(human) || randomMove();
      if(win!==null){ board[win] = ai; turn='human'; render(); check(); }
    }
    function findWin(player){
      const wins = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
      for(const line of wins){
        const [a,b,c]=line;
        const vals=[board[a],board[b],board[c]];
        if(vals.filter(v=>v===player).length===2 && vals.includes(null)){
          return line[vals.indexOf(null)];
        }
      }
      return null;
    }
    function randomMove(){
      const empties = board.map((v,i)=>v===null?i:-1).filter(i=>i>=0);
      if(empties.length===0) return null;
      return empties[Math.floor(Math.random()*empties.length)];
    }
    function check(){
      const wins = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
      for(const [a,b,c] of wins){
        if(board[a] && board[a]===board[b] && board[a]===board[c]) { over=true; status.textContent = (board[a]===human?'You win!':'AI wins'); return; }
      }
      if(board.every(x=>x!==null)){ over=true; status.textContent = "Draw"; }
    }
    reset.addEventListener('click', ()=>{ board=Array(9).fill(null); turn='human'; over=false; render(); });
    render();
  })();

  /* ----------------------------
     6. Simon Says (4 buttons)
     ---------------------------- */
  (function(){
    const pads = [0,1,2,3].map(i => document.getElementById('simon-'+i));
    const start = document.getElementById('simon-start');
    const levelEl = document.getElementById('simon-level');
    let seq=[], idx=0, playing=false;

    function light(i){
      const el = pads[i];
      const old = el.style.background;
      el.style.transition='transform .12s';
      el.style.transform='scale(1.06)';
      el.style.filter='brightness(1.4)';
      setTimeout(()=>{ el.style.transform=''; el.style.filter=''; },220);
    }
    function playSeq(){
      playing=true;
      let i=0;
      const t = setInterval(()=> {
        light(seq[i]);
        i++;
        if(i>=seq.length){ clearInterval(t); playing=false; idx=0; }
      },600);
    }
    pads.forEach((p,i)=>{ p.addEventListener('click', ()=>{
      if(playing) return;
      light(i);
      if(seq[idx]===i){ idx++; if(idx===seq.length){ // level passed
        setTimeout(()=>{ nextLevel(); },400);
      }} else { alert('Wrong — try again'); seq=[]; levelEl.textContent='Level: 0'; }
    })});
    function nextLevel(){
      seq.push(Math.floor(Math.random()*4)); levelEl.textContent='Level: '+seq.length; playSeq();
    }
    start.addEventListener('click', ()=>{ seq=[]; nextLevel(); });
  })();

  /* ----------------------------
     7. Pong (canvas)
     ---------------------------- */
  (function(){
    const canvas = document.getElementById('pong-canvas');
    const ctx = canvas.getContext('2d');
    const startBtn = document.getElementById('pong-start');
    const scoreEl = document.getElementById('pong-score');
    let w=canvas.width,h=canvas.height;
    let paddle={x:10,y:h/2-30,w:12,h:60}, aiPaddle={x:w-22,y:h/2-30,w:12,h:60};
    let ball={x:w/2,y:h/2,r:7,vx:5,vy:3}, score=0, anim=null, running=false;

    function draw(){
      ctx.clearRect(0,0,w,h);
      // background grid subtle
      ctx.fillStyle='rgba(255,255,255,0.02)'; for(let i=0;i<w;i+=20) ctx.fillRect(i, h/2-1, 10, 2);
      // paddles
      ctx.fillStyle='#f6c865'; ctx.fillRect(paddle.x,paddle.y,paddle.w,paddle.h);
      ctx.fillStyle='#6be1ff'; ctx.fillRect(aiPaddle.x,aiPaddle.y,aiPaddle.w,aiPaddle.h);
      // ball
      ctx.beginPath(); ctx.fillStyle='#fff'; ctx.arc(ball.x,ball.y,ball.r,0,Math.PI*2); ctx.fill();
    }
    function step(){
      ball.x += ball.vx; ball.y += ball.vy;
      // bounce top/bottom
      if(ball.y<ball.r || ball.y>h-ball.r) ball.vy *= -1;
      // paddle collision
      if(ball.x - ball.r < paddle.x + paddle.w && ball.y>paddle.y && ball.y<paddle.y+paddle.h){
        ball.vx = Math.abs(ball.vx) + 0.6; ball.vx *= -1; score++; scoreEl.textContent=score;
      }
      if(ball.x + ball.r > aiPaddle.x && ball.y>aiPaddle.y && ball.y<aiPaddle.y+aiPaddle.h){
        ball.vx = -Math.abs(ball.vx) - 0.4;
      }
      // ai movement
      const target = ball.y - aiPaddle.h/2;
      aiPaddle.y += (target - aiPaddle.y) * 0.08;

      // lose
      if(ball.x < -50 || ball.x > w+50){ running=false; cancelAnimationFrame(anim); alert('Game over — score: '+score); }
      draw();
      if(running) anim = requestAnimationFrame(step);
    }
    // mouse control
    canvas.addEventListener('mousemove', (e)=> {
      const rect = canvas.getBoundingClientRect();
      const y = e.clientY - rect.top;
      paddle.y = Math.max(0, Math.min(h-paddle.h, y - paddle.h/2));
    });
    startBtn.addEventListener('click', ()=>{
      if(running){ running=false; startBtn.textContent='Start'; cancelAnimationFrame(anim); }
      else {
        // reset
        ball = {x:w/2,y:h/2,r:7,vx:5*(Math.random()<0.5?1:-1),vy:3*(Math.random()<0.5?1:-1)}; score=0; scoreEl.textContent=0;
        running=true; startBtn.textContent='Stop'; step();
      }
    });
    draw();
  })();

  /* ----------------------------
     8. Snake (grid)
     ---------------------------- */
  (function(){
    const canvas = document.getElementById('snake-canvas');
    const ctx = canvas.getContext('2d');
    const startBtn = document.getElementById('snake-start');
    const scoreEl = document.getElementById('snake-score');
    const size = 18; // cell size
    const cols = Math.floor(canvas.width/size), rows = Math.floor(canvas.height/size);
    let snake, dir, food, anim=null, running=false, score=0, speed=120;

    function reset(){
      snake = [{x:Math.floor(cols/2), y:Math.floor(rows/2)}];
      dir = {x:1,y:0}; placeFood(); score=0; scoreEl.textContent=0;
    }
    function placeFood(){
      while(true){
        const f = {x: Math.floor(Math.random()*cols), y: Math.floor(Math.random()*rows)};
        if(!snake.some(s=>s.x===f.x && s.y===f.y)){ food=f; break; }
      }
    }
    function step(){
      const head = {x: snake[0].x + dir.x, y: snake[0].y + dir.y};
      // wall collision = game over
      if(head.x<0 || head.x>=cols || head.y<0 || head.y>=rows || snake.some(s=>s.x===head.x && s.y===head.y)){
        running=false; clearInterval(anim); alert('Game over — score: '+score); return;
      }
      snake.unshift(head);
      if(head.x===food.x && head.y===food.y){ score++; scoreEl.textContent=score; placeFood(); }
      else snake.pop();
      draw();
    }
    function draw(){
      ctx.fillStyle='#061018'; ctx.fillRect(0,0,canvas.width,canvas.height);
      // food
      ctx.fillStyle='#ffd77a'; ctx.fillRect(food.x*size+2, food.y*size+2, size-4, size-4);
      // snake
      ctx.fillStyle='#6be1ff'; snake.forEach((s,i)=> ctx.fillRect(s.x*size+1, s.y*size+1, size-2, size-2));
    }
    document.addEventListener('keydown', e=>{
      if(!running && ['ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.key)) return;
      if(e.key==='ArrowUp' && dir.y!==1) dir={x:0,y:-1};
      if(e.key==='ArrowDown' && dir.y!==-1) dir={x:0,y:1};
      if(e.key==='ArrowLeft' && dir.x!==1) dir={x:-1,y:0};
      if(e.key==='ArrowRight' && dir.x!==-1) dir={x:1,y:0};
    });
    startBtn.addEventListener('click', ()=>{
      if(running){ running=false; startBtn.textContent='Start'; clearInterval(anim); }
      else { reset(); running=true; startBtn.textContent='Stop'; anim = setInterval(step, speed); draw(); }
    });
    reset();
    draw();
  })();

  /* ----------------------------
     9. Sliding puzzle 3x3
     ---------------------------- */
  (function(){
    const boardEl = document.getElementById('slide-board');
    const movesEl = document.getElementById('slide-moves');
    const shuffleBtn = document.getElementById('slide-shuffle');
    let board=[], moves=0;
    function initSolved(){ board=[1,2,3,4,5,6,7,8,null]; render(); moves=0; movesEl.textContent=0; }
    function shuffle(){
      // simple fisher-yates with only solvable shuffles by random swaps
      do {
        board = [1,2,3,4,5,6,7,8,null].sort(()=>Math.random()-0.5);
      } while(!isSolvable(board));
      moves=0; movesEl.textContent=0; render();
    }
    function isSolvable(arr){
      const list = arr.filter(x=>x!==null);
      let inv = 0;
      for(let i=0;i<list.length;i++) for(let j=i+1;j<list.length;j++) if(list[i]>list[j]) inv++;
      return inv%2===0;
    }
    function render(){
      boardEl.innerHTML='';
      board.forEach((v,i)=>{
        const d=document.createElement('div');
        d.style.width='80px'; d.style.height='80px'; d.style.display='flex'; d.style.alignItems='center';
        d.style.justifyContent='center'; d.style.fontSize='24px'; d.style.borderRadius='8px';
        d.style.cursor = v? 'pointer':'default';
        d.style.background = v? 'linear-gradient(90deg,#0b2a30,#062032)':'transparent';
        d.style.color = v? '#eaf7ff':'transparent';
        d.textContent = v || '';
        d.addEventListener('click', ()=>{ if(!v) return; const pos = i; const blank = board.indexOf(null);
          const allowed = [pos-1,pos+1,pos-3,pos+3]; if(allowed.includes(blank) && !(pos%3===0 && blank===pos-1) && !(blank%3===0 && pos===blank-1)){
            board[blank]=v; board[pos]=null; moves++; movesEl.textContent=moves; render();
            if(JSON.stringify(board)===JSON.stringify([1,2,3,4,5,6,7,8,null])) setTimeout(()=>alert('Solved in '+moves+' moves!'),200);
          }
        });
        boardEl.appendChild(d);
      });
    }
    shuffleBtn.addEventListener('click', shuffle);
    initSolved();
  })();

  /* ----------------------------
     10. Dodge (falling blocks)
     ---------------------------- */
  (function(){
    const canvas = document.getElementById('dodge-canvas');
    const ctx = canvas.getContext('2d');
    const startBtn = document.getElementById('dodge-start');
    const scoreEl = document.getElementById('dodge-score');
    let w=canvas.width, h=canvas.height;
    let player = {x: w/2, y: h-18, r:12};
    let blocks=[], anim=null, running=false, score=0, speed=2.0, spawnRate=900;
    function draw(){
      ctx.clearRect(0,0,w,h);
      // player
      ctx.fillStyle='#f6c865'; ctx.beginPath(); ctx.arc(player.x, player.y, player.r,0,Math.PI*2); ctx.fill();
      // blocks
      ctx.fillStyle='#6be1ff'; blocks.forEach(b => ctx.fillRect(b.x,b.y,b.s,b.s));
    }
    function step(){
      // spawn occasionally
      if(Math.random()*1000 < 10) {
        blocks.push({x: Math.random()*(w-24)+12, y:-20, s: 18 + Math.random()*18, vy: speed + Math.random()*2});
      }
      // update
      blocks.forEach(b=> b.y += b.vy);
      // collision
      for(const b of blocks){
        const dx = b.x + b.s/2 - player.x;
        const dy = b.y + b.s/2 - player.y;
        const dist = Math.sqrt(dx*dx + dy*dy);
        if(dist < b.s/2 + player.r - 2){
          running=false; cancelAnimationFrame(anim); alert('You were hit! Score: '+score); return;
        }
      }
      // remove offscreen & increment score
      const remain = [];
      for(const b of blocks){
        if(b.y > h+30){ score++; scoreEl.textContent=score } else remain.push(b);
      }
      blocks = remain;
      // gradually increase difficulty
      speed += 0.0008;
      spawnRate = Math.max(300, spawnRate - 0.02);
      draw();
      if(running) anim = requestAnimationFrame(step);
    }
    document.addEventListener('keydown', e=>{
      if(e.key==='ArrowLeft' || e.key==='a'){ player.x -= 18; player.x = Math.max(12, player.x); }
      if(e.key==='ArrowRight' || e.key==='d'){ player.x += 18; player.x = Math.min(w-12, player.x); }
    });
    // also allow drag on canvas
    let dragging=false;
    canvas.addEventListener('mousedown', ()=>dragging=true); window.addEventListener('mouseup', ()=>dragging=false);
    canvas.addEventListener('mousemove', (e)=>{ if(!dragging) return; const rect=canvas.getBoundingClientRect(); player.x = e.clientX - rect.left; });
    startBtn.addEventListener('click', ()=>{
      if(running){ running=false; startBtn.textContent='Start'; cancelAnimationFrame(anim); }
      else { blocks=[]; player.x=w/2; score=0; speed=2; spawnRate=900; scoreEl.textContent=0; running=true; startBtn.textContent='Stop'; step(); }
    });
    draw();
  })();

})();
</script>
</body>
</html>