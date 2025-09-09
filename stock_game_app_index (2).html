<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Game Tài Xỉu - Bầu Cua - Chẵn Lẻ (Demo)</title>
  <!-- Tailwind CDN for quick styling (works in AppsGeyser webview) -->
  <script src="https://cdn.tailwindcss.com"></script>
  <style>/* small helpers */
    .card{background:linear-gradient(180deg,#ffffff,#f8fafc);}
  </style>
</head>
<body class="bg-slate-100 text-slate-800">
  <div class="max-w-4xl mx-auto p-4">
    <header class="flex items-center justify-between mb-4">
      <h1 class="text-2xl font-bold">Tài Xỉu • Bầu Cua • Chẵn Lẻ — Demo</h1>
      <div id="user-info" class="text-sm"></div>
    </header>

    <main class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <!-- Auth / Account -->
      <section class="card p-4 rounded-lg shadow">
        <h2 class="font-semibold mb-2">Đăng nhập / Đăng ký</h2>
        <form id="auth-form" class="space-y-2">
          <input id="username" placeholder="Tên đăng nhập" class="w-full p-2 border rounded" required />
          <input id="password" type="password" placeholder="Mật khẩu" class="w-full p-2 border rounded" required />
          <div class="flex gap-2">
            <button id="login-btn" type="button" class="px-3 py-2 bg-blue-600 text-white rounded">Đăng nhập</button>
            <button id="register-btn" type="button" class="px-3 py-2 bg-green-600 text-white rounded">Đăng ký</button>
          </div>
          <p class="text-xs text-slate-500">Demo: admin/admin123 (hãy đổi ngay trong Admin)</p>
        </form>

        <hr class="my-3" />
        <div class="text-sm">
          <strong>Số dư:</strong> <span id="balance">-</span><br>
          <strong>Lịch sử:</strong>
          <div id="history" class="h-32 overflow-auto text-xs mt-2 p-2 bg-white border rounded"></div>
        </div>
      </section>

      <!-- Game selection -->
      <section class="card p-4 rounded-lg shadow md:col-span-2">
        <h2 class="font-semibold mb-2">Chọn trò chơi & đặt cược</h2>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Tài / Xỉu</h3>
            <p class="text-xs text-slate-500">Tài: tổng 11-17, Xỉu: 4-10. (Quy tắc demo: ba viên giống nhau -> cược tài/xỉu thua)</p>
            <input id="tx-amount" type="number" min="100" step="100" placeholder="Số tiền" class="w-full p-2 border rounded my-2" />
            <div class="flex gap-2">
              <button data-game="taixiu" data-choice="tai" class="bet-btn px-3 py-2 bg-indigo-600 text-white rounded">Chọn Tài</button>
              <button data-game="taixiu" data-choice="xiu" class="bet-btn px-3 py-2 bg-indigo-400 text-white rounded">Chọn Xỉu</button>
            </div>
          </div>

          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Bầu Cua</h3>
            <p class="text-xs text-slate-500">Chọn 1 trong 6 mặt: Bầu, Cua, Tôm, Cá, Gà, Nai</p>
            <select id="bc-face" class="w-full p-2 border rounded my-2">
              <option value="bau">Bầu</option>
              <option value="cua">Cua</option>
              <option value="tom">Tôm</option>
              <option value="ca">Cá</option>
              <option value="ga">Gà</option>
              <option value="nai">Nai</option>
            </select>
            <input id="bc-amount" type="number" min="100" step="100" placeholder="Số tiền" class="w-full p-2 border rounded my-2" />
            <button data-game="baucua" class="bet-btn w-full px-3 py-2 bg-rose-600 text-white rounded">Đặt cược</button>
          </div>

          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Chẵn / Lẻ</h3>
            <p class="text-xs text-slate-500">Dựa trên tổng ba xúc xắc</p>
            <input id="cl-amount" type="number" min="100" step="100" placeholder="Số tiền" class="w-full p-2 border rounded my-2" />
            <div class="flex gap-2">
              <button data-game="chanle" data-choice="chan" class="bet-btn px-3 py-2 bg-emerald-600 text-white rounded">Chẵn</button>
              <button data-game="chanle" data-choice="le" class="bet-btn px-3 py-2 bg-amber-600 text-white rounded">Lẻ</button>
            </div>
          </div>
        </div>

        <hr class="my-3" />
        <div class="flex items-center gap-3">
          <button id="spin-btn" class="px-4 py-2 bg-slate-900 text-white rounded">Quay xúc xắc (Spin)</button>
          <div id="result-area" class="text-sm"></div>
        </div>

        <div class="mt-3 p-3 bg-white border rounded text-sm" id="last-round">Kết quả: —</div>
      </section>

      <!-- Admin panel (hidden until admin login) -->
      <section id="admin-panel" class="card p-4 rounded-lg shadow hidden md:col-span-3">
        <h2 class="font-semibold mb-2">Admin — Quản lý</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Người chơi</h3>
            <div id="user-list" class="text-xs h-48 overflow-auto mt-2"></div>
          </div>
          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Hành động</h3>
            <input id="admin-target" placeholder="username" class="w-full p-2 border rounded my-2" />
            <input id="admin-amount" type="number" placeholder="Số tiền (+/-)" class="w-full p-2 border rounded my-2" />
            <button id="admin-adjust" class="w-full px-3 py-2 bg-yellow-600 text-white rounded">Điều chỉnh số dư</button>
            <hr class="my-2" />
            <button id="admin-reset" class="w-full px-3 py-2 bg-red-600 text-white rounded">Reset dữ liệu (demo)</button>
          </div>
          <div class="p-3 bg-white border rounded">
            <h3 class="font-medium">Cấu hình</h3>
            <label class="text-xs">Admin user:</label>
            <input id="admin-user" class="w-full p-2 border rounded my-2" />
            <label class="text-xs">Admin pass (mật khẩu plaintext để demo)</label>
            <input id="admin-pass" class="w-full p-2 border rounded my-2" />
            <button id="admin-save-creds" class="w-full px-3 py-2 bg-sky-600 text-white rounded">Lưu</button>
          </div>
        </div>
      </section>

    </main>

    <footer class="mt-6 text-xs text-slate-500">Lưu ý: Đây là bản demo chạy hoàn toàn phía client (không dùng tiền thật). Để đưa lên AppsGeyser tải file này làm WebView hoặc host trên web có HTTPS rồi nhập URL vào AppsGeyser.</footer>
  </div>

<script>
// ---- Simple client-side "server" using localStorage (DEMO ONLY) ----
const DB = {
  key: 'txbc_demo_v1',
  load(){
    const raw = localStorage.getItem(this.key);
    if(!raw){
      const init = {
        users: { 'admin': { username:'admin', salt: '', passHash: '', balance: 1000000, isAdmin:true } },
        rounds: [],
        config:{ adminUser:'admin', adminPass:'admin123' }
      };
      localStorage.setItem(this.key, JSON.stringify(init));
      return init;
    }
    return JSON.parse(raw);
  },
  save(state){ localStorage.setItem(this.key, JSON.stringify(state)); }
}
let STATE = DB.load();

// On first run, if admin pass not set, store hashed admin123
(async function ensureAdmin(){
  if(!STATE.users.admin.passHash || STATE.users.admin.passHash===''){
    const salt = crypto.getRandomValues(new Uint8Array(16));
    const passHash = await hashPassword('admin123', salt);
    STATE.users.admin.salt = ab2hex(salt);
    STATE.users.admin.passHash = passHash;
    STATE.users.admin.isAdmin = true;
    DB.save(STATE);
    STATE.config.adminPass = 'admin123';
  }
})();

// --- utils ---
function ab2hex(buf){ return [...new Uint8Array(buf)].map(b=>b.toString(16).padStart(2,'0')).join(''); }
function hex2ab(hex){ const bytes = new Uint8Array(hex.match(/.{1,2}/g).map(h=>parseInt(h,16))); return bytes.buffer; }
async function hashPassword(pass, salt){
  // PBKDF2 with SHA-256, 100k iterations (client-side; OK for demo). In prod do server-side!
  const enc = new TextEncoder();
  const key = await crypto.subtle.importKey('raw', enc.encode(pass),'PBKDF2',false,['deriveBits']);
  const derived = await crypto.subtle.deriveBits({name:'PBKDF2',salt,iterations:100000,hash:'SHA-256'}, key, 256);
  return ab2hex(derived);
}

function cryptoRandInt(max){ // secure random int [0,max)
  const array = new Uint32Array(1);
  crypto.getRandomValues(array);
  return array[0] % max;
}

function saveState(){ DB.save(STATE); }

// --- Auth ---
let currentUser = null;
async function register(username, password){
  username = username.trim().toLowerCase();
  if(STATE.users[username]) throw 'Tên người dùng đã tồn tại';
  const salt = crypto.getRandomValues(new Uint8Array(16));
  const passHash = await hashPassword(password, salt);
  STATE.users[username] = { username, salt: ab2hex(salt), passHash, balance: 10000, isAdmin:false };
  saveState();
}
async function login(username, password){
  username = username.trim().toLowerCase();
  const u = STATE.users[username];
  if(!u) throw 'Người dùng không tồn tại';
  const salt = hex2ab(u.salt);
  const passHash = await hashPassword(password, salt);
  if(passHash !== u.passHash) throw 'Mật khẩu sai';
  currentUser = u;
  renderUser();
}
function logout(){ currentUser = null; renderUser(); }

// --- UI wiring ---
const userInfoEl = document.getElementById('user-info');
const balanceEl = document.getElementById('balance');
const historyEl = document.getElementById('history');
const lastRoundEl = document.getElementById('last-round');
const adminPanel = document.getElementById('admin-panel');

function renderUser(){
  if(currentUser){
    userInfoEl.innerHTML = `${currentUser.username} • <a href='#' id='logout'>Đăng xuất</a>`;
    balanceEl.textContent = new Intl.NumberFormat().format(currentUser.balance) + ' VNĐ (ảo)';
    document.getElementById('logout').onclick = (e)=>{ e.preventDefault(); logout(); };
    // admin panel show
    if(currentUser.isAdmin) adminPanel.classList.remove('hidden'); else adminPanel.classList.add('hidden');
  } else {
    userInfoEl.innerHTML = '';
    balanceEl.textContent = '-';
    adminPanel.classList.add('hidden');
  }
  // history
  const rounds = (STATE.rounds||[]).slice().reverse();
  historyEl.innerHTML = rounds.slice(0,40).map(r=>`<div>${r.time} — ${r.user} — ${r.msg}</div>`).join('');
  renderUserList();
}

function renderUserList(){
  const ul = document.getElementById('user-list');
  ul.innerHTML = Object.values(STATE.users).map(u=>`<div class='p-1 border-b'>${u.username} — ${new Intl.NumberFormat().format(u.balance)}</div>`).join('');
}

// --- Bets and spin ---
let pendingBets = [];
function addRoundLog(msg){
  STATE.rounds.push({ time: new Date().toLocaleString(), msg, user: currentUser?currentUser.username:'(guest)' });
  saveState(); renderUser();
}

function placeBet(bet){
  if(!currentUser) { alert('Bạn phải đăng nhập để đặt cược'); return; }
  if(bet.amount <=0 || isNaN(bet.amount)) return alert('Số tiền không hợp lệ');
  if(currentUser.balance < bet.amount) return alert('Không đủ tiền');
  // deduct immediately
  currentUser.balance -= bet.amount;
  STATE.users[currentUser.username].balance = currentUser.balance;
  pendingBets.push({ ...bet, user: currentUser.username });
  addRoundLog(`${currentUser.username} đặt ${bet.game} — ${JSON.stringify(bet)} — ${bet.amount}`);
  saveState(); renderUser();
}

// Bet buttons
document.querySelectorAll('.bet-btn').forEach(b=>b.addEventListener('click', (e)=>{
  const btn = e.currentTarget;
  const game = btn.dataset.game;
  if(game==='taixiu'){
    const choice = btn.dataset.choice;
    const amt = parseInt(document.getElementById('tx-amount').value || 0);
    placeBet({ game:'taixiu', choice, amount:amt });
  } else if(game==='baucua'){
    const face = document.getElementById('bc-face').value;
    const amt = parseInt(document.getElementById('bc-amount').value || 0);
    placeBet({ game:'baucua', face, amount:amt });
  } else if(game==='chanle'){
    const choice = btn.dataset.choice;
    const amt = parseInt(document.getElementById('cl-amount').value || 0);
    placeBet({ game:'chanle', choice, amount:amt });
  }
}));

// Spin
document.getElementById('spin-btn').addEventListener('click', ()=>{
  if(pendingBets.length===0){ alert('Chưa có cược nào'); return; }
  runRound();
});

function rollDie(){ return cryptoRandInt(6)+1; }

function runRound(){
  // roll 3 dice
  const d1 = rollDie(), d2 = rollDie(), d3 = rollDie();
  const faces = ['','bau','cua','tom','ca','ga','nai']; // 1..6
  const faceNames = [null,'Bầu','Cua','Tôm','Cá','Gà','Nai'];
  const diceFaces = [faces[d1], faces[d2], faces[d3]];
  const sum = d1 + d2 + d3;
  const isTriple = (d1===d2 && d2===d3);
  const resText = `Xúc xắc: ${d1}, ${d2}, ${d3} — ${diceFaces.map(f=>f).join(', ')} — Tổng=${sum}`;

  // process bets
  const results = [];
  for(const bet of pendingBets){
    let win = 0;
    if(bet.game==='taixiu'){
      const side = (sum>=11 && sum<=17)?'tai':'xiu';
      // demo rule: triple => all tài/xỉu lose
      if(isTriple){ win = 0; }
      else if(bet.choice===side){ win = bet.amount * 2; } // 1:1 payout (win gives back 2x incl stake)
    } else if(bet.game==='baucua'){
      // count occurrences
      const count = diceFaces.filter(f=>f===bet.face).length;
      if(count>0) win = bet.amount * (1 + count); // return stake + count * stake
    } else if(bet.game==='chanle'){
      const side = (sum%2===0)?'chan':'le';
      if(bet.choice===side){ win = bet.amount * 2; }
    }
    // settle
    if(win>0){
      STATE.users[bet.user].balance += win;
      results.push(`${bet.user} thắng ${new Intl.NumberFormat().format(win)} (${bet.game})`);
    } else {
      results.push(`${bet.user} thua ${new Intl.NumberFormat().format(bet.amount)} (${bet.game})`);
    }
  }

  // record
  const roundMsg = `${resText} — ${results.join(' | ')}`;
  STATE.rounds.push({ time: new Date().toLocaleString(), msg: roundMsg, user:'system' });
  saveState();
  pendingBets = [];
  lastRoundEl.textContent = roundMsg;
  renderUser();
}

// --- Auth form handlers ---
document.getElementById('register-btn').addEventListener('click', async ()=>{
  const u = document.getElementById('username').value;
  const p = document.getElementById('password').value;
  try{ await register(u,p); alert('Đăng ký thành công — hãy đăng nhập'); }
  catch(e){ alert('Lỗi: '+e); }
});

document.getElementById('login-btn').addEventListener('click', async ()=>{
  const u = document.getElementById('username').value;
  const p = document.getElementById('password').value;
  try{ await login(u,p); alert('Đăng nhập thành công'); }
  catch(e){ alert('Lỗi: '+e); }
});

// --- Admin actions ---
document.getElementById('admin-adjust').addEventListener('click', ()=>{
  const tgt = document.getElementById('admin-target').value.trim().toLowerCase();
  const amt = parseInt(document.getElementById('admin-amount').value || 0);
  if(!STATE.users[tgt]) return alert('Không tìm thấy user');
  STATE.users[tgt].balance += amt;
  saveState(); renderUser();
  addRoundLog(`Admin thay đổi ${tgt} ${amt}`);
});

document.getElementById('admin-reset').addEventListener('click', ()=>{
  if(!confirm('Bạn có chắc reset dữ liệu demo?')) return;
  localStorage.removeItem(DB.key);
  location.reload();
});

// Save admin creds (demo only)
document.getElementById('admin-save-creds').addEventListener('click', ()=>{
  const au = document.getElementById('admin-user').value.trim();
  const ap = document.getElementById('admin-pass').value.trim();
  if(!au||!ap) return alert('Nhập đủ');
  // This demo simply stores plaintext in STATE.config for ease; in prod never do this
  STATE.config.adminUser = au; STATE.config.adminPass = ap;
  // also create user if not exists
  if(!STATE.users[au]) STATE.users[au] = { username:au, salt:'', passHash:'', balance:100000, isAdmin:true };
  STATE.users[au].isAdmin = true;
  saveState(); alert('Lưu (demo). Để thay đổi mật khẩu admin thật, dùng chức năng đăng ký + đổi pass hoặc triển khai server.');
});

// initial render
renderUser();

// --- Notes for deploy to AppsGeyser:
// 1) Save this file as index.html and upload to a web hosting (HTTPS recommended) OR directly paste HTML into AppsGeyser WebView if they accept raw HTML.
// 2) AppsGeyser usually wraps your web page into an Android app; ensure you serve via HTTPS for best compatibility.
// 3) THIS DEMO is purely client-side—sensitive operations (auth, wallets, payouts) MUST be moved to a secure server for a production app.

</script>
</body>
</html>
