<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-title" content="MOG 231">
<title>MOG 231 — Project Manager</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&family=Source+Sans+3:wght@300;400;600&display=swap');
  * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
  body { background: #0f1117; font-family: 'Source Sans 3', sans-serif; color: #e8e4dc; min-height: 100vh; }
  input, textarea, button { font-family: inherit; }
  textarea:focus, input:focus { outline: none; border-color: #c8a96e !important; }
  ::-webkit-scrollbar { width: 3px; } ::-webkit-scrollbar-track { background: #0f1117; } ::-webkit-scrollbar-thumb { background: #2a2d3a; border-radius: 2px; }

  /* LAYOUT */
  #app { max-width: 720px; margin: 0 auto; padding: 24px 16px 80px; }

  /* TOAST */
  #toast { position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%) translateY(80px); background: #1e2a18; border: 1px solid #4caf7a44; color: #4caf7a; padding: 10px 20px; border-radius: 20px; font-size: 12px; font-weight: 600; transition: transform 0.3s ease; z-index: 9999; white-space: nowrap; }
  #toast.show { transform: translateX(-50%) translateY(0); }

  /* LOADING */
  #loading { position: fixed; inset: 0; background: #0f1117; display: flex; align-items: center; justify-content: center; z-index: 999; flex-direction: column; gap: 12px; }
  .spinner { width: 28px; height: 28px; border: 2px solid #2a2d3a; border-top-color: #c8a96e; border-radius: 50%; animation: spin 0.8s linear infinite; }
  @keyframes spin { to { transform: rotate(360deg); } }

  /* CARDS */
  .card { background: #1a1d27; border: 1px solid #2a2d3a; border-radius: 10px; overflow: hidden; transition: border-color 0.15s; }
  .card:hover { border-color: #3a3d4a; }
  .card-check { background: #111820; border-color: #1e3a2a; }
  .card-done { opacity: 0.78; }
  .card-locked { opacity: 0.45; background: #131620; border-color: #1e2030; }

  /* STEP accents */
  .accent-left { position: absolute; left: 0; top: 0; bottom: 0; width: 3px; }

  /* TAGS */
  .tag { font-size: 10px; padding: 2px 8px; border-radius: 20px; font-weight: 600; display: inline-block; }
  .tag-doc { background: #1e2535; color: #6a9ad4; border: 1px solid #2a3a55; }
  .tag-ai  { background: #1e1535; color: #9b79d4; border: 1px solid #2d2440; }
  .tag-out { background: #1e2518; color: #6aaa60; border: 1px solid #2a3a20; }
  .tag-warn{ background: #251e10; color: #c49a3a; border: 1px solid #3a2e10; }

  /* BUTTONS */
  .btn { padding: 7px 16px; border-radius: 20px; font-size: 12px; font-weight: 600; cursor: pointer; border: 1px solid; transition: all 0.15s; }
  .btn-green { background: #0d2a18; border-color: #2e7d5244; color: #4caf7a; }
  .btn-green:hover { background: #1a4028; }
  .btn-gold { background: #251e10; border-color: #c8a96e44; color: #c8a96e; }
  .btn-ghost { background: #1a1d27; border-color: #2a2d3a; color: #5a5545; }
  .btn-ghost:hover { border-color: #c8a96e44; color: #c8a96e; }

  /* PROGRESS BAR */
  .progress-track { height: 4px; background: #1e2030; border-radius: 2px; overflow: hidden; }
  .progress-fill { height: 100%; background: linear-gradient(90deg, #c8a96e, #4caf7a); border-radius: 2px; transition: width 0.4s ease; }

  /* AVATAR */
  .av { border-radius: 50%; display: inline-flex; align-items: center; justify-content: center; font-weight: 700; letter-spacing: 0.03em; flex-shrink: 0; position: relative; }

  /* MEMBER PICKER */
  .mpick-btn { display: inline-flex; align-items: center; gap: 5px; padding: 4px 10px; border-radius: 20px; font-size: 11px; font-weight: 600; cursor: pointer; border: 1px solid #2a2d3a; background: #1a1d27; color: #5a5545; transition: all 0.15s; }

  /* SECTIONS */
  .section-label { font-size: 10px; letter-spacing: 0.15em; text-transform: uppercase; font-weight: 600; display: flex; align-items: center; gap: 8px; margin-bottom: 9px; }
  .section-line { height: 1px; flex: 1; }

  /* FIRMA ROW */
  .firma-row { display: flex; align-items: center; gap: 10px; padding: 8px 12px; border-radius: 8px; border: 1px solid #2a2d3a; background: #141720; transition: all 0.2s; }
  .firma-row.signed { background: #0d2a18; border-color: #2e6a3a; }

  /* FILTER TABS */
  .ftab { padding: 5px 14px; border-radius: 20px; font-size: 11px; font-weight: 600; cursor: pointer; border: 1px solid #2a2d3a; background: #1a1d27; color: #5a5545; transition: all 0.15s; }
  .ftab.active { background: #c8a96e22; border-color: #c8a96e; color: #c8a96e; }

  /* HANDOFF BANNER */
  .handoff-banner { padding: 10px 14px; border-radius: 8px; background: #1e1a10; border: 1px solid #3a2e10; margin-bottom: 16px; }

  /* FORM INPUTS */
  .finput { width: 100%; background: #13151e; border: 1px solid #2a2d3a; border-radius: 6px; color: #c8c0b0; font-size: 13px; padding: 9px 11px; }
  .flabel { font-size: 10px; color: #4a4d5a; letter-spacing: 0.1em; text-transform: uppercase; margin-bottom: 4px; display: block; }

  /* SYNC indicator */
  #sync { position: fixed; top: 12px; right: 14px; font-size: 10px; color: #3a3d4a; z-index: 100; display: flex; align-items: center; gap: 4px; }
  #sync.saving { color: #c49a3a; }
  #sync.saved  { color: #4caf7a; }
</style>
</head>
<body>

<div id="loading">
  <div class="spinner"></div>
  <div style="color:#5a5545;font-size:12px;letter-spacing:0.1em">Connessione al database...</div>
</div>

<div id="sync">● salvato</div>
<div id="toast">Salvato</div>
<div id="app"></div>

<script>
// ─── CONFIG ──────────────────────────────────────────────────────────────────
const SUPABASE_URL = "https://snixtrmpexjaxlktixul.supabase.co";
const SUPABASE_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InNuaXh0cm1wZXhqYXhsa3RpeHVsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODAxNjM5ODUsImV4cCI6MjA5NTczOTk4NX0.qKZjZ25nW_TdJuhHAUw5-xsg0bcrjcBmmEd8L0l9aKQ";
const HEADERS = { "Content-Type": "application/json", "apikey": SUPABASE_KEY, "Authorization": "Bearer " + SUPABASE_KEY, "Prefer": "return=representation" };

// ─── TEAM ─────────────────────────────────────────────────────────────────────
const TEAM = [
  { id: "valerio",   name: "Valerio",   initials: "VA", color: "#c8a96e" },
  { id: "donatella", name: "Donatella", initials: "DO", color: "#7eb8c9" },
  { id: "francesco", name: "Francesco", initials: "FR", color: "#a09fd4" },
];

// ─── STEPS ───────────────────────────────────────────────────────────────────
const STEPS = [
  { id:"s1",  phaseIdx:0, phase:"Fase 0 — Intake Commerciale",        num:"1",  isCheck:false, title:"Raccolta documenti preliminari",                      desc:"Visura camerale, bilancio (ultimi 2 esercizi), organigramma, codice ATECO, dimensione societaria.",                                                                                                               tags:[{l:"Visura camerale",t:"doc"},{l:"Bilancio",t:"doc"},{l:"ATECO",t:"doc"}],                                                         aiCmd:null },
  { id:"s2",  phaseIdx:0, phase:"Fase 0 — Intake Commerciale",        num:"2",  isCheck:false, title:"Analisi preliminare e predisposizione offerta + NDA",  desc:"Analisi profilo rischio 231, macro-aree di rischio, proposta commerciale con scope/deliverable/fee. Bozza NDA allegata.",                                                                                          tags:[{l:"AI: ANALISI CLIENTE",t:"ai"},{l:"Offerta .docx",t:"out"},{l:"Bozza NDA",t:"out"}],                                             aiCmd:"ANALISI CLIENTE" },
  { id:"c1",  phaseIdx:0, phase:"Fase 0 — Intake Commerciale",        num:"✓",  isCheck:true,  title:"Checkpoint 1 — Verifica Avvocato",                    desc:"Review offerta e NDA. Approvazione scope e pricing. Invio cliente, attesa accettazione e firma NDA.",                                                                                                              tags:[],                                                                                                                                 aiCmd:null },
  { id:"s3",  phaseIdx:1, phase:"Fase 1 — Raccolta Documentale",      num:"3",  isCheck:false, title:"Richiesta documentazione aziendale al cliente",        desc:"Checklist: DVR (D.Lgs. 81/08), certificazioni ISO, procedure interne, descrizioni analitiche processi, contrattualistica tipo.",                                                                                     tags:[{l:"DVR",t:"doc"},{l:"Certificazioni",t:"doc"},{l:"Procedure interne",t:"doc"},{l:"Minimo obbligatorio",t:"warn"}],               aiCmd:null },
  { id:"c2",  phaseIdx:1, phase:"Fase 1 — Raccolta Documentale",      num:"✓",  isCheck:true,  title:"Checkpoint 2 — Verifica Avvocato",                    desc:"Controllo completezza documentazione. Sollecito se mancante. Analisi critica prima del questionario.",                                                                                                              tags:[],                                                                                                                                 aiCmd:null },
  { id:"s4",  phaseIdx:2, phase:"Fase 2 — Mappatura e Questionario",  num:"4",  isCheck:false, title:"Predisposizione questionario personalizzato",          desc:"Costruzione questionario su misura dal template studio. Selezione attività sensibili per ATECO. Identificazione interlocutori per funzione.",                                                                         tags:[{l:"AI: PREPARA QUESTIONARIO",t:"ai"},{l:"Questionario .xlsx",t:"out"}],                                                           aiCmd:"PREPARA QUESTIONARIO" },
  { id:"c3",  phaseIdx:2, phase:"Fase 2 — Mappatura e Questionario",  num:"✓",  isCheck:true,  title:"Checkpoint 3 — Verifica Avvocato",                    desc:"Review questionario: completezza reati presupposto, adeguatezza settore, copertura funzioni. Approvazione prima della somministrazione.",                                                                       tags:[],                                                                                                                                 aiCmd:null },
  { id:"s5",  phaseIdx:2, phase:"Fase 2 — Mappatura e Questionario",  num:"5",  isCheck:false, title:"Conduzione interviste",                                desc:"Somministrazione questionario. Interviste integrative con top management e responsabili funzioni a rischio. Verbalizzazione colloqui.",                                                                          tags:[{l:"Verbali interviste",t:"doc"},{l:"Questionari compilati",t:"doc"}],                                                             aiCmd:null },
  { id:"s6",  phaseIdx:3, phase:"Fase 3 — Risk Assessment",           num:"6",  isCheck:false, title:"Elaborazione mappa dei rischi",                       desc:"Risk assessment da interviste e documenti. Valutazione probabilità/impatto attività sensibili. Gap analysis. Calcolo rischio residuo.",                                                                         tags:[{l:"AI: PREPARA RISK ASSESSMENT",t:"ai"},{l:"Risk Assessment .xlsx",t:"out"}],                                                    aiCmd:"PREPARA RISK ASSESSMENT" },
  { id:"c4",  phaseIdx:3, phase:"Fase 3 — Risk Assessment",           num:"✓",  isCheck:true,  title:"Checkpoint 4 — Verifica Avvocato",                    desc:"Revisione critica mappa rischi: coerenza con documenti, correttezza imputazione reati presupposto, adeguatezza scala. Validazione formale.",                                                                   tags:[],                                                                                                                                 aiCmd:null },
  { id:"s7",  phaseIdx:4, phase:"Fase 4 — Redazione MOG",             num:"7",  isCheck:false, title:"Redazione Parte Generale",                            desc:"Presentazione società, quadro normativo, deleghe e procure, struttura organizzativa, OdV, sistema disciplinare, whistleblowing, flussi informativi.",                                                         tags:[{l:"AI: MOG COMPLETO",t:"ai"},{l:"Parte Generale .docx",t:"out"}],                                                                aiCmd:"MOG COMPLETO" },
  { id:"s8",  phaseIdx:4, phase:"Fase 4 — Redazione MOG",             num:"8",  isCheck:false, title:"Redazione Parti Speciali",                            desc:"Una Parte Speciale per ogni macro-area (Reati PA, Societari, SSL, Ambientali, Informatici, Corruzione tra privati). Reati presupposto, attività sensibili, protocolli.",                                  tags:[{l:"AI: RISCHIO [area]",t:"ai"},{l:"AI: PROCEDURA [processo]",t:"ai"},{l:"Parti Speciali .docx",t:"out"}],                        aiCmd:"RISCHIO [area]" },
  { id:"s9",  phaseIdx:4, phase:"Fase 4 — Redazione MOG",             num:"9",  isCheck:false, title:"Redazione Codice Etico",                              desc:"Principi generali, destinatari, norme di condotta per area, meccanismi di segnalazione, sistema sanzionatorio. Adattato a cultura e settore.",                                                             tags:[{l:"AI: CODICE ETICO",t:"ai"},{l:"Codice Etico .docx",t:"out"}],                                                                  aiCmd:"CODICE ETICO" },
  { id:"c5",  phaseIdx:4, phase:"Fase 4 — Redazione MOG",             num:"✓",  isCheck:true,  title:"Checkpoint 5 — Verifica Avvocato",                    desc:"Review complessiva MOG (PG + PS + CE). Coerenza interna, completezza vs risk assessment. Risoluzione punti [VERIFICA AVVOCATO].",                                                                          tags:[],                                                                                                                                 aiCmd:null },
  { id:"s10", phaseIdx:5, phase:"Fase 5 — Finalizzazione e Consegna", num:"10", isCheck:false, title:"Integrazione osservazioni e assemblaggio pacchetto",  desc:"Recepimento note avvocato. Pacchetto finale: MOG + Allegati + Codice Etico + Regolamento OdV + Piano Vigilanza + Flussi informativi.",                                                                            tags:[{l:"AI: ODV",t:"ai"},{l:"Pacchetto MOG completo",t:"out"}],                                                                       aiCmd:"ODV" },
  { id:"c6",  phaseIdx:5, phase:"Fase 5 — Finalizzazione e Consegna", num:"✓",  isCheck:true,  title:"Checkpoint 6 — Approvazione Finale Avvocato",         desc:"Revisione finale pacchetto. Sign-off professionale. Predisposizione per adozione CDA. Consegna con lettera di trasmissione.",                                                                                   tags:[],                                                                                                                                 aiCmd:null },
];

const PHASE_COLORS = ["#c8a96e","#7eb8c9","#a09fd4","#e07b7b","#7ec99e","#c9a07e"];

// ─── STATE ───────────────────────────────────────────────────────────────────
let state = { projects: [], activeProjectId: null };
let saveTimer = null;

// ─── SUPABASE API ─────────────────────────────────────────────────────────────
async function dbFetch(path, opts = {}) {
  const res = await fetch(SUPABASE_URL + "/rest/v1/" + path, { headers: HEADERS, ...opts });
  if (!res.ok) { const e = await res.text(); throw new Error(e); }
  if (res.status === 204) return null;
  return res.json();
}

async function loadProjects() {
  const rows = await dbFetch("projects?select=*&order=created_at.asc");
  return (rows || []).map(r => ({
    id: r.id, name: r.name, clientName: r.client_name,
    createdAt: r.created_at, steps: r.steps || {},
  }));
}

async function upsertProject(p) {
  await dbFetch("projects", {
    method: "POST",
    body: JSON.stringify({ id: p.id, name: p.name, client_name: p.clientName, created_at: p.createdAt, steps: p.steps }),
    headers: { ...HEADERS, "Prefer": "resolution=merge-duplicates,return=minimal" },
  });
}

async function deleteProject(id) {
  await dbFetch("projects?id=eq." + id, { method: "DELETE" });
}

// ─── HELPERS ─────────────────────────────────────────────────────────────────
function getMember(id) { return TEAM.find(t => t.id === id); }
function isStepDone(sd) {
  if (!sd || !sd.owners || sd.owners.length === 0) return false;
  return sd.owners.every(oid => sd.ownerFlags && sd.ownerFlags[oid] && sd.ownerFlags[oid].done);
}
function stepProgress(sd) {
  if (!sd || !sd.owners || sd.owners.length === 0) return { done: 0, total: 0 };
  const done = sd.owners.filter(oid => sd.ownerFlags && sd.ownerFlags[oid] && sd.ownerFlags[oid].done).length;
  return { done, total: sd.owners.length };
}
function projectProgress(p) {
  const done = STEPS.filter(s => isStepDone(p.steps[s.id])).length;
  return Math.round((done / STEPS.length) * 100);
}
function isStepLocked(idx, p) {
  if (idx === 0) return false;
  return !isStepDone(p.steps[STEPS[idx - 1].id]);
}
function makeStepData() { return { owners: [], ownerFlags: {}, nextOwners: [], note: "" }; }
function makeProject(name, clientName) {
  const steps = {};
  STEPS.forEach(s => { steps[s.id] = makeStepData(); });
  return { id: Date.now().toString(), name, clientName, createdAt: new Date().toISOString(), steps };
}
function getProject(id) { return state.projects.find(p => p.id === id); }

// ─── SYNC ─────────────────────────────────────────────────────────────────────
function setSyncState(s) {
  const el = document.getElementById("sync");
  el.className = s;
  el.textContent = s === "saving" ? "● salvando..." : "● salvato";
}

function scheduleSave(project) {
  setSyncState("saving");
  clearTimeout(saveTimer);
  saveTimer = setTimeout(async () => {
    try { await upsertProject(project); setSyncState("saved"); showToast("Salvato"); }
    catch(e) { setSyncState(""); showToast("Errore salvataggio"); console.error(e); }
  }, 800);
}

function showToast(msg) {
  const t = document.getElementById("toast");
  t.textContent = msg;
  t.classList.add("show");
  setTimeout(() => t.classList.remove("show"), 2000);
}

// ─── RENDER ENGINE ────────────────────────────────────────────────────────────
function h(tag, attrs, ...children) {
  const el = document.createElement(tag);
  if (attrs) Object.entries(attrs).forEach(([k, v]) => {
    if (k === "class") el.className = v;
    else if (k.startsWith("on")) el.addEventListener(k.slice(2).toLowerCase(), v);
    else if (k === "style" && typeof v === "object") Object.assign(el.style, v);
    else el.setAttribute(k, v);
  });
  children.flat(Infinity).forEach(c => {
    if (c == null || c === false) return;
    el.appendChild(typeof c === "string" ? document.createTextNode(c) : c);
  });
  return el;
}

function avatar(memberId, size = 26, ring = false, checked = false, faded = false) {
  const m = getMember(memberId);
  if (!m) return h("span", {});
  const el = h("div", {
    class: "av",
    title: m.name,
    style: {
      width: size + "px", height: size + "px",
      background: faded ? "#1a1d27" : m.color + "22",
      border: `${ring ? 2 : 1}px solid ${faded ? "#2a2d3a" : m.color}`,
      fontSize: (size * 0.33) + "px",
      color: faded ? "#3a3d4a" : m.color,
      opacity: faded ? "0.45" : "1",
    }
  }, m.initials);
  if (checked) {
    const dot = h("div", { style: {
      position: "absolute", bottom: "-2px", right: "-2px",
      width: (size * 0.42) + "px", height: (size * 0.42) + "px",
      borderRadius: "50%", background: "#4caf7a", border: "1.5px solid #0f1117",
      display: "flex", alignItems: "center", justifyContent: "center",
      fontSize: (size * 0.22) + "px", color: "#0f1117", fontWeight: "900",
    }}, "✓");
    el.appendChild(dot);
  }
  return el;
}

function multiOwnerPicker(selected, onChange, label) {
  const wrap = h("div", { style: { display: "flex", flexDirection: "column", gap: "5px" } });
  if (label) wrap.appendChild(h("span", { class: "flabel" }, label));
  const row = h("div", { style: { display: "flex", gap: "6px", flexWrap: "wrap" } });
  TEAM.forEach(m => {
    const active = selected.includes(m.id);
    const btn = h("button", {
      class: "mpick-btn",
      style: {
        background: active ? m.color + "20" : "#1a1d27",
        borderColor: active ? m.color : "#2a2d3a",
        color: active ? m.color : "#5a5545",
      },
      onClick: () => {
        const next = active ? selected.filter(x => x !== m.id) : [...selected, m.id];
        onChange(next);
      }
    }, avatar(m.id, 15), " " + m.name, active ? " ✓" : "");
    row.appendChild(btn);
  });
  wrap.appendChild(row);
  return wrap;
}

// ─── STEP CARD ────────────────────────────────────────────────────────────────
function renderStepCard(step, stepData, project, stepIdx) {
  const locked = isStepLocked(stepIdx, project);
  const done = isStepDone(stepData);
  const { done: flagsDone, total: flagsTotal } = stepProgress(stepData);
  const allSigned = flagsTotal > 0 && flagsDone === flagsTotal;
  const phaseColor = PHASE_COLORS[step.phaseIdx] || "#c8a96e";

  let borderColor = "#2a2d3a";
  if (locked) borderColor = "#1e2030";
  else if (done) borderColor = "#2e5a3a";
  else if (step.isCheck) borderColor = "#1e3a2a";

  const card = h("div", {
    class: ["card", step.isCheck ? "card-check" : "", done ? "card-done" : "", locked ? "card-locked" : ""].join(" "),
    style: { borderColor, position: "relative", marginBottom: "8px" }
  });

  // Left accent
  const accentColor = locked ? "#1e2030" : done ? "#4caf7a" : step.isCheck ? "#2e7d52" : phaseColor + "88";
  card.appendChild(h("div", { class: "accent-left", style: { background: accentColor } }));

  // Header
  const header = h("div", {
    style: { display: "flex", alignItems: "flex-start", gap: "12px", padding: "13px 16px 13px 20px", cursor: locked ? "not-allowed" : "pointer" },
    onClick: () => { if (!locked) toggleExpand(); }
  });

  // Badge num
  header.appendChild(h("div", {
    style: {
      width: "30px", height: "30px", minWidth: "30px", borderRadius: "50%",
      display: "flex", alignItems: "center", justifyContent: "center",
      fontSize: step.isCheck ? "14px" : "12px", fontWeight: "700",
      background: locked ? "#1a1d27" : step.isCheck ? (done ? "#0d2a18" : "#0d1f18") : "#252836",
      border: `1px solid ${locked ? "#2a2d3a" : step.isCheck ? (done ? "#4caf7a" : "#1e3a2a") : phaseColor + "44"}`,
      color: locked ? "#3a3d4a" : step.isCheck ? (done ? "#4caf7a" : "#3a7a52") : phaseColor,
      flexShrink: "0", marginTop: "1px",
    }
  }, locked ? "🔒" : step.num));

  const hBody = h("div", { style: { flex: "1", minWidth: "0" } });
  hBody.appendChild(h("div", {
    style: {
      fontSize: step.isCheck ? "11px" : "13px", fontWeight: "600",
      color: locked ? "#3a3d4a" : step.isCheck ? (done ? "#4caf7a" : "#3a7a52") : "#e8e0cc",
      letterSpacing: step.isCheck ? "0.08em" : "0.01em",
      textTransform: step.isCheck ? "uppercase" : "none",
      marginBottom: "5px",
    }
  }, step.title));

  // Owner row
  const ownerRow = h("div", { style: { display: "flex", alignItems: "center", gap: "6px", flexWrap: "wrap" } });
  const owners = stepData.owners || [];
  if (owners.length === 0) {
    ownerRow.appendChild(h("span", { style: { fontSize: "10px", color: "#3a3d4a" } }, "nessun owner"));
  } else {
    owners.forEach(oid => {
      ownerRow.appendChild(avatar(oid, 20, true, stepData.ownerFlags && stepData.ownerFlags[oid] && stepData.ownerFlags[oid].done, !(stepData.ownerFlags && stepData.ownerFlags[oid] && stepData.ownerFlags[oid].done)));
    });
  }
  if (flagsTotal > 0) {
    ownerRow.appendChild(h("span", {
      style: {
        fontSize: "10px", padding: "1px 8px", borderRadius: "20px", fontWeight: "600",
        background: allSigned ? "#0d2a18" : "#1a1d27",
        border: `1px solid ${allSigned ? "#4caf7a44" : "#2a2d3a"}`,
        color: allSigned ? "#4caf7a" : "#5a5545",
      }
    }, `${flagsDone}/${flagsTotal} firmato`));
  }
  if ((stepData.nextOwners || []).length > 0) {
    ownerRow.appendChild(h("span", { style: { fontSize: "9px", color: "#3a3d4a" } }, "→"));
    stepData.nextOwners.forEach(oid => ownerRow.appendChild(avatar(oid, 18)));
  }
  if (locked) ownerRow.appendChild(h("span", { style: { fontSize: "10px", color: "#3a3d4a" } }, "In attesa dello step precedente"));
  hBody.appendChild(ownerRow);
  header.appendChild(hBody);

  const chevron = h("div", { style: { color: "#3a3d4a", fontSize: "11px", marginTop: "4px" } }, "▼");
  header.appendChild(chevron);
  card.appendChild(header);

  // Expanded panel (hidden by default)
  const panel = h("div", { style: { display: "none", padding: "0 16px 16px 20px", borderTop: "1px solid #1e2030" } });

  function buildPanel() {
    panel.innerHTML = "";
    panel.appendChild(h("p", { style: { fontSize: "12px", color: "#5a5545", lineHeight: "1.65", margin: "10px 0 13px" } }, step.desc));

    // Tags
    if (step.tags.length > 0) {
      const trow = h("div", { style: { display: "flex", flexWrap: "wrap", gap: "5px", marginBottom: "13px" } });
      step.tags.forEach(t => trow.appendChild(h("span", { class: `tag tag-${t.t}` }, t.l)));
      panel.appendChild(trow);
    }

    // Multi-owner picker
    panel.appendChild(h("div", { style: { marginBottom: "13px" } },
      multiOwnerPicker(stepData.owners || [], (newOwners) => {
        const newFlags = {};
        newOwners.forEach(oid => { newFlags[oid] = (stepData.ownerFlags || {})[oid] || { done: false, doneAt: null }; });
        stepData.owners = newOwners;
        stepData.ownerFlags = newFlags;
        saveProjectUpdate(project);
        buildPanel();
        refreshHeader();
      }, "Owner (uno o più)")
    ));

    // Firma panel
    if ((stepData.owners || []).length > 0) {
      panel.appendChild(h("div", { style: { marginBottom: "13px" } },
        h("span", { class: "flabel" }, `Firma individuale (${flagsDone}/${flagsTotal})`),
        ...stepData.owners.map(oid => {
          const m = getMember(oid);
          if (!m) return h("span");
          const flag = (stepData.ownerFlags || {})[oid] || {};
          const signed = !!flag.done;
          const row = h("div", { class: "firma-row " + (signed ? "signed" : ""), style: { marginBottom: "5px" } },
            avatar(oid, 26, true, signed),
            h("div", { style: { flex: "1" } },
              h("div", { style: { fontSize: "12px", fontWeight: "600", color: signed ? "#4caf7a" : "#c8c0b0" } }, m.name),
              signed && flag.doneAt ? h("div", { style: { fontSize: "10px", color: "#3a7a52" } },
                "Firmato il " + new Date(flag.doneAt).toLocaleString("it-IT", { day: "2-digit", month: "short", hour: "2-digit", minute: "2-digit" })
              ) : h("span")
            ),
            h("button", {
              class: "btn " + (signed ? "btn-ghost" : "btn-green"),
              style: { fontSize: "11px", padding: "5px 12px" },
              onClick: () => {
                if (!stepData.ownerFlags) stepData.ownerFlags = {};
                const nowDone = !signed;
                stepData.ownerFlags[oid] = { done: nowDone, doneAt: nowDone ? new Date().toISOString() : null };
                saveProjectUpdate(project);
                buildPanel();
                refreshHeader();
                if (nowDone) {
                  const p2 = stepProgress(stepData);
                  if (p2.done === p2.total) showToast("✓ Step completato da tutti!");
                }
              }
            }, signed ? "Annulla" : "Firma")
          );
          return row;
        }),
        allSigned ? h("div", { style: { marginTop: "8px", padding: "8px 12px", borderRadius: "8px", background: "#0d2a18", border: "1px solid #2e6a3a", fontSize: "12px", color: "#4caf7a", fontWeight: "600" } }, "✓ Tutti gli owner hanno firmato — step completato") : h("span")
      ));
    }

    // Passa la palla
    panel.appendChild(h("div", { style: { marginBottom: "13px" } },
      multiOwnerPicker(stepData.nextOwners || [], (v) => {
        stepData.nextOwners = v;
        saveProjectUpdate(project);
        buildPanel();
      }, "Passa la palla a")
    ));

    // Note
    panel.appendChild(h("div", { style: { marginBottom: step.aiCmd ? "12px" : "0" } },
      h("span", { class: "flabel" }, "Note"),
      h("textarea", {
        class: "finput",
        style: { resize: "vertical", lineHeight: "1.5", minHeight: "60px" },
        placeholder: "Note su questo step...",
        onBlur: (e) => { stepData.note = e.target.value; saveProjectUpdate(project); },
      }, stepData.note || "")
    ));

    // AI command
    if (step.aiCmd) {
      panel.appendChild(h("div", { style: { padding: "8px 12px", borderRadius: "6px", background: "#1e1535", border: "1px solid #2d2440", fontSize: "11px", color: "#9b79d4", fontFamily: "monospace" } },
        "Comando AI: ", h("strong", {}, step.aiCmd)
      ));
    }
  }

  function refreshHeader() {
    const newOwners = stepData.owners || [];
    const newFlags = stepData.ownerFlags || {};
    const { done: d2, total: t2 } = stepProgress(stepData);
    ownerRow.innerHTML = "";
    if (newOwners.length === 0) {
      ownerRow.appendChild(h("span", { style: { fontSize: "10px", color: "#3a3d4a" } }, "nessun owner"));
    } else {
      newOwners.forEach(oid => ownerRow.appendChild(avatar(oid, 20, true, newFlags[oid] && newFlags[oid].done, !(newFlags[oid] && newFlags[oid].done))));
    }
    if (t2 > 0) {
      const as2 = d2 === t2;
      ownerRow.appendChild(h("span", {
        style: { fontSize: "10px", padding: "1px 8px", borderRadius: "20px", fontWeight: "600", background: as2 ? "#0d2a18" : "#1a1d27", border: `1px solid ${as2 ? "#4caf7a44" : "#2a2d3a"}`, color: as2 ? "#4caf7a" : "#5a5545" }
      }, `${d2}/${t2} firmato`));
    }
  }

  let expanded = false;
  function toggleExpand() {
    expanded = !expanded;
    chevron.textContent = expanded ? "▲" : "▼";
    if (expanded) { buildPanel(); panel.style.display = "block"; }
    else panel.style.display = "none";
  }

  card.appendChild(panel);
  return card;
}

// ─── PROJECT VIEW ─────────────────────────────────────────────────────────────
function saveProjectUpdate(project) {
  const idx = state.projects.findIndex(p => p.id === project.id);
  if (idx >= 0) state.projects[idx] = project;
  scheduleSave(project);
  // Refresh progress bars in list if needed
}

function renderProjectView(project) {
  const app = document.getElementById("app");
  app.innerHTML = "";

  const wrap = h("div", {});

  // Back btn
  wrap.appendChild(h("button", { class: "btn btn-ghost", style: { marginBottom: "16px", fontSize: "12px" }, onClick: renderProjectList }, "← Tutti i progetti"));

  // Header
  const prog = projectProgress(project);
  wrap.appendChild(h("div", { style: { display: "flex", justifyContent: "space-between", alignItems: "flex-start", gap: "12px", marginBottom: "4px" } },
    h("div", {},
      h("h2", { style: { fontFamily: "'Playfair Display', serif", fontSize: "20px", color: "#f0ece2", marginBottom: "3px" } }, project.name),
      h("div", { style: { fontSize: "11px", color: "#5a5545" } }, "Cliente: " + project.clientName)
    ),
    h("div", { style: { textAlign: "right", flexShrink: "0" } },
      h("div", { style: { fontSize: "20px", fontWeight: "700", color: "#c8a96e", fontFamily: "'Playfair Display', serif" } }, prog + "%"),
      h("div", { style: { fontSize: "10px", color: "#5a5545" } }, STEPS.filter(s => isStepDone(project.steps[s.id])).length + "/" + STEPS.length + " step")
    )
  ));
  const progBar = h("div", { class: "progress-track", style: { marginBottom: "20px" } }, h("div", { class: "progress-fill", style: { width: prog + "%" } }));
  wrap.appendChild(progBar);

  // Handoff banner
  const handoffs = STEPS.filter((s, i) => {
    const sd = project.steps[s.id] || {};
    if (!(sd.nextOwners || []).length) return false;
    const prevDone = i === 0 ? true : isStepDone(project.steps[STEPS[i-1].id]);
    return prevDone && !isStepDone(sd);
  });
  if (handoffs.length > 0) {
    const banner = h("div", { class: "handoff-banner" },
      h("div", { style: { fontSize: "11px", color: "#c49a3a", fontWeight: "600", marginBottom: "4px" } }, "🔔 " + handoffs.length + " passaggio/i di consegna attivi"),
      ...handoffs.map(s => {
        const sd = project.steps[s.id] || {};
        return h("div", { style: { display: "flex", alignItems: "center", gap: "6px", marginBottom: "3px" } },
          h("span", { style: { fontSize: "11px", color: "#6a5a30" } }, (s.title.length > 30 ? s.title.slice(0,30)+"…" : s.title) + " →"),
          ...(sd.nextOwners || []).map(oid => avatar(oid, 18, true))
        );
      })
    );
    wrap.appendChild(banner);
  }

  // Filter tabs
  let activeFilter = "all";
  const filterWrap = h("div", { style: { display: "flex", gap: "6px", flexWrap: "wrap", marginBottom: "18px" } });
  const filters = [{ id: "all", label: "Tutti" }, { id: "todo", label: "Senza owner" }, { id: "inprogress", label: "In corso" }, { id: "done", label: "Completati" }];
  const tabs = {};
  filters.forEach(f => {
    const btn = h("button", { class: "ftab " + (f.id === "all" ? "active" : ""), onClick: () => {
      activeFilter = f.id;
      Object.entries(tabs).forEach(([k, b]) => b.className = "ftab " + (k === f.id ? "active" : ""));
      renderSteps();
    }}, f.label);
    tabs[f.id] = btn;
    filterWrap.appendChild(btn);
  });
  wrap.appendChild(filterWrap);

  const stepsContainer = h("div", {});
  wrap.appendChild(stepsContainer);

  function filterStep(step) {
    const sd = project.steps[step.id] || {};
    if (activeFilter === "todo") return (sd.owners || []).length === 0 && !isStepDone(sd);
    if (activeFilter === "inprogress") return (sd.owners || []).length > 0 && !isStepDone(sd);
    if (activeFilter === "done") return isStepDone(sd);
    return true;
  }

  function renderSteps() {
    stepsContainer.innerHTML = "";
    // Group by phase
    const phases = [];
    STEPS.forEach(s => {
      if (!phases[s.phaseIdx]) phases[s.phaseIdx] = { label: s.phase, steps: [] };
      phases[s.phaseIdx].steps.push(s);
    });
    phases.forEach((phase, pi) => {
      if (!phase) return;
      const visible = phase.steps.filter(filterStep);
      if (!visible.length) return;
      const col = PHASE_COLORS[pi] || "#c8a96e";
      const phaseWrap = h("div", { style: { marginBottom: "24px" } });
      phaseWrap.appendChild(h("div", { class: "section-label", style: { color: col + "99" } },
        h("div", { class: "section-line", style: { background: col + "44", maxWidth: "20px" } }),
        phase.label,
        h("div", { class: "section-line", style: { background: col + "22" } })
      ));
      visible.forEach(step => {
        const globalIdx = STEPS.findIndex(s => s.id === step.id);
        if (!project.steps[step.id]) project.steps[step.id] = makeStepData();
        phaseWrap.appendChild(renderStepCard(step, project.steps[step.id], project, globalIdx));
      });
      stepsContainer.appendChild(phaseWrap);
    });
  }

  renderSteps();
  app.appendChild(wrap);
}

// ─── PROJECT LIST ─────────────────────────────────────────────────────────────
function renderProjectList() {
  state.activeProjectId = null;
  const app = document.getElementById("app");
  app.innerHTML = "";

  // Header
  app.appendChild(h("div", { style: { textAlign: "center", marginBottom: "32px" } },
    h("h1", { style: { fontFamily: "'Playfair Display', serif", fontSize: "24px", color: "#f0ece2", marginBottom: "5px" } }, "MOG 231"),
    h("div", { style: { fontSize: "11px", color: "#5a5545", letterSpacing: "0.12em", textTransform: "uppercase", marginBottom: "14px" } }, "Studio Legale · Compliance D.Lgs. 231/2001"),
    h("div", { style: { display: "flex", justifyContent: "center", gap: "14px" } },
      ...TEAM.map(m => h("div", { style: { display: "flex", alignItems: "center", gap: "6px", fontSize: "12px", color: m.color } }, avatar(m.id, 24, true), " " + m.name))
    )
  ));

  // Project cards
  const listWrap = h("div", { style: { display: "flex", flexDirection: "column", gap: "10px", marginBottom: "14px" } });

  if (state.projects.length === 0) {
    listWrap.appendChild(h("div", { style: { textAlign: "center", padding: "44px 24px", border: "1px dashed #2a2d3a", borderRadius: "12px", color: "#3a3d4a", fontSize: "13px" } },
      "Nessun progetto. Crea il primo cliente."
    ));
  }

  state.projects.forEach(p => {
    const prog = projectProgress(p);
    const done = STEPS.filter(s => isStepDone(p.steps[s.id])).length;
    const inProgress = STEPS.filter(s => { const sd = p.steps[s.id]||{}; return (sd.owners||[]).length > 0 && !isStepDone(sd); }).length;
    const allOwners = [...new Set(STEPS.flatMap(s => (p.steps[s.id]||{}).owners || []))];

    const card = h("div", { class: "card", style: { cursor: "pointer" } });
    const inner = h("div", { style: { padding: "15px 17px" }, onClick: () => { state.activeProjectId = p.id; renderProjectView(p); } });
    inner.appendChild(h("div", { style: { display: "flex", justifyContent: "space-between", gap: "10px", marginBottom: "4px" } },
      h("div", {},
        h("div", { style: { fontSize: "15px", fontWeight: "600", color: "#e8e0cc", marginBottom: "2px" } }, p.name),
        h("div", { style: { fontSize: "11px", color: "#5a5545" } }, p.clientName + " · " + new Date(p.createdAt).toLocaleDateString("it-IT"))
      ),
      h("div", { style: { textAlign: "right", flexShrink: "0" } },
        h("div", { style: { fontSize: "18px", fontWeight: "700", color: "#c8a96e", fontFamily: "'Playfair Display', serif" } }, prog + "%"),
        h("div", { style: { fontSize: "10px", color: "#4a4d5a" } }, done + "/" + STEPS.length)
      )
    ));
    inner.appendChild(h("div", { class: "progress-track", style: { marginBottom: "10px" } }, h("div", { class: "progress-fill", style: { width: prog + "%" } })));
    inner.appendChild(h("div", { style: { display: "flex", alignItems: "center", justifyContent: "space-between" } },
      h("div", { style: { display: "flex", gap: "3px" } }, ...allOwners.map(oid => avatar(oid, 21, true))),
      h("div", { style: { display: "flex", gap: "5px" } },
        inProgress > 0 ? h("span", { class: "tag tag-warn" }, inProgress + " in corso") : h("span"),
        h("span", { class: "tag tag-doc" }, done + "/" + STEPS.length + " step")
      )
    ));
    card.appendChild(inner);

    // Delete bar
    const delBar = h("div", { style: { borderTop: "1px solid #1e2030", padding: "5px 17px", display: "flex", justifyContent: "flex-end" } },
      h("button", {
        style: { background: "none", border: "none", color: "#3a2d2d", fontSize: "11px", cursor: "pointer", padding: "2px 6px" },
        onClick: async (e) => {
          e.stopPropagation();
          if (!confirm('Eliminare "' + p.name + '"?')) return;
          try { await deleteProject(p.id); state.projects = state.projects.filter(x => x.id !== p.id); renderProjectList(); showToast("Progetto eliminato"); }
          catch(err) { showToast("Errore eliminazione"); }
        }
      }, "Elimina")
    );
    card.appendChild(delBar);
    listWrap.appendChild(card);
  });
  app.appendChild(listWrap);

  // New project form
  let showForm = false;
  const formWrap = h("div", {});

  const addBtn = h("button", {
    class: "btn btn-ghost",
    style: { width: "100%", padding: "12px", borderRadius: "10px", fontSize: "13px", border: "1px dashed #3a3d4a" },
    onClick: () => { showForm = true; renderForm(); }
  }, "+ Nuovo progetto cliente");

  function renderForm() {
    formWrap.innerHTML = "";
    if (!showForm) { formWrap.appendChild(addBtn); return; }
    const box = h("div", { class: "card", style: { padding: "18px" } });
    box.appendChild(h("div", { style: { fontSize: "13px", fontWeight: "600", color: "#c8a96e", marginBottom: "13px" } }, "Nuovo progetto"));
    const nameIn = h("input", { class: "finput", placeholder: "es. MOG Alfa Srl 2025", style: { marginBottom: "10px" } });
    const clientIn = h("input", { class: "finput", placeholder: "es. Alfa S.r.l." });
    box.appendChild(h("div", {}, h("span", { class: "flabel" }, "Nome progetto"), nameIn));
    box.appendChild(h("div", { style: { marginTop: "8px", marginBottom: "12px" } }, h("span", { class: "flabel" }, "Nome cliente / società"), clientIn));
    box.appendChild(h("div", { style: { display: "flex", gap: "8px" } },
      h("button", { class: "btn btn-green", onClick: async () => {
        const n = nameIn.value.trim(), c = clientIn.value.trim();
        if (!n || !c) { showToast("Compila nome e cliente"); return; }
        const p = makeProject(n, c);
        try {
          await upsertProject(p);
          state.projects.push(p);
          showForm = false;
          renderProjectList();
          showToast("Progetto creato");
        } catch(e) { showToast("Errore creazione"); console.error(e); }
      }}, "Crea progetto"),
      h("button", { class: "btn btn-ghost", onClick: () => { showForm = false; renderForm(); } }, "Annulla")
    ));
    formWrap.appendChild(box);
  }

  renderForm();
  app.appendChild(formWrap);
}

// ─── INIT ─────────────────────────────────────────────────────────────────────
async function init() {
  try {
    state.projects = await loadProjects();
  } catch(e) {
    console.error("Errore caricamento:", e);
    document.getElementById("loading").innerHTML = '<div style="color:#e07b7b;text-align:center;padding:24px"><div style="font-size:14px;font-weight:600;margin-bottom:8px">Errore connessione</div><div style="font-size:12px;color:#7a5a5a">Controlla la connessione e ricarica</div></div>';
    return;
  }
  document.getElementById("loading").style.display = "none";
  renderProjectList();
}

init();
</script>
</body>
</html>
