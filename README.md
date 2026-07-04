<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CourtConnect — Naga City Run Finder</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Anton&family=Inter:wght@400;500;600;700;800&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --ink:#14171C;
    --ink2:#1B1F26;
    --ink3:#242A33;
    --line:#31394480;
    --chalk:#F4F0E6;
    --chalk-dim:#B7B1A2;
    --paint:#FF5A29;
    --paint-dim:#B8431D;
    --court:#D79A54;
    --net:#8A93A1;
    --green:#4CAF7D;
    --red:#E85A4F;
    --gold:#E8B54C;
    --guard:#4C8FE8;
    --wing:#D79A54;
    --big:#E85A4F;
    --radius:14px;
  }
  *{box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    margin:0;
    background:var(--ink);
    color:var(--chalk);
    font-family:'Inter',sans-serif;
    -webkit-font-smoothing:antialiased;
  }
  h1,h2,h3,.display{
    font-family:'Anton',sans-serif;
    font-weight:400;
    letter-spacing:0.02em;
    text-transform:uppercase;
  }
  .mono{font-family:'IBM Plex Mono',monospace;}
  a{color:inherit;}
  button{font-family:'Inter',sans-serif; cursor:pointer;}
  :focus-visible{outline:2px solid var(--paint); outline-offset:2px;}

  /* ---------- layout shell ---------- */
  .court-lines{
    position:fixed; inset:0; z-index:0; pointer-events:none; opacity:0.05;
    background-image:
      repeating-linear-gradient(90deg, transparent, transparent 78px, var(--chalk) 78px, var(--chalk) 80px),
      repeating-linear-gradient(0deg, transparent, transparent 78px, var(--chalk) 78px, var(--chalk) 80px);
  }
  .wrap{position:relative; z-index:1; max-width:1180px; margin:0 auto; padding:0 24px;}

  nav{
    position:sticky; top:0; z-index:50;
    background:rgba(20,23,28,0.92); backdrop-filter:blur(8px);
    border-bottom:1px solid var(--line);
  }
  .nav-inner{display:flex; align-items:center; justify-content:space-between; max-width:1180px; margin:0 auto; padding:14px 24px;}
  .brand{display:flex; align-items:center; gap:10px;}
  .brand .badge{
    width:38px; height:38px; border-radius:50%; background:var(--paint);
    display:flex; align-items:center; justify-content:center;
    font-family:'Anton',sans-serif; font-size:18px; color:var(--ink); flex:none;
  }
  .brand .name{font-family:'Anton',sans-serif; font-size:20px; letter-spacing:0.03em;}
  .brand .name span{color:var(--paint);}
  .tabs{display:flex; gap:4px;}
  .tab-btn{
    background:transparent; border:none; color:var(--net); padding:10px 16px;
    font-size:14px; font-weight:600; border-radius:999px; transition:all .15s ease;
  }
  .tab-btn:hover{color:var(--chalk);}
  .tab-btn.active{background:var(--ink3); color:var(--chalk);}
  @media (max-width:720px){
    .tabs{display:none;}
    .tabs.mobile-open{
      display:flex; position:fixed; top:65px; left:0; right:0; background:var(--ink2);
      flex-direction:column; padding:12px; border-bottom:1px solid var(--line); z-index:60;
    }
    .menu-btn{display:inline-flex !important;}
  }
  .menu-btn{display:none; background:none; border:1px solid var(--line); color:var(--chalk); border-radius:8px; padding:8px 12px;}

  .page{display:none; padding:48px 0 96px;}
  .page.active{display:block; animation:fadeIn .35s ease;}
  @keyframes fadeIn{from{opacity:0; transform:translateY(6px);} to{opacity:1; transform:translateY(0);}}

  /* ---------- home / hero ---------- */
  .hero{padding:36px 0 12px; display:grid; grid-template-columns:1.15fr 0.85fr; gap:48px; align-items:center;}
  @media (max-width:880px){.hero{grid-template-columns:1fr;}}
  .eyebrow{color:var(--paint); font-weight:700; font-size:13px; letter-spacing:0.14em; text-transform:uppercase; margin-bottom:14px; display:flex; align-items:center; gap:8px;}
  .eyebrow::before{content:""; width:22px; height:2px; background:var(--paint); display:inline-block;}
  .hero h1{font-size:clamp(38px,5.6vw,68px); line-height:0.98; margin:0 0 18px;}
  .hero h1 .accent{color:var(--paint);}
  .hero p.lead{color:var(--chalk-dim); font-size:17px; line-height:1.6; max-width:46ch; margin:0 0 28px;}
  .cta-row{display:flex; gap:12px; flex-wrap:wrap;}
  .btn{
    border:none; border-radius:10px; padding:14px 22px; font-weight:700; font-size:14.5px;
    display:inline-flex; align-items:center; gap:8px; transition:transform .12s ease, filter .12s ease;
  }
  .btn:active{transform:scale(0.97);}
  .btn-primary{background:var(--paint); color:var(--ink);}
  .btn-primary:hover{filter:brightness(1.08);}
  .btn-ghost{background:transparent; color:var(--chalk); border:1px solid var(--line);}
  .btn-ghost:hover{border-color:var(--chalk-dim);}
  .btn-sm{padding:9px 14px; font-size:13px; border-radius:8px;}

  .hero-card{
    background:linear-gradient(155deg, var(--ink3), var(--ink2));
    border:1px solid var(--line); border-radius:20px; padding:26px;
    position:relative; overflow:hidden;
  }
  .hero-card::after{
    content:""; position:absolute; right:-60px; top:-60px; width:200px; height:200px;
    border-radius:50%; border:22px solid var(--paint); opacity:0.08;
  }
  .hero-card .tag{font-size:12px; color:var(--net); text-transform:uppercase; letter-spacing:0.1em; font-weight:700; margin-bottom:6px;}
  .scoreboard{display:grid; grid-template-columns:repeat(3,1fr); gap:14px; margin-top:6px;}
  .sb-cell{background:var(--ink); border:1px solid var(--line); border-radius:12px; padding:16px 10px; text-align:center;}
  .sb-cell .num{font-family:'IBM Plex Mono',monospace; font-size:30px; font-weight:600; color:var(--paint);}
  .sb-cell .lbl{font-size:11px; color:var(--net); margin-top:4px; text-transform:uppercase; letter-spacing:0.06em;}

  .position-legend{display:flex; gap:10px; margin-top:22px; flex-wrap:wrap;}
  .pos-chip{
    display:flex; align-items:center; gap:8px; background:var(--ink); border:1px solid var(--line);
    padding:8px 12px; border-radius:999px; font-size:12.5px; color:var(--chalk-dim);
  }
  .pos-dot{width:20px; height:20px; border-radius:50%; display:flex; align-items:center; justify-content:center;
    font-family:'IBM Plex Mono',monospace; font-size:10px; font-weight:700; color:var(--ink); flex:none;}

  .section-title{display:flex; align-items:baseline; justify-content:space-between; margin:64px 0 22px; gap:16px; flex-wrap:wrap;}
  .section-title h2{font-size:28px; margin:0;}
  .section-title p{color:var(--net); font-size:14px; margin:4px 0 0; max-width:52ch;}

  .how-grid{display:grid; grid-template-columns:repeat(5,1fr); gap:12px;}
  @media (max-width:900px){.how-grid{grid-template-columns:repeat(2,1fr);}}
  @media (max-width:520px){.how-grid{grid-template-columns:1fr;}}
  .how-card{background:var(--ink2); border:1px solid var(--line); border-radius:14px; padding:18px; position:relative;}
  .how-card .weight{font-family:'IBM Plex Mono',monospace; color:var(--paint); font-size:22px; font-weight:600;}
  .how-card h3{font-size:14px; margin:6px 0 6px; letter-spacing:0.02em;}
  .how-card p{font-size:12.5px; color:var(--net); margin:0; line-height:1.5;}
  .how-bar{height:5px; background:var(--ink3); border-radius:4px; overflow:hidden; margin-top:12px;}
  .how-bar span{display:block; height:100%; background:var(--paint);}

  /* ---------- forms / profile ---------- */
  .form-shell{display:grid; grid-template-columns:1fr 320px; gap:28px; align-items:start;}
  @media (max-width:960px){.form-shell{grid-template-columns:1fr;}}
  .card{background:var(--ink2); border:1px solid var(--line); border-radius:16px; padding:26px;}
  .card + .card{margin-top:18px;}
  .card h3{font-size:16px; margin:0 0 4px; display:flex; align-items:center; gap:10px;}
  .card .sub{font-size:12.5px; color:var(--net); margin:0 0 18px;}
  .icon-badge{width:28px; height:28px; border-radius:8px; background:var(--ink); display:flex; align-items:center; justify-content:center; font-size:14px; flex:none;}

  .field-grid{display:grid; grid-template-columns:1fr 1fr; gap:16px;}
  @media (max-width:520px){.field-grid{grid-template-columns:1fr;}}
  label{font-size:12.5px; color:var(--chalk-dim); font-weight:600; display:block; margin-bottom:6px;}
  input[type=text], input[type=number], select, textarea{
    width:100%; background:var(--ink); border:1px solid var(--line); border-radius:9px; color:var(--chalk);
    padding:10px 12px; font-size:14px; font-family:'Inter',sans-serif;
  }
  input:focus, select:focus, textarea:focus{border-color:var(--paint);}
  .field{margin-bottom:14px;}
  .row-inline{display:flex; gap:10px; align-items:center;}
  input[type=range]{width:100%; accent-color:var(--paint);}
  .range-val{font-family:'IBM Plex Mono',monospace; color:var(--paint); font-weight:600; font-size:13px;}

  .chip-select{display:flex; flex-wrap:wrap; gap:8px;}
  .chip-opt{
    padding:8px 14px; border-radius:999px; border:1px solid var(--line); background:var(--ink); color:var(--chalk-dim);
    font-size:13px; font-weight:600; transition:all .12s ease;
  }
  .chip-opt.selected{background:var(--paint); border-color:var(--paint); color:var(--ink);}
  .chip-opt:hover{border-color:var(--chalk-dim);}

  .pos-select{display:flex; flex-wrap:wrap; gap:10px;}
  .pos-opt{
    width:56px; height:56px; border-radius:50%; border:2px solid var(--line); background:var(--ink);
    display:flex; align-items:center; justify-content:center; font-family:'IBM Plex Mono',monospace; font-weight:700;
    color:var(--chalk-dim); font-size:13px; flex-direction:column; gap:0;
  }
  .pos-opt.selected{border-color:var(--paint); color:var(--paint); background:#2a1810;}

  .avail-grid{display:grid; grid-template-columns:70px repeat(7,1fr); gap:6px; font-size:11px;}
  .avail-grid .hd{color:var(--net); text-align:center; padding:6px 0; font-weight:700;}
  .avail-grid .rowlabel{color:var(--net); display:flex; align-items:center; font-weight:600;}
  .avail-cell{
    aspect-ratio:1; border-radius:6px; background:var(--ink); border:1px solid var(--line); cursor:pointer;
  }
  .avail-cell.on{background:var(--green); border-color:var(--green);}

  .sticky-side{position:sticky; top:90px; display:flex; flex-direction:column; gap:16px;}
  .preview-card{background:linear-gradient(165deg,var(--ink3),var(--ink2)); border:1px solid var(--line); border-radius:16px; padding:20px; text-align:center;}
  .avatar{
    width:74px; height:74px; border-radius:50%; margin:0 auto 12px; display:flex; align-items:center; justify-content:center;
    background:var(--ink); border:2px solid var(--paint); font-family:'Anton',sans-serif; font-size:26px; color:var(--paint);
  }
  .preview-card h4{margin:0; font-size:17px;}
  .preview-card .meta{color:var(--net); font-size:12.5px; margin-top:2px;}
  .skill-bar-wrap{margin-top:16px; text-align:left;}
  .skill-bar-wrap .lbl-row{display:flex; justify-content:space-between; font-size:11.5px; color:var(--net); margin-bottom:4px;}
  .skill-bar{height:8px; border-radius:6px; background:var(--ink); overflow:hidden;}
  .skill-bar span{display:block; height:100%; background:linear-gradient(90deg, var(--court), var(--paint));}

  .save-bar{position:sticky; bottom:0; background:var(--ink); border-top:1px solid var(--line); padding:14px 0; margin-top:24px; display:flex; justify-content:flex-end; gap:10px;}

  /* ---------- find a run ---------- */
  .find-shell{display:grid; grid-template-columns:280px 1fr; gap:26px; align-items:start;}
  @media (max-width:900px){.find-shell{grid-template-columns:1fr;}}
  .filter-card{background:var(--ink2); border:1px solid var(--line); border-radius:16px; padding:20px; position:sticky; top:90px;}
  .filter-card h3{font-size:14px; margin:0 0 16px; text-transform:uppercase; letter-spacing:0.06em; color:var(--net);}
  .filter-group{margin-bottom:18px;}
  .filter-group label{margin-bottom:8px;}

  .results-head{display:flex; justify-content:space-between; align-items:center; margin-bottom:16px; gap:10px; flex-wrap:wrap;}
  .results-head .count{color:var(--net); font-size:13px;}
  .sort-select{width:auto; padding:8px 10px; font-size:13px;}

  .player-grid{display:grid; grid-template-columns:repeat(auto-fill,minmax(250px,1fr)); gap:16px;}
  .p-card{
    background:var(--ink2); border:1px solid var(--line); border-radius:16px; padding:18px; cursor:pointer;
    transition:transform .15s ease, border-color .15s ease;
  }
  .p-card:hover{transform:translateY(-3px); border-color:var(--paint);}
  .p-card-top{display:flex; gap:12px; align-items:center;}
  .p-avatar{width:48px; height:48px; border-radius:50%; background:var(--ink); border:2px solid var(--court); display:flex; align-items:center; justify-content:center; font-family:'Anton',sans-serif; color:var(--court); font-size:17px; flex:none;}
  .p-name{font-weight:700; font-size:15px; margin:0;}
  .p-sub{font-size:12px; color:var(--net); margin-top:2px;}
  .gauge{
    width:52px; height:52px; border-radius:50%; display:flex; align-items:center; justify-content:center; flex:none;
    background:conic-gradient(var(--paint) calc(var(--pct)*1%), var(--ink3) 0);
  }
  .gauge-inner{width:40px; height:40px; border-radius:50%; background:var(--ink2); display:flex; align-items:center; justify-content:center; font-family:'IBM Plex Mono',monospace; font-size:12px; font-weight:700;}
  .p-tags{display:flex; flex-wrap:wrap; gap:6px; margin:12px 0;}
  .tag-chip{font-size:10.5px; background:var(--ink); border:1px solid var(--line); padding:4px 9px; border-radius:999px; color:var(--chalk-dim);}
  .p-foot{display:flex; justify-content:space-between; align-items:center; font-size:12px; color:var(--net); margin-top:10px;}
  .stars{color:var(--gold); letter-spacing:1px; font-size:12px;}

  .empty-state{text-align:center; padding:60px 20px; color:var(--net);}
  .empty-state .big{font-size:40px; margin-bottom:10px;}

  /* ---------- leaderboard ---------- */
  .lb-table{width:100%; border-collapse:collapse; background:var(--ink2); border:1px solid var(--line); border-radius:16px; overflow:hidden;}
  .lb-table th{text-align:left; font-size:11px; text-transform:uppercase; letter-spacing:0.06em; color:var(--net); padding:14px 16px; border-bottom:1px solid var(--line); background:var(--ink3);}
  .lb-table td{padding:14px 16px; border-bottom:1px solid var(--line); font-size:13.5px;}
  .lb-table tr:last-child td{border-bottom:none;}
  .rank-badge{width:28px; height:28px; border-radius:50%; background:var(--ink); display:flex; align-items:center; justify-content:center; font-family:'IBM Plex Mono',monospace; font-weight:700; font-size:12px; color:var(--chalk-dim);}
  .rank-badge.top{background:var(--gold); color:var(--ink);}
  .lb-name-cell{display:flex; align-items:center; gap:10px;}
  .lb-avatar{width:32px; height:32px; border-radius:50%; background:var(--ink); border:1px solid var(--line); display:flex; align-items:center; justify-content:center; font-family:'Anton',sans-serif; font-size:13px; color:var(--court);}
  @media (max-width:700px){ .lb-table .hide-sm{display:none;} }

  /* ---------- modal ---------- */
  .modal-bg{position:fixed; inset:0; background:rgba(10,12,15,0.72); z-index:100; display:none; align-items:flex-start; justify-content:center; padding:40px 16px; overflow-y:auto;}
  .modal-bg.open{display:flex;}
  .modal{background:var(--ink2); border:1px solid var(--line); border-radius:18px; max-width:640px; width:100%; padding:0; overflow:hidden;}
  .modal-head{background:linear-gradient(155deg,var(--ink3),var(--ink2)); padding:26px; display:flex; gap:16px; align-items:center; position:relative;}
  .modal-close{position:absolute; top:14px; right:16px; background:none; border:none; color:var(--net); font-size:22px; line-height:1;}
  .modal-close:hover{color:var(--chalk);}
  .modal-avatar{width:66px; height:66px; border-radius:50%; background:var(--ink); border:2px solid var(--paint); display:flex; align-items:center; justify-content:center; font-family:'Anton',sans-serif; font-size:24px; color:var(--paint); flex:none;}
  .modal-body{padding:24px 26px 26px;}
  .modal-section{margin-bottom:22px;}
  .modal-section h4{font-size:12px; text-transform:uppercase; letter-spacing:0.08em; color:var(--net); margin:0 0 12px;}
  .stat-row{display:flex; align-items:center; gap:10px; margin-bottom:8px; font-size:12.5px;}
  .stat-row .stat-lbl{width:64px; color:var(--chalk-dim); flex:none;}
  .stat-row .stat-track{flex:1; height:7px; background:var(--ink); border-radius:5px; overflow:hidden;}
  .stat-row .stat-track span{display:block; height:100%; background:var(--court);}
  .stat-row .stat-num{width:32px; text-align:right; font-family:'IBM Plex Mono',monospace; color:var(--chalk-dim);}
  .match-breakdown{display:grid; gap:10px;}
  .mb-row{display:grid; grid-template-columns:110px 1fr 40px; gap:10px; align-items:center; font-size:12px;}
  .mb-row .stat-track span{background:var(--paint);}
  .mini-avail{display:grid; grid-template-columns:repeat(7,1fr); gap:4px;}
  .mini-avail .day{font-size:9px; text-align:center; color:var(--net); margin-bottom:3px;}
  .mini-avail .cell{aspect-ratio:1; border-radius:4px; background:var(--ink);}
  .mini-avail .cell.on{background:var(--green);}
  .rep-row{display:flex; justify-content:space-between; align-items:center; padding:8px 0; border-bottom:1px solid var(--line); font-size:13px;}
  .rep-row:last-child{border-bottom:none;}
  .modal-actions{display:flex; gap:10px; margin-top:6px;}

  /* ---------- toast ---------- */
  .toast{
    position:fixed; bottom:24px; left:50%; transform:translateX(-50%) translateY(20px); opacity:0;
    background:var(--chalk); color:var(--ink); padding:12px 20px; border-radius:10px; font-weight:600; font-size:13.5px;
    z-index:200; transition:all .25s ease; pointer-events:none; box-shadow:0 8px 24px rgba(0,0,0,0.35);
  }
  .toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

  footer{border-top:1px solid var(--line); padding:26px 0; text-align:center; color:var(--net); font-size:12.5px;}
</style>
</head>
<body>

<div class="court-lines"></div>

<nav>
  <div class="nav-inner">
    <div class="brand">
      <div class="badge">CC</div>
      <div class="name">Court<span>Connect</span></div>
    </div>
    <div class="tabs" id="tabs">
      <button class="tab-btn active" data-page="home">Home</button>
      <button class="tab-btn" data-page="profile">My Roster Card</button>
      <button class="tab-btn" data-page="find">Find a Run</button>
      <button class="tab-btn" data-page="leaderboard">Leaderboard</button>
    </div>
    <button class="menu-btn" id="menuBtn">☰ Menu</button>
  </div>
</nav>

<div class="wrap">

  <!-- ============ HOME ============ -->
  <section class="page active" id="page-home">
    <div class="hero">
      <div>
        <div class="eyebrow">Naga City · Camarines Sur pickup runs</div>
        <h1>Find a run that<br>actually <span class="accent">fits your game.</span></h1>
        <p class="lead">CourtConnect matches you with nearby players by skill, position, schedule, and playing style — so you're not stuck in a 5-centers-under-the-rim mess or a game three levels above your speed.</p>
        <div class="cta-row">
          <button class="btn btn-primary" onclick="goTo('profile')">Build your roster card</button>
          <button class="btn btn-ghost" onclick="goTo('find')">Browse nearby players</button>
        </div>
      </div>
      <div class="hero-card">
        <div class="tag">Naga City, right now</div>
        <h3 style="margin:0 0 4px; font-size:20px;">Court pulse</h3>
        <div class="scoreboard">
          <div class="sb-cell"><div class="num mono" id="statPlayers">--</div><div class="lbl">Players nearby</div></div>
          <div class="sb-cell"><div class="num mono" id="statRuns">--</div><div class="lbl">Runs this week</div></div>
          <div class="sb-cell"><div class="num mono" id="statScore">--</div><div class="lbl">Avg match score</div></div>
        </div>
        <div class="position-legend">
          <div class="pos-chip"><span class="pos-dot" style="background:var(--guard)">PG</span>Floor general</div>
          <div class="pos-chip"><span class="pos-dot" style="background:var(--guard)">SG</span>Shot maker</div>
          <div class="pos-chip"><span class="pos-dot" style="background:var(--wing)">SF</span>Two-way wing</div>
          <div class="pos-chip"><span class="pos-dot" style="background:var(--big)">PF</span>Interior force</div>
          <div class="pos-chip"><span class="pos-dot" style="background:var(--big)">C</span>Rim anchor</div>
        </div>
      </div>
    </div>

    <div class="section-title">
      <div>
        <h2>How matching actually works</h2>
        <p>Every nearby player gets a live compatibility score built from five weighted signals — no more guessing if a run is worth the drive.</p>
      </div>
    </div>
    <div class="how-grid">
      <div class="how-card"><div class="weight">30%</div><h3>Skill fit</h3><p>How close your rating is to theirs — close games, not blowouts.</p><div class="how-bar"><span style="width:30%"></span></div></div>
      <div class="how-card"><div class="weight">20%</div><h3>Position need</h3><p>Fills the slot your squad is missing instead of stacking one role.</p><div class="how-bar"><span style="width:20%"></span></div></div>
      <div class="how-card"><div class="weight">20%</div><h3>Distance</h3><p>Inside your max travel radius from your set location.</p><div class="how-bar"><span style="width:20%"></span></div></div>
      <div class="how-card"><div class="weight">15%</div><h3>Schedule overlap</h3><p>Shares real open windows in your weekly availability grid.</p><div class="how-bar"><span style="width:15%"></span></div></div>
      <div class="how-card"><div class="weight">15%</div><h3>Style balance</h3><p>Different tags than yours — shooters need rebounders, not more shooters.</p><div class="how-bar"><span style="width:15%"></span></div></div>
    </div>
  </section>

  <!-- ============ PROFILE ============ -->
  <section class="page" id="page-profile">
    <div class="section-title">
      <div>
        <h2>Build your roster card</h2>
        <p>This is what nearby players see when CourtConnect suggests you as a match. Fields marked optional can stay blank.</p>
      </div>
    </div>

    <div class="form-shell">
      <div>

        <div class="card">
          <h3><span class="icon-badge">🪪</span>Identity</h3>
          <p class="sub">Who you are, and where to find you.</p>
          <div class="field-grid">
            <div class="field"><label>Full name / username</label><input type="text" id="f-name" placeholder="e.g. Marco Villanueva"></div>
            <div class="field"><label>Age</label><input type="number" id="f-age" min="12" max="70" placeholder="24"></div>
            <div class="field"><label>Gender (optional — for league type)</label>
              <select id="f-gender"><option value="">Prefer not to say</option><option>Male</option><option>Female</option><option>Co-ed / no preference</option></select>
            </div>
            <div class="field"><label>Contact (in-app only, optional)</label><input type="text" id="f-contact" placeholder="Messenger / number"></div>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">📏</span>Physical attributes</h3>
          <p class="sub">Helps balance size on the court — guards vs. bigs.</p>
          <div class="field-grid">
            <div class="field"><label>Height (cm)</label><input type="number" id="f-height" placeholder="178"></div>
            <div class="field"><label>Weight (kg, optional)</label><input type="number" id="f-weight" placeholder="72"></div>
            <div class="field"><label>Wingspan (cm, optional)</label><input type="number" id="f-wingspan" placeholder="182"></div>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">🎯</span>Playing position</h3>
          <p class="sub">Pick your primary spot, or stay flexible.</p>
          <div class="pos-select" id="posSelect">
            <div class="pos-opt" data-pos="PG">PG</div>
            <div class="pos-opt" data-pos="SG">SG</div>
            <div class="pos-opt" data-pos="SF">SF</div>
            <div class="pos-opt" data-pos="PF">PF</div>
            <div class="pos-opt" data-pos="C">C</div>
            <div class="pos-opt" data-pos="Flex">Flex</div>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">⭐</span>Skill level</h3>
          <p class="sub">The single most important number for fair matchmaking.</p>
          <div class="chip-select" id="skillTier">
            <div class="chip-opt" data-tier="Beginner">Beginner</div>
            <div class="chip-opt" data-tier="Intermediate">Intermediate</div>
            <div class="chip-opt" data-tier="Advanced">Advanced</div>
            <div class="chip-opt" data-tier="Competitive">Competitive</div>
          </div>
          <div class="field" style="margin-top:16px;">
            <div class="row-inline" style="justify-content:space-between;"><label style="margin:0;">Rating (1–100)</label><span class="range-val" id="ratingVal">50</span></div>
            <input type="range" id="f-rating" min="1" max="100" value="50">
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">📊</span>Performance stats <span style="font-weight:400; font-size:11.5px; color:var(--net);">(optional)</span></h3>
          <p class="sub">Per-game averages. Leave at 0 if you're not tracking yet.</p>
          <div class="field-grid">
            <div class="field"><label>Points per game</label><input type="number" id="f-ppg" placeholder="12"></div>
            <div class="field"><label>Assists per game</label><input type="number" id="f-apg" placeholder="4"></div>
            <div class="field"><label>Rebounds per game</label><input type="number" id="f-rpg" placeholder="5"></div>
            <div class="field"><label>Steals + blocks per game</label><input type="number" id="f-spg" placeholder="2"></div>
            <div class="field"><label>Win rate (%)</label><input type="number" id="f-winrate" placeholder="55"></div>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">🗓️</span>Availability</h3>
          <p class="sub">Tap a cell to mark yourself open. Used to auto-schedule matches.</p>
          <div class="avail-grid" id="availGrid">
            <div class="hd"></div>
            <div class="hd">Mon</div><div class="hd">Tue</div><div class="hd">Wed</div><div class="hd">Thu</div><div class="hd">Fri</div><div class="hd">Sat</div><div class="hd">Sun</div>
          </div>
          <div class="field" style="margin-top:14px;">
            <label>Preferred game duration</label>
            <select id="f-duration"><option>30–45 min</option><option selected>1 hour</option><option>1.5 hours</option><option>2+ hours</option></select>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">📍</span>Location & court preference</h3>
          <p class="sub">Only nearby, matching runs get shown to you.</p>
          <div class="field-grid">
            <div class="field"><label>Current location / barangay</label><input type="text" id="f-location" placeholder="Naga City"></div>
            <div class="field"><label>Preferred court</label><input type="text" id="f-court" placeholder="e.g. Naga City Sports Complex"></div>
          </div>
          <div class="field">
            <div class="row-inline" style="justify-content:space-between;"><label style="margin:0;">Max travel distance</label><span class="range-val" id="distVal">10 km</span></div>
            <input type="range" id="f-distance" min="1" max="30" value="10">
          </div>
          <button class="btn btn-ghost btn-sm" type="button" onclick="useGeolocation()">📡 Use my current location</button>
        </div>

        <div class="card">
          <h3><span class="icon-badge">🏷️</span>Play style tags</h3>
          <p class="sub">Pick up to 3 — mismatched tags build more balanced teams.</p>
          <div class="chip-select" id="styleTags">
            <div class="chip-opt" data-tag="Shooter">Shooter</div>
            <div class="chip-opt" data-tag="Defender">Defender</div>
            <div class="chip-opt" data-tag="Playmaker">Playmaker</div>
            <div class="chip-opt" data-tag="Slasher">Slasher</div>
            <div class="chip-opt" data-tag="Rebounder">Rebounder</div>
          </div>
        </div>

        <div class="card">
          <h3><span class="icon-badge">🤝</span>Team preferences</h3>
          <div class="field-grid">
            <div class="field"><label>Game intensity</label>
              <select id="f-intensity"><option>Casual</option><option>Competitive</option><option selected>Either</option></select>
            </div>
            <div class="field"><label>Squad type</label>
              <select id="f-squad"><option selected>Open to randoms</option><option>With friends</option><option>Either</option></select>
            </div>
            <div class="field"><label>Game visibility</label>
              <select id="f-visibility"><option selected>Public</option><option>Private (invite only)</option></select>
            </div>
          </div>
        </div>

      </div>

      <div class="sticky-side">
        <div class="preview-card">
          <div class="avatar" id="prevInitials">?</div>
          <h4 id="prevName">Your name</h4>
          <div class="meta" id="prevMeta">Position · Location</div>
          <div class="skill-bar-wrap">
            <div class="lbl-row"><span>Skill rating</span><span id="prevRatingLbl">50 / 100</span></div>
            <div class="skill-bar"><span id="prevRatingBar" style="width:50%"></span></div>
          </div>
          <div class="skill-bar-wrap">
            <div class="lbl-row"><span>Tags</span></div>
            <div class="p-tags" id="prevTags"><span class="tag-chip">None yet</span></div>
          </div>
        </div>
        <div class="card" style="padding:16px;">
          <p class="sub" style="margin:0;">Your reputation (stars, sportsmanship, no-show record) builds automatically as other players confirm games with you — it's not self-reported.</p>
        </div>
      </div>
    </div>

    <div class="save-bar">
      <button class="btn btn-ghost" onclick="resetProfile()">Reset</button>
      <button class="btn btn-primary" onclick="saveProfile()">Save roster card</button>
    </div>
  </section>

  <!-- ============ FIND A RUN ============ -->
  <section class="page" id="page-find">
    <div class="section-title">
      <div>
        <h2>Find a run near you</h2>
        <p>Ranked by match score against your saved roster card. Adjust filters to widen or narrow the pool.</p>
      </div>
    </div>
    <div class="find-shell">
      <div class="filter-card">
        <h3>Filters</h3>
        <div class="filter-group">
          <label>Position needed</label>
          <select id="filt-pos">
            <option value="Any">Any</option>
            <option>PG</option><option>SG</option><option>SF</option><option>PF</option><option>C</option><option>Flex</option>
          </select>
        </div>
        <div class="filter-group">
          <label>Min skill rating: <span class="range-val" id="filtSkillMinVal">0</span></label>
          <input type="range" id="filt-skillmin" min="0" max="100" value="0">
        </div>
        <div class="filter-group">
          <label>Max distance: <span class="range-val" id="filtDistVal">15 km</span></label>
          <input type="range" id="filt-dist" min="1" max="30" value="15">
        </div>
        <div class="filter-group">
          <label>Play style</label>
          <select id="filt-tag">
            <option value="Any">Any</option>
            <option>Shooter</option><option>Defender</option><option>Playmaker</option><option>Slasher</option><option>Rebounder</option>
          </select>
        </div>
        <div class="filter-group">
          <label>Game intensity</label>
          <select id="filt-intensity"><option>Any</option><option>Casual</option><option>Competitive</option></select>
        </div>
        <button class="btn btn-ghost btn-sm" style="width:100%; justify-content:center;" onclick="resetFilters()">Reset filters</button>
      </div>

      <div>
        <div class="results-head">
          <div class="count" id="resultsCount">— players</div>
          <select class="sort-select" id="sortBy">
            <option value="match">Sort: Best match</option>
            <option value="distance">Sort: Nearest</option>
            <option value="skill">Sort: Highest skill</option>
            <option value="rating">Sort: Top rated</option>
          </select>
        </div>
        <div class="player-grid" id="playerGrid"></div>
      </div>
    </div>
  </section>

  <!-- ============ LEADERBOARD ============ -->
  <section class="page" id="page-leaderboard">
    <div class="section-title">
      <div>
        <h2>Naga City leaderboard</h2>
        <p>Ranked by reputation and win rate. Sportsmanship and no-shows factor into where you land.</p>
      </div>
    </div>
    <table class="lb-table">
      <thead>
        <tr>
          <th>#</th><th>Player</th><th>Pos</th><th class="hide-sm">Rating</th><th>Reputation</th><th class="hide-sm">Win rate</th><th class="hide-sm">Sportsmanship</th>
        </tr>
      </thead>
      <tbody id="leaderboardBody"></tbody>
    </table>
  </section>

</div>

<footer>CourtConnect · a matchmaking concept for pickup basketball in Naga City, Camarines Sur</footer>

<!-- ============ MODAL ============ -->
<div class="modal-bg" id="modalBg">
  <div class="modal">
    <div class="modal-head">
      <button class="modal-close" onclick="closeModal()">✕</button>
      <div class="modal-avatar" id="mAvatar">?</div>
      <div>
        <h3 id="mName" style="margin:0; font-size:20px;">Player name</h3>
        <div class="mono" id="mSub" style="color:var(--net); font-size:12.5px; margin-top:4px;"></div>
      </div>
    </div>
    <div class="modal-body">
      <div class="modal-section">
        <h4>Match breakdown</h4>
        <div class="match-breakdown" id="mBreakdown"></div>
      </div>
      <div class="modal-section">
        <h4>Performance</h4>
        <div id="mStats"></div>
      </div>
      <div class="modal-section">
        <h4>Weekly availability</h4>
        <div class="mini-avail" id="mAvail"></div>
      </div>
      <div class="modal-section">
        <h4>Reputation</h4>
        <div id="mRep"></div>
      </div>
      <div class="modal-actions">
        <button class="btn btn-primary" onclick="requestGame()">Request a game</button>
        <button class="btn btn-ghost" onclick="messagePlayer()">Message in-app</button>
      </div>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
/* ---------------- constants ---------------- */
const DAYS = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
const SLOTS = ['Morning','Afternoon','Night'];
const POS_GROUP = {PG:'guard', SG:'guard', SF:'wing', PF:'big', C:'big', Flex:'wing'};
const NAGA = {lat:13.6218, lng:123.1948};

function haversine(lat1,lng1,lat2,lng2){
  const R=6371, toRad=d=>d*Math.PI/180;
  const dLat=toRad(lat2-lat1), dLng=toRad(lng2-lng1);
  const a=Math.sin(dLat/2)**2 + Math.cos(toRad(lat1))*Math.cos(toRad(lat2))*Math.sin(dLng/2)**2;
  return R*2*Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
}
function emptyAvail(){
  const m = {};
  DAYS.forEach(d=>{ m[d]={}; SLOTS.forEach(s=> m[d][s]=false); });
  return m;
}
function randAvail(density){
  const m = emptyAvail();
  DAYS.forEach(d=> SLOTS.forEach(s=>{ m[d][s] = Math.random() < density; }));
  return m;
}
function initials(name){
  return name.split(' ').map(w=>w[0]).slice(0,2).join('').toUpperCase();
}

/* ---------------- seed players ---------------- */
const NAMES = ['Marco Villanueva','Jake Ortega','Ren Bautista','Miko Salazar','Diego Fernandez',
  'Paolo Reyes','Kean Abueva','Josh Nierras','Ali Ramos','Cess Molina','Trisha Gomez','Bea Santos',
  'Nico Panganiban','Ivan Cerezo','Sam Villareal'];
const CITY_SPOTS = [
  {name:'Naga City Sports Complex', lat:13.6182, lng:123.1935},
  {name:'Barlin St. Court', lat:13.6255, lng:123.1889},
  {name:'Concepcion Pequeña Court', lat:13.6291, lng:123.1971},
  {name:'Pacol Barangay Court', lat:13.6495, lng:123.2154},
  {name:'Panicuason Court', lat:13.6663, lng:123.2601},
  {name:'Camaligan Gym', lat:13.6420, lng:123.2081},
  {name:'Canaman Court', lat:13.6625, lng:123.1848},
  {name:'Pili Gym', lat:13.5730, lng:123.2782},
];
const TAGS = ['Shooter','Defender','Playmaker','Slasher','Rebounder'];
const POSITIONS = ['PG','SG','SF','PF','C','Flex'];
const TIERS = ['Beginner','Intermediate','Advanced','Competitive'];

function seedPlayer(i){
  const name = NAMES[i];
  const spot = CITY_SPOTS[i % CITY_SPOTS.length];
  const jitterLat = (Math.random()-0.5)*0.02;
  const jitterLng = (Math.random()-0.5)*0.02;
  const pos = POSITIONS[i % POSITIONS.length];
  const rating = 25 + Math.floor(Math.random()*70);
  const tier = rating<40?'Beginner':rating<60?'Intermediate':rating<82?'Advanced':'Competitive';
  const tagCount = 1 + Math.floor(Math.random()*2);
  const shuffled = [...TAGS].sort(()=>Math.random()-0.5);
  return {
    id:i,
    name, age: 18+Math.floor(Math.random()*20),
    gender: Math.random()>0.85?'Female':'Male',
    position: pos,
    height: 165 + Math.floor(Math.random()*30),
    weight: 60 + Math.floor(Math.random()*30),
    wingspan: 170 + Math.floor(Math.random()*32),
    rating, tier,
    ppg: Math.round(Math.random()*22*10)/10,
    apg: Math.round(Math.random()*8*10)/10,
    rpg: Math.round(Math.random()*10*10)/10,
    spg: Math.round(Math.random()*4*10)/10,
    winRate: 35 + Math.floor(Math.random()*55),
    availability: randAvail(0.28 + Math.random()*0.3),
    duration: ['30–45 min','1 hour','1.5 hours','2+ hours'][Math.floor(Math.random()*4)],
    lat: spot.lat+jitterLat, lng: spot.lng+jitterLng,
    court: spot.name,
    maxTravel: 5 + Math.floor(Math.random()*20),
    tags: shuffled.slice(0,tagCount),
    intensity: ['Casual','Competitive','Either'][Math.floor(Math.random()*3)],
    squad: ['Open to randoms','With friends','Either'][Math.floor(Math.random()*3)],
    visibility: 'Public',
    starRating: Math.round((3 + Math.random()*2)*10)/10,
    sportsmanship: 55 + Math.floor(Math.random()*45),
    noShows: Math.floor(Math.random()*3),
    gamesPlayed: 8 + Math.floor(Math.random()*60),
  };
}
const PLAYERS = NAMES.map((_,i)=>seedPlayer(i));

/* ---------------- my profile (in-memory) ---------------- */
let myProfile = {
  name:'', age:null, gender:'', contact:'',
  height:null, weight:null, wingspan:null,
  position:null, tier:null, rating:50,
  ppg:0, apg:0, rpg:0, spg:0, winRate:0,
  availability: emptyAvail(), duration:'1 hour',
  location:'Naga City', court:'', maxTravel:10,
  lat: NAGA.lat, lng: NAGA.lng,
  tags:[], intensity:'Either', squad:'Open to randoms', visibility:'Public',
  saved:false,
};

/* ---------------- nav ---------------- */
function goTo(page){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page-'+page).classList.add('active');
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.toggle('active', b.dataset.page===page));
  document.getElementById('tabs').classList.remove('mobile-open');
  window.scrollTo({top:0, behavior:'smooth'});
  if(page==='find') renderPlayerGrid();
  if(page==='leaderboard') renderLeaderboard();
}
document.querySelectorAll('.tab-btn').forEach(b=> b.addEventListener('click', ()=>goTo(b.dataset.page)));
document.getElementById('menuBtn').addEventListener('click', ()=> document.getElementById('tabs').classList.toggle('mobile-open'));

