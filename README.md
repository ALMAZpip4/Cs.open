<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CS2 Simulator V32</title>
  <style>
    :root {
      --bg: #0b0b0b;
      --card: #151515;
      --accent: #eb4b4b; 
      --blue: #4b69ff;
      --purple: #8847ff;
      --pink: #d32ce6;
      --red: #eb4b4b;
      --gold: #caab05;
      --text: #e0e0e0;
      --upgrade-grad: linear-gradient(180deg, #ff4b4b 0%, #880000 100%); 
    }

    body {
      background-color: var(--bg);
      color: var(--text);
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding-bottom: 90px;
      overflow-x: hidden;
    }

    /* Auth Screen */
    #auth-screen { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; justify-content: center; align-items: center; }
    .auth-box { background: #111; padding: 30px; border-radius: 15px; border: 1px solid #333; text-align: center; width: 300px; }
    .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
    .auth-tab { flex: 1; padding: 8px; background: #222; cursor: pointer; border-radius: 5px; font-size: 0.8rem; border: 1px solid transparent; }
    .auth-tab.active { background: var(--accent); color: #fff; font-weight: bold; }
    
    input, select { background: #222; border: 1px solid #444; color: white; padding: 10px; border-radius: 5px; width: 100%; margin-bottom: 10px; box-sizing: border-box; }
    
    .btn { background: var(--accent); color: #fff; border: none; padding: 10px 15px; font-weight: bold; cursor: pointer; border-radius: 5px; text-transform: uppercase; font-size: 0.75rem; transition: 0.2s; }
    .btn:active { transform: scale(0.95); }

    /* UI */
    .header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid #333; }
    .balance { color: var(--accent); font-weight: bold; font-size: 1.2rem; }

    .page { display: none; padding: 20px; animation: fadeIn 0.2s; }
    .page.active { display: block; }
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

    .case-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 15px; }
    .inv-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(110px, 1fr)); gap: 10px; }
    .card { background: var(--card); border: 1px solid #222; padding: 15px; text-align: center; border-radius: 10px; cursor: pointer; }
    .skin-card { background: #1a1a1a; padding: 8px; border-radius: 5px; font-size: 0.65rem; border-bottom: 4px solid #ccc; text-align: center; cursor: pointer; position: relative; }
    .skin-card.selected { outline: 2px solid #fff; box-shadow: 0 0 10px #fff; }

    .Mil-Spec { border-color: var(--blue) !important; }
    .Restricted { border-color: var(--purple) !important; }
    .Classified { border-color: var(--pink) !important; }
    .Covert { border-color: var(--red) !important; }
    .Gold { border-color: var(--gold) !important; }

    /* Spinner */
    .spinner-wrap { position: relative; width: 100%; height: 120px; background: #050505; border: 2px solid #222; margin: 20px 0; overflow: hidden; border-radius: 10px; }
    .spinner-line { position: absolute; left: 50%; top: 0; bottom: 0; width: 3px; background: var(--accent); z-index: 10; transform: translateX(-50%); box-shadow: 0 0 10px var(--accent); }
    .spinner-track { display: flex; position: absolute; left: 0; top: 10px; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); will-change: transform; align-items: center; }
    .spinner-item { min-width: 100px; max-width: 100px; height: 100px; margin: 0 5px; background: #111; border-bottom: 4px solid #fff; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 0.5rem; text-align: center; padding: 5px; box-sizing: border-box; flex-shrink: 0; }

    /* Clicker Page */
    .click-area { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 300px; }
    .big-button { width: 180px; height: 180px; background: var(--upgrade-grad); border-radius: 50%; border: 5px solid white; color: white; font-size: 1.5rem; font-weight: bold; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.1s; user-select: none; box-shadow: 0 0 20px rgba(235, 75, 75, 0.5); }
    .big-button:active { transform: scale(0.9); }

    .bottom-nav { position: fixed; bottom: 0; width: 100%; height: 80px; background: #000; display: flex; justify-content: space-around; align-items: center; border-top: 2px solid #222; z-index: 1000; padding: 0 5px; box-sizing: border-box; }
    .nav-item { flex: 1; margin: 0 2px; height: 50px; background: var(--upgrade-grad); color: white !important; font-weight: bold; border-radius: 10px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 0.55rem; text-transform: uppercase; text-align: center; border: 1px solid var(--red); }
    .nav-item.active { filter: brightness(1.4); border-color: #fff; }
    
    /* Red Upgrade Button Styling */
    .nav-item.red-btn { background: var(--upgrade-grad); border: 2px solid var(--red); box-shadow: 0 0 10px rgba(235,75,75,0.3); }

    .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 2000; display: none; justify-content: center; align-items: center; }
    .modal-box { background: #111; border: 1px solid #333; padding: 20px; border-radius: 12px; width: 90%; max-width: 450px; text-align: center; max-height: 80vh; overflow-y: auto; }
    
    #admin-btn-trigger { position: fixed; bottom: 90px; right: 10px; color: #333; font-size: 0.6rem; cursor: pointer; z-index: 2000; border: none; background: transparent; }
    
    #admin-label { display: none; color: var(--red); font-size: 2.5rem; font-weight: 900; margin-bottom: 10px; text-align: center; width: 100%; }
  </style>
</head>
<body>

<button id="admin-btn-trigger" onclick="adminAuth()">админ</button>

<div id="auth-screen">
  <div class="auth-box">
    <h2 style="color:var(--accent); margin-bottom:15px;">CS2 SIMULATOR</h2>
    <div class="auth-tabs">
      <div id="tab-login" class="auth-tab active" onclick="switchAuth('login')">ВХОД</div>
      <div id="tab-reg" class="auth-tab" onclick="switchAuth('reg')">РЕГИСТРАЦИЯ</div>
    </div>
    <input type="text" id="user-nick" placeholder="Никнейм">
    <input type="password" id="user-pass" placeholder="Пароль">
    <button id="auth-btn" class="btn" style="width:100%" onclick="handleAuth()">Войти</button>
  </div>
</div>

<div class="header">
  <div id="display-name">...</div>
  <div class="balance"><span id="balance-val">0</span> ₽</div>
</div>

<!-- Admin Modal -->
<div id="admin-modal" class="modal">
  <div class="modal-box">
    <h3 style="color:var(--red)">ADMIN PANEL</h3>
    <input type="text" id="adm-target" placeholder="Ник игрока">
    <div style="display:flex; gap:5px; margin-bottom:10px;">
      <input type="number" id="adm-amount" placeholder="Сумма" style="margin:0;">
      <button class="btn" onclick="adminModifyBalance('add')">+</button>
      <button class="btn" onclick="adminModifyBalance('set')">=</button>
    </div>
    <p>Выдать скин:</p>
    <select id="adm-item-select"></select>
    <button class="btn" style="width:100%; background:#fff; color:#000; margin-bottom:10px;" onclick="adminGiveItem()">ВЫДАТЬ</button>
    <button class="btn" style="width:100%; background:#222; color:#fff" onclick="closeModal('admin-modal')">ЗАКРЫТЬ</button>
  </div>
</div>

<!-- Promo Modal -->
<div id="promo-modal" class="modal">
  <div class="modal-box">
    <h3>Введите промокод</h3>
    <input type="text" id="promo-input" placeholder="Код...">
    <button class="btn" style="width:100%" onclick="applyPromo()">АКТИВИРОВАТЬ</button>
    <button class="btn" style="width:100%; background:#222; margin-top:5px;" onclick="closeModal('promo-modal')">ОТМЕНА</button>
  </div>
</div>

<!-- Case Opening Modal -->
<div id="case-modal" class="modal">
  <div class="modal-box">
    <h2 id="m-title">Кейс</h2>
    <div id="m-view">
      <div id="m-contents" class="inv-grid" style="max-height:200px; overflow-y:auto; background:#050505; padding:10px; margin-bottom:15px;"></div>
      <button class="btn" id="m-open-btn" style="width:100%">ОТКРЫТЬ</button>
      <button class="btn" style="background:#222; color:#fff; width:100%; margin-top:5px;" onclick="closeModal('case-modal')">НАЗАД</button>
    </div>
    <div id="m-anim" style="display:none;">
      <div class="spinner-wrap">
        <div class="spinner-line"></div>
        <div class="spinner-track" id="spinner-track"></div>
      </div>
      <div id="m-win-info" style="margin-top:10px; font-weight:bold; min-height:40px;"></div>
      <button class="btn" id="m-claim-btn" style="width:100%; display:none;" onclick="closeModal('case-modal')">ЗАБРАТЬ</button>
    </div>
  </div>
</div>

<!-- Pages -->
<div id="page-cases" class="page active">
  <h3>Магазин</h3>
  <div class="case-grid">
    <div class="card" onclick="showCaseInfo('Snakebite')">
      <p>Snakebite Case<br>100 ₽</p>
    </div>
    <div class="card" onclick="showCaseInfo('Recoil')">
      <p>Recoil Case<br>250 ₽</p>
    </div>
    <div class="card" onclick="showCaseInfo('Revolution')">
      <p>Revolution<br>500 ₽</p>
    </div>
    <div class="card" onclick="showCaseInfo('Knives')">
      <p>Knives<br>15 000 ₽</p>
    </div>
  </div>
</div>

<div id="page-clicker" class="page">
  <div class="click-area">
    <div class="big-button" onclick="clickMoney()">ПОЛУЧИТЬ 0.1</div>
  </div>
</div>

<div id="page-upgrade" class="page">
  <h3>Upgrade</h3>
  <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
    <div>
      <p>Ваш предмет:</p>
      <div id="upg-my-item" class="skin-card" style="height:60px; line-height:60px;">Выбери предмет</div>
    </div>
    <div>
      <p>Цель:</p>
      <div id="upg-target-item" class="skin-card" style="height:60px; line-height:60px;">Выбери цель</div>
    </div>
  </div>
  <div id="upg-chance" style="text-align:center; font-size:1.5rem; margin:15px 0; color:var(--gold);">Шанс: 0%</div>
  <button class="btn" style="width:100%; height:50px;" onclick="runUpgrade()">УЛУЧШИТЬ</button>
  <hr style="border:1px solid #222; margin:15px 0;">
  <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
    <div id="upg-inv-list" class="inv-grid" style="max-height:200px; overflow-y:auto;"></div>
    <div id="upg-pool-list" class="inv-grid" style="max-height:200px; overflow-y:auto;"></div>
  </div>
</div>

<div id="page-contract" class="page">
  <h3>Контракт (3+ предмета)</h3>
  <div id="contract-selection" class="inv-grid" style="margin-bottom:15px; max-height:250px; overflow-y:auto; background:#111; padding:10px;"></div>
  <button class="btn" style="width:100%; height:50px;" onclick="runContract()">ОБМЕНЯТЬ</button>
</div>

<div id="page-inventory" class="page">
  <div id="admin-label">ADMIN</div>
  <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
    <h3>Инвентарь</h3>
    <div style="display:flex; gap:5px;">
      <button class="btn" style="background:var(--red); color:white;" onclick="openPromoModal()">ПРОМОКОД</button>
      <button class="btn" style="background:red;" onclick="sellAll()">ПРОДАТЬ ВСЁ</button>
    </div>
  </div>
  <div id="inventory-grid" class="inv-grid"></div>
</div>

<div class="bottom-nav">
  <div class="nav-item active" onclick="showPage('cases', this)">КЕЙСЫ</div>
  <div class="nav-item" onclick="showPage('clicker', this)">КЛИКЕР</div>
  <div class="nav-item red-btn" onclick="showPage('upgrade', this)">UPGRADE</div>
  <div class="nav-item" onclick="showPage('contract', this)">КОНТРАКТ</div>
  <div class="nav-item" onclick="showPage('inventory', this)">ПРОФИЛЬ</div>
</div>

<script>
  const caseData = {
    'Snakebite': { price: 100, skins: [
      { name: "SG 553 | Heavy Metal", price: 50, rarity: "Mil-Spec" },
      { name: "USP-S | The Traitor", price: 1500, rarity: "Covert" }
    ]},
    'Recoil': { price: 250, skins: [
      { name: "AK-47 | Ice Coaled", price: 800, rarity: "Classified" },
      { name: "AWP | Chromatic Aberration", price: 2200, rarity: "Covert" }
    ]},
    'Revolution': { price: 500, skins: [
      { name: "AK-47 | Slate", price: 350, rarity: "Mil-Spec" },
      { name: "M4A4 | Temukau", price: 5000, rarity: "Covert" },
      { name: "M4A1-S | Printstream", price: 15000, rarity: "Covert" }
    ]},
    'Knives': { price: 15000, skins: [
      { name: "★ Karambit | Doppler", price: 120000, rarity: "Gold" },
      { name: "★ Butterfly | Lore", price: 250000, rarity: "Gold" }
    ]}
  };

  let allSkinsPool = [];
  for(let c in caseData) allSkinsPool = allSkinsPool.concat(caseData[c].skins);

  let user = null;
  let usersDB = JSON.parse(localStorage.getItem('cs_db_v32') || '{}');
  let contractSelected = [];
  let upgMy = null;
  let upgTarget = null;

  function switchAuth(mode) {
    document.querySelectorAll('.auth-tab').forEach(t => t.classList.remove('active'));
    document.getElementById('tab-' + mode).classList.add('active');
    document.getElementById('auth-btn').innerText = mode === 'login' ? 'Войти' : 'Создать';
    document.getElementById('auth-btn').onclick = () => handleAuth(mode);
  }
  switchAuth('login');

  function handleAuth(mode) {
    const n = document.getElementById('user-nick').value.trim();
    const p = document.getElementById('user-pass').value.trim();
    if(!n || !p) return alert("Заполни поля!");
    if(mode === 'login') {
      if(usersDB[n] && usersDB[n].password === p) { user = usersDB[n]; loginSuccess(); }
      else alert("Ошибка!");
    } else {
      if(usersDB[n]) return alert("Ник занят!");
      usersDB[n] = { nick: n, password: p, balance: 1000, inventory: [], usedPromos: [] };
      user = usersDB[n]; save(); loginSuccess();
    }
  }

  function loginSuccess() {
    document.getElementById('auth-screen').style.display = 'none';
    document.getElementById('display-name').innerText = user.nick;
    
    // Check for T3DO Admin label
    if(user.nick === 'T3DO' && user.password === '1235') {
      document.getElementById('admin-label').style.display = 'block';
    } else {
      document.getElementById('admin-label').style.display = 'none';
    }

    initAdminSelect();
    updateUI();
  }

  function save() { if(user) { usersDB[user.nick] = user; localStorage.setItem('cs_db_v32', JSON.stringify(usersDB)); } }
  function updateUI() { document.getElementById('balance-val').innerText = user.balance.toFixed(1); save(); }

  function showPage(id, el) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById('page-' + id).classList.add('active');
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    el.classList.add('active');
    if(id === 'inventory') renderInv();
    if(id === 'contract') renderContractSelection();
    if(id === 'upgrade') renderUpgradePage();
  }

  function clickMoney() { user.balance += 0.1; updateUI(); }

  // --- CASE LOGIC ---
  function showCaseInfo(name) {
    const c = caseData[name];
    document.getElementById('m-title').innerText = name;
    document.getElementById('m-contents').innerHTML = c.skins.map(s => `<div class="skin-card ${s.rarity}">${s.name}</div>`).join('');
    document.getElementById('m-open-btn').innerText = `ОТКРЫТЬ (${c.price}₽)`;
    document.getElementById('m-open-btn').onclick = () => startOpen(name);
    document.getElementById('m-view').style.display = 'block';
    document.getElementById('m-anim').style.display = 'none';
    document.getElementById('case-modal').style.display = 'flex';
  }

  function startOpen(name) {
    const c = caseData[name];
    if(user.balance < c.price) return alert("Мало денег!");
    user.balance -= c.price; updateUI();
    document.getElementById('m-view').style.display = 'none';
    document.getElementById('m-anim').style.display = 'block';
    document.getElementById('m-claim-btn').style.display = 'none';
    document.getElementById('m-win-info').innerText = "";

    const track = document.getElementById('spinner-track');
    track.style.transition = 'none';
    track.style.transform = 'translateX(0px)';
    
    let items = [];
    for(let i=0; i<40; i++) items.push(c.skins[Math.floor(Math.random()*c.skins.length)]);
    const winItem = items[35];

    track.innerHTML = items.map(s => `<div class="spinner-item ${s.rarity}">${s.name}</div>`).join('');

    setTimeout(() => {
      track.style.transition = 'transform 5s cubic-bezier(0.1, 0, 0.1, 1)';
      track.style.transform = 'translateX(-3400px)';
    }, 50);

    setTimeout(() => {
      user.inventory.push({...winItem, id: Date.now() + Math.random()});
      document.getElementById('m-win-info').innerText = "ВЫПАЛО: " + winItem.name;
      document.getElementById('m-claim-btn').style.display = 'block';
      updateUI();
    }, 5200);
  }

  // --- UPGRADE LOGIC ---
  function renderUpgradePage() {
    const invList = document.getElementById('upg-inv-list');
    invList.innerHTML = user.inventory.map(s => `<div class="skin-card ${s.rarity}" onclick="setUpMy('${s.id}')">${s.name}</div>`).join('');
    
    const poolList = document.getElementById('upg-pool-list');
    poolList.innerHTML = allSkinsPool.map((s, idx) => `<div class="skin-card ${s.rarity}" onclick="setUpTarget(${idx})">${s.name}</div>`).join('');
    updateUpgradeUI();
  }

  function setUpMy(id) { upgMy = user.inventory.find(i => i.id == id); updateUpgradeUI(); }
  function setUpTarget(idx) { upgTarget = allSkinsPool[idx]; updateUpgradeUI(); }

  function updateUpgradeUI() {
    document.getElementById('upg-my-item').innerText = upgMy ? upgMy.name : "Выбери предмет";
    document.getElementById('upg-target-item').innerText = upgTarget ? upgTarget.name : "Выбери цель";
    if(upgMy && upgTarget) {
      let chance = (upgMy.price / upgTarget.price) * 100;
      if(chance > 95) chance = 95;
      document.getElementById('upg-chance').innerText = "Шанс: " + chance.toFixed(1) + "%";
    } else {
      document.getElementById('upg-chance').innerText = "Шанс: 0%";
    }
  }

  function runUpgrade() {
    if(!upgMy || !upgTarget) return alert("Выбери оба предмета!");
    let chance = (upgMy.price / upgTarget.price) * 100;
    if(chance > 95) chance = 95;
    
    user.inventory = user.inventory.filter(i => i.id != upgMy.id);
    if(Math.random() * 100 <= chance) {
      user.inventory.push({...upgTarget, id: Date.now()});
      alert("УСПЕХ! Получено: " + upgTarget.name);
    } else {
      alert("НЕУДАЧА! Предмет потерян.");
    }
    upgMy = null;
    updateUpgradeUI();
    renderUpgradePage();
    updateUI();
  }

  // --- PROFILE & INVENTORY ---
  function renderInv() {
    const grid = document.getElementById('inventory-grid');
    grid.innerHTML = user.inventory.map(s => `
      <div class="skin-card ${s.rarity}">
        ${s.name}<br>${s.price}₽<br>
        <button class="btn" style="padding:2px 5px; font-size:10px; margin-top:5px; background:#444;" onclick="sellItem('${s.id}')">ПРОДАТЬ</button>
      </div>
    `).join('');
  }

  function sellItem(id) {
    const idx = user.inventory.findIndex(i => i.id == id);
    if(idx > -1) {
      user.balance += user.inventory[idx].price;
      user.inventory.splice(idx, 1);
      updateUI(); renderInv();
    }
  }

  function sellAll() {
    user.inventory.forEach(s => user.balance += s.price);
    user.inventory = [];
    updateUI(); renderInv();
  }

  // --- CONTRACTS ---
  function renderContractSelection() {
    contractSelected = [];
    const grid = document.getElementById('contract-selection');
    grid.innerHTML = user.inventory.map(s => `
      <div class="skin-card ${s.rarity}" onclick="toggleContract(this, '${s.id}')">${s.name}</div>
    `).join('');
  }

  function toggleContract(el, id) {
    const idx = contractSelected.indexOf(id);
    if(idx > -1) { contr
