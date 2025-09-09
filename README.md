/*
Stock Exchange Simulator (single-file React app)
- Uses Tailwind CSS for styling (assume Tailwind set up in the project)
- Uses lightweight-charts for candlestick charting
- Simulates live price updates via a local WebSocket-like simulator
- Includes: auth (user/admin), ticker list, watchlist, chart with timeframe buttons, buy/sell panel, admin panel

Setup (recommended):
1) Create a new Vite React app (or CRA):
   npm create vite@latest stock-sim --template react
   cd stock-sim
2) Install deps:
   npm install lightweight-charts uuid
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   (configure tailwind per docs)
3) Replace src/App.jsx with this file content and run:
   npm run dev

Note: This is a single-file demo. For production, split into components, add backend for auth, real market data, and a real WebSocket price feed.

Admin default credentials (demo): username: admin   password: admin123
CHANGE THEM in production!
*/

import React, { useEffect, useRef, useState } from 'react';
import { createChart } from 'lightweight-charts';
import { v4 as uuidv4 } from 'uuid';

export default function App() {
  // Simple in-memory users (demo)
  const [user, setUser] = useState(null);
  const [isAdmin, setIsAdmin] = useState(false);
  const [tickers, setTickers] = useState(getInitialTickers());
  const [selectedTicker, setSelectedTicker] = useState(tickers[0].symbol);
  const [timeframe, setTimeframe] = useState('1m');
  const wsRef = useRef(null);

  useEffect(() => {
    // start simulator
    wsRef.current = createPriceSimulator(tickers, onPriceUpdate);
    return () => wsRef.current && wsRef.current.close();
  }, []);

  useEffect(() => {
    // if a ticker is removed/added, ensure selectedTicker still exists
    if (!tickers.find(t => t.symbol === selectedTicker)) {
      setSelectedTicker(tickers[0]?.symbol || null);
    }
  }, [tickers]);

  function onPriceUpdate(update) {
    // update tickers state with new price
    setTickers(prev => prev.map(t => t.symbol === update.symbol ? { ...t, price: update.price, change: +(100 * (update.price - t.lastClose) / t.lastClose).toFixed(2) } : t));
  }

  function handleLogin(username, password) {
    if (username === 'admin' && password === 'admin123') {
      setUser({ id: 'admin', name: 'Administrator' });
      setIsAdmin(true);
      return true;
    }
    // demo: any username/password logs in as regular user
    setUser({ id: uuidv4(), name: username });
    setIsAdmin(false);
    return true;
  }

  return (
    <div className="min-h-screen bg-gray-50 text-gray-900">
      <Header user={user} onLogin={handleLogin} onLogout={() => { setUser(null); setIsAdmin(false); }} />
      <main className="max-w-7xl mx-auto p-4 grid grid-cols-12 gap-4">
        <aside className="col-span-3 bg-white p-3 rounded-lg shadow h-[70vh] overflow-auto">
          <TickerList tickers={tickers} onSelect={setSelectedTicker} selected={selectedTicker} />
        </aside>
        <section className="col-span-6 bg-white p-3 rounded-lg shadow h-[70vh] flex flex-col">
          <ChartPanel symbol={selectedTicker} timeframe={timeframe} onTimeframeChange={setTimeframe} />
        </section>
        <aside className="col-span-3 bg-white p-3 rounded-lg shadow h-[70vh] flex flex-col">
          <OrderPanel symbol={selectedTicker} tickers={tickers} />
          {isAdmin && <AdminPanel tickers={tickers} setTickers={setTickers} />}
        </aside>
      </main>
    </div>
  );
}

function Header({ user, onLogin, onLogout }) {
  return (
    <header className="bg-white shadow">
      <div className="max-w-7xl mx-auto px-4 py-3 flex items-center justify-between">
        <div className="flex items-center gap-3">
          <div className="text-2xl font-bold">MiniInvest</div>
          <div className="text-sm text-gray-500">Market / Equities / United States</div>
        </div>
        <div className="flex items-center gap-4">
          <SearchBox />
          {user ? (
            <div className="flex items-center gap-3">
              <div className="text-sm">Hello, <strong>{user.name}</strong></div>
              <button className="px-3 py-1 bg-red-50 text-red-600 rounded" onClick={onLogout}>Logout</button>
            </div>
          ) : (
            <LoginForm onLogin={onLogin} />
          )}
        </div>
      </div>
    </header>
  );
}

function SearchBox() {
  const [q, setQ] = useState('');
  return (
    <div className="relative">
      <input value={q} onChange={e => setQ(e.target.value)} placeholder="Search tickers or companies" className="px-3 py-2 border rounded w-72" />
      <div className="absolute right-1 top-1 text-sm text-gray-500">⌕</div>
    </div>
  );
}