/* ---------------- toast ---------------- */
let toastTimer;
function showToast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(()=> t.classList.remove('show'), 2600);
}

/* ---------------- profile form wiring ---------------- */
document.querySelectorAll('.pos-opt').forEach(el=>{
  el.addEventListener('click', ()=>{
    document.querySelectorAll('.pos-opt').forEach(o=>o.classList.remove('selected'));
    el.classList.add('selected');
    myProfile.position = el.dataset.pos;
    updatePreview();
  });
});
document.querySelectorAll('#skillTier .chip-opt').forEach(el=>{
  el.addEventListener('click', ()=>{
    document.querySelectorAll('#skillTier .chip-opt').forEach(o=>o.classList.remove('selected'));
    el.classList.add('selected');
    myProfile.tier = el.dataset.tier;
    updatePreview();
  });
});
document.querySelectorAll('#styleTags .chip-opt').forEach(el=>{
  el.addEventListener('click', ()=>{
    const tag = el.dataset.tag;
    const idx = myProfile.tags.indexOf(tag);
    if(idx>-1){ myProfile.tags.splice(idx,1); el.classList.remove('selected'); }
    else{
      if(myProfile.tags.length>=3){ showToast('You can pick up to 3 style tags'); return; }
      myProfile.tags.push(tag); el.classList.add('selected');
    }
    updatePreview();
  });
});
document.getElementById('f-rating').addEventListener('input', e=>{
  myProfile.rating = +e.target.value;
  document.getElementById('ratingVal').textContent = e.target.value;
  updatePreview();
});
document.getElementById('f-distance').addEventListener('input', e=>{
  document.getElementById('distVal').textContent = e.target.value+' km';
});

