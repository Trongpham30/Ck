<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Stock Game - Demo</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lightweight Charts (TradingView) -->
  <script src="https://unpkg.com/lightweight-charts/dist/lightweight-charts.standalone.production.js"></script>
  <style>
    /* small custom tweaks */
    html,body,#app{height:100%}
    .glass{backdrop-filter: blur(6px); background: rgba(255,255,255,0.6)}
  </style>
</head>
<body class="bg-slate-100 font-sans" >
  <div id="app" class="min-h-screen flex flex-col">
    <header class="p-4 bg-white/80 shadow sticky top-0 z-20 flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded bg-gradient-to-br from-indigo-500 to-pink-500 flex items-center justify-center text-white font-bold">SG</div>
        <div>
          <div class="text-lg font-semibold">Stock Game</div>
          <div class="text-sm text-slate-500">Ứng dụng demo - dán vào AppsGeyser</div>
        </div>
      </div>
      <div class="flex items-center gap-4">
        <div id="welcome" class="text-sm text-slate-600"></div>
        <button id="btn-logout" class="hidden px-3 py-1 rounded bg-rose-500 text-white text-sm">Logout</button>
        <button id="btn-show-auth" class="px-3 py-1 rounded bg-indigo-600 text-white text-sm">Đăng nhập / Đăng ký</button>
      </div>
    </header>

    <main class="flex-1 p-4 grid grid-cols-1 lg:grid-cols-3 gap-4">
      <!-- Left: Controls + tickers -->
      <section class="col-span-1 glass p-4 rounded shadow">
        <div class="flex items-center justify-between mb-3">
          <h3 class="font-semibold">Tickers</h3>
          <div>
            <select id="interval" class="px-2 py-1 border rounded text-sm">
              <option value="1">1s</option>
              <option value="5">5s</option>
              <option value="10">10s</option>
              <option value="15">15s</option>
              <option value="30">30s</option>
              <option value="60">1m</option>
            </select>
          </div>
        </div>
        <div id="tickers-list" class="space-y-2 max-h-72 overflow-auto"></div>

        <div class="mt-4">
          <h4 class="font-medium">Mua/Bán nhanh</h4>
          <div class="mt-2 grid grid-cols-2 gap-2">
            <input id="order-qty" type="number" min="1" value="1" class="p-2 border rounded" />
            <select id="order-side" class="p-2 border rounded">
              <option value="buy">Mua</option>
              <option value="sell">Bán</option>
            </select>
            <select id="order-ticker" class="p-2 border rounded col-span-2"></select>
            <button id="btn-order" class="col-span-2 p-2 bg-emerald-500 text-white rounded">Thực hiện</button>
          </div>
        </div>

        <div class="mt-4">
          <h4 class="font-medium">Người chơi</h4>
          <div id="player-stats" class="text-sm text-slate-700 mt-2"></div>
        </div>
      </section>

      <!-- Middle: Chart -->
      <section class="col-span-2 lg:col-span-2 glass p-4 rounded shadow flex flex-col">
        <div class="flex items-center justify-between mb-2">
          <h3 class="font-semibold">Biểu đồ nến</h3>
          <div class="text-sm text-slate-500">Cập nhật động từng giây</div>
        </div>
        <div id="chart" style="height:420px"></div>
        <div class="mt-3 grid grid-cols-3 gap-2">
          <div class="p-3 rounded bg-white shadow-sm">
            <div class="text-xs text-slate-500">Giá hiện tại</div>
            <div id="price-now" class="text-lg font-bold">-</div>
          </div>
          <div class="p-3 rounded bg-white shadow-sm">
            <div class="text-xs text-slate-500">Change</div>
            <div id="price-change" class="text-lg font-bold">-</div>
          </div>
          <div class="p-3 rounded bg-white shadow-sm">
            <div class="text-xs text-slate-500">Volume (sim)</div>
            <div id="price-vol" class="text-lg font-bold">-</div>
          </div>
        </div>
      </section>

      <!-- Right: Orders & Admin -->
      <aside class="col-span-1 glass p-4 rounded shadow">
        <div class="flex items-center justify-between mb-2">
          <h4 class="font-semibold">Orderbook & Lịch sử</h4>
          <button id="btn-open-admin" class="text-xs px-2 py-1 bg-yellow-400 rounded">Admin</button>
        </div>
        <div id="orderbook" class="text-sm max-h-48 overflow-auto"></div>
        <hr class="my-3" />
        <div>
          <h4 class="font-medium">Lịch sử giao dịch</h4>
          <div id="trades" class="text-sm max-h-40 overflow-auto mt-2"></div>
        </div>
      </aside>
    </main>

    <footer class="p-3 text-center text-xs text-slate-500">Demo app — không dùng cho giao dịch thật. Để triển khai an toàn, dùng server + Firebase/Auth hoặc backend có hashing & HTTPS.</footer>
  </div>

  <!-- Auth Modal -->
  <div id="auth-modal" class="fixed inset-0 hidden items-center justify-center bg-black/40 z-50">
    <div class="bg-white rounded p-4 w-full max-w-md">
      <div class="flex justify-between items-center mb-3">
        <h3 class="font-semibold">Đăng nhập / Đăng ký</h3>
        <button id="close-auth" class="text-slate-500">✕</button>
      </div>
      <div class="grid grid-cols-2 gap-3">
        <div>
          <h4 class="font-medium mb-2">Đăng nhập</h4>
          <input id="login-user" placeholder="Username" class="w-full p-2 border rounded mb-2" />
          <input id="login-pass" placeholder="Password" type="password" class="w-full p-2 border rounded mb-2" />
          <button id="btn-login-local" class="w-full p-2 bg-indigo-600 text-white rounded">Login</button>
        </div>
        <div>
          <h4 class="font-medium mb-2">Đăng ký</h4>
          <input id="reg-user" placeholder="Username" class="w-full p-2 border rounded mb-2" />
          <input id="reg-pass" placeholder="Password" type="password" class="w-full p-2 border rounded mb-2" />
          <button id="btn-register" class="w-full p-2 bg-emerald-500 text-white rounded">Register</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Admin Modal -->
  <div id="admin-modal" class="fixed inset-0 hidden items-center justify-center bg-black/40 z-50">
    <div class="bg-white rounded p-4 w-full max-w-2xl">
      <div class="flex justify-between items-center mb-3">
        <h3 class="font-semibold">Admin Dashboard</h3>
        <button id="close-admin" class="text-slate-500">✕</button>
      </div>
      <div class="grid grid-cols-2 gap-4">
        <div>
          <h4 class="font-medium">Quản lý người dùng</h4>
          <div id="admin-users" class="max-h-48 overflow-auto text-sm mt-2"></div>
        </div>
        <div>
          <h4 class="font-medium">Quản lý tickers</h4>
          <div class="flex gap-2">
            <input id="admin-ticker-name" placeholder="Mã VD: ABC" class="p-2 border rounded w-1/2" />
            <input id="admin-ticker-price" placeholder="Start" type="number" class="p-2 border rounded w-1/2" />
          </div>
          <div class="mt-2 flex gap-2">
            <button id="btn-add-ticker" class="px-3 py-1 bg-indigo-600 text-white rounded">Thêm</button>
            <button id="btn-reset-db" class="px-3 py-1 bg-rose-500 text-white rounded">Reset</button>
          </div>
          <div class="text-xs text-slate-500 mt-2">Admin mặc định: username <code>admin</code>, password <code>admin123</code>. Đổi ngay trong bảng Users.</div>
        </div>
      </div>
    </div>
  </div>

  <script>
  // ---------- Simple client-only stock game (single-file) ----------
  // WARNING: This is a frontend-only demo. For real security and persistence,
  // build a backend (Node/FastAPI) with HTTPS and server-side password hashing.

  // Utilities - hashing password via WebCrypto
  async function hashPassword(pw){
    const enc = new TextEncoder();
    const data = enc.encode(pw);
    const hash = await crypto.subtle.digest('SHA-256', data);
    return Array.from(new Uint8Array(hash)).map(b=>b.toString(16).padStart(2,'0')).join('');
  }

  // Local DB helpers
  function loadDB(){
    const json = localStorage.getItem('stockgame-db');
    if(json) return JSON.parse(json);
    const db = {
      users: [],
      tickers: [],
      trades: [],
      orders: [],
      settings: {interval:1}
    };
    // seed admin
    db.users.push({username:'admin', passwordHash:null, cash:100000, isAdmin:true});
    // we'll set admin password hash on first run
    localStorage.setItem('stockgame-db', JSON.stringify(db));
    return db;
  }
  async function ensureAdminPassword(db){
    if(!db.users.find(u=>u.username==='admin').passwordHash){
      const h = await hashPassword('admin123');
      db.users = db.users.map(u=> u.username==='admin'?{...u,passwordHash:h}:u);
      saveDB(db);
    }
  }
  function saveDB(db){ localStorage.setItem('stockgame-db', JSON.stringify(db)); }

  // Simulated market engine
  const db = loadDB();
  let chart = null;
  let series = null;
  let currentTicker = null;
  let candleMap = {}; // ticker -> array of candles
  let players = {}; // username -> user summary cached

  // seed some tickers if empty
  if(db.tickers.length===0){
    db.tickers = [
      {code:'AAPL', price:175, vol:1000, volatility:0.6},
      {code:'MSFT', price:340, vol:800, volatility:0.5},
      {code:'TSLA', price:240, vol:1200, volatility:1.2},
      {code:'VNDS', price:15, vol:2000, volatility:2.5}
    ];
    saveDB(db);
  }

  // UI elements
  const el = id=>document.getElementById(id);
  const intervalSelect = el('interval');
  const tickersList = el('tickers-list');
  const orderTicker = el('order-ticker');
  const priceNow = el('price-now');
  const priceChange = el('price-change');
  const priceVol = el('price-vol');
  const tradesDiv = el('trades');
  const orderbookDiv = el('orderbook');
  const playerStats = el('player-stats');

  // Auth UI
  const authModal = el('auth-modal');
  const adminModal = el('admin-modal');
  const btnShowAuth = el('btn-show-auth');
  const btnLogout = el('btn-logout');
  const welcome = el('welcome');

  let currentUser = null;

  // initialize chart
  function initChart(){
    const chartEl = document.getElementById('chart');
    chartEl.innerHTML = '';
    chart = LightweightCharts.createChart(chartEl, {layout:{textColor:'#222',background:'#ffffff'}, rightPriceScale:{visible:true}});
    series = chart.addCandlestickSeries();
  }

  function renderTickers(){
    tickersList.innerHTML = '';
    orderTicker.innerHTML = '';
    db.tickers.forEach(t=>{
      const div = document.createElement('div');
      div.className = 'flex items-center justify-between p-2 bg-white rounded shadow-sm';
      div.innerHTML = `
        <div>
          <div class="font-medium">${t.code}</div>
          <div class="text-sm text-slate-500">${t.price.toFixed(2)} • vol ${t.vol}</div>
        </div>
        <div class="flex gap-2">
          <button class="px-2 py-1 text-xs bg-slate-100 rounded select-ticker" data-code="${t.code}">Xem</button>
        </div>
      `;
      tickersList.appendChild(div);

      const opt = document.createElement('option'); opt.value = t.code; opt.textContent = t.code; orderTicker.appendChild(opt);
    });

    document.querySelectorAll('.select-ticker').forEach(b=>b.addEventListener('click',()=>{
      selectTicker(b.dataset.code);
    }));
  }

  function selectTicker(code){
    currentTicker = db.tickers.find(x=>x.code===code);
    // prepare candle data if not existing
    if(!candleMap[code]) candleMap[code] = generateInitialCandles(currentTicker);
    series.setData(candleMap[code]);
    updateOverview();
  }

  function generateInitialCandles(t){
    const now = Math.floor(Date.now()/1000);
    const out = [];
    let price = t.price;
    let vol = t.vol;
    for(let i=60;i>0;i--){
      const ts = (now - i);
      const open = price;
      const close = Math.max(0.01, price * (1 + (Math.random()-0.5)*t.volatility/100));
      const high = Math.max(open, close) * (1 + Math.random()*0.002);
      const low = Math.min(open, close) * (1 - Math.random()*0.002);
      out.push({time:ts, open:open, high:high, low:low, close:close});
      price = close;
    }
    return out;
  }

  // market tick - called every second
  function marketTick(){
    const iv = Number(intervalSelect.value);
    // update each ticker price a bit
    db.tickers.forEach(t=>{
      // simple random walk
      const changePct = (Math.random()-0.5) * t.volatility/100;
      t.price = Math.max(0.01, t.price*(1+changePct));
      t.vol = Math.max(1, Math.round(t.vol*(1 + (Math.random()-0.5)*0.05)));
      // push a new raw tick into candleMap (we collapse client-side by second/min)
      const now = Math.floor(Date.now()/1000);
      if(!candleMap[t.code]) candleMap[t.code] = generateInitialCandles(t);
      const arr = candleMap[t.code];
      const last = arr[arr.length-1];
      // if last.time == now then update it
      if(last && last.time === now){
        // expand
        last.close = t.price;
        last.high = Math.max(last.high,t.price);
        last.low = Math.min(last.low,t.price);
      } else {
        arr.push({time:now, open:last?last.close:t.price, high:t.price, low:t.price, close:t.price});
        // keep length
        if(arr.length>500) arr.shift();
      }
    });

    // refresh UI for currentTicker
    if(currentTicker){
      const arr = candleMap[currentTicker.code];
      // to emulate candle interval (1s/5s/...) we will downsample
      const candleData = downsampleCandles(arr, Number(intervalSelect.value));
      series.setData(candleData);
      const last = candleData[candleData.length-1];
      priceNow.textContent = last.close.toFixed(2);
      const first = candleData[0];
      priceChange.textContent = ((last.close - candleData[0].open)/candleData[0].open*100).toFixed(2)+'%';
      priceVol.textContent = currentTicker.vol;
    }

    renderPlayers();
    renderOrderbook();
  }

  function downsampleCandles(arr, seconds){
    // group by floor(time/seconds)
    const map = new Map();
    arr.forEach(c=>{
      const k = Math.floor(c.time/seconds)*seconds;
      if(!map.has(k)) map.set(k, {time:k, open:c.open, high:c.high, low:c.low, close:c.close});
      else{
        const o = map.get(k);
        o.high = Math.max(o.high, c.high);
        o.low = Math.min(o.low, c.low);
        o.close = c.close;
      }
    });
    return Array.from(map.values()).slice(-200);
  }

  // Orders & trades (very simple immediate execution)
  function placeOrder(username, ticker, side, qty){
    const user = db.users.find(u=>u.username===username);
    if(!user) return false;
    const t = db.tickers.find(x=>x.code===ticker);
    if(!t) return false;
    const price = t.price;
    if(side==='buy'){
      const cost = price*qty;
      if(user.cash < cost) return false;
      user.cash -= cost;
      user.holdings = user.holdings || {};
      user.holdings[ticker] = (user.holdings[ticker]||0) + qty;
    } else {
      user.holdings = user.holdings || {};
      const have = user.holdings[ticker]||0;
      if(have < qty) return false;
      user.holdings[ticker] = have - qty;
      user.cash += price*qty;
    }
    db.trades.unshift({time:Date.now(),user:username,ticker,side,qty,price});
    if(db.trades.length>200) db.trades.pop();
    saveDB(db);
    return true;
  }

  // UI render players
  function renderPlayers(){
    const rows = db.users.map(u=>{
      const totalHold = Object.entries(u.holdings||{}).reduce((s,[k,q])=>{
        const t = db.tickers.find(x=>x.code===k); return s + (t? t.price*q:0);
      },0);
      return `<div class=\"p-2 bg-white rounded my-1\"><div class=\"flex justify-between\"><div><b>${u.username}</b> ${u.isAdmin?'<span class=\"text-xs text-yellow-600\">(ADMIN)</span>':''}</div><div>${(u.cash+totalHold).toFixed(2)} USD</div></div></div>`;
    }).join('');
    playerStats.innerHTML = rows;
  }

  function renderOrderbook(){
    orderbookDiv.innerHTML = db.tickers.map(t=>`<div class=\"p-2 bg-white rounded my-1\"><div class=\"flex justify-between\"><div>${t.code}</div><div>${t.price.toFixed(2)}</div></div></div>`).join('');
    tradesDiv.innerHTML = db.trades.map(tr=>`<div class=\"p-1 text-xs\">${new Date(tr.time).toLocaleTimeString()} - ${tr.user} ${tr.side} ${tr.qty} ${tr.ticker} @ ${tr.price.toFixed(2)}</div>`).join('');
  }

  // Auth logic
  async function registerUser(username,password){
    if(db.users.find(u=>u.username===username)) return {ok:false,msg:'Username tồn tại'};
    const h = await hashPassword(password);
    db.users.push({username,passwordHash:h,cash:10000,holdings:{},isAdmin:false});
    saveDB(db);
    return {ok:true};
  }
  async function loginUser(username,password){
    const user = db.users.find(u=>u.username===username);
    if(!user) return {ok:false,msg:'Không tìm thấy user'};
    const h = await hashPassword(password);
    if(h !== user.passwordHash) return {ok:false,msg:'Sai mật khẩu'};
    currentUser = user;
    el('btn-logout').classList.remove('hidden');
    el('btn-show-auth').classList.add('hidden');
    welcome.textContent = 'Xin chào, '+currentUser.username;
    return {ok:true};
  }

  // Admin functions
  function listAdminUsers(){
    return db.users.map(u=>`<div class=\"p-2 bg-slate-50 rounded my-1 flex justify-between items-center\"><div><b>${u.username}</b> - ${u.cash} USD ${u.isAdmin?'<span class=\"text-xs text-yellow-600\">(ADMIN)</span>':''}</div><div><button data-user=\"${u.username}\" class=\"btn-set-admin text-xs px-2 py-1 bg-slate-200 rounded\">Toggle Admin</button> <button data-user=\"${u.username}\" class=\"btn-del-user text-xs px-2 py-1 bg-rose-200 rounded\">Xóa</button></div></div>`).join('');
  }

  function bindAdminActions(){
    document.querySelectorAll('.btn-set-admin').forEach(btn=>btn.addEventListener('click',e=>{
      const u = e.currentTarget.dataset.user;
      const user = db.users.find(x=>x.username===u);
      user.isAdmin = !user.isAdmin; saveDB(db); renderAdminUsers();
    }));
    document.querySelectorAll('.btn-del-user').forEach(btn=>btn.addEventListener('click',e=>{
      const u = e.currentTarget.dataset.user;
      if(u==='admin'){ alert('Không xóa admin mặc định'); return; }
      const idx = db.users.findIndex(x=>x.username===u); if(idx>=0) db.users.splice(idx,1); saveDB(db); renderAdminUsers();
    }));
  }

  function renderAdminUsers(){
    el('admin-users').innerHTML = listAdminUsers(); bindAdminActions();
  }

  // UI events
  el('btn-order').addEventListener('click',()=>{
    if(!currentUser){ alert('Vui lòng đăng nhập'); return; }
    const qty = Number(el('order-qty').value)||1;
    const ticker = el('order-ticker').value;
    const side = el('order-side').value;
    const ok = placeOrder(currentUser.username,ticker,side,qty);
    if(!ok) alert('Giao dịch thất bại (kiểm tra vốn / số lượng)'); else alert('Giao dịch thành công');
  });

  // Auth modal toggles
  btnShowAuth.addEventListener('click',()=>{ authModal.classList.remove('hidden'); authModal.style.display='flex'; });
  el('close-auth').addEventListener('click',()=>{ authModal.classList.add('hidden'); authModal.style.display='none'; });
  el('btn-login-local').addEventListener('click', async ()=>{
    const u = el('login-user').value.trim(); const p = el('login-pass').value;
    const r = await loginUser(u,p);
    if(!r.ok) alert(r.msg); else { authModal.classList.add('hidden'); authModal.style.display='none'; renderPlayers(); }
  });
  el('btn-register').addEventListener('click', async ()=>{
    const u = el('reg-user').value.trim(); const p = el('reg-pass').value;
    if(!u||!p){ alert('Điền đủ thông tin'); return; }
    const r = await registerUser(u,p);
    if(!r.ok) alert(r.msg); else { alert('Đăng ký thành công. Bạn đã có: 10,000 USD'); }
  });
  el('btn-logout').addEventListener('click',()=>{ currentUser=null; el('btn-logout').classList.add('hidden'); el('btn-show-auth').classList.remove('hidden'); welcome.textContent=''; });

  // Admin modal
  el('btn-open-admin').addEventListener('click',()=>{
    // require admin
    if(!currentUser || !currentUser.isAdmin){ alert('Chỉ admin mới vào được'); return; }
    adminModal.classList.remove('hidden'); adminModal.style.display='flex'; renderAdminUsers();
  });
  el('close-admin').addEventListener('click',()=>{ adminModal.classList.add('hidden'); adminModal.style.display='none'; });
  el('btn-add-ticker').addEventListener('click',()=>{
    const code = el('admin-ticker-name').value.trim().toUpperCase();
    const start = Number(el('admin-ticker-price').value)||1;
    if(!code) return alert('Nhập mã');
    db.tickers.push({code, price:start, vol:500, volatility:1.0}); saveDB(db); renderTickers(); renderAdminUsers();
  });
  el('btn-reset-db').addEventListener('click',()=>{
    if(!confirm('Reset toàn bộ DB local?')) return;
    localStorage.removeItem('stockgame-db'); location.reload();
  });

  // Admin shortcut: auto-login admin (user asked "admin đăng nhập luôn")
  (async ()=>{
    await ensureAdminPassword(db);
    // auto-login admin so owner can manage immediately
    const adminUser = db.users.find(u=>u.username==='admin');
    currentUser = adminUser;
    welcome.textContent = 'Xin chào, admin';
    el('btn-logout').classList.remove('hidden');
    el('btn-show-auth').classList.add('hidden');
  })();

  // initialize
  initChart(); renderTickers(); selectTicker(db.tickers[0].code);
  renderPlayers(); renderOrderbook();
  setInterval(marketTick, 1000);

  // provide basic keyboard shortcuts for demo
  window.addEventListener('keydown', (e)=>{
    if(e.key==='/' && !authModal.classList.contains('hidden')){ el('login-user').focus(); }
  });

  </script>
</body>
</html>
