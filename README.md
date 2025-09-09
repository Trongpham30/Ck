<!--
Stock Game - single-file web app (index.html)
Designed to be used inside a WebView-based app builder like AppsGeyser (Website app) or hosted as a static page.
Features:
 - Player register / login (localStorage)
 - Admin panel protected by PIN (localStorage default 1234)
 - Add / remove tickers (admin)
 - Simulated market engine that updates prices every second and forms 1-second "candles"
 - Buy / Sell market orders (immediate fill at current price)
 - Stop-loss and Limit orders (checked every tick)
 - Simple chart (canvas) drawing candles
 - Avatars (image upload stored as dataURL)
 - Leaderboard, balances, portfolio

How to use:
 - Open this file as index.html in a browser or upload to a website and give that URL to AppsGeyser when creating a "Website" app.
 - Admin default PIN: 1234 (change in Admin panel -> Settings -> Admin PIN)
 - The app stores everything in localStorage; to reset, clear site storage.

Note: This app is a game/simulation only. No real money or real market connectivity.
-->

<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Stock Game - AppsGeyser</title>
  <!-- Tailwind CDN for quick styling -->
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    /* Small custom styles */
    body { background: linear-gradient(180deg,#0f172a,#020617); color:#e6eef8 }
    .glass { background: rgba(255,255,255,0.03); backdrop-filter: blur(6px); }
    canvas { width: 100%; height: 250px; }
    .ticker-pill { font-family: ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto; }
  </style>
</head>
<body class="min-h-screen p-4">
  <div class="max-w-5xl mx-auto">
    <header class="flex items-center justify-between mb-4">
      <h1 class="text-2xl font-semibold">Stock Game (AppsGeyser)</h1>
      <div id="auth-area" class="flex items-center gap-3"></div>
    </header>

    <main class="grid grid-cols-1 lg:grid-cols-3 gap-4">
      <section class="lg:col-span-2 glass p-4 rounded shadow">
        <div class="flex items-start gap-4">
          <div class="w-2/3">
            <div class="flex items-center justify-between mb-2">
              <h2 class="text-lg font-medium">Market</h2>
              <div class="flex items-center gap-2">
                <select id="ticker-select" class="rounded px-2 py-1 bg-slate-800 text-white"></select>
                <button id="admin-open" class="px-2 py-1 rounded bg-yellow-500 text-black">Admin</button>
              </div>
            </div>
            <canvas id="chart"></canvas>
            <div class="mt-2 flex items-center gap-4">
              <div class="text-sm">Price: <span id="cur-price" class="font-semibold">-</span></div>
              <div class="text-sm">Change: <span id="cur-change">-</span></div>
              <div class="text-sm">Volume (sim): <span id="cur-vol">-</span></div>
            </div>
          </div>

          <div class="w-1/3">
            <div class="mb-2 text-sm">Place Order</div>
            <div class="flex flex-col gap-2">
              <select id="side" class="px-2 py-1 rounded bg-slate-800 text-white">
                <option value="buy">Buy</option>
                <option value="sell">Sell</option>
              </select>
              <input id="qty" type="number" min="1" value="1" class="px-2 py-1 rounded bg-slate-800 text-white" />
              <label class="text-xs">Stop-Loss (optional)</label>
              <input id="stopPrice" type="number" step="0.01" class="px-2 py-1 rounded bg-slate-800 text-white" placeholder="e.g. 95.50" />
              <label class="text-xs">Limit Price (optional)</label>
              <input id="limitPrice" type="number" step="0.01" class="px-2 py-1 rounded bg-slate-800 text-white" />
              <button id="place-order" class="px-3 py-1 rounded bg-indigo-600">Place</button>
            </div>
          </div>
        </div>

        <hr class="my-3 border-slate-700" />
        <div class="grid grid-cols-2 gap-4">
          <div>
            <h3 class="font-medium">Order Book (simulated)</h3>
            <div id="orderbook" class="text-sm mt-2 bg-slate-900 p-2 rounded h-44 overflow-auto"></div>
          </div>
          <div>
            <h3 class="font-medium">Trades</h3>
            <div id="trades" class="text-sm mt-2 bg-slate-900 p-2 rounded h-44 overflow-auto"></div>
          </div>
        </div>
      </section>

      <aside class="glass p-4 rounded shadow">
        <div id="profile-area"></div>
        <hr class="my-3 border-slate-700" />
        <div>
          <h3 class="font-medium">Portfolio</h3>
          <div id="portfolio" class="text-sm mt-2"></div>
        </div>
        <hr class="my-3 border-slate-700" />
        <div>
          <h3 class="font-medium">Leaderboard</h3>
          <div id="leaderboard" class="text-sm mt-2"></div>
        </div>
      </aside>
    </main>

    <!-- Admin modal hidden by default -->
    <div id="admin-modal" class="fixed inset-0 flex items-center justify-center bg-black/60 p-4 hidden">
      <div class="bg-slate-800 p-4 rounded w-full max-w-2xl">
        <div class="flex justify-between items-center mb-3">
          <h2 class="text-lg font-medium">Admin Panel</h2>
          <button id="admin-close" class="px-2 py-1 rounded bg-red-500">Close</button>
        </div>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <h4 class="font-semibold">Tickers</h4>
            <div class="mt-2 flex gap-2">
              <input id="new-code" placeholder="CODE" class="px-2 py-1 rounded bg-slate-900" />
              <input id="new-name" placeholder="Name" class="px-2 py-1 rounded bg-slate-900" />
              <input id="new-price" type="number" placeholder="Start Price" class="px-2 py-1 rounded bg-slate-900" />
              <button id="add-ticker" class="px-2 py-1 rounded bg-green-600">Add</button>
            </div>
            <div id="ticker-list" class="mt-2 text-sm bg-slate-900 p-2 rounded h-36 overflow-auto"></div>
          </div>

          <div>
            <h4 class="font-semibold">Settings</h4>
            <div class="mt-2">
              <label class="text-sm">Admin PIN</label>
              <input id="admin-pin" class="px-2 py-1 rounded bg-slate-900 w-full mt-1" />
              <button id="save-pin" class="mt-2 px-2 py-1 rounded bg-indigo-600">Save PIN</button>
            </div>
            <div class="mt-4">
              <label class="text-sm">Simulation speed (ms per tick)</label>
              <input id="sim-speed" type="number" min="200" class="px-2 py-1 rounded bg-slate-900 w-full mt-1" />
            </div>
            <div class="mt-4">
              <button id="reset-game" class="px-2 py-1 rounded bg-red-600">Reset Game (clears localStorage)</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Simple login/register modal -->
    <div id="auth-modal" class="fixed inset-0 flex items-center justify-center bg-black/60 p-4 hidden">
      <div class="bg-slate-800 p-4 rounded w-full max-w-md">
        <h3 class="text-lg font-medium mb-2">Login / Register</h3>
        <div class="flex flex-col gap-2">
          <input id="username" placeholder="Username" class="px-2 py-1 rounded bg-slate-900" />
          <input id="avatar-file" type="file" accept="image/*" class="text-sm" />
          <div class="flex gap-2">
            <button id="btn-login" class="px-2 py-1 rounded bg-green-600">Login / Register</button>
            <button id="btn-guest" class="px-2 py-1 rounded bg-gray-600">Guest</button>
          </div>
        </div>
      </div>
    </div>

  </div>

  <script>
    /***** Simple storage helpers *****/
    const DB = {
      load(){ return JSON.parse(localStorage.getItem('sg-db')||'null') },
      save(state){ localStorage.setItem('sg-db', JSON.stringify(state)) }
    }

    function ensureState(){
      let s = DB.load();
      if(!s){
        s = {
          adminPin: '1234',
          simSpeed: 1000,
          tickers: {
            BTC: { code:'BTC', name:'BitCoin-like', price: 100, candles: [] },
            ABC: { code:'ABC', name:'Alpha Corp', price: 50, candles: [] }
          },
          users: {},
          orders: [],
          trades: []
        }
        DB.save(s)
      }
      return s
    }

    let state = ensureState();
    let currentUser = null; // username
    let chartCanvas = document.getElementById('chart');
    let chartCtx = chartCanvas.getContext('2d');
    let selectedTicker = null;

    /***** UI wiring *****/
    const authArea = document.getElementById('auth-area');
    const profileArea = document.getElementById('profile-area');

    function renderAuth(){
      authArea.innerHTML = '';
      if(!currentUser){
        const btn = document.createElement('button'); btn.textContent='Login'; btn.className='px-3 py-1 rounded bg-green-600';
        btn.onclick = ()=>openAuth();
        authArea.appendChild(btn);
      } else {
        const user = state.users[currentUser];
        const el = document.createElement('div'); el.className='flex items-center gap-2';
        const img = document.createElement('img'); img.src = user.avatar || 'https://via.placeholder.com/40?text=U'; img.className='w-8 h-8 rounded-full';
        el.appendChild(img);
        const name = document.createElement('div'); name.textContent = currentUser; el.appendChild(name);
        const logout = document.createElement('button'); logout.textContent='Logout'; logout.className='px-2 py-1 rounded bg-red-600';
        logout.onclick = ()=>{ currentUser=null; renderAuth(); renderProfile(); };
        el.appendChild(logout);
        authArea.appendChild(el);
      }
    }

    function renderProfile(){
      profileArea.innerHTML = '';
      if(!currentUser){
        const btn = document.createElement('button'); btn.textContent='Login / Create account'; btn.className='w-full px-3 py-2 rounded bg-indigo-600';
        btn.onclick = openAuth; profileArea.appendChild(btn);
        return;
      }
      const u = state.users[currentUser];
      profileArea.innerHTML = `
        <div class="flex items-center gap-3">
          <img src="${u.avatar||'https://via.placeholder.com/80'}" class="w-16 h-16 rounded-full" />
          <div>
            <div class="font-semibold">${currentUser}</div>
            <div class="text-sm">Cash: $<span id="cash">${u.cash.toFixed(2)}</span></div>
          </div>
        </div>
      `;
      const depositBtn = document.createElement('button'); depositBtn.className='mt-3 px-2 py-1 rounded bg-green-600'; depositBtn.textContent='Add $1000';
      depositBtn.onclick = ()=>{ u.cash += 1000; saveState(); renderProfile(); renderPortfolio(); renderLeaderboard(); }
      profileArea.appendChild(depositBtn);
    }

    function openAuth(){ document.getElementById('auth-modal').classList.remove('hidden') }
    function closeAuth(){ document.getElementById('auth-modal').classList.add('hidden') }

    document.getElementById('btn-login').onclick = async ()=>{
      const name = document.getElementById('username').value.trim();
      if(!name){ alert('Enter username'); return }
      const file = document.getElementById('avatar-file').files[0];
      let avatar = null;
      if(file){ avatar = await readFileAsDataURL(file) }
      if(!state.users[name]){ state.users[name] = { avatar, cash:10000, holdings: {} } }
      else if(avatar) state.users[name].avatar = avatar
      currentUser = name; saveState(); closeAuth(); renderAuth(); renderProfile(); renderPortfolio(); renderLeaderboard();
    }
    document.getElementById('btn-guest').onclick = ()=>{
      const name = 'Guest'+Math.floor(Math.random()*9000+1000);
      state.users[name] = { avatar:null, cash:5000, holdings: {} };
      currentUser = name; saveState(); closeAuth(); renderAuth(); renderProfile(); renderPortfolio(); renderLeaderboard();
    }

    function readFileAsDataURL(f){ return new Promise(res=>{ const r=new FileReader(); r.onload=e=>res(e.target.result); r.readAsDataURL(f); }) }

    /***** Admin UI wiring *****/
    document.getElementById('admin-open').onclick = ()=>{
      const pin = prompt('Enter Admin PIN:');
      if(pin === state.adminPin){ showAdmin() } else alert('Wrong PIN')
    }
    function showAdmin(){
      document.getElementById('admin-modal').classList.remove('hidden');
      document.getElementById('admin-pin').value = state.adminPin;
      document.getElementById('sim-speed').value = state.simSpeed;
      renderTickerList();
    }
    document.getElementById('admin-close').onclick = ()=>{ document.getElementById('admin-modal').classList.add('hidden') }
    document.getElementById('add-ticker').onclick = ()=>{
      const code = document.getElementById('new-code').value.trim().toUpperCase();
      const name = document.getElementById('new-name').value.trim();
      const price = parseFloat(document.getElementById('new-price').value)||10;
      if(!code) return alert('Enter CODE');
      state.tickers[code] = { code, name: name||code, price, candles: [] };
      saveState(); renderTickerSelect(); renderTickerList();
    }
    document.getElementById('save-pin').onclick = ()=>{
      const p = document.getElementById('admin-pin').value.trim(); if(!p) return alert('PIN cannot be empty'); state.adminPin = p; saveState(); alert('Saved') }
    document.getElementById('reset-game').onclick = ()=>{ if(confirm('Clear all data?')){ localStorage.removeItem('sg-db'); location.reload(); } }
    document.getElementById('sim-speed').onchange = (e)=>{ state.simSpeed = parseInt(e.target.value)||1000; saveState(); }

    function renderTickerList(){
      const out = document.getElementById('ticker-list'); out.innerHTML='';
      Object.values(state.tickers).forEach(t=>{
        const div = document.createElement('div'); div.className='flex items-center justify-between p-1';
        div.innerHTML = `<div>${t.code} — ${t.name} <span class="text-xs">$${t.price.toFixed(2)}</span></div>`;
        const del = document.createElement('button'); del.textContent='Delete'; del.className='px-2 py-0.5 rounded bg-red-600 text-xs';
        del.onclick = ()=>{ delete state.tickers[t.code]; saveState(); renderTickerSelect(); renderTickerList(); }
        div.appendChild(del); out.appendChild(div);
      })
    }

    /***** Market engine (simulation) *****/
    function saveState(){ DB.save(state) }

    function initTickerSelect(){
      renderTickerSelect();
      document.getElementById('ticker-select').onchange = (e)=>{ selectTicker(e.target.value) };
      // initial selection
      const first = Object.keys(state.tickers)[0]; if(first) selectTicker(first)
    }

    function renderTickerSelect(){
      const sel = document.getElementById('ticker-select'); sel.innerHTML='';
      Object.values(state.tickers).forEach(t=>{ const opt = document.createElement('option'); opt.value=t.code; opt.textContent = t.code+' — '+t.name; sel.appendChild(opt) });
    }

    function selectTicker(code){ selectedTicker = state.tickers[code]; renderChart(); renderMarketInfo(); renderPortfolio(); }

    function marketTick(){
      // For each ticker, create a new price by random walk and append candle
      Object.values(state.tickers).forEach(t=>{
        // random percent change small
        const vol = Math.max(0.1, Math.abs(t.price * (Math.random()-0.5)*0.02));
        const change = (Math.random()-0.5)*0.02*t.price;
        const newPrice = Math.max(0.01, t.price + change);
        const candle = { t: Date.now(), open: t.price, high: Math.max(t.price, newPrice), low: Math.min(t.price, newPrice), close: newPrice, vol: Math.floor(Math.random()*1000) };
        t.candles.push(candle);
        t.price = newPrice;
      });

      // process orders (market orders executed immediately at ticker.price; check stop/limit orders)
      processOrders();

      // append trades simulated
      saveState();
      renderMarketInfo(); renderChart(); renderOrderbook(); renderTrades(); renderPortfolio(); renderLeaderboard();
    }

    function processOrders(){
      const now = Date.now();
      const remaining = [];
      state.orders.forEach(o=>{
        const t = state.tickers[o.ticker];
        if(!t) return; // ignore
        // check stop/limit logic
        let execute = false;
        let price = t.price;
        if(o.type==='market') execute = true;
        if(o.stopPrice){
          if(o.side==='sell' && t.price<=o.stopPrice) execute = true;
          if(o.side==='buy' && t.price>=o.stopPrice) execute = true;
        }
        if(o.limitPrice){
          if(o.side==='buy' && t.price<=o.limitPrice) execute = true;
          if(o.side==='sell' && t.price>=o.limitPrice) execute = true;
        }
        if(execute){
          // fill the order: transfer cash and holdings
          fillOrder(o, price);
          state.trades.unshift({ time: now, user: o.user, ticker: o.ticker, side: o.side, price, qty: o.qty });
        } else {
          remaining.push(o);
        }
      });
      state.orders = remaining;
    }

    function fillOrder(o, price){
      const user = state.users[o.user];
      if(!user) return;
      const cost = price * o.qty;
      if(o.side==='buy'){
        if(user.cash >= cost){ user.cash -= cost; user.holdings[o.ticker] = (user.holdings[o.ticker]||0)+o.qty; }
        else { /* insufficient: ignore */ }
      } else {
        const have = user.holdings[o.ticker]||0;
        if(have >= o.qty){ user.holdings[o.ticker] -= o.qty; user.cash += cost; }
      }
      saveState();
    }

    // place order from UI
    document.getElementById('place-order').onclick = ()=>{
      if(!currentUser) return alert('Login first');
      const side = document.getElementById('side').value;
      const qty = parseFloat(document.getElementById('qty').value)||1;
      const stopPrice = parseFloat(document.getElementById('stopPrice').value)||null;
      const limitPrice = parseFloat(document.getElementById('limitPrice').value)||null;
      const t = selectedTicker.code;
      const order = { user: currentUser, side, qty, ticker: t, stopPrice: stopPrice||null, limitPrice: limitPrice||null, type: (limitPrice||stopPrice)?'conditional':'market', created: Date.now() };
      state.orders.push(order); saveState(); renderOrderbook();
      alert('Order placed');
    }

    function renderOrderbook(){
      const ob = document.getElementById('orderbook'); ob.innerHTML = '';
      state.orders.slice().reverse().forEach(o=>{
        const d = document.createElement('div'); d.className='p-1 border-b border-slate-700'; d.textContent = `${o.ticker} ${o.side.toUpperCase()} ${o.qty} by ${o.user} ${o.limitPrice?('L:'+o.limitPrice):''} ${o.stopPrice?('S:'+o.stopPrice):''}`;
        ob.appendChild(d);
      })
    }

    function renderTrades(){
      const tr = document.getElementById('trades'); tr.innerHTML='';
      state.trades.slice(0,200).forEach(t=>{ const d=document.createElement('div'); d.className='p-1 border-b border-slate-700'; d.textContent = `${new Date(t.time).toLocaleTimeString()} ${t.ticker} ${t.side} ${t.qty}@${t.price.toFixed(2)} by ${t.user}`; tr.appendChild(d) })
    }

    function renderMarketInfo(){
      if(!selectedTicker) return;
      document.getElementById('cur-price').textContent = '$'+selectedTicker.price.toFixed(2);
      const c = selectedTicker.candles[selectedTicker.candles.length-1];
      if(c){ const change = (c.close-c.open)/c.open*100; document.getElementById('cur-change').textContent = change.toFixed(2)+'%'; document.getElementById('cur-vol').textContent = c.vol; }
    }

    /***** Chart drawing (simple candles) *****/
    function renderChart(){
      const w = chartCanvas.width = chartCanvas.clientWidth*devicePixelRatio;
      const h = chartCanvas.height = chartCanvas.clientHeight*devicePixelRatio;
      chartCtx.clearRect(0,0,w,h);
      if(!selectedTicker) return;
      const candles = selectedTicker.candles.slice(-80);
      if(candles.length===0) return;
      const prices = candles.flatMap(c=>[c.high,c.low]);
      const max = Math.max(...prices); const min = Math.min(...prices);
      const pad = (max-min)*0.1 || 1;
      const xStep = w / candles.length;
      candles.forEach((c,i)=>{
        const x = i * xStep + xStep/2;
        const yOpen = h - ((c.open - min + pad) / (max - min + 2*pad) * h);
        const yClose = h - ((c.close - min + pad) / (max - min + 2*pad) * h);
        const yHigh = h - ((c.high - min + pad) / (max - min + 2*pad) * h);
        const yLow = h - ((c.low - min + pad) / (max - min + 2*pad) * h);
        // wick
        chartCtx.beginPath(); chartCtx.moveTo(x, yHigh); chartCtx.lineTo(x, yLow); chartCtx.lineWidth = 1*devicePixelRatio; chartCtx.strokeStyle = '#aaa'; chartCtx.stroke();
        // body
        const bodyTop = Math.min(yOpen,yClose); const bodyH = Math.max(2, Math.abs(yOpen-yClose));
        chartCtx.fillStyle = c.close >= c.open ? '#06b6d4' : '#ef4444';
        chartCtx.fillRect(x - xStep*0.25, bodyTop, xStep*0.5, bodyH);
      })
    }

    /***** Portfolio & leaderboard *****/
    function renderPortfolio(){
      const out = document.getElementById('portfolio'); out.innerHTML='';
      if(!currentUser) return out.innerHTML='<div class="text-sm">Login to see portfolio</div>';
      const u = state.users[currentUser];
      const rows = [];
      let total = u.cash;
      for(const [code,qty] of Object.entries(u.holdings||{})){
        const price = state.tickers[code]?.price || 0;
        const v = price*qty; total += v;
        rows.push(`<div class="flex justify-between"><div>${code} x ${qty}</div><div>$${v.toFixed(2)}</div></div>`)
      }
      out.innerHTML = `<div class="text-sm">Cash: $${u.cash.toFixed(2)}</div>` + rows.join('') + `<hr class="my-2"> <div class="font-semibold">Total: $${total.toFixed(2)}</div>`;
    }

    function renderLeaderboard(){
      const out = document.getElementById('leaderboard'); out.innerHTML='';
      const arr = Object.entries(state.users).map(([name,u])=>{
        let total = u.cash; for(const [code,qty] of Object.entries(u.holdings||{})){ const price = state.tickers[code]?.price||0; total+=price*qty }
        return { name, total }
      }).sort((a,b)=>b.total-a.total);
      out.innerHTML = arr.slice(0,10).map(x=>`<div class="flex justify-between"><div>${x.name}</div><div>$${x.total.toFixed(2)}</div></div>`).join('');
    }

    /***** Init & loop *****/
    initTickerSelect(); renderAuth(); renderProfile(); renderPortfolio(); renderLeaderboard(); renderOrderbook(); renderTrades();

    // simulation loop
    let simHandle = null;
    function startSim(){ if(simHandle) clearInterval(simHandle); simHandle = setInterval(marketTick, state.simSpeed||1000); }
    startSim();

    // small resize handler
    window.addEventListener('resize', ()=>{ renderChart() })

    // restore from storage on load
    window.addEventListener('load', ()=>{ state = ensureState(); initTickerSelect(); renderAuth(); renderProfile(); renderPortfolio(); renderLeaderboard(); renderOrderbook(); renderTrades(); renderChart(); })

  </script>
</body>
</html>