/* availability grid build */
const availGrid = document.getElementById('availGrid');
SLOTS.forEach(slot=>{
  const rowLabel = document.createElement('div');
  rowLabel.className='rowlabel'; rowLabel.textContent = slot;
  availGrid.appendChild(rowLabel);
  DAYS.forEach(day=>{
    const cell = document.createElement('div');
    cell.className='avail-cell';
    cell.dataset.day=day; cell.dataset.slot=slot;
    cell.addEventListener('click', ()=>{
      myProfile.availability[day][slot] = !myProfile.availability[day][slot];
      cell.classList.toggle('on');
    });
    availGrid.appendChild(cell);
  });
});

function useGeolocation(){
  if(!navigator.geolocation){ showToast('Geolocation not supported on this device'); return; }
  navigator.geolocation.getCurrentPosition(
    pos=>{
      myProfile.lat = pos.coords.latitude;
      myProfile.lng = pos.coords.longitude;
      showToast('Location captured — distances will use your GPS position');
    },
    ()=> showToast('Could not get your location — using Naga City center instead')
  );
}

function updatePreview(){
  const name = document.getElementById('f-name').value || 'Your name';
  document.getElementById('prevInitials').textContent = name==='Your name' ? '?' : initials(name);
  document.getElementById('prevName').textContent = name;
  document.getElementById('prevMeta').textContent = (myProfile.position||'No position') + ' · ' + (document.getElementById('f-location').value || 'Naga City');
  document.getElementById('prevRatingLbl').textContent = myProfile.rating + ' / 100';
  document.getElementById('prevRatingBar').style.width = myProfile.rating+'%';
  const tagsEl = document.getElementById('prevTags');
  tagsEl.innerHTML = myProfile.tags.length ? myProfile.tags.map(t=>`<span class="tag-chip">${t}</span>`).join('') : '<span class="tag-chip">None yet</span>';
}
document.getElementById('f-name').addEventListener('input', updatePreview);
document.getElementById('f-location').addEventListener('input', updatePreview);

