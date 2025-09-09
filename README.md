<!doctype html>
<html lang="vi">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>ChatBot Demo — AppsGeyser</title>
<style>
  :root{ --bg:#0b1220; --card:#071021; --accent:#06b6d4; --muted:#9aa8b7; color-scheme: dark; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }
  html,body{height:100%; margin:0; background:linear-gradient(180deg,#051019,#071028); color:#e6eef6}
  .wrap{max-width:900px;margin:12px auto;padding:12px}
  header{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px}
  .logo{display:flex;gap:10px;align-items:center}
  .logo-box{width:44px;height:44px;border-radius:8px;background:linear-gradient(135deg,var(--accent),#0369a1);display:flex;align-items:center;justify-content:center;font-weight:700}
  .title{font-weight:700}
  .sub{color:var(--muted);font-size:13px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border-radius:10px; padding:12px; border:1px solid rgba(255,255,255,0.03)}
  .chat-window{height:60vh; overflow:auto; padding:12px; margin:10px 0; border-radius:8px; background:linear-gradient(180deg, rgba(0,0,0,0.15), rgba(0,0,0,0.08)); border:1px solid rgba(255,255,255,0.02)}
  .msg{display:flex; margin-bottom:10px}
  .msg.me{justify-content:flex-end}
  .bubble{max-width:75%; padding:10px 12px; border-radius:12px; background:rgba(255,255,255,0.03); color:inherit}
  .bubble.me{background:linear-gradient(90deg,#065f46,#0ea5a4); color:#022}
  .meta{font-size:12px; color:var(--muted); margin-bottom:6px}
  .controls{display:flex; gap:8px; align-items:center}
  input[type=text], textarea{width:100%; padding:10px; border-radius:8px; border:1px solid rgba(255,255,255,0.04); background:transparent; color:inherit}
  button{padding:8px 12px; border-radius:8px; border: none; background:var(--accent); color:#022; font-weight:600; cursor:pointer}
  .btn-ghost{background:transparent;border:1px solid rgba(255,255,255,0.03); color:var(--muted)}
  .small{font-size:13px;color:var(--muted)}
  .row{display:flex; gap:8px; align-items:center}
  .sticky-bottom{position:sticky; bottom:0; background:linear-gradient(180deg, rgba(0,0,0,0.0), rgba(0,0,0,0.02)); padding-top:6px}
  .badge{font-size:12px;padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.02); color:var(--muted)}
  .switch{display:inline-flex; align-items:center; gap:6px}
  .export-area{width:100%; height:90px; margin-top:8px; padding:8px; border-radius:8px; background:transparent; border:1px dashed rgba(255,255,255,0.03); color:var(--muted)}
  @media (max-width:720px){ .chat-window{height:55vh} }
</style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="logo">
        <div class="logo-box">CB</div>
        <div>
          <div class="title">ChatBot Auto — Demo</div>
          <div class="sub">Dán file này vào AppsGeyser để tạo app chat tự động</div>
        </div>
      </div>
      <div class="small">Local demo · No server</div>
    </header>

    <div class="card">
      <div style="display:flex;justify-content:space-between; align-items:center;">
        <div>
          <div style="font-weight:700">Cuộc trò chuyện</div>
          <div class="small">Bot tự động / Lưu cục bộ (localStorage)</div>
        </div>
        <div class="row">
          <div class="switch">
            <label class="small">Auto-generate</label>
            <input id="autoToggle" type="checkbox" />
          </div>
          <button id="clearBtn" class="btn-ghost">Xóa</button>
        </div>
      </div>

      <div id="chat" class="chat-window" aria-live="polite"></div>

      <div class="sticky-bottom">
        <div style="display:flex; gap:8px; align-items:center; margin-bottom:6px;">
          <input id="inputMsg" type="text" placeholder="Nhập tin nhắn..." />
          <button id="sendBtn">Gửi</button>
        </div>
        <div style="display:flex; gap:8px; align-items:center;">
          <input id="botName" type="text" placeholder="Tên bot (mặc định: Thảo)" style="width:200px" />
          <button id="startConv" class="btn-ghost">Bot bắt chuyện</button>
          <div style="flex:1"></div>
          <div class="badge" id="statusBadge">Status: idle</div>
        </div>
      </div>

      <hr style="margin:12px 0; border-color:rgba(255,255,255,0.03)" />

      <div style="display:flex; gap:12px; flex-wrap:wrap;">
        <div style="flex:1; min-width:220px">
          <div style="font-weight:700; margin-bottom:6px">Thiết lập</div>
          <div class="small">Chu kỳ tự động (s)</div>
          <input id="autoInterval" type="number" value="8" min="2" style="width:110px; margin-top:6px" />
          <div class="small" style="margin-top:8px">Chế độ trả lời</div>
          <select id="replyMode" style="margin-top:6px; width:220px">
            <option value="smart">Smart (mẫu + keyword)</option>
            <option value="echo">Echo (nhắc lại)</option>
            <option value="random">Random (vui)</option>
          </select>
        </div>

        <div style="flex:1; min-width:220px">
          <div style="font-weight:700; margin-bottom:6px">Xuất/nhập lịch sử</div>
          <div class="small">Export JSON</div>
          <textarea id="exportBox" class="export-area" readonly></textarea>
          <div style="display:flex; gap:8px; margin-top:8px;">
            <button id="exportBtn" class="btn">Export</button>
            <button id="importBtn" class="btn-ghost">Import (paste JSON)</button>
          </div>
        </div>

        <div style="flex:1; min-width:220px">
          <div style="font-weight:700; margin-bottom:6px">Thông tin nhanh</div>
          <div class="small">- Bot mô phỏng: dễ tích hợp webhook hoặc API nếu bạn có server.</div>
          <div class="small" style="margin-top:6px">- AppsGeyser có thể chặn remote scripts — nếu bot dùng API cần host backend riêng.</div>
        </div>
      </div>
    </div>

    <footer style="margin-top:10px; text-align:center; color:var(--muted); font-size:13px">
      Built for AppsGeyser — Dán HTML này vào form tạo app.
    </footer>
  </div>

<script>
/* ChatBot Auto — single-file JS
 - Lưu lịch sử ở localStorage
 - Auto-generate conversation khi bật (bot tự gửi theo chu kỳ)
 - 3 chế độ trả lời: smart, echo, random
 - Typing indicator, simple rule-based replies
*/

const CHAT_KEY = 'appsgeyser_chat_demo_v1';
let state = {
  messages: [], // {id, sender:'me'|'bot', text, time}
  auto: false,
  intervalSec: 8,
  replyMode: 'smart',
  botName: 'Thảo',
  isTyping: false
};

const chatEl = document.getElementById('chat');
const inputMsg = document.getElementById('inputMsg');
const sendBtn = document.getElementById('sendBtn');
const clearBtn = document.getElementById('clearBtn');
const autoToggle = document.getElementById('autoToggle');
const autoInterval = document.getElementById('autoInterval');
const replyMode = document.getElementById('replyMode');
const botNameInput = document.getElementById('botName');
const startConvBtn = document.getElementById('startConv');
const statusBadge = document.getElementById('statusBadge');
const exportBox = document.getElementById('exportBox');
const exportBtn = document.getElementById('exportBtn');
const importBtn = document.getElementById('importBtn');

function uid(){ return 'm'+Math.random().toString(36).slice(2,9); }
function now(){ return new Date().toISOString(); }

// persistence
function loadState(){
  try{
    const raw = localStorage.getItem(CHAT_KEY);
    if(raw){ state.messages = JSON.parse(raw); }
  }catch(e){ console.warn('load failed', e); }
}
function saveState(){
  try{ localStorage.setItem(CHAT_KEY, JSON.stringify(state.messages)); }catch(e){ console.warn('save failed', e); }
}

// render
function render(){
  chatEl.innerHTML = '';
  state.messages.forEach(m=>{
    const container = document.createElement('div');
    container.className = 'msg ' + (m.sender==='me'? 'me':'');
    const meta = document.createElement('div');
    meta.className = 'meta small';
    meta.innerText = `${m.sender==='me' ? 'Bạn' : (m.sender==='bot' ? (m.botName || state.botName) : m.sender)} · ${new Date(m.time).toLocaleString()}`;
    const bubble = document.createElement('div');
    bubble.className = 'bubble ' + (m.sender==='me' ? 'me':'');
    bubble.innerText = m.text;
    container.appendChild(bubble);
    container.appendChild(meta);
    chatEl.appendChild(container);
  });

  // typing indicator
  if(state.isTyping){
    const t = document.createElement('div'); t.className='msg';
    const bubble = document.createElement('div'); bubble.className='bubble'; bubble.innerText='...';
    t.appendChild(bubble);
    chatEl.appendChild(t);
  }

  // scroll bottom
  chatEl.scrollTop = chatEl.scrollHeight;
  statusBadge.innerText = `Status: ${state.auto ? 'auto' : 'idle'}`;
}

// send message
function send(text){
  if(!text) return;
  const m = { id: uid(), sender:'me', text, time: now() };
  state.messages.push(m);
  saveState();
  render();
  // bot reply
  setTimeout(()=>botReply(text), 400);
}

// bot reply logic
function botReply(userText){
  // select behavior based on replyMode
  const mode = state.replyMode;
  let replyText = '';
  if(mode === 'echo'){
    replyText = `Bạn vừa nói: "${userText}"`;
  } else if(mode === 'random'){
    const jokes = [
      'Haha, nghe vui đấy 😄',
      'Mình hiểu rồi!',
      'Ồ, nói rõ hơn được không?',
      'Thật không? kể mình nghe thêm đi.',
      'Mình đang tìm thông tin...'
    ];
    replyText = jokes[Math.floor(Math.random()*jokes.length)];
  } else { // smart (keyword)
    const u = userText.toLowerCase();
    if(u.includes('xin chào') || u.includes('hello') || u.includes('hi')) replyText = `Xin chào! Mình là ${state.botName}. Bạn có thể hỏi mình bất cứ điều gì.`;
    else if(u.includes('tên') && u.includes('gì')) replyText = `Mình tên là ${state.botName}. Rất vui được trò chuyện!`;
    else if(u.includes('thời tiết')) replyText = `Mình không có dữ liệu thời tiết trực tiếp trong app demo này. Bạn cần mình hướng dẫn cách lấy thời tiết từ API không?`;
    else if(u.includes('giờ') || u.includes('time')) replyText = `Bây giờ là ${new Date().toLocaleString()}`;
    else if(u.includes('cách') && u.includes('làm')) replyText = `Bạn đang cần hướng dẫn cụ thể chỗ nào hơn? Mô tả ngắn giúp mình nhé.`;
    else replyText = `Mình nghe bạn: "${userText}". Bạn muốn mình trả lời chi tiết hay gợi ý?`;
  }

  // simulate typing
  state.isTyping = true; render();
  const typingDelay = 600 + Math.random()*1200;
  setTimeout(()=>{
    state.isTyping = false;
    const botMsg = { id: uid(), sender:'bot', botName: state.botName, text: replyText, time: now() };
    state.messages.push(botMsg);
    saveState();
    render();
  }, typingDelay);
}

// auto-generate conversation (bot initiates and replies)
let autoTimer = null;
function startAuto(){
  stopAuto();
  const sec = Math.max(2, Number(autoInterval.value) || 8);
  state.auto = true;
  state.intervalSec = sec;
  function tick(){
    // bot or user message alternation simulation
    const rand = Math.random();
    if(rand < 0.4){
      // bot sends a prompt (initiates)
      const prompts = [
        'Chào bạn, hôm nay bạn thế nào?',
        'Bạn thích chủ đề gì hôm nay?',
        'Bạn đang cần trợ giúp về gì?',
        'Mình có vài mẹo hay muốn chia sẻ...'
      ];
      const txt = prompts[Math.floor(Math.random()*prompts.length)];
      state.isTyping = true; render();
      setTimeout(()=> {
        state.isTyping = false;
        state.messages.push({ id: uid(), sender:'bot', botName: state.botName, text: txt, time: now() });
        saveState(); render();
      }, 800 + Math.random()*800);
    } else {
      // simulated user reply (bot will then auto-reply)
      const replies = [
        'OK, cho mình biết thêm.',
        'Nghe hay đó!',
        'Bạn có thể giải thích rõ hơn?',
        'Mình đồng ý.',
        'Thử hỏi một điều khác nhé.'
      ];
      const txt = replies[Math.floor(Math.random()*replies.length)];
      state.messages.push({ id: uid(), sender:'me', text: txt, time: now() });
      saveState(); render();
      // bot auto-reply shortly
      setTimeout(()=> botReply(txt), 600 + Math.random()*600);
    }
  }
  tick(); // immediate
  autoTimer = setInterval(tick, sec*1000);
  render();
}
function stopAuto(){
  if(autoTimer) clearInterval(autoTimer);
  autoTimer = null;
  state.auto = false;
  render();
}

// UI wiring
sendBtn.onclick = ()=>{ send(inputMsg.value.trim()); inputMsg.value=''; inputMsg.focus(); };
inputMsg.addEventListener('keydown', e=>{ if(e.key==='Enter'){ e.preventDefault(); sendBtn.click(); } });
clearBtn.onclick = ()=>{ if(confirm('Xóa toàn bộ cuộc trò chuyện?')){ state.messages=[]; saveState(); render(); } };
autoToggle.onchange = (e)=>{ if(e.target.checked) startAuto(); else stopAuto(); };
autoInterval.onchange = ()=>{ if(state.auto) startAuto(); };
replyMode.onchange = ()=>{ state.replyMode = replyMode.value; };
botNameInput.onchange = ()=>{ state.botName = botNameInput.value.trim() || 'Thảo'; };
startConvBtn.onclick = ()=>{
  const starter = [
    `Xin chào! Mình là ${state.botName}. Bạn cần giúp gì?`,
    `Chào bạn, mình có thể hỗ trợ những gì hôm nay?`
  ];
  state.messages.push({ id: uid(), sender:'bot', botName: state.botName, text: starter[Math.floor(Math.random()*starter.length)], time: now() });
  saveState(); render();
};

// export/import
exportBtn.onclick = ()=>{
  exportBox.value = JSON.stringify(state.messages, null, 2);
  alert('Đã tạo JSON trong ô Export. Bạn có thể sao chép để lưu.');
};
importBtn.onclick = ()=>{
  const raw = prompt('Dán JSON cuộc trò chuyện để import:', '');
  if(!raw) return;
  try{
    const arr = JSON.parse(raw);
    if(Array.isArray(arr)){ state.messages = arr; saveState(); render(); alert('Import OK'); }
    else alert('JSON không đúng định dạng (mảng)');
  }catch(e){ alert('JSON lỗi: '+e.message); }
};

// load on start
loadState();
render();

// populate controls initial values
autoInterval.value = state.intervalSec;
replyMode.value = state.replyMode;
botNameInput.value = state.botName;

// helpful housekeeping: if there are no messages, add a greeting
if(state.messages.length === 0){
  state.messages.push({ id: uid(), sender:'bot', botName: state.botName, text: `Xin chào! Mình là ${state.botName}. Đây là demo chat tự động.`, time: now() });
  saveState();
  render();
}

// cleanup on page hide
window.addEventListener('beforeunload', ()=> saveState());

</script>
</body>
</html>
