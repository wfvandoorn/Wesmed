<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<meta name="theme-color" content="#0f172a" />
<title>WesMed — Persoonlijke medicatie tools</title>
<style>
  :root {
    --bg:#0b1220; --bg2:#0f172a; --line:#1f2937; --line2:#233046;
    --text:#e5e7eb; --muted:#94a3b8; --accent:#22c55e; --warn:#f59e0b; --danger:#ef4444;
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0; font-family:system-ui,-apple-system,Segoe UI,Roboto,sans-serif; color:var(--text);
    background:
      radial-gradient(1200px 800px at 85% -10%, #0b4aa8 0, rgba(11,74,168,0) 60%),
      radial-gradient(900px 700px at -10% 110%, #0a7a8f 0, rgba(10,122,143,0) 58%),
      linear-gradient(180deg, var(--bg) 0%, var(--bg2) 100%);
    display:flex; flex-direction:column;
  }
  header{
    position:sticky; top:0; z-index:10;
    display:flex; align-items:center; justify-content:space-between;
    padding:12px 16px; border-bottom:1px solid var(--line);
    background:rgba(8,13,24,0.75); backdrop-filter: blur(8px);
  }
  .brand{display:flex; align-items:center; gap:10px}
  .logo{
    width:28px; height:28px; border-radius:8px; display:inline-block;
    background:linear-gradient(180deg,#1e40af,#60a5fa);
    position:relative; box-shadow:0 0 0 1px rgba(255,255,255,0.06) inset, 0 6px 18px rgba(37,99,235,.25);
  }
  .logo::before{
    content:""; position:absolute; inset:5px;
    background:
      radial-gradient(circle at 50% 35%, #fff 0 40%, transparent 41%),
      radial-gradient(circle at 50% 65%, #fff 0 40%, transparent 41%),
      radial-gradient(circle at 35% 50%, #fff 0 40%, transparent 41%),
      radial-gradient(circle at 65% 50%, #fff 0 40%, transparent 41%);
    mask: radial-gradient(100% 100% at 50% 50%, #000 70%, transparent 71%);
    border-radius:6px;
  }
  .title{font-weight:800; letter-spacing:.2px}
  .title span{opacity:.9; font-weight:400}
  main{flex:1; display:grid; grid-template-columns:460px 1fr; gap:16px; padding:16px}
  @media (max-width: 1080px){ main{grid-template-columns:1fr} }
  .card{
    background:rgba(10,16,28,0.75);
    border:1px solid var(--line); border-radius:14px; padding:12px;
    box-shadow:0 20px 60px rgba(0,0,0,.25), inset 0 1px 0 rgba(255,255,255,.02);
    display:flex; flex-direction:column; gap:10px;
  }
  .section{font-size:14px; color:var(--muted); text-transform:uppercase; letter-spacing:.06em; margin:2px 0 6px}
  label{font-size:13px; color:var(--muted)}
  input, select, textarea{
    width:100%; background:#0b1220; color:var(--text); border:1px solid var(--line);
    border-radius:10px; padding:9px 10px; font-size:14px; outline:none;
  }
  input:focus, select:focus, textarea:focus{border-color:#2b3b54; box-shadow:0 0 0 3px rgba(37,99,235,.15)}
  button{
    background:#0b1220; color:var(--text); border:1px solid var(--line); padding:9px 12px;
    border-radius:10px; cursor:pointer; font-weight:700; letter-spacing:.2px;
  }
  button.primary{background:var(--accent); color:#052e16; border:none}
  button:disabled{opacity:.6; cursor:not-allowed}
  .row{display:flex; gap:10px; align-items:center; flex-wrap:wrap}
  .out{background:#0b1220; border:1px dashed var(--line2); border-radius:10px; padding:10px; white-space:pre-wrap}
  .note{color:var(--muted); font-size:12px}
  .warn{padding:10px; border-left:3px solid var(--warn); background:#1a1405; color:#f8e6be; border-radius:8px}
  .danger{padding:10px; border-left:3px solid var(--danger); background:#2a0c0c; color:#ffd4d4; border-radius:8px}
  table{width:100%; border-collapse:collapse; font-size:13px}
  th,td{border-bottom:1px solid var(--line2); padding:8px; text-align:left; vertical-align:top}
  .footer{padding:10px 16px; color:var(--muted); font-size:12px; border-top:1px solid var(--line); text-align:center}
  .copybtn{border:none; background:#1e293b; color:#dbeafe; padding:6px 10px; border-radius:8px; cursor:pointer}
  .disabled{opacity:.55; pointer-events:none}
</style>
</head>
<body>
  <header>
    <div class="brand">
      <span class="logo" aria-hidden="true"></span>
      <div class="title">WesMed <span>— Persoonlijke medicatie tools</span></div>
    </div>
  </header>

  <main>
    <!-- Linker kolom: Admin & Sets -->
    <section class="card" id="leftPanel">
      <div class="section">Admin en API keys</div>
      <div class="row">
        <div style="flex:1">
          <label for="adminToken">Admin token</label>
          <input id="adminToken" type="text" readonly />
        </div>
        <button id="copyAdmin" class="copybtn" title="Kopieer admin token">Kopieer</button>
        <button id="regenAdmin" title="Nieuw admin token">Nieuw token</button>
      </div>
      <div class="note">Token = jouw beheersleutel. Gebruik dit om keys aan te maken of in te zien.</div>

      <div class="row">
        <div style="flex:1;min-width:200px">
          <label for="scopes">Scopes (komma‑gescheiden)</label>
          <input id="scopes" value="convert,*" />
        </div>
        <div style="width:140px">
          <label for="rpm">Limiet p/min</label>
          <input id="rpm" type="number" min="1" value="60" />
        </div>
      </div>
      <div class="row">
        <button id="genKey" class="primary">Genereer API key</button>
        <button id="listKeys">Toon keys</button>
      </div>
      <div id="keysOut" class="out" aria-live="polite"></div>

      <div class="section">Equivalente sets</div>
      <div class="row">
        <select id="setSelect"></select>
        <button id="applySet">Set toepassen</button>
        <button id="saveCustomSet">Sla ‘Mijn set’ op</button>
      </div>
      <div class="note">Actieve set wordt gebruikt bij omrekenen. Je kunt een eigen set opslaan.</div>
      <table>
        <thead><tr><th>Middel</th><th>Type</th><th>Diazepam/1mg</th><th>≈10mg diazepam</th></tr></thead>
        <tbody id="tblBody"></tbody>
      </table>
      <div class="warn">Informatief; bevestig klinisch met arts/apotheker.</div>
    </section>

    <!-- Rechter kolom: API Explorer & Leverbaarheid -->
    <section class="card" id="rightPanel">
      <div class="section">API Explorer</div>
      <div class="row">
        <div style="flex:1">
          <label for="clientKey">X‑API‑Key</label>
          <input id="clientKey" placeholder="Plak je API key" />
        </div>
        <button id="copyKey" class="copybtn">Kopieer key</button>
      </div>
      <div class="row">
        <div style="flex:1;min-width:200px">
          <label for="fromDrug">Van</label>
          <select id="fromDrug"></select>
        </div>
        <div style="width:140px">
          <label for="fromDose">Dosis (mg)</label>
          <input id="fromDose" type="number" min="0" step="0.1" value="1.0" />
        </div>
      </div>
      <div class="row">
        <div style="flex:1;min-width:200px">
          <label for="toDrug">Naar</label>
          <select id="toDrug"></select>
        </div>
        <div style="width:160px">
          <label for="rounding">Afronding</label>
          <select id="rounding">
            <option value="0.1">0,1 mg</option>
            <option value="0.25">0,25 mg</option>
            <option value="0.5" selected>0,5 mg</option>
            <option value="1">1 mg</option>
            <option value="2">2 mg</option>
          </select>
        </div>
      </div>
      <div class="row">
        <button id="callConvert" class="primary">POST /v1/convert</button>
        <button id="callChat">POST /v1/chat</button>
      </div>
      <div id="apiOut" class="out" aria-live="polite"></div>

      <div class="section">Leverbaarheid (NL)</div>
      <div class="row">
        <div style="flex:1;min-width:220px">
          <label for="supplyName">Naam (generiek/merk)</label>
          <input id="supplyName" placeholder="bijv. oxazepam of Seresta" />
        </div>
        <div style="width:200px">
          <label for="supplyForm">Sterkte/vorm (optioneel)</label>
          <input id="supplyForm" placeholder="bijv. 10 mg tablet" />
        </div>
      </div>
      <div class="row">
        <a id="openCBG" href="#">CBG</a>
        <a id="openShort" href="#">Tekorten/reden</a>
        <a id="openApotheek" href="#">Apotheek.nl</a>
        <a id="openKosten" href="#">Medicijnkosten</a>
      </div>
      <div class="note">Links openen publieke NL-bronnen.</div>

      <div class="danger">Geen individuele dosis‑adviezen. Wisselen/afbouwen vereist klinisch oordeel.</div>
    </section>
  </main>

  <div class="footer">
    WesMed — lokaal, gratis, zonder accounts. Gebruik verantwoord; raadpleeg je apotheker/arts.
  </div>

<script>
document.addEventListener('DOMContentLoaded', () => {
  // ---------- Data ----------
  const DRUGS = [
    {id:"alprazolam", name:"Alprazolam", type:"BZD"},
    {id:"bromazepam", name:"Bromazepam", type:"BZD"},
    {id:"brotizolam", name:"Brotizolam", type:"BZD"},
    {id:"chlordiazepoxide", name:"Chlordiazepoxide", type:"BZD"},
    {id:"clobazam", name:"Clobazam", type:"BZD"},
    {id:"clonazepam", name:"Clonazepam", type:"BZD"},
    {id:"cloxazolam", name:"Cloxazolam", type:"BZD"},
    {id:"diazepam", name:"Diazepam", type:"BZD"},
    {id:"estazolam", name:"Estazolam", type:"BZD"},
    {id:"flunitrazepam", name:"Flunitrazepam", type:"BZD"},
    {id:"flurazepam", name:"Flurazepam", type:"BZD"},
    {id:"ketazolam", name:"Ketazolam", type:"BZD"},
    {id:"loprazolam", name:"Loprazolam", type:"BZD"},
    {id:"lorazepam", name:"Lorazepam", type:"BZD"},
    {id:"lormetazepam", name:"Lormetazepam", type:"BZD"},
    {id:"medazepam", name:"Medazepam", type:"BZD"},
    {id:"midazolam", name:"Midazolam", type:"BZD"},
    {id:"nitrazepam", name:"Nitrazepam", type:"BZD"},
    {id:"nordazepam", name:"Nordazepam", type:"BZD"},
    {id:"oxazepam", name:"Oxazepam", type:"BZD"},
    {id:"prazepam", name:"Prazepam", type:"BZD"},
    {id:"temazepam", name:"Temazepam", type:"BZD"},
    {id:"triazolam", name:"Triazolam", type:"BZD"},
    {id:"zaleplon", name:"Zaleplon", type:"Z"},
    {id:"zolpidem", name:"Zolpidem", type:"Z"},
    {id:"zopiclone", name:"Zopiclon", type:"Z"}
  ];

  const EQ_SETS = {
    "Standaard (praktijk)": {
      alprazolam:20,bromazepam:2,brotizolam:20,chlordiazepoxide:0.4,clobazam:0.5,clonazepam:20,cloxazolam:2,
      diazepam:1,estazolam:10,flunitrazepam:10,flurazepam:0.5,ketazolam:0.5,loprazolam:10,lorazepam:10,
      lormetazepam:10,medazepam:0.5,midazolam:0.67,nitrazepam:1,nordazepam:1,oxazepam:0.5,prazepam:0.5,
      temazepam:0.5,triazolam:20,zaleplon:0.5,zolpidem:1,zopiclone:1.33
    },
    "Conservatief (lager effect)": {
      alprazolam:18,bromazepam:1.8,brotizolam:18,chlordiazepoxide:0.35,clobazam:0.45,clonazepam:18,cloxazolam:1.8,
      diazepam:1,estazolam:9,flunitrazepam:9,flurazepam:0.45,ketazolam:0.45,loprazolam:9,lorazepam:9,
      lormetazepam:9,medazepam:0.45,midazolam:0.6,nitrazepam:0.9,nordazepam:1,oxazepam:0.45,prazepam:0.45,
      temazepam:0.45,triazolam:18,zaleplon:0.45,zolpidem:0.9,zopiclone:1.2
    },
    "Alternatief (hogere schatting)": {
      alprazolam:22,bromazepam:2.2,brotizolam:22,chlordiazepoxide:0.45,clobazam:0.55,clonazepam:22,cloxazolam:2.2,
      diazepam:1,estazolam:11,flunitrazepam:11,flurazepam:0.55,ketazolam:0.55,loprazolam:11,lorazepam:11,
      lormetazepam:11,medazepam:0.55,midazolam:0.75,nitrazepam:1.1,nordazepam:1,oxazepam:0.55,prazepam:0.55,
      temazepam:0.55,triazolam:22,zaleplon:0.55,zolpidem:1.1,zopiclone:1.45
    }
  };

  // ---------- Storage (lokaal) ----------
  const LS = {
    ADMIN: 'wesmed_admin_token',
    KEYS: 'wesmed_api_keys',        // array van key-records
    LAST_KEY: 'wesmed_last_key',
    ACTIVE_SET: 'wesmed_active_set',
    CUSTOM_SET: 'wesmed_custom_set'
  };

  const getKeys = () => JSON.parse(localStorage.getItem(LS.KEYS) || '[]');
  const setKeys = arr => localStorage.setItem(LS.KEYS, JSON.stringify(arr));

  function ensureAdminToken() {
    let tok = localStorage.getItem(LS.ADMIN);
    if (!tok) {
      tok = randomHex(32);
      localStorage.setItem(LS.ADMIN, tok);
    }
    return tok;
  }

  function randomHex(bytes) {
    const a = new Uint8Array(bytes);
    crypto.getRandomValues(a);
    return Array.from(a).map(b => ('0'+b.toString(16)).slice(-2)).join('');
  }

  function normalizeScopes(scopes) {
    if (Array.isArray(scopes)) return scopes.map(s=>String(s).trim()).filter(Boolean);
    return String(scopes || '*').split(',').map(s=>s.trim()).filter(Boolean);
  }

  function createApiKey({ scopes='*', rpm=60, expiresAt=null }) {
    const key = randomHex(32);
    const rec = {
      key,
      scopes: normalizeScopes(scopes),
      rpm: Number(rpm)||60,
      createdAt: Date.now(),
      expiresAt: expiresAt || null,
      active: true,
      _rate: { count: 0, windowStart: 0 }
    };
    const list = getKeys();
    list.push(rec);
    setKeys(list);
    localStorage.setItem(LS.LAST_KEY, key);
    return rec;
  }

  function listApiKeys() {
    return getKeys();
  }

  function revokeApiKey(key) {
    const list = getKeys();
    const i = list.findIndex(k => k.key === key);
    if (i >= 0) {
      list[i].active = false;
      setKeys(list);
      return true;
    }
    return false;
  }

  function getKeyRecord(key) {
    return getKeys().find(k => k.key === key) || null;
  }

  function hasScope(rec, scope) {
    const scopes = normalizeScopes(rec.scopes);
    return scopes.includes('*') || scopes.includes(scope);
  }

  function withinRate(rec) {
    const now = Date.now();
    const rpm = Number(rec.rpm)||60;
    if (!rec._rate) rec._rate = { count: 0, windowStart: 0 };
    if ((now - rec._rate.windowStart) >= 60000) {
      rec._rate.count = 0;
      rec._rate.windowStart = now;
    }
    rec._rate.count += 1;
    // schrijf terug in storage
    const list = getKeys();
    const idx = list.findIndex(k => k.key === rec.key);
    if (idx >= 0) { list[idx]._rate = rec._rate; setKeys(list); }
    return rec._rate.count <= rpm;
  }

  // ---------- Equivalente sets ----------
  let currentSetName = localStorage.getItem(LS.ACTIVE_SET) || 'Standaard (praktijk)';
  let currentSet = JSON.parse(JSON.stringify(EQ_SETS[currentSetName]));

  function saveCustomSet(set) {
    localStorage.setItem(LS.CUSTOM_SET, JSON.stringify(set));
  }
  function loadCustomSet() {
    const s = localStorage.getItem(LS.CUSTOM_SET);
    return s ? JSON.parse(s) : null;
  }

  // ---------- UI refs ----------
  const adminTokenEl = document.getElementById('adminToken');
  const copyAdmin = document.getElementById('copyAdmin');
  const regenAdmin = document.getElementById('regenAdmin');
  const scopesEl = document.getElementById('scopes');
  const rpmEl = document.getElementById('rpm');
  const genKeyBtn = document.getElementById('genKey');
  const listKeysBtn = document.getElementById('listKeys');
  const keysOut = document.getElementById('keysOut');

  const setSelect = document.getElementById('setSelect');
  const applySetBtn = document.getElementById('applySet');
  const saveCustomSetBtn = document.getElementById('saveCustomSet');
  const tblBody = document.getElementById('tblBody');

  const clientKeyEl = document.getElementById('clientKey');
  const copyKeyBtn = document.getElementById('copyKey');
  const fromDrug = document.getElementById('fromDrug');
  const toDrug = document.getElementById('toDrug');
  const fromDose = document.getElementById('fromDose');
  const rounding = document.getElementById('rounding');
  const callConvert = document.getElementById('callConvert');
  const callChat = document.getElementById('callChat');
  const apiOut = document.getElementById('apiOut');

  const supplyName = document.getElementById('supplyName');
  const supplyForm = document.getElementById('supplyForm');
  const openCBG = document.getElementById('openCBG');
  const openShort = document.getElementById('openShort');
  const openApotheek = document.getElementById('openApotheek');
  const openKosten = document.getElementById('openKosten');

  // ---------- Helpers ----------
  async function copyText(text) {
    try {
      if (navigator.clipboard && window.isSecureContext !== false) {
        await navigator.clipboard.writeText(text);
        return true;
      }
    } catch {}
    const ta = document.createElement('textarea');
    ta.value = text; ta.style.position='fixed'; ta.style.left='-9999px';
    document.body.appendChild(ta); ta.focus(); ta.select();
    const ok = document.execCommand('copy');
    document.body.removeChild(ta);
    return ok;
  }

  function basisEquivalentFromDz(dz) {
    const eq10 = 10 / dz;
    const step = eq10 < 1 ? 0.25 : (eq10 < 5 ? 0.5 : 1);
    return Math.round(eq10 / step) * step;
  }

  function populateDrugSelects() {
    for (const d of DRUGS) {
      const o1 = document.createElement('option'); o1.value=d.id; o1.textContent=d.name; fromDrug.appendChild(o1);
      const o2 = document.createElement('option'); o2.value=d.id; o2.textContent=d.name; toDrug.appendChild(o2);
    }
    fromDrug.value = 'lorazepam';
    toDrug.value = 'oxazepam';
  }

  function populateSets() {
    setSelect.innerHTML = '';
    Object.keys(EQ_SETS).forEach(name => {
      const o = document.createElement('option'); o.value = name; o.textContent = name; setSelect.appendChild(o);
    });
    if (loadCustomSet()) {
      const o = document.createElement('option'); o.value = 'Mijn set'; o.textContent = 'Mijn set'; setSelect.appendChild(o);
    }
    if (!EQ_SETS[currentSetName] && currentSetName !== 'Mijn set') currentSetName = 'Standaard (praktijk)';
    setSelect.value = currentSetName;
  }

  function renderSetTable(setObj) {
    tblBody.innerHTML = '';
    for (const d of DRUGS) {
      const dz = setObj[d.id] ?? 1;
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${d.name}</td>
        <td>${d.type==='BZD'?'Benzodiazepine':'Z‑drug'}</td>
        <td><input data-id="${d.id}" type="number" step="0.01" min="0.01" value="${dz}" style="width:120px"/></td>
        <td class="note">${basisEquivalentFromDz(dz)} mg ${d.name}</td>
      `;
      tblBody.appendChild(tr);
    }
    // changes
    tblBody.querySelectorAll('input[type=number]').forEach(inp => {
      inp.addEventListener('change', () => {
        const id = inp.dataset.id; const val = Number(inp.value);
        if (val > 0) { currentSet[id] = val; renderSetTable(currentSet); }
      });
    });
  }

  function applyActiveSetName(name) {
    if (name === 'Mijn set') {
      const custom = loadCustomSet();
      currentSet = custom ? custom : JSON.parse(JSON.stringify(EQ_SETS['Standaard (praktijk)']));
    } else {
      currentSet = JSON.parse(JSON.stringify(EQ_SETS[name]));
    }
    currentSetName = name;
    localStorage.setItem(LS.ACTIVE_SET, name);
    renderSetTable(currentSet);
  }

  // ---------- Endpoints (lokaal gesimuleerd) ----------
  function ep_convert(apiKey, body) {
    const rec = getKeyRecord(apiKey);
    if (!rec) return { status: 403, json: { error: 'Ongeldige key' } };
    if (!rec.active) return { status: 403, json: { error: 'Key ingetrokken' } };
    if (rec.expiresAt && Date.now() > rec.expiresAt) return { status: 403, json: { error: 'Key verlopen' } };
    if (!withinRate(rec)) return { status: 429, json: { error: 'Rate limit overschreden' } };
    if (!hasScope(rec, 'convert')) return { status: 403, json: { error: 'Scope ontbreekt: convert' } };

    // setName (optioneel) om tijdens call set te wisselen
    if (body && body.setName) {
      if (EQ_SETS[body.setName]) { currentSet = JSON.parse(JSON.stringify(EQ_SETS[body.setName])); currentSetName = body.setName; localStorage.setItem(LS.ACTIVE_SET, currentSetName); }
      else if (body.setName === 'Mijn set' && loadCustomSet()) { currentSet = loadCustomSet(); currentSetName = 'Mijn set'; localStorage.setItem(LS.ACTIVE_SET, currentSetName); }
    }

    const okDrug = id => DRUGS.some(d => d.id === id);
    if (!body || typeof body.dose !== 'number' || body.dose <= 0 || !okDrug(body.fromDrug) || !okDrug(body.toDrug)) {
      return { status: 400, json: { error: 'Ongeldige invoer' } };
    }
    const fromDz = currentSet[body.fromDrug] ?? 1;
    const toDz = currentSet[body.toDrug] ?? 1;
    const dzEq = body.dose * fromDz;
    const raw = dzEq / toDz;
    const step = Number(body.rounding) || 0.5;
    const out = Math.max(0, Math.round(raw / step) * step);

    return {
      status: 200,
      json: {
        set: currentSetName,
        steps: [
          `Stap 1 — Diazepam-equivalent: ${body.dose} mg ${body.fromDrug} × ${fromDz} = ${dzEq.toFixed(2)} mg`,
          `Stap 2 — Doeldrug: ${dzEq.toFixed(2)} mg ÷ ${toDz} = ${raw.toFixed(2)} mg`,
          `Afronding: ${step} mg-stap → ≈ ${out.toFixed(2)} mg`
        ],
        result: { mg: out, raw },
        note: 'Benadering; geen persoonlijk advies.'
      }
    };
  }

  function ep_chat(apiKey, body) {
    const rec = getKeyRecord(apiKey);
    if (!rec) return { status: 403, json: { error: 'Ongeldige key' } };
    if (!rec.active) return { status: 403, json: { error: 'Key ingetrokken' } };
    if (rec.expiresAt && Date.now() > rec.expiresAt) return { status: 403, json: { error: 'Key verlopen' } };
    if (!withinRate(rec)) return { status: 429, json: { error: 'Rate limit overschreden' } };
    if (!hasScope(rec, 'chat')) return { status: 403, json: { error: 'Scope ontbreekt: chat' } };

    const message = (body && body.message) || '';
    return { status: 200, json: { reply: `[WesMed echo] ${message}` } };
  }

  // ---------- UI events ----------
  // Admin token
  adminTokenEl.value = ensureAdminToken();

  document.getElementById('copyAdmin').addEventListener('click', async () => {
    if (!adminTokenEl.value) return;
    await copyText(adminTokenEl.value);
    copyAdmin.textContent = 'Gekopieerd';
    setTimeout(()=> copyAdmin.textContent='Kopieer', 1200);
  });

  document.getElementById('regenAdmin').addEventListener('click', () => {
    if (!confirm('Nieuw admin token genereren?')) return;
    const tok = randomHex(32);
    localStorage.setItem(LS.ADMIN, tok);
    adminTokenEl.value = tok;
    alert('Nieuw admin token ingesteld.');
  });

  // Keys
  document.getElementById('genKey').addEventListener('click', () => {
    const entered = prompt('Voer je admin token in om een key te genereren:');
    if (!entered || entered !== localStorage.getItem(LS.ADMIN)) {
      alert('Onjuist admin token.');
      return;
    }
    const rec = createApiKey({ scopes: scopesEl.value, rpm: Number(rpmEl.value)||60 });
    keysOut.textContent = JSON.stringify({ ok: true, key: rec }, null, 2);
    clientKeyEl.value = rec.key;
  });

  document.getElementById('listKeys').addEventListener('click', () => {
    const entered = prompt('Voer je admin token in om keys te bekijken:');
    if (!entered || entered !== localStorage.getItem(LS.ADMIN)) {
      alert('Onjuist admin token.');
      return;
    }
    keysOut.textContent = JSON.stringify({ keys: listApiKeys() }, null, 2);
  });

  document.getElementById('copyKey').addEventListener('click', async () => {
    const k = clientKeyEl.value.trim() || localStorage.getItem(LS.LAST_KEY) || '';
    if (!k) { alert('Geen key gevonden'); return; }
    await copyText(k);
    document.getElementById('copyKey').textContent = 'Key gekopieerd';
    setTimeout(()=> document.getElementById('copyKey').textContent='Kopieer key', 1200);
  });

  // Sets + tabel
  function renderSetUI() {
    populateSets();
    applyActiveSetName(setSelect.value);
  }
  document.getElementById('applySet').addEventListener('click', () => applyActiveSetName(setSelect.value));
  document.getElementById('saveCustomSet').addEventListener('click', () => {
    saveCustomSet(currentSet);
    if ([...setSelect.options].every(o => o.value !== 'Mijn set')) {
      const o = document.createElement('option'); o.value='Mijn set'; o.textContent='Mijn set'; setSelect.appendChild(o);
    }
    setSelect.value = 'Mijn set';
    applyActiveSetName('Mijn set');
    alert('Opgeslagen als “Mijn set”.');
  });

  // Drugs UI
  function initDrugsUI() {
    for (const d of DRUGS) {
      const o1 = document.createElement('option'); o1.value=d.id; o1.textContent=d.name; fromDrug.appendChild(o1);
      const o2 = document.createElement('option'); o2.value=d.id; o2.textContent=d.name; toDrug.appendChild(o2);
    }
    fromDrug.value = 'lorazepam';
    toDrug.value = 'oxazepam';
  }

  // API Explorer actions (lokale ep_*)
  document.getElementById('callConvert').addEventListener('click', () => {
    const key = clientKeyEl.value.trim();
    if (!key) { alert('Plak een API key'); return; }
    const body = {
      fromDrug: fromDrug.value,
      toDrug: toDrug.value,
      dose: Number(fromDose.value),
      rounding: Number(rounding.value)
    };
    const res = ep_convert(key, body);
    apiOut.textContent = JSON.stringify(res, null, 2);
  });

  document.getElementById('callChat').addEventListener('click', () => {
    const key = clientKeyEl.value.trim();
    if (!key) { alert('Plak een API key'); return; }
    const res = ep_chat(key, { message: "Hallo WesMed!" });
    apiOut.textContent = JSON.stringify(res, null, 2);
  });

  // Leverbaarheid links
  function openLink(base) {
    const q = encodeURIComponent((supplyName.value + ' ' + supplyForm.value).trim());
    if (!q) { alert('Vul naam/sterkte in.'); return; }
    window.open(base + q, '_blank', 'noopener');
  }
  openCBG.addEventListener('click', e => { e.preventDefault(); openLink('https://www.geneesmiddeleninformatiebank.nl/nl/zoeken?q='); });
  openShort.addEventListener('click', e => { e.preventDefault(); openLink('https://www.farmanco.knmp.nl/tekorten?search='); });
  openApotheek.addEventListener('click', e => { e.preventDefault(); openLink('https://www.apotheek.nl/zoeken?zoekterm='); });
  openKosten.addEventListener('click', e => { e.preventDefault(); openLink('https://www.medicijnkosten.nl/?zoekterm='); });

  // Init
  adminTokenEl.value = ensureAdminToken();
  initDrugsUI();
  renderSetUI();
});
</script>
</body>
</html>