function saveProfile(){
  myProfile.name = document.getElementById('f-name').value || 'Anonymous Baller';
  myProfile.age = +document.getElementById('f-age').value || null;
  myProfile.gender = document.getElementById('f-gender').value;
  myProfile.contact = document.getElementById('f-contact').value;
  myProfile.height = +document.getElementById('f-height').value || null;
  myProfile.weight = +document.getElementById('f-weight').value || null;
  myProfile.wingspan = +document.getElementById('f-wingspan').value || null;
  myProfile.ppg = +document.getElementById('f-ppg').value || 0;
  myProfile.apg = +document.getElementById('f-apg').value || 0;
  myProfile.rpg = +document.getElementById('f-rpg').value || 0;
  myProfile.spg = +document.getElementById('f-spg').value || 0;
  myProfile.winRate = +document.getElementById('f-winrate').value || 0;
  myProfile.duration = document.getElementById('f-duration').value;
  myProfile.location = document.getElementById('f-location').value || 'Naga City';
  myProfile.court = document.getElementById('f-court').value;
  myProfile.maxTravel = +document.getElementById('f-distance').value;
  myProfile.intensity = document.getElementById('f-intensity').value;
  myProfile.squad = document.getElementById('f-squad').value;
  myProfile.visibility = document.getElementById('f-visibility').value;

  if(!myProfile.position){ showToast('Pick a position before saving'); return; }
  if(!myProfile.tier){ showToast('Pick a skill tier before saving'); return; }

  myProfile.saved = true;
  updatePreview();
  showToast('Roster card saved — check Find a Run for your matches');
  updateHomeStats();
}
function resetProfile(){
  document.querySelectorAll('input[type=text],input[type=number]').forEach(i=>i.value='');
  document.querySelectorAll('.pos-opt, #skillTier .chip-opt, #styleTags .chip-opt, .avail-cell').forEach(el=>el.classList.remove('selected','on'));
  document.getElementById('f-rating').value=50;
  document.getElementById('ratingVal').textContent='50';
  document.getElementById('f-distance').value=10;
  document.getElementById('distVal').textContent='10 km';
  myProfile = {...myProfile, name:'', age:null, gender:'', contact:'', height:null, weight:null, wingspan:null,
    position:null, tier:null, rating:50, ppg:0, apg:0, rpg:0, spg:0, winRate:0,
    availability: emptyAvail(), duration:'1 hour', location:'Naga City', court:'', maxTravel:10,
    lat:NAGA.lat, lng:NAGA.lng, tags:[], intensity:'Either', squad:'Open to randoms', visibility:'Public', saved:false};
  updatePreview();
  showToast('Roster card reset');
}

