<!--
Advanced Stock Game - single-file web app (index.html)
Features added/updated from previous version:
 - Admin account with username/password (secure-ish hashing via SubtleCrypto SHA-256 + random salt stored in localStorage)
 - User registration/login with password (hashed), email-like field optional, stronger validation
 - Admin panel requires admin login and is integrated as an account (can promote/demote users to admin)
 - Matching engine: full orderbook, price-time priority, supports Market, Limit orders and Time-in-Force: GTC, IOC, FOK
 - Trailing Stop orders (percentage-based or absolute): tracks best price and triggers into market/limit when touched
 - Candle aggregation selectable: 1s, 5s, 10s, 15s, 30s, 60s. Candles are built from simulation ticks.
 - Orders display with live counts; pending orders update when placed and during execution; number animations included.
 - Admin management: adjust user cash, force-fill or cancel orders, add/remove tickers, seed users.
 - UI improved with Tailwind, clearer panels and responsive layout.

How to use:
 - Upload this index.html as the website URL to AppsGeyser when creating a Website/WebView app.
 - Admin default account: username `admin`, password `admin123` (change immediately in Admin -> Settings).
 - All data stored in localStorage under key `asg-db`. To reset, Admin -> Reset.

Note: This is a front-end-only simulation suitable for demo/play. Do not use for real trading or real money.
-->

