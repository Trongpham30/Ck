<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>MiniInvest - App Chứng Khoán (Demo)</title>

  <!-- lightweight-charts (candlestick) -->
  <script src="https://unpkg.com/lightweight-charts/dist/lightweight-charts.standalone.production.js"></script>

  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --muted:#94a3b8; --accent:#0ea5a4;
      --green:#16a34a; --red:#ef4444; --panel:#071028;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    html,body{height:100%; margin:0; background:linear-gradient(180deg,#071023 0%, #071029 100%); color:#e6eef6;}
    .wrap{max-width:1100px; margin:12px auto; padding:12px;}
    header{display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;}
    .brand{display:flex; gap:12px; align-items:center;}
    .logo{width:44px;height:44px;border-radius:8px;background:linear-gradient(135deg,var(--accent),#0369a1);display:flex;align-items:center;justify-content:center;font-weight:700;}
    .title{font-size:18px;font-weight:600}
    .sub{color:var(--muted); font-size:13px}
    .grid{display:grid; grid-template-columns: 300px 1fr 300px; gap:12px; align-items:start;}
    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border:1px solid rgba(255,255,255,0.03); padding:10px; border-radius:10px;}
    .ticker{display:flex;justify-content:space-between; padding:8px; border-radius:8px; cursor:pointer;}
    .ticker:hover{background:rgba(255,255,255,0.02)}
    .sym{font-weight:700}
    .price{font-weight:600}
    .pct{font-size:13px}
    .green{color:var(--green)}
    .red{color:var(--red)}
    .chart-wrap{height:520px; display:flex; flex-direction:column;}
    #chart{flex:1; min-height:420px;}
    .tf-btns{display:flex; gap:6px; margin-bottom:8px}
    .btn{padding:6px 10px; border-radius:6px; background:rgba(255,255,255,0.03); border:1px solid rgba(255,255,255,0.02); cursor:pointer; font-size:13px}
    .btn.active{background:var(--accent); color:#022; font-weight:700}
    .order-row{display:flex; gap:8px; margin-bottom:8px}
    input[type=number], input[type=text], input[type=password]{width:100%; padding:8px; border-radius:6px; border:1px solid rgba(255,255,255,0.05); background:transparent; color:inherit}
    .small{font-size:13px; color:var(--muted)}
    footer{margin-top:12px; text-align:center; color:var(--muted); font-size:13px}
    /* responsive */
    @media (max-width:980px){ .grid{grid-template-columns:1fr; } .chart-wrap{height:420px} }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="brand">
        <div class="logo">MI</div>
        <div>
          <div class="title">MiniInvest — Ứng dụng Chứng khoán (Demo)</div>
          <div class="sub">Trang mô phỏng tương tự vn.investing.com — phù hợp để đóng gói app trên AppsGeyser</div>
        </div>
      </div>
      <div style="display:flex; gap:12px; align-items:center;">
        <div id="clock" class="sub small">—</div>
        <button id="loginBtn" class="btn">Login</button>
      </div>
    </header>

    <div class="grid">
      <!-- left: tickers -->
      <div class="card" style="height:520px; overflow:auto;">
        <div style="display:flex;justify-content:space-between; align-items:center; margin-bottom:8px;">
          <div style="font-weight:700">Danh sách cổ phiếu</div>
          <div class="small">Thời gian thực (mô phỏng)</div>
        </div>
        <div id="tickers"></div>
      </div>

      <!-- center: chart -->
      <div class="card chart-wrap">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:6px;">
          <div>
            <div id="activeSym" style="font-weight:800; font-size:18px">AAPL</div>
            <div class="small" id="activeName">Apple Inc.</div>
          </div>
          <div style="text-align:right">
            <div style="font-size:18px" id="activePrice">0.00</div>
            <div id="activeChange" class="small">—</div>
          </div>
        </div>

        <div class="tf-btns" id="tfBtns"></div>
        <div id="chart"></div>
      </div>

      <!-- right: order + admin -->
      <div class="card" style="height:520px; display:flex; flex-direction:column; justify-content:flex-start;">
        <div style="font-weight:700; margin-bottom:8px;">Place Order (Demo)</div>
        <div class="small" style="margin-bottom:8px">Tài khoản demo — tiền ảo</div>

        <div style="margin-bottom:8px;">
          <label class="small">Side</label>
          <div style="display:flex; gap:8px; margin-top:6px;">
            <button id="buyBtn" class="btn active">Buy</button>
            <button id="sellBtn" class="btn">Sell</button>
          </div>
        </div>

        <div style="margin-bottom:8px;">
          <label class="small">Quantity</label>
          <input id="qty" type="number" value="1" min="1" />
        </div>

        <div style="margin-bottom:10px;">
          <label class="small">Price (market)</label>
          <input id="orderPrice" type="text" readonly />
        </div>

        <button id="submitOrder" class="btn" style="width:100%; margin-bottom:12px;">Submit Order (Demo)</button>

        <div style="border-top:1px dashed rgba(255,255,255,0.04); padding-top:10px; margin-top:8px;">
          <div style="display:flex; justify-content:space-between; align-items:center;">
            <div style="font-weight:700">Admin</div>
            <div class="small">admin / admin123</div>
          </div>
          <div style="margin-top:8px;">
            <input id="addSym" placeholder="Symbol (e.g. XYZ)" />
            <input id="addName" placeholder="Company name" style="margin-top:6px"/>
            <div style="display:flex; gap:8px; margin-top:8px;">
              <button id="addBtn" class="btn" style="flex:1">Add</button>
              <button id="removeBtn" class="btn" style="flex:1">Remove Selected</button>
            </div>
          </div>
        </div>

        <div style="margin-top:12px; font-size:13px; color:var(--muted)">
          <div>Đơn demo không thực hiện giao dịch thật. Thay API && persistence để dùng production.</div>
        </div>
      </div>
    </div>

    <footer>
      <div>Built for AppsGeyser — copy file HTML vào AppsGeyser hoặc host và dùng URL</div>
    </footer>
  </div>

<script>
/*
  MiniInvest single-file JS
  - Simulate live prices per ticker
  - Candlestick chart with lightweight-charts
  - Timeframes (1m,5m,15m,30m,1h,1d) simulated
  - Basic admin add/remove tickers (no persistence)
*/

const DEFAULT_TICKERS = [
  { symbol: 'AAPL', name: 'Apple Inc.', price: 172.26, lastClose: 170.25 },
  { symbol: 'MSFT', name: 'Microsoft Corp.', price: 333.92, lastClose: 330.12 },
  { symbol: 'AMZN', name: 'Amazon.com Inc.', price: 140.51, lastClose: 139.30 },
  { symbol: 'TSLA', name: 'Tesla Inc.', price: 253.45, lastClose: 250.10 },
  { symbol: 'GOOGL', name: 'Alphabet Inc.', price: 151.22, lastClose: 150.00 },
  { symbol: 'NVDA', name: 'NVIDIA Corp.', price: 720.13, lastClose: 710.00 }
];

let tickers = JSON.parse(JSON.stringify(DEFAULT_TICKERS));
let selectedSymbol = tickers[0].symbol;
let side = 'buy';
let chart; let candleSeries;
let candleDataPerSymbol = {}; // store simulated candle arrays per symbol
let currentTF = '1m';
const TF_CONFIG = {
  '1m': 60,
  '5m': 60*5,
  '15m': 60*15,
  '30m': 60*30,
  '1h': 60*60,
  '1d': 60*60*24
};

// UI refs
const tickersDiv = document.getElementById('tickers');
const activeSym = document.getElementById('activeSym');
const activeName = document.getElementById('activeName');
const activePrice = document.getElementById('activePrice');
const activeChange = document.getElementById('activeChange');
const orderPrice = document.getElementById('orderPrice');
const qtyInput = document.getElementById('qty');
const buyBtn = document.getElementById('buyBtn');
const sellBtn = document.getElementById('sellBtn');
const submitOrder = document.getElementById('submitOrder');
const addBtn = document.getElementById('addBtn');
const removeBtn = document.getElementById('removeBtn');
const addSymInput = document.getElementById('addSym');
const addNameInput = document.getElementById('addName');
const loginBtn = document.getElementById('loginBtn');
const tfBtnsDiv = document.getElementById('tfBtns');
const clockEl = document.getElementById('clock');

// init clock
setInterval(()=>{ const d=new Date(); clockEl.innerText = d.toLocaleString(); }, 1000);

// build TF buttons
Object.keys(TF_CONFIG).forEach(tf=>{
  const b = document.createElement('button');
  b.className='btn'+(tf===currentTF? ' active':'');
  b.innerText = tf;
  b.onclick = ()=>{ setTimeframe(tf); Array.from(tfBtnsDiv.children).forEach(x=>x.classList.remove('active')); b.classList.add('active'); };
  tfBtnsDiv.appendChild(b);
});

// init chart
function initChart(){
  const container = document.getElementById('chart');
  container.innerHTML = '';
  chart = LightweightCharts.createChart(container, {
    width: container.clientWidth,
    height: container.clientHeight,
    layout: {background: 'rgba(0,0,0,0)', textColor: '#e6eef6'},
    grid: { vertLines: {visible:false}, horzLines: {color: 'rgba(255,255,255,0.03)'}},
    timeScale: { timeVisible: true, secondsVisible: false }
  });
  candleSeries = chart.addCandlestickSeries({
    upColor: '#16a34a', downColor: '#ef4444', borderVisible: false, wickUpColor: '#16a34a', wickDownColor:'#ef4444'
  });
  window.addEventListener('resize', ()=>chart.applyOptions({ width: container.clientWidth }));
}

// generate initial candles
function ensureCandles(sym){
  if (candleDataPerSymbol[sym]) return candleDataPerSymbol[sym];
  const base = (sym.charCodeAt(0) % 50) + 100;
  const data = [];
  const step = TF_CONFIG[currentTF];
  let t = Math.floor(Date.now()/1000) - 3600*6; // last 6 hours
  let lastClose = base + Math.random()*10;
  for (let i=0;i<200;i++){
    const open = +(lastClose * (1 + (Math.random()-0.5)/200)).toFixed(2);
    const close = +(open * (1 + (Math.random()-0.5)/50)).toFixed(2);
    const high = Math.max(open, close) * (1 + Math.random()/100);
    const low = Math.min(open, close) * (1 - Math.random()/100);
    data.push({ time: t, open: +open.toFixed(2), high: +high.toFixed(2), low: +low.toFixed(2), close: +close.toFixed(2) });
    lastClose = close; t += step;
  }
  candleDataPerSymbol[sym] = data;
  return data;
}

function setActiveSymbol(sym){
  selectedSymbol = sym;
  const t = tickers.find(x=>x.symbol===sym);
  activeSym.innerText = t.symbol;
  activeName.innerText = t.name;
  updatePricePanel(t);
  // update chart data
  const data = ensureCandles(sym);
  candleSeries.setData(data);
}

// update price & change panel
function updatePricePanel(t){
  activePrice.innerText = Number(t.price).toFixed(2);
  const pct = ((t.price - t.lastClose)/t.lastClose*100).toFixed(2);
  activeChange.innerText = `${pct}%`;
  activeChange.className = pct>=0 ? 'small green' : 'small red';
  orderPrice.value = Number(t.price).toFixed(2);
}

// render tickers list
function renderTickers(){
  tickersDiv.innerHTML = '';
  tickers.forEach(t=>{
    const el = document.createElement('div');
    el.className = 'ticker';
    el.onclick = ()=> setActiveSymbol(t.symbol);
    el.innerHTML = `
      <div>
        <div class="sym">${t.symbol}</div>
        <div class="small" style="color:var(--muted)">${t.name}</div>
      </div>
      <div style="text-align:right">
        <div class="price">${Number(t.price).toFixed(2)}</div>
        <div class="pct ${((t.price - t.lastClose)>=0)?'green':'red'}">${((t.price - t.lastClose)/t.lastClose*100).toFixed(2)}%</div>
      </div>
    `;
    if (t.symbol === selectedSymbol) el.style.background = 'rgba(255,255,255,0.02)';
    tickersDiv.appendChild(el);
  });
}

// simulate live prices
let simTimers = [];
function startSim(){
  stopSim();
  tickers.forEach(t=>{
    const id = setInterval(()=>{
      const pct = (Math.random()-0.5) * 0.6; // +/-0.3%
      t.price = +(t.price * (1 + pct/100)).toFixed(2);
      // update candles last bar
      const data = ensureCandles(t.symbol);
      const last = data[data.length-1];
      const newClose = +(last.close * (1 + (Math.random()-0.5)/100)).toFixed(2);
      const newHigh = Math.max(last.open, newClose) * (1 + Math.random()/200);
      const newLow = Math.min(last.open, newClose) * (1 - Math.random()/200);
      const newBar = { time: last.time + TF_CONFIG[currentTF], open: last.close, high: +newHigh.toFixed(2), low: +newLow.toFixed(2), close: newClose };
      data.push(newBar);
      if (data.length>500) data.shift();
      // if selected, update chart and panel
      if (t.symbol === selectedSymbol){
        candleSeries.setData(data);
        updatePricePanel(t);
      }
      renderTickers();
    }, 1000 + Math.random()*1000);
    simTimers.push(id);
  });
}
function stopSim(){ simTimers.forEach(id=>clearInterval(id)); simTimers=[]; }

// timeframe change
function setTimeframe(tf){
  currentTF = tf;
  // regenerate candles for all symbols to match TF (simple approach)
  candleDataPerSymbol = {};
  ensureCandles(selectedSymbol);
  candleSeries.setData(ensureCandles(selectedSymbol));
}

// order actions
buyBtn.onclick = ()=>{ side='buy'; buyBtn.classList.add('active'); sellBtn.classList.remove('active'); };
sellBtn.onclick = ()=>{ side='sell'; sellBtn.classList.add('active'); buyBtn.classList.remove('active'); };
submitOrder.onclick = ()=>{
  const qty = Number(qtyInput.value) || 0;
  if (!qty || qty<=0) return alert('Nhập quantity hợp lệ');
  const price = Number(orderPrice.value);
  alert(`DEMO: ${side.toUpperCase()} ${qty} ${selectedSymbol} @ ${price.toFixed(2)}\nĐây chỉ là mô phỏng.`);
};

// admin add/remove
addBtn.onclick = ()=>{
  const sym = addSymInput.value.trim().toUpperCase();
  const name = addNameInput.value.trim() || sym;
  if (!sym) return alert('Nhập symbol');
  if (tickers.find(x=>x.symbol===sym)) return alert('Symbol đã có');
  const obj = { symbol: sym, name, price: +(100*(1+Math.random())).toFixed(2), lastClose: +(100*(1+Math.random())).toFixed(2) };
  tickers.unshift(obj);
  addSymInput.value=''; addNameInput.value='';
  renderTickers();
};
removeBtn.onclick = ()=>{
  tickers = tickers.filter(x=>x.symbol !== selectedSymbol);
  selectedSymbol = tickers[0]?.symbol || null;
  renderTickers();
  if (selectedSymbol) setActiveSymbol(selectedSymbol);
  else { document.getElementById('activeSym').innerText = ''; document.getElementById('activePrice').innerText='0.00'; }
};

// login (simple admin)
loginBtn.onclick = ()=>{
  const u = prompt('Username:', '');
  if (u===null) return;
  const p = prompt('Password:', '');
  if (u==='admin' && p==='admin123') {
    alert('Admin login success');
    loginBtn.innerText = 'Admin';
  } else {
    alert('Login demo: any username/password logs in as user (no persistence)');
    loginBtn.innerText = u || 'User';
  }
};

// init everything
initChart();
renderTickers();
setActiveSymbol(selectedSymbol);
startSim();

// ensure chart updates when symbol switched
// also update orderPrice every second from selected ticker
setInterval(()=>{
  const t = tickers.find(x=>x.symbol===selectedSymbol);
  if (t) {
    updatePricePanel(t);
    orderPrice.value = Number(t.price).toFixed(2);
  }
}, 1000);

</script>
</body>
</html>