/* ---------------- matching engine ---------------- */
function skillScore(a,b){
  const diff = Math.abs(a-b);
  return Math.max(0, 100 - diff*1.6);
}
function positionScore(myPos, theirPos){
  if(myPos==='Flex' || theirPos==='Flex') return 75;
  if(myPos===theirPos) return 40; // same position = less needed
  return POS_GROUP[myPos]===POS_GROUP[theirPos] ? 70 : 100; // complementary group = best
}
function distanceScore(km, maxTravel){
  if(km > maxTravel) return 0;
  return Math.max(0, 100 - (km/maxTravel)*100);
}
function availabilityScore(myAvail, theirAvail){
  let shared=0, mySlots=0;
  DAYS.forEach(d=> SLOTS.forEach(s=>{
    if(myAvail[d][s]) mySlots++;
    if(myAvail[d][s] && theirAvail[d][s]) shared++;
  }));
  if(mySlots===0) return 50; // no data yet, neutral
  return Math.min(100, (shared/mySlots)*100 + (shared>0?20:0));
}
function styleScore(myTags, theirTags){
  if(!myTags.length || !theirTags.length) return 60;
  const overlap = myTags.filter(t=>theirTags.includes(t)).length;
  const distinct = theirTags.length - overlap;
  return Math.min(100, 50 + distinct*25 - overlap*10);
}
function computeMatch(player){
  const dist = haversine(myProfile.lat, myProfile.lng, player.lat, player.lng);
  const sSkill = skillScore(myProfile.rating, player.rating);
  const sPos = myProfile.position ? positionScore(myProfile.position, player.position) : 60;
  const sDist = distanceScore(dist, myProfile.maxTravel || 10);
  const sAvail = availabilityScore(myProfile.availability, player.availability);
  const sStyle = styleScore(myProfile.tags, player.tags);
  const total = sSkill*0.30 + sPos*0.20 + sDist*0.20 + sAvail*0.15 + sStyle*0.15;
  return {total:Math.round(total), dist:Math.round(dist*10)/10, breakdown:{
    skill:Math.round(sSkill), position:Math.round(sPos), distance:Math.round(sDist), availability:Math.round(sAvail), style:Math.round(sStyle)
  }};
}