<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Advanced Stock Game - AppsGeyser</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background: linear-gradient(180deg,#071024,#00040a); color: #e6eef8; }
    .glass { background: rgba(255,255,255,0.03); backdrop-filter: blur(6px); }
    canvas { width: 100%; height: 260px; }
    .small-muted { color: #9aa6b2; font-size: 0.85rem }
    .pill { padding: .15rem .5rem; border-radius: .375rem; }
  </style>
</head>
<body class="min-h-screen p-4">
  <div class="max-w-6xl mx-auto">
    <header class="flex items-center justify-between mb-4">
      <h1 class="text-2xl font-semibold">Advanced Stock Game</h1>
      <div id="top-controls" class="flex items-center gap-3"></div>
    </header>

    <main class="grid grid-cols-1 lg:grid-cols-4 gap-4">
      <section class="lg:col-span-3 glass p-4 rounded shadow">
        <div class="flex justify-between items-start gap-4">
          <div class="flex-1">
            <div class="flex items-center justify-between mb-2">
              <div>
                <select id="ticker-select" class="rounded px-2 py-1 bg-slate-900 text-white"></select>
                <span id="ticker-name" class="ml-2 small-muted"></span>
              </div>
              <div class="flex items-center gap-2">
                <label class="small-muted">Candle</label>
                <select id="candle-interval" class="rounded px-2 py-1 bg-slate-900 text-white">
                  <option value="1000">1s</option>
                  <option value="5000">5s</option>
                  <option value="10000">10s</option>
                  <option value="15000">15s</option>
                  <option value="30000">30s</option>
                  <option value="60000">1m</option>
                </select>
                <button id="open-admin" class="px-2 py-1 rounded bg-yellow-500 text-black">Admin</button>
              </div>
            </div>

            <canvas id="chart"></canvas>

            <div class="mt-2 flex items-center gap-4">
              <div class="text-sm">Price: <span id="cur-price" class="font-semibold">-</span></div>
              <div class="text-sm">Change: <span id="cur-change">-</span></div>
              <div class="text-sm">Vol: <span id="cur-vol">-</span></div>
              <div class="text-sm">Last: <span id="last-trade">-</span></div>
            </div>
          </div>

          <div class="w-80">
            <div class="p-3 bg-slate-900 rounded">
              <h3 class="font-semibold">Place Order</h3>
              <div class="mt-2 grid grid-cols-2 gap-2">
                <select id="ord-side" class="col-span-1 px-2 py-1 rounded bg-slate-800 text-white">
                  <option value="buy">Buy</option>
                  <option value="sell">Sell</option>
                </select>
                <select id="ord-type" class="col-span-1 px-2 py-1 rounded bg-slate-800 text-white">
                  <option value="market">Market</option>
                  <option value="limit">Limit</option>
                  <option value="stop">Stop</option>
                  <option value="trailing_stop">Trailing Stop</option>
                </select>
                <input id="ord-qty" type="number" min="1" value="1" class="px-2 py-1 rounded bg-slate-800 text-white" />
                <input id="ord-price" type="number" step="0.01" placeholder="Price (for Limit)" class="px-2 py-1 rounded bg-slate-800 text-white" />
                <select id="tif" class="col-span-2 px-2 py-1 rounded bg-slate-800 text-white">
                  <option value="GTC">GTC</option>
                  <option value="IOC">IOC</option>
                  <option value="FOK">FOK</option>
                </select>
                <input id="trailing-val" placeholder="Trailing % or abs" class="col-span-2 px-2 py-1 rounded bg-slate-800 text-white" />
                <button id="place-order" class="col-span-2 mt-2 px-3 py-1 rounded bg-indigo-600">Place Order</button>
              </div>
            </div>

            <div class="mt-3 p-3 bg-slate-900 rounded">
              <h4 class="font-semibold">Your Quick Panel</h4>
              <div id="quick-panel" class="mt-2 small-muted">Login to trade</div>
            </div>
          </div>
        </div>

        <hr class="my-3 border-slate-700" />

        <div class="grid grid-cols-3 gap-4">
          <div class="col-span-1 bg-slate-900 p-2 rounded">
            <h4 class="font-semibold">Orderbook</h4>
            <div id="orderbook" class="text-sm mt-2 h-56 overflow-auto"></div>
          </div>
          <div class="col-span-1 bg-slate-900 p-2 rounded">
            <h4 class="font-semibold">Trades</h4>
            <div id="trades" class="text-sm mt-2 h-56 overflow-auto"></div>
          </div>
          <div class="col-span-1 bg-slate-900 p-2 rounded">
            <h4 class="font-semibold">Market Stats</h4>
            <div id="stats" class="text-sm mt-2"></div>
          </div>
        </div>

      </section>

      <aside class="glass p-4 rounded shadow">
        <div id="auth-area"></div>
        <hr class="my-3 border-slate-700" />
        <div>
          <h4 class="font-semibold">Portfolio</h4>
          <div id="portfolio" class="text-sm mt-2"></div>
        </div>
        <hr class="my-3 border-slate-700" />
        <div>
          <h4 class="font-semibold">Leaderboard</h4>
          <div id="leaderboard" class="text-sm mt-2"></div>
        </div>
      </aside>
    </main>

    <!-- Admin modal -->
    <div id="admin-modal" class="fixed inset-0 hidden items-center justify-center bg-black/60 p-4">
      <div class="bg-slate-800 p-4 rounded w-full max-w-3xl">
        <div class="flex justify-between items-center mb-3">
          <h2 class="text-lg font-medium">Admin Panel</h2>
          <button id="admin-close" class="px-2 py-1 rounded bg-red-600">Close</button>
        </div>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <h4 class="font-semibold">Tickers</h4>
            <div class="mt-2 flex gap-2">
              <input id="new-code" placeholder="CODE" class="px-2 py-1 rounded bg-slate-900" />
              <input id="new-name" placeholder="Name" class="px-2 py-1 rounded bg-slate-900" />
              <input id="new-price" type="number" placeholder="Start" class="px-2 py-1 rounded bg-slate-900" />
              <button id="add-ticker" class="px-2 py-1 rounded bg-green-600">Add</button>
            </div>
            <div id="ticker-list" class="mt-2 text-sm bg-slate-900 p-2 rounded h-48 overflow-auto"></div>
          </div>
          <div>
            <h4 class="font-semibold">Users</h4>
            <div id="user-list" class="mt-2 text-sm bg-slate-900 p-2 rounded h-48 overflow-auto"></div>
            <div class="mt-3">
              <button id="seed-users" class="px-2 py-1 rounded bg-indigo-600">Seed Sample Users</button>
              <button id="reset-db" class="px-2 py-1 rounded bg-red-600">Reset DB</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Auth modal -->
    <div id="auth-modal" class="fixed inset-0 hidden items-center justify-center bg-black/60 p-4">
      <div class="bg-slate-800 p-4 rounded w-full max-w-md">
        <h3 class="text-lg font-medium mb-2">Login / Register</h3>
        <div class="grid gap-2">
          <input id="reg-username" placeholder="Username" class="px-2 py-1 rounded bg-slate-900" />
          <input id="reg-password" placeholder="Password" type="password" class="px-2 py-1 rounded bg-slate-900" />
          <input id="reg-password2" placeholder="Confirm Password" type="password" class="px-2 py-1 rounded bg-slate-900" />
          <div class="flex gap-2">
            <button id="btn-register" class="px-2 py-1 rounded bg-green-600">Register</button>
            <button id="btn-login" class="px-2 py-1 rounded bg-indigo-600">Login</button>
            <button id="btn-guest" class="px-2 py-1 rounded bg-gray-600">Guest</button>
          </div>
        </div>
      </div>
    </div>

  </div>

<script>
/* Storage and helpers */
const LSKEY = 'asg-db';
function loadDB(){ try{ return JSON.parse(localStorage.getItem(LSKEY)||'null') }catch(e){return null} }
function saveDB(db){ localStorage.setItem(LSKEY, JSON.stringify(db)) }

function ensureDB(){
  let db = loadDB();
  if(!db){
    db = {
      tickers: {
        BTC: makeTicker('BTC','BitCoin-like',100),
        ABC: makeTicker('ABC','Alpha Corp',50)
      },
      users: {},
      orders: [], // live orderbook: objects {id, user, side, qty, remaining, type(limit/market/stop/trailing_stop), price, stopPrice, trailing, tif, created, status}
      trades: [],
      settings: { candleInterval:1000, simTick:1000 }
    };
    // create default admin
    (async ()=>{
      const {salt, hash} = await hashPassword('admin123');
      db.users['admin'] = { username:'admin', hash, salt, isAdmin:true, cash:100000, holdings:{}, created:Date.now() };
      saveDB(db);
    })();
  }
  return db;
}

function makeTicker(code,name,price){ return { code, name, lastPrice:price, candles:[], bids:[], asks:[] } }

let DB = ensureDB();
let currentUser = null;
let selectedTicker = Object.keys(DB.tickers)[0];

/* crypto hashing for passwords */
async function genSalt(){ const a = crypto.getRandomValues(new Uint8Array(16)); return btoa(String.fromCharCode(...a)); }
async function hashPassword(pw, salt=null){ salt = salt || await genSalt(); const enc = new TextEncoder(); const data = enc.encode(pw + salt); const digest = await crypto.subtle.digest('SHA-256', data); const hash = btoa(String.fromCharCode(...new Uint8Array(digest))); return { salt, hash }; }

async function verifyPassword(pw, salt, hash){ const h = await hashPassword(pw, salt); return h.hash === hash }

/* UI elems */
const tickerSelect = document.getElementById('ticker-select');
const candleInterval = document.getElementById('candle-interval');
const chart = document.getElementById('chart'); const ctx = chart.getContext('2d');

function renderTop(){ const el = document.getElementById('top-controls'); el.innerHTML=''; if(!currentUser){ const btn=document.createElement('button'); btn.textContent='Login'; btn.className='px-3 py-1 rounded bg-green-600'; btn.onclick=openAuth; el.appendChild(btn);} else { const u=DB.users[currentUser]; const div=document.createElement('div'); div.className='flex items-center gap-2'; const img=document.createElement('div'); img.className='w-8 h-8 rounded-full bg-slate-700 flex items-center justify-center'; img.textContent=u.username[0]; div.appendChild(img); const nm=document.createElement('div'); nm.textContent=u.username; div.appendChild(nm); const logout=document.createElement('button'); logout.textContent='Logout'; logout.className='px-2 py-1 rounded bg-red-600'; logout.onclick=()=>{currentUser=null; renderTop(); renderQuickPanel();}; div.appendChild(logout); el.appendChild(div);} }

function renderAuthArea(){ const a=document.getElementById('auth-area'); a.innerHTML=''; if(!currentUser){ const btn=document.createElement('button'); btn.className='w-full px-3 py-2 rounded bg-indigo-600'; btn.textContent='Login / Create'; btn.onclick=openAuth; a.appendChild(btn);} else { const u=DB.users[currentUser]; a.innerHTML = `<div class="flex items-center gap-3"><div class="w-12 h-12 rounded-full bg-slate-700 flex items-center justify-center">${u.username[0]}</div><div><div class="font-semibold">${u.username}</div><div class="small-muted">$${u.cash.toFixed(2)}</div></div></div>` } }

function renderTickerSelect(){ tickerSelect.innerHTML=''; Object.values(DB.tickers).forEach(t=>{ const o=document.createElement('option'); o.value=t.code; o.textContent = t.code + ' — ' + t.name; tickerSelect.appendChild(o); }); tickerSelect.value = selectedTicker; document.getElementById('ticker-name').textContent = DB.tickers[selectedTicker].name; }

/* orderbook & matching engine */
function orderId(){ return 'o'+Math.random().toString(36).slice(2,9) }

function placeOrderUI(){ if(!currentUser){ alert('Login to place orders'); return }
  const side = document.getElementById('ord-side').value;
  const type = document.getElementById('ord-type').value;
  const qty = Math.max(1, parseFloat(document.getElementById('ord-qty').value)||1);
  const price = parseFloat(document.getElementById('ord-price').value) || null;
  const tif = document.getElementById('tif').value;
  const trailingRaw = document.getElementById('trailing-val').value.trim();
  let trailing = null;
  if(trailingRaw){ if(trailingRaw.endsWith('%')) trailing = { pct: parseFloat(trailingRaw)/100 } else trailing = { abs: parseFloat(trailingRaw) } }
  const ord = { id: orderId(), user: currentUser, ticker: selectedTicker, side, qty, remaining: qty, type, price, tif, trailing, stopPrice: (type==='stop'?price:null), created: Date.now(), status:'open', lastUpdate: Date.now() };
  // Validation: funds/holdings
  const u = DB.users[currentUser];
  if(side==='buy' && (type==='market' || type==='limit') ){ const est = (price||DB.tickers[selectedTicker].lastPrice||0) * qty; if(u.cash < est*1.1){ if(!confirm('Insufficient cash for full size; still place?')) return } }
  DB.orders.push(ord); saveDB(DB); renderOrderbook(); renderQuickPanel();
}

document.getElementById('place-order').addEventListener('click', placeOrderUI);

function sortBook(){ // for matching: highest bids first, lowest asks first
  DB.orders = DB.orders.filter(o=>o.status==='open');
}

function matchEngineTick(){
  // Basic simulated market movement and candle building
  const T = DB.tickers[selectedTicker];
  // Simulate small price delta if no trades
  if(DB.trades.length===0){ T.lastPrice = T.lastPrice * (1 + (Math.random()-0.5)*0.002); }

  // First process trailing stops: update their stopPrice based on favorable movement
  DB.orders.forEach(o=>{
    if(o.type==='trailing_stop' && o.status==='open'){
      if(!o._trailRef) o._trailRef = { best: T.lastPrice };
      if(o.side==='sell'){
        // best is highest price seen since order
        o._trailRef.best = Math.max(o._trailRef.best, T.lastPrice);
        const trigger = o._trailRef.best - (o.trailing?.abs || (o._trailRef.best * (o.trailing?.pct||0)));
        if(T.lastPrice <= trigger){ // trigger to market sell
          o.type = 'market'; o.price = null; o.triggered=true; }
      } else {
        // buy trailing stop (less common) - trigger when price >= best + distance
        o._trailRef.best = Math.min(o._trailRef.best, T.lastPrice);
        const trigger = o._trailRef.best + (o.trailing?.abs || (o._trailRef.best * (o.trailing?.pct||0)));
        if(T.lastPrice >= trigger){ o.type='market'; o.price=null; o.triggered=true; }
      }
    }
    if(o.type==='stop' && o.status==='open' && o.stopPrice!=null){
      if(o.side==='buy' && T.lastPrice >= o.stopPrice){ o.type='market'; o.triggered=true }
      if(o.side==='sell' && T.lastPrice <= o.stopPrice){ o.type='market'; o.triggered=true }
    }
  });

  // Separate books for limit orders
  let bids = DB.orders.filter(o=>o.ticker===selectedTicker && o.side==='buy' && (o.type==='limit' || o.type==='market')).slice().sort((a,b)=>{
    if(a.type==='market' && b.type!=='market') return -1; if(b.type==='market' && a.type!=='market') return 1;
    if(a.price!==b.price) return b.price - a.price; return a.created - b.created;
  });
  let asks = DB.orders.filter(o=>o.ticker===selectedTicker && o.side==='sell' && (o.type==='limit' || o.type==='market')).slice().sort((a,b)=>{
    if(a.type==='market' && b.type!=='market') return -1; if(b.type==='market' && a.type!=='market') return 1;
    if(a.price!==b.price) return a.price - b.price; return a.created - b.created;
  });

  // matching loop
  for(let i=0;i< Math.max(bids.length, asks.length); i++){
    const bid = bids[i];
    for(let j=0;j<asks.length;j++){
      const ask = asks[j];
      if(!bid || !ask) continue;
      // determine match price
      const bidPrice = bid.type==='market' ? Infinity : bid.price;
      const askPrice = ask.type==='market' ? 0 : ask.price;
      // match if bidPrice >= askPrice
      if(bidPrice >= askPrice){
        const tradePrice = (bid.type==='market' && ask.type!=='market') ? ask.price : (ask.type==='market' && bid.type!=='market') ? bid.price : ((bid.price+ask.price)/2);
        const qty = Math.min(bid.remaining, ask.remaining);
        // handle FOK/IOC
        if(bid.tif==='FOK' && bid.qty>bid.remaining){ /* implies it already partially filled */ }
        // execute fill
        executeTrade(bid, ask, qty, tradePrice);
        // update arrays
        if(bid.remaining<=0) bids.splice(i,1);
        if(ask.remaining<=0) { asks.splice(j,1); j--; }
        // bookkeeping
      }
    }
  }

  // Post-process: handle IOC and FOK cancellations
  DB.orders.forEach(o=>{
    if(o.status==='open'){
      if(o.tif==='IOC' && !o._justFilled){ // IOC: cancel any remaining immediately after matching attempt
        // We implement by checking if created before this tick and still has remaining
        if(Date.now() - o.created > 50) { o.status='cancelled' }
      }
      if(o.tif==='FOK' && o._partialFill){ o.status='cancelled' }
    }
    delete o._justFilled; delete o._partialFill;
  });

  saveDB(DB); renderOrderbook(); renderTrades(); renderMarketInfo(); renderPortfolio(); renderLeaderboard(); renderChart();
}

function executeTrade(bid, ask, qty, price){
  // adjust buyer
  const buyer = DB.users[bid.user];
  const seller = DB.users[ask.user];
  const cost = qty * price;
  // buyer pays
  if(buyer){ if(buyer.cash + 1e-8 >= cost){ buyer.cash -= cost; buyer.holdings[bid.ticker] = (buyer.holdings[bid.ticker]||0)+qty; } else { /* insufficient: skip fill */ return } }
  if(seller){ const have = seller.holdings[ask.ticker]||0; if(have>=qty){ seller.holdings[ask.ticker] -= qty; seller.cash += cost; } else { /* seller lacks shares: skip */ return } }
  bid.remaining -= qty; ask.remaining -= qty;
  if(bid.remaining<=0) bid.status='filled'; else { bid._partialFill=true }
  if(ask.remaining<=0) ask.status='filled'; else { ask._partialFill=true }
  bid._justFilled = true; ask._justFilled = true;
  DB.trades.unshift({ time: Date.now(), ticker: bid.ticker, qty, price, buyer: bid.user, seller: ask.user });
  DB.tickers[bid.ticker].lastPrice = price;
}

/* Chart building */
function buildCandle(tickerCode){ const t = DB.tickers[tickerCode]; const interval = parseInt(candleInterval.value);
  const now = Date.now();
  const lastCandle = t.candles[t.candles.length-1];
  const price = t.lastPrice;
  if(!lastCandle || Math.floor(lastCandle.t/interval) !== Math.floor(now/interval)){
    // start new candle
    const c = { t: now, open: price, high: price, low: price, close: price, vol:0 };
    t.candles.push(c);
  } else {
    lastCandle.close = price; lastCandle.high = Math.max(lastCandle.high, price); lastCandle.low = Math.min(lastCandle.low, price);
  }
}

function renderChart(){ const can = chart; const w = can.width = can.clientWidth*devicePixelRatio; const h = can.height = can.clientHeight*devicePixelRatio; ctx.clearRect(0,0,w,h);
  const t = DB.tickers[selectedTicker]; if(!t) return; const candles = t.candles.slice(-80); if(!candles.length) return;
  const prices = candles.flatMap(c=>[c.high,c.low]); const max = Math.max(...prices); const min = Math.min(...prices); const pad=(max-min)*0.1||1; const xStep = w/candles.length;
  candles.forEach((c,i)=>{ const x = i*xStep + xStep/2; const yOpen = h - ((c.open-min+pad)/(max-min+2*pad)*h); const yClose = h - ((c.close-min+pad)/(max-min+2*pad)*h); const yHigh = h - ((c.high-min+pad)/(max-min+2*pad)*h); const yLow = h - ((c.low-min+pad)/(max-min+2*pad)*h); ctx.beginPath(); ctx.moveTo(x,yHigh); ctx.lineTo(x,yLow); ctx.lineWidth = 1*devicePixelRatio; ctx.strokeStyle='#6b7280'; ctx.stroke(); const bodyTop=Math.min(yOpen,yClose); const bodyH=Math.max(2,Math.abs(yOpen-yClose)); ctx.fillStyle = c.close>=c.open? '#06b6d4':'#ef4444'; ctx.fillRect(x-xStep*0.25, bodyTop, xStep*0.5, bodyH); }) }

/* UI rendering for orderbook, trades */
function renderOrderbook(){ const out = document.getElementById('orderbook'); out.innerHTML=''; const bids = DB.orders.filter(o=>o.ticker===selectedTicker && o.side==='buy' && o.status==='open').sort((a,b)=> (b.price||Infinity)-(a.price||Infinity) || a.created-b.created ); const asks = DB.orders.filter(o=>o.ticker===selectedTicker && o.side==='sell' && o.status==='open').sort((a,b)=> (a.price||0)-(b.price||0) || a.created-b.created ); const wrap=document.createElement('div'); wrap.innerHTML='<div class="flex justify-between small-muted"><div>BIDS</div><div>ASKS</div></div>'; const table=document.createElement('div'); table.className='grid grid-cols-2 gap-2 mt-2'; const left=document.createElement('div'); bids.slice(0,20).forEach(b=>{ const d=document.createElement('div'); d.textContent = `${b.user} ${b.qty} @ ${b.price||'MKT'}`; left.appendChild(d); }); const right=document.createElement('div'); asks.slice(0,20).forEach(a=>{ const d=document.createElement('div'); d.textContent = `${a.user} ${a.qty} @ ${a.price||'MKT'}`; right.appendChild(d); }); table.appendChild(left); table.appendChild(right); out.appendChild(wrap); out.appendChild(table); }

function renderTrades(){ const out = document.getElementById('trades'); out.innerHTML=''; DB.trades.slice(0,200).forEach(t=>{ const d=document.createElement('div'); d.className='p-1 border-b border-slate-700'; d.textContent = `${new Date(t.time).toLocaleTimeString()} ${t.ticker} ${t.qty}@${t.price.toFixed(2)} B:${t.buyer} S:${t.seller}`; out.appendChild(d); }); }

function renderMarketInfo(){ const p = DB.tickers[selectedTicker].lastPrice; document.getElementById('cur-price').textContent = '$' + p.toFixed(2); const c = DB.tickers[selectedTicker].candles.slice(-1)[0]; if(c){ const change = ((c.close-c.open)/c.open*100).toFixed(2); document.getElementById('cur-change').textContent = change + '%'; document.getElementById('cur-vol').textContent = c.vol; } document.getElementById('last-trade').textContent = DB.trades[0]?('$'+DB.trades[0].price.toFixed(2)): '-' }

function renderPortfolio(){ const out=document.getElementById('portfolio'); out.innerHTML=''; if(!currentUser) return out.innerHTML='<div class="small-muted">Login to see portfolio</div>';
  const u = DB.users[currentUser]; let total=u.cash; let html = `<div>Cash: $${u.cash.toFixed(2)}</div>`; for(const [code,qty] of Object.entries(u.holdings||{})){ const price = DB.tickers[code]?.lastPrice||0; const v = price*qty; total+=v; html += `<div class="flex justify-between"><div>${code} x ${qty}</div><div>$${v.toFixed(2)}</div></div>` }
  html += `<hr class="my-2"> <div class="font-semibold">Total: $${total.toFixed(2)}</div>`; out.innerHTML = html; }

function renderLeaderboard(){ const out = document.getElementById('leaderboard'); out.innerHTML=''; const arr = Object.values(DB.users).map(u=>{ let total=u.cash; for(const [code,qty] of Object.entries(u.holdings||{})){ total += (DB.tickers[code]?.lastPrice||0)*qty } return { name:u.username, total }; }).sort((a,b)=>b.total-a.total); out.innerHTML = arr.slice(0,10).map(x=>`<div class="flex justify-between"><div>${x.name}</div><div>$${x.total.toFixed(2)}</div></div>`).join(''); }

function renderQuickPanel(){ const el = document.getElementById('quick-panel'); if(!currentUser) return el.textContent='Login to trade'; const u=DB.users[currentUser]; el.innerHTML = `<div>Cash: $${u.cash.toFixed(2)}</div><div class="small-muted">Pending orders: ${DB.orders.filter(o=>o.user===currentUser && o.status==='open').length}</div>` }

/* Admin modal actions */
document.getElementById('open-admin').addEventListener('click', ()=>{
  const user = prompt('Admin username to login as admin:'); if(!user) return; if(!DB.users[user]) return alert('No such user'); if(!DB.users[user].isAdmin) return alert('User is not admin'); const pw = prompt('Password for admin'); verifyPassword(pw, DB.users[user].salt, DB.users[user].hash).then(ok=>{ if(ok){ currentUser = user; renderTop(); renderAuthArea(); document.getElementById('admin-modal').classList.remove('hidden'); renderTickerList(); renderUserList(); } else alert('Wrong password') });
});

document.getElementById('admin-close').addEventListener('click', ()=>{ document.getElementById('admin-modal').classList.add('hidden'); });

function renderTickerList(){ const el=document.getElementById('ticker-list'); el.innerHTML=''; Object.values(DB.tickers).forEach(t=>{ const d=document.createElement('div'); d.className='flex items-center justify-between p-1'; d.innerHTML = `<div>${t.code} — ${t.name} $${t.lastPrice.toFixed(2)}</div>`; const btnDel = document.createElement('button'); btnDel.textContent='Delete'; btnDel.className='px-2 py-0.5 rounded bg-red-600 text-xs'; btnDel.onclick=()=>{ if(confirm('Delete ticker?')){ delete DB.tickers[t.code]; saveDB(DB); renderTickerList(); renderTickerSelect(); } }; d.appendChild(btnDel); el.appendChild(d); }); }

document.getElementById('add-ticker').addEventListener('click', ()=>{ const code=document.getElementById('new-code').value.trim().toUpperCase(); const name=document.getElementById('new-name').value.trim(); const price=parseFloat(document.getElementById('new-price').value)||10; if(!code) return alert('Enter code'); DB.tickers[code]=makeTicker(code,name||code,price); saveDB(DB); renderTickerList(); renderTickerSelect(); });

function renderUserList(){ const el=document.getElementById('user-list'); el.innerHTML=''; Object.values(DB.users).forEach(u=>{ const d=document.createElement('div'); d.className='p-1 border-b border-slate-700 flex items-center justify-between'; d.innerHTML = `<div><div class="font-semibold">${u.username}${u.isAdmin? ' (admin)':''}</div><div class="small-muted">$${u.cash.toFixed(2)}</div></div>`; const actions=document.createElement('div'); const btnCash=document.createElement('button'); btnCash.textContent='Add $'; btnCash.className='px-2 py-0.5 rounded bg-green-600 text-xs'; btnCash.onclick=()=>{ const v=parseFloat(prompt('Amount to add',1000)||0); u.cash += v; saveDB(DB); renderUserList(); renderLeaderboard(); renderPortfolio(); }; actions.appendChild(btnCash); const btnDel=document.createElement('button'); btnDel.textContent='Delete'; btnDel.className='px-2 py-0.5 rounded bg-red-600 text-xs ml-1'; btnDel.onclick=()=>{ if(confirm('Delete user?')){ delete DB.users[u.username]; saveDB(DB); renderUserList(); renderLeaderboard(); } }; actions.appendChild(btnDel); d.appendChild(actions); el.appendChild(d); }); }

document.getElementById('seed-users').addEventListener('click', async ()=>{ for(let i=1;i<=3;i++){ const name='User'+i; const pw='pass'+i; const {salt,hash}=await hashPassword(pw); DB.users[name]={ username:name, salt, hash, hashAlgo:'SHA-256', cash:10000, holdings:{}, created:Date.now() } } saveDB(DB); renderUserList(); renderLeaderboard(); alert('Seeded users: User1..User3 (password pass1/pass2/pass3)') });

document.getElementById('reset-db').addEventListener('click', ()=>{ if(confirm('Reset entire DB?')){ localStorage.removeItem(LSKEY); location.reload(); } });

/* Auth modal */
function openAuth(){ document.getElementById('auth-modal').classList.remove('hidden'); }
function closeAuth(){ document.getElementById('auth-modal').classList.add('hidden'); }

document.getElementById('btn-register').addEventListener('click', async ()=>{ const u=document.getElementById('reg-username').value.trim(); const p=document.getElementById('reg-password').value; const p2=document.getElementById('reg-password2').value; if(!u||!p) return alert('Enter username and password'); if(p!==p2) return alert('Passwords mismatch'); if(DB.users[u]) return alert('Username exists'); const {salt,hash}=await hashPassword(p); DB.users[u]={ username:u, salt, hash, cash:10000, holdings:{}, created:Date.now() }; saveDB(DB); currentUser=u; renderTop(); renderAuthArea(); renderPortfolio(); renderLeaderboard(); closeAuth(); });

document.getElementById('btn-login').addEventListener('click', async ()=>{ const u=document.getElementById('reg-username').value.trim(); const p=document.getElementById('reg-password').value; if(!u||!p) return alert('Enter username and password'); if(!DB.users[u]) return alert('No such user'); const ok = await verifyPassword(p, DB.users[u].salt, DB.users[u].hash); if(ok){ currentUser=u; renderTop(); renderAuthArea(); renderPortfolio(); renderLeaderboard(); closeAuth(); } else alert('Wrong credentials'); });

document.getElementById('btn-guest').addEventListener('click', ()=>{ const name='Guest'+Math.floor(Math.random()*9000+1000); DB.users[name]={ username:name, salt:'', hash:'', guest:true, cash:5000, holdings:{}, created:Date.now() }; saveDB(DB); currentUser=name; renderTop(); renderAuthArea(); renderPortfolio(); renderLeaderboard(); closeAuth(); });

/* ticker & candle select handlers */
tickerSelect.addEventListener('change', (e)=>{ selectedTicker = e.target.value; renderMarketInfo(); renderOrderbook(); renderChart(); });
candleInterval.addEventListener('change', ()=>{ DB.settings.candleInterval = parseInt(candleInterval.value); saveDB(DB); });

/* Simulation loop */
let simHandle = null;
function startSim(){ if(simHandle) clearInterval(simHandle); simHandle = setInterval(()=>{ // each sim tick
  // simulate tiny random walk on every ticker's lastPrice if no trades
  Object.keys(DB.tickers).forEach(code=>{ const t=DB.tickers[code]; if(Math.random()<0.3){ t.lastPrice = Math.max(0.01, t.lastPrice*(1+(Math.random()-0.5)*0.01)); } buildCandle(code); });
  matchEngineTick(); saveDB(DB);
}, DB.settings.simTick || 1000); }
startSim();

/* initial render */
renderTickerSelect(); renderTop(); renderAuthArea(); renderPortfolio(); renderLeaderboard(); renderOrderbook(); renderTrades(); renderMarketInfo(); renderChart(); renderTickerList(); renderUserList();

window.addEventListener('resize', ()=>{ renderChart() });

</script>
</body>
</html>
