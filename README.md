<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Stock Game - Demo</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/lightweight-charts/dist/lightweight-charts.standalone.production.js"></script>
  <style>
    html,body,#app{height:100%}
    .glass{backdrop-filter: blur(6px); background: rgba(255,255,255,0.6)}
  </style>
</head>
<body class="bg-slate-100 font-sans">
  <div id="app" class="min-h-screen flex flex-col">
    <header class="p-4 bg-white/80 shadow sticky top-0 z-20 flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded bg-gradient-to-br from-indigo-500 to-pink-500 flex items-center justify-center text-white font-bold">SG</div>
        <div>
          <div class="text-lg font-semibold">Stock Game</div>
          <div class="text-sm text-slate-500">Demo - AppsGeyser</div>
        </div>
      </div>
      <div class="flex items-center gap-4">
        <div id="welcome" class="text-sm text-slate-600"></div>
        <button id="btn-logout" class="hidden px-3 py-1 rounded bg-rose-500 text-white text-sm">Logout</button>
        <button id="btn-show-auth" class="px-3 py-1 rounded bg-indigo-600 text-white text-sm">Đăng nhập / Đăng ký</button>
      </div>
    </header>

    <main class="flex-1 p-4 grid grid-cols-1 lg:grid-cols-3 gap-4">
      <section class="col-span-1 glass p-4 rounded shadow">
        <h3 class="font-semibold mb-3">Tickers</h3>
        <div id="tickers-list" class="space-y-2 max-h-72 overflow-auto"></div>
        <div class="mt-4">
          <h4 class="font-medium">Mua/Bán</h4>
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

      <section class="col-span-2 glass p-4 rounded shadow flex flex-col">
        <div class="flex items-center justify-between mb-2">
          <h3 class="font-semibold">Biểu đồ nến</h3>
          <select id="interval" class="px-2 py-1 border rounded text-sm">
            <option value="1">1s</option>
            <option value="5">5s</option>
            <option value="10">10s</option>
            <option value="15">15s</option>
            <option value="30">30s</option>
            <option value="60">1m</option>
          </select>
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
            <div class="text-xs text-slate-500">Volume</div>
            <div id="price-vol" class="text-lg font-bold">-</div>
          </div>
        </div>
      </section>

      <aside class="col-span-1 glass p-4 rounded shadow">
        <h4 class="font-semibold mb-2">Lịch sử giao dịch</h4>
        <div id="trades" class="text-sm max-h-64 overflow-auto mt-2"></div>
      </aside>
    </main>
  </div>

  <script>
  async function hashPassword(pw){
    const enc = new TextEncoder();
    const data = enc.encode(pw);
    const hash = await crypto.subtle.digest('SHA-256', data);
    return Array.from(new Uint8Array(hash)).map(b=>b.toString(16).padStart(2,'0')).join('');
  }

  function loadDB(){
    const json = localStorage.getItem('stockgame-db');
    if(json) return JSON.parse(json);
    const db = {users:[],tickers:[],trades:[]};
    db.users.push({username:'admin',cash:100000,isAdmin:true,holdings:{}});
    db.tickers=[{code:'AAPL',price:175,vol:1000,volatility:0.6}];
    localStorage.setItem('stockgame-db', JSON.stringify(db));
    return db;
  }
  function saveDB(db){localStorage.setItem('stockgame-db', JSON.stringify(db));}

  const db=loadDB();
  let currentUser=db.users.find(u=>u.username==='admin');
  let chart,series,currentTicker;
  let candles={};

  function initChart(){
    const chartEl=document.getElementById('chart');
    chart=LightweightCharts.createChart(chartEl,{layout:{textColor:'#222',background:'#fff'}});
    series=chart.addCandlestickSeries();
  }

  function generateCandle(t){
    const lastPrice=t.price;
    const change=(Math.random()-0.5)*t.volatility;
    const newPrice=Math.max(0.1,lastPrice*(1+change/100));
    t.price=newPrice;
    const time=Math.floor(Date.now()/1000);
    return {time:time,open:lastPrice,high:Math.max(lastPrice,newPrice),low:Math.min(lastPrice,newPrice),close:newPrice};
  }

  function tick(){
    db.tickers.forEach(t=>{
      if(!candles[t.code]) candles[t.code]=[];
      const c=generateCandle(t);
      candles[t.code].push(c);
      if(candles[t.code].length>200) candles[t.code].shift();
    });
    if(currentTicker){
      series.setData(candles[currentTicker.code]);
      const last=candles[currentTicker.code].at(-1);
      document.getElementById('price-now').textContent=last.close.toFixed(2);
      document.getElementById('price-change').textContent=((last.close-candles[currentTicker.code][0].open)/candles[currentTicker.code][0].open*100).toFixed(2)+'%';
      document.getElementById('price-vol').textContent=currentTicker.vol;
    }
    renderPlayers();
    renderTrades();
    saveDB(db);
  }

  function renderTickers(){
    const list=document.getElementById('tickers-list');
    list.innerHTML='';
    const sel=document.getElementById('order-ticker');
    sel.innerHTML='';
    db.tickers.forEach(t=>{
      const div=document.createElement('div');
      div.className='p-2 bg-white rounded shadow flex justify-between';
      div.innerHTML=`<b>${t.code}</b> ${t.price.toFixed(2)} <button data-code="${t.code}" class="select px-2 py-1 text-xs bg-slate-200 rounded">Xem</button>`;
      list.appendChild(div);
      const opt=document.createElement('option'); opt.value=t.code; opt.textContent=t.code; sel.appendChild(opt);
    });
    document.querySelectorAll('.select').forEach(b=>b.onclick=()=>{selectTicker(b.dataset.code)});
  }

  function selectTicker(code){currentTicker=db.tickers.find(t=>t.code===code);}

  function placeOrder(user,ticker,side,qty){
    const u=db.users.find(x=>x.username===user.username);
    const t=db.tickers.find(x=>x.code===ticker);
    if(!u||!t)return;
    const cost=t.price*qty;
    if(side==='buy'){
      if(u.cash<cost){alert('Không đủ tiền');return;}
      u.cash-=cost;
      u.holdings[ticker]=(u.holdings[ticker]||0)+qty;
    }else{
      if((u.holdings[ticker]||0)<qty){alert('Không đủ cổ phiếu');return;}
      u.holdings[ticker]-=qty;
      u.cash+=cost;
    }
    db.trades.unshift({time:Date.now(),user:u.username,side,ticker,qty,price:t.price});
  }

  function renderPlayers(){
    const div=document.getElementById('player-stats');
    div.innerHTML=db.users.map(u=>`<div class="p-1 bg-white rounded mb-1"><b>${u.username}</b> | Cash: ${u.cash.toFixed(2)}</div>`).join('');
  }
  function renderTrades(){
    const div=document.getElementById('trades');
    div.innerHTML=db.trades.map(tr=>`<div class="p-1 text-xs">${new Date(tr.time).toLocaleTimeString()} - ${tr.user} ${tr.side} ${tr.qty} ${tr.ticker}@${tr.price.toFixed(2)}</div>`).join('');
  }

  document.getElementById('btn-order').onclick=()=>{
    const qty=Number(document.getElementById('order-qty').value)||1;
    const side=document.getElementById('order-side').value;
    const ticker=document.getElementById('order-ticker').value;
    placeOrder(currentUser,ticker,side,qty);
    renderPlayers(); renderTrades(); saveDB(db);
  };

  initChart();
  renderTickers();
  selectTicker(db.tickers[0].code);
  setInterval(tick,1000);
  </script>
</body>
</html>