/* ---------------- find a run rendering ---------------- */
['filt-skillmin','filt-dist'].forEach(id=>{
  document.getElementById(id).addEventListener('input', e=>{
    if(id==='filt-skillmin') document.getElementById('filtSkillMinVal').textContent = e.target.value;
    if(id==='filt-dist') document.getElementById('filtDistVal').textContent = e.target.value+' km';
    renderPlayerGrid();
  });
});
['filt-pos','filt-tag','filt-intensity','sortBy'].forEach(id=>{
  document.getElementById(id).addEventListener('change', renderPlayerGrid);
});
function resetFilters(){
  document.getElementById('filt-pos').value='Any';
  document.getElementById('filt-skillmin').value=0;
  document.getElementById('filtSkillMinVal').textContent='0';
  document.getElementById('filt-dist').value=15;
  document.getElementById('filtDistVal').textContent='15 km';
  document.getElementById('filt-tag').value='Any';
  document.getElementById('filt-intensity').value='Any';
  renderPlayerGrid();
}

function renderPlayerGrid(){
  const posFilt = document.getElementById('filt-pos').value;
  const skillMin = +document.getElementById('filt-skillmin').value;
  const distMax = +document.getElementById('filt-dist').value;
  const tagFilt = document.getElementById('filt-tag').value;
  const intFilt = document.getElementById('filt-intensity').value;
  const sortBy = document.getElementById('sortBy').value;

  let list = PLAYERS.map(p=>({p, m:computeMatch(p)}));
  list = list.filter(({p,m})=>{
    if(posFilt!=='Any' && p.position!==posFilt) return false;
    if(p.rating < skillMin) return false;
    if(m.dist > distMax) return false;
    if(tagFilt!=='Any' && !p.tags.includes(tagFilt)) return false;
    if(intFilt!=='Any' && p.intensity!=='Either' && p.intensity!==intFilt) return false;
    return true;
  });

  if(sortBy==='match') list.sort((a,b)=>b.m.total-a.m.total);
  if(sortBy==='distance') list.sort((a,b)=>a.m.dist-b.m.dist);
  if(sortBy==='skill') list.sort((a,b)=>b.p.rating-a.p.rating);
  if(sortBy==='rating') list.sort((a,b)=>b.p.starRating-a.p.starRating);

  document.getElementById('resultsCount').textContent = list.length + (list.length===1?' player found':' players found');
  const grid = document.getElementById('playerGrid');

  if(!list.length){
    grid.innerHTML = `<div class="empty-state" style="grid-column:1/-1;"><div class="big">🏀</div>No players match these filters yet.<br>Try widening your distance or lowering the skill floor.</div>`;
    return;
  }

  grid.innerHTML = list.map(({p,m})=>{
    const stars = '★'.repeat(Math.round(p.starRating)) + '☆'.repeat(5-Math.round(p.starRating));
    return `
    <div class="p-card" onclick="openModal(${p.id})">
      <div class="p-card-top">
        <div class="p-avatar">${initials(p.name)}</div>
        <div style="flex:1;">
          <p class="p-name">${p.name}</p>
          <div class="p-sub">${p.position} · ${p.tier} · ${p.rating}/100</div>
        </div>
        <div class="gauge" style="--pct:${m.total};"><div class="gauge-inner mono">${m.total}%</div></div>
      </div>
      <div class="p-tags">${p.tags.map(t=>`<span class="tag-chip">${t}</span>`).join('')}</div>
      <div class="p-foot">
        <span>${m.dist} km away</span>
        <span class="stars">${stars}</span>
      </div>
    </div>`;
  }).join('');
}

