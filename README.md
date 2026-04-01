<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-Content-Type-Options" content="nosniff">
<meta http-equiv="X-Frame-Options" content="DENY">
<meta http-equiv="Referrer-Policy" content="strict-origin-when-cross-origin">
<title>CyberAce Quantum 5.0 — Digital Services Hub</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Lexend:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

<!-- Firebase v9 compat (works in plain HTML, no bundler needed) -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-analytics-compat.js"></script>

<style>
/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   DESIGN TOKENS  — industrial terminal aesthetic
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
:root{
  --ink:#040810;--surface:#090E1A;--panel:#0E1525;--card:#131E30;
  --card2:#192338;--lift:#1E2A3E;--border:rgba(255,255,255,0.06);
  --border2:rgba(255,255,255,0.11);--border3:rgba(255,255,255,0.18);
  --fire:#FF5722;--fire2:#FF7043;--gold:#FFB300;--jade:#00BFA5;
  --sky:#29B6F6;--violet:#7C4DFF;--rose:#E91E63;
  --text:#ECF0F1;--muted:#607080;--muted2:#8FA3B8;
  --success:#00E676;--danger:#FF5252;--warn:#FFD600;
  --ff-head:'Lexend',sans-serif;--ff-mono:'JetBrains Mono',monospace;
  --r:12px;--r2:18px;--r3:24px;
  --sh:0 16px 48px rgba(0,0,0,.55);--sh2:0 4px 20px rgba(0,0,0,.4);
}
*,*::before,*::after{margin:0;padding:0;box-sizing:border-box}
html{font-size:14.5px;scroll-behavior:smooth}
body{font-family:var(--ff-head);background:var(--ink);color:var(--text);min-height:100vh;overflow-x:hidden}

/* scrollbar */
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:var(--panel)}
::-webkit-scrollbar-thumb{background:var(--fire);border-radius:99px}

/* grid noise */
body::before{content:'';position:fixed;inset:0;pointer-events:none;z-index:0;
  background-image:linear-gradient(rgba(255,87,34,.025) 1px,transparent 1px),
    linear-gradient(90deg,rgba(255,87,34,.025) 1px,transparent 1px);
  background-size:40px 40px}

/* ━━━ LAYOUT ━━━ */
.shell{display:flex;height:100vh;position:relative;z-index:1}
.sidebar{width:248px;min-width:248px;background:var(--surface);border-right:1px solid var(--border);
  display:flex;flex-direction:column;overflow-y:auto;overflow-x:hidden;
  transition:width .25s ease;position:fixed;top:0;left:0;bottom:0;z-index:300}
.sidebar.slim{width:56px}
.sidebar.slim .hide-slim{display:none!important}
.main-area{flex:1;margin-left:248px;display:flex;flex-direction:column;min-height:100vh;
  transition:margin .25s ease}
.sidebar.slim~.main-area{margin-left:56px}
.topbar{height:54px;background:var(--surface);border-bottom:1px solid var(--border);
  display:flex;align-items:center;padding:0 22px;gap:14px;
  position:sticky;top:0;z-index:200;flex-shrink:0}
.page{flex:1;padding:24px;overflow-y:auto}