function LoginForm({ onLogin }) {
  const [open, setOpen] = useState(false);
  const [u, setU] = useState('');
  const [p, setP] = useState('');
  return (
    <div>
      {!open ? <button className="px-3 py-1 bg-blue-600 text-white rounded" onClick={() => setOpen(true)}>Login</button> : (
        <div className="p-3 bg-white border rounded shadow-lg absolute right-4 mt-12">
          <input className="block mb-2 p-2 border rounded" placeholder="Username" value={u} onChange={e=>setU(e.target.value)} />
          <input type="password" className="block mb-2 p-2 border rounded" placeholder="Password" value={p} onChange={e=>setP(e.target.value)} />
          <div className="flex gap-2">
            <button className="px-3 py-1 bg-green-600 text-white rounded" onClick={() => { onLogin(u||'guest', p); setOpen(false); }}>Sign in</button>
            <button className="px-3 py-1 bg-gray-200 rounded" onClick={() => setOpen(false)}>Cancel</button>
          </div>
        </div>
      )}
    </div>
  );
}

function TickerList({ tickers, onSelect, selected }) {
  return (
    <div>
      <h3 className="font-semibold mb-3">Top US Equities</h3>
      <ul className="space-y-2">
        {tickers.map(t => (
          <li key={t.symbol} className={`p-2 rounded hover:bg-gray-50 cursor-pointer ${selected===t.symbol? 'bg-gray-100':''}`} onClick={()=>onSelect(t.symbol)}>
            <div className="flex justify-between items-center">
              <div>
                <div className="font-medium">{t.symbol}</div>
                <div className="text-xs text-gray-500">{t.name}</div>
              </div>
              <div className="text-right">
                <div className="font-semibold">{t.price.toFixed(2)}</div>
                <div className={`text-sm ${t.change>=0? 'text-green-600':'text-red-600'}`}>{t.change>=0?'+':''}{t.change.toFixed(2)}%</div>
              </div>
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}

function ChartPanel({ symbol, timeframe, onTimeframeChange }) {
  const chartRef = useRef(null);
  const candleSeriesRef = useRef(null);
  useEffect(() => {
    const chartDiv = chartRef.current;
    chartDiv.innerHTML = '';
    const chart = createChart(chartDiv, { width: chartDiv.clientWidth, height: 420, layout: { backgroundColor: '#ffffff', textColor: '#333' } });
    const candleSeries = chart.addCandlestickSeries();
    candleSeriesRef.current = candleSeries;
    // load initial mock data
    const data = generateMockCandles(symbol, timeframe, 200);
    candleSeries.setData(data);
    // subscribe to simulated live ticks (in real app use WebSocket)
    const id = setInterval(()=>{
      const last = data[data.length-1];
      const next = generateNextCandle(last);
      data.push(next);
      if (data.length>500) data.shift();
      candleSeries.setData(data);
    }, 1000);
    function handleResize() { chart.applyOptions({ width: chartDiv.clientWidth }); }
    window.addEventListener('resize', handleResize);
    return ()=>{ clearInterval(id); window.removeEventListener('resize', handleResize); chart.remove(); };
  }, [symbol, timeframe]);

  return (
    <div className="flex-1 flex flex-col">
      <div className="flex items-center justify-between mb-2">
        <h2 className="font-bold">{symbol}</h2>
        <div className="flex gap-2">
          {['1m','5m','15m','30m','1h','1d'].map(tf => (
            <button key={tf} onClick={()=>onTimeframeChange(tf)} className={`px-2 py-1 rounded ${timeframe===tf? 'bg-blue-600 text-white':'bg-gray-100'}`}>{tf}</button>
          ))}
        </div>
      </div>
      <div ref={chartRef} className="flex-1" />
    </div>
  );
}

function OrderPanel({ symbol, tickers }) {
  const [side, setSide] = useState('buy');
  const [qty, setQty] = useState(1);
  const price = tickers.find(t=>t.symbol===symbol)?.price || 0;
  function submit() {
    alert(`${side.toUpperCase()} ${qty} ${symbol} @ ${price.toFixed(2)} (demo only)`);
  }
  return (
    <div className="mb-4">
      <h3 className="font-medium mb-2">Place Order</h3>
      <div className="flex gap-2 mb-2">
        <button className={`flex-1 py-2 rounded ${side==='buy'?'bg-green-600 text-white':'bg-gray-100'}`} onClick={()=>setSide('buy')}>Buy</button>
        <button className={`flex-1 py-2 rounded ${side==='sell'?'bg-red-600 text-white':'bg-gray-100'}`} onClick={()=>setSide('sell')}>Sell</button>
      </div>
      <div className="mb-2">
        <label className="block text-xs text-gray-600">Quantity</label>
        <input type="number" value={qty} min={1} onChange={e=>setQty(Number(e.target.value))} className="w-full p-2 border rounded" />
      </div>
      <div className="text-sm text-gray-600 mb-2">Price: <strong>{price.toFixed(2)}</strong></div>
      <button onClick={submit} className="w-full py-2 rounded bg-blue-600 text-white">Submit Order</button>
    </div>
  );
}

function AdminPanel({ tickers, setTickers }) {
  const [sym, setSym] = useState('');
  const [name, setName] = useState('');
  function addTicker() {
    if (!sym) return;
    setTickers(prev => [{ symbol: sym.toUpperCase(), name: name||sym.toUpperCase(), price: 100, lastClose: 100, change: 0 }, ...prev]);
    setSym(''); setName('');
  }
  function remove(symbol) {
    setTickers(prev => prev.filter(t=>t.symbol!==symbol));
  }
  return (
    <div className="mt-4 border-t pt-3">
      <h4 className="font-semibold mb-2">Admin</h4>
      <div className="mb-2">
        <input placeholder="Symbol" value={sym} onChange={e=>setSym(e.target.value)} className="w-full p-2 border rounded mb-2" />
        <input placeholder="Name" value={name} onChange={e=>setName(e.target.value)} className="w-full p-2 border rounded mb-2" />
        <div className="flex gap-2"><button className="flex-1 py-2 bg-indigo-600 text-white rounded" onClick={addTicker}>Add</button></div>
      </div>
      <div className="text-sm">
        {tickers.slice(0,6).map(t=> (
          <div key={t.symbol} className="flex items-center justify-between py-1">
            <div>{t.symbol} — {t.name}</div>
            <button className="text-sm text-red-600" onClick={()=>remove(t.symbol)}>Remove</button>
          </div>
        ))}
      </div>
    </div>
  );
}

// ------------------ Helpers & Mock Data ------------------

function getInitialTickers(){
  // a small selection simulating US equities
  return [
    { symbol: 'AAPL', name: 'Apple Inc.', price: 172.26, lastClose: 170.25, change: +1.18 },
    { symbol: 'MSFT', name: 'Microsoft Corp.', price: 333.92, lastClose: 330.12, change: +1.15 },
    { symbol: 'AMZN', name: 'Amazon.com Inc.', price: 140.51, lastClose: 139.30, change: +0.86 },
    { symbol: 'TSLA', name: 'Tesla Inc.', price: 253.45, lastClose: 250.10, change: +1.34 },
    { symbol: 'GOOGL', name: 'Alphabet Inc.', price: 151.22, lastClose: 150.00, change: +0.81 },
    { symbol: 'NVDA', name: 'NVIDIA Corp.', price: 720.13, lastClose: 710.00, change: +1.44 },
  ];
}

function createPriceSimulator(initialTickers, onUpdate) {
  // returns an object with close method to stop simulation
  let running = true;
  const timers = [];
  initialTickers.forEach(t => {
    const id = setInterval(() => {
      if (!running) return;
      const changePct = (Math.random() - 0.5) * 0.5; // ±0.25%
      const newPrice = +(t.price * (1 + changePct/100)).toFixed(2);
      t.price = newPrice;
      onUpdate({ symbol: t.symbol, price: newPrice });
    }, 1000 + Math.random()*1500);
    timers.push(id);
  });
  return {
    close: () => { running=false; timers.forEach(id=>clearInterval(id)); }
  };
}

function generateMockCandles(symbol, timeframe, count=200) {
  // generate simple synthetic candle data
  const base = 100 + (symbol.charCodeAt(0)%50);
  const data = [];
  let t = Date.now()/1000 - count*60;
  let lastClose = base;
  for (let i=0;i<count;i++){
    const o = +(lastClose * (1 + (Math.random()-0.5)/200)).toFixed(2);
    const c = +(o * (1 + (Math.random()-0.5)/50)).toFixed(2);
    const h = Math.max(o,c) * (1 + Math.random()/100);
    const l = Math.min(o,c) * (1 - Math.random()/100);
    data.push({ time: Math.floor(t), open: o, high: +h.toFixed(2), low: +l.toFixed(2), close: +c.toFixed(2) });
    lastClose = c;
    t += 60; // advance 1 minute
  }
  return data;
}
function generateNextCandle(last){
  const o = last.close;
  const c = +(o * (1 + (Math.random()-0.5)/100)).toFixed(2);
  const h = Math.max(o,c) * (1 + Math.random()/200);
  const l = Math.min(o,c) * (1 - Math.random()/200);
  return { time: last.time+60, open: o, high: +h.toFixed(2), low: +l.toFixed(2), close: c };
}