/* ---------------- modal ---------------- */
let currentPlayerId = null;
function openModal(id){
  currentPlayerId = id;
  const p = PLAYERS.find(x=>x.id===id);
  const m = computeMatch(p);
  document.getElementById('mAvatar').textContent = initials(p.name);
  document.getElementById('mName').textContent = p.name;
  document.getElementById('mSub').textContent = `${p.position} · ${p.tier} · ${p.height}cm · ${m.dist} km away · ${p.court}`;

  const bd = m.breakdown;
  document.getElementById('mBreakdown').innerHTML = `
    <div class="mb-row"><span>Skill fit (30%)</span><div class="stat-track"><span style="width:${bd.skill}%"></span></div><span class="mono">${bd.skill}</span></div>
    <div class="mb-row"><span>Position (20%)</span><div class="stat-track"><span style="width:${bd.position}%"></span></div><span class="mono">${bd.position}</span></div>
    <div class="mb-row"><span>Distance (20%)</span><div class="stat-track"><span style="width:${bd.distance}%"></span></div><span class="mono">${bd.distance}</span></div>
    <div class="mb-row"><span>Schedule (15%)</span><div class="stat-track"><span style="width:${bd.availability}%"></span></div><span class="mono">${bd.availability}</span></div>
    <div class="mb-row"><span>Style balance (15%)</span><div class="stat-track"><span style="width:${bd.style}%"></span></div><span class="mono">${bd.style}</span></div>
  `;

  const maxStat = {ppg:25, apg:10, rpg:12, spg:5};
  document.getElementById('mStats').innerHTML = `
    <div class="stat-row"><span class="stat-lbl">PPG</span><div class="stat-track"><span style="width:${Math.min(100,p.ppg/maxStat.ppg*100)}%"></span></div><span class="stat-num mono">${p.ppg}</span></div>
    <div class="stat-row"><span class="stat-lbl">APG</span><div class="stat-track"><span style="width:${Math.min(100,p.apg/maxStat.apg*100)}%"></span></div><span class="stat-num mono">${p.apg}</span></div>
    <div class="stat-row"><span class="stat-lbl">RPG</span><div class="stat-track"><span style="width:${Math.min(100,p.rpg/maxStat.rpg*100)}%"></span></div><span class="stat-num mono">${p.rpg}</span></div>
    <div class="stat-row"><span class="stat-lbl">STL+BLK</span><div class="stat-track"><span style="width:${Math.min(100,p.spg/maxStat.spg*100)}%"></span></div><span class="stat-num mono">${p.spg}</span></div>
    <div class="stat-row"><span class="stat-lbl">Win %</span><div class="stat-track"><span style="width:${p.winRate}%"></span></div><span class="stat-num mono">${p.winRate}</span></div>
  `;

  const availHtml = DAYS.map(d=>{
    const cells = SLOTS.map(s=>`<div class="cell ${p.availability[d][s]?'on':''}" title="${d} ${s}"></div>`).join('');
    return `<div><div class="day">${d}</div>${cells}</div>`;
  }).join('');
  document.getElementById('mAvail').innerHTML = availHtml;

  const stars = '★'.repeat(Math.round(p.starRating)) + '☆'.repeat(5-Math.round(p.starRating));
  document.getElementById('mRep').innerHTML = `
    <div class="rep-row"><span>Player rating</span><span class="stars">${stars} (${p.starRating})</span></div>
    <div class="rep-row"><span>Sportsmanship score</span><span class="mono">${p.sportsmanship}/100</span></div>
    <div class="rep-row"><span>No-show history</span><span class="mono">${p.noShows} of ${p.gamesPlayed} games</span></div>
    <div class="rep-row"><span>Squad type</span><span>${p.squad}</span></div>
    <div class="rep-row"><span>Game visibility</span><span>${p.visibility}</span></div>
  `;

  document.getElementById('modalBg').classList.add('open');
}
function closeModal(){ document.getElementById('modalBg').classList.remove('open'); }
document.getElementById('modalBg').addEventListener('click', e=>{ if(e.target.id==='modalBg') closeModal(); });
function requestGame(){
  const p = PLAYERS.find(x=>x.id===currentPlayerId);
  showToast(`Game request sent to ${p.name}`);
  closeModal();
}
function messagePlayer(){
  showToast('In-app messaging opens here');
}