/* ━━━ SIDEBAR ━━━ */
.sb-brand{padding:16px 14px;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:10px;flex-shrink:0}
.sb-orb{width:36px;height:36px;border-radius:9px;background:linear-gradient(135deg,var(--fire),#C62828);
  display:flex;align-items:center;justify-content:center;font-size:1.1rem;flex-shrink:0;
  box-shadow:0 4px 14px rgba(255,87,34,.4)}
.sb-name{font-size:.95rem;font-weight:800;line-height:1.15}
.sb-name small{display:block;font-size:.6rem;font-weight:400;color:var(--muted2);margin-top:1px}
.sb-user{margin:8px 10px;background:var(--panel);border:1px solid var(--border);
  border-radius:var(--r);padding:9px 11px;display:flex;align-items:center;gap:9px}
.sb-avatar{width:30px;height:30px;border-radius:50%;background:linear-gradient(135deg,var(--fire),var(--gold));
  display:flex;align-items:center;justify-content:center;font-weight:800;font-size:.8rem;flex-shrink:0}
.sb-uinfo{min-width:0}
.sb-uinfo .n{font-size:.78rem;font-weight:600;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.sb-uinfo .r{font-size:.6rem;color:var(--jade)}
.nav{flex:1;padding:6px 0}
.nav-sec{padding:8px 16px 2px;font-size:.58rem;letter-spacing:.1em;text-transform:uppercase;color:var(--muted)}
.nav-item{display:flex;align-items:center;gap:10px;padding:9px 14px;cursor:pointer;
  color:var(--muted2);border-left:2px solid transparent;transition:all .17s;font-size:.82rem;
  user-select:none;white-space:nowrap}
.nav-item:hover{background:rgba(255,255,255,.03);color:var(--text);border-left-color:rgba(255,87,34,.4)}
.nav-item.active{background:rgba(255,87,34,.1);color:var(--text);border-left-color:var(--fire)}
.nav-item i{width:17px;text-align:center;font-size:.85rem;flex-shrink:0}
.nav-badge{margin-left:auto;background:var(--fire);color:#fff;font-size:.58rem;
  padding:1px 6px;border-radius:99px;font-weight:700}
.sb-footer{padding:12px 10px;border-top:1px solid var(--border)}
.upi-pill{background:rgba(255,87,34,.08);border:1px solid rgba(255,87,34,.25);
  border-radius:9px;padding:9px 11px;font-size:.68rem}
.upi-pill strong{display:block;color:var(--fire);margin-bottom:2px;font-size:.72rem}
.upi-pill span{color:var(--muted2);word-break:break-all;font-family:var(--ff-mono)}

/* ━━━ TOPBAR ━━━ */
.tb-toggle{background:none;border:none;color:var(--muted2);cursor:pointer;font-size:1rem;padding:5px;border-radius:7px;transition:color .15s}
.tb-toggle:hover{color:var(--text)}
.tb-title{font-size:1.05rem;font-weight:700;flex:1}
.tb-right{display:flex;align-items:center;gap:10px}
.tb-secure{background:rgba(0,230,118,.07);border:1px solid rgba(0,230,118,.2);
  border-radius:99px;padding:4px 12px;font-size:.7rem;color:var(--success);
  display:flex;align-items:center;gap:5px}
.tb-secure::before{content:'';width:6px;height:6px;border-radius:50%;background:var(--success);animation:blink 2s infinite}
@keyframes blink{0%,100%{opacity:1}50%{opacity:.3}}
.tb-logout{background:rgba(255,82,82,.09);border:1px solid rgba(255,82,82,.28);
  color:var(--danger);padding:5px 13px;border-radius:8px;font-size:.75rem;
  cursor:pointer;font-family:var(--ff-head);transition:all .17s}
.tb-logout:hover{background:rgba(255,82,82,.18)}

/* ━━━ AUTH SCREEN ━━━ */
.auth-wrap{min-height:100vh;background:var(--ink);display:flex;align-items:center;justify-content:center;padding:20px;position:relative;overflow:hidden}
.auth-glow{position:absolute;border-radius:50%;filter:blur(100px);pointer-events:none}
.auth-glow-1{width:500px;height:500px;background:var(--fire);opacity:.06;top:-100px;right:-100px;animation:gDrift 9s ease-in-out infinite}
.auth-glow-2{width:400px;height:400px;background:var(--sky);opacity:.05;bottom:-80px;left:-80px;animation:gDrift 11s ease-in-out infinite reverse}
@keyframes gDrift{0%,100%{transform:translateY(0) scale(1)}50%{transform:translateY(-40px) scale(1.06)}}
.auth-card{position:relative;z-index:1;background:var(--panel);border:1px solid var(--border2);
  border-radius:var(--r3);padding:36px;width:100%;max-width:420px;box-shadow:var(--sh);
  animation:cardRise .4s cubic-bezier(.4,0,.2,1)}
@keyframes cardRise{from{opacity:0;transform:translateY(24px) scale(.97)}to{opacity:1;transform:none}}
.auth-logo{text-align:center;margin-bottom:26px}
.auth-icon{display:inline-flex;align-items:center;justify-content:center;width:62px;height:62px;
  border-radius:15px;background:linear-gradient(135deg,var(--fire),#B71C1C);font-size:1.7rem;
  margin-bottom:12px;box-shadow:0 8px 28px rgba(255,87,34,.38);
  animation:iconBounce .5s cubic-bezier(.34,1.56,.64,1) .15s both}
@keyframes iconBounce{from{transform:scale(.3);opacity:0}to{transform:scale(1);opacity:1}}
.auth-logo h1{font-size:1.55rem;font-weight:800}
.auth-logo p{color:var(--muted2);font-size:.78rem;margin-top:4px}
.auth-tabs{display:flex;background:var(--card);border-radius:10px;padding:3px;margin-bottom:20px}
.auth-tab{flex:1;padding:8px;text-align:center;border-radius:8px;cursor:pointer;
  font-size:.82rem;font-weight:500;color:var(--muted2);transition:all .17s}
.auth-tab.on{background:var(--fire);color:#fff;font-weight:700}
.auth-note{background:rgba(255,183,0,.07);border:1px solid rgba(255,183,0,.2);border-radius:9px;
  padding:9px 13px;font-size:.72rem;color:var(--warn);margin-bottom:14px;display:flex;gap:8px}

/* ━━━ FORM FIELDS ━━━ */
.fld{margin-bottom:12px}
.fld-lbl{font-size:.72rem;color:var(--muted2);margin-bottom:5px;display:block;font-weight:500}
.fld-wrap{position:relative}
.fld-ico{position:absolute;left:12px;top:50%;transform:translateY(-50%);color:var(--muted);font-size:.8rem;pointer-events:none}
.fld-in{width:100%;padding:10px 12px 10px 36px;background:var(--card2);border:1px solid var(--border2);
  border-radius:9px;color:var(--text);font-family:var(--ff-head);font-size:.85rem;
  outline:none;transition:border-color .17s,box-shadow .17s}
.fld-in:focus{border-color:var(--fire);box-shadow:0 0 0 3px rgba(255,87,34,.11)}
.fld-in::placeholder{color:var(--muted)}
.fld-in.err{border-color:var(--danger)}
.fld-err{font-size:.68rem;color:#FF8A80;margin-top:3px;display:none}
.fld-err.on{display:block}
.fld-eye{position:absolute;right:11px;top:50%;transform:translateY(-50%);
  background:none;border:none;color:var(--muted);cursor:pointer;font-size:.85rem;transition:color .15s}
.fld-eye:hover{color:var(--text)}
.row2{display:grid;grid-template-columns:1fr 1fr;gap:11px}
.pwd-bar{height:3px;border-radius:99px;background:var(--card2);margin-top:5px;overflow:hidden}
.pwd-fill{height:100%;border-radius:99px;transition:width .3s,background .3s;width:0}

/* ━━━ ALERTS ━━━ */
.alert{border-radius:9px;padding:9px 13px;font-size:.78rem;margin-bottom:12px;
  display:none;align-items:center;gap:8px}
.alert.on{display:flex}
.alert-err{background:rgba(255,82,82,.09);border:1px solid rgba(255,82,82,.28);color:#FF8A80}
.alert-ok{background:rgba(0,230,118,.09);border:1px solid rgba(0,230,118,.28);color:#A5D6A7}
.alert-warn{background:rgba(255,183,0,.09);border:1px solid rgba(255,183,0,.28);color:var(--warn)}

/* ━━━ BUTTONS ━━━ */
.btn{display:inline-flex;align-items:center;justify-content:center;gap:7px;
  padding:11px 20px;border-radius:10px;font-family:var(--ff-head);font-size:.85rem;
  font-weight:600;cursor:pointer;border:none;transition:all .2s;width:100%}
.btn-fire{background:linear-gradient(135deg,var(--fire),#C62828);color:#fff;
  box-shadow:0 4px 18px rgba(255,87,34,.3)}
.btn-fire:hover{transform:translateY(-1px);box-shadow:0 8px 26px rgba(255,87,34,.45)}
.btn-fire:active{transform:none}
.btn-fire:disabled{opacity:.45;cursor:not-allowed;transform:none!important}
.btn-outline{background:transparent;border:1px solid var(--border2);color:var(--muted2);width:auto}
.btn-outline:hover{border-color:var(--border3);color:var(--text)}
.btn-jade{background:linear-gradient(135deg,var(--jade),#00796B);color:#fff}
.btn-sm{padding:6px 14px;font-size:.75rem}
.btn-xs{padding:4px 10px;font-size:.7rem}
.spin{width:16px;height:16px;border:2px solid rgba(255,255,255,.2);border-top-color:var(--fire);
  border-radius:50%;animation:rot .65s linear infinite;flex-shrink:0}
@keyframes rot{to{transform:rotate(360deg)}}

/* ━━━ STATS ROW ━━━ */
.stats{display:grid;grid-template-columns:repeat(4,1fr);gap:13px;margin-bottom:22px}
.stat-c{background:var(--card);border:1px solid var(--border);border-radius:var(--r);
  padding:16px;position:relative;overflow:hidden;transition:border-color .2s,transform .2s;cursor:default}
.stat-c:hover{border-color:var(--border2);transform:translateY(-2px)}
.stat-c::after{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:var(--ln,var(--fire))}
.stat-c:nth-child(2){--ln:var(--gold)}.stat-c:nth-child(3){--ln:var(--jade)}.stat-c:nth-child(4){--ln:var(--sky)}
.stat-lbl{font-size:.68rem;color:var(--muted2);text-transform:uppercase;letter-spacing:.05em}
.stat-v{font-size:1.8rem;font-weight:800;margin:5px 0 2px;line-height:1}
.stat-s{font-size:.68rem;color:var(--muted)}
.stat-ico{position:absolute;right:13px;top:50%;transform:translateY(-50%);font-size:1.9rem;opacity:.07}

/* ━━━ SECTION HEADER ━━━ */
.sec-hd{display:flex;align-items:center;justify-content:space-between;margin-bottom:15px;flex-wrap:wrap;gap:10px}
.sec-title{font-size:1.1rem;font-weight:700}
.ftabs{display:flex;gap:5px;flex-wrap:wrap}
.ft{padding:4px 13px;border-radius:99px;font-size:.73rem;cursor:pointer;
  border:1px solid var(--border);color:var(--muted2);background:transparent;
  font-family:var(--ff-head);transition:all .17s}
.ft:hover{border-color:var(--fire);color:var(--fire)}
.ft.on{background:var(--fire);border-color:var(--fire);color:#fff}

/* ━━━ SERVICE GRID ━━━ */
.svc-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(250px,1fr));gap:13px}
.svc-c{background:var(--card);border:1px solid var(--border);border-radius:var(--r);
  padding:17px;cursor:pointer;transition:all .22s;position:relative;overflow:hidden}
.svc-c:hover{transform:translateY(-4px);border-color:rgba(255,87,34,.4);box-shadow:0 12px 36px rgba(0,0,0,.35)}
.svc-cat{font-size:.6rem;letter-spacing:.08em;text-transform:uppercase;color:var(--muted);
  margin-bottom:9px;display:flex;align-items:center;gap:5px}
.cat-dot{width:5px;height:5px;border-radius:50%}
.svc-name{font-size:.95rem;font-weight:700;line-height:1.3;margin-bottom:9px}
.svc-foot{display:flex;justify-content:space-between;align-items:center;
  padding-top:11px;border-top:1px solid var(--border)}
.svc-price{font-size:1rem;font-weight:700;color:var(--fire)}
.svc-comm{font-size:.72rem;color:var(--jade)}
.auto-tag{position:absolute;top:10px;right:10px;background:rgba(0,191,165,.12);
  border:1px solid rgba(0,191,165,.3);color:var(--jade);font-size:.58rem;padding:2px 7px;border-radius:99px}

/* cat colors */
.cat-central .cat-dot{background:#7C4DFF}
.cat-state    .cat-dot{background:var(--fire)}
.cat-job      .cat-dot{background:var(--sky)}
.cat-utility  .cat-dot{background:var(--gold)}
.cat-travel   .cat-dot{background:#EC407A}
.cat-financial.cat-dot{background:var(--jade)}
.cat-food     .cat-dot{background:#FF7043}
.cat-shopping .cat-dot{background:#26C6DA}

/* ━━━ JOB CARDS ━━━ */
.job-list{display:flex;flex-direction:column;gap:9px}
.job-c{background:var(--card);border:1px solid var(--border);border-radius:var(--r);
  padding:14px 17px;display:flex;align-items:center;gap:13px;cursor:pointer;transition:all .2s}
.job-c:hover{border-color:rgba(41,182,246,.4);transform:translateX(4px)}
.job-dept-ico{width:40px;height:40px;border-radius:9px;background:var(--card2);
  display:flex;align-items:center;justify-content:center;font-size:1rem;flex-shrink:0}
.job-info{flex:1;min-width:0}
.job-title{font-size:.88rem;font-weight:700;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.job-meta{font-size:.68rem;color:var(--muted2);margin-top:3px}
.job-meta span{margin-right:10px}
.job-r{text-align:right;flex-shrink:0}
.job-fee{font-size:.8rem;color:var(--fire);font-weight:600}
.job-date{font-size:.67rem;color:var(--muted);margin-top:2px}
.hot-btn{display:inline-block;background:rgba(41,182,246,.1);border:1px solid rgba(41,182,246,.28);
  color:var(--sky);padding:4px 12px;border-radius:7px;font-size:.72rem;cursor:pointer;
  margin-top:5px;transition:all .17s;font-family:var(--ff-head);font-weight:500}
.hot-btn:hover{background:rgba(41,182,246,.2)}
.fire-tag{color:var(--fire2);font-size:.68rem;font-weight:700}

/* ━━━ TABLE ━━━ */
.tbl-wrap{background:var(--card);border:1px solid var(--border);border-radius:var(--r);overflow:hidden}
table{width:100%;border-collapse:collapse}
thead tr{background:rgba(255,255,255,.025)}
th{padding:10px 14px;font-size:.64rem;letter-spacing:.06em;text-transform:uppercase;
  color:var(--muted);text-align:left;font-weight:500;border-bottom:1px solid var(--border)}
td{padding:11px 14px;font-size:.79rem;border-bottom:1px solid rgba(255,255,255,.035)}
tr:last-child td{border-bottom:none}
tr:hover td{background:rgba(255,255,255,.015)}
.pill{padding:2px 9px;border-radius:99px;font-size:.65rem;font-weight:600}
.p-pending{background:rgba(255,183,0,.12);color:var(--warn)}
.p-processing{background:rgba(124,77,255,.12);color:#B39DDB}
.p-submitted{background:rgba(0,230,118,.12);color:var(--success)}
.p-failed{background:rgba(255,82,82,.12);color:var(--danger)}
.p-verified{background:rgba(0,191,165,.12);color:var(--jade)}

/* ━━━ PORTAL LAUNCH BOX ━━━ */
.portal-launch-box{background:rgba(0,191,165,.07);border:1.5px solid rgba(0,191,165,.3);
  border-radius:12px;padding:16px;margin-top:14px}
.portal-launch-box h4{color:var(--jade);font-size:.88rem;font-weight:700;margin-bottom:7px;
  display:flex;align-items:center;gap:7px}
.portal-launch-box p{color:var(--muted2);font-size:.77rem;margin-bottom:12px;line-height:1.5}
.portal-launch-box .portal-url{font-family:var(--ff-mono);font-size:.7rem;color:var(--muted2);
  background:rgba(255,255,255,.05);border-radius:5px;padding:2px 7px;word-break:break-all}
.btn-launch{background:linear-gradient(135deg,var(--jade),#00695C)!important;
  box-shadow:0 4px 18px rgba(0,191,165,.3)!important;font-size:.88rem!important;padding:12px 22px!important;width:auto!important}
.btn-launch:hover{transform:translateY(-2px)!important;box-shadow:0 8px 26px rgba(0,191,165,.45)!important}
.btn-google{background:linear-gradient(135deg,#4285F4,#1A73E8)!important;width:auto!important;
  box-shadow:0 4px 14px rgba(66,133,244,.3)!important}
.btn-google:hover{transform:translateY(-1px)!important}
.quick-launch-pill{position:absolute;bottom:10px;right:10px;background:rgba(0,191,165,.12);
  border:1px solid rgba(0,191,165,.35);color:var(--jade);font-size:.6rem;padding:3px 9px;
  border-radius:99px;cursor:pointer;transition:all .17s;z-index:2;display:none}
.svc-c:hover .quick-launch-pill{display:block}
.quick-launch-pill:hover{background:rgba(0,191,165,.25)}
.ext-badge{display:inline-flex;align-items:center;gap:5px;background:rgba(124,77,255,.1);
  border:1px solid rgba(124,77,255,.3);color:#B39DDB;font-size:.65rem;padding:2px 9px;
  border-radius:99px;margin-left:auto}
.fill-preview{background:var(--card2);border:1px solid var(--border);border-radius:9px;
  padding:10px 12px;margin:10px 0;display:grid;grid-template-columns:1fr 1fr;gap:4px 14px}
.fill-row{display:flex;justify-content:space-between;font-size:.72rem;padding:2px 0;
  border-bottom:1px solid rgba(255,255,255,.04)}
.fill-row:last-child{border-bottom:none}
.fill-key{color:var(--muted)}
.fill-val{color:var(--text);font-weight:500;max-width:120px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;text-align:right}

/* ━━━ MODAL ━━━ */

/* ━━━ FIELD DATA ROWS ━━━ */
.fd-list{display:flex;flex-direction:column;gap:4px}
.fd-row{display:flex;align-items:center;gap:8px;padding:7px 10px;
  background:rgba(255,255,255,.03);border-radius:8px;border:1px solid rgba(255,255,255,.06)}
.fd-k{font-size:.7rem;color:var(--muted);min-width:62px;flex-shrink:0;font-weight:500}
.fd-v{font-size:.8rem;color:var(--text);font-weight:600;flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.fd-copy{background:rgba(255,255,255,.07);border:1px solid rgba(255,255,255,.12);
  border-radius:6px;padding:3px 7px;cursor:pointer;font-size:.75rem;flex-shrink:0;
  transition:all .15s;color:var(--text)}
.fd-copy:hover{background:rgba(0,191,165,.2);border-color:var(--jade)}

/* ━━━ FLOATING DATA PANEL ━━━ */
#__dp__{position:fixed;bottom:70px;right:12px;z-index:99999;width:270px;
  font-family:var(--ff-head),Arial,sans-serif}
.dp-inner{background:#0C1220;border:1.5px solid rgba(255,87,34,.45);
  border-radius:14px;box-shadow:0 18px 55px rgba(0,0,0,.75);overflow:hidden}
.dp-hd{background:linear-gradient(135deg,#FF5722,#B71C1C);padding:10px 13px;
  display:flex;align-items:center;cursor:grab;user-select:none}
.dp-hd:active{cursor:grabbing}
.dp-ht{color:#fff;font-weight:700;font-size:.82rem;flex:1}
.dp-min,.dp-close{background:none;border:none;color:rgba(255,255,255,.75);
  cursor:pointer;font-size:.9rem;padding:0 3px;transition:color .15s}
.dp-min:hover,.dp-close:hover{color:#fff}
.dp-bd{padding:10px 12px;max-height:340px;overflow-y:auto}
.dp-svc{font-size:.65rem;color:var(--muted);text-transform:uppercase;letter-spacing:.06em;
  margin-bottom:6px;padding-bottom:5px;border-bottom:1px solid rgba(255,255,255,.07)}
.dp-row{display:flex;align-items:center;gap:6px;padding:5px 0;
  border-bottom:1px solid rgba(255,255,255,.05)}
.dp-row:last-child{border-bottom:none}
.dp-lbl{font-size:.65rem;color:var(--muted);min-width:52px;flex-shrink:0}
.dp-val{font-size:.78rem;color:var(--text);font-weight:600;flex:1;
  overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.dp-cp{background:rgba(255,255,255,.08);border:none;border-radius:5px;
  padding:2px 7px;font-size:.68rem;cursor:pointer;color:var(--muted2);
  flex-shrink:0;transition:all .15s;font-family:inherit}
.dp-cp:hover{background:rgba(0,191,165,.25);color:var(--jade)}
.dp-ft{padding:8px 12px;border-top:1px solid rgba(255,255,255,.07);
  display:flex;gap:6px}
.dp-ft button{flex:1;padding:6px;border-radius:7px;font-size:.7rem;
  font-weight:600;cursor:pointer;border:none;font-family:inherit;transition:all .15s}
.dp-btn-copy{background:rgba(255,87,34,.15);color:var(--fire2);border:1px solid rgba(255,87,34,.3)!important}
.dp-btn-copy:hover{background:rgba(255,87,34,.28)!important}
.dp-btn-hide{background:rgba(255,255,255,.06);color:var(--muted2);border:1px solid rgba(255,255,255,.1)!important}
.dp-minimized .dp-bd,.dp-minimized .dp-ft{display:none!important}

/* ━━━ APPLY DIRECT LINK ━━━ (for job cards) */
.job-portal-link{display:inline-flex;align-items:center;gap:5px;
  padding:5px 13px;background:linear-gradient(135deg,#00BFA5,#00796B);
  color:#fff;border-radius:8px;font-size:.73rem;font-weight:600;
  text-decoration:none;margin-top:5px;transition:all .17s}
.job-portal-link:hover{transform:translateX(2px);box-shadow:0 4px 12px rgba(0,191,165,.3)}

.overlay{position:fixed;inset:0;background:rgba(0,0,0,.72);backdrop-filter:blur(9px);
  z-index:500;display:none;align-items:center;justify-content:center;padding:16px}
.overlay.open{display:flex}
.modal{background:var(--panel);border:1px solid var(--border2);border-radius:var(--r3);
  width:100%;max-width:520px;max-height:93vh;overflow-y:auto;box-shadow:var(--sh);
  animation:mIn .24s cubic-bezier(.4,0,.2,1)}
@keyframes mIn{from{opacity:0;transform:scale(.95) translateY(14px)}to{opacity:1;transform:none}}
.modal-hd{padding:20px 22px 0;display:flex;justify-content:space-between;align-items:flex-start;
  position:sticky;top:0;background:var(--panel);z-index:2;
  padding-bottom:13px;border-bottom:1px solid var(--border)}
.modal-title{font-size:1.15rem;font-weight:700}
.modal-sub{font-size:.72rem;color:var(--muted2);margin-top:2px}
.modal-close{background:none;border:none;color:var(--muted);cursor:pointer;font-size:1.05rem;padding:3px;border-radius:6px;transition:color .15s}
.modal-close:hover{color:var(--text)}
.modal-body{padding:18px 22px 22px}

/* steps */
.steps{display:flex;align-items:center;margin-bottom:18px}
.step-n{display:flex;flex-direction:column;align-items:center;gap:4px;flex:1;position:relative;z-index:1}
.step-conn{flex:1;height:2px;background:var(--border2);margin-top:-16px;transition:background .3s}
.step-conn.done{background:var(--jade)}
.step-dot{width:27px;height:27px;border-radius:50%;border:2px solid var(--border2);background:var(--card2);
  display:flex;align-items:center;justify-content:center;font-size:.7rem;font-weight:700;transition:all .3s}
.step-n.done .step-dot{background:var(--jade);border-color:var(--jade);color:#fff}
.step-n.active .step-dot{background:var(--fire);border-color:var(--fire);color:#fff;box-shadow:0 0 0 4px rgba(255,87,34,.2)}
.step-lbl{font-size:.58rem;color:var(--muted);text-align:center}
.step-n.active .step-lbl{color:var(--fire)}
.step-n.done  .step-lbl{color:var(--jade)}

/* Upload zone */
.up-zone{border:2px dashed rgba(255,255,255,.09);border-radius:10px;padding:18px;text-align:center;
  cursor:pointer;transition:all .2s;position:relative}
.up-zone:hover{border-color:var(--fire);background:rgba(255,87,34,.03)}
.up-zone input{position:absolute;inset:0;opacity:0;cursor:pointer;width:100%;height:100%}
.up-zone .u-ico{font-size:1.5rem;color:var(--muted2);margin-bottom:5px}
.up-zone .u-txt{font-size:.78rem;color:var(--muted2)}
.up-zone .u-sub{font-size:.67rem;color:var(--muted);margin-top:2px}
.up-ok{background:rgba(0,230,118,.07);border:1px solid rgba(0,230,118,.22);border-radius:8px;
  padding:7px 11px;font-size:.76rem;color:#A5D6A7;margin-top:7px;display:none;align-items:center;gap:7px}
.up-ok.on{display:flex}
.up-prog{height:3px;background:var(--card2);border-radius:99px;margin-top:5px;overflow:hidden;display:none}
.up-prog.on{display:block}
.up-prog-fill{height:100%;background:linear-gradient(90deg,var(--fire),var(--gold));border-radius:99px;transition:width .3s}

/* extracted data */
.ex-grid{display:grid;grid-template-columns:1fr 1fr;gap:7px;margin:10px 0}
.ex-item{background:var(--card2);border:1px solid var(--border);border-radius:8px;padding:7px 10px}
.ex-l{font-size:.6rem;color:var(--muted);text-transform:uppercase;letter-spacing:.05em}
.ex-v{font-size:.8rem;font-weight:600;margin-top:2px;word-break:break-all}

/* QR box */
.qr-box{text-align:center;padding:14px 0}
.qr-box canvas,.qr-box img{width:170px!important;height:170px!important;
  border-radius:11px;border:3px solid var(--fire);padding:4px;background:#fff;
  display:block;margin:0 auto}
.qr-amt{font-size:2rem;font-weight:800;color:var(--fire);margin:11px 0 3px}
.qr-upi{font-size:.76rem;color:var(--muted2);font-family:var(--ff-mono)}
.qr-txn{font-family:var(--ff-mono);font-size:.69rem;background:var(--card2);
  border-radius:7px;padding:6px 10px;margin-top:9px;color:var(--muted2);
  word-break:break-all;cursor:pointer}
.qr-txn:hover{color:var(--fire)}
.pay-breakdown{background:var(--card2);border:1px solid var(--border);border-radius:9px;padding:12px;margin:12px 0;font-size:.78rem}
.pb-row{display:flex;justify-content:space-between;padding:4px 0;border-bottom:1px solid var(--border)}
.pb-row:last-child{border-bottom:none;font-weight:700;padding-top:8px}
.utr-wrap{background:rgba(255,183,0,.07);border:1px solid rgba(255,183,0,.2);
  border-radius:9px;padding:12px;margin:10px 0;font-size:.78rem}

/* success */
.success-scr{text-align:center;padding:18px 0}
.s-ico{font-size:3.2rem;animation:pop .45s cubic-bezier(.34,1.56,.64,1)}
@keyframes pop{from{transform:scale(.2);opacity:0}to{transform:scale(1);opacity:1}}
.s-title{font-size:1.35rem;font-weight:800;color:var(--jade);margin-top:10px}
.track-box{background:var(--card2);border:1px solid var(--border);border-radius:9px;
  padding:12px;margin:13px 0;text-align:left}
.track-box .tl{font-size:.66rem;color:var(--muted)}
.track-box .tv{font-family:var(--ff-mono);font-size:.85rem;color:var(--fire);margin-top:2px}

/* ━━━ CALC / EARNINGS ━━━ */
.calc-grid{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:20px}
.calc-out{background:var(--card2);border:1px solid var(--border);border-radius:var(--r);padding:16px}
.calc-out .cl{font-size:.68rem;color:var(--muted2);text-transform:uppercase;letter-spacing:.05em}
.calc-out .cv{font-size:1.6rem;font-weight:800;margin-top:5px}

/* ━━━ ADMIN PANEL ━━━ */
.admin-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(230px,1fr));gap:13px;margin-bottom:22px}
.admin-card{background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:17px}
.admin-card h4{font-size:.82rem;font-weight:700;margin-bottom:13px;color:var(--muted2)}
.franchise-item{display:flex;justify-content:space-between;align-items:center;
  padding:8px 0;border-bottom:1px solid var(--border);font-size:.78rem}
.franchise-item:last-child{border-bottom:none}
.fi-name{font-weight:600}
.fi-stat{text-align:right}
.fi-stat .v{color:var(--jade)}
.fi-stat .s{font-size:.65rem;color:var(--muted)}
.badge-admin{background:rgba(255,183,0,.15);border:1px solid rgba(255,183,0,.3);
  color:var(--gold);font-size:.6rem;padding:2px 7px;border-radius:99px;font-weight:600}

/* ━━━ OCR ━━━ */
.ocr-result{background:var(--card2);border:1px solid var(--border);border-radius:var(--r);padding:14px;margin-top:11px;display:none}
.ocr-result.on{display:block}
.ocr-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid var(--border);font-size:.77rem}
.ocr-row:last-child{border-bottom:none}
.ocr-row .l{color:var(--muted2)}
.ocr-row .v{color:var(--jade);font-weight:600}

/* ━━━ SECURITY STRIP ━━━ */
.sec-strip{display:flex;align-items:center;justify-content:center;gap:18px;flex-wrap:wrap;
  background:rgba(0,230,118,.04);border:1px solid rgba(0,230,118,.1);
  border-radius:var(--r);padding:9px 14px;margin-bottom:20px;font-size:.69rem;color:var(--jade)}
.sec-strip span{display:flex;align-items:center;gap:4px}

/* ━━━ SERVICE CATEGORIES (big tiles) ━━━ */
.cat-tiles{display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:11px;margin-bottom:22px}
.cat-tile{background:var(--card);border:1px solid var(--border);border-radius:var(--r);
  padding:16px 13px;text-align:center;cursor:pointer;transition:all .2s}
.cat-tile:hover{border-color:var(--fire);transform:translateY(-3px)}
.cat-tile .ico{font-size:1.8rem;margin-bottom:7px}
.cat-tile .nm{font-size:.78rem;font-weight:700}
.cat-tile .cnt{font-size:.62rem;color:var(--muted);margin-top:2px}

/* ━━━ TOASTS ━━━ */
.toasts{position:fixed;bottom:20px;right:20px;z-index:999;display:flex;flex-direction:column;gap:9px;pointer-events:none}
.toast{background:var(--card2);border:1px solid var(--border2);border-radius:9px;
  padding:11px 15px;font-size:.79rem;min-width:240px;display:flex;align-items:center;gap:9px;
  animation:tIn .28s ease;pointer-events:all;box-shadow:var(--sh2);max-width:330px}
.toast.ok{border-color:rgba(0,230,118,.35)}
.toast.err{border-color:rgba(255,82,82,.35)}
.toast.warn{border-color:rgba(255,183,0,.35)}
@keyframes tIn{from{opacity:0;transform:translateX(22px)}to{opacity:1;transform:none}}

/* ━━━ LOADING ━━━ */
.loader{position:fixed;inset:0;background:var(--ink);display:flex;flex-direction:column;
  align-items:center;justify-content:center;z-index:9999;gap:14px;transition:opacity .4s}
.loader.off{opacity:0;pointer-events:none}
.loader-bar{width:200px;height:3px;background:var(--card2);border-radius:99px;overflow:hidden}
.loader-fill{height:100%;background:linear-gradient(90deg,var(--fire),var(--gold));border-radius:99px;
  animation:lBar 1.5s ease-in-out infinite}
@keyframes lBar{0%{transform:translateX(-100%)}100%{transform:translateX(200px)}}

/* ━━━ RESPONSIVE ━━━ */
@media(max-width:1024px){.stats{grid-template-columns:1fr 1fr}}
@media(max-width:768px){
  .sidebar{transform:translateX(-100%);width:248px!important}
  .sidebar.mob-open{transform:translateX(0)}
  .main-area{margin-left:0!important}
  .stats{grid-template-columns:1fr 1fr}
  .row2{grid-template-columns:1fr}
  .calc-grid{grid-template-columns:1fr}
}
@media(max-width:480px){.stats{grid-template-columns:1fr}.page{padding:14px}}
.view{animation:viewIn .2s ease}
@keyframes viewIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:none}}

/* ━━━ SEARCH BAR ━━━ */
#svc-search:focus,#job-search:focus,#dash-search:focus{outline:none;}
#search-wrap:focus-within{border-color:var(--fire)!important;box-shadow:0 0 0 3px rgba(255,87,34,.12);}
#dash-search-wrap:focus-within{border-color:var(--fire)!important;box-shadow:0 0 0 3px rgba(255,87,34,.14);}
#search-results::-webkit-scrollbar,#dash-search-results::-webkit-scrollbar{width:4px;}
#search-results::-webkit-scrollbar-thumb,#dash-search-results::-webkit-scrollbar-thumb{background:var(--fire);border-radius:99px;}

/* ━━━ JOB CARD DIRECT BUTTON ━━━ */
.job-portal-link{display:inline-flex;align-items:center;gap:5px;
  padding:6px 12px;background:linear-gradient(135deg,var(--jade),#00695C);
  color:#fff;border-radius:8px;font-size:.72rem;font-weight:700;
  text-decoration:none;margin-top:5px;transition:all .17s;box-shadow:0 3px 10px rgba(0,191,165,.25);}
.job-portal-link:hover{transform:translateY(-1px);box-shadow:0 5px 16px rgba(0,191,165,.4);}

/* ━━━ SVC GRID DIRECT PORTAL ━━━ */
.svc-c{display:flex;flex-direction:column;}
.svc-c .svc-foot{margin-top:auto;}

/* ━━━ NEW 2026 BADGE ━━━ */
.badge-new{background:var(--jade);color:#fff;font-size:.58rem;padding:2px 7px;
  border-radius:99px;font-weight:800;vertical-align:middle;margin-left:5px;}

</style>
</head>
<body>

<!-- ━━━ LOADER ━━━ -->
<div class="loader" id="loader">
  <div style="font-size:2.4rem">🧠</div>
  <div style="font-size:1.6rem;font-weight:800">CyberAce Quantum</div>
  <div class="loader-bar"><div class="loader-fill"></div></div>
  <div style="font-size:.72rem;color:var(--muted)">Connecting to Firebase…</div>
</div>

<!-- ━━━ TOASTS ━━━ -->
<div class="toasts" id="toasts"></div>

<!-- ━━━ APPLY MODAL ━━━ -->
<div class="overlay" id="overlay" onclick="if(event.target.id==='overlay')closeModal()">
  <div class="modal">
    <div class="modal-hd">
      <div><div class="modal-title" id="mTitle">Apply</div><div class="modal-sub" id="mSub"></div></div>
      <button class="modal-close" onclick="closeModal()"><i class="fas fa-times"></i></button>
    </div>
    <div class="modal-body" id="mBody"></div>
  </div>
</div>

<!-- ━━━ APP ROOT ━━━ -->
<div id="root"></div>

<script>
'use strict';

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   FIREBASE CONFIG  (v9 compat — updated correct config)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const FB_CFG = {
  apiKey:            "AIzaSyBIebPXVjzVBZl380ZCELg2LUSCLr-6QrE",
  authDomain:        "neurocoin-ntc.firebaseapp.com",
  databaseURL:       "https://neurocoin-ntc-default-rtdb.firebaseio.com",
  projectId:         "neurocoin-ntc",
  storageBucket:     "neurocoin-ntc.firebasestorage.app",
  messagingSenderId: "1040732555800",
  appId:             "1:1040732555800:web:55e63b5ff81781c6b1916d",
  measurementId:     "G-6X8T0YQTQ3"
};
const UPI        = "bonagirispinoj-1@oksbi";
const ADMIN_EMAIL = "bonagirispinoj@gmail.com";
const PLATFORM_CUT = 0.15; // 15%
const SUB_FEE = 2000;

/* ━━━ Firebase Init ━━━ */
firebase.initializeApp(FB_CFG);
const auth    = firebase.auth();
const db      = firebase.firestore();
const storage = firebase.storage();
try { firebase.analytics(); } catch(e) {}

/* Firestore persistence (offline support) */
db.enablePersistence({ synchronizeTabs: true }).catch(() => {});

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SECURITY UTILITIES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const S = {
  esc(s) {
    if (s == null) return '';
    const d = document.createElement('div');
    d.textContent = String(s);
    return d.innerHTML;
  },
  clean(s, maxLen=500) {
    if (!s) return '';
    return String(s)
      .replace(/<[^>]*>/g,'')
      .replace(/[`\\]/g,'')
      .replace(/\b(SELECT|INSERT|UPDATE|DELETE|DROP|UNION|EXEC|SCRIPT)\b/gi,'')
      .trim().slice(0, maxLen);
  },
  isEmail: v => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test((v||'').trim()),
  isPhone: v => /^[6-9]\d{9}$/.test((v||'').replace(/\s/g,'')),
  isAadhaar: v => /^\d{12}$/.test((v||'').replace(/\s/g,'')),
  isPAN: v => /^[A-Z]{5}[0-9]{4}[A-Z]$/.test((v||'').trim().toUpperCase()),
  pwdStr(p) {
    let s=0;
    if(p.length>=8)s++;if(/[A-Z]/.test(p))s++;
    if(/\d/.test(p))s++;if(/[!@#$%^&*\-_]/.test(p))s++;
    return s;
  },
  _rl:{},
  rateLimit(key, max, ms) {
    const now=Date.now(), e=this._rl[key]||{n:0,t:now};
    if(now-e.t>ms){e.n=0;e.t=now;}
    e.n++;this._rl[key]=e;
    return e.n<=max;
  },
  token(len=12) {
    const a=new Uint8Array(len);
    crypto.getRandomValues(a);
    return Array.from(a,b=>b.toString(16).padStart(2,'0')).join('').toUpperCase().slice(0,len);
  }
};

/* ━━━ App State ━━━ */
const A = {
  user:null, isAdmin:false,
  svc:null, txnId:null,
  formData:{},
  uploadedFiles:{},
};

/* ━━━ Services Master Data ━━━ */
const SVCS = [
  // ═══ AADHAAR / UIDAI ═══
  {id:'aadh_down',  name:'Aadhaar Download (e-Aadhaar)',  cat:'uidai',   price:50,  comm:50,  auto:'full', url:'https://myaadhaar.uidai.gov.in/genricDownloadAadhaar', keywords:'uidai aadhaar download eaadhaar print'},
  {id:'aadh_update',name:'Aadhaar Update Address',        cat:'uidai',   price:150, comm:100, auto:'full', url:'https://myaadhaar.uidai.gov.in/update-aadhaar',         keywords:'uidai aadhaar address update correction'},
  {id:'aadh_name',  name:'Aadhaar Name/DOB Correction',   cat:'uidai',   price:150, comm:100, auto:'full', url:'https://myaadhaar.uidai.gov.in/update-aadhaar',         keywords:'uidai aadhaar name dob correction update'},
  {id:'aadh_mobile',name:'Aadhaar Mobile Update (OTP)',   cat:'uidai',   price:100, comm:80,  auto:'full', url:'https://myaadhaar.uidai.gov.in/',                        keywords:'uidai aadhaar mobile number update link'},
  {id:'aadh_new',   name:'Aadhaar New Enrolment',         cat:'uidai',   price:100, comm:80,  auto:'semi', url:'https://uidai.gov.in/en/my-aadhaar/about-your-aadhaar.html', keywords:'new aadhaar card enrolment apply'},
  {id:'aadh_lock',  name:'Aadhaar Biometric Lock/Unlock', cat:'uidai',   price:50,  comm:30,  auto:'full', url:'https://myaadhaar.uidai.gov.in/',                        keywords:'aadhaar biometric lock unlock'},
  {id:'aadh_virtual',name:'Aadhaar Virtual ID (VID)',     cat:'uidai',   price:50,  comm:30,  auto:'full', url:'https://myaadhaar.uidai.gov.in/',                        keywords:'aadhaar virtual id vid generate'},
  // ═══ PAN CARD ═══
  {id:'pan_new',    name:'PAN Card Apply (New)',           cat:'central', price:150, comm:100, auto:'full', url:'https://www.onlineservices.nsdl.com/paam/endUserRegisterContact.html', keywords:'pan card apply new nsdl'},
  {id:'pan_utm',    name:'PAN Card UTI (New)',             cat:'central', price:150, comm:100, auto:'full', url:'https://www.utiitsl.com/UTIITSL_SITE/jsp/index.jsp',    keywords:'pan card utiitsl apply new'},
  {id:'pan_update', name:'PAN Correction / Update',        cat:'central', price:120, comm:80,  auto:'full', url:'https://www.onlineservices.nsdl.com/paam/endUserRegisterContact.html', keywords:'pan correction name update address'},
  {id:'pan_link',   name:'PAN–Aadhaar Link',               cat:'central', price:80,  comm:50,  auto:'full', url:'https://eportal.incometax.gov.in/iec/foservices/#/pre-login/link-aadhaar', keywords:'pan aadhaar link income tax'},
  {id:'pan_status', name:'PAN Status Check',               cat:'central', price:30,  comm:20,  auto:'full', url:'https://tin.tin.nsdl.com/pantan/StatusTrack.html',      keywords:'pan status track check'},
  // ═══ VOTER ID ═══
  {id:'voter_new',  name:'Voter ID Form 6 (New)',          cat:'central', price:150, comm:100, auto:'full', url:'https://voters.eci.gov.in/signup-form6',                 keywords:'voter id form 6 new registration nvsp'},
  {id:'voter_cor',  name:'Voter ID Correction Form 8',     cat:'central', price:100, comm:80,  auto:'full', url:'https://voters.eci.gov.in/signup-form8',                 keywords:'voter id correction form 8 name address'},
  {id:'voter_del',  name:'Voter ID Deletion Form 7',       cat:'central', price:80,  comm:50,  auto:'full', url:'https://voters.eci.gov.in/signup-form7',                 keywords:'voter id deletion objection form 7'},
  {id:'voter_migration',name:'Voter ID Migration Form 6A', cat:'central', price:100, comm:80,  auto:'full', url:'https://voters.eci.gov.in/signup-form6a',                keywords:'voter id transfer migration form 6A'},
  {id:'voter_search',name:'Voter ID Search / Download',    cat:'central', price:30,  comm:20,  auto:'full', url:'https://electoralsearch.eci.gov.in/',                    keywords:'voter id search download print electoral roll'},
  // ═══ PASSPORT ═══
  {id:'passport_new',  name:'Passport Apply (New)',         cat:'central', price:500, comm:200, auto:'full', url:'https://www.passportindia.gov.in/AppOnlineProject/online/createNewAccount', keywords:'passport new apply online'},
  {id:'passport_renew',name:'Passport Renewal',            cat:'central', price:400, comm:150, auto:'full', url:'https://www.passportindia.gov.in/AppOnlineProject/online/createNewAccount', keywords:'passport renewal reissue'},
  {id:'passport_minor',name:'Minor Passport',              cat:'central', price:400, comm:150, auto:'semi', url:'https://www.passportindia.gov.in/AppOnlineProject/online/createNewAccount', keywords:'passport minor child apply'},
  {id:'passport_tatkal',name:'Tatkal Passport',            cat:'central', price:700, comm:250, auto:'semi', url:'https://www.passportindia.gov.in/',                      keywords:'tatkal passport urgent fast track'},
  // ═══ DRIVING LICENCE / RTO ═══
  {id:'dl_new',     name:'Driving Licence (New)',           cat:'state',   price:500, comm:150, auto:'full', url:'https://sarathi.parivahan.gov.in/sarathiservice/stateSelection.do',      keywords:'driving licence new apply dl sarathi'},
  {id:'dl_renew',   name:'DL Renewal',                     cat:'state',   price:400, comm:150, auto:'full', url:'https://sarathi.parivahan.gov.in/sarathiservice/stateSelection.do',      keywords:'driving licence renewal dl sarathi'},
  {id:'learner',    name:"Learner's Licence (LLR)",        cat:'state',   price:300, comm:150, auto:'full', url:'https://sarathi.parivahan.gov.in/sarathiservice/stateSelection.do',      keywords:'learner licence llr l test apply'},
  {id:'dl_intl',    name:'International Driving Permit',   cat:'state',   price:600, comm:200, auto:'semi', url:'https://sarathi.parivahan.gov.in/',                      keywords:'international driving permit idp'},
  {id:'rc',         name:'Vehicle RC Registration',        cat:'state',   price:400, comm:150, auto:'full', url:'https://vahan.parivahan.gov.in/vahanservice/',            keywords:'vehicle rc registration certificate'},
  {id:'rc_tf',      name:'RC Transfer',                    cat:'state',   price:500, comm:150, auto:'full', url:'https://vahan.parivahan.gov.in/vahanservice/',            keywords:'rc transfer vehicle ownership'},
  {id:'puc',        name:'Pollution Certificate (PUC)',    cat:'state',   price:200, comm:100, auto:'full', url:'https://vahan.parivahan.gov.in/vahanservice/',            keywords:'puc pollution certificate vahan'},
  {id:'challan',    name:'Traffic Challan Payment',        cat:'state',   price:50,  comm:30,  auto:'full', url:'https://echallan.parivahan.gov.in/index/accused-challan',  keywords:'traffic challan pay echallan fine'},
  {id:'rc_fitness', name:'Vehicle Fitness Certificate',    cat:'state',   price:300, comm:100, auto:'semi', url:'https://vahan.parivahan.gov.in/vahanservice/',            keywords:'vehicle fitness certificate test'},
  {id:'ration',     name:'Ration Card (New/Update)',       cat:'state',   price:200, comm:100, auto:'semi', url:'https://fcs.telangana.gov.in/',                          keywords:'ration card new update apply food'},
  {id:'domicile',   name:'Domicile / Residence Certificate',cat:'state',  price:200, comm:100, auto:'semi', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'domicile residence nativity certificate'},
  // ═══ CERTIFICATES ═══
  {id:'birth_cert', name:'Birth Certificate',              cat:'central', price:100, comm:80,  auto:'semi', url:'https://crsorgi.gov.in/',                                keywords:'birth certificate apply online crsorgi'},
  {id:'death_cert', name:'Death Certificate',              cat:'central', price:100, comm:80,  auto:'semi', url:'https://crsorgi.gov.in/',                                keywords:'death certificate apply online'},
  {id:'marriage_cert',name:'Marriage Certificate',         cat:'central', price:150, comm:100, auto:'semi', url:'https://ts.meeseva.gov.in/',                             keywords:'marriage certificate registration apply'},
  {id:'income_cert',name:'Income Certificate',             cat:'state',   price:100, comm:80,  auto:'semi', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'income certificate apply meeseva'},
  {id:'caste_cert', name:'Caste Certificate',              cat:'state',   price:100, comm:80,  auto:'semi', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'caste certificate apply sc st obc'},
  {id:'nativity',   name:'Nativity / Residence Cert',      cat:'state',   price:100, comm:80,  auto:'semi', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'nativity residence locality certificate'},
  {id:'obc_cert',   name:'OBC Certificate',                cat:'state',   price:100, comm:80,  auto:'semi', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'obc other backward class certificate'},
  {id:'disability', name:'Disability Certificate',         cat:'state',   price:100, comm:80,  auto:'semi', url:'https://swasthya.telangana.gov.in/',                     keywords:'disability certificate udid apply'},
  {id:'udid',       name:'UDID Card (Disability)',         cat:'central', price:100, comm:80,  auto:'full', url:'https://www.swavlambancard.gov.in/',                     keywords:'udid disability card swavlamban'},
  // ═══ MEESEVA / TSWAN PORTALS ═══
  {id:'meeseva',    name:'MeeSeva TS (All Certs)',         cat:'state',   price:150, comm:100, auto:'full', url:'https://ts.meeseva.gov.in/TSMeeseva/home.htm',            keywords:'meeseva telangana certificates services'},
  {id:'ap_meeseva', name:'AP MeeSeva (AP Certs)',          cat:'state',   price:150, comm:100, auto:'semi', url:'https://ap.meeseva.gov.in/',                             keywords:'ap meeseva andhra pradesh certificates'},
  {id:'ap_edistrict',name:'AP eDistrict',                  cat:'state',   price:150, comm:100, auto:'semi', url:'https://edistrict.ap.gov.in/',                           keywords:'ap edistrict andhra certificates'},
  {id:'ts_edistrict',name:'TS eDistrict',                  cat:'state',   price:150, comm:100, auto:'semi', url:'https://edistrict.telangana.gov.in/',                    keywords:'ts edistrict telangana certificates'},
  // ═══ EPFO / PF ═══
  {id:'pf_withdrawal',name:'EPF/PF Withdrawal (Form 31)',  cat:'financial',price:200,comm:150, auto:'full', url:'https://unifiedportal-mem.epfindia.gov.in/memberinterface/', keywords:'epf pf withdrawal claim form 31 19'},
  {id:'pf_transfer', name:'PF Transfer (Form 13)',         cat:'financial',price:200,comm:150, auto:'full', url:'https://unifiedportal-mem.epfindia.gov.in/memberinterface/', keywords:'pf transfer claim form 13 epfo'},
  {id:'pf_passbook', name:'EPF Passbook View',             cat:'financial',price:50, comm:30,  auto:'full', url:'https://passbook.epfindia.gov.in/MemberPassBook/login',  keywords:'epf passbook balance check view'},
  {id:'pf_uan',      name:'UAN Activation',                cat:'financial',price:100,comm:80,  auto:'full', url:'https://unifiedportal-mem.epfindia.gov.in/memberinterface/', keywords:'uan activation epf member portal'},
  {id:'esi_card',    name:'ESI Card / Medical',            cat:'financial',price:100,comm:80,  auto:'semi', url:'https://esic.in/',                                       keywords:'esi card esic medical insurance'},
  // ═══ INCOME TAX / GST / ITR ═══
  {id:'itr',         name:'ITR / Income Tax Filing',       cat:'financial',price:300,comm:300, auto:'full', url:'https://eportal.incometax.gov.in/',                      keywords:'itr income tax return filing efiling'},
  {id:'itr_refund',  name:'ITR Refund Status',             cat:'financial',price:50, comm:30,  auto:'full', url:'https://eportal.incometax.gov.in/',                      keywords:'itr refund status income tax refund'},
  {id:'form26as',    name:'Form 26AS Download',            cat:'financial',price:50, comm:30,  auto:'full', url:'https://eportal.incometax.gov.in/',                      keywords:'form 26as download tds tax credit'},
  {id:'gst_reg',     name:'GST Registration',              cat:'financial',price:500,comm:400, auto:'semi', url:'https://www.gst.gov.in/taxPayer/GSTRegistration',        keywords:'gst registration new apply'},
  {id:'gst_return',  name:'GST Return Filing',             cat:'financial',price:300,comm:250, auto:'semi', url:'https://www.gst.gov.in/',                                keywords:'gst return filing gstr 3b 1'},
  {id:'gst_num',     name:'GSTIN Verification',            cat:'financial',price:30, comm:20,  auto:'full', url:'https://www.gst.gov.in/taxPayer/TaxPayer',               keywords:'gstin verification search gst number'},
  // ═══ BANKING / FINANCIAL ═══
  {id:'pmjdy',       name:'Jan Dhan Account (PMJDY)',      cat:'financial',price:100,comm:80,  auto:'semi', url:'https://pmjdy.gov.in/',                                  keywords:'jan dhan account pmjdy zero balance'},
  {id:'mudra',       name:'Mudra Loan (PMMY)',             cat:'financial',price:500,comm:400, auto:'semi', url:'https://udyamimitra.in/',                                keywords:'mudra loan pmmy shishu kishore tarun'},
  {id:'demat',       name:'Demat Account Opening',         cat:'financial',price:150,comm:150, auto:'semi', url:'https://zerodha.com/open-account',                      keywords:'demat account open zerodha upstox'},
  {id:'insure',      name:'Insurance (Life/Health)',       cat:'financial',price:200,comm:200, auto:'semi', url:'https://www.policybazaar.com/',                         keywords:'insurance life health policy bazaar apply'},
  {id:'pmsby',       name:'PMSBY Accident Insurance',      cat:'financial',price:50, comm:30,  auto:'semi', url:'https://www.jansuraksha.gov.in/Forms-PMSBY.aspx',        keywords:'pmsby accident insurance 12 rupees'},
  {id:'pmjjby',      name:'PMJJBY Life Insurance',         cat:'financial',price:50, comm:30,  auto:'semi', url:'https://www.jansuraksha.gov.in/Forms-PMJJBY.aspx',       keywords:'pmjjby life insurance 436 rupees'},
  {id:'atal_pension',name:'Atal Pension Yojana (APY)',     cat:'financial',price:100,comm:80,  auto:'semi', url:'https://www.npscra.nsdl.co.in/scheme-details.php',       keywords:'atal pension yojana apy retirement'},
  {id:'nps',         name:'NPS Registration',              cat:'financial',price:300,comm:200, auto:'semi', url:'https://enps.nsdl.com/eNPS/NationalPensionSystem.html',  keywords:'nps national pension system registration'},
  {id:'kisan_cc',    name:'Kisan Credit Card (KCC)',       cat:'financial',price:300,comm:200, auto:'semi', url:'https://www.pmkisan.gov.in/',                           keywords:'kisan credit card kcc agriculture loan'},
  {id:'loan',        name:'Personal Loan Application',     cat:'financial',price:500,comm:500, auto:'semi', url:'https://www.paisabazaar.com/',                          keywords:'personal loan apply online'},
  {id:'home_loan',   name:'Home Loan Application',         cat:'financial',price:500,comm:500, auto:'semi', url:'https://pmay-urban.gov.in/',                            keywords:'home loan pmay housing apply'},
  // ═══ AGRICULTURE / KISAN ═══
  {id:'pm_kisan',    name:'PM Kisan Registration',         cat:'welfare', price:100, comm:80,  auto:'full', url:'https://pmkisan.gov.in/RegistrationForm.aspx',           keywords:'pm kisan samman nidhi registration 6000'},
  {id:'fasal_bima',  name:'PM Fasal Bima Yojana',         cat:'welfare', price:150, comm:100, auto:'semi', url:'https://pmfby.gov.in/',                                  keywords:'fasal bima crop insurance pmfby'},
  {id:'kisan_reg',   name:'Farmer Registration (PM Kisan)',cat:'welfare', price:100, comm:80,  auto:'full', url:'https://pmkisan.gov.in/',                               keywords:'kisan farmer registration pm kisan portal'},
  // ═══ WELFARE SCHEMES ═══
  {id:'ration_card', name:'Ration Card New / Update',      cat:'welfare', price:200, comm:150, auto:'semi', url:'https://fcs.telangana.gov.in/',                          keywords:'ration card new apply update'},
  {id:'old_age',     name:'Old Age Pension (NSAP)',        cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://nsap.nic.in/',                                   keywords:'old age pension nsap apply'},
  {id:'widow_pen',   name:'Widow Pension',                 cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://nsap.nic.in/',                                   keywords:'widow pension apply'},
  {id:'disability_pen',name:'Disability Pension',          cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://nsap.nic.in/',                                   keywords:'disability pension apply'},
  {id:'pmay',        name:'PMAY Housing (Awas Yojana)',    cat:'welfare', price:200, comm:150, auto:'semi', url:'https://pmay-urban.gov.in/registration',                 keywords:'pmay housing awas yojana apply urban rural'},
  {id:'nsp',         name:'NSP Scholarship',               cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://scholarships.gov.in/',                          keywords:'nsp scholarship national portal apply'},
  {id:'ayushman',    name:'Ayushman Bharat / PMJAY',       cat:'welfare', price:100, comm:80,  auto:'full', url:'https://pmjay.gov.in/',                                  keywords:'ayushman bharat pmjay health card golden card'},
  {id:'abha',        name:'ABHA Health ID (Ayushman)',     cat:'welfare', price:50,  comm:30,  auto:'full', url:'https://abha.abdm.gov.in/',                              keywords:'abha health id abdm ayushman digital'},
  {id:'e_shram',     name:'e-Shram Card (Unorganised)',   cat:'welfare', price:50,  comm:30,  auto:'full', url:'https://eshram.gov.in/',                                 keywords:'e shram card unorganised labour registration'},
  {id:'mahatma_gan', name:'MGNREGS / Job Card',            cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://nrega.nic.in/',                                  keywords:'mgnrega nregs job card apply 100 days'},
  {id:'pradhan_ujj', name:'Ujjwala Gas Connection',        cat:'welfare', price:100, comm:80,  auto:'semi', url:'https://pmuy.gov.in/',                                   keywords:'ujjwala gas connection pmuy lpg'},
  // ═══ UDYAM / BUSINESS ═══
  {id:'udyam',       name:'Udyam Registration (MSME)',     cat:'business',price:200, comm:150, auto:'full', url:'https://udyamregistration.gov.in/',                     keywords:'udyam registration msme udyog aadhaar'},
  {id:'fssai',       name:'FSSAI Food Licence',            cat:'business',price:500, comm:400, auto:'semi', url:'https://foscos.fssai.gov.in/',                          keywords:'fssai food licence registration apply'},
  {id:'shop_est',    name:'Shop & Establishment Licence',  cat:'business',price:300, comm:200, auto:'semi', url:'https://ts.meeseva.gov.in/',                             keywords:'shop establishment licence registration'},
  {id:'trade_lic',   name:'Trade Licence',                 cat:'business',price:300, comm:200, auto:'semi', url:'https://ts.meeseva.gov.in/',                             keywords:'trade licence apply municipal'},
  {id:'gumasta',     name:'Gumasta / Business Licence',    cat:'business',price:300, comm:200, auto:'semi', url:'https://ts.meeseva.gov.in/',                             keywords:'gumasta professional tax business licence'},
  // ═══ UTILITIES ═══
  {id:'elec',        name:'Electricity Bill Pay',          cat:'utility', price:20,  comm:20,  auto:'full', url:'https://tssouthernpower.com/',                          keywords:'electricity bill payment tsspdcl tsgenco'},
  {id:'gas',         name:'Gas Cylinder Booking',          cat:'utility', price:25,  comm:25,  auto:'full', url:'https://ebharatgas.com/',                               keywords:'gas cylinder booking lpg bharat'},
  {id:'hp_gas',      name:'HP Gas Booking',                cat:'utility', price:25,  comm:25,  auto:'full', url:'https://www.hindustanpetroleum.com/book-cylinder',       keywords:'hp gas cylinder booking'},
  {id:'indane',      name:'Indane Gas Booking',            cat:'utility', price:25,  comm:25,  auto:'full', url:'https://www.indianoil.co.in/',                          keywords:'indane gas cylinder booking indian oil'},
  {id:'mobile',      name:'Mobile Recharge',               cat:'utility', price:15,  comm:15,  auto:'full', url:'https://prepaid.jio.com/',                              keywords:'mobile recharge jio airtel vi bsnl'},
  {id:'dth',         name:'DTH Recharge',                  cat:'utility', price:15,  comm:15,  auto:'full', url:'https://www.tataplay.com/recharge',                     keywords:'dth recharge tata play dish tv sun direct'},
  {id:'water',       name:'Water Bill Payment',            cat:'utility', price:20,  comm:20,  auto:'full', url:'https://www.hmwssb.gov.in/',                            keywords:'water bill payment hmwssb hyderabad'},
  {id:'fastag',      name:'FASTag Recharge',               cat:'utility', price:25,  comm:25,  auto:'full', url:'https://fastag.ihmcl.com/',                             keywords:'fastag recharge toll national highway'},
  {id:'broadband',   name:'Broadband Bill Pay',            cat:'utility', price:20,  comm:20,  auto:'semi', url:'https://bsnl.co.in/',                                   keywords:'broadband bill payment bsnl jio fiber'},
  // ═══ EDUCATION / NTA ═══
  {id:'neet',        name:'NEET UG 2026',                  cat:'exam',    price:150, comm:150, auto:'full', url:'https://neet.nta.nic.in/',                              keywords:'neet ug 2026 medical entrance apply nta'},
  {id:'jee_main',    name:'JEE Main 2026',                 cat:'exam',    price:150, comm:150, auto:'full', url:'https://jeemain.nta.nic.in/',                           keywords:'jee main 2026 engineering entrance apply nta'},
  {id:'jee_adv',     name:'JEE Advanced 2026',             cat:'exam',    price:150, comm:150, auto:'semi', url:'https://jeeadv.ac.in/',                                 keywords:'jee advanced 2026 iit entrance'},
  {id:'ctet',        name:'CTET 2026',                     cat:'exam',    price:120, comm:120, auto:'full', url:'https://ctet.nic.in/',                                  keywords:'ctet 2026 central teacher eligibility test'},
  {id:'cuet',        name:'CUET UG 2026',                  cat:'exam',    price:120, comm:100, auto:'full', url:'https://cuet.samarth.ac.in/',                           keywords:'cuet 2026 central university entrance test'},
  {id:'ugcnet',      name:'UGC NET 2026',                  cat:'exam',    price:120, comm:100, auto:'full', url:'https://ugcnet.nta.nic.in/',                            keywords:'ugc net 2026 nta assistant professor'},
  {id:'clat',        name:'CLAT 2026 (Law)',               cat:'exam',    price:120, comm:100, auto:'semi', url:'https://consortiumofnlus.ac.in/',                       keywords:'clat 2026 law entrance nlus'},
  // ═══ CENTRAL GOVT JOBS ═══
  {id:'upsc',        name:'UPSC Civil Services 2026',      cat:'job',     price:200, comm:200, auto:'full', url:'https://upsconline.nic.in/',                            keywords:'upsc cse 2026 civil services ias ips ifs'},
  {id:'ssc_cgl',     name:'SSC CGL 2026',                  cat:'job',     price:150, comm:150, auto:'full', url:'https://ssc.nic.in/',                                   keywords:'ssc cgl 2026 combined graduate level'},
  {id:'ssc_chsl',    name:'SSC CHSL 2026',                 cat:'job',     price:150, comm:150, auto:'full', url:'https://ssc.nic.in/',                                   keywords:'ssc chsl 2026 combined higher secondary'},
  {id:'ssc_gd',      name:'SSC GD Constable 2026',         cat:'job',     price:150, comm:150, auto:'full', url:'https://ssc.nic.in/',                                   keywords:'ssc gd constable 2026 police'},
  {id:'ssc_mts',     name:'SSC MTS 2026',                  cat:'job',     price:120, comm:100, auto:'full', url:'https://ssc.nic.in/',                                   keywords:'ssc mts 2026 multi tasking staff'},
  {id:'rrb_ntpc',    name:'RRB NTPC 2026',                 cat:'job',     price:150, comm:150, auto:'full', url:'https://rrbapply.gov.in/',                              keywords:'rrb ntpc 2026 railway non technical popular'},
  {id:'rrb_gd',      name:'Railway Group D 2026',          cat:'job',     price:150, comm:150, auto:'full', url:'https://rrbapply.gov.in/',                              keywords:'rrb group d 2026 railway level 1'},
  {id:'rrb_je',      name:'RRB JE 2026',                   cat:'job',     price:150, comm:150, auto:'semi', url:'https://rrbapply.gov.in/',                              keywords:'rrb je 2026 junior engineer railway'},
  {id:'ibps_po',     name:'IBPS PO 2026',                  cat:'job',     price:150, comm:150, auto:'full', url:'https://ibps.in/',                                      keywords:'ibps po 2026 bank probationary officer'},
  {id:'ibps_cl',     name:'IBPS Clerk 2026',               cat:'job',     price:150, comm:150, auto:'full', url:'https://ibps.in/',                                      keywords:'ibps clerk 2026 bank clerk'},
  {id:'ibps_rrb',    name:'IBPS RRB 2026',                 cat:'job',     price:150, comm:150, auto:'full', url:'https://ibps.in/',                                      keywords:'ibps rrb 2026 regional rural bank officer'},
  {id:'sbi_po',      name:'SBI PO 2026',                   cat:'job',     price:150, comm:150, auto:'full', url:'https://sbi.co.in/careers',                             keywords:'sbi po 2026 state bank probationary officer'},
  {id:'sbi_clerk',   name:'SBI Clerk 2026',                cat:'job',     price:150, comm:150, auto:'full', url:'https://sbi.co.in/careers',                             keywords:'sbi clerk 2026 state bank junior associate'},
  {id:'nabard',      name:'NABARD Grade A/B 2026',         cat:'job',     price:150, comm:150, auto:'semi', url:'https://nabard.org/Career.aspx',                        keywords:'nabard grade a b 2026 agriculture bank'},
  {id:'india_post',  name:'India Post GDS 2026',           cat:'job',     price:100, comm:100, auto:'full', url:'https://indiapostgdsonline.gov.in/',                    keywords:'india post gds 2026 gramin dak sevak'},
  {id:'constable',   name:'Central Police (CAPF) 2026',    cat:'job',     price:150, comm:150, auto:'semi', url:'https://ssc.nic.in/',                                   keywords:'capf constable asi 2026 central police'},
  {id:'crpf',        name:'CRPF Constable 2026',           cat:'job',     price:150, comm:150, auto:'full', url:'https://crpf.gov.in/',                                  keywords:'crpf constable 2026 para military'},
  {id:'bsf',         name:'BSF Constable 2026',            cat:'job',     price:150, comm:150, auto:'full', url:'https://rectt.bsf.gov.in/',                             keywords:'bsf constable 2026 border security force'},
  {id:'cisf',        name:'CISF Constable 2026',           cat:'job',     price:150, comm:150, auto:'full', url:'https://cisfrectt.cisf.gov.in/',                        keywords:'cisf constable 2026 industrial security'},
  {id:'nda',         name:'NDA 2026',                      cat:'job',     price:150, comm:150, auto:'full', url:'https://upsconline.nic.in/',                            keywords:'nda 2026 national defence academy army navy airforce'},
  {id:'cds',         name:'CDS 2026',                      cat:'job',     price:150, comm:150, auto:'full', url:'https://upsconline.nic.in/',                            keywords:'cds 2026 combined defence services army'},
  {id:'afcat',       name:'AFCAT 2026 (Air Force)',        cat:'job',     price:150, comm:150, auto:'semi', url:'https://afcat.cdac.in/',                                keywords:'afcat 2026 air force common admission test'},
  // ═══ TELANGANA STATE JOBS ═══
  {id:'tgpsc_g1',    name:'TGPSC Group-I 2026',            cat:'state_job',price:150,comm:150, auto:'full', url:'https://tgpsc.telangana.gov.in/',                       keywords:'tgpsc tspsc group 1 2026 telangana'},
  {id:'tgpsc_g2',    name:'TGPSC Group-II 2026',           cat:'state_job',price:150,comm:150, auto:'full', url:'https://tgpsc.telangana.gov.in/',                       keywords:'tgpsc group 2 2026 telangana psc'},
  {id:'tgpsc_g3',    name:'TGPSC Group-III 2026',          cat:'state_job',price:120,comm:100, auto:'full', url:'https://tgpsc.telangana.gov.in/',                       keywords:'tgpsc group 3 2026 telangana'},
  {id:'tgpsc_g4',    name:'TGPSC Group-IV (MESO)',         cat:'state_job',price:100,comm:80,  auto:'full', url:'https://tgpsc.telangana.gov.in/',                       keywords:'tgpsc group 4 meso 2026 telangana'},
  {id:'ts_police',   name:'TS Police Constable 2026',      cat:'state_job',price:150,comm:150, auto:'full', url:'https://tspolice.gov.in/',                              keywords:'telangana police constable 2026 ts'},
  {id:'ts_si',       name:'TS Sub Inspector 2026',         cat:'state_job',price:150,comm:150, auto:'full', url:'https://tspolice.gov.in/',                              keywords:'telangana si sub inspector 2026'},
  {id:'ts_teacher',  name:'TG TET / DSC 2026',             cat:'state_job',price:120,comm:100, auto:'semi', url:'https://tstet.cgg.gov.in/',                             keywords:'tstet ts teacher eligibility test dsc 2026'},
  {id:'ts_trb',      name:'TS TRB Teachers 2026',          cat:'state_job',price:150,comm:150, auto:'semi', url:'https://trb.telangana.gov.in/',                         keywords:'trb teacher recruitment board telangana 2026'},
  {id:'ts_tet',      name:'TS TET 2026',                   cat:'state_job',price:120,comm:100, auto:'full', url:'https://tstet.cgg.gov.in/',                             keywords:'ts tet 2026 telangana teacher eligibility'},
  {id:'ts_psc',      name:'TGPSC AEE 2026 (Engineers)',    cat:'state_job',price:150,comm:150, auto:'semi', url:'https://tgpsc.telangana.gov.in/',                       keywords:'tgpsc aee assistant executive engineer 2026'},
  // ═══ ANDHRA PRADESH JOBS ═══
  {id:'appsc_g1',    name:'APPSC Group-I 2026',            cat:'state_job',price:150,comm:150, auto:'full', url:'https://psc.ap.gov.in/',                                keywords:'appsc group 1 2026 andhra pradesh'},
  {id:'appsc_g2',    name:'APPSC Group-II 2026',           cat:'state_job',price:150,comm:150, auto:'full', url:'https://psc.ap.gov.in/',                                keywords:'appsc group 2 2026 ap psc'},
  {id:'ap_police',   name:'AP Police Constable 2026',      cat:'state_job',price:150,comm:150, auto:'full', url:'https://slprb.ap.gov.in/',                              keywords:'ap police constable 2026 andhra pradesh'},
  {id:'ap_teacher',  name:'AP TET 2026',                   cat:'state_job',price:120,comm:100, auto:'full', url:'https://aptet.apcfss.in/',                              keywords:'ap tet 2026 andhra teacher eligibility'},
  // ═══ TRAVEL & BOOKING ═══
  {id:'irctc',       name:'Train Ticket (IRCTC)',          cat:'travel',  price:30,  comm:30,  auto:'full', url:'https://www.irctc.co.in/',                              keywords:'train ticket irctc booking railway reservation'},
  {id:'flight',      name:'Flight Booking',                cat:'travel',  price:199, comm:199, auto:'semi', url:'https://www.makemytrip.com/flights/',                   keywords:'flight ticket booking makemytrip goibibo'},
  {id:'bus',         name:'Bus Ticket (redBus)',           cat:'travel',  price:20,  comm:20,  auto:'full', url:'https://www.redbus.in/',                                keywords:'bus ticket booking redbus online'},
  {id:'ola',         name:'Ola Cab Booking',               cat:'travel',  price:20,  comm:20,  auto:'semi', url:'https://book.olacabs.com/',                             keywords:'ola cab book ride booking'},
  {id:'uber',        name:'Uber Cab Booking',              cat:'travel',  price:20,  comm:20,  auto:'semi', url:'https://m.uber.com/',                                   keywords:'uber cab book ride'},
  {id:'rapido',      name:'Rapido Bike/Auto',              cat:'travel',  price:15,  comm:15,  auto:'semi', url:'https://rapido.bike/',                                  keywords:'rapido bike auto booking ride'},
  {id:'cinema',      name:'Cinema Ticket (BookMyShow)',    cat:'travel',  price:30,  comm:30,  auto:'semi', url:'https://in.bookmyshow.com/',                            keywords:'cinema movie ticket bookmyshow'},
  // ═══ FOOD & SHOPPING ═══
  {id:'swiggy',      name:'Swiggy Food Order',             cat:'food',    price:20,  comm:20,  auto:'semi', url:'https://www.swiggy.com/',                               keywords:'swiggy food order delivery'},
  {id:'zomato',      name:'Zomato Food Order',             cat:'food',    price:20,  comm:20,  auto:'semi', url:'https://www.zomato.com/',                               keywords:'zomato food order delivery'},
  {id:'flipkart',    name:'Flipkart Order',                cat:'shopping',price:20,  comm:20,  auto:'semi', url:'https://www.flipkart.com/',                             keywords:'flipkart shop order buy online'},
  {id:'amazon',      name:'Amazon India Order',            cat:'shopping',price:20,  comm:20,  auto:'semi', url:'https://www.amazon.in/',                                keywords:'amazon india shop buy order'},
  {id:'meesho',      name:'Meesho Order',                  cat:'shopping',price:15,  comm:15,  auto:'semi', url:'https://www.meesho.com/',                               keywords:'meesho shop buy order online'},
  {id:'flipkart_reg',name:'Flipkart Registration',         cat:'shopping',price:20,  comm:20,  auto:'full', url:'https://www.flipkart.com/account/login',                keywords:'flipkart new account registration signup'},
  {id:'amazon_reg',  name:'Amazon Account Registration',   cat:'shopping',price:20,  comm:20,  auto:'full', url:'https://www.amazon.in/ap/register',                    keywords:'amazon new account registration signup'},
  // ═══ DIGITAL INDIA / MISC ═══
  {id:'digilocker',  name:'DigiLocker Registration',       cat:'digital', price:50,  comm:30,  auto:'full', url:'https://digilocker.gov.in/',                            keywords:'digilocker digital documents register'},
  {id:'cowin',       name:'CoWIN Vaccination Certificate', cat:'digital', price:50,  comm:30,  auto:'full', url:'https://www.cowin.gov.in/',                             keywords:'cowin vaccination certificate covid download'},
  {id:'umang',       name:'UMANG App Services',            cat:'digital', price:50,  comm:30,  auto:'semi', url:'https://web.umang.gov.in/',                             keywords:'umang app government services portal'},
];



const JOBS = [
  // CENTRAL 2026
  {id:'upsc26',    title:'UPSC CSE 2026',          dept:'UPSC',  cat:'central',    posts:1056,  date:'2026-02-15', fee:100,  hot:9200,  url:'https://upsconline.nic.in/',       new:true},
  {id:'ssc_cgl26', title:'SSC CGL 2026',            dept:'SSC',   cat:'central',    posts:18000, date:'2026-03-01', fee:100,  hot:16000, url:'https://ssc.nic.in/',              new:true},
  {id:'ssc_chsl26',title:'SSC CHSL 2026',           dept:'SSC',   cat:'central',    posts:5000,  date:'2026-03-10', fee:100,  hot:11000, url:'https://ssc.nic.in/',              new:true},
  {id:'ssc_gd26',  title:'SSC GD Constable 2026',   dept:'SSC',   cat:'central',    posts:50000, date:'2026-04-01', fee:100,  hot:28000, url:'https://ssc.nic.in/',              new:true},
  {id:'rrb_ntpc26',title:'RRB NTPC 2026',            dept:'RRB',   cat:'central',    posts:11558, date:'2026-02-20', fee:100,  hot:22000, url:'https://rrbapply.gov.in/',         new:true},
  {id:'rrb_gd26',  title:'Railway Group D 2026',    dept:'RRB',   cat:'central',    posts:32000, date:'2026-03-15', fee:100,  hot:25000, url:'https://rrbapply.gov.in/',         new:true},
  {id:'india_post26',title:'India Post GDS 2026',   dept:'Post',  cat:'central',    posts:44228, date:'2026-01-20', fee:0,   hot:18000, url:'https://indiapostgdsonline.gov.in/', new:true},
  // BANKING 2026
  {id:'ibps_po26', title:'IBPS PO 2026',             dept:'IBPS',  cat:'banking',    posts:5000,  date:'2026-04-10', fee:850,  hot:12000, url:'https://ibps.in/',                 new:true},
  {id:'ibps_cl26', title:'IBPS Clerk 2026',          dept:'IBPS',  cat:'banking',    posts:6500,  date:'2026-04-20', fee:800,  hot:9500,  url:'https://ibps.in/',                 new:true},
  {id:'ibps_rrb26',title:'IBPS RRB Officer 2026',   dept:'IBPS',  cat:'banking',    posts:9500,  date:'2026-06-01', fee:700,  hot:8000,  url:'https://ibps.in/',                 new:true},
  {id:'sbi_po26',  title:'SBI PO 2026',              dept:'SBI',   cat:'banking',    posts:600,   date:'2026-04-15', fee:750,  hot:7000,  url:'https://sbi.co.in/careers',        new:true},
  {id:'sbi_cl26',  title:'SBI Clerk 2026',           dept:'SBI',   cat:'banking',    posts:14000, date:'2026-05-01', fee:750,  hot:11000, url:'https://sbi.co.in/careers',        new:true},
  {id:'nabard26',  title:'NABARD Grade A/B 2026',   dept:'NABARD',cat:'banking',    posts:160,   date:'2026-03-20', fee:800,  hot:4500,  url:'https://nabard.org/Career.aspx',   new:true},
  // STATE - TELANGANA / TGPSC 2026
  {id:'tgpsc_g1_26',title:'TGPSC Group-I 2026',     dept:'TGPSC', cat:'state',      posts:563,   date:'2026-04-01', fee:200,  hot:7500,  url:'https://tgpsc.telangana.gov.in/',  new:true},
  {id:'tgpsc_g2_26',title:'TGPSC Group-II 2026',    dept:'TGPSC', cat:'state',      posts:820,   date:'2026-04-15', fee:150,  hot:6200,  url:'https://tgpsc.telangana.gov.in/',  new:true},
  {id:'tgpsc_aee',  title:'TGPSC AEE (Engg) 2026',  dept:'TGPSC', cat:'state',      posts:300,   date:'2026-05-01', fee:200,  hot:3800,  url:'https://tgpsc.telangana.gov.in/',  new:true},
  {id:'ts_police26',title:'TS Police Constable 2026',dept:'TSPOLICE',cat:'state',   posts:2000,  date:'2026-03-10', fee:200,  hot:8800,  url:'https://tspolice.gov.in/',          new:true},
  {id:'ts_si26',   title:'TS Sub Inspector 2026',   dept:'TSPOLICE',cat:'state',    posts:500,   date:'2026-03-20', fee:200,  hot:5500,  url:'https://tspolice.gov.in/',          new:true},
  {id:'ts_dsc26',  title:'TS DSC Teachers 2026',    dept:'TGPSC', cat:'teaching',   posts:6000,  date:'2026-04-10', fee:100,  hot:9000,  url:'https://trb.telangana.gov.in/',     new:true},
  // STATE - AP 2026
  {id:'appsc_g1_26',title:'APPSC Group-I 2026',     dept:'APPSC', cat:'state',      posts:400,   date:'2026-04-20', fee:250,  hot:6500,  url:'https://psc.ap.gov.in/',           new:true},
  {id:'appsc_g2_26',title:'APPSC Group-II 2026',    dept:'APPSC', cat:'state',      posts:750,   date:'2026-05-01', fee:200,  hot:5800,  url:'https://psc.ap.gov.in/',           new:true},
  {id:'ap_police26',title:'AP Police Constable 2026',dept:'SLPRB',cat:'state',      posts:3500,  date:'2026-03-25', fee:200,  hot:9500,  url:'https://slprb.ap.gov.in/',         new:true},
  // TEACHING 2026
  {id:'ctet26',    title:'CTET 2026',                dept:'CBSE',  cat:'teaching',   posts:0,     date:'2026-03-15', fee:1000, hot:4000,  url:'https://ctet.nic.in/',              new:true},
  {id:'kv_tgt26',  title:'KV TGT/PGT 2026',         dept:'KVS',   cat:'teaching',   posts:12000, date:'2026-04-01', fee:1000, hot:7800,  url:'https://kvsangathan.nic.in/',       new:true},
  {id:'nv_tgt26',  title:'NVS TGT/PGT 2026',        dept:'NVS',   cat:'teaching',   posts:3000,  date:'2026-05-01', fee:1000, hot:5200,  url:'https://navodaya.gov.in/',          new:true},
  // MEDICAL/ENGINEERING 2026
  {id:'neet26',    title:'NEET UG 2026',             dept:'NTA',   cat:'medical',    posts:0,     date:'2026-05-04', fee:1700, hot:45000, url:'https://neet.nta.nic.in/',         new:true},
  {id:'neetpg26',  title:'NEET PG 2026',             dept:'NBE',   cat:'medical',    posts:0,     date:'2026-06-15', fee:4250, hot:18000, url:'https://nbe.edu.in/',               new:true},
  {id:'jee_m26',   title:'JEE Main 2026 Sess 1',    dept:'NTA',   cat:'engg',       posts:0,     date:'2026-01-22', fee:900,  hot:32000, url:'https://jeemain.nta.nic.in/',       new:true},
  // DEFENCE 2026
  {id:'nda26',     title:'NDA 2026 (I)',              dept:'UPSC',  cat:'defence',    posts:400,   date:'2026-03-01', fee:100,  hot:8500,  url:'https://upsconline.nic.in/',        new:true},
  {id:'crpf26',    title:'CRPF Constable 2026',      dept:'CRPF',  cat:'defence',    posts:9212,  date:'2026-02-28', fee:100,  hot:7200,  url:'https://crpf.gov.in/',              new:true},
  {id:'bsf26',     title:'BSF Constable 2026',       dept:'BSF',   cat:'defence',    posts:2788,  date:'2026-03-10', fee:100,  hot:6800,  url:'https://rectt.bsf.gov.in/',         new:true},
  // SCHOLARSHIP 2026
  {id:'nsp26',     title:'NSP Scholarship 2025-26',  dept:'MoE',   cat:'scholar',    posts:0,     date:'2026-01-31', fee:0,    hot:9500,  url:'https://scholarships.gov.in/',      new:true},
  {id:'pm_yasasvi',title:'PM YASASVI 2026',          dept:'SEBC',  cat:'scholar',    posts:0,     date:'2026-02-15', fee:0,    hot:6500,  url:'https://yet.nta.ac.in/',            new:true},
  // TRANSPORT
  {id:'dl_rto',    title:'Driving Licence (RTO)',    dept:'RTO',   cat:'transport',  posts:0,     date:'Open',       fee:200,  hot:4500,  url:'https://sarathi.parivahan.gov.in/', new:false},
];

const CAT_META = {
  uidai:'🆔', central:'🏛️', state:'🚗', job:'💼', state_job:'🏢',
  exam:'📝', utility:'⚡', travel:'✈️', financial:'💳', welfare:'🤝',
  business:'🏬', food:'🍕', shopping:'🛒', digital:'📱'
};
const CAT_LABELS = {
  uidai:'Aadhaar/UIDAI', central:'Central Govt', state:'State/RTO', job:'Central Jobs',
  state_job:'State Jobs', exam:'Entrance Exams', utility:'Utilities', travel:'Travel & Cabs',
  financial:'Financial', welfare:'Welfare Schemes', business:'Business Licences',
  food:'Food Delivery', shopping:'Online Shopping', digital:'Digital India'
};

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   AUTH LISTENER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
auth.onAuthStateChanged(async user => {
  A.user = user;
  if (user) {
    // Check admin
    A.isAdmin = user.email === ADMIN_EMAIL;
    // Ensure user doc exists
    try {
      const ref = db.collection('users').doc(user.uid);
      const snap = await ref.get();
      if (!snap.exists) {
        await ref.set({
          email: user.email,
          role: A.isAdmin ? 'admin' : 'operator',
          createdAt: firebase.firestore.FieldValue.serverTimestamp(),
          shopName: '',
          isActive: true
        });
      }
    } catch(e) { console.warn('User doc:', e.message); }
  }
  hideLoader();
  render();
});

function hideLoader() {
  setTimeout(() => document.getElementById('loader').classList.add('off'), 600);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   TOAST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function toast(msg, type='ok') {
  const icons = {ok:'✅',err:'❌',warn:'⚠️'};
  const el = document.createElement('div');
  el.className = `toast ${type}`;
  el.innerHTML = `<span>${icons[type]||'ℹ️'}</span><span>${S.esc(msg)}</span>`;
  document.getElementById('toasts').appendChild(el);
  setTimeout(()=>el.style.opacity='0',3800);
  setTimeout(()=>el.remove(),4300);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   ROOT RENDER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function render() {
  document.getElementById('root').innerHTML = A.user ? renderShell() : renderAuthHTML();
  if (A.user) { wireShell(); gotoView('dashboard'); }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   AUTH HTML
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function renderAuthHTML() {
  return `<div class="auth-wrap">
    <div class="auth-glow auth-glow-1"></div>
    <div class="auth-glow auth-glow-2"></div>
    <div class="auth-card">
      <div class="auth-logo">
        <div class="auth-icon">🧠</div>
        <h1>CyberAce Quantum 5.0</h1>
        <p>Digital Services Automation Hub</p>
      </div>

      <div class="auth-note">
        <i class="fas fa-info-circle" style="flex-shrink:0;margin-top:1px"></i>
        <span>Admin: <strong>${S.esc(ADMIN_EMAIL)}</strong> — use your registered Firebase email &amp; password</span>
      </div>

      <div class="auth-tabs">
        <div class="auth-tab on" id="tab-login"  onclick="switchTab('login')">Login</div>
        <div class="auth-tab"   id="tab-signup" onclick="switchTab('signup')">Sign Up</div>
      </div>

      <div id="auth-err"  class="alert alert-err"><i class="fas fa-exclamation-circle"></i><span id="auth-err-msg"></span></div>
      <div id="auth-ok"   class="alert alert-ok"><i class="fas fa-check-circle"></i><span id="auth-ok-msg"></span></div>
      <div id="auth-warn" class="alert alert-warn"><i class="fas fa-exclamation-triangle"></i><span id="auth-warn-msg"></span></div>

      <!-- LOGIN -->
      <div id="panel-login">
        <div class="fld">
          <label class="fld-lbl">Email Address</label>
          <div class="fld-wrap">
            <i class="fas fa-envelope fld-ico"></i>
            <input class="fld-in" id="li-email" type="email" placeholder="bonagirispinoj@gmail.com" autocomplete="email" maxlength="100">
          </div>
        </div>
        <div class="fld">
          <label class="fld-lbl">Password</label>
          <div class="fld-wrap">
            <i class="fas fa-lock fld-ico"></i>
            <input class="fld-in" id="li-pass" type="password" placeholder="••••••••" autocomplete="current-password" maxlength="128"
              onkeydown="if(event.key==='Enter')doLogin()">
            <button class="fld-eye" onclick="toggleEye('li-pass',this)"><i class="fas fa-eye"></i></button>
          </div>
        </div>
        <div style="text-align:right;margin-bottom:12px">
          <a href="#" onclick="doResetPwd()" style="font-size:.72rem;color:var(--sky)">Forgot password?</a>
        </div>
        <button class="btn btn-fire" id="btn-login" onclick="doLogin()">
          <i class="fas fa-sign-in-alt"></i> Login Securely
        </button>
      </div>

      <!-- SIGNUP -->
      <div id="panel-signup" style="display:none">
        <div class="fld">
          <label class="fld-lbl">Email Address</label>
          <div class="fld-wrap">
            <i class="fas fa-envelope fld-ico"></i>
            <input class="fld-in" id="su-email" type="email" placeholder="your@email.com" autocomplete="email" maxlength="100">
          </div>
        </div>
        <div class="fld">
          <label class="fld-lbl">Password <span id="pwd-lbl" style="font-size:.65rem;color:var(--muted);margin-left:6px"></span></label>
          <div class="fld-wrap">
            <i class="fas fa-lock fld-ico"></i>
            <input class="fld-in" id="su-pass" type="password" placeholder="Min 8 chars + number + symbol" autocomplete="new-password" maxlength="128"
              oninput="updatePwdStr(this.value)">
            <button class="fld-eye" onclick="toggleEye('su-pass',this)"><i class="fas fa-eye"></i></button>
          </div>
          <div class="pwd-bar"><div class="pwd-fill" id="pwd-fill"></div></div>
        </div>
        <div class="fld">
          <label class="fld-lbl">Confirm Password</label>
          <div class="fld-wrap">
            <i class="fas fa-lock fld-ico"></i>
            <input class="fld-in" id="su-pass2" type="password" placeholder="Repeat password" autocomplete="new-password" maxlength="128">
          </div>
        </div>
        <div class="fld">
          <label class="fld-lbl">Shop / Cafe Name</label>
          <div class="fld-wrap">
            <i class="fas fa-store fld-ico"></i>
            <input class="fld-in" id="su-shop" type="text" placeholder="My Internet Cafe" maxlength="80">
          </div>
        </div>
        <button class="btn btn-fire" id="btn-signup" onclick="doSignup()">
          <i class="fas fa-user-plus"></i> Create Account
        </button>
      </div>

      <div style="margin-top:14px;text-align:center;font-size:.68rem;color:var(--muted)">
        <i class="fas fa-shield-alt" style="color:var(--jade)"></i>
        Firebase Auth • TLS Encrypted • Rate Limited
      </div>
    </div>
  </div>`;
}

function switchTab(t) {
  document.getElementById('tab-login').classList.toggle('on', t==='login');
  document.getElementById('tab-signup').classList.toggle('on', t==='signup');
  document.getElementById('panel-login').style.display  = t==='login'  ? 'block' : 'none';
  document.getElementById('panel-signup').style.display = t==='signup' ? 'block' : 'none';
  clearAuthAlerts();
}

function toggleEye(id, btn) {
  const el = document.getElementById(id);
  const show = el.type==='password';
  el.type = show ? 'text' : 'password';
  btn.querySelector('i').className = show ? 'fas fa-eye-slash' : 'fas fa-eye';
}

function updatePwdStr(p) {
  const s = S.pwdStr(p);
  const w = s*25+'%';
  const cols = ['','#EF5350','#FF7043','#FFB300','#00E676'];
  const lbls = ['','Weak','Fair','Good','Strong'];
  document.getElementById('pwd-fill').style.width = w;
  document.getElementById('pwd-fill').style.background = cols[s]||'#555';
  document.getElementById('pwd-lbl').textContent = lbls[s]||'';
  document.getElementById('pwd-lbl').style.color = cols[s]||'';
}

function clearAuthAlerts() {
  ['auth-err','auth-ok','auth-warn'].forEach(id => document.getElementById(id).classList.remove('on'));
}
function authErr(msg)  { document.getElementById('auth-err-msg').textContent=msg;  document.getElementById('auth-err').classList.add('on');  }
function authOk(msg)   { document.getElementById('auth-ok-msg').textContent=msg;   document.getElementById('auth-ok').classList.add('on');   }
function authWarn(msg) { document.getElementById('auth-warn-msg').textContent=msg; document.getElementById('auth-warn').classList.add('on');  }

function setBtnLoading(id, loading) {
  const btn = document.getElementById(id);
  if (!btn) return;
  btn.disabled = loading;
  const labels = {'btn-login':'<i class="fas fa-sign-in-alt"></i> Login Securely','btn-signup':'<i class="fas fa-user-plus"></i> Create Account'};
  btn.innerHTML = loading ? '<div class="spin"></div> Please wait…' : (labels[id]||'Submit');
}

const FB_ERR_MAP = {
  'auth/user-not-found':    'No account found with this email. Please sign up.',
  'auth/wrong-password':    'Incorrect password. Please try again.',
  'auth/invalid-credential':'Invalid email or password. Please check and retry.',
  'auth/too-many-requests': 'Too many failed attempts. Account temporarily locked for 15 minutes.',
  'auth/invalid-email':     'Invalid email address format.',
  'auth/user-disabled':     'This account has been disabled. Contact support.',
  'auth/email-already-in-use':'An account already exists with this email.',
  'auth/weak-password':     'Password is too weak. Use 8+ characters with numbers and symbols.',
  'auth/network-request-failed':'Network error. Check your internet connection.',
  'auth/operation-not-allowed':'Email/password login is disabled. Enable it in Firebase Console.',
};
function fbErr(code) { return FB_ERR_MAP[code] || `Authentication error (${code}). Please try again.`; }

async function doLogin() {
  clearAuthAlerts();
  const email = (document.getElementById('li-email').value||'').trim();
  const pass  = document.getElementById('li-pass').value||'';
  if (!email) return authErr('Email is required.');
  if (!S.isEmail(email)) return authErr('Please enter a valid email address.');
  if (pass.length < 6) return authErr('Password must be at least 6 characters.');
  if (!S.rateLimit('login',5,5*60*1000)) return authErr('Too many login attempts. Please wait 5 minutes.');
  setBtnLoading('btn-login', true);
  try {
    await auth.signInWithEmailAndPassword(email, pass);
  } catch(e) {
    authErr(fbErr(e.code));
    setBtnLoading('btn-login', false);
  }
}

async function doSignup() {
  clearAuthAlerts();
  const email = (document.getElementById('su-email').value||'').trim();
  const pass  = document.getElementById('su-pass').value||'';
  const pass2 = document.getElementById('su-pass2').value||'';
  const shop  = S.clean(document.getElementById('su-shop').value||'My Cafe');
  if (!email || !pass) return authErr('Please fill all required fields.');
  if (!S.isEmail(email)) return authErr('Invalid email address.');
  if (pass.length < 8) return authErr('Password must be at least 8 characters.');
  if (S.pwdStr(pass) < 2) return authErr('Password too weak. Add uppercase letters, numbers, or symbols.');
  if (pass !== pass2) return authErr('Passwords do not match.');
  if (!S.rateLimit('signup',3,10*60*1000)) return authErr('Too many signup attempts. Try again in 10 minutes.');
  setBtnLoading('btn-signup', true);
  try {
    const cred = await auth.createUserWithEmailAndPassword(email, pass);
    await db.collection('users').doc(cred.user.uid).set({
      email, shopName: shop, role:'operator',
      isActive:true,
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    authOk('Account created! Logging you in…');
  } catch(e) {
    authErr(fbErr(e.code));
    setBtnLoading('btn-signup', false);
  }
}

async function doResetPwd() {
  clearAuthAlerts();
  const email = (document.getElementById('li-email').value||'').trim();
  if (!email) return authErr('Enter your email above to reset password.');
  if (!S.isEmail(email)) return authErr('Enter a valid email address.');
  try {
    await auth.sendPasswordResetEmail(email);
    authOk(`Password reset email sent to ${email}. Check your inbox.`);
  } catch(e) {
    authErr(fbErr(e.code));
  }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SHELL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function renderShell() {
  const em  = A.user.email||'';
  const ini = em[0]?.toUpperCase()||'?';
  return `<div class="shell">
    <aside class="sidebar" id="sb">
      <div class="sb-brand">
        <div class="sb-orb">🧠</div>
        <div class="sb-name hide-slim">CyberAce Quantum<small>v5.0 Digital Hub</small></div>
      </div>
      <div class="sb-user hide-slim">
        <div class="sb-avatar">${S.esc(ini)}</div>
        <div class="sb-uinfo">
          <div class="n">${S.esc(em.split('@')[0])}</div>
          <div class="r">${A.isAdmin?'👑 Admin':'✓ Operator'}</div>
        </div>
      </div>
      <nav class="nav">
        <div class="nav-sec hide-slim">Main</div>
        <div class="nav-item active" id="nv-dashboard" onclick="gotoView('dashboard')"><i class="fas fa-chart-pie"></i><span class="hide-slim">Dashboard</span></div>
        <div class="nav-item" id="nv-services"    onclick="gotoView('services')"><i class="fas fa-th-large"></i><span class="hide-slim">All Services</span></div>
        <div class="nav-item" id="nv-jobs"         onclick="gotoView('jobs')"><i class="fas fa-briefcase"></i><span class="hide-slim">Job Alerts</span> <span class="nav-badge hide-slim">${JOBS.length}</span></div>
        ${A.isAdmin ? `<div class="nav-item" id="nv-applications" onclick="gotoView('applications')"><i class="fas fa-file-alt"></i><span class="hide-slim">Applications</span></div>` : ''}
        ${A.isAdmin ? `<div class="nav-sec hide-slim">Finance</div>
        <div class="nav-item" id="nv-earnings"    onclick="gotoView('earnings')"><i class="fas fa-rupee-sign"></i><span class="hide-slim">Earnings</span></div>` : ''}
        <div class="nav-sec hide-slim">Tools</div>
        <div class="nav-item" id="nv-ocr"         onclick="gotoView('ocr')"><i class="fas fa-file-image"></i><span class="hide-slim">OCR Scanner</span></div>
        ${A.isAdmin ? `<div class="nav-item" id="nv-bookmarklet" onclick="gotoView('bookmarklet')"><i class="fas fa-bookmark"></i><span class="hide-slim">Auto-Fill Setup</span> <span class="nav-badge hide-slim" style="background:var(--jade);color:#000">ADM</span></div>` : ''}
        ${A.isAdmin ? `
        <div class="nav-sec hide-slim">Admin</div>
        <div class="nav-item" id="nv-admin" onclick="gotoView('admin')"><i class="fas fa-crown"></i><span class="hide-slim">Admin Panel</span> <span class="nav-badge hide-slim" style="background:var(--gold);color:#000">ADM</span></div>` : ''}
        <div class="nav-sec hide-slim">Account</div>
        <div class="nav-item" onclick="doLogout()"><i class="fas fa-sign-out-alt"></i><span class="hide-slim">Logout</span></div>
      </nav>
      <div class="sb-footer">
        <div class="upi-pill">
          <strong>💳 UPI Collection</strong>
          <span>${UPI}</span>
        </div>
      </div>
    </aside>
    <div class="main-area">
      <div class="topbar">
        <button class="tb-toggle" onclick="toggleSB()"><i class="fas fa-bars"></i></button>
        <div class="tb-title" id="tb-title">Dashboard</div>
        <div class="tb-right">
          <div class="tb-secure">🔒 Secure</div>
          <button class="tb-logout" onclick="doLogout()"><i class="fas fa-sign-out-alt"></i> Logout</button>
        </div>
      </div>
      <div class="page" id="page"></div>
    </div>
  </div>`;
}

function wireShell() {
  document.addEventListener('click', e => {
    const sb = document.getElementById('sb');
    if (!sb) return;
    if (window.innerWidth<768 && sb.classList.contains('mob-open')) {
      if (!sb.contains(e.target) && !e.target.closest('.tb-toggle')) sb.classList.remove('mob-open');
    }
  });
}

function toggleSB() {
  const sb = document.getElementById('sb');
  if (!sb) return;
  if (window.innerWidth<768) { sb.classList.toggle('mob-open'); return; }
  sb.classList.toggle('slim');
  const ma = document.querySelector('.main-area');
  if (ma) ma.style.marginLeft = sb.classList.contains('slim') ? '56px' : '248px';
}

async function doLogout() {
  await auth.signOut();
  toast('Logged out successfully');
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   VIEW ROUTER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const VIEW_TITLES = {
  dashboard:'Dashboard',services:'All Services',jobs:'Latest Job Alerts 2026',
  applications:'Applications',earnings:'Earnings',ocr:'OCR Scanner',
  bookmarklet:'🔖 Auto-Fill Setup',
  admin:'Admin Panel'
};
function gotoView(name) {
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  const nv = document.getElementById('nv-'+name);
  if (nv) nv.classList.add('active');
  const t = document.getElementById('tb-title');
  if (t) t.textContent = VIEW_TITLES[name]||name;
  const pg = document.getElementById('page');
  if (!pg) return;
  pg.innerHTML='';
  const map = {dashboard:vDashboard,services:vServices,jobs:vJobs,
    applications:vApplications,earnings:vEarnings,ocr:vOCR,
    bookmarklet:vBookmarklet,admin:vAdmin};
  if (map[name]) map[name](pg);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   DASHBOARD VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
async function vDashboard(pg) {
  pg.innerHTML = `<div class="view">
    <div class="sec-strip">
      <span><i class="fas fa-shield-alt"></i> Firebase Auth</span>
      <span><i class="fas fa-database"></i> Firestore Encrypted</span>
      <span><i class="fas fa-upload"></i> Storage Upload</span>
      <span><i class="fas fa-lock"></i> XSS Sanitised</span>
      <span><i class="fas fa-ban"></i> Rate Limited</span>
    </div>
    ${A.isAdmin ? `<div class="stats" id="dash-stats">
      <div class="stat-c"><div class="stat-lbl">Total Applications</div><div class="stat-v" id="st-total">…</div><div class="stat-s">All time</div><div class="stat-ico">📋</div></div>
      <div class="stat-c"><div class="stat-lbl">Today</div><div class="stat-v" id="st-today">…</div><div class="stat-s">Applications today</div><div class="stat-ico">📅</div></div>
      <div class="stat-c"><div class="stat-lbl">Total Earnings</div><div class="stat-v" id="st-earn">…</div><div class="stat-s">Gross commission</div><div class="stat-ico">💰</div></div>
      <div class="stat-c"><div class="stat-lbl">This Month</div><div class="stat-v" id="st-month">…</div><div class="stat-s">Net earnings</div><div class="stat-ico">📈</div></div>
    </div>` : ''}

    <!-- UPI QR -->
    <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:18px;
      display:flex;align-items:center;gap:22px;margin-bottom:20px;flex-wrap:wrap">
      <div style="flex:1;min-width:200px">
        <div style="font-size:1.05rem;font-weight:700;margin-bottom:6px">💳 Quick Fee Collection</div>
        <div style="font-size:.8rem;color:var(--muted2)">Show QR to customer → They pay ₹100 service fee via UPI</div>
        <div style="margin-top:10px;font-family:var(--ff-mono);font-size:.75rem;color:var(--jade)">${UPI}</div>
      </div>
      <div id="dash-qr" style="background:#fff;padding:5px;border-radius:10px;width:124px;height:124px;flex-shrink:0"></div>
    </div>

    <!-- DASHBOARD SEARCH -->
    <div style="margin-bottom:20px;position:relative">
      <div style="display:flex;align-items:center;background:var(--card);border:1.5px solid var(--border2);border-radius:16px;padding:5px 16px;gap:12px;box-shadow:0 6px 24px rgba(0,0,0,.25)" id="dash-search-wrap">
        <i class="fas fa-search" style="color:var(--muted);font-size:1.1rem;flex-shrink:0"></i>
        <input id="dash-search" placeholder="🔍 Search any service — Aadhaar, PAN, Passport, SSC, IRCTC, GST..."
          style="flex:1;background:none;border:none;outline:none;color:var(--text);font-size:.94rem;padding:11px 0;font-family:var(--ff-head)"
          oninput="dashSearch(this.value)" autocomplete="off" onkeydown="if(event.key==='Enter'){gotoView('services');setTimeout(()=>{ const el=document.getElementById('svc-search'); if(el){el.value=document.getElementById('dash-search')?.value||''; searchServices(el.value);} },100)}"/>
        <div style="flex-shrink:0;display:flex;gap:6px">
          <button onclick="gotoView('services')" style="background:var(--fire);border:none;color:#fff;padding:7px 14px;border-radius:9px;cursor:pointer;font-size:.76rem;font-weight:700">All Services</button>
        </div>
      </div>
      <div id="dash-search-results" style="display:none;position:absolute;top:100%;left:0;right:0;z-index:200;background:var(--card);border:1px solid var(--border2);border-radius:12px;margin-top:5px;max-height:340px;overflow-y:auto;box-shadow:0 16px 48px rgba(0,0,0,.5)"></div>
    </div>

    <!-- Category quick tiles -->
    <div class="sec-title" style="margin-bottom:13px">🚀 Quick Access</div>
    <div class="cat-tiles" id="cat-tiles"></div>

    <div style="display:grid;grid-template-columns:1fr 300px;gap:16px;flex-wrap:wrap" id="dash-bot">
      <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:17px">
        <div class="sec-title" style="margin-bottom:12px">📋 Recent Applications</div>
        <div id="dash-recent"><div style="text-align:center;padding:30px;color:var(--muted)"><div class="spin" style="margin:auto"></div></div></div>
      </div>
      <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:17px">
        <div class="sec-title" style="margin-bottom:12px">🔥 Trending</div>
        <div id="dash-trending"></div>
      </div>
    </div>
  </div>`;

  // QR
  setTimeout(() => {
    try { new QRCode(document.getElementById('dash-qr'),{text:`upi://pay?pa=${UPI}&pn=CyberAce&am=100&cu=INR`,width:114,height:114}); } catch(e){}
  }, 100);

  // Cat tiles
  const uniqueCats = [...new Set(SVCS.map(s=>s.cat))];
  document.getElementById('cat-tiles').innerHTML = uniqueCats.map(c=>`
    <div class="cat-tile" onclick="gotoViewFiltered('services','${S.esc(c)}')">
      <div class="ico">${CAT_META[c]||'🔧'}</div>
      <div class="nm">${S.esc(CAT_LABELS[c]||c)}</div>
      <div class="cnt">${SVCS.filter(s=>s.cat===c).length} services</div>
    </div>`).join('');

  // Stats (admin only)
  if (A.isAdmin) try {
    const snap = await db.collection('applications').where('uid','==',A.user.uid).get();
    const apps = snap.docs.map(d=>d.data());
    const today = new Date().toISOString().slice(0,10);
    const todayN = apps.filter(a=>(a.createdAt?.toDate?.()?.toISOString?.()||'').startsWith(today)).length;
    const earn = apps.reduce((s,a)=>s+(a.commission||0),0);
    const mo = new Date().toISOString().slice(0,7);
    const moEarn = apps.filter(a=>(a.createdAt?.toDate?.()?.toISOString?.()||'').startsWith(mo)).reduce((s,a)=>s+(a.commission||0),0);
    if(document.getElementById('st-total')) document.getElementById('st-total').textContent = apps.length;
    if(document.getElementById('st-today')) document.getElementById('st-today').textContent = todayN;
    if(document.getElementById('st-earn'))  document.getElementById('st-earn').textContent  = fmtINR(earn);
    if(document.getElementById('st-month')) document.getElementById('st-month').textContent = fmtINR(moEarn);

    const recent = apps.sort((a,b)=>(b.createdAt?.seconds||0)-(a.createdAt?.seconds||0)).slice(0,5);
    const dr = document.getElementById('dash-recent');
    if (dr) dr.innerHTML = recent.length ? recent.map(a=>`
      <div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border);font-size:.78rem">
        <div><div style="font-weight:600">${S.esc(a.serviceName||'—')}</div><div style="color:var(--muted);font-size:.68rem">${S.esc(a.customerName||'—')}</div></div>
        <div style="text-align:right"><span class="pill p-${S.esc(a.status||'pending')}">${S.esc(a.status||'pending')}</span><div style="color:var(--jade);font-size:.68rem;margin-top:3px">+₹${a.commission||0}</div></div>
      </div>`).join('')
      : `<div style="text-align:center;padding:28px;color:var(--muted)">No applications yet. Start from Services!</div>`;
  } catch(e) {
    ['st-total','st-today','st-earn','st-month'].forEach(id=>{ const el=document.getElementById(id); if(el) el.textContent='0'; });
  }

  // Trending jobs
  const trending = [...JOBS].sort((a,b)=>b.hot-a.hot).slice(0,5);
  document.getElementById('dash-trending').innerHTML = trending.map(j=>`
    <div onclick="openJobApply(${JSON.stringify(j)})" style="display:flex;justify-content:space-between;padding:8px 0;border-bottom:1px solid var(--border);cursor:pointer;font-size:.78rem">
      <div><div style="font-weight:600">${S.esc(j.title)}</div><div style="color:var(--muted);font-size:.67rem">${S.esc(j.dept)}</div></div>
      <div style="text-align:right;flex-shrink:0"><div class="fire-tag">🔥${(j.hot/1000).toFixed(1)}K</div><div style="font-size:.65rem;color:var(--muted)">${j.date}</div></div>
    </div>`).join('');
}

let _filteredCat = '';
function gotoViewFiltered(view, cat) { _filteredCat = cat; gotoView(view); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SERVICES VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function vServices(pg) {
  pg.innerHTML = `<div class="view">
    <!-- MEGA SEARCH BAR -->
    <div style="margin-bottom:18px;position:relative">
      <div style="display:flex;align-items:center;background:var(--card);border:1.5px solid var(--border2);border-radius:14px;padding:4px 14px;gap:10px;transition:border-color .2s;box-shadow:0 4px 20px rgba(0,0,0,.2)" id="search-wrap">
        <i class="fas fa-search" style="color:var(--muted);font-size:1rem;flex-shrink:0"></i>
        <input id="svc-search" placeholder="Search any service... e.g. Aadhaar download, PAN, Passport, IRCTC, SSC CGL..."
          style="flex:1;background:none;border:none;outline:none;color:var(--text);font-size:.92rem;padding:10px 0;font-family:var(--ff-head)"
          oninput="searchServices(this.value)" autocomplete="off"/>
        <button id="search-clear" onclick="clearSearch()" style="display:none;background:none;border:none;color:var(--muted);cursor:pointer;font-size:.9rem;padding:4px">✕</button>
      </div>
      <div id="search-results" style="display:none;position:absolute;top:100%;left:0;right:0;z-index:200;background:var(--card);border:1px solid var(--border2);border-radius:12px;margin-top:5px;max-height:380px;overflow-y:auto;box-shadow:0 16px 48px rgba(0,0,0,.5)"></div>
    </div>
    <div class="sec-hd">
      <div class="sec-title">🏛️ All Services (${SVCS.length})</div>
      <div class="ftabs" id="svc-tabs" style="flex-wrap:wrap">
        <button class="ft on" onclick="filterSvc('',this)">All</button>
        ${[...new Set(SVCS.map(s=>s.cat))].map(c=>`<button class="ft" onclick="filterSvc('${S.esc(c)}',this)">${S.esc(CAT_META[c]||'')} ${S.esc(CAT_LABELS[c]||c)}</button>`).join('')}
      </div>
    </div>
    <div class="svc-grid" id="svc-grid"></div>
  </div>`;
  if (_filteredCat) {
    const btn = [...document.querySelectorAll('#svc-tabs .ft')].find(b=>b.textContent.includes(CAT_LABELS[_filteredCat]||_filteredCat));
    if (btn) { document.querySelectorAll('#svc-tabs .ft').forEach(b=>b.classList.remove('on')); btn.classList.add('on'); }
    renderSvcGrid(SVCS.filter(s=>s.cat===_filteredCat));
    _filteredCat='';
  } else renderSvcGrid(SVCS);
}

function searchServices(q) {
  const wrap = document.getElementById('search-wrap');
  const dropdown = document.getElementById('search-results');
  const clearBtn = document.getElementById('search-clear');
  if (!q || q.trim().length < 1) { dropdown.style.display='none'; clearBtn.style.display='none'; wrap.style.borderColor=''; return; }
  clearBtn.style.display='block';
  wrap.style.borderColor='var(--fire)';
  const term = q.toLowerCase();
  const results = SVCS.filter(s =>
    s.name.toLowerCase().includes(term) ||
    (s.keywords||'').toLowerCase().includes(term) ||
    (s.cat||'').toLowerCase().includes(term) ||
    (CAT_LABELS[s.cat]||'').toLowerCase().includes(term)
  ).slice(0,12);
  if (!results.length) {
    dropdown.style.display='block';
    dropdown.innerHTML=`<div style="padding:20px;text-align:center;color:var(--muted)">
      <div style="font-size:1.4rem;margin-bottom:6px">🔍</div>
      <div style="font-size:.82rem">No services found for "<strong>${S.esc(q)}</strong>"</div>
      <a href="https://www.google.com/search?q=${encodeURIComponent(q+' official portal India apply online 2026')}" target="_blank" rel="noopener"
        style="display:inline-flex;align-items:center;gap:6px;margin-top:10px;padding:7px 14px;background:linear-gradient(135deg,#4285F4,#1565C0);color:#fff;border-radius:8px;font-size:.78rem;font-weight:700;text-decoration:none">
        <i class="fab fa-google"></i> Search on Google
      </a>
    </div>`;
    return;
  }
  dropdown.style.display='block';
  dropdown.innerHTML = results.map(s=>`
    <div onclick="selectSearchResult(${JSON.stringify(s)})"
      style="display:flex;align-items:center;gap:12px;padding:11px 14px;cursor:pointer;border-bottom:1px solid var(--border);transition:background .15s"
      onmouseover="this.style.background='rgba(255,87,34,.07)'" onmouseout="this.style.background=''">
      <span style="font-size:1.1rem;flex-shrink:0">${CAT_META[s.cat]||'🔧'}</span>
      <div style="flex:1;min-width:0">
        <div style="font-size:.86rem;font-weight:700;color:var(--text)">${S.esc(s.name)}</div>
        <div style="font-size:.7rem;color:var(--muted)">${S.esc(CAT_LABELS[s.cat]||s.cat)} · ₹${s.price}</div>
      </div>
      ${s.url ? `<a href="${S.esc(s.url)}" target="_blank" rel="noopener"
        onclick="event.stopPropagation();saveJobData('${S.esc(s.name)}','${S.esc(s.url)}')"
        style="flex-shrink:0;background:var(--jade);color:#fff;border:none;border-radius:7px;padding:5px 10px;font-size:.72rem;font-weight:700;text-decoration:none;display:flex;align-items:center;gap:4px">
        <i class="fas fa-rocket"></i> Open
      </a>` : `<button onclick="event.stopPropagation();openApplyModal(${JSON.stringify(s)})"
        style="flex-shrink:0;background:var(--fire);color:#fff;border:none;border-radius:7px;padding:5px 10px;font-size:.72rem;font-weight:700;cursor:pointer">
        Apply
      </button>`}
    </div>`).join('')+
    `<div style="padding:9px 14px;text-align:center;font-size:.72rem;color:var(--muted)">
      ${results.length} result${results.length!==1?'s':''} • Type more to refine
    </div>`;
}

function clearSearch() {
  const inp = document.getElementById('svc-search');
  const dropdown = document.getElementById('search-results');
  const clearBtn = document.getElementById('search-clear');
  const wrap = document.getElementById('search-wrap');
  if (inp) inp.value = '';
  if (dropdown) dropdown.style.display = 'none';
  if (clearBtn) clearBtn.style.display = 'none';
  if (wrap) wrap.style.borderColor = '';
}

function dashSearch(q) {
  const dropdown = document.getElementById('dash-search-results');
  if (!q || q.trim().length < 1) { dropdown.style.display='none'; return; }
  const term = q.toLowerCase();
  const results = SVCS.filter(s =>
    s.name.toLowerCase().includes(term) ||
    (s.keywords||'').toLowerCase().includes(term) ||
    (CAT_LABELS[s.cat]||'').toLowerCase().includes(term)
  ).slice(0,10);
  if (!results.length) {
    dropdown.style.display='block';
    dropdown.innerHTML=`<div style="padding:16px;text-align:center;color:var(--muted);font-size:.82rem">No results. <a href="https://www.google.com/search?q=${encodeURIComponent(q+' official portal India 2026')}" target="_blank" rel="noopener" style="color:var(--sky)">Search on Google →</a></div>`;
    return;
  }
  dropdown.style.display='block';
  dropdown.innerHTML = results.map(s=>`
    <div onclick="selectDashResult(${JSON.stringify(s)})"
      style="display:flex;align-items:center;gap:12px;padding:11px 14px;cursor:pointer;border-bottom:1px solid var(--border);transition:background .15s"
      onmouseover="this.style.background='rgba(255,87,34,.07)'" onmouseout="this.style.background=''">
      <span style="font-size:1rem;flex-shrink:0">${CAT_META[s.cat]||'🔧'}</span>
      <div style="flex:1;min-width:0">
        <div style="font-size:.86rem;font-weight:700;color:var(--text)">${S.esc(s.name)}</div>
        <div style="font-size:.7rem;color:var(--muted)">${S.esc(CAT_LABELS[s.cat]||s.cat)}</div>
      </div>
      ${s.url ? `<a href="${S.esc(s.url)}" target="_blank" rel="noopener"
        onclick="event.stopPropagation();saveJobData('${S.esc(s.name)}','${S.esc(s.url)}')"
        style="flex-shrink:0;background:var(--jade);color:#fff;border:none;border-radius:7px;padding:5px 10px;font-size:.72rem;font-weight:700;text-decoration:none;display:flex;align-items:center;gap:4px">
        <i class="fas fa-external-link-alt"></i> Open Portal
      </a>` : ''}
    </div>`).join('');
}

function selectDashResult(svc) {
  document.getElementById('dash-search-results').style.display='none';
  if (svc.url) {
    try { localStorage.setItem('ca_autofill',JSON.stringify({data:A.formData||{},url:svc.url,service:svc.name,ts:Date.now()})); } catch(e){}
    window.open(svc.url,'_blank');
    showDataPanel(A.formData||{},{name:svc.name,url:svc.url});
  } else { openApplyModal(svc); }
}

function selectSearchResult(svc) {
  const dropdown = document.getElementById('search-results');
  if (dropdown) dropdown.style.display='none';
  if (svc.url) {
    try { localStorage.setItem('ca_autofill',JSON.stringify({data:A.formData||{},url:svc.url,service:svc.name,ts:Date.now()})); } catch(e){}
    window.open(svc.url,'_blank');
    showDataPanel(A.formData||{},{name:svc.name,url:svc.url});
  } else {
    openApplyModal(svc);
  }
}

// Close search dropdown on outside click
document.addEventListener('click', e=>{
  const d=document.getElementById('search-results');
  const w=document.getElementById('search-wrap');
  if (d && w && !w.contains(e.target)) { d.style.display='none'; }
});

function filterSvc(cat, el) {
  document.querySelectorAll('#svc-tabs .ft').forEach(b=>b.classList.remove('on'));
  el.classList.add('on');
  renderSvcGrid(cat ? SVCS.filter(s=>s.cat===cat) : SVCS);
}

function renderSvcGrid(list) {
  const g = document.getElementById('svc-grid');
  if (!g) return;
  if (!list.length) { g.innerHTML=`<div style="text-align:center;padding:60px;color:var(--muted);grid-column:1/-1"><div style="font-size:2.5rem;opacity:.3">🔍</div><div>No services found</div></div>`; return; }
  g.innerHTML = list.map(s=>`
    <div class="svc-c cat-${S.esc(s.cat)}" style="position:relative">
      ${s.auto==='full'?'<span class="auto-tag">⚡ Auto</span>':''}
      <div class="svc-cat" style="cursor:pointer" onclick="openApplyModal(${JSON.stringify(s)})"><span class="cat-dot"></span>${S.esc(CAT_META[s.cat]||'')} ${S.esc(CAT_LABELS[s.cat]||s.cat)}</div>
      <div class="svc-name" style="cursor:pointer" onclick="openApplyModal(${JSON.stringify(s)})">${S.esc(s.name)}</div>
      <div class="svc-foot" style="margin-top:auto;padding-top:10px">
        <span class="svc-price">₹${s.price}</span>
        ${s.url ?
          `<a href="${S.esc(s.url)}" target="_blank" rel="noopener"
            onclick="event.stopPropagation();saveJobData('${S.esc(s.name)}','${S.esc(s.url)}')"
            style="display:inline-flex;align-items:center;gap:5px;background:linear-gradient(135deg,var(--jade),#00796B);
            color:#fff;padding:5px 11px;border-radius:8px;font-size:.7rem;font-weight:700;text-decoration:none;box-shadow:0 3px 10px rgba(0,191,165,.3)">
            <i class="fas fa-external-link-alt"></i> Open Portal
          </a>`
          : `<span class="svc-comm" style="cursor:pointer" onclick="openApplyModal(${JSON.stringify(s)})">Apply →</span>`
        }
      </div>
    </div>`).join('');
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   JOBS VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function vJobs(pg) {
  pg.innerHTML = `<div class="view">
    <!-- JOB SEARCH -->
    <div style="margin-bottom:16px;position:relative">
      <div style="display:flex;align-items:center;background:var(--card);border:1.5px solid var(--border2);border-radius:14px;padding:4px 14px;gap:10px">
        <i class="fas fa-search" style="color:var(--muted);font-size:1rem"></i>
        <input id="job-search" placeholder="Search jobs... e.g. TGPSC, SSC CGL 2026, IBPS PO, NEET..."
          style="flex:1;background:none;border:none;outline:none;color:var(--text);font-size:.9rem;padding:9px 0;font-family:var(--ff-head)"
          oninput="searchJobs(this.value)" autocomplete="off"/>
      </div>
    </div>
    <div class="sec-hd">
      <div class="sec-title">💼 Latest Job Alerts 2026 <span style="background:var(--jade);color:#fff;font-size:.6rem;padding:2px 7px;border-radius:99px;font-weight:800;vertical-align:middle">LIVE</span></div>
      <div class="ftabs" id="job-tabs" style="flex-wrap:wrap">
        <button class="ft on" onclick="filterJobs('',this)">All</button>
        <button class="ft" onclick="filterJobs('central',this)">Central</button>
        <button class="ft" onclick="filterJobs('banking',this)">Banking</button>
        <button class="ft" onclick="filterJobs('state',this)">State PSC</button>
        <button class="ft" onclick="filterJobs('defence',this)">Defence</button>
        <button class="ft" onclick="filterJobs('teaching',this)">Teaching</button>
        <button class="ft" onclick="filterJobs('medical',this)">Medical</button>
        <button class="ft" onclick="filterJobs('engg',this)">Engineering</button>
        <button class="ft" onclick="filterJobs('scholar',this)">Scholarship</button>
        <button class="ft" onclick="filterJobs('transport',this)">Transport</button>
      </div>
    </div>
    <div class="job-list" id="job-list"></div>
  </div>`;
  renderJobList(JOBS);
}

function searchJobs(q) {
  if (!q || q.trim().length < 1) { renderJobList(JOBS); return; }
  const term = q.toLowerCase();
  const results = JOBS.filter(j =>
    j.title.toLowerCase().includes(term) ||
    j.dept.toLowerCase().includes(term) ||
    j.cat.toLowerCase().includes(term)
  );
  renderJobList(results);
}

/* vJobsOrig placeholder */
}

function filterJobs(cat, el) {
  document.querySelectorAll('#job-tabs .ft').forEach(b=>b.classList.remove('on'));
  el.classList.add('on');
  renderJobList(cat ? JOBS.filter(j=>j.cat===cat) : JOBS);
}

function renderJobList(list) {
  const el = document.getElementById('job-list');
  if (!el) return;
  if (!list.length) { el.innerHTML=`<div style="text-align:center;padding:60px;color:var(--muted)">No jobs found</div>`; return; }
  el.innerHTML = [...list].sort((a,b)=>b.hot-a.hot).map(j=>`
    <div class="job-c">
      <div class="job-dept-ico">${S.esc(j.dept[0])}</div>
      <div class="job-info">
        <div class="job-title">${S.esc(j.title)} ${j.new?'<span style="background:var(--jade);color:#fff;font-size:.58rem;padding:2px 6px;border-radius:99px;font-weight:800;vertical-align:middle;margin-left:4px">🆕 2026</span>':''}</div>
        <div class="job-meta">
          <span>🏛️ ${S.esc(j.dept)}</span>
          ${j.posts?`<span>👥 ${j.posts.toLocaleString('en-IN')} posts</span>`:''}
          ${j.hot>5000?`<span class="fire-tag">🔥 Trending</span>`:''}
        </div>
        ${j.url ? `<a href="${S.esc(j.url)}" target="_blank" rel="noopener" class="job-portal-link"
          onclick="saveJobData('${S.esc(j.title)}','${S.esc(j.url)}')">
          <i class="fas fa-external-link-alt"></i> Direct Portal
        </a>` : ''}
      </div>
      <div class="job-r">
        <div class="job-fee">₹${j.fee} exam fee</div>
        <div class="job-date">📅 ${S.esc(j.date)}</div>
        ${j.url ? `<a href="${S.esc(j.url)}" target="_blank" rel="noopener"
          onclick="saveJobData('${S.esc(j.title)}','${S.esc(j.url)}')"
          style="display:inline-flex;align-items:center;gap:5px;background:linear-gradient(135deg,var(--jade),#00695C);color:#fff;padding:6px 12px;border-radius:8px;font-size:.72rem;font-weight:700;text-decoration:none;margin-top:5px">
          <i class="fas fa-external-link-alt"></i> Direct Apply
        </a>` : `<div class="hot-btn" onclick="openJobApply(${JSON.stringify(j)})">Apply</div>`}
      </div>
    </div>`).join('');
}

function saveJobData(title, url) {
  try {
    localStorage.setItem('ca_autofill', JSON.stringify({
      data: A.formData||{}, url, service:title, ts:Date.now()
    }));
  } catch(e){}
  showDataPanel(A.formData||{}, {name:title, url});
}

function openJobApply(j) {
  const svc = {id:j.id, name:j.title, cat:'job', price:100+(j.fee||0), comm:150, auto:j.url?'full':'semi', url:j.url};
  openApplyModal(svc);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   APPLICATIONS VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
async function vApplications(pg) {
  pg.innerHTML = `<div class="view">
    <div class="sec-hd"><div class="sec-title">📋 My Applications</div>
      <button class="btn btn-outline btn-sm" onclick="gotoView('applications')"><i class="fas fa-sync"></i> Refresh</button>
    </div>
    <div class="tbl-wrap">
      <table>
        <thead><tr><th>App No.</th><th>Service</th><th>Customer</th><th>Status</th><th>Payment</th><th>Commission</th><th>Date</th></tr></thead>
        <tbody id="apps-body"><tr><td colspan="7" style="text-align:center;padding:40px"><div class="spin" style="margin:auto"></div></td></tr></tbody>
      </table>
    </div>
  </div>`;

  try {
    const snap = await db.collection('applications').where('uid','==',A.user.uid).orderBy('createdAt','desc').limit(100).get();
    const apps = snap.docs.map(d=>({id:d.id,...d.data()}));
    const tb = document.getElementById('apps-body');
    tb.innerHTML = apps.length ? apps.map(a=>`<tr>
      <td style="font-family:var(--ff-mono);font-size:.72rem">${S.esc(a.appNumber||a.id?.slice(-8)||'—')}</td>
      <td>${S.esc(a.serviceName||'—')}</td>
      <td>${S.esc(a.customerName||'—')}</td>
      <td><span class="pill p-${S.esc(a.status||'pending')}">${S.esc(a.status||'pending')}</span></td>
      <td><span class="pill p-${a.paymentVerified?'verified':'pending'}">${a.paymentVerified?'Verified':'Pending'}</span></td>
      <td style="color:var(--jade)">₹${a.commission||0}</td>
      <td style="color:var(--muted)">${a.createdAt?.toDate?.()?.toLocaleDateString('en-IN')||'—'}</td>
    </tr>`).join('')
    : `<tr><td colspan="7" style="text-align:center;padding:40px;color:var(--muted)">No applications yet. Go to Services to get started!</td></tr>`;
  } catch(e) {
    document.getElementById('apps-body').innerHTML = `<tr><td colspan="7" style="text-align:center;padding:20px;color:var(--muted)">Could not load: ${S.esc(e.message)}</td></tr>`;
  }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   EARNINGS VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function vEarnings(pg) {
  pg.innerHTML = `<div class="view">
    <div class="sec-hd"><div class="sec-title">💰 Profit Calculator</div></div>
    <div class="calc-grid">
      <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:17px">
        <div style="font-size:.88rem;font-weight:700;margin-bottom:14px">⚙️ Inputs</div>
        <div class="fld"><label class="fld-lbl">Apps / day</label><input class="fld-in" style="padding-left:12px" type="number" id="c-apps" value="20" min="1" max="500" oninput="recalc()"></div>
        <div class="fld"><label class="fld-lbl">Commission / app (₹)</label><input class="fld-in" style="padding-left:12px" type="number" id="c-comm" value="100" min="10" oninput="recalc()"></div>
        <div class="fld"><label class="fld-lbl">Working days / month</label><input class="fld-in" style="padding-left:12px" type="number" id="c-days" value="25" min="1" max="31" oninput="recalc()"></div>
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:11px">
        <div class="calc-out"><div class="cl">Revenue</div><div class="cv" id="c-rev" style="color:var(--fire)">—</div></div>
        <div class="calc-out"><div class="cl">Platform (15%)</div><div class="cv" id="c-fee" style="color:var(--danger)">—</div></div>
        <div class="calc-out"><div class="cl">Subscription</div><div class="cv" style="color:var(--muted2)">₹2,000</div></div>
        <div class="calc-out"><div class="cl">Net Profit</div><div class="cv" id="c-net" style="color:var(--jade)">—</div></div>
      </div>
    </div>
    <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);overflow:hidden">
      <div style="padding:14px 17px;border-bottom:1px solid var(--border);font-weight:700">📊 Earnings Scale</div>
      <table><thead><tr><th>Apps/Day</th><th>Monthly Apps</th><th>Gross</th><th>Platform (15%)</th><th>Net Profit</th></tr></thead>
      <tbody>${[10,20,30,50,100].map(d=>{
        const a=d*25,g=a*100,f=Math.round(g*.15),n=g-f-2000;
        return `<tr><td>${d}</td><td>${a}</td><td style="color:var(--fire)">${fmtINR(g)}</td><td style="color:var(--danger)">${fmtINR(f)}</td><td style="color:var(--jade);font-weight:700">${fmtINR(Math.max(0,n))}</td></tr>`;
      }).join('')}</tbody></table>
    </div>
  </div>`;
  recalc();
}

function recalc() {
  const a=parseInt(document.getElementById('c-apps')?.value)||0;
  const c=parseInt(document.getElementById('c-comm')?.value)||0;
  const d=parseInt(document.getElementById('c-days')?.value)||25;
  const r=a*d*c, f=Math.round(r*.15), n=r-f-2000;
  if(document.getElementById('c-rev')){
    document.getElementById('c-rev').textContent=fmtINR(r);
    document.getElementById('c-fee').textContent=fmtINR(f);
    document.getElementById('c-net').textContent=fmtINR(Math.max(0,n));
  }
}
function fmtINR(n){ return n>=100000?`₹${(n/100000).toFixed(2)}L`:`₹${n.toLocaleString('en-IN')}`; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   OCR TOOL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function vOCR(pg) {
  pg.innerHTML = `<div class="view">
    <div class="sec-hd"><div class="sec-title">📄 OCR Document Scanner</div></div>
    <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:20px;max-width:560px">
      <p style="font-size:.82rem;color:var(--muted2);margin-bottom:16px">Upload Aadhaar, PAN, or any document image. AI will extract Name, DOB, Aadhaar, PAN automatically.</p>
      <div class="up-zone" id="ocr-zone">
        <div class="u-ico"><i class="fas fa-file-image"></i></div>
        <div class="u-txt">Click or drag to upload document image</div>
        <div class="u-sub">JPG, PNG, PDF • Max 10MB</div>
        <input type="file" id="ocr-file" accept="image/*,.pdf" onchange="runOCR(this)">
      </div>
      <div id="ocr-spin" style="display:none;text-align:center;padding:18px">
        <div class="spin" style="margin:auto;width:26px;height:26px;border-width:3px"></div>
        <div style="color:var(--muted);font-size:.78rem;margin-top:9px">Running AI OCR…</div>
      </div>
      <div class="ocr-result" id="ocr-result">
        <div style="font-size:.75rem;color:var(--muted2);font-weight:600;margin-bottom:9px">Extracted Fields:</div>
        <div id="ocr-fields"></div>
        <div style="margin-top:12px">
          <div style="font-size:.68rem;color:var(--muted);margin-bottom:5px">Raw OCR Text:</div>
          <textarea id="ocr-raw" style="width:100%;background:var(--ink);border:1px solid var(--border);
            border-radius:8px;padding:9px;color:var(--muted2);font-size:.72rem;height:90px;
            font-family:var(--ff-mono);resize:vertical" readonly></textarea>
        </div>
        <button class="btn btn-jade btn-sm" style="width:auto;margin-top:11px" onclick="useOCRData()">
          <i class="fas fa-fill-drip"></i> Use This Data in Next Application
        </button>
      </div>
    </div>
  </div>`;
}

async function runOCR(input) {
  const file = input.files[0];
  if (!file) return;
  if (file.size > 10*1024*1024) { toast('File too large (max 10MB)','err'); return; }
  document.getElementById('ocr-spin').style.display='block';
  document.getElementById('ocr-result').classList.remove('on');
  try {
    if (typeof Tesseract !== 'undefined') {
      const { data: { text } } = await Tesseract.recognize(file,'eng');
      displayOCRResult(text);
    } else {
      const reader = new FileReader();
      reader.onload = e => displayOCRResult(e.target.result||'OCR lib not loaded');
      reader.readAsText(file);
    }
  } catch(e) { toast('OCR failed: '+e.message,'err'); }
  finally { document.getElementById('ocr-spin').style.display='none'; }
}

function displayOCRResult(text) {
  const f = extractOCRFields(text);
  A.formData = {...A.formData, ...f};
  document.getElementById('ocr-raw').value = text;
  const el = document.getElementById('ocr-fields');
  el.innerHTML = Object.entries(f).filter(([,v])=>v).map(([k,v])=>`
    <div class="ocr-row"><span class="l">${S.esc(k)}</span><span class="v">${S.esc(v)}</span></div>`
  ).join('') || '<div style="color:var(--muted);font-size:.78rem">Could not extract structured fields — see raw text.</div>';
  document.getElementById('ocr-result').classList.add('on');
  toast('OCR complete — data extracted!');
}

function extractOCRFields(txt) {
  const find = pats => { for(const p of pats){const m=txt.match(p);if(m)return (m[1]||'').trim();} return ''; };
  return {
    Name:    find([/name[:\s]+([A-Za-z\s]{3,40})/i,/^([A-Z][a-z]+ [A-Z][a-z]+)/m]),
    DOB:     find([/dob[:\s]+(\d{2}[\/\-]\d{2}[\/\-]\d{4})/i,/date of birth[:\s]+(\d{2}[\/\-]\d{2}[\/\-]\d{4})/i]),
    Gender:  find([/\b(Male|Female)\b/i]),
    Aadhaar: find([/(\d{4}\s\d{4}\s\d{4})/]),
    PAN:     find([/([A-Z]{5}[0-9]{4}[A-Z])/]),
    Phone:   find([/(\+91[-\s]?[6-9]\d{9})/,/\b([6-9]\d{9})\b/]),
    Address: find([/address[:\s]+(.{15,80})/i]),
  };
}

function useOCRData() { toast('OCR data ready — it will auto-fill your next application form!'); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   ADMIN PANEL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
async function vAdmin(pg) {
  if (!A.isAdmin) { pg.innerHTML='<div style="text-align:center;padding:60px;color:var(--danger)"><div style="font-size:3rem">🚫</div><div style="font-weight:700;margin-top:10px">Admin access only</div></div>'; return; }
  pg.innerHTML = `<div class="view">
    <div class="sec-hd">
      <div class="sec-title">👑 Admin Panel <span class="badge-admin">ADMIN</span></div>
    </div>
    <div id="admin-stats" class="stats" style="margin-bottom:20px">
      <div class="stat-c"><div class="stat-lbl">Total Users</div><div class="stat-v" id="adm-users">…</div><div class="stat-ico">👥</div></div>
      <div class="stat-c"><div class="stat-lbl">All Applications</div><div class="stat-v" id="adm-apps">…</div><div class="stat-ico">📋</div></div>
      <div class="stat-c"><div class="stat-lbl">Platform Revenue</div><div class="stat-v" id="adm-rev">…</div><div class="stat-ico">💰</div></div>
      <div class="stat-c"><div class="stat-lbl">Pending Verifications</div><div class="stat-v" id="adm-pend">…</div><div class="stat-ico">⏳</div></div>
    </div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:20px">
      <div class="admin-card">
        <h4>🏪 Franchises / Operators</h4>
        <div id="adm-franchises"><div class="spin" style="margin:auto"></div></div>
      </div>
      <div class="admin-card">
        <h4>🔔 Recent All Applications</h4>
        <div id="adm-recent-apps"><div class="spin" style="margin:auto"></div></div>
      </div>
    </div>
    <div class="admin-card">
      <h4>⏳ Pending Payment Verifications</h4>
      <div id="adm-pending-pay"></div>
    </div>
  </div>`;

  try {
    // Users
    const usersSnap = await db.collection('users').get();
    const users = usersSnap.docs.map(d=>({id:d.id,...d.data()}));
    document.getElementById('adm-users').textContent = users.length;

    // All apps
    const appsSnap = await db.collection('applications').orderBy('createdAt','desc').limit(200).get();
    const apps = appsSnap.docs.map(d=>({id:d.id,...d.data()}));
    document.getElementById('adm-apps').textContent = apps.length;
    const totalRev = apps.reduce((s,a)=>s+Math.round((a.commission||0)*PLATFORM_CUT),0);
    document.getElementById('adm-rev').textContent = fmtINR(totalRev);
    const pending = apps.filter(a=>!a.paymentVerified).length;
    document.getElementById('adm-pend').textContent = pending;

    // Franchises list
    const ops = users.filter(u=>u.role!=='admin');
    document.getElementById('adm-franchises').innerHTML = ops.length
      ? ops.map(u=>`<div class="franchise-item">
          <div class="fi-name">${S.esc(u.shopName||u.email?.split('@')[0]||'—')}<div style="font-size:.65rem;color:var(--muted)">${S.esc(u.email||'—')}</div></div>
          <div class="fi-stat">
            <div class="v">${apps.filter(a=>a.uid===u.id).length} apps</div>
            <div class="s">${u.isActive?'✅ Active':'❌ Inactive'}</div>
          </div>
        </div>`).join('')
      : '<div style="color:var(--muted);font-size:.78rem">No operators yet</div>';

    // Recent apps
    document.getElementById('adm-recent-apps').innerHTML = apps.slice(0,8).map(a=>`
      <div class="franchise-item">
        <div><div style="font-size:.78rem;font-weight:600">${S.esc(a.serviceName||'—')}</div><div style="font-size:.65rem;color:var(--muted)">${S.esc(a.customerName||'—')}</div></div>
        <div class="fi-stat"><span class="pill p-${S.esc(a.status||'pending')}">${S.esc(a.status||'pending')}</span></div>
      </div>`).join('');

    // Pending verifications
    const unpaid = apps.filter(a=>!a.paymentVerified).slice(0,10);
    document.getElementById('adm-pending-pay').innerHTML = unpaid.length
      ? unpaid.map(a=>`<div class="franchise-item">
          <div>
            <div style="font-size:.78rem;font-weight:600">${S.esc(a.serviceName||'—')} — ${S.esc(a.customerName||'—')}</div>
            <div style="font-size:.65rem;color:var(--muted);font-family:var(--ff-mono)">${S.esc(a.txnId||'—')} | UTR: ${S.esc(a.utrRef||'—')}</div>
          </div>
          <div class="fi-stat">
            <button onclick="adminVerifyPayment('${a.id}')" class="btn btn-jade btn-xs" style="width:auto">✓ Verify</button>
          </div>
        </div>`).join('')
      : '<div style="color:var(--jade);font-size:.78rem;padding:10px 0">✅ All payments verified</div>';

  } catch(e) { toast('Admin load error: '+e.message,'err'); }
}

async function adminVerifyPayment(docId) {
  try {
    await db.collection('applications').doc(docId).update({
      paymentVerified: true,
      status: 'submitted',
      verifiedAt: firebase.firestore.FieldValue.serverTimestamp(),
      verifiedBy: A.user.uid
    });
    toast('Payment verified ✅');
    vAdmin(document.getElementById('page'));
  } catch(e) { toast('Error: '+e.message,'err'); }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   APPLY MODAL  — Full Flow with Firebase File Upload
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function openApplyModal(svc) {
  if (!A.user) { toast('Please login first','err'); return; }
  A.svc = svc;
  A.txnId = null;
  document.getElementById('mTitle').textContent = svc.name;
  document.getElementById('mSub').textContent   = `₹${svc.price} total  •  ₹${svc.comm} commission for you`;
  document.getElementById('mBody').innerHTML    = renderStep1(svc);
  document.getElementById('overlay').classList.add('open');
}

function closeModal() {
  document.getElementById('overlay').classList.remove('open');
}

function stepBar(step) {
  const labels = ['Details','Upload','Payment','Submit'];
  return `<div class="steps">${labels.map((l,i)=>`
    ${i>0?`<div class="step-conn ${i<step?'done':''}"></div>`:''}
    <div class="step-n ${i+1<step?'done':i+1===step?'active':''}">
      <div class="step-dot">${i+1<step?'✓':i+1}</div>
      <div class="step-lbl">${l}</div>
    </div>`).join('')}
  </div>`;
}

function renderStep1(svc) {
  const d = A.formData; // pre-fill from OCR if available
  return stepBar(1) + `
  <div class="row2">
    <div class="fld">
      <label class="fld-lbl">Customer Full Name *</label>
      <div class="fld-wrap"><i class="fas fa-user fld-ico"></i>
        <input class="fld-in" id="f-name" placeholder="As per Aadhaar" maxlength="100" value="${S.esc(d.Name||'')}">
      </div><div class="fld-err" id="e-name">Name is required</div>
    </div>
    <div class="fld">
      <label class="fld-lbl">Date of Birth *</label>
      <div class="fld-wrap"><i class="fas fa-calendar fld-ico"></i>
        <input class="fld-in" id="f-dob" type="date" value="${S.esc(d.DOB||'')}">
      </div><div class="fld-err" id="e-dob">DOB required</div>
    </div>
  </div>
  <div class="row2">
    <div class="fld">
      <label class="fld-lbl">Mobile Number *</label>
      <div class="fld-wrap"><i class="fas fa-phone fld-ico"></i>
        <input class="fld-in" id="f-mobile" placeholder="10-digit" maxlength="10" value="${S.esc(d.Phone||'')}">
      </div><div class="fld-err" id="e-mobile">Valid 10-digit number required</div>
    </div>
    <div class="fld">
      <label class="fld-lbl">Email Address</label>
      <div class="fld-wrap"><i class="fas fa-envelope fld-ico"></i>
        <input class="fld-in" id="f-email" type="email" placeholder="optional" maxlength="100">
      </div>
    </div>
  </div>
  <div class="row2">
    <div class="fld">
      <label class="fld-lbl">Aadhaar Number</label>
      <div class="fld-wrap"><i class="fas fa-id-card fld-ico"></i>
        <input class="fld-in" id="f-aadhaar" placeholder="12 digits" maxlength="12" value="${S.esc(d.Aadhaar||'')}">
      </div><div class="fld-err" id="e-aadhaar">Must be 12 digits</div>
    </div>
    <div class="fld">
      <label class="fld-lbl">PAN Number</label>
      <div class="fld-wrap"><i class="fas fa-file fld-ico"></i>
        <input class="fld-in" id="f-pan" placeholder="ABCDE1234F" maxlength="10" style="text-transform:uppercase" value="${S.esc(d.PAN||'')}">
      </div><div class="fld-err" id="e-pan">Invalid PAN format</div>
    </div>
  </div>
  <div class="fld">
    <label class="fld-lbl">Address</label>
    <div class="fld-wrap"><i class="fas fa-map-marker-alt fld-ico"></i>
      <input class="fld-in" id="f-addr" placeholder="Full address" maxlength="250" value="${S.esc(d.Address||'')}">
    </div>
  </div>
  <button class="btn btn-fire" onclick="validateStep1()"><i class="fas fa-arrow-right"></i> Next: Upload Documents</button>`;
}

function validateStep1() {
  let ok = true;
  const setE = (id, eid, cond) => {
    const el=document.getElementById(id), er=document.getElementById(eid);
    el?.classList.toggle('err',cond); er?.classList.toggle('on',cond);
    if(cond) ok=false;
  };
  const name   = S.clean(document.getElementById('f-name')?.value||'');
  const dob    = document.getElementById('f-dob')?.value||'';
  const mobile = (document.getElementById('f-mobile')?.value||'').trim();
  const aadhaar= (document.getElementById('f-aadhaar')?.value||'').trim();
  const pan    = (document.getElementById('f-pan')?.value||'').trim().toUpperCase();
  setE('f-name',   'e-name',   !name);
  setE('f-dob',    'e-dob',    !dob);
  setE('f-mobile', 'e-mobile', !S.isPhone(mobile));
  setE('f-aadhaar','e-aadhaar', aadhaar && !S.isAadhaar(aadhaar));
  setE('f-pan',    'e-pan',     pan && !S.isPAN(pan));
  if (!ok) return;
  A.formData = {
    name, dob, mobile,
    email:   S.clean(document.getElementById('f-email')?.value||''),
    aadhaar, pan,
    address: S.clean(document.getElementById('f-addr')?.value||''),
  };
  renderStep2();
}

function renderStep2() {
  document.getElementById('mBody').innerHTML = stepBar(2) + `
  <div style="font-size:.82rem;color:var(--muted2);margin-bottom:14px">
    Upload documents (Aadhaar, PAN card, Photo, other certificates). They will be securely stored in Firebase Storage.
  </div>
  <div class="up-zone" id="up-zone">
    <div class="u-ico"><i class="fas fa-cloud-upload-alt"></i></div>
    <div class="u-txt">Click or drag to upload files</div>
    <div class="u-sub">ZIP folder, PDF, JPG, PNG • Max 100MB total</div>
    <input type="file" id="up-files" multiple accept=".zip,.pdf,.jpg,.jpeg,.png" onchange="onFilesSelected(this)">
  </div>
  <div class="up-ok" id="up-ok"><i class="fas fa-check-circle"></i><span id="up-ok-txt"></span></div>
  <div class="up-prog" id="up-prog"><div class="up-prog-fill" id="up-fill" style="width:0%"></div></div>
  <div id="up-file-list" style="margin-top:8px;font-size:.72rem;color:var(--muted2)"></div>

  <div style="margin-top:14px;background:rgba(255,183,0,.07);border:1px solid rgba(255,183,0,.18);
    border-radius:9px;padding:11px;font-size:.77rem;color:var(--warn)">
    <i class="fas fa-info-circle"></i> Documents will be uploaded to Firebase Storage.
    For Aadhaar update services, only official UIDAI portal (myaadhaar.uidai.gov.in) accepts uploads.
  </div>

  <div style="display:flex;gap:10px;margin-top:14px">
    <button class="btn btn-outline" onclick="document.getElementById('mBody').innerHTML=renderStep1(A.svc)" style="flex:0 0 auto;width:auto">← Back</button>
    <button class="btn btn-fire" id="btn-upload" onclick="uploadAndProceed()"><i class="fas fa-upload"></i> Upload &amp; Continue</button>
  </div>`;
}

function onFilesSelected(input) {
  const files = Array.from(input.files);
  if (!files.length) return;
  document.getElementById('up-ok-txt').textContent = `${files.length} file(s) selected`;
  document.getElementById('up-ok').classList.add('on');
  document.getElementById('up-file-list').innerHTML = files.map(f=>
    `<div>📄 ${S.esc(f.name)} (${(f.size/1024).toFixed(1)} KB)</div>`
  ).join('');
  document.getElementById('up-zone').style.borderColor='var(--jade)';
}

async function uploadAndProceed() {
  const input = document.getElementById('up-files');
  const files = input?.files ? Array.from(input.files) : [];
  const btn = document.getElementById('btn-upload');
  btn.disabled = true;
  btn.innerHTML = '<div class="spin"></div> Uploading…';

  A.uploadedFiles = {};

  if (files.length > 0) {
    const prog = document.getElementById('up-prog');
    const fill = document.getElementById('up-fill');
    prog.classList.add('on');
    let done=0;
    for (const file of files) {
      try {
        const safeName = file.name.replace(/[^a-zA-Z0-9._-]/g,'_').slice(0,100);
        const path = `uploads/${A.user.uid}/${S.token(8)}_${safeName}`;
        const ref = storage.ref(path);
        await ref.put(file);
        const url = await ref.getDownloadURL();
        A.uploadedFiles[safeName] = url;
        done++;
        fill.style.width = (done/files.length*100)+'%';
      } catch(e) {
        toast(`Upload failed for ${file.name}: ${e.message}`,'warn');
      }
    }
    toast(`${done}/${files.length} files uploaded to Firebase Storage`);
  }

  renderStep3();
}

function renderStep3() {
  const svc = A.svc;
  const txn = 'TXN'+S.token(8);
  A.txnId = txn;
  const govtFee = svc.price - svc.comm;
  const upiStr = `upi://pay?pa=${UPI}&pn=CyberAce&am=${svc.price}&cu=INR&tn=${txn}`;

  document.getElementById('mBody').innerHTML = stepBar(3) + `
  <div class="qr-box">
    <div style="font-size:.85rem;color:var(--muted2);margin-bottom:13px">Scan with any UPI app (PhonePe, GPay, Paytm, BHIM)</div>
    <div id="pay-qr"></div>
    <div class="qr-amt">₹${svc.price}</div>
    <div class="qr-upi">${UPI}</div>
    <div class="qr-txn" onclick="navigator.clipboard.writeText('${txn}').then(()=>toast('Copied!'))" title="Click to copy">
      <i class="fas fa-copy"></i> ${txn}
    </div>
  </div>
  <div class="pay-breakdown">
    <div class="pb-row"><span>Government Fee</span><span style="color:var(--muted2)">₹${govtFee}</span></div>
    <div class="pb-row"><span>Our Service Fee</span><span style="color:var(--jade)">₹${svc.comm}</span></div>
    <div class="pb-row"><span>Total</span><span style="color:var(--fire)">₹${svc.price}</span></div>
  </div>
  <div class="utr-wrap">
    <div style="font-weight:600;margin-bottom:7px">✅ After payment, enter UTR/Reference from your UPI app:</div>
    <input class="fld-in" style="padding-left:12px" id="utr-in" placeholder="12-digit UTR / Reference number (optional)" maxlength="25">
    <div style="font-size:.68rem;color:var(--muted);margin-top:5px">This helps us verify your payment faster</div>
  </div>
  <button class="btn btn-fire" onclick="submitApplication()"><i class="fas fa-paper-plane"></i> Submit Application</button>
  <button class="btn btn-outline" style="margin-top:8px" onclick="renderStep2()">← Back</button>`;

  setTimeout(()=>{
    try { new QRCode(document.getElementById('pay-qr'),{text:upiStr,width:170,height:170}); } catch(e){}
  },80);
}

async function submitApplication() {
  const btn = document.querySelector('#mBody .btn-fire');
  if (btn) { btn.disabled=true; btn.innerHTML='<div class="spin"></div> Saving…'; }

  const svc = A.svc;
  const appNumber = 'APP'+S.token(8);
  const platformCut = Math.round(svc.comm * PLATFORM_CUT);

  try {
    const docRef = await db.collection('applications').add({
      uid:           A.user.uid,
      operatorEmail: A.user.email,
      appNumber,
      serviceId:     svc.id,
      serviceName:   svc.name,
      serviceCategory: svc.cat,
      customerName:  A.formData.name||'',
      customerDob:   A.formData.dob||'',
      customerPhone: A.formData.mobile||'',
      customerEmail: A.formData.email||'',
      aadhaar:       A.formData.aadhaar||'',
      pan:           A.formData.pan||'',
      address:       A.formData.address||'',
      amount:        svc.price,
      govtFee:       svc.price - svc.comm,
      serviceFee:    svc.comm,
      commission:    svc.comm,
      platformCut,
      netCommission: svc.comm - platformCut,
      txnId:         A.txnId||'',
      utrRef:        S.clean(document.getElementById('utr-in')?.value||''),
      uploadedFiles: A.uploadedFiles||{},
      portalUrl:     svc.url||'',
      status:        'submitted',
      paymentVerified: false,
      automationType: svc.auto||'semi',
      createdAt:     firebase.firestore.FieldValue.serverTimestamp(),
    });

    renderStep4(appNumber, docRef.id, svc);
    toast(`✅ ${svc.name} submitted! +₹${svc.comm} commission earned`);
  } catch(e) {
    toast('Save failed: '+e.message,'err');
    if (btn) { btn.disabled=false; btn.innerHTML='<i class="fas fa-paper-plane"></i> Submit Application'; }
  }
}

function renderStep4(appNumber, docId, svc) {
  const d = A.formData || {};
  const rawFields = {name:d.name,dob:d.dob,mobile:d.mobile,email:d.email,aadhaar:d.aadhaar,pan:d.pan,address:d.address};

  // Save to localStorage BEFORE portal opens (sync)
  try {
    localStorage.setItem('ca_autofill', JSON.stringify({
      data:d, url:svc.url||'', service:svc.name, ts:Date.now()
    }));
  } catch(e){}

  // Show floating data panel
  showDataPanel(d, svc);

  let portalHost = '';
  try { portalHost = new URL(svc.url||'https://x').hostname.replace('www.',''); } catch(e){}

  const fieldRows = Object.entries(rawFields).filter(([,v])=>v).map(([k,v])=>`
    <div class="fd-row">
      <span class="fd-k">${k[0].toUpperCase()+k.slice(1)}</span>
      <span class="fd-v" id="fdv-${k}">${k==='aadhaar'?v.replace(/\d(?=\d{4})/g,'×'):S.esc(v)}</span>
      <button class="fd-copy" onclick="copyField('${k}',${JSON.stringify(v)},this)" title="Copy">📋</button>
    </div>`).join('');

  document.getElementById('mBody').innerHTML = stepBar(4) + `
  <div class="success-scr">
    <div class="s-ico">🎉</div>
    <div class="s-title">Application Submitted!</div>
    <p style="font-size:.78rem;color:var(--muted2);margin-top:4px">
      App No: <strong style="color:var(--jade);font-family:var(--ff-mono)">${S.esc(appNumber)}</strong>
       &nbsp;•&nbsp; Commission: <strong style="color:var(--jade)">+₹${svc.comm}</strong>
    </p>

    ${svc.url ? `
    <!-- OPEN PORTAL — native <a> tag, never popup-blocked -->
    <div style="background:linear-gradient(135deg,rgba(0,191,165,.1),rgba(0,191,165,.03));
      border:2px solid rgba(0,191,165,.4);border-radius:14px;padding:16px;margin-top:14px;text-align:center">
      <div style="font-weight:700;color:var(--jade);font-size:.92rem;margin-bottom:8px">
        🚀 Step 1 — Open Official Portal
      </div>
      <a href="${S.esc(svc.url)}" target="_blank" rel="noopener noreferrer"
        onclick="onPortalClick()"
        style="display:inline-flex;align-items:center;gap:9px;padding:14px 26px;
        background:linear-gradient(135deg,#00BFA5,#00695C);color:#fff;border-radius:11px;
        font-weight:700;font-size:.95rem;text-decoration:none;
        box-shadow:0 6px 22px rgba(0,191,165,.4)">
        <i class="fas fa-external-link-alt"></i> Open ${S.esc(portalHost)}
      </a>
      <div style="margin-top:8px;font-size:.7rem;color:var(--muted)">🔗 ${S.esc(svc.url)}</div>
    </div>

    <!-- STEP 2 DATA PANEL -->
    <div style="background:rgba(255,183,0,.07);border:1px solid rgba(255,183,0,.25);
      border-radius:11px;padding:14px;margin-top:10px">
      <div style="font-weight:700;color:var(--warn);margin-bottom:8px">
        📋 Step 2 — Customer Data (tap 📋 to copy each field)
      </div>
      <div class="fd-list">${fieldRows}</div>
      <div style="display:flex;gap:8px;margin-top:10px;flex-wrap:wrap">
        <button class="btn btn-outline btn-sm" style="width:auto" onclick="copyAllData(${JSON.stringify(rawFields)})">
          <i class="fas fa-copy"></i> Copy All to Clipboard
        </button>
        <button class="btn btn-outline btn-sm" style="width:auto" onclick="showDataPanel(A.formData,A.svc)">
          <i class="fas fa-window-restore"></i> Show Floating Panel
        </button>
      </div>
      <div style="font-size:.7rem;color:var(--muted);margin-top:8px">
        💡 On the portal page: tap each form field → copy from here → paste
      </div>
    </div>` : `
    <div style="margin-top:14px;text-align:center">
      <a href="https://www.google.com/search?q=${encodeURIComponent(svc.name+' official portal apply India 2025')}"
        target="_blank" rel="noopener"
        style="display:inline-flex;align-items:center;gap:8px;padding:11px 20px;
        background:linear-gradient(135deg,#4285F4,#1565C0);color:#fff;
        border-radius:10px;font-weight:700;font-size:.85rem;text-decoration:none">
        <i class="fab fa-google"></i> Search Official Portal on Google
      </a>
    </div>`}
  </div>
  <button class="btn btn-fire" style="margin-top:12px" onclick="closeModal();gotoView('applications')">
    <i class="fas fa-list-alt"></i> View My Applications
  </button>`;
}

function onPortalClick() {
  try {
    localStorage.setItem('ca_autofill', JSON.stringify({
      data: A.formData||{}, url:A.svc?.url||'', service:A.svc?.name||'', ts:Date.now()
    }));
  } catch(e){}
  setTimeout(()=>{ if(document.getElementById('__dp__')) return; showDataPanel(A.formData||{},A.svc||{}); }, 600);
}

function copyField(key, val, btn) {
  navigator.clipboard.writeText(val||'').then(()=>{
    const orig = btn.textContent;
    btn.textContent = '✅';
    btn.style.color = '#00E676';
    setTimeout(()=>{ btn.textContent=orig; btn.style.color=''; }, 1500);
    toast(`${key[0].toUpperCase()+key.slice(1)} copied!`);
  }).catch(()=>{
    // Fallback for older browsers
    const el = document.createElement('textarea');
    el.value = val;
    el.style.position = 'fixed';
    el.style.opacity = '0';
    document.body.appendChild(el);
    el.select();
    document.execCommand('copy');
    document.body.removeChild(el);
    toast(`${key[0].toUpperCase()+key.slice(1)} copied!`);
  });
}

function copyAllData(data) {
  const lines = Object.entries(data).filter(([,v])=>v)
    .map(([k,v])=>`${k[0].toUpperCase()+k.slice(1)}: ${v}`).join('\n');
  navigator.clipboard.writeText(lines).then(()=>{
    toast('✅ All customer data copied to clipboard!');
  }).catch(()=>{
    const el = document.createElement('textarea');
    el.value = lines;
    el.style.position = 'fixed';
    el.style.opacity = '0';
    document.body.appendChild(el);
    el.select();
    document.execCommand('copy');
    document.body.removeChild(el);
    toast('✅ All data copied!');
  });
}



/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SMART AUTO-FILL SYSTEM
   • Native <a target="_blank"> = never popup-blocked on mobile
   • Floating Data Panel = copy-paste any field instantly
   • localStorage bridge = works with desktop bookmarklet too
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */

/* Floating Data Panel — the core auto-fill UI for mobile */
function showDataPanel(data, svc) {
  const old = document.getElementById('__dp__');
  if (old) old.remove();
  if (!data || !Object.values(data||{}).some(Boolean)) return;

  const FIELD_MAP = [
    {k:'name',lbl:'Name'},{k:'mobile',lbl:'Mobile'},{k:'dob',lbl:'DOB'},
    {k:'email',lbl:'Email'},{k:'aadhaar',lbl:'Aadhaar'},{k:'pan',lbl:'PAN'},
    {k:'address',lbl:'Address'},{k:'pincode',lbl:'Pincode'}
  ];
  const rows = FIELD_MAP.filter(r=>data[r.k]).map(r=>`
    <div class="dp-row">
      <span class="dp-lbl">${r.lbl}</span>
      <span class="dp-val" title="${S.esc(data[r.k])}">${r.k==='aadhaar'?(data[r.k]||'').replace(/\d(?=\d{4})/g,'×'):S.esc(data[r.k])}</span>
      <button class="dp-cp" onclick="dpCopy('${r.lbl}',${JSON.stringify(data[r.k]||'')},this)">📋</button>
    </div>`).join('');

  const panel = document.createElement('div');
  panel.id = '__dp__';
  panel.style.cssText = 'position:fixed;bottom:70px;right:12px;z-index:99999;width:270px;font-family:Arial,sans-serif';
  panel.innerHTML = `
    <div class="dp-inner" id="__dp_inner__">
      <div class="dp-hd" id="__dp_hd__">
        <span class="dp-ht">🧠 Customer Data</span>
        <button class="dp-min" onclick="document.getElementById('__dp_inner__').classList.toggle('dp-minimized')" title="Minimize">—</button>
        <button class="dp-close" onclick="document.getElementById('__dp__').remove()">×</button>
      </div>
      <div class="dp-bd">
        <div class="dp-svc">${svc&&svc.name ? S.esc(svc.name) : 'Tap 📋 to copy each field'}</div>
        ${rows}
      </div>
      <div class="dp-ft">
        <button class="dp-btn-copy" onclick="dpCopyAll(${JSON.stringify(data).replace(/"/g,'&quot;')})">📋 Copy All</button>
        <button class="dp-btn-hide" onclick="document.getElementById('__dp__').remove()">✓ Done</button>
      </div>
    </div>`;
  document.body.appendChild(panel);

  // Touch + mouse drag
  const hd = document.getElementById('__dp_hd__');
  let ox=0,oy=0,drag=false;
  const dragStart = (cx,cy)=>{ drag=true; panel.style.right='auto';
    ox=cx-panel.offsetLeft; oy=cy-panel.offsetTop; };
  hd.addEventListener('mousedown',e=>dragStart(e.clientX,e.clientY));
  hd.addEventListener('touchstart',e=>{ const t=e.touches[0]; dragStart(t.clientX,t.clientY); },{passive:true});
  document.addEventListener('mousemove',e=>{ if(drag){
    panel.style.left=Math.max(0,e.clientX-ox)+'px';
    panel.style.top=Math.max(0,Math.min(window.innerHeight-60,e.clientY-oy))+'px';
    panel.style.bottom='auto'; }});
  document.addEventListener('touchmove',e=>{ if(drag){ const t=e.touches[0];
    panel.style.left=Math.max(0,t.clientX-ox)+'px';
    panel.style.top=Math.max(0,t.clientY-oy)+'px'; panel.style.bottom='auto'; }},{passive:true});
  document.addEventListener('mouseup',()=>drag=false);
  document.addEventListener('touchend',()=>drag=false);
}

function dpCopy(label, val, btn) {
  const text = String(val||'');
  const done=()=>{ const o=btn.innerHTML; btn.innerHTML='✅'; btn.style.color='#00E676';
    setTimeout(()=>{ btn.innerHTML=o; btn.style.color=''; },1400); toast(label+' copied!'); };
  if (navigator.clipboard) { navigator.clipboard.writeText(text).then(done).catch(()=>legacyCopy(text,done)); }
  else legacyCopy(text,done);
}

function dpCopyAll(data) {
  const text = Object.entries(data||{}).filter(([,v])=>v)
    .map(([k,v])=>k[0].toUpperCase()+k.slice(1)+': '+v).join('\n');
  const done=()=>toast('✅ All customer data copied to clipboard!');
  if (navigator.clipboard) { navigator.clipboard.writeText(text).then(done).catch(()=>legacyCopy(text,done)); }
  else legacyCopy(text,done);
}

function copyField(key, val, btn) { dpCopy(key[0].toUpperCase()+key.slice(1), val, btn); }
function copyAllData(data) { dpCopyAll(data); }

function legacyCopy(text, cb) {
  const t=document.createElement('textarea');
  t.value=text; t.style.cssText='position:fixed;opacity:0;top:0;left:0;width:1px;height:1px';
  document.body.appendChild(t); t.select(); t.setSelectionRange(0,99999);
  try { document.execCommand('copy'); if(cb)cb(); } catch(e) {}
  document.body.removeChild(t);
}

function onPortalClick() {
  try {
    localStorage.setItem('ca_autofill', JSON.stringify({
      data:A.formData||{}, url:A.svc?.url||'', service:A.svc?.name||'', ts:Date.now()
    }));
  } catch(e){}
  setTimeout(()=>{ if(!document.getElementById('__dp__')) showDataPanel(A.formData||{},A.svc||{}); }, 500);
}

/* Called from job Direct Portal link */
function saveJobData(title, url) {
  try { localStorage.setItem('ca_autofill', JSON.stringify({
    data:A.formData||{}, url, service:title, ts:Date.now()
  })); } catch(e){}
  showDataPanel(A.formData||{}, {name:title, url});
}

/* launchAutofill kept for backward compat — now opens via href so never blocked */
function launchAutofill(url, serviceName, customData) {
  const data = customData || A.formData || {};
  if (!url) { toast('No portal URL','warn'); return; }
  try { localStorage.setItem('ca_autofill',JSON.stringify({data,url,service:serviceName,ts:Date.now()})); } catch(e){}
  window.open(url,'_blank');
  showDataPanel(data, {name:serviceName,url});
}

function quickLaunch(svc) {
  if (svc.url) { window.open(svc.url,'_blank'); toast('Opening '+svc.name+'…'); }
  else openGoogleSearch(svc.name);
}

function openGoogleSearch(query) {
  window.open('https://www.google.com/search?q='+encodeURIComponent(query+' official portal India 2025'),'_blank');
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   BOOKMARKLET SETUP VIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function vBookmarklet(pg) {
  // The complete auto-fill bookmarklet — runs on any portal page
  const BM_CODE = `javascript:(function(){'use strict';var d=JSON.parse(localStorage.getItem('ca_autofill')||'null');if(!d||!d.data||Date.now()-d.ts>300000){alert('\\u26A0\\uFE0F CyberAce: No session data.\\n\\nStep 1: Open CyberAce app\\nStep 2: Fill customer details\\nStep 3: Click Launch Portal\\nStep 4: Click this bookmarklet on the portal');return;}var data=d.data;var filled=0;var SM={'parivahan.gov.in':{name:['#applicantName','input[name="applicantName"]','input[name="fld_appname"]'],dob:['#dob','input[name="dob"]','input[name="fld_dob"]','input[type="date"]'],mobile:['#mobileNo','input[name="mobileNo"]','input[name="mobile"]'],email:['#emailId','input[type="email"]'],aadhaar:['#aadharNo','input[name="aadhaarNumber"]','input[placeholder*="Aadhaar" i]'],address:['#address','textarea[name="address"]'],pincode:['#pinCode','input[name="pinCode"]']},'myaadhaar.uidai.gov.in':{aadhaar:['#uid','input[name="uid"]','input[placeholder*="Aadhaar" i]'],mobile:['input[name="mobileNo"]','#mobile'],name:['input[name="name"]','#name']},'ssc.nic.in':{name:['input[name="CandidateName"]','#txtName'],dob:['input[name="DOB"]','#txtDOB','input[type="date"]'],mobile:['input[name="MobileNo"]','#txtMobile'],email:['input[type="email"]'],pan:['input[name="PAN"]'],aadhaar:['input[name*="aadhaar" i]']},'upsconline.nic.in':{name:['input[name="name"]','#candidateName'],dob:['input[name="dob"]','input[type="date"]'],mobile:['input[name="mobile"]','#mobile'],email:['input[type="email"]'],aadhaar:['input[name*="aadhaar" i]']},'ibps.in':{name:['#candidateName','input[name="CandidateName"]'],dob:['#dob','input[name="DateOfBirth"]','input[type="date"]'],mobile:['#mobileNumber','input[name="MobileNumber"]'],email:['input[type="email"]'],pan:['input[name*="pan" i]'],aadhaar:['input[name*="aadhaar" i]']},'tspsc.gov.in':{name:['#CandidateName','input[name="CandidateName"]'],dob:['#DOB','input[name="DOB"]','input[type="date"]'],mobile:['#MobileNumber','input[name="MobileNumber"]'],aadhaar:['#AadharNumber','input[name="AadharNumber"]'],email:['input[type="email"]']},'neet.nta.nic.in':{name:['input[name="candidateName"]'],dob:['input[name="dob"]','input[type="date"]'],mobile:['input[name="mobile"]'],email:['input[type="email"]'],aadhaar:['input[name*="aadhaar" i]']},'jeemain.nta.nic.in':{name:['input[name="candidateName"]'],dob:['input[type="date"]'],mobile:['input[name="mobile"]'],email:['input[type="email"]']},'scholarships.gov.in':{name:['input[name="applicantName"]'],dob:['input[name="dob"]','input[type="date"]'],mobile:['input[name="mobile"]'],aadhaar:['input[name="aadhaar"]'],email:['input[type="email"]'],address:['textarea[name*="address" i]']},'irctc.co.in':{name:['#firstName','input[name="firstName"]'],mobile:['#mobile','input[name="mobileNumber"]'],email:['input[type="email"]','#emailId']},'eportal.incometax.gov.in':{pan:['input[name*="pan" i]'],mobile:['input[type="tel"]'],email:['input[type="email"]']},'gst.gov.in':{pan:['input[name*="pan" i]'],mobile:['input[type="tel"]'],email:['input[type="email"]'],name:['input[name*="name" i]']},'udyamimitra.in':{name:['input[name*="name" i]'],mobile:['input[name*="mobile" i]'],email:['input[type="email"]'],pan:['input[name*="pan" i]'],aadhaar:['input[name*="aadhaar" i]']},'ts.meeseva.gov.in':{name:['input[name*="name" i]'],dob:['input[type="date"]'],mobile:['input[type="tel"]'],aadhaar:['input[name*="aadhaar" i]']},'edistrict.ap.gov.in':{name:['input[name*="applicantname" i]'],dob:['input[type="date"]'],mobile:['input[type="tel"]'],aadhaar:['input[name*="aadhaar" i]'],address:['textarea[name*="address" i]']}};var G={name:['input[name*="candidatename" i]','input[name*="applicantname" i]','input[name*="firstname" i]','input[name*="fullname" i]','input[id*="name" i]','input[name*="name" i]','input[placeholder*="full name" i]','input[autocomplete="name"]'],dob:['input[type="date"]','input[name*="dateofbirth" i]','input[name*="dob" i]','input[id*="dob" i]'],mobile:['input[name*="mobileno" i]','input[name*="mobile" i]','input[name*="phone" i]','input[id*="mobile" i]','input[type="tel"]','input[placeholder*="mobile" i]'],email:['input[type="email"]','input[name*="email" i]','input[id*="email" i]'],aadhaar:['input[name*="aadhaarno" i]','input[name*="aadhaar" i]','input[name*="aadhar" i]','input[id*="aadhaar" i]','input[placeholder*="aadhaar" i]'],pan:['input[name*="pannumber" i]','input[name*="pan" i]','input[id*="pan" i]','input[placeholder*="pan" i]'],address:['textarea[name*="address" i]','textarea[id*="address" i]','input[name*="address" i]'],pincode:['input[name*="pincode" i]','input[id*="pincode" i]','input[placeholder*="pincode" i]']};function fi(el,v){if(!el||el.disabled||el.readOnly||!v)return false;var t=el.tagName;if(t!=='INPUT'&&t!=='TEXTAREA'&&t!=='SELECT')return false;var p=t==='TEXTAREA'?HTMLTextAreaElement.prototype:HTMLInputElement.prototype;var ns=Object.getOwnPropertyDescriptor(p,'value');el.focus();if(ns&&ns.set)ns.set.call(el,v);else el.value=v;['input','change','keyup','blur'].forEach(function(e){el.dispatchEvent(new Event(e,{bubbles:true}))});return true;}function ts2(ss,v){for(var i=0;i<ss.length;i++){try{var ee=document.querySelectorAll(ss[i]);for(var j=0;j<ee.length;j++){if(ee[j].offsetParent!==null&&fi(ee[j],v))return true;}}catch(e){}}return false;}var h=location.hostname.replace('www.','');var sm=null;Object.keys(SM).forEach(function(k){if(h===k||h.endsWith('.'+k)||k.endsWith('.'+h))sm=SM[k];});var fm={name:data.name,dob:data.dob,mobile:data.mobile,email:data.email,aadhaar:data.aadhaar,pan:data.pan,address:data.address,pincode:data.pincode};Object.keys(fm).forEach(function(f){if(!fm[f])return;var ss=(sm&&sm[f])?sm[f].concat(G[f]||[]):(G[f]||[]);if(ts2(ss,fm[f]))filled++;});var old=document.getElementById('__ca__');if(old)old.remove();var el=document.createElement('div');el.id='__ca__';el.style.cssText='position:fixed;top:12px;right:12px;z-index:2147483647;font-family:Arial,sans-serif';el.innerHTML='<div style="background:#0D1421;border:1.5px solid rgba(255,87,34,.5);border-radius:12px;padding:14px 16px;box-shadow:0 16px 50px rgba(0,0,0,.75);min-width:230px"><div style="color:#FF5722;font-weight:700;font-size:13px;margin-bottom:7px">&#x1F9E0; CyberAce Auto-Fill</div><div style="color:'+(filled>0?'#00E676':'#FFD600')+';font-size:12px;margin-bottom:8px">'+(filled>0?'&#x2705; '+filled+' field'+(filled!==1?'s':'')+' filled!':'&#x26A0;&#xFE0F; 0 fields filled — try Re-Fill')+'</div>'+(data.name?'<div style="color:#8FA3B8;font-size:11px">&#x1F464; '+data.name+'</div>':'')+(data.mobile?'<div style="color:#8FA3B8;font-size:11px">&#x1F4F1; '+data.mobile+'</div>':'')+'<div style="margin-top:9px;display:flex;gap:6px"><button onclick="(function(){var f=0;var fm2={name:\''+encodeURIComponent(data.name||'')+'\',dob:\''+encodeURIComponent(data.dob||'')+'\',mobile:\''+encodeURIComponent(data.mobile||'')+'\',email:\''+encodeURIComponent(data.email||'')+'\',aadhaar:\''+encodeURIComponent(data.aadhaar||'')+'\',pan:\''+encodeURIComponent(data.pan||'')+'\',address:\''+encodeURIComponent(data.address||'')+'\',pincode:\''+encodeURIComponent(data.pincode||'')+'\' };var keys=Object.keys(fm2);keys.forEach(function(k){if(!fm2[k])return;var v=decodeURIComponent(fm2[k]);var ss=(sm&&sm[k])?sm[k].concat(G[k]||[]):(G[k]||[]);if(ts2(ss,v))f++;});var b=document.getElementById(\'__ca__\');if(b){var d2=b.querySelector(\'div div:nth-child(2)\');if(d2)d2.textContent=(f>0?\'\\u2705 \'+f+\' field\'+(f!==1?\'s\':\'\')+\' filled!\':\'\\u26A0\\uFE0F 0 fields — scroll down and try again\');};})()" style="background:#FF5722;color:#fff;border:none;border-radius:7px;padding:5px 10px;cursor:pointer;font-size:11px;font-weight:700">&#x1F504; Re-Fill</button><button onclick="document.getElementById(\'__ca__\').remove()" style="background:rgba(255,255,255,.08);color:#8FA3B8;border:1px solid rgba(255,255,255,.15);border-radius:7px;padding:5px 10px;cursor:pointer;font-size:11px">Close</button></div></div>';document.body.appendChild(el);})();`;

  pg.innerHTML = `<div class="view">
    <div class="sec-hd"><div class="sec-title">🔖 Bookmarklet Setup — One-Time (Free!)</div></div>

    <!-- Why Bookmarklet -->
    <div style="background:linear-gradient(135deg,rgba(0,191,165,.08),rgba(124,77,255,.06));border:1.5px solid rgba(0,191,165,.3);border-radius:14px;padding:18px;margin-bottom:20px">
      <div style="font-size:1rem;font-weight:700;color:var(--jade);margin-bottom:8px">⚡ How Real Auto-Fill Works (No Extension Needed!)</div>
      <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:10px;margin-top:10px">
        <div style="background:rgba(255,255,255,.03);border-radius:10px;padding:12px;font-size:.8rem">
          <div style="font-weight:700;color:var(--fire);margin-bottom:4px">Step 1 — Setup (Once only)</div>
          <div style="color:var(--muted2)">Drag the 🧠 button below to your browser bookmarks bar. Done!</div>
        </div>
        <div style="background:rgba(255,255,255,.03);border-radius:10px;padding:12px;font-size:.8rem">
          <div style="font-weight:700;color:var(--gold);margin-bottom:4px">Step 2 — Fill Customer Data</div>
          <div style="color:var(--muted2)">Select any service → fill Name, Mobile, Aadhaar etc. in the form</div>
        </div>
        <div style="background:rgba(255,255,255,.03);border-radius:10px;padding:12px;font-size:.8rem">
          <div style="font-weight:700;color:var(--jade);margin-bottom:4px">Step 3 — Launch Portal</div>
          <div style="color:var(--muted2)">Click "🚀 Open Portal & Auto-Fill Now" — portal opens in new tab</div>
        </div>
        <div style="background:rgba(255,255,255,.03);border-radius:10px;padding:12px;font-size:.8rem">
          <div style="font-weight:700;color:var(--sky);margin-bottom:4px">Step 4 — Click Bookmarklet</div>
          <div style="color:var(--muted2)">On the portal page, click your 🧠 bookmark — all fields auto-fill instantly!</div>
        </div>
      </div>
    </div>

    <!-- DRAG THIS BUTTON -->
    <div style="background:var(--card);border:1px solid var(--border);border-radius:14px;padding:22px;text-align:center;margin-bottom:20px">
      <div style="font-size:.88rem;color:var(--muted2);margin-bottom:14px">
        <i class="fas fa-grip-lines" style="color:var(--fire)"></i>
        <strong style="color:var(--text)"> Drag this button to your Bookmarks Bar:</strong>
        <span style="font-size:.75rem;color:var(--muted)"> (Show bookmarks bar: Ctrl+Shift+B)</span>
      </div>
      <div style="display:flex;justify-content:center;margin-bottom:14px">
        <a href="${BM_CODE.replace(/"/g, '&quot;')}"
          style="display:inline-flex;align-items:center;gap:8px;padding:13px 22px;
          background:linear-gradient(135deg,#FF5722,#B71C1C);color:#fff;
          border-radius:11px;font-weight:700;font-size:1rem;text-decoration:none;
          box-shadow:0 6px 22px rgba(255,87,34,.4);cursor:grab;
          border:2px dashed rgba(255,255,255,.3);
          user-select:none;white-space:nowrap"
          onclick="event.preventDefault();alert('Don\\'t click — DRAG this button to your Bookmarks Bar!')">
          🧠 CyberAce Auto-Fill
        </a>
      </div>
      <div style="font-size:.72rem;color:var(--muted);display:flex;align-items:center;justify-content:center;gap:6px">
        <i class="fas fa-info-circle" style="color:var(--jade)"></i>
        Hold and drag the orange button ↑ to your browser's bookmarks bar
      </div>
    </div>

    <!-- Browser-specific instructions -->
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:13px;margin-bottom:20px">
      <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:15px">
        <div style="font-weight:700;font-size:.88rem;margin-bottom:9px;color:var(--sky)">
          <i class="fas fa-globe"></i> Chrome / Edge
        </div>
        <ol style="font-size:.77rem;color:var(--muted2);padding-left:18px;line-height:1.7">
          <li>Press <kbd style="background:var(--card2);padding:1px 5px;border-radius:4px;font-size:.7rem">Ctrl+Shift+B</kbd> to show bookmarks bar</li>
          <li>Drag the orange 🧠 button above to the bar</li>
          <li>It appears as "🧠 CyberAce Auto-Fill"</li>
          <li>Done! Click it on any portal to auto-fill</li>
        </ol>
      </div>
      <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:15px">
        <div style="font-weight:700;font-size:.88rem;margin-bottom:9px;color:var(--fire2)">
          <i class="fab fa-firefox"></i> Firefox
        </div>
        <ol style="font-size:.77rem;color:var(--muted2);padding-left:18px;line-height:1.7">
          <li>Press <kbd style="background:var(--card2);padding:1px 5px;border-radius:4px;font-size:.7rem">Ctrl+Shift+B</kbd> to show bookmarks bar</li>
          <li>Drag the orange 🧠 button above to the bar</li>
          <li>Click "Add" when Firefox asks</li>
          <li>Done! Click it on any portal to auto-fill</li>
        </ol>
      </div>
    </div>

    <!-- Supported sites -->
    <div style="background:var(--card);border:1px solid var(--border);border-radius:var(--r);padding:16px">
      <div style="font-weight:700;margin-bottom:11px">✅ Portals with Site-Specific Auto-Fill</div>
      <div style="display:flex;flex-wrap:wrap;gap:7px">
        ${['parivahan.gov.in','myaadhaar.uidai.gov.in','ssc.nic.in','upsconline.nic.in','ibps.in','tspsc.gov.in','neet.nta.nic.in','jeemain.nta.nic.in','scholarships.gov.in','irctc.co.in','eportal.incometax.gov.in','gst.gov.in','udyamimitra.in','ts.meeseva.gov.in','edistrict.ap.gov.in','rrbntpc.gov.in','ctet.nic.in','sbi.co.in','passportindia.gov.in'].map(s=>`
        <div style="background:rgba(0,230,118,.06);border:1px solid rgba(0,230,118,.2);
          border-radius:99px;padding:3px 10px;font-size:.68rem;color:var(--jade)">${s}</div>`).join('')}
        <div style="background:rgba(255,183,0,.06);border:1px solid rgba(255,183,0,.2);
          border-radius:99px;padding:3px 10px;font-size:.68rem;color:var(--warn)">+ 100 more via generic fill</div>
      </div>
    </div>
  </div>`;

  // Save bookmarklet code to window for later access
  window.CA_BM = BM_CODE;
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   GLOBAL EXPORTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
Object.assign(window, {
  switchTab, toggleEye, updatePwdStr, doLogin, doSignup, doResetPwd, doLogout,
  gotoView, gotoViewFiltered, toggleSB,
  filterSvc, filterJobs, openApplyModal, openJobApply, closeModal,
  validateStep1, renderStep1, renderStep2, uploadAndProceed, onFilesSelected,
  renderStep3, submitApplication, runOCR, useOCRData,
  adminVerifyPayment, recalc, fmtINR,
  launchAutofill, quickLaunch, openGoogleSearch, vBookmarklet,
  showDataPanel, dpCopy, dpCopyAll, copyField, copyAllData,
  onPortalClick, saveJobData, searchServices, clearSearch, selectSearchResult,
  dashSearch, selectDashResult, searchJobs,
});

/* Lazy-load Tesseract */
const ts = document.createElement('script');
ts.src = 'https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js';
document.head.appendChild(ts);
</script>

<!--
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FIRESTORE SECURITY RULES  (deploy in Firebase Console)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Helper functions
    function isAuth() { return request.auth != null; }
    function isOwner(uid) { return request.auth.uid == uid; }
    function isAdmin() {
      return request.auth.token.email == "bonagirispinoj@gmail.com";
    }
    function validStr(val, maxLen) {
      return val is string && val.size() <= maxLen;
    }

    // Users: can read/write own doc; admin can read all
    match /users/{uid} {
      allow read: if isAuth() && (isOwner(uid) || isAdmin());
      allow create: if isAuth() && isOwner(uid)
        && validStr(request.resource.data.email, 100)
        && validStr(request.resource.data.shopName, 80);
      allow update: if isAuth() && (isOwner(uid) || isAdmin());
      allow delete: if isAdmin();
    }

    // Applications: operators manage own; admin can read all & verify
    match /applications/{appId} {
      allow read: if isAuth() && (
        resource.data.uid == request.auth.uid || isAdmin()
      );
      allow create: if isAuth()
        && request.resource.data.uid == request.auth.uid
        && validStr(request.resource.data.customerName, 100)
        && validStr(request.resource.data.customerPhone, 15)
        && request.resource.data.amount is number
        && request.resource.data.amount > 0
        && request.resource.data.amount < 100000;
      allow update: if isAuth() && (
        // Operator can only update non-sensitive fields
        (resource.data.uid == request.auth.uid
          && !('paymentVerified' in request.resource.data.diff(resource.data).affectedKeys())
          && !('verifiedBy' in request.resource.data.diff(resource.data).affectedKeys())
        )
        || isAdmin()
      );
      allow delete: if isAdmin();
    }

    // Deny everything else
    match /{document=**} {
      allow read, write: if false;
    }
  }
}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FIREBASE STORAGE RULES  (deploy in Firebase Console)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /uploads/{uid}/{allPaths=**} {
      // Only the owner can upload/read their files
      allow read, write: if request.auth != null
        && request.auth.uid == uid
        && request.resource.size < 100 * 1024 * 1024
        && (request.resource.contentType.matches('image/.*')
            || request.resource.contentType == 'application/pdf'
            || request.resource.contentType == 'application/zip');
    }
    match /{allPaths=**} {
      allow read, write: if false;
    }
  }
}
-->
</body>
</html>