/* ---------------- leaderboard ---------------- */
function renderLeaderboard(){
  const ranked = [...PLAYERS].sort((a,b)=> (b.starRating - a.starRating) || (b.winRate - a.winRate));
  document.getElementById('leaderboardBody').innerHTML = ranked.map((p,i)=>{
    const stars = '★'.repeat(Math.round(p.starRating)) + '☆'.repeat(5-Math.round(p.starRating));
    return `
    <tr>
      <td><span class="rank-badge ${i<3?'top':''}">${i+1}</span></td>
      <td><div class="lb-name-cell"><div class="lb-avatar">${initials(p.name)}</div>${p.name}</div></td>
      <td>${p.position}</td>
      <td class="hide-sm mono">${p.rating}</td>
      <td class="stars">${stars}</td>
      <td class="hide-sm mono">${p.winRate}%</td>
      <td class="hide-sm mono">${p.sportsmanship}/100</td>
    </tr>`;
  }).join('');
}

/* ---------------- home stats ---------------- */
function updateHomeStats(){
  const near = PLAYERS.filter(p=> haversine(myProfile.lat,myProfile.lng,p.lat,p.lng) <= 15).length;
  document.getElementById('statPlayers').textContent = near;
  document.getElementById('statRuns').textContent = 6 + Math.floor(PLAYERS.length/3);
  const avg = Math.round(PLAYERS.reduce((s,p)=>s+computeMatch(p).total,0)/PLAYERS.length);
  document.getElementById('statScore').textContent = avg+'%';
}

/* ---------------- init ---------------- */
updatePreview();
updateHomeStats();
renderPlayerGrid();
renderLeaderboard();
</script>
</body>
</html>
