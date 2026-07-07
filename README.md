<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover" />
<title>Night Patrol — Cops &amp; Robbers (Driving)</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Oswald:wght@400;500;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0c0e13; --panel:rgba(18,22,32,0.82); --ink:#eef2f7; --muted:#8a93a6;
    --blue:#2f6bff; --red:#ff3b4e; --amber:#ffb020; --line:rgba(255,255,255,0.08);
    --display:"Oswald","Arial Narrow",system-ui,sans-serif;
    --mono:"Share Tech Mono",ui-monospace,monospace;
  }
  *{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent;}
  html,body{height:100%;overflow:hidden;background:var(--bg);color:var(--ink);font-family:var(--display);
    -webkit-user-select:none;user-select:none;touch-action:none;}
  #game{position:fixed;inset:0;}
  canvas{display:block;}

  .screen{position:fixed;inset:0;display:flex;align-items:flex-start;justify-content:center;z-index:20;
    overflow-y:auto;-webkit-overflow-scrolling:touch;
    padding:24px;text-align:center;background:radial-gradient(120% 90% at 50% 30%, rgba(20,26,40,0.55), rgba(7,8,12,0.94));}
  .hidden{display:none !important;}
  .siren{position:absolute;top:0;left:0;right:0;height:5px;
    background:linear-gradient(90deg,var(--blue),var(--blue) 50%,var(--red) 50%,var(--red));
    background-size:200% 100%;animation:siren 1.1s linear infinite;}
  @keyframes siren{from{background-position:0 0;}to{background-position:200% 0;}}
  .card{width:min(580px,100%);margin:auto 0;}
  #builder .card{margin:16px auto;}
  .eyebrow{font-size:13px;letter-spacing:.42em;color:var(--muted);text-transform:uppercase;margin-bottom:10px;}
  h1{font-weight:700;font-size:clamp(38px,8.5vw,72px);line-height:.92;letter-spacing:.02em;text-transform:uppercase;}
  h1 .amp{color:var(--amber);font-weight:400;}
  .tagline{color:var(--muted);margin:14px auto 24px;max-width:44ch;font-size:15px;line-height:1.5;}
  .roles{display:flex;gap:14px;justify-content:center;margin-bottom:20px;flex-wrap:wrap;}
  .role{flex:1 1 200px;min-width:180px;cursor:pointer;border:1px solid var(--line);background:var(--panel);
    border-radius:14px;padding:18px 16px;transition:transform .12s,border-color .12s,box-shadow .12s;}
  .role:hover{transform:translateY(-3px);}
  .role.sel.cop{border-color:var(--blue);box-shadow:0 0 0 1px var(--blue),0 12px 40px rgba(47,107,255,.25);}
  .role.sel.rob{border-color:var(--red);box-shadow:0 0 0 1px var(--red),0 12px 40px rgba(255,59,78,.25);}
  .role .tag{font-size:12px;letter-spacing:.3em;text-transform:uppercase;color:var(--muted);}
  .role .name{font-size:28px;font-weight:700;text-transform:uppercase;margin:6px 0 8px;}
  .role.cop .name{color:var(--blue);} .role.rob .name{color:var(--red);}
  .role .goal{font-size:13px;color:var(--muted);line-height:1.45;}
  .diff{display:flex;gap:8px;justify-content:center;margin-bottom:22px;}
  .diff button{font-family:var(--display);font-size:13px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);
    background:transparent;border:1px solid var(--line);border-radius:999px;padding:8px 16px;cursor:pointer;transition:.12s;}
  .diff button.sel{color:var(--ink);border-color:var(--ink);background:rgba(255,255,255,.06);}
  .row-label{font-size:11px;letter-spacing:.3em;text-transform:uppercase;color:var(--muted);margin:2px 0 8px;}
  .mapsel{display:flex;gap:8px;justify-content:center;margin-bottom:22px;flex-wrap:wrap;}
  .mapsel button{font-family:var(--display);font-size:13px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);
    background:transparent;border:1px solid var(--line);border-radius:999px;padding:8px 18px;cursor:pointer;transition:.12s;}
  .mapsel button.sel{color:var(--ink);border-color:var(--ink);background:rgba(255,255,255,.06);}
  .gadgetsel{display:flex;gap:8px;justify-content:center;margin-bottom:22px;}
  .gadgetsel button{font-family:var(--display);font-size:12px;letter-spacing:.16em;text-transform:uppercase;color:var(--muted);
    background:transparent;border:1px solid var(--line);border-radius:10px;padding:9px 12px;cursor:pointer;transition:all .15s;}
  .gadgetsel button.sel{color:var(--ink);border-color:var(--ink);background:rgba(255,255,255,.06);}
  .play{font-family:var(--display);text-transform:uppercase;letter-spacing:.18em;font-weight:700;font-size:18px;
    color:#0a0c10;background:var(--ink);border:none;border-radius:12px;padding:16px 40px;cursor:pointer;transition:transform .1s,filter .12s;}
  .play:hover{transform:translateY(-2px);filter:brightness(1.05);}
  .hint-keys{margin-top:16px;color:var(--muted);font-size:12px;letter-spacing:.03em;line-height:1.8;}
  kbd{font-family:var(--mono);background:rgba(255,255,255,.07);border:1px solid var(--line);border-radius:5px;padding:1px 7px;color:var(--ink);font-size:12px;}

  #hud{position:fixed;inset:0;z-index:10;pointer-events:none;}
  #getOutBtn,#getInBtn{pointer-events:auto;}
  .topbar{position:absolute;top:0;left:0;right:0;display:flex;align-items:center;justify-content:space-between;gap:14px;
    padding:12px 16px;background:linear-gradient(180deg,rgba(7,8,12,.78),transparent);}
  .role-chip{display:flex;align-items:center;gap:9px;font-size:13px;letter-spacing:.22em;text-transform:uppercase;}
  .dot{width:11px;height:11px;border-radius:50%;}
  .dot.cop{background:var(--blue);box-shadow:0 0 12px var(--blue);}
  .dot.rob{background:var(--red);box-shadow:0 0 12px var(--red);}
  .timer{font-family:var(--mono);font-size:clamp(28px,6.5vw,44px);letter-spacing:.04em;line-height:1;text-shadow:0 2px 18px rgba(0,0,0,.6);}
  .timer.warn{color:var(--amber);} .timer.crit{color:var(--red);animation:blink .7s steps(2) infinite;}
  @keyframes blink{50%{opacity:.45;}}
  .objective{font-size:11px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);margin-top:4px;text-align:center;}
  .right-stack{display:flex;align-items:center;gap:10px;}
  .score{font-family:var(--mono);font-size:18px;color:var(--amber);}
  .iconbtn{pointer-events:auto;font-family:var(--display);font-size:12px;letter-spacing:.12em;color:var(--muted);
    background:var(--panel);border:1px solid var(--line);border-radius:9px;padding:8px 11px;cursor:pointer;text-transform:uppercase;}
  .botcenter{position:absolute;left:50%;bottom:18px;transform:translateX(-50%);text-align:center;width:min(340px,64vw);}
  .speedo{font-family:var(--mono);font-size:20px;color:var(--ink);margin-bottom:6px;}
  #tach{display:block;margin:0 auto -4px;}
  .drift{font-family:var(--display);font-weight:700;letter-spacing:.1em;text-transform:uppercase;color:var(--amber);
    font-size:18px;height:22px;opacity:0;transition:opacity .1s;text-shadow:0 0 14px rgba(255,176,32,.6);}
  .drift.on{opacity:1;}
  .speedo small{color:var(--muted);font-size:11px;letter-spacing:.2em;}
  .boost-wrap{background:rgba(0,0,0,.35);border:1px solid var(--line);border-radius:999px;padding:4px;}
  .boost{height:9px;border-radius:999px;background:linear-gradient(90deg,#2f6bff,#7fd8ff);width:100%;transition:width .08s linear;}
  .boost.low{background:linear-gradient(90deg,#5a6072,#7c8499);}
  .boost-label{font-size:10px;letter-spacing:.3em;color:var(--muted);text-transform:uppercase;margin-bottom:3px;}
  .breaker{margin-top:8px;font-size:12px;letter-spacing:.18em;text-transform:uppercase;font-family:var(--display);
    border:1px solid var(--line);border-radius:999px;padding:6px 10px;transition:.15s;}
  .breaker.ready{color:#062018;background:linear-gradient(90deg,#7fe0ff,#9be15d);border-color:transparent;
    box-shadow:0 0 18px rgba(127,224,255,.5);animation:cbpulse 1.1s ease-in-out infinite;}
  .breaker.cooling{color:var(--muted);background:rgba(0,0,0,.35);}
  @keyframes cbpulse{50%{box-shadow:0 0 28px rgba(127,224,255,.85);}}
  #flash{position:fixed;inset:0;z-index:15;pointer-events:none;opacity:0;
    background:radial-gradient(circle at 50% 60%, rgba(127,224,255,.55), rgba(127,224,255,0) 65%);}
  #flash.on{animation:cbflash .5s ease-out;}
  @keyframes cbflash{0%{opacity:.85;}100%{opacity:0;}}
  .bigtext{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;
    font-family:var(--display);font-weight:800;font-size:min(16vw,150px);letter-spacing:.04em;
    opacity:0;pointer-events:none;z-index:40;text-transform:uppercase;}
  .bigtext.show{animation:bigpop 1.8s ease-out forwards;}
  .bigtext.busted{color:#ff3b4e;text-shadow:0 0 42px rgba(255,59,78,.7);}
  .bigtext.escaped{color:#9be15d;text-shadow:0 0 42px rgba(155,225,93,.7);}
  @keyframes bigpop{
    0%{opacity:0;transform:scale(1.6) rotate(-4deg);}
    14%{opacity:1;transform:scale(1) rotate(-2deg);}
    80%{opacity:1;transform:scale(1.02) rotate(-2deg);}
    100%{opacity:0;transform:scale(1.08) rotate(-2deg);}
  }
  #breakerBtn{position:absolute;right:30px;bottom:150px;width:96px;height:54px;border-radius:12px;
    background:rgba(127,224,255,.16);border:1px solid #7fe0ff;color:var(--ink);pointer-events:auto;
    font-family:var(--display);text-transform:uppercase;letter-spacing:.08em;font-size:12px;}
  #breakerBtn:disabled{opacity:.45;border-color:var(--line);background:rgba(0,0,0,.3);}
  #minimap{position:absolute;right:14px;bottom:14px;border:1px solid var(--line);border-radius:10px;background:rgba(8,10,16,.7);box-shadow:0 8px 30px rgba(0,0,0,.4);}
  .alert{position:absolute;top:92px;left:50%;transform:translateX(-50%);font-size:13px;letter-spacing:.3em;text-transform:uppercase;
    color:var(--red);opacity:0;transition:opacity .25s;text-shadow:0 0 14px rgba(255,59,78,.6);}
  .alert.show{opacity:1;}
  .health-box{position:absolute;top:80px;left:50%;transform:translateX(-50%);width:min(260px,54vw);text-align:center;}
  .health-label{font-size:10px;letter-spacing:.3em;text-transform:uppercase;color:var(--muted);margin-bottom:3px;}
  .health-wrap{background:rgba(0,0,0,.4);border:1px solid var(--line);border-radius:999px;padding:3px;}
  .health{height:8px;border-radius:999px;background:linear-gradient(90deg,#ff3b4e,#ff9f5c);width:100%;transition:width .12s linear;}
  .health.crit{background:linear-gradient(90deg,#ff3b4e,#ffd23b);animation:hpblink .5s steps(2) infinite;}
  @keyframes hpblink{50%{opacity:.5;}}
  .heat-box{position:absolute;top:122px;left:50%;transform:translateX(-50%);width:min(260px,54vw);text-align:center;}
  .streak-label{margin-top:5px;font-family:var(--display);letter-spacing:.14em;font-size:13px;font-weight:700;color:#ffd24a;text-shadow:0 0 8px rgba(255,190,40,.6);}
  .streak-label.pop{animation:streakPop .5s ease;}
  @keyframes streakPop{0%{transform:scale(1.5);} 100%{transform:scale(1);}}
  .heatbar{height:8px;border-radius:999px;background:linear-gradient(90deg,#ffb020,#ff5a2a);width:0%;transition:width .2s linear;}

  #touch{position:fixed;inset:0;z-index:12;pointer-events:none;display:none;}
  .stick{position:absolute;bottom:26px;width:132px;height:132px;border-radius:50%;background:rgba(255,255,255,.05);
    border:1px solid var(--line);pointer-events:auto;}
  #moveStick{left:20px;} #lookStick{right:20px;}
  .knob{position:absolute;left:36px;top:36px;width:60px;height:60px;border-radius:50%;background:rgba(238,242,247,.22);border:1px solid rgba(255,255,255,.25);}
  .sticklbl{position:absolute;left:0;right:0;bottom:-20px;text-align:center;font-size:10px;letter-spacing:.25em;color:var(--muted);}
  #boostBtn{position:absolute;right:170px;bottom:60px;width:78px;height:78px;border-radius:50%;background:rgba(47,107,255,.16);
    border:1px solid var(--blue);color:var(--ink);pointer-events:auto;font-family:var(--display);text-transform:uppercase;letter-spacing:.1em;font-size:12px;}
  #recenterBtn{position:absolute;right:170px;bottom:150px;width:70px;height:42px;border-radius:10px;background:var(--panel);
    border:1px solid var(--line);color:var(--muted);pointer-events:auto;font-family:var(--display);text-transform:uppercase;letter-spacing:.08em;font-size:11px;}

  .verdict{font-size:clamp(46px,12vw,104px);font-weight:700;text-transform:uppercase;line-height:.9;}
  .verdict.win{color:#9be15d;} .verdict.lose{color:var(--red);}
  .sub{color:var(--muted);margin:14px 0 28px;font-size:16px;}

  /* ---------- Garage ---------- */
  .menu-btns{display:flex;gap:10px;justify-content:center;flex-wrap:wrap;}
  .play.ghost{background:transparent;color:var(--ink);border:1px solid var(--line);}
  .play.ghost:hover{border-color:var(--ink);}
  .cust-card{width:min(560px,100%);}
  .role-toggle{display:inline-flex;gap:6px;margin:6px 0 14px;border:1px solid var(--line);border-radius:999px;padding:4px;}
  .role-toggle button{font-family:var(--display);text-transform:uppercase;letter-spacing:.18em;font-size:13px;color:var(--muted);
    background:transparent;border:none;border-radius:999px;padding:8px 22px;cursor:pointer;transition:.12s;}
  .role-toggle button.sel[data-role="cop"]{background:rgba(47,107,255,.22);color:#fff;}
  .role-toggle button.sel[data-role="robber"]{background:rgba(255,59,78,.22);color:#fff;}
  #carPreview{width:100%;height:230px;display:block;border:1px solid var(--line);border-radius:14px;
    background:radial-gradient(120% 120% at 50% 18%, #1b2336, #0a0d14);margin-bottom:14px;}
  .cust-row{display:flex;align-items:center;gap:12px;margin:9px 0;}
  .cust-label{flex:0 0 60px;text-align:right;font-size:11px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);}
  .swatches{display:flex;gap:8px;flex-wrap:wrap;flex:1;}
  .swatch{width:26px;height:26px;border-radius:8px;cursor:pointer;border:2px solid transparent;
    box-shadow:inset 0 0 0 1px rgba(255,255,255,.14);transition:transform .1s,border-color .1s;}
  .swatch:hover{transform:scale(1.1);}
  .swatch.sel{border-color:var(--ink);transform:scale(1.1);}
  .swatch.locked{opacity:.32;filter:grayscale(.5);}
  .seg button.locked{opacity:.5;}
  .bankline{font-family:var(--display);font-size:13px;letter-spacing:.14em;text-transform:uppercase;color:var(--muted);margin:0 0 14px;}
  .bankline span{color:var(--amber);font-weight:700;}
  .bankline span.flash{animation:bankflash .5s ease-out;}
  @keyframes bankflash{0%{color:#ff3b4e;}100%{color:var(--amber);}}
  .menustats{font-family:var(--mono);font-size:12px;color:var(--muted);margin:-6px 0 18px;}
  .seg{display:flex;gap:6px;flex:1;flex-wrap:wrap;}
  .seg button{font-family:var(--display);text-transform:uppercase;letter-spacing:.1em;font-size:12px;color:var(--muted);
    background:transparent;border:1px solid var(--line);border-radius:8px;padding:7px 12px;cursor:pointer;transition:.12s;}
  .seg button.sel{color:var(--ink);border-color:var(--ink);background:rgba(255,255,255,.06);}
  .up-title{font-size:11px;letter-spacing:.3em;text-transform:uppercase;color:var(--muted);margin:18px 0 8px;text-align:left;}
  .up-row{display:flex;align-items:center;gap:10px;padding:9px 0;border-top:1px solid var(--line);}
  .up-label{flex:1;text-align:left;display:flex;flex-direction:column;gap:2px;}
  .up-name{font-size:14px;font-weight:600;color:var(--ink);text-transform:uppercase;letter-spacing:.05em;}
  .up-desc{font-size:11px;color:var(--muted);}
  .up-pips{display:flex;gap:4px;}
  .up-pip{width:15px;height:8px;border-radius:3px;background:rgba(255,255,255,.12);}
  .up-pip.on{background:var(--amber);box-shadow:0 0 8px rgba(255,176,32,.5);}
  .up-buy{font-family:var(--mono);font-size:12px;color:var(--ink);background:rgba(255,255,255,.06);border:1px solid var(--line);border-radius:8px;padding:7px 12px;cursor:pointer;min-width:64px;transition:.12s;}
  .up-buy:hover{border-color:var(--ink);}
  .up-buy.maxed{color:var(--amber);border-color:rgba(255,176,32,.4);cursor:default;}
  .set-row{display:flex;flex-direction:column;gap:9px;padding:12px 0;border-top:1px solid var(--line);text-align:left;}
  .set-label{font-size:13px;color:var(--ink);text-transform:uppercase;letter-spacing:.06em;display:flex;justify-content:space-between;align-items:center;}
  .set-label em{font-family:var(--mono);font-style:normal;color:var(--amber);font-size:13px;}
  .set-note{font-size:12px;color:var(--muted);text-align:left;margin:8px 0 2px;}
  input[type=range]{-webkit-appearance:none;appearance:none;width:100%;height:6px;border-radius:999px;background:rgba(255,255,255,.14);outline:none;}
  input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;appearance:none;width:22px;height:22px;border-radius:50%;background:var(--ink);border:3px solid var(--bg);cursor:pointer;box-shadow:0 2px 7px rgba(0,0,0,.45);}
  input[type=range]::-moz-range-thumb{width:20px;height:20px;border-radius:50%;background:var(--ink);border:3px solid var(--bg);cursor:pointer;}
  .style-hint{font-size:12.5px;color:var(--muted);margin:4px 0 16px;min-height:16px;font-style:italic;}
  .palette{display:flex;flex-wrap:wrap;gap:6px;justify-content:center;margin:10px 0;}
  .pal-btn{display:flex;align-items:center;gap:6px;padding:6px 10px;border-radius:10px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.05);color:#dfe6f0;font-size:12px;cursor:pointer;}
  .featpop{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);z-index:60;width:min(86vw,360px);
    background:#121723;border:1px solid rgba(255,255,255,.16);border-radius:16px;padding:14px;box-shadow:0 18px 50px rgba(0,0,0,.6);}
  .featpop-title{font-size:14px;font-weight:700;color:#eaf0f8;margin-bottom:10px;text-align:center;}
  #bEdit.editing{background:#2b6fd6;border-color:#2b6fd6;color:#fff;}
  .palette{perspective:600px;}
  @keyframes palSpin{from{transform:rotateY(0deg);}to{transform:rotateY(360deg);}}
  .pal-btn.spin{animation:palSpin 1.5s linear infinite;}
  .pal-btn.sel{outline:2px solid #2b6fd6;background:rgba(43,111,214,.22);}
  .pal-btn.sel{border-color:#6cf;background:rgba(90,180,255,.18);}
  .pal-btn.empty{opacity:.4;}
  .pal-btn b{color:#9fd0ff;}
  .pal-sw{width:14px;height:14px;border-radius:3px;display:inline-block;}
  #buildCanvas{display:block;margin:8px auto;width:min(86vw,520px);height:min(86vw,520px);image-rendering:pixelated;border-radius:12px;border:1px solid rgba(255,255,255,.16);touch-action:pan-y;background:#0c0f17;cursor:crosshair;}
  .slots{display:flex;flex-wrap:wrap;gap:5px;justify-content:center;margin:8px 0;}
  .slot-btn{padding:6px 0;width:36px;border-radius:8px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.05);color:#8893a3;font-size:12px;cursor:pointer;}
  .slot-btn.used{color:#dfe6f0;border-color:rgba(120,200,255,.45);}
  .slot-btn.sel{border-color:#6cf;background:rgba(90,180,255,.2);color:#fff;}
  .build-actions{display:flex;gap:6px;flex-wrap:wrap;justify-content:center;margin:6px 0;}
  .build-actions .play{padding:9px 12px;font-size:13px;flex:1;min-width:74px;}
</style>
</head>
<body>
<div id="game"></div>
<div id="flash"></div>
<div id="bigtext" class="bigtext"></div>

<div id="hud" class="hidden">
  <div class="topbar">
    <div class="role-chip"><span id="roleDot" class="dot cop"></span><span id="roleText">Cop</span></div>
    <div style="text-align:center">
      <div id="timer" class="timer">5:00</div>
      <div id="objective" class="objective">Catch the robber</div>
    </div>
    <div class="right-stack">
      <span class="score" id="score">$0</span>
      <button class="iconbtn" id="soundBtn">Sound: On</button>
      <button class="iconbtn" id="dmgBtn">Damage: On</button>
      <button class="iconbtn" id="quitBtn">Quit</button>
    </div>
  </div>
  <div id="alert" class="alert">Cop on your tail</div>
  <div class="health-box">
    <div id="healthLabel" class="health-label">Robber Car</div>
    <div class="health-wrap"><div id="health" class="health"></div></div>
  </div>
  <div class="heat-box">
    <div class="health-label">Heat</div>
    <div class="health-wrap"><div id="heat" class="heatbar"></div></div>
    <div id="streakLabel" class="streak-label hidden">Streak x1</div>
  </div>
  <div class="botcenter">
    <canvas id="tach" width="150" height="84"></canvas>
    <div id="drift" class="drift"></div>
    <div class="speedo"><span id="speed">0</span> <small>MPH</small></div>
    <div class="boost-label">Boost</div>
    <div class="boost-wrap"><div id="boost" class="boost"></div></div>
    <div id="breaker" class="breaker ready">Space — Breaker Ready</div>
    <button id="getOutBtn" class="play ghost hidden" style="margin-top:6px">🚪 Get Out</button>
    <button id="getInBtn" class="play hidden" style="margin-top:6px">🚗 Get In</button>
  </div>
  <canvas id="minimap" width="160" height="160"></canvas>
</div>

<div id="touch">
  <div id="moveStick" class="stick"><div class="knob" id="moveKnob"></div><div class="sticklbl">Drive</div></div>
  <div id="lookStick" class="stick"><div class="knob" id="lookKnob"></div><div class="sticklbl">Camera</div></div>
  <button id="boostBtn">Boost</button>
  <button id="breakerBtn">Breaker</button>
  <button id="recenterBtn">Recenter</button>
</div>

<div id="menu" class="screen">
  <div class="siren"></div>
  <div class="card">
    <div class="eyebrow">Night Patrol</div>
    <h1>Cops <span class="amp">&amp;</span> Robbers</h1>
    <p class="tagline">A high-speed chase across the night city. Pick your side, gear, and turf — reach the safehouse or wreck the runner.</p>
    <div class="menustats" id="menuStats"></div>
    <div class="roles">
      <div class="role cop sel" data-role="cop">
        <div class="tag">Choose</div><div class="name">Cop</div>
        <div class="goal">Run the robber down before time runs out. Use boost to close the gap.</div>
      </div>
      <div class="role rob" data-role="robber">
        <div class="tag">Choose</div><div class="name">Robber</div>
        <div class="goal">Outrun the squad for five minutes. Grab cash on the streets for a payout.</div>
      </div>
    </div>
    <div class="row-label">Difficulty</div>
    <div class="diff">
      <button data-diff="easy">Easy</button>
      <button data-diff="normal" class="sel">Normal</button>
      <button data-diff="hard">Hard</button>
    </div>
    <div class="row-label">Mode</div>
    <div class="mapsel" id="modeSel">
      <button data-mode="chase" class="sel">Chase</button>
      <button data-mode="heistrun">Heist Run</button>
      <button data-mode="heist">Heist</button>
      <button data-mode="chopper">Chopper</button>
      <button data-mode="precinct">Precinct</button>
    </div>
    <div class="row-label">Map</div>
    <div class="mapsel">
      <button data-map="world">Mega World</button>
      <button data-map="crossing">The Crossing</button>
      <button data-map="city" class="sel">City</button>
      <button data-map="desert">Desert</button>
      <button data-map="plain">Plain</button>
      <button data-map="rain">Rain City</button>
      <button data-map="ice">Ice</button>
      <button data-map="highway">Highway</button>
      <button data-map="harbor">Harbor</button>
    </div>
    <div class="row-label">Gadget</div>
    <div class="gadgetsel">
      <button data-gadget="nitro" class="sel">Nitro</button>
      <button data-gadget="emp">EMP</button>
      <button data-gadget="smoke">Smoke</button>
      <button data-gadget="spikes">Spikes</button>
    </div>
    <div class="menu-btns">
      <button class="play" id="playBtn">Start Chase</button>
      <button class="play ghost" id="customizeBtn">Customize Car</button>
      <button class="play ghost" id="builderBtn">World Builder</button>
      <button class="play ghost" id="settingsBtn">Settings</button>
    </div>
    <div class="hint-keys">
      <kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd> drive &nbsp;·&nbsp; <kbd>Shift</kbd> boost &nbsp;·&nbsp; <kbd>Space</kbd> gadget<br>
      Drag mouse or <kbd>Q</kbd><kbd>E</kbd> rotate camera &nbsp;·&nbsp; scroll to zoom &nbsp;·&nbsp; <kbd>C</kbd> recenter
    </div>
  </div>
</div>

<div id="customize" class="screen hidden">
  <div class="siren"></div>
  <div class="card cust-card">
    <div class="eyebrow">Garage</div>
    <h1 style="font-size:clamp(30px,6vw,46px)">Customize</h1>
    <div class="role-toggle" id="custRole">
      <button data-role="cop" class="sel">Cop</button>
      <button data-role="robber">Robber</button>
    </div>
    <div class="bankline">Garage Cash <span id="bank">$0</span></div>
    <canvas id="carPreview"></canvas>
    <div class="cust-row"><span class="cust-label">Paint</span><div class="swatches" id="paintSwatches"></div></div>
    <div class="cust-row"><span class="cust-label">Wheels</span><div class="swatches" id="wheelSwatches"></div></div>
    <div class="cust-row"><span class="cust-label">Finish</span><div class="seg" id="finishSeg"></div></div>
    <div class="cust-row"><span class="cust-label">Body</span><div class="seg" id="styleSeg"></div></div>
    <div class="style-hint" id="styleHint">Balanced all-rounder</div>
    <div class="up-title">Performance Upgrades</div>
    <div id="upgradeRows"></div>
    <button class="play" id="custDone">Done</button>
  </div>
</div>

<div id="settings" class="screen hidden">
  <div class="siren"></div>
  <div class="card">
    <div class="eyebrow">Options</div>
    <h1 style="font-size:clamp(30px,6vw,46px)">Settings</h1>
    <div class="set-row"><span class="set-label">Master Volume <em id="volVal">80%</em></span>
      <input type="range" id="volRange" min="0" max="100" value="80"></div>
    <div class="set-row"><span class="set-label">Camera Sensitivity <em id="sensVal">1.0×</em></span>
      <input type="range" id="sensRange" min="30" max="200" value="100"></div>
    <div class="cust-row"><span class="cust-label">Invert Camera Y</span>
      <div class="seg" id="invertSeg"><button data-inv="off" class="sel">Off</button><button data-inv="on">On</button></div></div>
    <div class="cust-row"><span class="cust-label">Graphics</span>
      <div class="seg" id="qualitySeg"><button data-q="low">Low</button><button data-q="medium">Medium</button><button data-q="high" class="sel">High</button></div></div>
    <div class="set-note" id="qualityNote">High — full shadows &amp; resolution</div>
    <button class="play" id="setDone">Done</button>
  </div>
</div>

<div id="builder" class="screen hidden">
  <div class="card" style="max-width:640px;width:95%">
    <div class="eyebrow">Sandbox</div>
    <h1 style="font-size:clamp(26px,5vw,40px)">World Builder</h1>
    <div id="buildTool" class="set-note">Tap a plate type, then tap the map to place it</div>
    <div id="palette" class="palette"></div>
    <div class="build-actions" style="margin-bottom:2px">
      <button id="bEdit" class="play ghost">✎ Edit plates</button>
      <button id="bWZoomOut" class="play ghost">– Zoom</button>
      <button id="bWZoomIn" class="play ghost">Zoom +</button>
      <button id="bBack" class="play ghost hidden">◂ Back to world</button>
      <button id="bRotate" class="play ghost hidden">⟳ Rotate</button>
    </div>
    <canvas id="buildCanvas" width="576" height="576"></canvas>
    <div class="set-note" style="margin-top:4px">Worlds (up to 10) — tap to pick a slot</div>
    <div id="slots" class="slots"></div>
    <div class="set-note" style="margin-top:4px">Start from an existing map, then expand it</div>
    <div class="build-actions">
      <button id="bSeedBlank" class="play ghost">Blank World</button>
      <button id="bSeedCity" class="play ghost">Start: City</button>
      <button id="bSeedMega" class="play ghost">Start: Mega World 2</button>
    </div>
    <div class="build-actions">
      <button id="bSave" class="play ghost">Save</button>
      <button id="bLoad" class="play ghost">Load</button>
      <button id="bClear" class="play ghost">Clear</button>
      <button id="bDelete" class="play ghost">Delete</button>
    </div>
    <button class="play" id="bPlay">▶ Play This World</button>
    <button class="play ghost" id="buildDone">Done</button>
  </div>
</div>

<div id="end" class="screen hidden">
  <div class="siren"></div>
  <div class="card">
    <div id="verdict" class="verdict win">You Win</div>
    <p id="endSub" class="sub"></p>
    <button class="play" id="againBtn">Play Again</button>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){
"use strict";

// ----------------------------------------------------------------------------
//  Config
// ----------------------------------------------------------------------------
var CITY = 80;             // half-size; world spans -80..80  (bigger city)
var WEDGE = 80;            // the real spawn/objective reference edge — usually equals CITY, except on the Mega World where CITY has grown past the original islands
var ROUND_TIME = 600;
var CATCH_DIST = 3.6;
var DMG_DIST = 2.7;

var DIFF = {
  easy:   { cops:2, aiFactor:0.92 },
  normal: { cops:2, aiFactor:0.99 },
  hard:   { cops:3, aiFactor:1.05 }
};

// player car tuning
var P_MAXSPEED = 26, P_ACCEL = 22, P_BRAKE = 40, P_REVERSE = 11;
var TURN_RATE = 2.4, BOOST_MULT = 1.42;

// ----------------------------------------------------------------------------
//  Globals
// ----------------------------------------------------------------------------
var renderer, scene, camera, clock;
var ENV=null;
var TIRE_MAT=new THREE.MeshStandardMaterial({color:0x0a0b0e,roughness:0.85,metalness:0.05});
var aabbs = [], circles = [], buildings = [];
// ---- spatial grid: lets collision checks query only nearby obstacles instead of scanning every one on the whole map ----
var sGrid={}, SG=60, sGridBuilt=-1;
function sKey(cx,cz){ return cx+"_"+cz; }
function buildSpatialIndex(){
  sGrid={};
  for(var i=0;i<aabbs.length;i++){ var b=aabbs[i];
    var cx0=Math.floor(b.minX/SG), cx1=Math.floor(b.maxX/SG), cz0=Math.floor(b.minZ/SG), cz1=Math.floor(b.maxZ/SG);
    for(var cx=cx0;cx<=cx1;cx++) for(var cz=cz0;cz<=cz1;cz++){ var k=sKey(cx,cz); (sGrid[k]=sGrid[k]||{a:[],c:[]}).a.push(i); }
  }
  for(var j=0;j<circles.length;j++){ var c=circles[j];
    var dx0=Math.floor((c.x-c.r)/SG), dx1=Math.floor((c.x+c.r)/SG), dz0=Math.floor((c.z-c.r)/SG), dz1=Math.floor((c.z+c.r)/SG);
    for(var gx=dx0;gx<=dx1;gx++) for(var gz=dz0;gz<=dz1;gz++){ var k2=sKey(gx,gz); (sGrid[k2]=sGrid[k2]||{a:[],c:[]}).c.push(j); }
  }
  sGridBuilt=aabbs.length+circles.length;
}
function sQuery(x,z,r){
  if(sGridBuilt<0 || (aabbs.length+circles.length)-sGridBuilt>40) buildSpatialIndex();
  var cx0=Math.floor((x-r)/SG), cx1=Math.floor((x+r)/SG), cz0=Math.floor((z-r)/SG), cz1=Math.floor((z+r)/SG);
  var aSeen={}, cSeen={}, aList=[], cList=[];
  for(var cx=cx0;cx<=cx1;cx++) for(var cz=cz0;cz<=cz1;cz++){ var cell=sGrid[sKey(cx,cz)]; if(!cell) continue;
    for(var i=0;i<cell.a.length;i++){ var ia=cell.a[i]; if(!aSeen[ia]){aSeen[ia]=1;aList.push(ia);} }
    for(var j=0;j<cell.c.length;j++){ var ic=cell.c[j]; if(!cSeen[ic]){cSeen[ic]=1;cList.push(ic);} }
  }
  return {a:aList,c:cList};
}
var player, agents = [], loot = [];
var state = "menu", role = "cop", diff = "normal", map = "city", mode = "chase";
var cashTruck=null, hijackMeter=0, hijacked=false, crew=null;
var timeLeft = ROUND_TIME, score = 0;
var soundOn = true, audio = null, sirenGain = null, sirenOsc = null, masterGain = null;
var settings = { volume:0.8, camSens:1.0, quality:"high", invertY:false };
var keys = {}, isTouch = false;
var cbCooldown=0, shockwaves=[], flashEl=null;
var particles=[], theRobber=null;
var engineOsc=null, engineGain=null, skidOsc=null, skidGain=null;
var driftCombo=0, driftScore=0, driftActive=false, driftHold=0;
var traffic=[];
var ramps=[], GRAVITY=20;
var crossChan=0, crossH=6.5, crossGap=9, crossZc=0, drawbridge=null, train=null;
var decks=[];
var roadNodes=[], roadAdj=[];
function posBox(w,h,l,x,y,z,mat){ var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,l),mat); m.position.set(x,y,z); return m; }
var damageOn=true, debris=[];
var props=[], skidPool=[], skidIdx=0, SKID_CAP=440, skidMat=null;
var ending=null;
var powerups=[], hazards=[], shieldBubble=null;
var GADGETS={
  nitro:{label:"Nitro Dash", short:"Nitro", cd:16},
  emp:{label:"EMP Pulse", short:"EMP", cd:30},
  smoke:{label:"Smoke Screen", short:"Smoke", cd:22},
  spikes:{label:"Drop Spikes", short:"Spikes", cd:26}
};
var gadget="nitro", smokeZones=[];
var objectives=[], objIndex=0, heat=0, heatSpawned=0, spawnCfg=null, HEAT_RATE=0.8, heistStreak=0;
var chopper=null, roadblocks=[], copDeployTimer=0, CHOP_ALT=24, POOL_R=7.5, SPOT_HEAT=7.0, PIT_KICK=0.85;
var chopperMode=false, pChop=null, pinMeter=0;
var onFoot=false, onDutyMode=false, human=null, garageCars=[], selectedGarage=-1, leftStation=false, chaseStarted=false, stationPos={x:0,z:0}, stationR=34, roofHeli=null, heliTaken=false, nearGarageIdx=-1;
var _v1=new THREE.Vector3(), _v2=new THREE.Vector3();
var CIV_PAINTS=[0xced3da,0x8a92a0,0x3a4250,0xeef2f7,0x5a6473,0x222831,0x9ca6b5,0x4a5a7a,0x707a88];
var moveVec = {x:0,z:0}, lookVec = {x:0,z:0}, touchBoost = false;

// camera state (adjustable, Whisperwood-style free orbit)
var camYaw = 0, camPitch = 0.62, camDist = 15;
var camTargetYaw = 0, camTargetPitch = 0.62, camTargetDist = 15;
var dragging = false, lastPX = 0, lastPY = 0, pinchStart = 0;

var elHud=document.getElementById("hud"), elTimer=document.getElementById("timer");
var elObjective=document.getElementById("objective"), elRoleDot=document.getElementById("roleDot");
var elRoleText=document.getElementById("roleText"), elScore=document.getElementById("score");
var elBoost=document.getElementById("boost"), elSpeed=document.getElementById("speed");
var elBreaker=document.getElementById("breaker");
var elHealth=document.getElementById("health"), elHealthLabel=document.getElementById("healthLabel");
var elDrift=document.getElementById("drift");
var tachCanvas=document.getElementById("tach"), tachCtx=tachCanvas.getContext("2d");
var elAlert=document.getElementById("alert");
var elHeat=document.getElementById("heat"), elStreak=document.getElementById("streakLabel");
var miniCanvas=document.getElementById("minimap"), miniCtx=miniCanvas.getContext("2d");

// ----------------------------------------------------------------------------
//  Three.js setup
// ----------------------------------------------------------------------------
function makeEnvMap(){
  var pmrem=new THREE.PMREMGenerator(renderer);
  var es=new THREE.Scene();
  var cv=document.createElement("canvas"); cv.width=16; cv.height=128;
  var cx=cv.getContext("2d");
  var gd=cx.createLinearGradient(0,0,0,128);
  gd.addColorStop(0.0,"#0a1430"); gd.addColorStop(0.45,"#22386a"); gd.addColorStop(0.70,"#3a5f95"); gd.addColorStop(1.0,"#0a0e16");
  cx.fillStyle=gd; cx.fillRect(0,0,16,128);
  var tex=new THREE.CanvasTexture(cv);
  var sky=new THREE.Mesh(new THREE.SphereGeometry(60,24,16), new THREE.MeshBasicMaterial({map:tex,side:THREE.BackSide}));
  es.add(sky);
  function panel(x,y,z,w,h,c){ var m=new THREE.Mesh(new THREE.PlaneGeometry(w,h),new THREE.MeshBasicMaterial({color:c})); m.position.set(x,y,z); m.lookAt(0,0,0); es.add(m); }
  panel(0,40,0,60,60,0xffffff);
  panel(28,16,16,22,36,0x8aa0c0);
  panel(-30,12,-12,22,36,0x6a7fa0);
  var rt=pmrem.fromScene(es,0.04); pmrem.dispose();
  return rt.texture;
}

function initThree(){
  renderer = new THREE.WebGLRenderer({ antialias:true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  renderer.outputEncoding = THREE.sRGBEncoding;
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.12;
  var el = renderer.domElement;
  document.getElementById("game").appendChild(el);

  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x0b0f17);
  scene.fog = new THREE.Fog(0x0b0f17, 55, 120);
  ENV = makeEnvMap();
  scene.environment = ENV;

  camera = new THREE.PerspectiveCamera(58, window.innerWidth/window.innerHeight, 0.1, 3000);

  clock = new THREE.Clock();
  window.addEventListener("resize", onResize);
  bindCameraControls(el);
}
function onResize(){
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}
function applyQuality(q){
  if(!renderer) return;
  if(q==="low"){ renderer.setPixelRatio(1); renderer.shadowMap.enabled=false; }
  else if(q==="medium"){ renderer.setPixelRatio(Math.min(window.devicePixelRatio,1.5)); renderer.shadowMap.enabled=true; renderer.shadowMap.type=THREE.PCFShadowMap; }
  else { renderer.setPixelRatio(Math.min(window.devicePixelRatio,2)); renderer.shadowMap.enabled=true; renderer.shadowMap.type=THREE.PCFSoftShadowMap; }
  renderer.shadowMap.needsUpdate=true;
  renderer.setSize(window.innerWidth, window.innerHeight);
}

// ----------------------------------------------------------------------------
//  World
// ----------------------------------------------------------------------------
function buildLights(){
  scene.add(new THREE.HemisphereLight(0x4b5b8a, 0x0a0c12, 0.5));
  scene.add(new THREE.AmbientLight(0x2a3450, 0.45));
  var moon=new THREE.DirectionalLight(0xaecbff, 0.85);
  moon.position.set(-60,90,-40); moon.castShadow=true;
  moon.shadow.mapSize.set(2048,2048);
  var d=95;
  moon.shadow.camera.left=-d;moon.shadow.camera.right=d;moon.shadow.camera.top=d;moon.shadow.camera.bottom=-d;
  moon.shadow.camera.near=1;moon.shadow.camera.far=260;moon.shadow.bias=-0.0004;
  scene.add(moon);
}
function rand(a,b){return a+Math.random()*(b-a);}

var MAPS = {
  city:    { ground:0x10141d, fog:0x0b0f17, bg:0x0b0f17, near:55, far:120, wall:0x0d1119, grip:1.0 },
  desert:  { ground:0xc79b5e, fog:0x6b5230, bg:0x241a10, near:60, far:155, wall:0x6e4f30, grip:1.0 },
  plain:   { ground:0x35552a, fog:0x12230f, bg:0x0c1609, near:60, far:155, wall:0x22451c, grip:1.0 },
  rain:    { ground:0x0c1018, fog:0x0a0e16, bg:0x0a0e16, near:40, far:92,  wall:0x0b0f17, grip:0.72, weather:"rain" },
  ice:     { ground:0xdfe9f2, fog:0xcdd8e6, bg:0xb8c6d8, near:50, far:130, wall:0x9fb0c4, grip:0.55, weather:"snow" },
  highway: { ground:0x1a1d24, fog:0x10131a, bg:0x10131a, near:70, far:185, wall:0x2a2f3a, grip:1.0 },
  harbor:  { ground:0x1d2630, fog:0x0e1a24, bg:0x0e1a24, near:55, far:145, wall:0x24323e, grip:0.9 },
  world:   { ground:0x14181f, fog:0x0c0f15, bg:0x0c0f15, near:100, far:2500, wall:0x141922, grip:1.0, size:1770 },
  crossing:{ ground:0x0c161e, fog:0x0b1822, bg:0x0b1822, near:80, far:260, wall:0x1a2630, grip:1.0, size:175 },
  custom:  { ground:0x14181f, fog:0x0c0f15, bg:0x0c0f15, near:100, far:820, wall:0x141922, grip:1.0, size:120 }
};
var customWorld=null, customMeta=null, customSpawnP={x:0,z:0}, customSpawnR={x:0,z:0}, activeCustomBase=null, customTrain=null, liftSpans=[], thirdDraw=null;
var customWater=[], customDraws=[];
var boundHalfX=0, boundHalfZ=0;
var mapGrip=1, weather=null, weatherObj=null;

function buildGround(color){
  var mat=new THREE.MeshStandardMaterial({ color:color, roughness:0.98, metalness:0.0 });
  var g=new THREE.Mesh(new THREE.PlaneGeometry(CITY*2,CITY*2), mat);
  g.rotation.x=-Math.PI/2; g.receiveShadow=true; scene.add(g);
}
function addWall(cx,cz,w,h,color){
  var mesh=new THREE.Mesh(new THREE.BoxGeometry(w,5,h), new THREE.MeshStandardMaterial({color:color,roughness:1}));
  mesh.position.set(cx,2.5,cz); mesh.castShadow=true; mesh.receiveShadow=true; scene.add(mesh);
  aabbs.push({minX:cx-w/2,maxX:cx+w/2,minZ:cz-h/2,maxZ:cz+h/2});
}
function buildWalls(color){
  var t=2, L=CITY*2+t;
  addWall(0,-CITY,L,t,color); addWall(0,CITY,L,t,color); addWall(-CITY,0,t,L,color); addWall(CITY,0,t,L,color);
}

function addLiftBridge(zc, CHAN, S){
  var H=6.5, GAP=9, W=16, APR=26, KICK=3.0, KL=10;
  crossChan=CHAN; crossH=H; crossGap=GAP; crossZc=zc;
  var water=new THREE.Mesh(new THREE.PlaneGeometry(S*2, CHAN*2),
    new THREE.MeshStandardMaterial({color:0x14506b, roughness:0.25, metalness:0.55, transparent:true, opacity:0.94}));
  water.rotation.x=-Math.PI/2; water.position.set(0,0.3,zc); water.receiveShadow=true; scene.add(water);
  var foamMat=new THREE.MeshBasicMaterial({color:0x8fd4e8,transparent:true,opacity:0.16});
  for(var fz=-CHAN+5; fz<CHAN; fz+=8){ var fm=new THREE.Mesh(new THREE.PlaneGeometry(S*2,0.7),foamMat); fm.rotation.x=-Math.PI/2; fm.position.set(0,0.33,zc+fz); scene.add(fm); }
  var openHalf=W/2+1, segLen=(S-openHalf)/2;
  function seaWall(cx,cz){ var m=new THREE.Mesh(new THREE.BoxGeometry(segLen,2.4,1.6),new THREE.MeshStandardMaterial({color:0x2a323c,roughness:0.9})); m.position.set(cx,1.2,cz); m.castShadow=true; scene.add(m); aabbs.push({minX:cx-segLen/2,maxX:cx+segLen/2,minZ:cz-0.8,maxZ:cz+0.8}); }
  seaWall(-(openHalf+segLen/2),zc+CHAN); seaWall(openHalf+segLen/2,zc+CHAN);
  seaWall(-(openHalf+segLen/2),zc-CHAN); seaWall(openHalf+segLen/2,zc-CHAN);
  var deckMat=new THREE.MeshStandardMaterial({color:0x3a414d,roughness:0.7,metalness:0.4});
  var pierMat=new THREE.MeshStandardMaterial({color:0x4a525e,roughness:0.8,metalness:0.3});
  function deckSpan(czc,d){
    var dk=posBox(W,0.5,d,0,H,czc,deckMat); dk.receiveShadow=true; dk.castShadow=true; scene.add(dk);
    scene.add(posBox(0.3,0.9,d,W/2,H+0.55,czc,pierMat)); scene.add(posBox(0.3,0.9,d,-W/2,H+0.55,czc,pierMat));
    for(var pz=czc-d/2+3; pz<=czc+d/2-3; pz+=10){ scene.add(posBox(1.2,H,1.2,-W/2+1.5,H/2,pz,pierMat)); scene.add(posBox(1.2,H,1.2,W/2-1.5,H/2,pz,pierMat)); }
    decks.push({x:0,z:czc,w:W,d:d,h:H});
  }
  deckSpan(zc+(GAP+CHAN)/2, CHAN-GAP); deckSpan(zc-(GAP+CHAN)/2, CHAN-GAP);
  makeRamp(0, zc+CHAN+APR/2, Math.PI, APR, W, H);
  makeRamp(0, zc-(CHAN+APR/2), 0, APR, W, H);
  // ---- animated drawbridge across the central gap ----
  var liftDeck={x:0,z:zc,w:W,d:GAP*2+1,h:H}; decks.push(liftDeck);     // supports the gap while closed
  var Lp=KL*(H+KICK)/KICK;
  var nearKick={x:0,z:(zc+GAP)+(Lp/2),yaw:Math.PI,L:Lp,Wd:W,H:0}; ramps.push(nearKick);  // launch ramps, only active when open
  var farKick ={x:0,z:(zc-GAP)-(Lp/2),yaw:0,     L:Lp,Wd:W,H:0}; ramps.push(farKick);
  var nearPivot=new THREE.Group(); nearPivot.position.set(0,H,zc+GAP); scene.add(nearPivot);
  var nl=posBox(W,0.4,GAP,0,0,-GAP/2,deckMat); nl.castShadow=true; nl.receiveShadow=true; nearPivot.add(nl);
  nearPivot.add(posBox(0.3,0.7,GAP,W/2,0.4,-GAP/2,pierMat)); nearPivot.add(posBox(0.3,0.7,GAP,-W/2,0.4,-GAP/2,pierMat));
  var farPivot=new THREE.Group(); farPivot.position.set(0,H,zc-GAP); scene.add(farPivot);
  var fl=posBox(W,0.4,GAP,0,0,GAP/2,deckMat); fl.castShadow=true; fl.receiveShadow=true; farPivot.add(fl);
  farPivot.add(posBox(0.3,0.7,GAP,W/2,0.4,GAP/2,pierMat)); farPivot.add(posBox(0.3,0.7,GAP,-W/2,0.4,GAP/2,pierMat));
  // towers, cross-beams, warning light, cables
  var towerMat=new THREE.MeshStandardMaterial({color:0x586271,roughness:0.7,metalness:0.5});
  for(var tx=-1;tx<=1;tx+=2) for(var tz=-1;tz<=1;tz+=2) scene.add(posBox(1.6,22,1.6, tx*(W/2+0.9), 11, zc+tz*GAP, towerMat));
  scene.add(posBox(W+3,1.0,1.4,0,21,zc+GAP,towerMat)); scene.add(posBox(W+3,1.0,1.4,0,21,zc-GAP,towerMat));
  var warnMat=new THREE.MeshStandardMaterial({color:0xff3b30,emissive:0xff3b30,emissiveIntensity:0.2});
  var warn=posBox(1.0,0.5,1.0,0,22.2,zc,warnMat); scene.add(warn);
  var cableMat=new THREE.MeshStandardMaterial({color:0x20262f});
  for(var cx2=-1;cx2<=1;cx2+=2){ scene.add(posBox(0.16,0.16,GAP*2.2,cx2*(W/2-0.4),20,zc,cableMat)); }
  drawbridge={ t:0, angle:0, wasOpen:false, baseH:H, openH:H+KICK,
    liftDeck:liftDeck, nearKick:nearKick, farKick:farKick,
    nearPivot:nearPivot, farPivot:farPivot, warn:warn, openAngle:0.95 };
}
function updateDrawbridge(dt){
  if(!drawbridge) return; var d=drawbridge; d.t+=dt;
  var opening=(d.t%15)>=10;                                // opens every 15s (open for 5s, closed for 10s)
  var ta=opening?d.openAngle:0;
  d.angle+=(ta-d.angle)*Math.min(1,dt*1.4);
  d.nearPivot.rotation.x=d.angle; d.farPivot.rotation.x=-d.angle;
  var physOpen=d.angle>0.3;
  d.liftDeck.h=physOpen?-999:d.baseH;                      // remove deck support when raised
  d.nearKick.H=physOpen?d.openH:0; d.farKick.H=physOpen?d.openH:0;   // launch ramps active only when open
  if(d.warn) d.warn.material.emissiveIntensity=opening?(0.4+0.9*(0.5+0.5*Math.sin(d.t*9))):0.12;
  if(opening&&!d.wasOpen&&elAlert){ elAlert.textContent="Drawbridge opening — jump it or stop!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},2400); beep(300,0.14,"square"); }
  d.wasOpen=opening;
}
function buildCrossing(){
  var S=CITY, CHAN=34, openHalf=9;
  function patch(x0,z0,x1,z1,col){ var p=new THREE.Mesh(new THREE.PlaneGeometry(x1-x0,z1-z0),new THREE.MeshStandardMaterial({color:col,roughness:0.97})); p.rotation.x=-Math.PI/2; p.position.set((x0+x1)/2,0.02,(z0+z1)/2); p.receiveShadow=true; scene.add(p); }
  patch(-S,CHAN,0,S,0x10141d); patch(0,CHAN,S,S,0xc79b5e);
  patch(-S,-S,0,-CHAN,0x10141d); patch(0,-S,S,-CHAN,0x35552a);
  var roadMat=new THREE.MeshStandardMaterial({color:0x191c22,roughness:0.95});
  scene.add(posBox(16,0.06,S-CHAN,0,0.05,(CHAN+S)/2,roadMat));
  scene.add(posBox(16,0.06,S-CHAN,0,0.05,-(CHAN+S)/2,roadMat));
  function block(x,z,w,h,d,col){ var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),new THREE.MeshStandardMaterial({color:col,roughness:0.9})); m.position.set(x,h/2,z); m.castShadow=true; m.receiveShadow=true; scene.add(m); aabbs.push({minX:x-w/2,maxX:x+w/2,minZ:z-d/2,maxZ:z+d/2}); }
  function scatterSide(z0,z1){
    for(var i=0;i<24;i++){ var x=rand(-S+10,S-10), z=rand(z0,z1);
      if(Math.abs(x)<openHalf+5) continue;
      if(circleHitsAABB(x,z,3)) continue;
      if(x<0) block(x,z,6+Math.random()*4,8+Math.random()*22,6+Math.random()*4,0x222a36);
      else if(Math.random()<0.5) block(x,z,7,3+Math.random()*3,7,0x8a6a3c);
      else { var tr=new THREE.Mesh(new THREE.ConeGeometry(2.2,5,7),new THREE.MeshStandardMaterial({color:0x2f6b34,roughness:0.9})); tr.position.set(x,2.5,z); tr.castShadow=true; scene.add(tr); circles.push({x:x,z:z,r:2.0}); }
    }
  }
  scatterSide(CHAN+9,S-10); scatterSide(-S+10,-CHAN-9);
  addLiftBridge(0, CHAN, S);
}
function crossingUpdate(dt){
  var cars=[player].concat(agents); if(crew) cars.push(crew);
  for(var i=0;i<cars.length;i++){
    var e=cars[i]; if(!e||!e.group.visible) continue; var p=e.group.position, dz=p.z-crossZc;
    if(!e.isPlayer && Math.abs(p.x)<8 && Math.abs(dz)<crossGap+13 && e.y>crossH-2) e.speed=Math.max(e.speed,e.maxSpeed*0.98);  // AI guns the jump
    if(Math.abs(dz)<crossChan && Math.abs(p.x)<CITY-2 && e.y<1.3 && deckHeightAt(e)<1) splashOut(e);
  }
}
function splashOut(e){
  var p=e.group.position;
  for(var i=0;i<20;i++){ var a=Math.random()*6.283, s=1+Math.random()*4; addParticle(p.x,0.5,p.z,0x9fd8ec,0.2,Math.cos(a)*s,5+Math.random()*5,Math.sin(a)*s,0.8); }
  spawnShockwave(p.x,p.z);
  if(e===theRobber){ if(e.isPlayer) thud(); endGame(role==="cop","Sank in the bay!","busted"); }
  else { e.y=0; e.vy=0; e.speed=0; var far=p.z<crossZc; var bz=far?crossZc-crossChan-12:crossZc+crossChan+12; e.group.position.set(Math.max(-CITY+10,Math.min(CITY-10,p.x)),0,bz); e.heading=far?0:Math.PI; e.group.rotation.y=e.heading; }
}
function updateCustomDraws(dt){
  for(var i=0;i<customDraws.length;i++){ var d=customDraws[i]; d.t+=dt;
    var opening=(d.t%15)>=10;                                     // same rhythm as the Mega World bridge: open 5s every 15s
    var ta=opening?d.openAngle:0;
    d.angle+=(ta-d.angle)*Math.min(1,dt*1.4);
    if(d.axis==="z"){ d.nearGroup.rotation.x=d.angle; d.farGroup.rotation.x=-d.angle; }
    else { d.nearGroup.rotation.z=-d.angle; d.farGroup.rotation.z=d.angle; }
    var physOpen=d.angle>0.3;
    d.liftDeck.h=physOpen?-999:d.baseH;
    d.nearKick.H=physOpen?d.openH:0; d.farKick.H=physOpen?d.openH:0;
    if(d.warn) d.warn.material.emissiveIntensity=opening?(0.4+0.9*(0.5+0.5*Math.sin(d.t*9))):0.12;
    if(opening&&!d.wasOpen&&elAlert){ elAlert.textContent="Drawbridge opening — jump it or stop!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},2400); beep(300,0.14,"square"); }
    d.wasOpen=opening;
    // gap-fall detection, mirrors the Mega World's crossingUpdate/splashOut
    var cars=[player].concat(agents); if(crew) cars.push(crew);
    for(var ci=0;ci<cars.length;ci++){ var e=cars[ci]; if(!e||!e.group||!e.group.visible||e.isHeli) continue;
      var p=e.group.position, u=(d.axis==="z")?(p.z-d.cz):(p.x-d.cx), v=(d.axis==="z")?(p.x-d.cx):(p.z-d.cz);
      if(Math.abs(u)<d.GAP && Math.abs(v)<d.Wd/2+2 && physOpen && !e.isHeli && e.y<1.3 && deckHeightAt(e)<1){
        var p2=e.group.position;
        for(var k=0;k<20;k++){ var a=Math.random()*6.283,s=1+Math.random()*4; addParticle(p2.x,0.5,p2.z,0x9fd8ec,0.2,Math.cos(a)*s,5+Math.random()*5,Math.sin(a)*s,0.8); }
        spawnShockwave(p2.x,p2.z);
        if(e===theRobber){ if(e.isPlayer) thud(); endGame(role==="cop","Sank in the drawbridge gap!","busted"); }
        else { e.y=0; e.vy=0; e.speed=0; var far=u<0; var back=d.axis==="z"?{x:p2.x,z:far?d.cz-d.GAP-12:d.cz+d.GAP+12}:{x:far?d.cx-d.GAP-12:d.cx+d.GAP+12,z:p2.z};
          e.group.position.set(back.x,0,back.z); e.heading=far?0:Math.PI; e.group.rotation.y=e.heading; }
      }
    }
  }
}
function inRects(p,rects){ for(var i=0;i<rects.length;i++){ var r=rects[i]; if(p.x>r.minX&&p.x<r.maxX&&p.z>r.minZ&&p.z<r.maxZ) return true; } return false; }
function customWaterUpdate(dt){
  if(onFoot) return; var cars=[player].concat(agents); if(crew) cars.push(crew);
  for(var i=0;i<cars.length;i++){ var e=cars[i]; if(!e||!e.group||!e.group.visible||e.isHeli) continue;
    var p=e.group.position; var hazard=inRects(p,customWater) && (e.y||0)<1.0;
    if(hazard){ e.sinkT=(e.sinkT||0)+dt; e.group.position.y=-Math.min(3.2,e.sinkT*7); e.speed*=Math.pow(0.82,dt*60);
      if(e.sinkT>0.45){
        for(var k=0;k<16;k++){ var a=Math.random()*6.283,s=1+Math.random()*4; addParticle(p.x,0.4,p.z,0x9fd8ec,0.2,Math.cos(a)*s,4+Math.random()*5,Math.sin(a)*s,0.8); }
        spawnShockwave(p.x,p.z);
        var sp=e.lastSafe||customSpawnP; e.group.position.set(sp.x,0,sp.z); e.y=0; e.vy=0; e.speed=0; e.sinkT=0;
        if(e.isPlayer){ thud(); if(elAlert){ elAlert.textContent="Splashed! Pulled back to shore."; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1800); } }
      }
    } else { e.sinkT=0; e.lastSafe={x:p.x,z:p.z}; }
  }
}
function buildPathCum(path,closed){
  var cum=[0];
  for(var i=1;i<path.length;i++) cum.push(cum[i-1]+Math.hypot(path[i].x-path[i-1].x,path[i].z-path[i-1].z));
  if(closed) cum.push(cum[cum.length-1]+Math.hypot(path[0].x-path[path.length-1].x,path[0].z-path[path.length-1].z));
  return cum;
}
function sampleTrainPos(T,d){
  var path=T.path, cum=T.cum, total=T.total, closed=T.closed, n=cum.length-1;
  if(closed) d=((d%total)+total)%total;
  else { var period=total*2; d=((d%period)+period)%period; if(d>total) d=period-d; }
  for(var i=0;i<n;i++){ if(d<=cum[i+1] || i===n-1){
    var segLen=cum[i+1]-cum[i]||1e-4, t=Math.max(0,Math.min(1,(d-cum[i])/segLen));
    var a=path[i], b=(closed&&i===n-1)?path[0]:path[i+1];
    return {x:a.x+(b.x-a.x)*t, z:a.z+(b.z-a.z)*t}; } }
  return {x:path[0].x,z:path[0].z};
}
function makeSimpleTrainCar(loco){
  var g=new THREE.Group();
  var body=new THREE.MeshStandardMaterial({color:loco?0x16335e:0x394250,roughness:0.45,metalness:0.55});
  var dark=new THREE.MeshStandardMaterial({color:0x10141c,roughness:0.7});
  var yellow=new THREE.MeshStandardMaterial({color:0xf0c020,emissive:0x3a2e06,roughness:0.5});
  function bb(w,h,d,x,y,z,m){ var mm=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); mm.position.set(x,y,z); mm.castShadow=true; g.add(mm); }
  bb(4.0,0.6,loco?8.4:7.6,0,0.5,0,dark);
  bb(3.8,loco?2.8:2.4,loco?8.2:7.4,0,(loco?1.9:1.65),0,body);
  bb(4.0,0.45,loco?8.2:7.4,0,0.45,0,yellow);
  if(loco) bb(0.6,0.6,0.6,0,1.4,4.2,new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffd070,emissiveIntensity:1.0}));
  scene.add(g); return g;
}
function updateThirdDrawbridge(dt){
  var L=thirdDraw; L.t+=dt;
  var opening=(L.t%16)>=10;
  var ta=opening?1:0; L.angle+=(ta-L.angle)*Math.min(1,dt*1.6);
  L.mesh.position.y=L.baseH+L.angle*2.2; L.mesh.rotation.x=-L.angle*0.5;
  var physOpen=L.angle>0.4; L.deckRef.h=physOpen?-999:L.baseH;
  if(L.warn) L.warn.material.emissiveIntensity=opening?(0.4+0.9*(0.5+0.5*Math.sin(L.t*9))):0.15;
  if(ending) return;
  var cars=[player].concat(agents); if(crew) cars.push(crew);
  for(var i=0;i<cars.length;i++){ var e=cars[i]; if(!e||!e.group||!e.group.visible||e.isHeli) continue;
    var p=e.group.position;
    if(physOpen && Math.hypot(p.x-L.cx,p.z-L.cz)<9 && (e.y||0)<1.6){
      for(var k=0;k<16;k++){ var a=Math.random()*6.283,s=1+Math.random()*4; addParticle(p.x,0.5,p.z,0x9fd8ec,0.2,Math.cos(a)*s,4+Math.random()*4,Math.sin(a)*s,0.8); }
      spawnShockwave(p.x,p.z);
      if(e===theRobber){ if(e.isPlayer) thud(); endGame(role==="cop","Sank at the third island crossing!","busted"); }
      else { var sp=e.lastSafe||{x:L.cx,z:L.cz-30}; e.group.position.set(sp.x,0,sp.z); e.y=0; e.vy=0; e.speed=0; }
    } else if(!physOpen) e.lastSafe = (Math.hypot(p.x-L.cx,p.z-L.cz)<40) ? {x:p.x,z:p.z} : e.lastSafe;
  }
}
function addThirdDrawbridge(cx,zc){                                                      // a real animated drawbridge, self-contained — doesn't touch the main crossing's singleton state
  var H=5.0, W=14, GAP=16;
  var deckMat=new THREE.MeshStandardMaterial({color:0x9a8a3a,roughness:0.7,metalness:0.3});
  var deck=posBox(W,0.5,GAP,cx,H,zc,deckMat); deck.castShadow=true; scene.add(deck);
  var towerMat=new THREE.MeshStandardMaterial({color:0x586271,roughness:0.7,metalness:0.5});
  var t1=posBox(1.2,10,1.2,cx-W/2-1,5,zc-GAP/2-1,towerMat), t2=posBox(1.2,10,1.2,cx+W/2+1,5,zc+GAP/2+1,towerMat);
  t1.castShadow=true; t2.castShadow=true; scene.add(t1); scene.add(t2);
  var warn=new THREE.Mesh(new THREE.SphereGeometry(0.5,10,8),new THREE.MeshStandardMaterial({color:0xff3b30,emissive:0xff3b30,emissiveIntensity:0.2}));
  warn.position.set(cx,10.6,zc); scene.add(warn);
  var liftDeck={x:cx,z:zc,w:W,d:GAP,h:H}; decks.push(liftDeck);
  thirdDraw={cx:cx,cz:zc,baseH:H,angle:0,t:Math.random()*16,mesh:deck,deckRef:liftDeck,warn:warn};
  makeRamp(cx, zc-GAP/2-13, 0, 26, W, H); makeRamp(cx, zc+GAP/2+13, Math.PI, 26, W, H);
}
function updateCustomTrain(dt){
  if(!customTrain) return; var T=customTrain;
  T.dist+=T.speed*dt; var spacing=8.5;
  for(var i=0;i<T.cars.length;i++){
    var p1=sampleTrainPos(T,T.dist-i*spacing), p2=sampleTrainPos(T,T.dist-i*spacing+0.6);
    T.cars[i].position.set(p1.x,0,p1.z); T.cars[i].rotation.y=Math.atan2(p2.x-p1.x,p2.z-p1.z);
  }
  if(ending) return;
  function struck(e){ if(!e||!e.group||(e.y||0)>3) return false; var p=e.group.position; for(var i=0;i<T.cars.length;i++){ var cp=T.cars[i].position; if(Math.hypot(p.x-cp.x,p.z-cp.z)<3.6) return true; } return false; }
  if(struck(player) && !onFoot){ spawnShockwave(player.group.position.x,player.group.position.z); thud(); endGame(false,"You were hit by the train!","busted"); return; }
  if(theRobber && theRobber!==player && struck(theRobber)){ spawnShockwave(theRobber.group.position.x,theRobber.group.position.z); thud(); endGame(role==="cop","The suspect was hit by a train!","busted"); }
}
function updateLiftSpans(dt){
  for(var i=0;i<liftSpans.length;i++){ var L=liftSpans[i]; L.t+=dt;
    var opening=(L.t%12)>=8;                                                     // open 4s every 12s — a compact version of the real cycle
    var ta=opening?1:0; L.angle+=(ta-L.angle)*Math.min(1,dt*2.2);
    L.mesh.position.y=L.baseH+L.angle*1.6; L.mesh.rotation.x=-L.angle*0.55;
    var physOpen=L.angle>0.4; L.deckRef.h=physOpen?-999:L.baseH;
    if(L.warn) L.warn.material.emissiveIntensity=opening?(0.4+0.9*(0.5+0.5*Math.sin(L.t*9))):0.15;
    if(ending) continue;
    var cars=[player].concat(agents); if(crew) cars.push(crew);
    for(var ci=0;ci<cars.length;ci++){ var e=cars[ci]; if(!e||!e.group||!e.group.visible||e.isHeli) continue;
      var p=e.group.position;
      if(physOpen && Math.hypot(p.x-L.cx,p.z-L.cz)<5.5 && (e.y||0)<1.3){
        for(var k=0;k<14;k++){ var a=Math.random()*6.283,s=1+Math.random()*3; addParticle(p.x,0.5,p.z,0x9fd8ec,0.2,Math.cos(a)*s,4+Math.random()*4,Math.sin(a)*s,0.8); }
        spawnShockwave(p.x,p.z);
        if(e===theRobber){ if(e.isPlayer) thud(); endGame(role==="cop","Sank through the lift span!","busted"); }
        else { var sp=e.lastSafe||customSpawnP; e.group.position.set(sp.x,0,sp.z); e.y=0; e.vy=0; e.speed=0; }
      }
    }
  }
}
function buildCustomWorld(){
  var data=customWorld||{world:new Uint8Array(PW*PW),interiors:{}};
  var warr=data.world, inter=data.interiors||{};
  var meta=customMeta||{cxm:PW/2,cym:PW/2,ST:10,PT:60}, cxm=meta.cxm, cym=meta.cym, ST=meta.ST, PT=meta.PT;
  if(activeCustomBase==="mega") buildMegaWorld();          // the REAL Mega World geometry, exactly as the "world" map builds it
  else if(activeCustomBase==="city") buildCity();           // the REAL City map geometry
  else { scene.add(new THREE.HemisphereLight(0x35415a,0x0a0c12,0.42));
    var sun=new THREE.DirectionalLight(0xbcd0e6,0.45); sun.position.set(-60,120,40); scene.add(sun); }
  var bBaseNodeCount=roadNodes.length;
  var GC={1:0x14506b,2:0x12161f,3:0x35552a,4:0xc79b5e,5:0x1a2230,8:0xdfe9f2,9:0x1d2630};
  function plateCenter(px,py){ return { x:(px-cxm)*PT, z:(py-cym)*PT }; }
  function landAt(qx,qy){ if(qx<0||qy<0||qx>=PW||qy>=PW) return 0; return warr[qy*PW+qx]; }
  function isLand(t){ return t===2||t===3||t===4||t===5||t===8||t===9; }
  function patch(x,z,w,col,y){ var m=new THREE.Mesh(new THREE.PlaneGeometry(w,w),new THREE.MeshStandardMaterial({color:col,roughness:0.96})); m.rotation.x=-Math.PI/2; m.position.set(x,y||0.02,z); m.receiveShadow=true; scene.add(m); }
  function box(w,h,d,x,y,z,col,solid){ var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),new THREE.MeshStandardMaterial({color:col,roughness:0.65,metalness:0.3})); m.position.set(x,y,z); m.castShadow=true; m.receiveShadow=true; scene.add(m); if(solid) aabbs.push({minX:x-w/2,maxX:x+w/2,minZ:z-d/2,maxZ:z+d/2}); return m; }
  var px,py,sx,sy;
  customWater=[]; customDraws=[]; liftSpans=[];
  for(py=0;py<PW;py++) for(px=0;px<PW;px++){ var terr=warr[py*PW+px]; if(!terr) continue; var c=plateCenter(px,py);
    if(terr===1){ // water — sinkable
      var wm=new THREE.Mesh(new THREE.PlaneGeometry(PT,PT),new THREE.MeshStandardMaterial({color:0x1c6ea0,roughness:0.32,metalness:0.2,transparent:true,opacity:0.92}));
      wm.rotation.x=-Math.PI/2; wm.position.set(c.x,0.08,c.z); wm.receiveShadow=true; scene.add(wm);
      customWater.push({minX:c.x-PT/2+2,maxX:c.x+PT/2-2,minZ:c.z-PT/2+2,maxZ:c.z+PT/2-2});
    } else if(terr===6){ // bridge — EXACT Mega World overpass size: L=16 ramp, W=8 deck, h=4.6
      var bAxis=(isLand(landAt(px,py-1))||isLand(landAt(px,py+1)))?"z":"x";
      var H=4.6, L=16, W=8;
      var dw=(bAxis==="z")?W:PT, dd=(bAxis==="z")?PT:W;
      var deckMat=new THREE.MeshStandardMaterial({color:0x2b3038,roughness:0.85,metalness:0.12});
      var dk=posBox(dw,0.4,dd,c.x,H-0.2,c.z,deckMat); dk.castShadow=true; dk.receiveShadow=true; scene.add(dk);
      decks.push({x:c.x,z:c.z,w:dw,d:dd,h:H});
      var lnMat=new THREE.MeshStandardMaterial({color:0xd8c24a,emissive:0x2a2206,roughness:0.7});
      if(bAxis==="z") for(var lz=-dd/2+4; lz<dd/2-4; lz+=7) scene.add(posBox(0.3,0.05,3,c.x,H+0.02,c.z+lz,lnMat));
      else            for(var lx=-dw/2+4; lx<dw/2-4; lx+=7) scene.add(posBox(3,0.05,0.3,c.x+lx,H+0.02,c.z,lnMat));
      var raMat=new THREE.MeshStandardMaterial({color:0x3a4150,roughness:0.7,metalness:0.3});
      if(bAxis==="z"){ scene.add(posBox(0.22,0.5,dd,c.x+dw/2,H+0.25,c.z,raMat)); scene.add(posBox(0.22,0.5,dd,c.x-dw/2,H+0.25,c.z,raMat)); }
      else { scene.add(posBox(dw,0.5,0.22,c.x,H+0.25,c.z+dd/2,raMat)); scene.add(posBox(dw,0.5,0.22,c.x,H+0.25,c.z-dd/2,raMat)); }
      var piMat=new THREE.MeshStandardMaterial({color:0x222831,roughness:0.9});
      var pn=6; for(var pi=0;pi<=pn;pi++){ var pf=(pi/pn-0.5); var ppx=(bAxis==="z")?c.x:c.x+pf*dw, ppz=(bAxis==="z")?c.z+pf*dd:c.z;
        for(var ps=-1;ps<=1;ps+=2){ var pox=(bAxis==="z")?ps*(W/2-0.6):0, poz=(bAxis==="z")?0:ps*(W/2-0.6);
          var pil=posBox(0.7,H,0.7,ppx+pox,H/2,ppz+poz,piMat); pil.castShadow=true; scene.add(pil); } }
      if(bAxis==="z"){ if(isLand(landAt(px,py-1))) makeRamp(c.x,c.z-dd/2-L/2,0,L,W,H); if(isLand(landAt(px,py+1))) makeRamp(c.x,c.z+dd/2+L/2,Math.PI,L,W,H); }
      else { if(isLand(landAt(px-1,py))) makeRamp(c.x-dw/2-L/2,c.z,Math.PI/2,L,W,H); if(isLand(landAt(px+1,py))) makeRamp(c.x+dw/2+L/2,c.z,-Math.PI/2,L,W,H); }
    } else if(terr===7){ // drawbridge — bascule bridge matching the Mega World crossing, but per-plate
      var axis=(isLand(landAt(px,py-1))||isLand(landAt(px,py+1)))?"z":"x";
      var GAP=9, Wd=16, HH=6.5, KICK=3.0, KL=10, APR=26, Lp=KL*(HH+KICK)/KICK;
      function toXZ(u,v){ return axis==="z" ? {x:c.x+v, z:c.z+u} : {x:c.x+u, z:c.z+v}; }
      function locBox(w,dOrH,dep,mat){ return axis==="z" ? new THREE.BoxGeometry(w,dOrH,dep) : new THREE.BoxGeometry(dep,dOrH,w); }
      // faint water under the span
      var wU=new THREE.Mesh(new THREE.PlaneGeometry(PT,PT),new THREE.MeshStandardMaterial({color:0x184e6c,roughness:0.3,metalness:0.4,transparent:true,opacity:0.85}));
      wU.rotation.x=-Math.PI/2; wU.position.set(c.x,0.05,c.z); scene.add(wU);
      var deckMat=new THREE.MeshStandardMaterial({color:0x3a414d,roughness:0.7,metalness:0.4});
      var pierMat=new THREE.MeshStandardMaterial({color:0x4a525e,roughness:0.8,metalness:0.3});
      var towerMat=new THREE.MeshStandardMaterial({color:0x586271,roughness:0.7,metalness:0.5});
      var cableMat=new THREE.MeshStandardMaterial({color:0x20262f});
      var warnMat=new THREE.MeshStandardMaterial({color:0xff3b30,emissive:0xff3b30,emissiveIntensity:0.2});
      var nP=toXZ(GAP,0), fP=toXZ(-GAP,0);
      var nearGroup=new THREE.Group(); nearGroup.position.set(nP.x,HH,nP.z); scene.add(nearGroup);
      var farGroup=new THREE.Group(); farGroup.position.set(fP.x,HH,fP.z); scene.add(farGroup);
      var nl=new THREE.Mesh(locBox(Wd,0.4,GAP),deckMat), fl=new THREE.Mesh(locBox(Wd,0.4,GAP),deckMat);
      if(axis==="z"){ nl.position.set(0,0,-GAP/2); fl.position.set(0,0,GAP/2); } else { nl.position.set(-GAP/2,0,0); fl.position.set(GAP/2,0,0); }
      nl.castShadow=true; nl.receiveShadow=true; fl.castShadow=true; fl.receiveShadow=true; nearGroup.add(nl); farGroup.add(fl);
      // side rails on each leaf
      function railPair(grp,sign){ var r1=new THREE.Mesh(locBox(0.3,0.9,GAP),pierMat), r2=new THREE.Mesh(locBox(0.3,0.9,GAP),pierMat);
        if(axis==="z"){ r1.position.set(Wd/2,0.4,sign*GAP/2); r2.position.set(-Wd/2,0.4,sign*GAP/2); } else { r1.position.set(sign*GAP/2,0.4,Wd/2); r2.position.set(sign*GAP/2,0.4,-Wd/2); }
        grp.add(r1); grp.add(r2); }
      railPair(nearGroup,-1); railPair(farGroup,1);
      // towers + cross-beam + warning light + cables
      var towerH=13;
      for(var ta1=-1;ta1<=1;ta1+=2) for(var tb1=-1;tb1<=1;tb1+=2){ var tPos=toXZ(ta1*GAP, tb1*(Wd/2+0.9)); scene.add(posBox(1.6,towerH,1.6,tPos.x,towerH/2,tPos.z,towerMat)); }
      var beamN=toXZ(GAP,0), beamF=toXZ(-GAP,0);
      if(axis==="z"){ scene.add(posBox(Wd+3,1.0,1.4,beamN.x,towerH+HH,beamN.z,towerMat)); scene.add(posBox(Wd+3,1.0,1.4,beamF.x,towerH+HH,beamF.z,towerMat)); }
      else { scene.add(posBox(1.4,1.0,Wd+3,beamN.x,towerH+HH,beamN.z,towerMat)); scene.add(posBox(1.4,1.0,Wd+3,beamF.x,towerH+HH,beamF.z,towerMat)); }
      var warn=posBox(1.0,0.5,1.0,c.x,towerH+HH+1.2,c.z,warnMat); scene.add(warn);
      for(var cbs=-1;cbs<=1;cbs+=2){ var cp1=toXZ(0,cbs*(Wd/2-0.4)); if(axis==="z") scene.add(posBox(0.16,0.16,GAP*2.2,cp1.x,towerH+HH-1,cp1.z,cableMat)); else scene.add(posBox(GAP*2.2,0.16,0.16,cp1.x,towerH+HH-1,cp1.z,cableMat)); }
      // approach ramps up to the pivots (reuse existing ramp/deck systems)
      if(axis==="z"){ if(isLand(landAt(px,py-1))) makeRamp(c.x,c.z-PT/2-APR/2,0,APR,Wd,HH); if(isLand(landAt(px,py+1))) makeRamp(c.x,c.z+PT/2+APR/2,Math.PI,APR,Wd,HH); }
      else { if(isLand(landAt(px-1,py))) makeRamp(c.x-PT/2-APR/2,c.z,Math.PI/2,APR,Wd,HH); if(isLand(landAt(px+1,py))) makeRamp(c.x+PT/2+APR/2,c.z,-Math.PI/2,APR,Wd,HH); }
      // gameplay: deck support while closed, launch ramps active only while open (matches Mega World)
      var liftDeck={x:c.x,z:c.z,w:(axis==="z"?Wd:PT),d:(axis==="z"?PT:Wd),h:HH}; decks.push(liftDeck);
      var kN=toXZ(GAP+Lp/2,0), kF=toXZ(-(GAP+Lp/2),0);
      var nearKick={x:kN.x,z:kN.z,yaw:(axis==="z"?Math.PI:-Math.PI/2),L:Lp,Wd:Wd,H:0}; ramps.push(nearKick);
      var farKick ={x:kF.x,z:kF.z,yaw:(axis==="z"?0:Math.PI/2),      L:Lp,Wd:Wd,H:0}; ramps.push(farKick);
      customDraws.push({axis:axis,cx:c.x,cz:c.z,GAP:GAP,Wd:Wd,baseH:HH,openH:HH+KICK,openAngle:0.95,
        nearGroup:nearGroup,farGroup:farGroup,liftDeck:liftDeck,nearKick:nearKick,farKick:farKick,warn:warn,
        t:Math.random()*15,angle:0,wasOpen:false});
    } else {
      patch(c.x,c.z,PT,GC[terr]||0x222222, 0.02);
      if(terr===5){ box(PT*0.5,9,PT*0.5,c.x,4.5,c.z,0x232c3a,true); box(PT*0.56,1.5,PT*0.56,c.x,9.4,c.z,0x2b6fd6,false); }
      else if(terr===9){ box(PT*0.7,0.3,PT*0.7,c.x,0.18,c.z,0x3a4048,false);                          // harbor dock decking
        var craneMat=new THREE.MeshStandardMaterial({color:0xd8a51e,roughness:0.7,metalness:0.4});
        var cr=new THREE.Group(); cr.position.set(c.x+PT*0.28,0,c.z-PT*0.28); scene.add(cr);
        var leg=new THREE.Mesh(new THREE.BoxGeometry(0.8,14,0.8),craneMat); leg.position.y=7; leg.castShadow=true; cr.add(leg);
        var arm=new THREE.Mesh(new THREE.BoxGeometry(0.6,0.6,16),craneMat); arm.position.set(0,13.5,6); arm.castShadow=true; cr.add(arm); }
      else if(terr===8){ for(var isc=0;isc<3;isc++) icerock(c.x+rand(-PT*0.35,PT*0.35), c.z+rand(-PT*0.35,PT*0.35)); }
    }
  }
  function subPos(c,sx,sy){ return { x:c.x+(sx-(IW-1)/2)*ST, z:c.z+(sy-(IW-1)/2)*ST }; }
  var rotm=data.rot||{};
  for(var key in inter){ var pidx=parseInt(key,10); var ia=inter[key]; if(!ia) continue; var ra=rotm[key];
    px=pidx%PW; py=(pidx/PW)|0; var c2=plateCenter(px,py);
    for(sy=0;sy<IW;sy++) for(sx=0;sx<IW;sx++){ var fc=ia[sy*IW+sx]; if(!fc) continue; var q=subPos(c2,sx,sy);
      var rv=ra?ra[sy*IW+sx]:0, ang=rv*Math.PI/2, odd=(rv%2===1);
      if(fc===1){ var rg=new THREE.Group(); rg.position.set(q.x,0.07,q.z); rg.rotation.y=ang;
        var rdm=new THREE.Mesh(new THREE.PlaneGeometry(ST,ST),new THREE.MeshStandardMaterial({color:0x34383f,roughness:0.95})); rdm.rotation.x=-Math.PI/2; rdm.receiveShadow=true; rg.add(rdm);
        var dlMat=new THREE.MeshStandardMaterial({color:0xd8c24a,emissive:0x2a2206,roughness:0.7});
        for(var dd=-ST/2+1.6; dd<ST/2-1; dd+=2.4){ var dash=new THREE.Mesh(new THREE.PlaneGeometry(0.5,1.2),dlMat); dash.rotation.x=-Math.PI/2; dash.position.set(0,0.02,dd); rg.add(dash); }
        scene.add(rg); }
      else if(fc===7){ var rg2=new THREE.Group(); rg2.position.set(q.x,0.07,q.z); rg2.rotation.y=ang;                 // Avenue — solid double yellow, no-passing
        var rdm2=new THREE.Mesh(new THREE.PlaneGeometry(ST,ST),new THREE.MeshStandardMaterial({color:0x2e333c,roughness:0.95})); rdm2.rotation.x=-Math.PI/2; rdm2.receiveShadow=true; rg2.add(rdm2);
        var dlMat2=new THREE.MeshStandardMaterial({color:0xd8c24a,emissive:0x2a2206,roughness:0.7});
        var l1=new THREE.Mesh(new THREE.PlaneGeometry(0.35,ST-1),dlMat2); l1.rotation.x=-Math.PI/2; l1.position.set(-0.4,0.02,0); rg2.add(l1);
        var l2=new THREE.Mesh(new THREE.PlaneGeometry(0.35,ST-1),dlMat2); l2.rotation.x=-Math.PI/2; l2.position.set(0.4,0.02,0); rg2.add(l2);
        scene.add(rg2); }
      else if(fc===2){ tower(q.x,q.z); }
      else if(fc===3){ tree(q.x,q.z); }
      else if(fc===4){ boulder(q.x,q.z); }
      else if(fc===5){ mesa(q.x,q.z); }
      else if(fc===6){ var g=new THREE.Group(); g.position.set(q.x,0,q.z); g.rotation.y=ang;                 // real ballast + wooden ties + metal rails, not just a flat strip
        var ballastMat=new THREE.MeshStandardMaterial({color:0x262a31,roughness:0.95});
        var bed=new THREE.Mesh(new THREE.PlaneGeometry(ST*0.78,ST),ballastMat); bed.rotation.x=-Math.PI/2; bed.position.y=0.04; g.add(bed);
        var tieMat=new THREE.MeshStandardMaterial({color:0x3a2c1c,roughness:1});
        for(var tt=-ST/2+0.8; tt<ST/2; tt+=1.6){ var tie=new THREE.Mesh(new THREE.BoxGeometry(ST*0.6,0.16,0.9),tieMat); tie.position.set(0,0.1,tt); g.add(tie); }
        var railMat=new THREE.MeshStandardMaterial({color:0x8a9098,roughness:0.35,metalness:0.85});
        for(var rl=-1;rl<=1;rl+=2){ var rail=new THREE.Mesh(new THREE.BoxGeometry(0.16,0.22,ST),railMat); rail.position.set(rl*1.5,0.2,0); g.add(rail); }
        scene.add(g); }
      else if(fc===8){ container(q.x,q.z); }
      else if(fc===9){ barrier(q.x,q.z); }
      else if(fc===10){ var bh=1.4, dw=ST, dd=ST;                                          // Bridge Deck — a real drivable deck over water, with solid side rails
        var bdMat=new THREE.MeshStandardMaterial({color:0x3a414d,roughness:0.7,metalness:0.3});
        var bdm=new THREE.Mesh(new THREE.BoxGeometry(dw,0.4,dd),bdMat); bdm.position.set(q.x,bh,q.z); bdm.rotation.y=ang; bdm.castShadow=true; bdm.receiveShadow=true; scene.add(bdm);
        var railMat=new THREE.MeshStandardMaterial({color:0x2a2f38,roughness:0.8});
        var r1=new THREE.Mesh(new THREE.BoxGeometry(0.5,0.9,dd),railMat); var r2=r1.clone();
        r1.position.set(q.x-dw/2+0.3,bh+0.65,q.z); r1.rotation.y=ang; r1.castShadow=true; scene.add(r1);
        r2.position.set(q.x+dw/2-0.3,bh+0.65,q.z); r2.rotation.y=ang; scene.add(r2);
        var vw=odd?dd:dw, vd=odd?dw:dd;
        decks.push({x:q.x,z:q.z,w:vw,d:vd,h:bh});
        if(!odd){ aabbs.push({minX:q.x-dw/2-0.3,maxX:q.x-dw/2+0.3,minZ:q.z-dd/2,maxZ:q.z+dd/2}); aabbs.push({minX:q.x+dw/2-0.3,maxX:q.x+dw/2+0.3,minZ:q.z-dd/2,maxZ:q.z+dd/2}); }
        else { aabbs.push({minX:q.x-dd/2,maxX:q.x+dd/2,minZ:q.z-dw/2-0.3,maxZ:q.z-dw/2+0.3}); aabbs.push({minX:q.x-dd/2,maxX:q.x+dd/2,minZ:q.z+dw/2-0.3,maxZ:q.z+dw/2+0.3}); } }
      else if(fc===11){ var lh=1.4, ldw=ST, ldd=ST;                                        // Lift Span — a small drawbridge section that opens on a cycle
        var lMat=new THREE.MeshStandardMaterial({color:0x9a8a3a,roughness:0.7,metalness:0.3});
        var lm=new THREE.Mesh(new THREE.BoxGeometry(ldw,0.4,ldd),lMat); lm.position.set(q.x,lh,q.z); lm.rotation.y=ang; lm.castShadow=true; lm.receiveShadow=true; scene.add(lm);
        var towerMat=new THREE.MeshStandardMaterial({color:0x586271,roughness:0.7,metalness:0.5});
        var t1=new THREE.Mesh(new THREE.BoxGeometry(0.7,6,0.7),towerMat), t2=t1.clone();
        t1.position.set(q.x-ldw/2+0.4,3,q.z-ldd/2+0.4); t2.position.set(q.x+ldw/2-0.4,3,q.z+ldd/2-0.4);
        t1.castShadow=true; t2.castShadow=true; scene.add(t1); scene.add(t2);
        var warn=new THREE.Mesh(new THREE.SphereGeometry(0.35,10,8),new THREE.MeshStandardMaterial({color:0xff3b30,emissive:0xff3b30,emissiveIntensity:0.2}));
        warn.position.set(q.x,6.4,q.z); scene.add(warn);
        var lvw=odd?ldd:ldw, lvd=odd?ldw:ldd;
        var liftDeck={x:q.x,z:q.z,w:lvw,d:lvd,h:lh}; decks.push(liftDeck);
        liftSpans.push({cx:q.x,cz:q.z,baseH:lh,angle:0,t:Math.random()*12,mesh:lm,deckRef:liftDeck,warn:warn}); }
    }
  }
  // ---- moving train along whatever railroad tiles were drawn ----
  customTrain=null;
  var railPts=[];
  for(var rky in inter){ var rpidx=parseInt(rky,10), ria=inter[rky]; if(!ria) continue; var rpx=rpidx%PW, rpy=(rpidx/PW)|0, rcc=plateCenter(rpx,rpy);
    for(var rsy=0;rsy<IW;rsy++) for(var rsx=0;rsx<IW;rsx++){ if(ria[rsy*IW+rsx]===6){ var rq=subPos(rcc,rsx,rsy); railPts.push({x:rq.x,z:rq.z}); } } }
  if(railPts.length>=6){
    var radj=[]; for(var ri=0;ri<railPts.length;ri++) radj.push([]);
    var rthr=ST*1.6;
    for(var ra=0;ra<railPts.length;ra++) for(var rb=ra+1;rb<railPts.length;rb++){ var rdx=railPts[ra].x-railPts[rb].x, rdz=railPts[ra].z-railPts[rb].z; if(Math.hypot(rdx,rdz)<rthr){ radj[ra].push(rb); radj[rb].push(ra); } }
    var rseen=new Array(railPts.length), rcomps=[];
    for(var rs=0;rs<railPts.length;rs++){ if(rseen[rs]) continue; var rstack=[rs], rcomp=[]; rseen[rs]=true;
      while(rstack.length){ var rcu=rstack.pop(); rcomp.push(rcu); for(var rn=0;rn<radj[rcu].length;rn++){ var rnx=radj[rcu][rn]; if(!rseen[rnx]){ rseen[rnx]=true; rstack.push(rnx); } } }
      rcomps.push(rcomp); }
    rcomps.sort(function(a,b){ return b.length-a.length; });
    var rbest=rcomps[0];
    if(rbest && rbest.length>=6){
      var rstartNode=rbest[0];
      for(var rbi=0;rbi<rbest.length;rbi++){ if(radj[rbest[rbi]].length===1){ rstartNode=rbest[rbi]; break; } }
      var rvisited={}, rorder=[rstartNode]; rvisited[rstartNode]=true; var rcur=rstartNode;
      while(rorder.length<rbest.length){
        var rnbrs=radj[rcur].filter(function(n){ return rbest.indexOf(n)>=0 && !rvisited[n]; });
        if(!rnbrs.length) break;
        rnbrs.sort(function(a,b){ return Math.hypot(railPts[a].x-railPts[rcur].x,railPts[a].z-railPts[rcur].z)-Math.hypot(railPts[b].x-railPts[rcur].x,railPts[b].z-railPts[rcur].z); });
        var rnxt=rnbrs[0]; rvisited[rnxt]=true; rorder.push(rnxt); rcur=rnxt;
      }
      var rpath=rorder.map(function(i){ return railPts[i]; });
      var rclosed=(Math.hypot(rpath[0].x-rpath[rpath.length-1].x, rpath[0].z-rpath[rpath.length-1].z)<rthr*1.3) && rpath.length>=8;
      var rcars=[makeSimpleTrainCar(true)]; for(var rw=0;rw<2;rw++) rcars.push(makeSimpleTrainCar(false));
      var rcum=buildPathCum(rpath,rclosed);
      customTrain={path:rpath,cum:rcum,total:rcum[rcum.length-1],closed:rclosed,cars:rcars,dist:0,speed:9};
    }
  }
  // spawns on open drivable sub-cells
  var open=[];
  for(py=0;py<PW;py++) for(px=0;px<PW;px++){ var tt=warr[py*PW+px]; if(!tt||tt===1||tt===6||tt===7) continue; var cc=plateCenter(px,py); var ia2=inter[py*PW+px];
    for(sy=0;sy<IW;sy++) for(sx=0;sx<IW;sx++){ var f2=ia2?ia2[sy*IW+sx]:0; if(!f2||f2===1||f2===6||f2===7||f2===10||f2===11) open.push(subPos(cc,sx,sy)); } }
  if(open.length>=2){ customSpawnP=open[0]; customSpawnR=open[open.length-1]; }
  else { customSpawnP={x:0,z:Math.min(20,CITY-20)}; customSpawnR={x:0,z:-Math.min(20,CITY-20)}; }
  if(!activeCustomBase){ roadNodes=[]; roadAdj=[]; }
  for(var ky in inter){ var pp=parseInt(ky,10), ar=inter[ky]; if(!ar) continue; var ppx=pp%PW, ppy=(pp/PW)|0, pcc=plateCenter(ppx,ppy);
    for(var s2=0;s2<IW;s2++) for(var s1=0;s1<IW;s1++){ var fv=ar[s2*IW+s1]; if(fv===1||fv===7||fv===10||fv===11){ var qq=subPos(pcc,s1,s2); roadNodes.push({x:qq.x,z:qq.z,forest:false,bridge:(fv===10||fv===11)}); } } }
  for(py=0;py<PW;py++) for(px=0;px<PW;px++){ if(warr[py*PW+px]===6||warr[py*PW+px]===7){ var bcc=plateCenter(px,py); roadNodes.push({x:bcc.x,z:bcc.z,forest:false,bridge:true}); } }
  for(var na=bBaseNodeCount;na<roadNodes.length;na++) roadAdj[na]=[];               // only reset the NEW plate nodes — leave the base map's graph untouched
  for(var ga=0;ga<roadNodes.length;ga++){ for(var gb=Math.max(ga+1,bBaseNodeCount);gb<roadNodes.length;gb++){ var ddx=roadNodes[ga].x-roadNodes[gb].x, ddz=roadNodes[ga].z-roadNodes[gb].z, dd=Math.hypot(ddx,ddz); var thr=(roadNodes[ga].bridge||roadNodes[gb].bridge)?PT*0.7:ST*1.5; if(dd>0.5&&dd<thr){ roadAdj[ga].push(gb); roadAdj[gb].push(ga); } } }
  var padMat=new THREE.MeshStandardMaterial({color:0x2e333c,roughness:0.95});                 // round every real turn/junction, like the Mega World does
  for(var pd=bBaseNodeCount; pd<roadNodes.length; pd++){ if(roadAdj[pd].length>=2 && !roadNodes[pd].bridge){
    var pad=new THREE.Mesh(new THREE.CylinderGeometry(ST*0.55,ST*0.55,0.06,20),padMat); pad.position.set(roadNodes[pd].x,0.075,roadNodes[pd].z); pad.receiveShadow=true; scene.add(pad); } }
}
function playCustomWorld(){
  var minx=PW,miny=PW,maxx=-1,maxy=-1, px,py;
  for(py=0;py<PW;py++) for(px=0;px<PW;px++){ var i=py*PW+px; if(world[i]||(interiors[i]&&!bInterEmpty(i))){ if(px<minx)minx=px; if(px>maxx)maxx=px; if(py<miny)miny=py; if(py>maxy)maxy=py; } }
  if(maxx<0 && !bBuilderBase){ alert("Place some plates first!"); return; }
  var ST=10, PT=IW*ST, mid=Math.floor(PW/2), cxm, cym, sizeVal;
  if(bBuilderBase){
    cxm=mid; cym=mid;                                                                    // keep the plate grid anchored to the real map's fixed origin
    var farX=0, farZ=0;
    if(maxx>=0){ farX=Math.max(farX,Math.abs(minx-mid),Math.abs(maxx-mid)); farZ=Math.max(farZ,Math.abs(miny-mid),Math.abs(maxy-mid)); }
    var baseMin=(bBuilderBase==="mega")?420:80;                                          // exact real size when there's no expansion yet
    sizeVal=Math.max(baseMin, farX*PT+40, farZ*PT+40);
  } else {
    cxm=(minx+maxx)/2; cym=(miny+maxy)/2;
    var usedX=maxx-minx+1, usedZ=maxy-miny+1;
    sizeVal=Math.max(45, Math.max(usedX,usedZ)*PT/2 + 8);
  }
  var wcopy=new Uint8Array(world), icopy={}, rcopy={}; for(var k in interiors){ if(!bInterEmpty(k)){ icopy[k]=new Uint8Array(interiors[k]); rcopy[k]=new Uint8Array(bInteriorRot(k)); } }
  customWorld={world:wcopy,interiors:icopy,rot:rcopy}; customMeta={cxm:cxm,cym:cym,ST:ST,PT:PT};
  activeCustomBase=bBuilderBase;
  MAPS.custom.size=sizeVal;
  map="custom"; mode="chase"; role="cop"; chopperMode=false; onDutyMode=false;
  bStopSpin(); document.getElementById("builder").classList.add("hidden");
  startGame();
}
function buildWorld(){
  var mp=MAPS[map]||MAPS.city;
  CITY = mp.size || 80;                  // bigger world for the mega map
  WEDGE = (map==="world") ? 820 : CITY;  // where spawns/objectives actually anchor — pinned to the original island size on the Mega World
  mapGrip=mp.grip||1;
  scene.background = new THREE.Color(map==="world" ? 0x8ec6f0 : mp.bg);
  scene.fog = (map==="world") ? null : new THREE.Fog(mp.fog, mp.near, mp.far);
  if(map!=="custom" || activeCustomBase){ buildGround(mp.ground); buildWalls(mp.wall); }
  if(map==="desert") buildDesert();
  else if(map==="plain") buildPlain();
  else if(map==="ice") buildIce();
  else if(map==="highway") buildHighway();
  else if(map==="harbor") buildHarbor();
  else if(map==="world") buildMegaWorld();
  else if(map==="crossing") buildCrossing();
  else if(map==="custom") buildCustomWorld();
  else buildCity();                       // city + rain share the city layout
  buildWeather(mp.weather||null);
  buildSpatialIndex();   // one final rebuild so gameplay starts fully up to date, not stale by up to 40 entries
}

function addRings(bx,bz,rad,ht,mat,seg){
  for(var f=3; f<ht-1; f+=3.2){
    var ring=new THREE.Mesh(new THREE.TorusGeometry(rad,0.12,6,seg), mat);
    ring.rotation.x=Math.PI/2; ring.position.set(bx,f,bz); scene.add(ring);
  }
}

function buildCity(){
  var roadMat=new THREE.MeshStandardMaterial({ color:0x171c26, roughness:0.9 });
  var pitch=24, half=CITY;
  for(var x=-half; x<=half; x+=pitch){
    var v=new THREE.Mesh(new THREE.PlaneGeometry(9, CITY*2), roadMat);
    v.rotation.x=-Math.PI/2; v.position.set(x,0.01,0); scene.add(v);
    var hh=new THREE.Mesh(new THREE.PlaneGeometry(CITY*2, 9), roadMat);
    hh.rotation.x=-Math.PI/2; hh.position.set(0,0.01,x); scene.add(hh);
  }
  var palette=[0x1d2433,0x232033,0x1b2a2a,0x2c2630,0x202a3a,0x26314a];
  var winMat=new THREE.MeshStandardMaterial({color:0xffd27a,emissive:0xffb74d,emissiveIntensity:0.55});
  var spawnSafe=[{x:0,z:CITY-14,r:14},{x:0,z:-CITY+18,r:18}];
  for(var bx=-half+pitch/2; bx<half; bx+=pitch){
    for(var bz=-half+pitch/2; bz<half; bz+=pitch){
      if(Math.random()<0.12) continue;
      var safe=false;
      for(var s=0;s<spawnSafe.length;s++){ if(Math.hypot(bx-spawnSafe[s].x,bz-spawnSafe[s].z)<spawnSafe[s].r){safe=true;break;} }
      if(safe) continue;
      var rad=rand(5,7), ht=rand(7,24), col=palette[(Math.random()*palette.length)|0];
      var mat=new THREE.MeshStandardMaterial({color:col,roughness:0.8,metalness:0.12});
      var kind=Math.random();
      if(kind<0.34){
        var m=new THREE.Mesh(new THREE.CylinderGeometry(rad,rad*1.05,ht,24),mat);
        m.position.set(bx,ht/2,bz); m.castShadow=true; m.receiveShadow=true; scene.add(m);
        addRings(bx,bz,rad*1.01,ht,winMat,24);
      } else if(kind<0.60){
        var m2=new THREE.Mesh(new THREE.CylinderGeometry(rad*0.55,rad,ht,8),mat);
        m2.rotation.y=rand(0,1); m2.position.set(bx,ht/2,bz); m2.castShadow=true; m2.receiveShadow=true; scene.add(m2);
        addRings(bx,bz,rad*0.85,ht,winMat,8);
      } else if(kind<0.82){
        var m3=new THREE.Mesh(new THREE.CylinderGeometry(rad,rad,ht,6),mat);
        m3.rotation.y=rand(0,1); m3.position.set(bx,ht/2,bz); m3.castShadow=true; m3.receiveShadow=true; scene.add(m3);
        addRings(bx,bz,rad*0.98,ht,winMat,6);
      } else {
        var tiers=2+(Math.random()<0.5?1:0), y=0, rr=rad;
        for(var t=0;t<tiers;t++){
          var th=ht/tiers, sg=(Math.random()<0.5?24:6);
          var mt=new THREE.Mesh(new THREE.CylinderGeometry(rr*0.85,rr,th,sg),mat);
          mt.position.set(bx,y+th/2,bz); mt.castShadow=true; mt.receiveShadow=true; scene.add(mt);
          y+=th; rr*=0.78;
        }
        addRings(bx,bz,rad*0.82,ht,winMat,18);
      }
      if(Math.random()<0.3){
        var dome=new THREE.Mesh(new THREE.SphereGeometry(rad*0.55,16,10,0,Math.PI*2,0,Math.PI/2),mat);
        dome.position.set(bx,ht,bz); dome.castShadow=true; scene.add(dome);
      }
      circles.push({x:bx,z:bz,r:rad});
      buildings.push({x:bx,z:bz,w:rad*2,d:rad*2});
    }
  }
  for(var i=0;i<10;i++){ var lx=rand(-half+10,half-10), lz=rand(-half+10,half-10); var pl=new THREE.PointLight(0xffd9a0,0.7,26,2); pl.position.set(lx,7,lz); scene.add(pl); }
}

function buildDesert(){
  var half=CITY, rockCols=[0xb07a4a,0x9c6b3e,0xc28a55,0x8a5d34];
  var sun=new THREE.DirectionalLight(0xffce8a,0.5); sun.position.set(40,55,25); scene.add(sun);
  function clearSpawn(x,z){ return Math.hypot(x,z-(CITY-14))<16 || Math.hypot(x,z+(CITY-18))<20; }
  // mesas / buttes
  for(var i=0;i<16;i++){
    var x=rand(-half+8,half-8), z=rand(-half+8,half-8); if(clearSpawn(x,z)) continue;
    var rad=rand(4,8), ht=rand(5,16);
    var mat=new THREE.MeshStandardMaterial({color:rockCols[(Math.random()*rockCols.length)|0],roughness:1.0,metalness:0,flatShading:true});
    var m=new THREE.Mesh(new THREE.CylinderGeometry(rad*rand(0.5,0.8),rad,ht,(rand(5,8)|0)),mat);
    m.position.set(x,ht/2,z); m.castShadow=true; m.receiveShadow=true; scene.add(m);
    circles.push({x:x,z:z,r:rad}); buildings.push({x:x,z:z,w:rad*2,d:rad*2});
  }
  // boulders
  for(var b=0;b<22;b++){
    var rx=rand(-half+6,half-6), rz=rand(-half+6,half-6); if(clearSpawn(rx,rz)) continue;
    var br=rand(1.2,2.8);
    var bm=new THREE.Mesh(new THREE.IcosahedronGeometry(br,0), new THREE.MeshStandardMaterial({color:rockCols[(Math.random()*rockCols.length)|0],roughness:1,flatShading:true}));
    bm.position.set(rx,br*0.7,rz); bm.rotation.set(rand(0,3),rand(0,3),rand(0,3)); bm.castShadow=true; scene.add(bm);
    circles.push({x:rx,z:rz,r:br}); buildings.push({x:rx,z:rz,w:br*2,d:br*2});
  }
  // cacti (decor)
  for(var c=0;c<14;c++){
    var cx=rand(-half+6,half-6), cz=rand(-half+6,half-6); if(clearSpawn(cx,cz)) continue;
    var ch=rand(2.5,4.2), cm=new THREE.MeshStandardMaterial({color:0x3f6b3a,roughness:0.9});
    var trunk=new THREE.Mesh(new THREE.CylinderGeometry(0.4,0.5,ch,10),cm); trunk.position.set(cx,ch/2,cz); trunk.castShadow=true; scene.add(trunk);
    var cap=new THREE.Mesh(new THREE.SphereGeometry(0.4,10,8),cm); cap.position.set(cx,ch,cz); scene.add(cap);
    if(Math.random()<0.7){ var arm=new THREE.Mesh(new THREE.CylinderGeometry(0.22,0.25,ch*0.4,8),cm); arm.position.set(cx+0.5,ch*0.65,cz); scene.add(arm); }
  }
}

function buildPlain(){
  var half=CITY;
  var sun=new THREE.DirectionalLight(0xdfeaff,0.35); sun.position.set(-30,50,30); scene.add(sun);
  function clearSpawn(x,z){ return Math.hypot(x,z-(CITY-14))<14 || Math.hypot(x,z+(CITY-18))<18; }
  for(var i=0;i<28;i++){
    var x=rand(-half+6,half-6), z=rand(-half+6,half-6); if(clearSpawn(x,z)) continue;
    var th=rand(3,5);
    var trunk=new THREE.Mesh(new THREE.CylinderGeometry(0.35,0.5,th,8), new THREE.MeshStandardMaterial({color:0x5a3b22,roughness:1}));
    trunk.position.set(x,th/2,z); trunk.castShadow=true; scene.add(trunk);
    var folMat=new THREE.MeshStandardMaterial({color:(Math.random()<0.5?0x2f6b34:0x356f2a),roughness:0.95});
    var fol=new THREE.Mesh(new THREE.SphereGeometry(rand(1.6,2.4),12,10), folMat); fol.position.set(x,th+1.0,z); fol.castShadow=true; scene.add(fol);
    circles.push({x:x,z:z,r:0.9}); buildings.push({x:x,z:z,w:2,d:2});
  }
  for(var b=0;b<8;b++){
    var rx=rand(-half+6,half-6), rz=rand(-half+6,half-6); if(clearSpawn(rx,rz)) continue;
    var br=rand(1.4,2.4);
    var bm=new THREE.Mesh(new THREE.IcosahedronGeometry(br,0), new THREE.MeshStandardMaterial({color:0x6b7280,roughness:1,flatShading:true}));
    bm.position.set(rx,br*0.6,rz); bm.castShadow=true; scene.add(bm);
    circles.push({x:rx,z:rz,r:br}); buildings.push({x:rx,z:rz,w:br*2,d:br*2});
  }
}

// ----------------------------------------------------------------------------
//  Weather + extra maps
// ----------------------------------------------------------------------------
function mPos(geo,mat,x,y,z){ var m=new THREE.Mesh(geo,mat); m.position.set(x,y,z); return m; }
function tower(x,z){
  var w=rand(5,10), h=rand(13,40);
  if(Math.random()<0.6){                                   // rounded glass tower with a domed crown
    var r=w/2;
    addInstance("twrCylGlass",pGeo("cyl"),pMat("twrGlass"),x,h/2,z,r,h,r);
    addInstance("twrCylMat",pGeo("cyl"),pMat("twrMat"),x,h+h*0.05,z,r*0.85,h*0.10,r*0.85);
    addInstance("twrDome",pGeo("domeHalf"),pMat("twrMat"),x,h+h*0.10,z,r*0.72,r*0.72,r*0.72);
    circles.push({x:x,z:z,r:r});
  } else {                                                 // setback skyscraper with a rounded roof
    var lvls=2+(Math.random()*2|0), bw=w, by=0;
    for(var l=0;l<lvls;l++){ var lh=(h/lvls)*(1-l*0.10);
      if(l%2) addInstance("twrBoxGlass",pGeo("box"),pMat("twrGlass"),x,by+lh/2,z,bw,lh,bw);
      else addInstance("twrBoxMat",pGeo("box"),pMat("twrMat"),x,by+lh/2,z,bw,lh,bw);
      by+=lh; bw*=0.76; }
    addInstance("twrCylMat",pGeo("cyl"),pMat("twrMat"),x,by+h*0.025,z,bw*0.5,h*0.05,bw*0.5);
    aabbs.push({minX:x-w/2,maxX:x+w/2,minZ:z-w/2,maxZ:z+w/2});
  }
  if(Math.random()<0.5) addInstance("twrCylMat",pGeo("cyl"),pMat("twrMat"),x,h*1.13,z,0.13,h*0.22,0.13);
  buildings.push({x:x,z:z,w:w,d:w});
  return w;
}
// ---- shared geometry/material caches for scattered props (one GPU buffer each, instead of thousands) ----
var P_GEO={ trunk:null, foliage:null, rock:null, mesa:null, box:null };
var P_MAT={ trunk:null, foliage:null, boulder:null, mesa:null, ice:null, barrier:null, cont:{} };
function pGeo(k){ if(!P_GEO.trunk){ P_GEO.trunk=new THREE.CylinderGeometry(0.38,0.55,1,7); P_GEO.foliage=new THREE.SphereGeometry(1,10,8); P_GEO.rock=new THREE.IcosahedronGeometry(1,0); P_GEO.mesa=new THREE.CylinderGeometry(0.8,1,1,7); P_GEO.box=new THREE.BoxGeometry(1,1,1); P_GEO.pad=new THREE.CylinderGeometry(1,1,1,24); P_GEO.cyl=new THREE.CylinderGeometry(1,1,1,18); P_GEO.domeHalf=new THREE.SphereGeometry(1,18,10,0,6.2832,0,Math.PI/2); for(var g in P_GEO) if(P_GEO[g]) P_GEO[g].shared=true; } return P_GEO[k]; }
function roadPad(x,z,mat,r){ addInstance("roadPad",pGeo("pad"),mat,x,0.05,z,r||7.5,0.05,r||7.5,0,0,0); }
function pMat(k,col){ if(!P_MAT.trunk){ P_MAT.trunk=new THREE.MeshStandardMaterial({color:0x4a3a2c,roughness:1}); P_MAT.foliage=new THREE.MeshStandardMaterial({color:0x2f5d2a,roughness:0.9}); P_MAT.boulder=new THREE.MeshStandardMaterial({color:0x8a6a44,roughness:1,flatShading:true}); P_MAT.mesa=new THREE.MeshStandardMaterial({color:0x9c6b3a,roughness:0.95,flatShading:true}); P_MAT.ice=new THREE.MeshStandardMaterial({color:0xbfe0f0,roughness:0.3,flatShading:true,transparent:true,opacity:0.92}); P_MAT.barrier=new THREE.MeshStandardMaterial({color:0x2a2f3a,roughness:0.9}); P_MAT.twrMat=new THREE.MeshStandardMaterial({color:0x161e2c,roughness:0.6,metalness:0.25,emissive:0x0a1830,emissiveIntensity:0.1}); P_MAT.twrGlass=new THREE.MeshStandardMaterial({color:0x223149,roughness:0.32,metalness:0.55,emissive:0x16356e,emissiveIntensity:0.15}); }
  if(k==="cont"){ if(!P_MAT.cont[col]){ P_MAT.cont[col]=new THREE.MeshStandardMaterial({color:col,roughness:0.7,metalness:0.2}); P_MAT.cont[col].shared=true; } return P_MAT.cont[col]; } if(!P_MAT[k].shared){ for(var mk in P_MAT) if(P_MAT[mk]&&P_MAT[mk].isMaterial) P_MAT[mk].shared=true; } return P_MAT[k]; }
var instPools={}, INST_CAP=16000, _instDummy=new THREE.Object3D();
function addInstance(key,geo,mat,x,y,z,sx,sy,sz,rx,ry,rz){
  var pool=instPools[key];
  if(!pool){ var im=new THREE.InstancedMesh(geo,mat,INST_CAP); im.count=0; im.castShadow=false; im.receiveShadow=true; im.instanceMatrix.setUsage(THREE.DynamicDrawUsage); scene.add(im); pool=instPools[key]={mesh:im,n:0}; }
  if(pool.n>=INST_CAP) return false;
  _instDummy.position.set(x,y,z); _instDummy.scale.set(sx,sy,sz); _instDummy.rotation.set(rx||0,ry||0,rz||0); _instDummy.updateMatrix();
  pool.mesh.setMatrixAt(pool.n, _instDummy.matrix); pool.n++; pool.mesh.count=pool.n; pool.mesh.instanceMatrix.needsUpdate=true;
  return true;
}
function mesa(x,z){ var r=rand(3,6),h=rand(6,14); addInstance("mesa",pGeo("mesa"),pMat("mesa"),x,h/2,z,r,h,r); circles.push({x:x,z:z,r:r}); buildings.push({x:x,z:z,w:r*2,d:r*2}); return r; }
function boulder(x,z){ var r=rand(1.5,3); addInstance("boulder",pGeo("rock"),pMat("boulder"),x,r*0.6,z,r,r,r,rand(0,3),rand(0,3),rand(0,3)); circles.push({x:x,z:z,r:r}); buildings.push({x:x,z:z,w:r*2,d:r*2}); return r; }
function tree(x,z){ var th=rand(3,5); addInstance("trunk",pGeo("trunk"),pMat("trunk"),x,th/2,z,1,th,1); var fr=rand(1.6,2.4); addInstance("foliage",pGeo("foliage"),pMat("foliage"),x,th+1,z,fr,fr,fr); circles.push({x:x,z:z,r:0.8}); buildings.push({x:x,z:z,w:1.6,d:1.6}); }
function icerock(x,z){ var r=rand(1.6,3.2); addInstance("icerock",pGeo("rock"),pMat("ice"),x,r*0.6,z,r,r,r,rand(0,3),rand(0,3),rand(0,3)); circles.push({x:x,z:z,r:r}); buildings.push({x:x,z:z,w:r*2,d:r*2}); return r; }
function container(x,z){ var stack=1+(Math.random()*2|0),cols=[0xc0492f,0x2f6b8c,0xb88a2a,0x3a7a4a]; for(var s=0;s<stack;s++){ var cm=new THREE.Mesh(pGeo("box"),pMat("cont",cols[(Math.random()*cols.length)|0])); cm.scale.set(6,2.6,2.6); cm.position.set(x,1.3+s*2.6,z); scene.add(cm); } aabbs.push({minX:x-3,maxX:x+3,minZ:z-1.3,maxZ:z+1.3}); buildings.push({x:x,z:z,w:6,d:2.6}); }
function barrier(x,z){ var seg=new THREE.Mesh(pGeo("box"),pMat("barrier")); seg.scale.set(1.4,1.6,10); seg.position.set(x,0.8,z); scene.add(seg); aabbs.push({minX:x-0.7,maxX:x+0.7,minZ:z-5,maxZ:z+5}); buildings.push({x:x,z:z,w:1.4,d:10}); }
function addCauseway(cx,zc,chan){                                                        // a second, always-open crossing — a real route choice, no lift mechanism
  var H=3.2, W=12, L=chan*2;
  var deckMat=new THREE.MeshStandardMaterial({color:0x3a414d,roughness:0.75,metalness:0.25});
  var dk=posBox(W,0.5,L,cx,H,zc,deckMat); dk.castShadow=true; dk.receiveShadow=true; scene.add(dk);
  decks.push({x:cx,z:zc,w:W,d:L,h:H});
  var railMat=new THREE.MeshStandardMaterial({color:0x2a2f38,roughness:0.85});
  var r1=posBox(0.6,1.1,L,cx-W/2+0.3,H+0.7,zc,railMat); r1.castShadow=true; scene.add(r1);
  var r2=posBox(0.6,1.1,L,cx+W/2-0.3,H+0.7,zc,railMat); r2.castShadow=true; scene.add(r2);
  aabbs.push({minX:cx-W/2-0.3,maxX:cx-W/2+0.3,minZ:zc-L/2,maxZ:zc+L/2});
  aabbs.push({minX:cx+W/2-0.3,maxX:cx+W/2+0.3,minZ:zc-L/2,maxZ:zc+L/2});
  var pilMat=new THREE.MeshStandardMaterial({color:0x232831,roughness:0.9});
  for(var pz=-L/2+8; pz<L/2; pz+=16){ var pil=posBox(1.0,H,1.0,cx,H/2,zc+pz,pilMat); pil.castShadow=true; scene.add(pil); }
  makeRamp(cx, zc-L/2-13, 0, 26, W, H);
  makeRamp(cx, zc+L/2+13, Math.PI, 26, W, H);
}
function makeSign(text,x,z,rotY,subtext){                                                // a real highway sign — canvas-drawn text on a green panel, readable from the road
  var cw=512, ch=256;
  var canvas=document.createElement('canvas'); canvas.width=cw; canvas.height=ch;
  var ctx=canvas.getContext('2d');
  ctx.fillStyle='#1a5c2e'; ctx.fillRect(0,0,cw,ch);
  ctx.strokeStyle='#ffffff'; ctx.lineWidth=10; ctx.strokeRect(10,10,cw-20,ch-20);
  ctx.fillStyle='#ffffff'; ctx.textAlign='center'; ctx.textBaseline='middle';
  ctx.font='bold 62px Arial'; ctx.fillText(text, cw/2, subtext?ch*0.4:ch/2);
  if(subtext){ ctx.font='36px Arial'; ctx.fillText(subtext, cw/2, ch*0.74); }
  var tex=new THREE.CanvasTexture(canvas); tex.needsUpdate=true;
  var faceMat=new THREE.MeshStandardMaterial({map:tex,roughness:0.7});
  var backMat=new THREE.MeshStandardMaterial({color:0x134a24,roughness:0.85});
  var face=new THREE.Mesh(new THREE.PlaneGeometry(14,7),faceMat); face.position.set(x,9,z); face.rotation.y=rotY; face.castShadow=true; scene.add(face);
  var back=new THREE.Mesh(new THREE.PlaneGeometry(14,7),backMat); back.position.set(x,9,z); back.rotation.y=rotY+Math.PI; scene.add(back);
  var poleMat=new THREE.MeshStandardMaterial({color:0x4a4f58,roughness:0.8,metalness:0.4});
  var px=Math.sin(rotY)*6.2, pz=Math.cos(rotY)*6.2;
  var p1=new THREE.Mesh(new THREE.CylinderGeometry(0.35,0.35,9,8),poleMat); p1.position.set(x-px,4.5,z-pz); p1.castShadow=true; scene.add(p1);
  var p2=new THREE.Mesh(new THREE.CylinderGeometry(0.35,0.35,9,8),poleMat); p2.position.set(x+px,4.5,z+pz); p2.castShadow=true; scene.add(p2);
}
function buildStadium(x,z){                                                              // a landmark to anchor the city district and give the skyline a focal point
  var r=22, h=14;
  var wallMat=new THREE.MeshStandardMaterial({color:0x2a3040,roughness:0.8,metalness:0.2});
  var roofMat=new THREE.MeshStandardMaterial({color:0xd8dde6,roughness:0.5,metalness:0.3,transparent:true,opacity:0.85});
  var ring=new THREE.Mesh(new THREE.CylinderGeometry(r,r*1.05,h,28,1,true),wallMat); ring.position.set(x,h/2,z); ring.castShadow=true; ring.receiveShadow=true; scene.add(ring);
  var dome=new THREE.Mesh(new THREE.SphereGeometry(r*0.94,28,14,0,6.2832,0,Math.PI/2.2),roofMat); dome.position.set(x,h,z); scene.add(dome);
  for(var i=0;i<4;i++){ var a=i*Math.PI/2+0.4, lx=x+Math.cos(a)*r*1.15, lz=z+Math.sin(a)*r*1.15;
    var pole=new THREE.Mesh(new THREE.CylinderGeometry(0.4,0.4,h+6,8),wallMat); pole.position.set(lx,(h+6)/2,lz); pole.castShadow=true; scene.add(pole);
    var lamp=new THREE.Mesh(new THREE.BoxGeometry(2.2,1.4,0.6),new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffe9b0,emissiveIntensity:0.9})); lamp.position.set(lx,h+6,lz); scene.add(lamp);
  }
  aabbs.push({minX:x-r,maxX:x+r,minZ:z-r,maxZ:z+r});
  buildings.push({x:x,z:z,w:r*2,d:r*2});
}
function buildRailDepot(x,z){                                                            // fills the empty ground inside the railway loop
  var wMat=new THREE.MeshStandardMaterial({color:0x3a4048,roughness:0.85,metalness:0.15});
  var roofMat=new THREE.MeshStandardMaterial({color:0x6a3a2a,roughness:0.8});
  for(var i=0;i<2;i++){ var sx=x+(i-0.5)*24;
    var shed=posBox(16,7,34,sx,3.5,z,wMat); shed.castShadow=true; shed.receiveShadow=true; scene.add(shed);
    var roof=posBox(17.5,1,35.5,sx,7.2,z,roofMat); roof.castShadow=true; scene.add(roof);
    aabbs.push({minX:sx-8,maxX:sx+8,minZ:z-17,maxZ:z+17}); buildings.push({x:sx,z:z,w:16,d:34});
  }
  var towerMat=new THREE.MeshStandardMaterial({color:0x6a7079,roughness:0.7,metalness:0.4});
  var tx=x+34, tz=z-24;
  var leg=posBox(0.7,10,0.7,tx,5,tz,towerMat); leg.castShadow=true; scene.add(leg);
  var tank=new THREE.Mesh(new THREE.CylinderGeometry(4,4,6,12),towerMat); tank.position.set(tx,13,tz); tank.castShadow=true; scene.add(tank);
  aabbs.push({minX:tx-4,maxX:tx+4,minZ:tz-4,maxZ:tz+4});
  var fenceMat=new THREE.MeshStandardMaterial({color:0x2a2f38,roughness:0.9});
  scene.add(posBox(0.3,1.8,64,x-42,0.9,z,fenceMat)); scene.add(posBox(0.3,1.8,64,x+42,0.9,z,fenceMat));
  return {x0:x-46,z0:z-36,x1:x+46,z1:z+36,w:92};
}
function buildAirport(cx,cz){                                                            // a real landmark: a long runway, tower, hangar, and a parked plane
  var rw=26, rl=100;
  var rwMat=new THREE.MeshStandardMaterial({color:0x24272d,roughness:0.9});
  var rwm=posBox(rw,0.08,rl,cx,0.04,cz,rwMat); rwm.receiveShadow=true; scene.add(rwm);
  var stripeMat=new THREE.MeshStandardMaterial({color:0xe8e8e0,emissive:0x2a2a24,roughness:0.6});
  for(var rz=-rl/2+6; rz<rl/2-6; rz+=14) scene.add(posBox(1.2,0.05,7,cx,0.09,cz+rz,stripeMat));
  scene.add(posBox(rw+4,0.06,2,cx,0.07,cz-rl/2,stripeMat)); scene.add(posBox(rw+4,0.06,2,cx,0.07,cz+rl/2,stripeMat));
  var apronMat=new THREE.MeshStandardMaterial({color:0x2c3038,roughness:0.9});
  var apx=cx+rw/2+26, apz=cz-rl/2+20;
  scene.add(posBox(46,0.07,56,apx,0.05,apz,apronMat));
  var towerMat=new THREE.MeshStandardMaterial({color:0xd8dde6,roughness:0.6,metalness:0.2});
  var glassMat=new THREE.MeshStandardMaterial({color:0x2a3d57,roughness:0.15,metalness:0.85,emissive:0x16356e,emissiveIntensity:0.2});
  var twx=apx, twz=apz-32;
  var towerBase=posBox(6,22,6,twx,11,twz,towerMat); towerBase.castShadow=true; scene.add(towerBase);
  var towerCab=posBox(9,4,9,twx,23,twz,glassMat); towerCab.castShadow=true; scene.add(towerCab);
  aabbs.push({minX:twx-3,maxX:twx+3,minZ:twz-3,maxZ:twz+3});
  var hangarMat=new THREE.MeshStandardMaterial({color:0x3a4048,roughness:0.85,metalness:0.15});
  var hgx=apx, hgz=apz+18;
  var hangar=new THREE.Mesh(new THREE.CylinderGeometry(11,11,16,16,1,false,0,Math.PI),hangarMat);
  hangar.rotation.z=Math.PI/2; hangar.rotation.y=Math.PI/2; hangar.position.set(hgx,8,hgz); hangar.castShadow=true; scene.add(hangar);
  aabbs.push({minX:hgx-11,maxX:hgx+11,minZ:hgz-8,maxZ:hgz+8});
  var planeMat=new THREE.MeshStandardMaterial({color:0xe8ecf0,roughness:0.4,metalness:0.3});
  var plx=apx+18, plz=apz-2;
  var pbody=posBox(3,3,18,plx,2,plz,planeMat); pbody.castShadow=true; scene.add(pbody);
  var pwing=posBox(22,0.6,3,plx,2,plz,planeMat); pwing.castShadow=true; scene.add(pwing);
  var ptail=posBox(0.6,3,3,plx,3,plz-8,planeMat); ptail.castShadow=true; scene.add(ptail);
  aabbs.push({minX:plx-11,maxX:plx+11,minZ:plz-9,maxZ:plz+9});
  var lampMat=new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffe9b0,emissiveIntensity:1.0});
  for(var lz2=-rl/2+8; lz2<rl/2; lz2+=16){ for(var ls=-1;ls<=1;ls+=2){ var lamp2=new THREE.Mesh(new THREE.SphereGeometry(0.35,8,8),lampMat); lamp2.position.set(cx+ls*(rw/2+1),0.4,cz+lz2); scene.add(lamp2); } }
  return {x0:cx,z0:cz-rl/2,x1:cx,z1:cz+rl/2,w:rw};                                        // caller registers this in its own road-avoidance list
}
function buildMegaWorld(){
  scene.add(new THREE.HemisphereLight(0xbfe0ff,0x8a9a6a,0.55));
  var sun=new THREE.DirectionalLight(0xfff3d6,0.85); sun.position.set(-900,1400,600); scene.add(sun);
  var sunMat=new THREE.MeshBasicMaterial({color:0xfff6d8,fog:false});
  var sunDisc=new THREE.Mesh(new THREE.SphereGeometry(220,20,16),sunMat); sunDisc.position.set(-4200,3800,2800); scene.add(sunDisc);
  var sunGlow=new THREE.Mesh(new THREE.SphereGeometry(420,20,16),new THREE.MeshBasicMaterial({color:0xffe9a0,transparent:true,opacity:0.35,fog:false})); sunGlow.position.copy(sunDisc.position); scene.add(sunGlow);
  var S=CITY, OLD_S=820;                                                  // the original islands stay locked to this size; S itself keeps growing for the new island
  var OCN_N=-20, OCN_S=-320, CEN=(OCN_N+OCN_S)/2, OHALF=(OCN_N-OCN_S)/2;   // wide ocean between the two islands
  var depotBounds={x0:1e9,x1:-1e9,z0:1e9,z1:-1e9};                        // a real rectangular keepout — set once the depot is actually placed
  var scatterQueue=[];                                                     // every zone queues its obstacles here; nothing actually places until ALL roads for the whole world exist
  function queueScatter(x0,z0,x1,z1,biome){ scatterQueue.push([x0,z0,x1,z1,biome]); }
  var FX0=-OLD_S+4, FX1=-OLD_S*0.42, FZ0=-OLD_S+4, FZ1=OCN_S-6;                        // wider, deeper forest zone on the left of the second island
  var TRK={cx:0, cz:324, rx:170, rz:78};                                    // railway loop — sized and placed to sit inside one gap in the road grid, not across it
  var GROUND={city:0x12161f,desert:0xc79b5e,plain:0x35552a,ice:0xdfe9f2,highway:0x2b2f38,harbor:0x1d2630};
  function spawnClear(x,z){ return Math.hypot(x,z-(OLD_S-16))<26 || Math.hypot(x,z-(OLD_S-40))<24 || Math.hypot(x,z-(-OLD_S+20))<26 || Math.hypot(x,z-(-OLD_S+40))<24; }
  var roads=[];
  function nearRoad(x,z){ for(var i=0;i<roads.length;i++){ var R=roads[i], dx=R.x1-R.x0, dz=R.z1-R.z0, L2=dx*dx+dz*dz, t=L2?Math.max(0,Math.min(1,((x-R.x0)*dx+(z-R.z0)*dz)/L2)):0; if(Math.hypot(x-(R.x0+dx*t), z-(R.z0+dz*t))<R.w/2+8) return true; } return false; }
  function distToRoadEdge(x,z){ var best=1e18; for(var i=0;i<roads.length;i++){ var R=roads[i], dx=R.x1-R.x0, dz=R.z1-R.z0, L2=dx*dx+dz*dz, t=L2?Math.max(0,Math.min(1,((x-R.x0)*dx+(z-R.z0)*dz)/L2)):0; var d=Math.hypot(x-(R.x0+dx*t), z-(R.z0+dz*t))-(R.w||10)/2; if(d<best) best=d; } return best; }
  function inBay(z){ return z<OCN_N+5 && z>OCN_S-5; }
  function nearRamp(x,z){ for(var i=0;i<ramps.length;i++){ var r=ramps[i]; if(Math.hypot(x-r.x,z-r.z)<r.L/2+r.Wd/2+6) return true; } return false; }
  function inRect(x0,z0,x1,z1,n,fn){ var t=0,m=0; while(m<n && t<n*8){ t++; var x=rand(x0+5,x1-5),z=rand(z0+5,z1-5); if(inBay(z)||(x>=FX0&&x<=FX1&&z>=FZ0&&z<=FZ1)||Math.abs(Math.hypot((x-TRK.cx)/TRK.rx,(z-TRK.cz)/TRK.rz)-1)<0.05||(x>=depotBounds.x0&&x<=depotBounds.x1&&z>=depotBounds.z0&&z<=depotBounds.z1)) continue; if(spawnClear(x,z)||nearRoad(x,z)||nearRamp(x,z)||circleHitsAABB(x,z,3)) continue; fn(x,z); m++; } }
  function patch(x0,z0,x1,z1,biome){ var pm=new THREE.Mesh(new THREE.PlaneGeometry(x1-x0,z1-z0),new THREE.MeshStandardMaterial({color:GROUND[biome],roughness:0.98})); pm.rotation.x=-Math.PI/2; pm.position.set((x0+x1)/2,0.02,(z0+z1)/2); pm.receiveShadow=true; scene.add(pm); }
  function highwayLanes(x0,z0,x1,z1){                                     // dashed lane lines + lamp posts so a highway zone actually reads as a highway
    var lineMat=new THREE.MeshStandardMaterial({color:0xd8b13a,emissive:0x352808,roughness:0.7});
    var w=x1-x0, d=z1-z0;
    if(w>=d){ var mz=(z0+z1)/2; for(var lx=x0+6; lx<x1-6; lx+=9){ if(inBay(mz)) continue; scene.add(posBox(4,0.05,0.4,lx,0.05,mz,lineMat)); } }
    else { var mx=(x0+x1)/2; for(var lz=z0+6; lz<z1-6; lz+=9){ if(inBay(lz)) continue; scene.add(posBox(0.4,0.05,4,mx,0.05,lz,lineMat)); } }
    var poleMat=new THREE.MeshStandardMaterial({color:0x3a4150,roughness:0.8});
    var lampMat=new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffe9b0,emissiveIntensity:1.1});
    var n=Math.max(2,Math.round(Math.max(w,d)/60));
    for(var i=0;i<n;i++){ var t=(i+0.5)/n; var px2=(w>=d)?(x0+w*t):(x0+8); var pz2=(w>=d)?(z0+8):(z0+d*t);
      if(inBay(pz2)) continue;
      var pole=new THREE.Mesh(new THREE.CylinderGeometry(0.3,0.3,7,8),poleMat); pole.position.set(px2,3.5,pz2); pole.castShadow=true; scene.add(pole);
      var lamp=new THREE.Mesh(new THREE.SphereGeometry(0.4,8,8),lampMat); lamp.position.set(px2,7,pz2); scene.add(lamp);
    }
  }
  function cityStreets(x0,z0,x1,z1){
    var pre=roadNodes.length, cn=3, nodes=[];
    for(var r=0;r<cn;r++) for(var c=0;c<cn;c++){ var nx=x0+((c+0.5)/cn)*(x1-x0), nz=z0+((r+0.5)/cn)*(z1-z0); nodes.push(rnode(nx,nz)); }
    for(var r2=0;r2<cn;r2++) for(var c2=0;c2<cn;c2++){ var idx=r2*cn+c2; if(c2<cn-1) safeRedge(nodes[idx],nodes[idx+1]); if(r2<cn-1) safeRedge(nodes[idx],nodes[idx+cn]); }
    var mid=nodes[4], near=-1, dNear=1e18;
    for(var i=0;i<pre;i++){ if(roadNodes[i].forest) continue; var d=Math.hypot(roadNodes[i].x-roadNodes[mid].x, roadNodes[i].z-roadNodes[mid].z); if(d<dNear){dNear=d;near=i;} }
    safeRedge(near,mid);
    for(var pi=0;pi<nodes.length;pi++) roadPad(roadNodes[nodes[pi]].x,roadNodes[nodes[pi]].z,roadMat,6);
  }
  function scatter(x0,z0,x1,z1,biome){
    var a=(x1-x0)*(z1-z0)/10000;
    if(biome==="city"){ cityStreets(x0,z0,x1,z1); inRect(x0,z0,x1,z1,Math.round(30*a),tower); }
    else if(biome==="desert"){ inRect(x0,z0,x1,z1,Math.round(8*a),mesa); inRect(x0,z0,x1,z1,Math.round(10*a),boulder); }
    else if(biome==="plain") inRect(x0,z0,x1,z1,Math.round(14*a),tree);
    else if(biome==="ice") inRect(x0,z0,x1,z1,Math.round(12*a),icerock);
    else if(biome==="harbor") inRect(x0,z0,x1,z1,Math.round(12*a),container);
    else if(biome==="highway"){
      inRect(x0,z0,x1,z1,Math.round(5*a),tree);                                // sparse roadside scatter so the open ground isn't bare
      highwayLanes(x0,z0,x1,z1);
    }
  }
  var roadMat=new THREE.MeshStandardMaterial({color:0x191c22,roughness:0.95,metalness:0.0});
  var lineMat=new THREE.MeshStandardMaterial({color:0xd8c24a,emissive:0x322706,roughness:0.6});
  function addRoad(x0,z0,x1,z1){
    var dx=x1-x0, dz=z1-z0, len=Math.hypot(dx,dz), ang=Math.atan2(dx,dz), W=10;
    addInstance("roadStrip",pGeo("box"),roadMat,(x0+x1)/2,0.04,(z0+z1)/2,W,0.06,len,0,ang,0);
    var n=Math.max(1,Math.floor(len/9));
    for(var i=0;i<n;i++){ var t=(i+0.5)/n; addInstance("roadDash",pGeo("box"),lineMat,x0+dx*t,0.08,z0+dz*t,0.5,0.05,3.2,0,ang,0); }
    roads.push({x0:x0,z0:z0,x1:x1,z1:z1,w:W});
  }
  roadNodes=[]; roadAdj=[];   // populated below with a road graph per island
  // ---- MAIN ISLAND (north): full set of biomes incl. desert & plains ----
  var mainGrid=[ ["city","desert","plain"], ["highway","city","ice"], ["harbor","plain","desert"] ];
  function mc(c,r){ var x0=-OLD_S+(c/3)*2*OLD_S, x1=-OLD_S+((c+1)/3)*2*OLD_S, z0=OCN_N+(r/3)*(OLD_S-OCN_N), z1=OCN_N+((r+1)/3)*(OLD_S-OCN_N); return [x0,z0,x1,z1]; }
  for(var r=0;r<3;r++) for(var c=0;c<3;c++){ var b=mc(c,r); patch(b[0],b[1],b[2],b[3],mainGrid[r][c]); }
  // ---- SECOND ISLAND (south): another city, plains, highway, desert ----
  var islGrid=[ ["city","highway"], ["plain","desert"] ];
  function ic(c,r){ var x0=-OLD_S+(c/2)*2*OLD_S, x1=-OLD_S+((c+1)/2)*2*OLD_S, z0=-OLD_S+(r/2)*(OCN_S+OLD_S), z1=-OLD_S+((r+1)/2)*(OCN_S+OLD_S); return [x0,z0,x1,z1]; }
  for(var r3=0;r3<2;r3++) for(var c3=0;c3<2;c3++){ var ib=ic(c3,r3); patch(ib[0],ib[1],ib[2],ib[3],islGrid[r3][c3]); }
  // ---- road network: a connected grid per island (traffic follows it; islands stay separate) ----
  function rnode(x,z){ roadNodes.push({x:x,z:z}); roadAdj.push([]); return roadNodes.length-1; }
  function redge(a,b){ addRoad(roadNodes[a].x,roadNodes[a].z,roadNodes[b].x,roadNodes[b].z); roadAdj[a].push(b); roadAdj[b].push(a); }
  var MC=4, MR=4, mCols=[], mRows=[], mI=[];                                            // 4x4 main-island grid — denser, harder to shake a tail
  for(var mci=0;mci<MC;mci++) mCols.push(-OLD_S*0.78+ (mci/(MC-1))*OLD_S*1.56);
  for(var mri=0;mri<MR;mri++) mRows.push(OCN_N+((mri+0.5)/MR)*0.82*(OLD_S-OCN_N));           // stop short of the far edge — leaves real open room for a new district
  for(var mr=0;mr<MR;mr++){ mI[mr]=[]; for(var mcc=0;mcc<MC;mcc++) mI[mr][mcc]=rnode(mCols[mcc],mRows[mr]); }
  for(var mr2=0;mr2<MR;mr2++) for(var mc2=0;mc2<MC;mc2++){ if(mc2<MC-1) redge(mI[mr2][mc2],mI[mr2][mc2+1]); if(mr2<MR-1) redge(mI[mr2][mc2],mI[mr2+1][mc2]); }
  var SC=3, SR=3, sCols=[], sRows=[], sI=[];                                            // 3x3 second-island grid
  for(var sci=0;sci<SC;sci++) sCols.push(-OLD_S*0.7+ (sci/(SC-1))*OLD_S*1.4);
  for(var sri=0;sri<SR;sri++) sRows.push(-OLD_S+((sri+0.5)/SR)*(OCN_S+OLD_S));
  for(var sr=0;sr<SR;sr++){ sI[sr]=[]; for(var scc=0;scc<SC;scc++) sI[sr][scc]=rnode(sCols[scc],sRows[sr]); }
  for(var sr2=0;sr2<SR;sr2++) for(var sc2=0;sc2<SC;sc2++){ if(sc2<SC-1) redge(sI[sr2][sc2],sI[sr2][sc2+1]); if(sr2<SR-1) redge(sI[sr2][sc2],sI[sr2+1][sc2]); }
  addRoad(0,mRows[0],0,OCN_N); addRoad(0,OCN_S,0,sRows[SR-1]);   // cosmetic links to the bridge mouths
  for(var qi=0;qi<roadNodes.length;qi++) roadPad(roadNodes[qi].x,roadNodes[qi].z,roadMat);  // round the turns
  // ---- link the two islands through the main bridge so civilian traffic crosses ----
  function radj(a,b){ roadAdj[a].push(b); roadAdj[b].push(a); }                         // graph-only link (the bridge deck IS the road, so no flat strip)
  var sJ=rnode(0,sRows[SR-1]); for(var scj=0;scj<SC;scj++) redge(sJ,sI[SR-1][scj]);      // x=0 junction on the second island's near row
  roadPad(0,sRows[SR-1],roadMat);
  var bN=rnode(0,OCN_N), bg1=rnode(0,CEN+12), bg2=rnode(0,CEN-12), bS=rnode(0,OCN_S);   // corridor nodes spanning the bridge
  radj(mI[0][Math.floor(MC/2)],bN); radj(bN,bg1); radj(bg1,bg2); radj(bg2,bS); radj(bS,sJ);
  // ---- a second crossing — a fixed low causeway with real solid rails, giving the robber a route choice ----
  var CW_X=OLD_S*0.5;
  addCauseway(CW_X, CEN, OHALF);
  var cwN=rnode(CW_X,OCN_N), cwS=rnode(CW_X,OCN_S);
  radj(cwN,mI[0][MC-1]); radj(cwS,sI[SR-1][SC-1]); radj(cwN,cwS);
  // ---- a landmark: a stadium dome anchoring the city district ----
  buildStadium(-OLD_S*0.55, OCN_N+(OLD_S-OCN_N)*0.28);
  var apCx=-OLD_S*0.25, apCz=(mRows[MR-1]+OLD_S)/2;                                        // centered in the real open gap past the road grid's last row
  var apPatch=new THREE.Mesh(new THREE.PlaneGeometry(260,150),new THREE.MeshStandardMaterial({color:0x2c3038,roughness:0.95}));
  apPatch.rotation.x=-Math.PI/2; apPatch.position.set(apCx,0.025,apCz); apPatch.receiveShadow=true; scene.add(apPatch);
  roads.push(buildAirport(apCx, apCz));
  // ---- local street grids inside every city cell — not just the coarse backbone, real streets through the blocks ----
  var preCityCount=roadNodes.length;
  function nearestRoadNode(x,z){ var best=-1,bd=1e18; for(var i=0;i<preCityCount;i++){ var d=Math.hypot(roadNodes[i].x-x,roadNodes[i].z-z); if(d<bd){bd=d;best=i;} } return best; }
  function cityLocalGrid(x0,z0,x1,z1){
    var cn=3, lCols=[], lRows=[], lI=[];
    for(var i=0;i<cn;i++){ lCols.push(x0+((i+0.5)/cn)*(x1-x0)); lRows.push(z0+((i+0.5)/cn)*(z1-z0)); }
    for(var r=0;r<cn;r++){ lI[r]=[]; for(var c=0;c<cn;c++) lI[r][c]=rnode(lCols[c],lRows[r]); }
    for(var r2=0;r2<cn;r2++) for(var c2=0;c2<cn;c2++){ if(c2<cn-1) redge(lI[r2][c2],lI[r2][c2+1]); if(r2<cn-1) redge(lI[r2][c2],lI[r2+1][c2]); }
    var nn=nearestRoadNode((x0+x1)/2,(z0+z1)/2); if(nn>=0) radj(lI[1][1],nn);
  }
  for(var r4a=0;r4a<3;r4a++) for(var c4a=0;c4a<3;c4a++){ if(mainGrid[r4a][c4a]==="city"){ var b4a=mc(c4a,r4a); cityLocalGrid(b4a[0],b4a[1],b4a[2],b4a[3]); } }
  for(var qi2=preCityCount;qi2<roadNodes.length;qi2++) roadPad(roadNodes[qi2].x,roadNodes[qi2].z,roadMat,6);
  // ---- dense forest on the second island: weaving off-road tracks, trucks & off-roaders only ----
  var ff=new THREE.Mesh(new THREE.PlaneGeometry(FX1-FX0,FZ1-FZ0),new THREE.MeshStandardMaterial({color:0x15311a,roughness:1.0}));
  ff.rotation.x=-Math.PI/2; ff.position.set((FX0+FX1)/2,0.03,(FZ0+FZ1)/2); ff.receiveShadow=true; scene.add(ff);
  var dirtMat=new THREE.MeshStandardMaterial({color:0x5a4326,roughness:1.0});
  function fnode(x,z){ roadNodes.push({x:x,z:z,forest:true}); roadAdj.push([]); return roadNodes.length-1; }
  function dirtTrack(A,B){ var dx=B.x-A.x, dz=B.z-A.z, len=Math.hypot(dx,dz); var rd=posBox(7,0.05,len,(A.x+B.x)/2,0.045,(A.z+B.z)/2,dirtMat); rd.rotation.y=Math.atan2(dx,dz); rd.receiveShadow=true; scene.add(rd); roads.push({x0:A.x,z0:A.z,x1:B.x,z1:B.z,w:7}); }
  function fedge(a,b){ dirtTrack(roadNodes[a],roadNodes[b]); roadAdj[a].push(b); roadAdj[b].push(a); }
  function fx(fu){ return FX0+fu*(FX1-FX0); } function fz(fv){ return FZ0+fv*(FZ1-FZ0); }   // proportional to the (now bigger) forest bounds, not hardcoded
  var f0=fnode(fx(0.80),fz(0.62)), f1=fnode(fx(0.52),fz(0.72)), f2=fnode(fx(0.24),fz(0.48)),
      f3=fnode(fx(0.42),fz(0.16)), f4=fnode(fx(0.08),fz(0.28)), f5=fnode(fx(0.10),fz(0.66)),
      f6=fnode(fx(0.64),fz(0.32));
  fedge(f0,f1); fedge(f1,f2); fedge(f2,f3); fedge(f3,f4); fedge(f4,f5); fedge(f5,f1); fedge(f2,f4); fedge(f3,f6); fedge(f6,f0);
  roadAdj[f0].push(sI[SR-1][0]); roadAdj[sI[SR-1][0]].push(f0); dirtTrack(roadNodes[f0],roadNodes[sI[SR-1][0]]);   // entrance off the island grid
  for(var fp=0;fp<roadNodes.length;fp++) if(roadNodes[fp].forest){ var dp=new THREE.Mesh(new THREE.CylinderGeometry(5,5,0.05,18),dirtMat); dp.position.set(roadNodes[fp].x,0.05,roadNodes[fp].z); dp.receiveShadow=true; scene.add(dp); }
  var ftarget=Math.round((FX1-FX0)*(FZ1-FZ0)/10000*95), fplaced=0, ftries=0;
  while(fplaced<ftarget && ftries<ftarget*6){ ftries++; var frx=rand(FX0+3,FX1-3), frz=rand(FZ0+3,FZ1-3); if(distToRoadEdge(frx,frz)<6||circleHitsAABB(frx,frz,2.6)) continue; tree(frx,frz); fplaced++; }
  // ---- obstacles ----
  // ---- rail depot filling the empty ground inside the loop — built BEFORE the tower scatter so it's actually excluded ----
  var depotRect=buildRailDepot(TRK.cx, TRK.cz);
  depotBounds={x0:depotRect.x0-8,x1:depotRect.x1+8,z0:depotRect.z0-8,z1:depotRect.z1+8};
  var depotNode=rnode(TRK.cx, TRK.cz-45);
  var nearestDepot=-1, dDepot=1e18;
  for(var dgi=0; dgi<roadNodes.length-1; dgi++){ if(roadNodes[dgi].forest) continue; var ddx=roadNodes[dgi].x-TRK.cx, ddz=roadNodes[dgi].z-(TRK.cz-45); var dd=Math.hypot(ddx,ddz); if(dd<dDepot){ dDepot=dd; nearestDepot=dgi; } }
  if(nearestDepot>=0) redge(depotNode, nearestDepot);
  // ---- ocean + long bridge with the drawbridge in the middle — built BEFORE scatter so its ramps are actually excluded ----
  addLiftBridge(CEN, OHALF, S);
  buildRailway(TRK.cx, TRK.cz, TRK.rx, TRK.rz);
  for(var r4=0;r4<3;r4++) for(var c4=0;c4<3;c4++){ var b4=mc(c4,r4); queueScatter(b4[0],b4[1],b4[2],b4[3],mainGrid[r4][c4]); }
  for(var r5=0;r5<2;r5++) for(var c5=0;c5<2;c5++){ var b5=ic(c5,r5); queueScatter(b5[0],b5[1],b5[2],b5[3],islGrid[r5][c5]); }
  // ---- physical pavement connecting the bridge mouths to the actual road network on each side ----
  var nb=-1,dNb=1e18; for(var nbi=0;nbi<MC;nbi++){ var nnd=mI[0][nbi]; var d1=Math.hypot(roadNodes[nnd].x-0,roadNodes[nnd].z-OCN_N); if(d1<dNb){dNb=d1;nb=nnd;} }
  if(nb>=0) addRoad(0,OCN_N,roadNodes[nb].x,roadNodes[nb].z);
  var sb=-1,dSb=1e18; for(var sbi=0;sbi<SC;sbi++){ var snd=sI[SR-1][sbi]; var d2=Math.hypot(roadNodes[snd].x-0,roadNodes[snd].z-OCN_S); if(d2<dSb){dSb=d2;sb=snd;} }
  if(sb>=0) addRoad(0,OCN_S,roadNodes[sb].x,roadNodes[sb].z);
  // ---- east & west expansion strips — the world boundary outgrew the main island, so this fills that blank margin ----
  function hasEdge(a,b){ return roadAdj[a] && roadAdj[a].indexOf(b)>=0; }
  function safeRedge(a,b){ if(a>=0 && b>=0 && a!==b && !hasEdge(a,b)) redge(a,b); }
  var ZONE_NAMES={city:"CITY",plain:"PLAINS",desert:"DESERT",harbor:"HARBOR",ice:"SNOW ZONE"};
  function sideStrip(x0,x1,biomes,facing){
    var eNodes=[], bounds=[];
    for(var bi=0;bi<3;bi++){ var bx0=x0+(bi/3)*(x1-x0), bx1=x0+((bi+1)/3)*(x1-x0);
      patch(bx0,OCN_N,bx1,OLD_S,biomes[bi]); bounds.push([bx0,OCN_N,bx1,OLD_S,biomes[bi]]);
      eNodes.push(rnode((bx0+bx1)/2,(OCN_N+OLD_S)/2)); }
    for(var bj=0;bj<2;bj++) safeRedge(eNodes[bj],eNodes[bj+1]);
    var near=-1, dNear=1e18;
    for(var emr=0;emr<MR;emr++) for(var emc=0;emc<MC;emc++){ var nd=mI[emr][emc]; var dd2=Math.hypot(roadNodes[nd].x-roadNodes[eNodes[1]].x, roadNodes[nd].z-roadNodes[eNodes[1]].z); if(dd2<dNear){dNear=dd2;near=nd;} }
    safeRedge(near,eNodes[1]);
    for(var pi=0;pi<eNodes.length;pi++) roadPad(roadNodes[eNodes[pi]].x,roadNodes[eNodes[pi]].z,roadMat);
    for(var bk=0;bk<bounds.length;bk++){ queueScatter(bounds[bk][0],bounds[bk][1],bounds[bk][2],bounds[bk][3],bounds[bk][4]);   // deferred — placed only after the ENTIRE world's roads exist
      var bcx=(bounds[bk][0]+bounds[bk][2])/2; makeSign(ZONE_NAMES[bounds[bk][4]]||bounds[bk][4].toUpperCase(), bcx, OCN_N+40, facing, "DISTRICT"); }
    return eNodes;
  }
  var eastNodes=sideStrip(OLD_S+10, S-10, ["city","plain","desert"], -Math.PI/2);
  var westNodes=sideStrip(-S+10, -OLD_S-10, ["desert","city","plain"], Math.PI/2);
  // ---- south strip — the same blank-margin bug, but past the second island's far edge ----
  function southStrip(z0,z1,biomes){
    var sNodes=[], bounds=[];
    for(var bi=0;bi<2;bi++){ var bx0=-OLD_S+(bi/2)*2*OLD_S, bx1=-OLD_S+((bi+1)/2)*2*OLD_S;
      patch(bx0,z0,bx1,z1,biomes[bi]); bounds.push([bx0,z0,bx1,z1,biomes[bi]]);
      sNodes.push(rnode((bx0+bx1)/2,(z0+z1)/2)); }
    safeRedge(sNodes[0],sNodes[1]);
    var near=-1, dNear=1e18;
    for(var sci2=0;sci2<SC;sci2++){ var nd=sI[0][sci2]; var dd3=Math.hypot(roadNodes[nd].x-roadNodes[sNodes[0]].x, roadNodes[nd].z-roadNodes[sNodes[0]].z); if(dd3<dNear){dNear=dd3;near=nd;} }
    safeRedge(near,sNodes[0]);
    for(var pi2=0;pi2<sNodes.length;pi2++) roadPad(roadNodes[sNodes[pi2]].x,roadNodes[sNodes[pi2]].z,roadMat);
    for(var bk2=0;bk2<bounds.length;bk2++){ queueScatter(bounds[bk2][0],bounds[bk2][1],bounds[bk2][2],bounds[bk2][3],bounds[bk2][4]);
      var scx2=(bounds[bk2][0]+bounds[bk2][2])/2; makeSign(ZONE_NAMES[bounds[bk2][4]]||bounds[bk2][4].toUpperCase(), scx2, z1-40, 0, "DISTRICT"); }
    return sNodes;
  }
  var southNodes=southStrip(-(S-10), -(OLD_S+10), ["harbor","desert"]);
  // ---- corner zones — the side strips only run alongside the main island's depth, the south strip only spans the second island's width, so the four diagonal corners between them were still empty ----
  function cornerZone(x0,x1,z0,z1,biome,connectA,connectB){
    patch(x0,z0,x1,z1,biome);
    var cn=rnode((x0+x1)/2,(z0+z1)/2);
    safeRedge(cn,connectA); safeRedge(cn,connectB);
    roadPad(roadNodes[cn].x,roadNodes[cn].z,roadMat);
    queueScatter(x0,z0,x1,z1,biome);
    makeSign(ZONE_NAMES[biome]||biome.toUpperCase(), (x0+x1)/2, (z0+z1)/2, Math.PI/4, "DISTRICT");
  }
  cornerZone(OLD_S+10, S-10, -(OLD_S+10), OCN_N-10, "city", -1, southNodes[1]);       // SE corner — only connects south; the east strip is across open water
  cornerZone(-S+10, -OLD_S-10, -(OLD_S+10), OCN_N-10, "city", -1, southNodes[0]);      // SW corner — same on the west side
  // ==== THIRD ISLAND — a whole new landmass off the main island's north edge, different layout, its own crossings ====
  var NCH_S=OLD_S, NCH_N=OLD_S+200, T_N=NCH_N, T_S=S;
  function tc(c,r){ var x0=-OLD_S+(c/3)*2*OLD_S, x1=-OLD_S+((c+1)/3)*2*OLD_S, z0=T_N+(r/3)*(T_S-T_N), z1=T_N+((r+1)/3)*(T_S-T_N); return [x0,z0,x1,z1]; }
  var thirdGrid=[["harbor","city","ice"],["desert","desert","plain"],["ice","harbor","city"]];     // a different arrangement than either existing island
  for(var tr=0;tr<3;tr++) for(var tcx=0;tcx<3;tcx++){ var tb=tc(tcx,tr); patch(tb[0],tb[1],tb[2],tb[3],thirdGrid[tr][tcx]); }
  // ---- the channel between the main island and the third island ----
  var TCEN=(NCH_S+NCH_N)/2, TOHALF=(NCH_N-NCH_S)/2;
  var twater=new THREE.Mesh(new THREE.PlaneGeometry(OLD_S*2+80, NCH_N-NCH_S), new THREE.MeshStandardMaterial({color:0x14506b,roughness:0.28,metalness:0.5,transparent:true,opacity:0.93}));
  twater.rotation.x=-Math.PI/2; twater.position.set(0,0.28,TCEN); twater.receiveShadow=true; scene.add(twater);
  var tfoamMat=new THREE.MeshBasicMaterial({color:0x8fd4e8,transparent:true,opacity:0.15});
  for(var tfz=NCH_S+5; tfz<NCH_N; tfz+=8){ var tfm=new THREE.Mesh(new THREE.PlaneGeometry(OLD_S*2+80,0.6),tfoamMat); tfm.rotation.x=-Math.PI/2; tfm.position.set(0,0.31,tfz); scene.add(tfm); }
  var TB_X=-OLD_S*0.35, TD_X=OLD_S*0.4;                                                  // bridge / drawbridge crossing positions
  var tSeaMat=new THREE.MeshStandardMaterial({color:0x2a323c,roughness:0.9});
  function tSeaWall(x0,x1,z){ if(x1<=x0) return; var m=new THREE.Mesh(new THREE.BoxGeometry(x1-x0,2.2,1.6),tSeaMat); m.position.set((x0+x1)/2,1.1,z); m.castShadow=true; scene.add(m); aabbs.push({minX:x0,maxX:x1,minZ:z-0.8,maxZ:z+0.8}); }
  [NCH_S,NCH_N].forEach(function(zz){ tSeaWall(-OLD_S-20, TB_X-8, zz); tSeaWall(TB_X+8, TD_X-9, zz); tSeaWall(TD_X+9, OLD_S+20, zz); });
  addCauseway(TB_X, TCEN, TOHALF);                                                        // crossing 1 — a plain bridge
  addThirdDrawbridge(TD_X, TCEN);                                                         // crossing 2 — a real animated drawbridge
  // ---- a fresh road grid on the third island ----
  var TC3=3, TR3=3, tCols=[], tRows=[], tI=[];
  for(var tci=0;tci<TC3;tci++) tCols.push(-OLD_S*0.72 + (tci/(TC3-1))*OLD_S*1.44);
  for(var tri=0;tri<TR3;tri++) tRows.push(T_N+((tri+0.5)/TR3)*(T_S-T_N));
  for(var tr2=0;tr2<TR3;tr2++){ tI[tr2]=[]; for(var tc2=0;tc2<TC3;tc2++) tI[tr2][tc2]=rnode(tCols[tc2],tRows[tr2]); }
  for(var tr3=0;tr3<TR3;tr3++) for(var tc3=0;tc3<TC3;tc3++){ if(tc3<TC3-1) redge(tI[tr3][tc3],tI[tr3][tc3+1]); if(tr3<TR3-1) redge(tI[tr3][tc3],tI[tr3+1][tc3]); }
  for(var trp=0;trp<TR3;trp++) for(var tcp=0;tcp<TC3;tcp++) roadPad(roadNodes[tI[trp][tcp]].x,roadNodes[tI[trp][tcp]].z,roadMat);
  // ---- wire both crossings into the shared road graph ----
  var bNodeMain=rnode(TB_X,NCH_S), bNodeIsl=rnode(TB_X,NCH_N); radj(bNodeMain,bNodeIsl);
  var nearMB=-1,dMB=1e18; for(var i1=0;i1<MC;i1++){ var nd1=mI[MR-1][i1]; var d1=Math.abs(roadNodes[nd1].x-TB_X); if(d1<dMB){dMB=d1;nearMB=nd1;} } radj(bNodeMain,nearMB);
  var nearIB=-1,dIB=1e18; for(var i2=0;i2<TC3;i2++){ var nd2=tI[0][i2]; var d2=Math.abs(roadNodes[nd2].x-TB_X); if(d2<dIB){dIB=d2;nearIB=nd2;} } radj(bNodeIsl,nearIB);
  var dNodeMain=rnode(TD_X,NCH_S), dNodeIsl=rnode(TD_X,NCH_N); radj(dNodeMain,dNodeIsl);
  var nearMD=-1,dMD=1e18; for(var i3=0;i3<MC;i3++){ var nd3=mI[MR-1][i3]; var d3=Math.abs(roadNodes[nd3].x-TD_X); if(d3<dMD){dMD=d3;nearMD=nd3;} } radj(dNodeMain,nearMD);
  var nearID=-1,dID=1e18; for(var i4=0;i4<TC3;i4++){ var nd4=tI[0][i4]; var d4=Math.abs(roadNodes[nd4].x-TD_X); if(d4<dID){dID=d4;nearID=nd4;} } radj(dNodeIsl,nearID);
  // ---- obstacles on the third island ----
  for(var tr5=0;tr5<3;tr5++) for(var tc5=0;tc5<3;tc5++){ var tb5=tc(tc5,tr5); queueScatter(tb5[0],tb5[1],tb5[2],tb5[3],thirdGrid[tr5][tc5]); }
  // ---- NOW place every queued obstacle — every road, ramp, bridge, and connector on the entire map already exists at this point ----
  for(var sqi=0;sqi<scatterQueue.length;sqi++){ var sq=scatterQueue[sqi]; scatter(sq[0],sq[1],sq[2],sq[3],sq[4]); }
}
// ----------------------------------------------------------------------------
//  Weather
// ----------------------------------------------------------------------------
function buildWeather(type){
  weather=type||null; if(!weather) return;
  var N=(weather==="rain")?340:260, pos=new Float32Array(N*3), vel=new Float32Array(N);
  for(var i=0;i<N;i++){ pos[i*3]=rand(-52,52); pos[i*3+1]=rand(0,62); pos[i*3+2]=rand(-52,52); vel[i]=(weather==="rain")?rand(42,64):rand(6,12); }
  var geo=new THREE.BufferGeometry(); geo.setAttribute("position", new THREE.BufferAttribute(pos,3));
  var mat=new THREE.PointsMaterial({ color:(weather==="rain")?0x9fb6d6:0xffffff, size:(weather==="rain")?0.5:0.65,
    transparent:true, opacity:(weather==="rain")?0.5:0.85, depthWrite:false });
  weatherObj=new THREE.Points(geo,mat); weatherObj.userData.vel=vel; scene.add(weatherObj);
}
function updateWeather(dt){
  if(!weatherObj) return;
  var pos=weatherObj.geometry.attributes.position, vel=weatherObj.userData.vel, n=pos.count;
  weatherObj.position.set(player?player.group.position.x:0, 0, player?player.group.position.z:0);
  var slant=(weather==="rain")?14*dt:0;
  for(var i=0;i<n;i++){
    var y=pos.getY(i)-vel[i]*dt, x=pos.getX(i)-slant;
    if(weather==="snow") x+=Math.sin(y*0.6+i)*0.4*dt;
    if(y<0){ y=rand(46,62); x=rand(-52,52); pos.setZ(i,rand(-52,52)); }
    pos.setY(i,y); pos.setX(i,x);
  }
  pos.needsUpdate=true;
}
function buildIce(){
  var half=CITY;
  var sun=new THREE.DirectionalLight(0xeaf3ff,0.55); sun.position.set(-30,60,30); scene.add(sun);
  scene.add(new THREE.HemisphereLight(0xdfeeff,0x6878,0.5));
  function clearSpawn(x,z){ return Math.hypot(x,z-(CITY-14))<16 || Math.hypot(x,z+(CITY-18))<20; }
  for(var b=0;b<16;b++){
    var rx=rand(-half+6,half-6), rz=rand(-half+6,half-6); if(clearSpawn(rx,rz)) continue;
    var br=rand(1.6,3.2);
    var bm=new THREE.Mesh(new THREE.IcosahedronGeometry(br,0), new THREE.MeshStandardMaterial({color:0xbfe0f0,roughness:0.25,metalness:0.1,flatShading:true,transparent:true,opacity:0.92}));
    bm.position.set(rx,br*0.6,rz); bm.rotation.set(rand(0,3),rand(0,3),rand(0,3)); bm.castShadow=true; scene.add(bm);
    circles.push({x:rx,z:rz,r:br}); buildings.push({x:rx,z:rz,w:br*2,d:br*2});
  }
  for(var i=0;i<16;i++){
    var x=rand(-half+6,half-6), z=rand(-half+6,half-6); if(clearSpawn(x,z)) continue;
    var th=rand(3,5);
    var trunk=new THREE.Mesh(new THREE.CylinderGeometry(0.3,0.45,th,8), new THREE.MeshStandardMaterial({color:0x4a3a2c,roughness:1}));
    trunk.position.set(x,th/2,z); trunk.castShadow=true; scene.add(trunk);
    var fol=new THREE.Mesh(new THREE.ConeGeometry(1.5,3,8), new THREE.MeshStandardMaterial({color:0xe9f2f7,roughness:0.85}));
    fol.position.set(x,th+0.8,z); fol.castShadow=true; scene.add(fol);
    circles.push({x:x,z:z,r:0.8}); buildings.push({x:x,z:z,w:1.8,d:1.8});
  }
}
function buildHighway(){
  var half=CITY;
  var sun=new THREE.DirectionalLight(0xaecbff,0.6); sun.position.set(-40,80,-30); scene.add(sun);
  var lineMat=new THREE.MeshStandardMaterial({color:0xd8b13a,emissive:0x352808,roughness:0.7});
  for(var lane=-half+20; lane<=half-20; lane+=28){
    for(var z=-half+4; z<half-4; z+=8){ var dash=new THREE.Mesh(new THREE.BoxGeometry(0.4,0.05,4),lineMat); dash.position.set(lane,0.06,z); scene.add(dash); }
  }
  var barMat=new THREE.MeshStandardMaterial({color:0x2a2f3a,roughness:0.9});
  for(var bx=-half+34; bx<=half-34; bx+=28){
    for(var bz=-half+10; bz<half-10; bz+=22){
      if(Math.random()<0.35) continue;
      var seg=new THREE.Mesh(new THREE.BoxGeometry(1.4,1.6,12),barMat); seg.position.set(bx,0.8,bz); seg.castShadow=true; scene.add(seg);
      aabbs.push({minX:bx-0.7,maxX:bx+0.7,minZ:bz-6,maxZ:bz+6}); buildings.push({x:bx,z:bz,w:1.4,d:12});
    }
  }
  for(var p=0;p<14;p++){
    var lx=rand(-half+6,half-6), lz=rand(-half+6,half-6);
    if(Math.hypot(lx,lz-(CITY-16))<16 || Math.hypot(lx,lz+(CITY-18))<18) continue;
    var pole=new THREE.Mesh(new THREE.CylinderGeometry(0.3,0.3,7,8), new THREE.MeshStandardMaterial({color:0x3a4150,roughness:0.8})); pole.position.set(lx,3.5,lz); pole.castShadow=true; scene.add(pole);
    var lamp=new THREE.Mesh(new THREE.SphereGeometry(0.4,8,8), new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffe9b0,emissiveIntensity:1.2})); lamp.position.set(lx,7,lz); scene.add(lamp);
    circles.push({x:lx,z:lz,r:0.4}); buildings.push({x:lx,z:lz,w:0.8,d:0.8});
  }
}
function buildHarbor(){
  var half=CITY;
  var sun=new THREE.DirectionalLight(0xbcd0e6,0.55); sun.position.set(30,70,-30); scene.add(sun);
  scene.add(new THREE.HemisphereLight(0x3a5a7a,0x0a0c12,0.45));
  function clearSpawn(x,z){ return Math.hypot(x,z-(CITY-14))<16 || Math.hypot(x,z+(CITY-18))<20; }
  var water=new THREE.Mesh(new THREE.PlaneGeometry(CITY*2+140,140), new THREE.MeshStandardMaterial({color:0x12384f,roughness:0.18,metalness:0.65,transparent:true,opacity:0.94}));
  water.rotation.x=-Math.PI/2; water.position.set(0,-0.25,-CITY-58); scene.add(water);
  var cols=[0xc0492f,0x2f6b8c,0xb88a2a,0x3a7a4a,0x8a4a7a,0xb5b5b5];
  for(var i=0;i<26;i++){
    var x=rand(-half+8,half-8), z=rand(-half+8,half-8); if(clearSpawn(x,z)) continue;
    var w=6,h=2.6,d=2.6,stack=1+(Math.random()*2|0);
    for(var s=0;s<stack;s++){ var cm=new THREE.Mesh(new THREE.BoxGeometry(w,h,d), new THREE.MeshStandardMaterial({color:cols[(Math.random()*cols.length)|0],roughness:0.7,metalness:0.2})); cm.position.set(x,h/2+s*h,z); cm.castShadow=true; cm.receiveShadow=true; scene.add(cm); }
    aabbs.push({minX:x-w/2,maxX:x+w/2,minZ:z-d/2,maxZ:z+d/2}); buildings.push({x:x,z:z,w:w,d:d});
  }
  for(var c=0;c<3;c++){
    var cx=rand(-half+12,half-12), cz=rand(-half+12,half-12); if(clearSpawn(cx,cz)) continue;
    var leg=new THREE.Mesh(new THREE.BoxGeometry(1,16,1), new THREE.MeshStandardMaterial({color:0xd8a52a,roughness:0.7})); leg.position.set(cx,8,cz); leg.castShadow=true; scene.add(leg);
    var arm=new THREE.Mesh(new THREE.BoxGeometry(14,1,1), new THREE.MeshStandardMaterial({color:0xd8a52a,roughness:0.7})); arm.position.set(cx,15,cz); scene.add(arm);
    circles.push({x:cx,z:cz,r:1.0}); buildings.push({x:cx,z:cz,w:2,d:2});
  }
}

// ----------------------------------------------------------------------------
//  Cars
// ----------------------------------------------------------------------------
// ---- customization data ----
var STYLES = {
  supercar: { label:"Supercar", speed:1.12, turn:1.06, bump:0.32, mass:1.0, hint:"Sleek and fast with sharp, planted handling" },
  offroad:  { label:"Off-road", speed:0.96, turn:0.94, bump:0.55, mass:1.35, hint:"Slower but grippy — shrugs off curbs and walls" },
  hypercar: { label:"Hypercar", speed:1.24, turn:1.10, bump:0.30, mass:0.9, hint:"Highest top speed, twitchy at the limit" },
  truck:    { label:"Truck",    speed:0.88, turn:0.80, bump:0.62, mass:1.8, hint:"Heavy and slow, but very hard to shake off" },
  muscle:      { label:"Muscle",      speed:1.18, turn:0.82, bump:0.34, mass:1.5,  hint:"Brutal straight-line speed, lazy in the corners" },
  interceptor: { label:"Interceptor", speed:1.10, turn:1.04, bump:0.40, mass:1.15, hint:"Balanced pursuit sedan with a steel push bar" },
  van:         { label:"Van",         speed:0.82, turn:0.70, bump:0.72, mass:2.1,  hint:"A rolling wall — bulldozes others, shrugs off hits" },
  buggy:       { label:"Buggy",       speed:1.00, turn:1.24, bump:0.60, mass:0.8,  hint:"Feather-light and razor-sharp; loves rough ground" },
  suv:         { label:"SUV",          speed:0.94, turn:0.92, bump:0.52, mass:1.4,  hint:"Roomy, stable family hauler that soaks up bumps" },
  sedan:       { label:"Sedan",        speed:1.02, turn:1.00, bump:0.42, mass:1.1,  hint:"A nimble, balanced everyday four-door" },
  tank:        { label:"Tank",         speed:0.62, turn:0.56, bump:0.85, mass:2.7,  hint:"Unstoppable steel beast — crushes all, turns like a barge" },
  moto:        { label:"Motorcycle",   speed:1.12, turn:1.42, bump:0.70, mass:0.5,  hint:"Insanely nimble and quick — but one hit and you're done" },
  hothatch:    { label:"Hot Hatch",    speed:1.04, turn:1.18, bump:0.50, mass:0.95, hint:"Cheeky pocket rocket; darty, light and fun" },
  limo:        { label:"Limo",         speed:1.00, turn:0.78, bump:0.45, mass:1.6,  hint:"Long and luxurious; sweeping turns, heaps of momentum" },
  bus:         { label:"Bus",          speed:0.80, turn:0.60, bump:0.80, mass:2.4,  hint:"A rolling fortress — long, heavy, near-impossible to stop" },
  monster:     { label:"Monster Truck",speed:0.92, turn:0.82, bump:0.82, mass:2.0,  hint:"Crawls over anything on giant tires" },
  swat:        { label:"SWAT Truck",   speed:0.86, turn:0.70, bump:0.84, mass:2.3,  hint:"Armored police truck; rams hard and shrugs off damage" },
  semi:        { label:"Semi Truck",   speed:0.78, turn:0.50, bump:0.86, mass:2.9,  hint:"Articulated 18-wheeler — colossal and near-unstoppable" },
  garbage:     { label:"Garbage Truck",speed:0.80, turn:0.66, bump:0.80, mass:2.3,  hint:"Civic workhorse" }
};
var FINISHES = {
  gloss:  { label:"Gloss",  rough:0.32, metal:0.55, cc:1.0,  ccr:0.08 },
  matte:  { label:"Matte",  rough:0.80, metal:0.15, cc:0.25, ccr:0.6  },
  chrome: { label:"Chrome", rough:0.06, metal:1.00, cc:1.0,  ccr:0.04 }
};
var PAINTS = [0x2f6bff,0xff3b4e,0xffb020,0x18c08a,0xb05cff,0xff7a3c,0xeef2f7,0x111722,0x6c7a8c,0x12d6c9];
var RIMS   = [0x0a0c10,0xc8ccd4,0xffd76a,0x2f6bff,0xff3b4e];

var playerConfig = { color:0x2f6bff, roof:0x0a0e16, wheel:0xc8ccd4, finish:"gloss", style:"supercar" };
var COP_CONFIG   = { color:0x2f6bff, roof:0x0a0e16, wheel:0xc8ccd4, finish:"gloss", style:"supercar" };
var ROB_CONFIG   = { color:0xb01b2b, roof:0x0a0e16, wheel:0x1a1d24, finish:"gloss", style:"supercar" };
// ---- Progression / persistence ----
var SAVE_KEY="nightpatrol_v1", lastEarn=0;
var prog={ bank:0, bestCash:0, bestEscape:0, unlocks:{}, upgrades:{engine:0,tires:0,armor:0,nitro:0}, cop:null, rob:null };
var UPGRADES={
  engine:{ label:"Engine", max:3, prices:[700,1400,2800], desc:"More top speed & acceleration" },
  tires: { label:"Tires",  max:3, prices:[500,1000,2000], desc:"Sharper grip & cornering" },
  armor: { label:"Armor",  max:3, prices:[600,1200,2400], desc:"Take less damage, hit harder" },
  nitro: { label:"Nitro",  max:3, prices:[450,900,1800],  desc:"Stronger boost, refills faster" }
};
var PRICES={ paint:{}, rim:{}, finish:{matte:400,chrome:800}, style:{offroad:600,hypercar:1500,truck:900,muscle:1100,interceptor:1300,van:800,buggy:1000,suv:700,sedan:500,tank:2200,moto:900,hothatch:600,limo:1600,bus:1400,monster:1800,swat:2000,semi:2500} };
(function(){ for(var i=0;i<PAINTS.length;i++) if(i>=4) PRICES.paint[PAINTS[i]]=(i-3)*150;
            for(var j=0;j<RIMS.length;j++) if(j>=2) PRICES.rim[RIMS[j]]=(j-1)*200; })();
function priceOf(cat,val){ var p=PRICES[cat]; return (p && p[val]!=null)?p[val]:0; }
function isUnlocked(cat,val){ return priceOf(cat,val)===0 || !!prog.unlocks[cat+":"+val]; }
function loadSave(){ try{ var s=localStorage.getItem(SAVE_KEY); if(s){ var o=JSON.parse(s); for(var k in o) prog[k]=o[k]; } }catch(e){}
  if(!prog.unlocks) prog.unlocks={}; if(!prog.upgrades) prog.upgrades={engine:0,tires:0,armor:0,nitro:0}; if(prog.cop) COP_CONFIG=prog.cop; if(prog.rob) ROB_CONFIG=prog.rob;
  if(prog.settings){ for(var sk in settings){ if(prog.settings[sk]!==undefined) settings[sk]=prog.settings[sk]; } } }
function persist(){ prog.cop=COP_CONFIG; prog.rob=ROB_CONFIG; prog.settings=settings; try{ localStorage.setItem(SAVE_KEY, JSON.stringify(prog)); }catch(e){} }
function buyItem(cat,val){
  if(isUnlocked(cat,val)) return true;
  var pr=priceOf(cat,val);
  if(prog.bank>=pr){ prog.bank-=pr; prog.unlocks[cat+":"+val]=true; persist(); updateBankUI();
    beep(900,0.08); setTimeout(function(){beep(1200,0.1);},80); return true; }
  beep(180,0.2,"sawtooth"); var b=document.getElementById("bank"); if(b){ b.classList.remove("flash"); void b.offsetWidth; b.classList.add("flash"); }
  return false;
}
function updateBankUI(){
  var b=document.getElementById("bank"); if(b) b.textContent="$"+prog.bank;
  var m=document.getElementById("menuStats");
  if(m){ var s="Bank $"+prog.bank; if(prog.bestCash) s+="  ·  Best round $"+prog.bestCash; if(prog.bestEscape) s+="  ·  Best escape "+formatTime(prog.bestEscape); m.textContent=s; }
}
function upgradeCost(cat){ var u=UPGRADES[cat], t=prog.upgrades[cat]||0; return t>=u.max?null:u.prices[t]; }
function buyUpgrade(cat){
  var cost=upgradeCost(cat); if(cost==null) return false;
  if(prog.bank>=cost){ prog.bank-=cost; prog.upgrades[cat]=(prog.upgrades[cat]||0)+1; persist(); updateBankUI(); refreshUpgrades();
    beep(900,0.08); setTimeout(function(){beep(1240,0.1);},80); return true; }
  beep(180,0.2,"sawtooth"); var b=document.getElementById("bank"); if(b){ b.classList.remove("flash"); void b.offsetWidth; b.classList.add("flash"); }
  return false;
}
function refreshUpgrades(){
  var host=document.getElementById("upgradeRows"); if(!host) return;
  host.innerHTML="";
  ["engine","tires","armor","nitro"].forEach(function(cat){
    var u=UPGRADES[cat], t=prog.upgrades[cat]||0;
    var row=document.createElement("div"); row.className="up-row";
    var lab=document.createElement("div"); lab.className="up-label";
    lab.innerHTML="<span class='up-name'>"+u.label+"</span><span class='up-desc'>"+u.desc+"</span>";
    row.appendChild(lab);
    var pips=document.createElement("div"); pips.className="up-pips";
    for(var i=0;i<u.max;i++){ var p=document.createElement("span"); p.className="up-pip"+(i<t?" on":""); pips.appendChild(p); }
    row.appendChild(pips);
    var btn=document.createElement("button"); btn.className="up-buy";
    var cost=upgradeCost(cat);
    if(cost==null){ btn.textContent="MAX"; btn.classList.add("maxed"); }
    else { btn.textContent="$"+cost; btn.addEventListener("click",function(){ buyUpgrade(cat); }); }
    row.appendChild(btn);
    host.appendChild(row);
  });
}
function applyUpgrades(e){
  var u=prog.upgrades||{}, eng=u.engine||0, tir=u.tires||0, arm=u.armor||0, nit=u.nitro||0;
  e.maxSpeed*=(1+0.08*eng); e.accelMul=1+0.10*eng;
  e.gripMul=1+0.12*tir;
  e.dmgTakenMul=1-0.15*arm; e.mass*=(1+0.06*arm);
  e.boostPow=1+0.06*nit; e.boostEcon=1+0.25*nit; e.boostRegen=1+0.30*nit;
}

function makeWheel(r, ww, rimColor){
  var grp=new THREE.Group();
  var rimR=r*0.62;
  // rounded-sidewall tire via lathe
  var prof=[
    new THREE.Vector2(rimR, -ww*0.5),
    new THREE.Vector2(r*0.92,-ww*0.5),
    new THREE.Vector2(r,    -ww*0.30),
    new THREE.Vector2(r,     ww*0.30),
    new THREE.Vector2(r*0.92, ww*0.5),
    new THREE.Vector2(rimR,  ww*0.5)
  ];
  var tire=new THREE.Mesh(new THREE.LatheGeometry(prof,28), TIRE_MAT);
  tire.rotation.z=Math.PI/2; tire.castShadow=true; grp.add(tire);
  // brake disc behind rim
  var disc=new THREE.Mesh(new THREE.CylinderGeometry(rimR*0.82,rimR*0.82,ww*0.5,20),
    new THREE.MeshStandardMaterial({color:0x3a3f47,roughness:0.4,metalness:0.85}));
  disc.rotation.z=Math.PI/2; grp.add(disc);
  // caliper
  var cal=new THREE.Mesh(new THREE.BoxGeometry(ww*0.5,rimR*0.5,0.12),
    new THREE.MeshStandardMaterial({color:0xc81f2e,roughness:0.4,metalness:0.4}));
  cal.position.set(0,rimR*0.6,0); grp.add(cal);
  // alloy rim
  var rimMat=new THREE.MeshPhysicalMaterial({color:rimColor,roughness:0.22,metalness:0.95,clearcoat:0.6,envMapIntensity:1.3});
  var rim=new THREE.Mesh(new THREE.CylinderGeometry(rimR,rimR,ww*0.5,22), rimMat);
  rim.rotation.z=Math.PI/2; rim.position.x=ww*0.16; grp.add(rim);
  for(var i=0;i<6;i++){
    var sp=new THREE.Mesh(new THREE.BoxGeometry(ww*0.22,0.07,r*1.02), rimMat);
    sp.rotation.x=i*Math.PI/6; sp.position.x=ww*0.16; grp.add(sp);
  }
  // chrome lip + hub
  var lip=new THREE.Mesh(new THREE.TorusGeometry(rimR,0.055,8,24),
    new THREE.MeshPhysicalMaterial({color:0xcfd4dc,roughness:0.14,metalness:1,envMapIntensity:1.4}));
  lip.rotation.y=Math.PI/2; lip.position.x=ww*0.42; grp.add(lip);
  var hub=new THREE.Mesh(new THREE.CylinderGeometry(rimR*0.2,rimR*0.2,ww*0.6,10), rimMat);
  hub.rotation.z=Math.PI/2; hub.position.x=ww*0.18; grp.add(hub);
  return grp;
}

function makeCar(isCop, cfg){
  cfg = cfg || playerConfig;
  var fn = FINISHES[cfg.finish] || FINISHES.gloss;
  var style = cfg.style || "supercar";
  var g=new THREE.Group();

  var paint =new THREE.MeshPhysicalMaterial({color:cfg.color,roughness:fn.rough,metalness:fn.metal,clearcoat:fn.cc,clearcoatRoughness:fn.ccr,envMapIntensity:1.25});
  var paintDark=new THREE.MeshPhysicalMaterial({color:new THREE.Color(cfg.color).multiplyScalar(0.5),roughness:fn.rough,metalness:fn.metal,clearcoat:fn.cc,clearcoatRoughness:fn.ccr,envMapIntensity:1.2});
  var glass =new THREE.MeshPhysicalMaterial({color:0x070b12,roughness:0.04,metalness:0.1,clearcoat:1.0,clearcoatRoughness:0.04,envMapIntensity:1.6});
  var trim  =new THREE.MeshStandardMaterial({color:0x0b0d12,roughness:0.55,metalness:0.3});
  var chrome=new THREE.MeshPhysicalMaterial({color:0xcfd4dc,roughness:0.16,metalness:1.0,envMapIntensity:1.4});
  var darkMt=new THREE.MeshStandardMaterial({color:0x07090d,roughness:0.5,metalness:0.6});
  var head  =new THREE.MeshStandardMaterial({color:0xfff6d0,emissive:0xfff0b0,emissiveIntensity:1.7});
  var tailM =new THREE.MeshStandardMaterial({color:0xff2a2a,emissive:0xff2030,emissiveIntensity:1.4});
  var auxM  =new THREE.MeshStandardMaterial({color:0xeaf6ff,emissive:0xdfefff,emissiveIntensity:1.0});
  var plateM=new THREE.MeshStandardMaterial({color:0xd8dde6,roughness:0.6});

  function add(mesh,x,y,z,rx){ mesh.position.set(x,y,z); if(rx)mesh.rotation.x=rx; mesh.castShadow=true; mesh.receiveShadow=true; g.add(mesh); return mesh; }
  function bx(w,h,l,mat){ return new THREE.Mesh(new THREE.BoxGeometry(w,h,l),mat); }
  function xmid(pts){ var lo=1e9,hi=-1e9; pts.forEach(function(p){ var xs; if(p[0]==='L')xs=[p[1]]; else if(p[0]==='Q')xs=[p[1],p[3]]; else xs=[p[0]]; xs.forEach(function(x){ if(x<lo)lo=x; if(x>hi)hi=x; }); }); return (lo+hi)/2; }
  function extr(pts,width,bevel,clearance,mat,cx){
    var sh=new THREE.Shape(); sh.moveTo(pts[0][0],pts[0][1]);
    for(var i=1;i<pts.length;i++){ var p=pts[i]; if(p[0]==='Q') sh.quadraticCurveTo(p[1],p[2],p[3],p[4]); else sh.lineTo(p[1],p[2]); }
    sh.closePath();
    var geo=new THREE.ExtrudeGeometry(sh,{depth:width,bevelEnabled:true,bevelThickness:bevel,bevelSize:bevel,bevelSegments:4,steps:1,curveSegments:20});
    geo.translate(-cx,0,-width/2);
    var m=new THREE.Mesh(geo,mat); m.rotation.y=-Math.PI/2; m.position.y=clearance; m.castShadow=true; m.receiveShadow=true; g.add(m); return m;
  }
  function wheels(r,ww,tx,fz,rz){ [[-tx,fz],[tx,fz],[-tx,rz],[tx,rz]].forEach(function(o){ var w=makeWheel(r,ww,cfg.wheel); w.position.set(o[0],r,o[1]); g.add(w); }); }
  function fender(tx,z,r){ var f=new THREE.Mesh(new THREE.TorusGeometry(r*0.95,0.14,8,18),trim); f.rotation.y=Math.PI/2; f.position.set(tx,r,z); g.add(f); }
  function bulge(tx,z,r){ var b=new THREE.Mesh(new THREE.SphereGeometry(1,18,14),paint); b.scale.set(r*0.62,r*0.85,r*1.35); b.position.set(tx,r*0.78,z); b.castShadow=true; g.add(b); return b; }
  function dome(w,h,l,y,z,mat){ var c=new THREE.Mesh(new THREE.SphereGeometry(1,22,18),mat||glass); c.scale.set(w,h,l); c.position.set(0,y,z); c.castShadow=true; g.add(c); return c; }
  function headlight(x,z,y,w,h){ var l=new THREE.Mesh(new THREE.SphereGeometry(1,12,10),head); l.scale.set(w,h,0.16); l.position.set(x,y,z); g.add(l); }
  function taillight(z,y,w){ add(bx(w,0.16,0.08,tailM),0,y,z); }
  function mirror(x,z,y){ var st=new THREE.Mesh(new THREE.CylinderGeometry(0.04,0.04,0.2,8),trim); st.rotation.z=Math.PI/2; st.position.set(x,y,z); g.add(st); var pod=new THREE.Mesh(new THREE.SphereGeometry(1,10,8),paint); pod.scale.set(0.15,0.11,0.1); pod.position.set(x+(x>0?0.17:-0.17),y+0.02,z); g.add(pod); }
  function grille(z,y,w,h){ add(bx(w,h,0.06,darkMt),0,y,z); for(var i=-1;i<=1;i++){ add(bx(w*0.92,0.025,0.09,chrome),0,y+i*h*0.28,z); } }
  function exhaust(x,z){ var e=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.11,0.28,12),chrome); e.rotation.x=Math.PI/2; e.position.set(x,0.4,z); g.add(e); }
  function plateAt(z,y){ add(bx(0.55,0.18,0.04,plateM),0,y,z); }
  function pillar(x,y,z,rx){ var p=bx(0.07,0.44,0.09,paint); p.position.set(x,y,z); if(rx)p.rotation.x=rx; g.add(p); }

  var roofY=1.5;

  if(style==="supercar"){
    // --- Chiron-style supercar (homage; no badges/branding) ---
    var carbon=new THREE.MeshPhysicalMaterial({color:0x15171c,roughness:0.32,metalness:0.6,clearcoat:1.0,clearcoatRoughness:0.18,envMapIntensity:1.1});
    var bodyP=[[0.15,0.0],['L',3.98,0.0],['Q',4.24,0.10,4.05,0.36],['Q',3.7,0.46,3.25,0.54],['L',2.45,0.62],['L',0.95,0.70],['L',0.4,0.70],['Q',0.05,0.46,0.15,0.0]];
    var cx=xmid(bodyP);
    var _bm=extr(bodyP,1.70,0.18,0.26,paint,cx);     // rounded low-wide body
    _bm.geometry.computeBoundingBox(); var fZ=_bm.geometry.boundingBox.max.x, rZ=_bm.geometry.boundingBox.min.x;
    fender(1.08,1.5,0.52); fender(-1.08,1.5,0.52); fender(1.08,-1.5,0.52); fender(-1.08,-1.5,0.52);
    dome(0.82,0.44,1.20,1.04,-0.05,glass);           // smooth curved canopy
    add(bx(0.05,0.05,1.5,carbon),0,1.22,-0.6);       // slim dorsal spine

    // signature side C-line in carbon, wrapping each side intake
    for(var sgn=-1; sgn<=1; sgn+=2){
      var cline=new THREE.Mesh(new THREE.TorusGeometry(0.56,0.085,10,22, Math.PI*1.2), carbon);
      cline.rotation.y=Math.PI/2; cline.rotation.x=-0.5;
      cline.position.set(sgn*1.0, 0.78, -0.1); g.add(cline);
      add(bx(0.05,0.34,0.62,carbon), sgn*1.02, 0.6, 0.2);   // dark side intake
    }

    // horseshoe grille (chrome surround + vertical slats)
    var hs=new THREE.Mesh(new THREE.TorusGeometry(0.27,0.05,10,24), chrome);
    hs.scale.set(0.85,1.25,1); hs.position.set(0,0.36,fZ-0.26); g.add(hs);
    add(bx(0.42,0.60,0.06,darkMt),0,0.36,fZ-0.28);
    for(var v=-2; v<=2; v++){ add(bx(0.035,0.54,0.08,chrome), v*0.09, 0.36, fZ-0.26); }

    // slim LED headlights on the nose (pinned to the front surface)
    for(var hl=-1; hl<=1; hl+=2){
      add(bx(0.5,0.14,0.24,head), hl*0.58,0.46,fZ-0.30);
      add(bx(0.52,0.05,0.12,carbon), hl*0.58,0.58,fZ-0.27);
    }

    // front splitter + carbon sills
    add(bx(1.92,0.08,0.30,carbon),0,0.10,2.0);
    add(bx(2.12,0.12,2.4,carbon),0,0.30,0);

    // full-width rear LED light bar
    add(bx(1.92,0.22,0.12,darkMt),0,0.55,rZ+0.44);
    add(bx(1.76,0.14,0.22,tailM),0,0.55,rZ+0.40);

    // central exhaust + diffuser fins
    add(bx(0.92,0.28,0.12,carbon),0,0.20,-1.99);
    for(var df=-2; df<=2; df++){ add(bx(0.05,0.26,0.18,carbon), df*0.17,0.20,-1.97); }
    var ex1=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.11,0.22,14),chrome); ex1.rotation.x=Math.PI/2; ex1.position.set(-0.13,0.5,-2.0); g.add(ex1);
    var ex2=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.11,0.22,14),chrome); ex2.rotation.x=Math.PI/2; ex2.position.set(0.13,0.5,-2.0); g.add(ex2);

    // flush deployable-style spoiler lip
    add(bx(1.9,0.05,0.34,carbon),0,0.92,-1.84);

    mirror(0.97,0.40,1.0); mirror(-0.97,0.40,1.0);
    plateAt(2.0,0.16); plateAt(-1.95,0.48);
    // two-tone lower valance + side beltline crease
    add(bx(2.08,0.18,3.3,paintDark),0,0.19,0);
    add(bx(0.05,0.03,2.4,carbon),1.02,0.60,0); add(bx(0.05,0.03,2.4,carbon),-1.02,0.60,0);
    // side intakes ahead of rear wheels + hood vent + outer quad-LED accents
    add(bx(0.06,0.24,0.5,darkMt),1.03,0.55,-0.8); add(bx(0.06,0.24,0.5,darkMt),-1.03,0.55,-0.8);
    add(bx(0.5,0.02,0.5,darkMt),0,0.67,1.2);
    add(bx(0.16,0.14,0.22,head),0.8,0.46,fZ-0.28); add(bx(0.16,0.14,0.22,head),-0.8,0.46,fZ-0.28);
    roofY=1.42; wheels(0.52,0.58,1.08,1.5,-1.5);

  } else if(style==="hypercar"){
    // --- Bolide-style track hypercar (homage; no badges/branding) ---
    var carbon=new THREE.MeshPhysicalMaterial({color:0x15171c,roughness:0.30,metalness:0.6,clearcoat:1.0,clearcoatRoughness:0.16,envMapIntensity:1.1});
    var bodyP=[[0.15,0.0],['L',4.25,0.0],['Q',4.5,0.08,4.32,0.26],['Q',3.5,0.36,3.0,0.42],['L',2.1,0.50],['L',0.9,0.56],['L',0.32,0.60],['Q',0.05,0.40,0.15,0.0]];
    var cx=xmid(bodyP);
    var _bm=extr(bodyP,1.66,0.18,0.22,paint,cx);     // rounded ultra-low body
    _bm.geometry.computeBoundingBox(); var fZ=_bm.geometry.boundingBox.max.x, rZ=_bm.geometry.boundingBox.min.x;
    fender(1.1,1.6,0.54); fender(-1.1,1.6,0.54); fender(1.1,-1.55,0.54); fender(-1.1,-1.55,0.54);
    dome(0.66,0.40,1.05,0.92,0.05,glass);            // tight teardrop canopy
    pillar(0.62,0.90,0.20,-0.55); pillar(-0.62,0.90,0.20,-0.55);
    // central roof airscoop -> tall shark fin
    add(bx(0.50,0.32,0.50,darkMt),0,1.14,0.55);      // scoop mouth
    add(bx(0.34,0.50,2.0,carbon),0,1.16,-0.50);      // tall central fin
    // front splitter + dive-plane canards
    add(bx(2.05,0.06,0.34,carbon),0,0.10,2.02);
    for(var cs=-1; cs<=1; cs+=2){ var can=bx(0.5,0.04,0.34,carbon); can.position.set(cs*0.96,0.34,1.94); can.rotation.z=cs*0.18; g.add(can); }
    // small horseshoe grille + slim angled headlights
    var hg=new THREE.Mesh(new THREE.TorusGeometry(0.2,0.045,10,22),chrome); hg.scale.set(0.8,1.2,1); hg.position.set(0,0.32,fZ-0.22); g.add(hg);
    add(bx(0.30,0.42,0.05,darkMt),0,0.32,fZ-0.24);
    for(var hl=-1; hl<=1; hl+=2){ add(bx(0.46,0.12,0.22,head), hl*0.52,0.40,fZ-0.28); var hd=new THREE.Mesh(new THREE.SphereGeometry(0.07,10,8),head); hd.position.set(hl*0.30,0.30,fZ-0.25); g.add(hd); }
    // carbon sills
    add(bx(2.16,0.10,2.4,carbon),0,0.28,0);
    // big swan-neck rear wing
    for(var wm=-1; wm<=1; wm+=2){ var mnt=bx(0.10,0.62,0.12,carbon); mnt.position.set(wm*0.62,1.22,-1.82); mnt.rotation.x=0.25; g.add(mnt); }
    add(bx(2.15,0.07,0.62,carbon),0,1.52,-1.95);
    add(bx(0.06,0.18,0.6,carbon),-1.03,1.52,-1.95); add(bx(0.06,0.18,0.6,carbon),1.03,1.52,-1.95);
    // X-shaped taillights each side
    for(var xs=-1; xs<=1; xs+=2){
      var a1=bx(0.05,0.40,0.18,tailM); a1.position.set(xs*0.55,0.52,rZ+0.42); a1.rotation.z=0.7; g.add(a1);
      var a2=bx(0.05,0.40,0.18,tailM); a2.position.set(xs*0.55,0.52,rZ+0.42); a2.rotation.z=-0.7; g.add(a2);
    }
    // rear diffuser + central exhaust
    add(bx(1.0,0.30,0.12,carbon),0,0.20,-2.0);
    for(var df=-2; df<=2; df++){ add(bx(0.05,0.28,0.18,carbon), df*0.18,0.20,-1.98); }
    var ex1=new THREE.Mesh(new THREE.CylinderGeometry(0.09,0.10,0.20,12),chrome); ex1.rotation.x=Math.PI/2; ex1.position.set(-0.12,0.46,-2.02); g.add(ex1);
    var ex2=new THREE.Mesh(new THREE.CylinderGeometry(0.09,0.10,0.20,12),chrome); ex2.rotation.x=Math.PI/2; ex2.position.set(0.12,0.46,-2.02); g.add(ex2);
    mirror(0.92,0.36,0.80); mirror(-0.92,0.36,0.80);
    plateAt(-1.98,0.42);
    // two-tone lower valance + carbon side pods + front lip + extra canards
    add(bx(2.04,0.16,3.5,paintDark),0,0.15,0);
    add(bx(0.28,0.30,1.3,carbon),1.0,0.40,0.2); add(bx(0.28,0.30,1.3,carbon),-1.0,0.40,0.2);
    add(bx(1.4,0.05,0.2,carbon),0,0.06,2.12);
    for(var c2=-1;c2<=1;c2+=2){ var cn=bx(0.4,0.03,0.26,carbon); cn.position.set(c2*1.0,0.52,1.78); cn.rotation.z=c2*0.18; g.add(cn); }
    roofY=1.52; wheels(0.54,0.6,1.1,1.6,-1.55);

  } else if(style==="offroad"){
    var bodyP=[[0.15,0.0],['L',3.75,0.0],['Q',3.98,0.2,3.88,0.7],['L',3.4,0.86],['Q',3.0,0.92,2.95,1.02],['L',0.7,1.04],['L',0.4,0.95],['Q',0.05,0.5,0.15,0.0]];
    var cabinP=[[0.7,1.04],['L',2.9,1.04],['Q',2.95,1.12,2.92,1.7],['L',1.0,1.76],['Q',0.7,1.74,0.7,1.04]];
    var cx=xmid(bodyP);
    extr(bodyP,2.02,0.08,0.5,paint,cx);
    extr(cabinP,1.74,0.04,0.5,glass,cx);
    add(bx(1.7,0.07,1.9,paint),0,2.30,-0.45);
    pillar(0.86,1.42,0.78,-0.05); pillar(-0.86,1.42,0.78,-0.05); pillar(0.86,1.42,-1.2,0.05); pillar(-0.86,1.42,-1.2,0.05);
    add(bx(1.95,0.1,1.7,trim),0,2.46,-0.45);
    add(bx(1.5,0.18,0.2,auxM),0,2.62,0.2);
    add(bx(2.16,0.22,0.5,trim),0,0.62,1.85);
    headlight(-0.66,1.96,1.0,0.32,0.18); headlight(0.66,1.96,1.0,0.32,0.18);
    grille(1.95,1.0,1.1,0.4);
    taillight(-1.92,1.3,1.6);
    mirror(1.04,1.7,0.95); mirror(-1.04,1.7,0.95);
    plateAt(1.98,0.7); plateAt(-1.9,1.0);
    fender(-1.04,1.4,0.74); fender(1.04,1.4,0.74); fender(-1.04,-1.4,0.74); fender(1.04,-1.4,0.74);
    roofY=2.50; wheels(0.74,0.6,1.06,1.4,-1.4);

  } else if(style==="muscle"){
    var bodyP=[[0.12,0.0],['L',4.5,0.0],['Q',4.74,0.12,4.55,0.42],['L',3.6,0.52],['L',1.2,0.6],['L',0.5,0.58],['Q',0.05,0.4,0.12,0.0]];
    var cabinP=[[1.3,0.6],['L',3.2,0.6],['Q',3.25,0.7,3.2,1.28],['L',1.5,1.32],['Q',1.3,1.3,1.3,0.6]];
    var cx=xmid(bodyP);
    extr(bodyP,1.88,0.16,0.30,paint,cx);
    extr(cabinP,1.62,0.05,0.30,glass,cx);
    add(bx(1.46,0.07,1.78,paint),0,1.33,-0.05);            // painted roof over the glass
    add(bx(2.02,0.18,4.3,paintDark),0,0.18,0);              // two-tone lower
    add(bx(0.7,0.18,0.9,trim),0,0.74,1.2); add(bx(0.5,0.12,0.55,darkMt),0,0.86,1.2); // hood scoop
    add(bx(1.95,0.12,0.5,paint),0,0.8,-1.95);               // ducktail spoiler
    add(bx(0.12,0.3,0.42,paint),0.82,0.66,-1.9); add(bx(0.12,0.3,0.42,paint),-0.82,0.66,-1.9);
    headlight(-0.78,2.18,0.5,0.3,0.22); headlight(0.78,2.18,0.5,0.3,0.22);
    grille(2.2,0.5,1.2,0.34);
    taillight(-2.2,0.55,1.95);
    mirror(1.02,1.0,0.95); mirror(-1.02,1.0,0.95);
    exhaust(0.6,-2.2); exhaust(-0.6,-2.2);
    plateAt(2.24,0.42); plateAt(-2.22,0.55);
    fender(1.06,1.6,0.58); fender(-1.06,1.6,0.58); fender(1.06,-1.6,0.58); fender(-1.06,-1.6,0.58);
    roofY=1.36; wheels(0.58,0.62,1.06,1.6,-1.6);

  } else if(style==="interceptor"){
    var bodyP=[[0.14,0.0],['L',4.2,0.0],['Q',4.42,0.14,4.28,0.5],['L',3.4,0.6],['L',1.0,0.64],['L',0.45,0.6],['Q',0.05,0.4,0.14,0.0]];
    var cabinP=[[1.0,0.64],['Q',1.6,0.66,2.0,1.2],['L',2.9,1.24],['Q',3.2,1.2,3.2,0.64],['L',1.0,0.64]];
    var cx=xmid(bodyP);
    extr(bodyP,1.84,0.12,0.34,paint,cx);
    extr(cabinP,1.66,0.05,0.34,glass,cx);
    add(bx(1.5,0.07,2.0,paint),0,1.28,-0.07);              // painted roof over the glass
    add(bx(0.06,0.5,1.6,trim),0.84,1.0,0.4); add(bx(0.06,0.5,1.6,trim),-0.84,1.0,0.4);
    pillar(0.84,1.0,1.5,-0.1); pillar(-0.84,1.0,1.5,-0.1); pillar(0.84,1.0,-0.7,0.1); pillar(-0.84,1.0,-0.7,0.1);
    add(bx(1.9,0.5,0.12,darkMt),0,0.5,2.3);                 // push bar
    add(bx(0.12,0.5,0.34,darkMt),0.7,0.5,2.18); add(bx(0.12,0.5,0.34,darkMt),-0.7,0.5,2.18);
    headlight(-0.74,2.16,0.56,0.3,0.2); headlight(0.74,2.16,0.56,0.3,0.2);
    grille(2.18,0.56,1.1,0.36);
    taillight(-2.12,0.6,1.85);
    mirror(1.0,1.2,0.98); mirror(-1.0,1.2,0.98);
    exhaust(0.6,-2.1);
    plateAt(2.2,0.46); plateAt(-2.12,0.6);
    fender(1.06,1.5,0.56); fender(-1.06,1.5,0.56); fender(1.06,-1.5,0.56); fender(-1.06,-1.5,0.56);
    roofY=1.44; wheels(0.56,0.6,1.06,1.5,-1.5);

  } else if(style==="van"){
    add(bx(2.1,1.7,4.4,paint),0,1.05,-0.1);                 // boxy body
    add(bx(2.0,1.0,0.16,glass),0,1.5,2.0);                  // windshield
    add(bx(2.1,0.7,0.7,paint),0,0.5,2.22);                  // hood
    add(bx(2.16,0.5,1.5,glass),0,1.55,0.6);                 // side windows
    add(bx(2.02,0.08,4.0,paintDark),0,1.92,-0.1);           // roof trim
    add(bx(2.16,0.42,4.4,paintDark),0,0.35,-0.1);           // two-tone lower
    headlight(-0.8,2.56,0.6,0.32,0.26); headlight(0.8,2.56,0.6,0.32,0.26);
    grille(2.57,0.55,1.3,0.4);
    taillight(-2.32,0.9,1.95);
    mirror(1.18,2.0,1.2); mirror(-1.18,2.0,1.2);
    plateAt(2.59,0.5); plateAt(-2.32,0.6);
    fender(1.08,1.5,0.62); fender(-1.08,1.5,0.62); fender(1.08,-1.5,0.62); fender(-1.08,-1.5,0.62);
    roofY=1.96; wheels(0.6,0.64,1.08,1.5,-1.5);

  } else if(style==="buggy"){
    var tube=new THREE.MeshStandardMaterial({color:0x111722,roughness:0.5,metalness:0.6});
    function bar(w,h,l,x,y,z,rx){ var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,l),tube); m.position.set(x,y,z); if(rx)m.rotation.x=rx; m.castShadow=true; g.add(m); }
    add(bx(1.7,0.3,3.4,paint),0,0.45,0);                    // platform
    add(bx(1.9,0.18,1.4,paintDark),0,0.3,0.2);              // skid plate
    bar(0.1,1.3,0.1, 0.78,1.1,-0.3); bar(0.1,1.3,0.1,-0.78,1.1,-0.3);   // rear hoops
    bar(0.1,1.1,0.1, 0.78,1.0,1.0,0.5); bar(0.1,1.1,0.1,-0.78,1.0,1.0,0.5); // front pillars
    var ws=bx(1.46,0.92,0.05,glass); ws.position.set(0,1.18,1.0); ws.rotation.x=0.5; ws.castShadow=true; g.add(ws);  // windscreen
    bar(1.66,0.1,0.1, 0,1.68,-0.3);                          // rear cross
    bar(0.1,0.1,1.7, 0.78,1.55,0.35); bar(0.1,0.1,1.7,-0.78,1.55,0.35);  // top rails
    add(bx(0.5,0.5,0.55,trim),0,0.85,-0.2);                  // seat
    add(bx(0.95,0.55,0.75,darkMt),0,0.72,-1.4);             // exposed engine
    headlight(-0.5,1.55,0.72,0.26,0.2); headlight(0.5,1.55,0.72,0.26,0.2);
    taillight(-1.5,0.72,1.4);
    exhaust(0.5,-1.65); exhaust(-0.5,-1.65);
    fender(1.0,1.3,0.64); fender(-1.0,1.3,0.64); fender(1.0,-1.3,0.64); fender(-1.0,-1.3,0.64);
    roofY=1.7; wheels(0.64,0.7,1.0,1.3,-1.3);

  } else if(style==="suv"){
    var bodyP=[[0.15,0.0],['L',3.95,0.0],['Q',4.18,0.18,4.08,0.62],['L',3.5,0.76],['Q',3.05,0.82,3.0,0.92],['L',0.7,0.94],['L',0.4,0.86],['Q',0.05,0.45,0.15,0.0]];
    var cabinP=[[0.78,0.94],['Q',1.2,0.96,1.5,1.5],['L',2.7,1.52],['Q',2.95,1.5,2.95,0.94],['L',0.78,0.94]];
    var cx=xmid(bodyP);
    extr(bodyP,1.96,0.10,0.42,paint,cx);
    extr(cabinP,1.72,0.05,0.42,glass,cx);
    add(bx(1.7,0.07,1.95,paint),0,2.04,-0.3);
    add(bx(0.1,0.12,1.9,trim),0.7,2.14,-0.3); add(bx(0.1,0.12,1.9,trim),-0.7,2.14,-0.3);   // roof rails
    pillar(0.84,1.26,0.95,-0.05); pillar(-0.84,1.26,0.95,-0.05); pillar(0.84,1.26,-1.1,0.05); pillar(-0.84,1.26,-1.1,0.05);
    add(bx(2.06,0.22,0.5,trim),0,0.5,1.9); add(bx(2.06,0.22,0.5,trim),0,0.5,-1.95);
    headlight(-0.7,2.02,0.92,0.32,0.18); headlight(0.7,2.02,0.92,0.32,0.18);
    grille(2.04,0.92,1.1,0.36);
    taillight(-1.98,1.1,1.7);
    mirror(1.04,1.5,0.92); mirror(-1.04,1.5,0.92);
    plateAt(2.06,0.6); plateAt(-1.98,0.9);
    fender(1.04,1.4,0.64); fender(-1.04,1.4,0.64); fender(1.04,-1.4,0.64); fender(-1.04,-1.4,0.64);
    roofY=2.18; wheels(0.64,0.56,1.04,1.4,-1.4);

  } else if(style==="sedan"){
    var bodyP=[[0.14,0.0],['L',4.15,0.0],['Q',4.38,0.14,4.24,0.5],['L',3.4,0.6],['L',1.0,0.64],['L',0.45,0.6],['Q',0.05,0.4,0.14,0.0]];
    var cabinP=[[1.1,0.64],['Q',1.7,0.66,2.05,1.18],['L',2.85,1.2],['Q',3.1,1.16,3.1,0.64],['L',1.1,0.64]];
    var cx=xmid(bodyP);
    extr(bodyP,1.8,0.12,0.34,paint,cx);
    extr(cabinP,1.62,0.05,0.34,glass,cx);
    add(bx(0.06,0.46,1.5,trim),0.82,1.0,0.45); add(bx(0.06,0.46,1.5,trim),-0.82,1.0,0.45);
    pillar(0.82,1.0,1.45,-0.1); pillar(-0.82,1.0,1.45,-0.1); pillar(0.82,1.0,-0.55,0.1); pillar(-0.82,1.0,-0.55,0.1);
    headlight(-0.72,2.12,0.54,0.3,0.18); headlight(0.72,2.12,0.54,0.3,0.18);
    grille(2.14,0.54,1.0,0.32);
    taillight(-2.08,0.58,1.75);
    mirror(1.0,1.15,0.95); mirror(-1.0,1.15,0.95);
    exhaust(0.55,-2.06);
    plateAt(2.16,0.44); plateAt(-2.08,0.58);
    fender(1.05,1.45,0.54); fender(-1.05,1.45,0.54); fender(1.05,-1.45,0.54); fender(-1.05,-1.45,0.54);
    roofY=1.4; wheels(0.55,0.58,1.05,1.45,-1.45);

  } else if(style==="tank"){
    var armor=new THREE.MeshStandardMaterial({color:cfg.color,roughness:0.8,metalness:0.45});
    var trackMat=new THREE.MeshStandardMaterial({color:0x14161b,roughness:0.92,metalness:0.3});
    var steel=new THREE.MeshStandardMaterial({color:0x3a3f47,roughness:0.6,metalness:0.7});
    add(bx(2.5,0.85,4.2,armor),0,0.95,0);                              // hull
    var gl=bx(2.5,0.95,1.1,armor); gl.position.set(0,0.95,2.0); gl.rotation.x=-0.6; gl.castShadow=true; g.add(gl);   // glacis
    for(var sgn=-1;sgn<=1;sgn+=2){
      var tk=bx(0.8,0.95,4.7,trackMat); tk.position.set(sgn*1.45,0.55,0); tk.castShadow=true; g.add(tk);
      for(var i=-2;i<=2;i++){ var rw=new THREE.Mesh(new THREE.CylinderGeometry(0.42,0.42,0.86,12),steel); rw.rotation.z=Math.PI/2; rw.position.set(sgn*1.45,0.42,i*0.85); g.add(rw); }
      add(bx(0.95,0.12,4.7,armor),sgn*1.45,1.05,0);                    // track fender
    }
    add(bx(1.9,0.7,2.0,armor),0,1.62,-0.25);                          // turret
    add(bx(1.4,0.45,1.4,armor),0,2.0,-0.25);                          // turret crown
    var barrel=new THREE.Mesh(new THREE.CylinderGeometry(0.14,0.16,3.4,14),steel); barrel.rotation.x=Math.PI/2; barrel.position.set(0,1.7,1.7); barrel.castShadow=true; g.add(barrel);
    var muzzle=new THREE.Mesh(new THREE.CylinderGeometry(0.22,0.22,0.4,14),steel); muzzle.rotation.x=Math.PI/2; muzzle.position.set(0,1.7,3.3); g.add(muzzle);
    add(bx(0.5,0.18,0.5,steel),0.42,2.28,-0.45);                      // commander hatch
    add(bx(0.9,0.24,0.06,glass),0,1.72,0.74);                        // turret vision block
    var pv=bx(0.62,0.18,0.05,glass); pv.position.set(0,1.32,2.2); pv.rotation.x=-0.6; pv.castShadow=true; g.add(pv);  // driver viewport
    headlight(-0.95,2.5,0.95,0.2,0.16); headlight(0.95,2.5,0.95,0.2,0.16);
    taillight(-2.1,0.9,1.6);
    roofY=2.3;

  } else if(style==="moto"){
    var fw=makeWheel(0.5,0.3,cfg.wheel); fw.position.set(0,0.5,1.15); g.add(fw);
    var rw=makeWheel(0.52,0.34,cfg.wheel); rw.position.set(0,0.52,-1.15); g.add(rw);
    add(bx(0.34,0.5,1.7,paint),0,0.85,-0.1);
    var tank2=new THREE.Mesh(new THREE.SphereGeometry(1,16,12),paint); tank2.scale.set(0.3,0.32,0.55); tank2.position.set(0,1.05,0.25); tank2.castShadow=true; g.add(tank2);
    add(bx(0.5,0.16,0.7,trim),0,1.02,-0.55); add(bx(0.46,0.12,0.5,darkMt),0,0.74,-0.55);
    var fork=bx(0.12,1.0,0.12,trim); fork.position.set(0,0.95,1.05); fork.rotation.x=-0.32; g.add(fork);
    add(bx(0.7,0.08,0.08,trim),0,1.32,0.95);
    add(bx(0.4,0.34,0.4,darkMt),0,0.66,0.15);
    var pipe=new THREE.Mesh(new THREE.CylinderGeometry(0.07,0.08,1.3,10),chrome); pipe.rotation.x=Math.PI/2; pipe.position.set(0.22,0.5,-0.5); g.add(pipe);
    headlight(0,0.95,1.0,0.2,0.2); taillight(-0.95,0.9,0.3);
    roofY=1.4;

  } else if(style==="hothatch"){
    var bodyP=[[0.14,0.0],['L',3.4,0.0],['Q',3.6,0.16,3.5,0.52],['L',2.9,0.62],['L',0.9,0.64],['L',0.4,0.56],['Q',0.05,0.36,0.14,0.0]];
    var cabinP=[[0.95,0.64],['Q',1.3,0.66,1.5,1.18],['L',2.5,1.2],['Q',2.8,1.12,2.95,0.64],['L',0.95,0.64]];
    var cx=xmid(bodyP);
    extr(bodyP,1.78,0.12,0.32,paint,cx); extr(cabinP,1.6,0.05,0.32,glass,cx);
    add(bx(1.5,0.08,0.5,paint),0,1.2,-1.35);
    pillar(0.82,1.0,0.9,-0.1); pillar(-0.82,1.0,0.9,-0.1); pillar(0.82,1.0,-0.7,0.12); pillar(-0.82,1.0,-0.7,0.12);
    headlight(-0.7,1.74,0.54,0.32,0.2); headlight(0.7,1.74,0.54,0.32,0.2);
    grille(1.76,0.5,0.95,0.3); taillight(-1.66,0.66,1.5);
    mirror(0.98,1.0,0.92); mirror(-0.98,1.0,0.92); exhaust(0.45,-1.7); exhaust(-0.45,-1.7);
    plateAt(1.78,0.42); plateAt(-1.66,0.58);
    fender(1.0,1.2,0.52); fender(-1.0,1.2,0.52); fender(1.0,-1.2,0.52); fender(-1.0,-1.2,0.52);
    roofY=1.32; wheels(0.52,0.56,1.0,1.2,-1.2);

  } else if(style==="limo"){
    var bodyP=[[0.14,0.0],['L',6.5,0.0],['Q',6.74,0.14,6.6,0.5],['L',5.6,0.6],['L',1.0,0.62],['L',0.45,0.58],['Q',0.05,0.4,0.14,0.0]];
    var cabinP=[[1.2,0.62],['Q',1.7,0.64,2.0,1.06],['L',5.0,1.08],['Q',5.25,1.04,5.3,0.62],['L',1.2,0.62]];
    var cx=xmid(bodyP);
    extr(bodyP,1.9,0.12,0.34,paint,cx); extr(cabinP,1.74,0.05,0.34,glass,cx);
    pillar(0.88,1.0,2.4,-0.1); pillar(-0.88,1.0,2.4,-0.1); pillar(0.88,1.0,0.4,0); pillar(-0.88,1.0,0.4,0); pillar(0.88,1.0,-1.6,0.1); pillar(-0.88,1.0,-1.6,0.1);
    headlight(-0.78,3.32,0.52,0.3,0.18); headlight(0.78,3.32,0.52,0.3,0.18);
    grille(3.34,0.52,1.0,0.3); taillight(-3.22,0.56,1.7);
    mirror(1.0,2.4,0.92); mirror(-1.0,2.4,0.92);
    plateAt(3.34,0.42); plateAt(-3.22,0.56);
    fender(1.05,2.6,0.54); fender(-1.05,2.6,0.54); fender(1.05,-2.6,0.54); fender(-1.05,-2.6,0.54);
    roofY=1.34; wheels(0.55,0.58,1.05,2.6,-2.6);

  } else if(style==="bus"){
    var bodyP=[[0.1,0.0],['L',6.5,0.0],['L',6.6,0.6],['L',6.5,2.5],['L',6.2,2.6],['L',0.4,2.6],['L',0.1,2.5],['L',0.0,0.6],['L',0.1,0.0]];
    var cx=xmid(bodyP);
    extr(bodyP,2.5,0.12,0.5,paint,cx);
    add(bx(2.54,0.8,5.8,glass),0,2.35,0);
    add(bx(2.42,0.12,6.0,paintDark),0,3.12,0);
    add(bx(0.05,1.8,1.2,darkMt),1.27,1.4,2.0);
    headlight(-0.95,3.25,1.1,0.3,0.22); headlight(0.95,3.25,1.1,0.3,0.22);
    grille(3.3,1.1,1.6,0.4); taillight(-3.25,1.2,2.0);
    plateAt(3.3,0.7); plateAt(-3.28,0.8);
    roofY=3.15; wheels(0.7,0.6,1.15,2.4,-2.4);

  } else if(style==="monster"){
    var bodyP=[[0.3,0.0],['L',4.0,0.0],['Q',4.3,0.2,4.2,0.7],['L',3.4,0.9],['L',1.0,0.92],['L',0.5,0.8],['Q',0.1,0.4,0.3,0.0]];
    var cabinP=[[1.1,0.92],['Q',1.5,0.96,1.8,1.6],['L',2.9,1.62],['Q',3.15,1.58,3.15,0.92],['L',1.1,0.92]];
    var cx=xmid(bodyP);
    extr(bodyP,2.1,0.12,1.3,paint,cx); extr(cabinP,1.9,0.06,1.3,glass,cx);
    add(bx(2.2,0.18,4.0,paintDark),0,1.2,0);
    add(bx(1.9,0.1,0.12,trim),0,2.7,-0.2);
    for(var li=-1;li<=1;li++){ var lb=new THREE.Mesh(new THREE.SphereGeometry(1,10,8),head); lb.scale.set(0.18,0.18,0.12); lb.position.set(li*0.5,2.82,-0.1); g.add(lb); }
    headlight(-0.8,2.32,1.7,0.34,0.22); headlight(0.8,2.32,1.7,0.34,0.22);
    grille(2.34,1.7,1.2,0.5); taillight(-2.1,1.6,1.5);
    var st1=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.1,1.2,10),chrome); st1.position.set(0.7,2.0,-0.4); g.add(st1);
    var st2=st1.clone(); st2.position.set(-0.7,2.0,-0.4); g.add(st2);
    plateAt(2.34,1.4);
    var bw=[[-1.35,1.55],[1.35,1.55],[-1.35,-1.55],[1.35,-1.55]];
    for(var wi=0;wi<bw.length;wi++){ var BW=makeWheel(1.15,0.95,cfg.wheel); BW.position.set(bw[wi][0],1.15,bw[wi][1]); g.add(BW); }
    roofY=2.4;

  } else if(style==="swat"){
    var armor=new THREE.MeshStandardMaterial({color:cfg.color,roughness:0.7,metalness:0.4});
    add(bx(2.5,1.7,4.6,armor),0,1.25,-0.1);
    add(bx(2.5,0.9,1.4,armor),0,1.05,2.0);
    add(bx(2.54,0.36,1.2,glass),0,1.95,1.6);
    add(bx(0.06,0.5,2.6,glass),1.26,1.7,-0.3); add(bx(0.06,0.5,2.6,glass),-1.26,1.7,-0.3);
    add(bx(2.42,0.12,4.4,paintDark),0,2.16,-0.1);
    add(bx(0.7,0.2,0.7,trim),0,2.32,-0.6);
    add(bx(2.3,0.16,0.18,chrome),0,0.7,2.85);
    add(bx(0.16,0.7,0.18,chrome),0.8,0.7,2.8); add(bx(0.16,0.7,0.18,chrome),-0.8,0.7,2.8);
    headlight(-0.85,2.66,1.0,0.3,0.22); headlight(0.85,2.66,1.0,0.3,0.22);
    grille(2.62,1.0,1.4,0.4); taillight(-2.4,1.2,1.8);
    plateAt(2.66,0.7); plateAt(-2.4,0.9);
    fender(1.1,1.5,0.66); fender(-1.1,1.5,0.66); fender(1.1,-1.5,0.66); fender(-1.1,-1.5,0.66);
    roofY=2.2; wheels(0.66,0.62,1.12,1.5,-1.5);

  } else if(style==="semi"){
    add(bx(2.4,2.3,2.6,paint),0,1.4,2.6);
    add(bx(2.44,0.7,1.4,glass),0,2.0,3.5);
    add(bx(2.5,0.18,0.18,chrome),0,0.7,3.95);
    var sx=new THREE.Mesh(new THREE.CylinderGeometry(0.12,0.12,1.8,10),chrome); sx.position.set(1.0,2.4,1.5); g.add(sx);
    var sx2=sx.clone(); sx2.position.set(-1.0,2.4,1.5); g.add(sx2);
    add(bx(2.6,2.6,6.5,paintDark),0,1.7,-2.4);
    add(bx(2.64,0.1,6.5,trim),0,3.0,-2.4);
    headlight(-0.85,3.95,1.0,0.3,0.22); headlight(0.85,3.95,1.0,0.3,0.22);
    grille(3.9,1.3,1.5,0.7); taillight(-5.6,1.0,1.8);
    plateAt(3.96,0.6); plateAt(-5.6,0.7);
    var fw1=makeWheel(0.7,0.55,cfg.wheel); fw1.position.set(1.15,0.7,2.9); g.add(fw1);
    var fw2=makeWheel(0.7,0.55,cfg.wheel); fw2.position.set(-1.15,0.7,2.9); g.add(fw2);
    for(var ss=-1;ss<=1;ss+=2){ for(var aa=0;aa<2;aa++){ var DW=makeWheel(0.7,0.55,cfg.wheel); DW.position.set(ss*1.15,0.7,0.9-aa*1.0); g.add(DW);} }
    for(var s2=-1;s2<=1;s2+=2){ for(var a2=0;a2<2;a2++){ var TW=makeWheel(0.7,0.55,cfg.wheel); TW.position.set(s2*1.15,0.7,-4.4-a2*1.0); g.add(TW);} }
    roofY=3.05;

  } else if(style==="garbage"){
    add(bx(2.4,1.2,1.8,paint),0,1.0,2.2);
    add(bx(2.44,0.5,1.0,glass),0,1.4,2.7);
    add(bx(2.6,2.0,4.2,paintDark),0,1.5,-0.6);
    add(bx(2.64,0.12,4.2,trim),0,2.55,-0.6);
    add(bx(2.5,1.4,0.8,trim),0,1.0,-2.9);
    add(bx(0.2,0.2,1.2,chrome),1.0,1.6,1.2); add(bx(0.2,0.2,1.2,chrome),-1.0,1.6,1.2);
    headlight(-0.8,3.15,0.8,0.3,0.2); headlight(0.8,3.15,0.8,0.3,0.2);
    grille(3.1,0.8,1.4,0.4); taillight(-3.35,0.9,1.8);
    plateAt(3.16,0.55); plateAt(-3.35,0.7);
    roofY=2.6; wheels(0.66,0.56,1.12,1.7,-1.9);

  } else {
    var bodyP=[[0.15,0.0],['L',4.3,0.0],['Q',4.52,0.2,4.42,0.7],['L',3.7,0.82],['Q',3.2,0.86,3.15,0.96],['L',3.0,1.0],['L',0.3,1.0],['Q',0.05,0.55,0.15,0.0]];
    var cabinP=[[1.9,1.0],['L',3.0,1.0],['Q',3.02,1.08,3.0,1.72],['L',1.95,1.76],['L',1.9,1.0]];
    var cx=xmid(bodyP);
    extr(bodyP,2.04,0.08,0.48,paint,cx);
    extr(cabinP,1.82,0.04,0.48,glass,cx);
    add(bx(1.78,0.07,1.0,paint),0,2.28,0.95);
    pillar(0.9,1.4,1.42,-0.06); pillar(-0.9,1.4,1.42,-0.06); pillar(0.9,1.4,0.5,0.04); pillar(-0.9,1.4,0.5,0.04);
    // open bed
    add(bx(2.04,0.5,1.7,paint),0,0.95,-1.05);
    add(bx(1.66,0.34,1.4,trim),0,1.08,-1.05);
    add(bx(2.04,0.42,0.1,paint),0,1.1,-1.92);
    add(bx(0.1,0.42,0.1,trim),-0.86,1.78,1.45); add(bx(0.1,0.42,0.1,trim),0.86,1.78,1.45);
    add(bx(1.8,0.1,0.1,trim),0,1.98,1.45);
    add(bx(2.18,0.26,0.5,trim),0,0.6,2.5);
    headlight(-0.7,2.45,1.0,0.34,0.2); headlight(0.7,2.45,1.0,0.34,0.2);
    grille(2.42,1.0,1.3,0.5);
    taillight(-1.98,1.0,1.7);
    mirror(1.1,1.65,1.5); mirror(-1.1,1.65,1.5);
    exhaust(0.7,-1.98);
    plateAt(2.46,0.7); plateAt(-1.98,0.7);
    fender(-1.06,1.5,0.7); fender(1.06,1.5,0.7); fender(-1.06,-1.55,0.7); fender(1.06,-1.55,0.7);
    roofY=2.20; wheels(0.7,0.62,1.06,1.5,-1.55);
  }

  if(isCop){
    add(bx(1.20,0.16,0.50,trim),0,roofY+0.12,-0.05);
    var bl=bx(0.50,0.16,0.44, new THREE.MeshStandardMaterial({color:0x2f6bff,emissive:0x2f6bff,emissiveIntensity:1.4}));
    add(bl,-0.30,roofY+0.12,-0.05);
    var rl=bx(0.50,0.16,0.44, new THREE.MeshStandardMaterial({color:0xff3b4e,emissive:0xff3b4e,emissiveIntensity:1.4}));
    add(rl,0.30,roofY+0.12,-0.05);
    var plB=new THREE.PointLight(0x2f6bff,1.4,16,2); plB.position.set(-0.5,roofY+0.5,0); g.add(plB);
    var plR=new THREE.PointLight(0xff3b4e,1.4,16,2); plR.position.set(0.5,roofY+0.5,0); g.add(plR);
    g.userData.bL=bl; g.userData.rL=rl; g.userData.plB=plB; g.userData.plR=plR;
  }
  return g;
}

function makeCarEntity(opts){
  if(opts.group) opts.group.rotation.order="YXZ";
  return {
    group:opts.group, role:opts.role, isPlayer:!!opts.isPlayer,
    radius:1.0, speed:0, heading:opts.heading||0,
    maxSpeed:opts.maxSpeed, turn:opts.turn||TURN_RATE, bumpKeep:opts.bumpKeep||0.35, boost:100, boosting:false,
    y:0, vy:0, prevSupport:0, pitch:0, airborne:false,
    mass:opts.mass||1, dmg:0, crush:0, leanSign:(Math.random()<0.5?-1:1), debrisCd:0,
    shield:0, oiled:0, spikeCd:0, pitCd:0,
    accelMul:1, gripMul:1, dmgTakenMul:1, boostPow:1, boostEcon:1, boostRegen:1
  };
}

function buildLoot(){
  var lootMat=new THREE.MeshStandardMaterial({color:0xffb020,emissive:0xffb020,emissiveIntensity:0.5,roughness:0.4,metalness:0.4});
  var spots=pickClearSpots(6);
  for(var i=0;i<spots.length;i++){
    var m=new THREE.Mesh(new THREE.BoxGeometry(0.9,0.6,0.6),lootMat.clone());
    m.position.set(spots[i].x,0.6,spots[i].z); m.castShadow=true; scene.add(m);
    loot.push({mesh:m,x:spots[i].x,z:spots[i].z,taken:false,spin:Math.random()*6});
  }
}
// ---- Power-ups & road hazards ----
var PU_COL={repair:0x46e07a, shield:0x52d6ff, emp:0xb06bff, nitro:0xff8a2a};
var PU_TYPES=["repair","shield","emp","nitro"];
function makePowerupMesh(type){
  var col=PU_COL[type], geo;
  if(type==="repair") geo=new THREE.BoxGeometry(0.8,0.8,0.8);
  else if(type==="shield") geo=new THREE.IcosahedronGeometry(0.62,0);
  else if(type==="emp") geo=new THREE.OctahedronGeometry(0.7,0);
  else geo=new THREE.ConeGeometry(0.5,1.0,6);
  return new THREE.Mesh(geo, new THREE.MeshStandardMaterial({color:col,emissive:col,emissiveIntensity:0.75,roughness:0.35,metalness:0.3}));
}
function spawnHazard(x,z,type){
  var g=new THREE.Group(); g.position.set(x,0,z); g.rotation.y=Math.random()*Math.PI;
  if(type==="spike"){
    g.add(new THREE.Mesh(new THREE.BoxGeometry(4.6,0.1,1.1), new THREE.MeshStandardMaterial({color:0x20242c,metalness:0.5,roughness:0.5})));
    for(var i=-2;i<=2;i++){ var sp=new THREE.Mesh(new THREE.ConeGeometry(0.12,0.42,4), new THREE.MeshStandardMaterial({color:0xc8ccd2,metalness:0.7,roughness:0.3})); sp.position.set(i*0.95,0.26,0); g.add(sp); }
  } else {
    var oil=new THREE.Mesh(new THREE.CircleGeometry(2.4,24), new THREE.MeshStandardMaterial({color:0x07090d,roughness:0.15,metalness:0.7,transparent:true,opacity:0.88}));
    oil.rotation.x=-Math.PI/2; oil.position.y=0.04; g.add(oil);
  }
  scene.add(g); hazards.push({group:g,x:x,z:z,type:type,r:2.3});
}
function buildPowerups(){
  powerups=[]; hazards=[];
  var spots=pickClearSpots(7);
  for(var i=0;i<spots.length;i++){
    var type=PU_TYPES[(Math.random()*PU_TYPES.length)|0], m=makePowerupMesh(type);
    m.position.set(spots[i].x,0.85,spots[i].z); scene.add(m);
    powerups.push({mesh:m,x:spots[i].x,z:spots[i].z,type:type,taken:false,spin:Math.random()*6,respawn:0});
  }
  var hs=pickClearSpots(6);
  for(var j=0;j<hs.length;j++) spawnHazard(hs[j].x,hs[j].z,(j%2===0)?"spike":"oil");
}
function applyPowerup(type){
  if(type==="repair"){ player.dmg=0; player.crush=0; applyCrush(player); player.health=100; beep(680,0.1); setTimeout(function(){beep(950,0.12);},90); }
  else if(type==="shield"){ player.shield=6; beep(560,0.1); setTimeout(function(){beep(820,0.12);},90); }
  else if(type==="emp"){ for(var i=0;i<agents.length;i++) agents[i].stun=2.6; spawnShockwave(player.group.position.x,player.group.position.z); flashScreen(); beep(220,0.16,"sawtooth"); }
  else { player.boost=100; player.cbBurst=Math.max(player.cbBurst||0,1.4); beep(520,0.1); setTimeout(function(){beep(760,0.12);},90); }
}
function hitHazards(e){
  for(var h=0;h<hazards.length;h++){
    var H=hazards[h];
    if(Math.hypot(e.group.position.x-H.x, e.group.position.z-H.z)<H.r){
      if(H.type==="spike"){
        if(e.spikeCd<=0){ e.spikeCd=1.2; e.speed*=0.45; sparks(e.group.position.x,0.4,e.group.position.z); if(e.isPlayer) thud(); if(damageOn) damageCar(e,14); }
      } else e.oiled=1.4;
    }
  }
}
// ---- Pursuit drama: police helicopter, roadblocks, spikes, PIT ----
function buildChopper(){
  var g=new THREE.Group();
  var navy=new THREE.MeshStandardMaterial({color:0x16203a,roughness:0.5,metalness:0.5});
  var white=new THREE.MeshStandardMaterial({color:0xdfe6f0,roughness:0.5,metalness:0.3});
  var dark=new THREE.MeshStandardMaterial({color:0x0c1018,roughness:0.6,metalness:0.4});
  function pb(w,h,d,x,y,z,m){ var b=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); b.position.set(x,y,z); b.castShadow=true; g.add(b); return b; }
  pb(2.0,1.3,3.0,0,0,0.2,navy);                 // cabin
  pb(2.02,0.5,1.4,0,0.18,1.0,dark);             // windshield band
  pb(2.04,0.34,3.05,0,-0.55,0.2,white);         // belly stripe
  pb(0.5,0.5,3.4,0,0.2,-2.6,navy);              // tail boom
  pb(0.18,1.3,0.7,0,0.5,-4.1,navy);             // tail fin
  pb(0.12,0.1,2.6,0.8,-1.05,0.2,dark); pb(0.12,0.1,2.6,-0.8,-1.05,0.2,dark);  // skids
  pb(0.12,0.5,0.12,0.8,-0.8,1.0,dark); pb(0.12,0.5,0.12,-0.8,-0.8,1.0,dark);
  pb(0.12,0.5,0.12,0.8,-0.8,-0.6,dark); pb(0.12,0.5,0.12,-0.8,-0.8,-0.6,dark);
  var bc=new THREE.Mesh(new THREE.SphereGeometry(0.18,10,8),new THREE.MeshStandardMaterial({color:0xff3b4e,emissive:0xff3b4e,emissiveIntensity:1.2})); bc.position.set(0,0.78,0.2); g.add(bc);
  var rotor=new THREE.Group();
  rotor.add(new THREE.Mesh(new THREE.CylinderGeometry(0.18,0.18,0.3,8),dark));
  for(var rb=0;rb<2;rb++){ var bl=new THREE.Mesh(new THREE.BoxGeometry(0.34,0.05,7.6),dark); bl.rotation.y=rb*Math.PI/2; rotor.add(bl); }
  rotor.position.set(0,0.95,0.1); g.add(rotor);
  var tail=new THREE.Mesh(new THREE.BoxGeometry(0.08,1.0,0.08),dark); tail.position.set(0.34,0.5,-4.1); g.add(tail);
  g.position.set(theRobber.group.position.x, CHOP_ALT, theRobber.group.position.z-6); g.visible=false; scene.add(g);
  var spot=new THREE.SpotLight(0xfff2cf, 0, 160, Math.atan(POOL_R/CHOP_ALT)*1.08, 0.45, 1.0);
  spot.position.copy(g.position); scene.add(spot);
  var starget=new THREE.Object3D(); scene.add(starget); spot.target=starget;
  var pool=new THREE.Mesh(new THREE.CircleGeometry(POOL_R,28), new THREE.MeshBasicMaterial({color:0xffe9b0,transparent:true,opacity:0,blending:THREE.AdditiveBlending,depthWrite:false}));
  pool.rotation.x=-Math.PI/2; pool.position.y=0.08; scene.add(pool);
  var cone=new THREE.Mesh(new THREE.ConeGeometry(POOL_R,CHOP_ALT,24,1,true), new THREE.MeshBasicMaterial({color:0xffe9b0,transparent:true,opacity:0,blending:THREE.AdditiveBlending,side:THREE.DoubleSide,depthWrite:false}));
  scene.add(cone);
  chopper={group:g,rotor:rotor,tailRotor:tail,beacon:bc,spot:spot,starget:starget,pool:pool,cone:cone,
           tx:theRobber.group.position.x, tz:theRobber.group.position.z, active:false, lit:false, t:0};
}
function updateChopper(dt){
  if(!chopper) return;
  chopper.t+=dt; chopper.rotor.rotation.y+=dt*38; chopper.tailRotor.rotation.x+=dt*46;
  chopper.beacon.material.emissiveIntensity=(Math.floor(chopper.t*4)%2)?1.4:0.2;
  if(!chopper.active){
    if(heat>30){ chopper.active=true; chopper.group.visible=true;
      if(elAlert){ elAlert.textContent="Air support inbound"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1700); }
      beep(740,0.12,"square"); setTimeout(function(){beep(560,0.14,"square");},120);
    } else return;
  }
  var R=theRobber.group.position;
  var gx=chopper.group.position.x, gz=chopper.group.position.z;
  var lx=R.x-Math.sin(theRobber.heading)*4, lz=R.z-Math.cos(theRobber.heading)*4;   // hover slightly behind
  gx+=(lx-gx)*Math.min(1,1.4*dt); gz+=(lz-gz)*Math.min(1,1.4*dt);
  chopper.group.position.set(gx,CHOP_ALT,gz);
  chopper.group.rotation.y+=angleDiff(chopper.group.rotation.y, Math.atan2(R.x-gx,R.z-gz))*Math.min(1,3*dt);
  chopper.spot.position.set(gx,CHOP_ALT,gz);
  chopper.tx+=(R.x-chopper.tx)*Math.min(1,1.7*dt);                                    // beam lags -> dodgeable
  chopper.tz+=(R.z-chopper.tz)*Math.min(1,1.7*dt);
  chopper.starget.position.set(chopper.tx,0,chopper.tz);
  chopper.pool.position.set(chopper.tx,0.08,chopper.tz);
  var dx=gx-chopper.tx, dy=CHOP_ALT-0.08, dz=gz-chopper.tz, len=Math.hypot(dx,dy,dz)||1;
  chopper.cone.position.set((gx+chopper.tx)/2,(CHOP_ALT+0.08)/2,(gz+chopper.tz)/2);
  chopper.cone.quaternion.setFromUnitVectors(_v1.set(0,1,0), _v2.set(dx,dy,dz).multiplyScalar(1/len));
  chopper.cone.scale.set(1,len/CHOP_ALT,1);
  chopper.lit=Math.hypot(R.x-chopper.tx,R.z-chopper.tz)<POOL_R*0.92;
  chopper.spot.intensity+=((chopper.lit?9:5)-chopper.spot.intensity)*Math.min(1,4*dt);
  chopper.pool.material.opacity+=((chopper.lit?0.5:0.32)-chopper.pool.material.opacity)*Math.min(1,5*dt);
  chopper.cone.material.opacity+=((chopper.lit?0.14:0.07)-chopper.cone.material.opacity)*Math.min(1,5*dt);
  chopper.pool.material.color.setHex(chopper.lit?0xfff0c0:0xffe9b0);
  if(chopper.lit) heat=Math.min(100,heat+SPOT_HEAT*dt);
}
function buildPlayerChopper(){
  var g=new THREE.Group();
  var navy=new THREE.MeshStandardMaterial({color:0x16203a,roughness:0.5,metalness:0.5});
  var white=new THREE.MeshStandardMaterial({color:0xdfe6f0,roughness:0.5,metalness:0.3});
  var dark=new THREE.MeshStandardMaterial({color:0x0c1018,roughness:0.6,metalness:0.4});
  function pb(w,h,d,x,y,z,m){ var b=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); b.position.set(x,y,z); b.castShadow=true; g.add(b); return b; }
  pb(2.0,1.3,3.0,0,0,0.2,navy); pb(2.02,0.5,1.4,0,0.18,1.0,dark); pb(2.04,0.34,3.05,0,-0.55,0.2,white);
  pb(0.5,0.5,3.4,0,0.2,-2.6,navy); pb(0.18,1.3,0.7,0,0.5,-4.1,navy);
  pb(0.12,0.1,2.6,0.8,-1.05,0.2,dark); pb(0.12,0.1,2.6,-0.8,-1.05,0.2,dark);
  pb(0.12,0.5,0.12,0.8,-0.8,1.0,dark); pb(0.12,0.5,0.12,-0.8,-0.8,1.0,dark);
  pb(0.12,0.5,0.12,0.8,-0.8,-0.6,dark); pb(0.12,0.5,0.12,-0.8,-0.8,-0.6,dark);
  var bc=new THREE.Mesh(new THREE.SphereGeometry(0.18,10,8),new THREE.MeshStandardMaterial({color:0xff3b4e,emissive:0xff3b4e,emissiveIntensity:1.2})); bc.position.set(0,0.78,0.2); g.add(bc);
  var rotor=new THREE.Group(); rotor.add(new THREE.Mesh(new THREE.CylinderGeometry(0.18,0.18,0.3,8),dark));
  for(var rb=0;rb<2;rb++){ var bl=new THREE.Mesh(new THREE.BoxGeometry(0.34,0.05,7.6),dark); bl.rotation.y=rb*Math.PI/2; rotor.add(bl); }
  rotor.position.set(0,0.95,0.1); g.add(rotor);
  var tail=new THREE.Mesh(new THREE.BoxGeometry(0.08,1.0,0.08),dark); tail.position.set(0.34,0.5,-4.1); g.add(tail);
  var hz=(crossChan>0)?WEDGE-44:-WEDGE+24;
  g.position.set(0,CHOP_ALT,hz); scene.add(g);
  var PR=11;
  var spot=new THREE.SpotLight(0xfff2cf,0,200,Math.atan(PR/CHOP_ALT)*1.1,0.45,1.0); scene.add(spot);
  var starget=new THREE.Object3D(); scene.add(starget); spot.target=starget;
  var pool=new THREE.Mesh(new THREE.CircleGeometry(PR,30), new THREE.MeshBasicMaterial({color:0xffe9b0,transparent:true,opacity:0,blending:THREE.AdditiveBlending,depthWrite:false}));
  pool.rotation.x=-Math.PI/2; pool.position.y=0.08; scene.add(pool);
  var cone=new THREE.Mesh(new THREE.ConeGeometry(PR,CHOP_ALT,26,1,true), new THREE.MeshBasicMaterial({color:0xffe9b0,transparent:true,opacity:0,blending:THREE.AdditiveBlending,side:THREE.DoubleSide,depthWrite:false}));
  scene.add(cone);
  pChop={spot:spot,starget:starget,pool:pool,cone:cone,beacon:bc,r:PR};
  player={group:g,isPlayer:true,isHeli:true,role:"cop",heading:Math.PI,vx:0,vz:0,y:CHOP_ALT,
          speed:0,boost:100,health:100,shield:0,cbBurst:0,lit:false,rotor:rotor,tail:tail};
}
function flyChopper(inp,dt){
  player.heading += inp.steer*1.9*dt;
  var fx=Math.sin(player.heading), fz=Math.cos(player.heading);
  var acc=inp.boost?150:105;
  player.vx += fx*inp.throttle*acc*dt; player.vz += fz*inp.throttle*acc*dt;
  var damp=Math.pow(0.985, dt*60); player.vx*=damp; player.vz*=damp;   // light drag so the cap below is the real top speed
  var maxv=inp.boost?60:40, sp=Math.hypot(player.vx,player.vz);        // ~180 / ~120 mph on the readout
  if(sp>maxv){ player.vx*=maxv/sp; player.vz*=maxv/sp; }
  var g=player.group, px=Math.max(-CITY+6,Math.min(CITY-6,g.position.x+player.vx*dt)), pz=Math.max(-CITY+6,Math.min(CITY-6,g.position.z+player.vz*dt));
  g.position.set(px,CHOP_ALT,pz); player.y=CHOP_ALT; player.speed=sp;
  g.rotation.y=player.heading;
  g.rotation.x = -Math.max(-0.34,Math.min(0.34,(player.vx*fx+player.vz*fz)*0.006));
  g.rotation.z =  Math.max(-0.30,Math.min(0.30,(player.vx*fz-player.vz*fx)*0.006));
  if(player.rotor) player.rotor.rotation.y+=dt*42;
  if(player.tail) player.tail.rotation.x+=dt*50;
  // spotlight straight down
  pChop.spot.position.set(px,CHOP_ALT,pz); pChop.starget.position.set(px,0,pz);
  pChop.pool.position.set(px,0.08,pz);
  pChop.cone.position.set(px,(CHOP_ALT+0.08)/2,pz); pChop.cone.quaternion.identity();
  var R=theRobber.group.position;
  var lit=theRobber.group.visible && Math.hypot(R.x-px,R.z-pz)<pChop.r*0.94; player.lit=lit;
  pChop.spot.intensity+=((lit?12:6)-pChop.spot.intensity)*Math.min(1,5*dt);
  pChop.pool.material.opacity+=((lit?0.55:0.30)-pChop.pool.material.opacity)*Math.min(1,6*dt);
  pChop.cone.material.opacity+=((lit?0.17:0.07)-pChop.cone.material.opacity)*Math.min(1,6*dt);
  pChop.pool.material.color.setHex(lit?0xfff0c0:0xffe9b0);
  pChop.beacon.material.emissiveIntensity=(Math.floor(clock.elapsedTime*4)%2)?1.4:0.2;
  if(lit){ heat=Math.min(100,heat+SPOT_HEAT*dt); pinMeter=Math.min(100,pinMeter+14*dt); }
  else pinMeter=Math.max(0,pinMeter-10*dt);
  if(pinMeter>=100 && !ending){ if(elAlert){ elAlert.textContent="Suspect pinned!"; elAlert.classList.add("show"); } endGame(true,"You pinned the suspect from the air!","busted"); }
}
function makeHuman(){
  var g=new THREE.Group();
  var navy=new THREE.MeshStandardMaterial({color:0x1c2c58,roughness:0.6,metalness:0.1});
  var skin=new THREE.MeshStandardMaterial({color:0xc98d63,roughness:0.7});
  var dark=new THREE.MeshStandardMaterial({color:0x0e1118,roughness:0.7});
  var gold=new THREE.MeshStandardMaterial({color:0xe6c84a,emissive:0x3a2f06,roughness:0.4,metalness:0.6});
  function part(geo,mat,x,y,z){ var m=new THREE.Mesh(geo,mat); m.position.set(x,y,z); m.castShadow=true; g.add(m); return m; }
  part(new THREE.CylinderGeometry(0.15,0.15,0.7,8),dark,-0.16,0.35,0);
  part(new THREE.CylinderGeometry(0.15,0.15,0.7,8),dark, 0.16,0.35,0);
  part(new THREE.CylinderGeometry(0.34,0.28,0.82,12),navy,0,1.06,0);
  part(new THREE.BoxGeometry(0.7,0.12,0.7),gold,0,0.72,0);
  part(new THREE.CylinderGeometry(0.1,0.1,0.62,8),navy,-0.42,1.06,0).rotation.z=0.18;
  part(new THREE.CylinderGeometry(0.1,0.1,0.62,8),navy, 0.42,1.06,0).rotation.z=-0.18;
  part(new THREE.SphereGeometry(0.22,12,10),skin,0,1.62,0);
  part(new THREE.CylinderGeometry(0.25,0.25,0.13,14),dark,0,1.78,0);
  part(new THREE.BoxGeometry(0.5,0.05,0.16),dark,0,1.74,0.2);
  part(new THREE.CircleGeometry(0.05,8),gold,0,1.82,0).rotation.x=-Math.PI/2;
  return g;
}
function buildRailway(cx,cz,rx,rz){
  var tieMat=new THREE.MeshStandardMaterial({color:0x3a2c1c,roughness:1});
  var railMat=new THREE.MeshStandardMaterial({color:0x6a7078,roughness:0.4,metalness:0.85});
  var ballast=new THREE.MeshStandardMaterial({color:0x262a31,roughness:0.95});
  var N=170;
  for(var i=0;i<N;i++){
    var a=i/N*6.2832, na=(i+1)/N*6.2832;
    var x=cx+rx*Math.cos(a), z=cz+rz*Math.sin(a), x2=cx+rx*Math.cos(na), z2=cz+rz*Math.sin(na);
    var mx=(x+x2)/2, mz=(z+z2)/2, ang=Math.atan2(x2-x,z2-z), len=Math.hypot(x2-x,z2-z)+0.4, px=Math.cos(ang), pz=-Math.sin(ang);
    var bl=posBox(6.6,0.12,len+1.4, mx,0.05,mz, ballast); bl.rotation.y=ang; scene.add(bl);
    var tie=posBox(5.2,0.2,1.0, mx,0.12,mz, tieMat); tie.rotation.y=ang; scene.add(tie);
    for(var s=-1;s<=1;s+=2){ var r=posBox(0.18,0.24,len, mx+px*1.6*s,0.28,mz+pz*1.6*s, railMat); r.rotation.y=ang; scene.add(r); }
  }
  var cps=[[cx,cz+rz],[cx,cz-rz],[cx+rx,cz],[cx-rx,cz]], crossings=[];
  for(var c=0;c<cps.length;c++){
    var cxp=cps[c][0], czp=cps[c][1];
    var pad=new THREE.Mesh(new THREE.PlaneGeometry(13,13), new THREE.MeshStandardMaterial({color:0x1d2129,roughness:0.9})); pad.rotation.x=-Math.PI/2; pad.position.set(cxp,0.06,czp); scene.add(pad);
    for(var sx2=-1;sx2<=1;sx2+=2){ var stp=new THREE.Mesh(new THREE.PlaneGeometry(1.0,13),new THREE.MeshBasicMaterial({color:0xeae0b0})); stp.rotation.x=-Math.PI/2; stp.position.set(cxp+sx2*5,0.08,czp); scene.add(stp); }
    var pmat=new THREE.MeshStandardMaterial({color:0x14171d,roughness:0.7});
    var lite=new THREE.Mesh(new THREE.SphereGeometry(0.55,10,8), new THREE.MeshStandardMaterial({color:0xff2a2a,emissive:0xff2a2a,emissiveIntensity:0.2}));
    scene.add(posBox(0.5,6,0.5, cxp+8, 3, czp, pmat)); lite.position.set(cxp+8,6,czp); scene.add(lite);
    scene.add(posBox(2.6,0.3,0.3, cxp+8, 5.4, czp, new THREE.MeshBasicMaterial({color:0xffffff}))); // crossbuck-ish bar
    crossings.push({x:cxp,z:czp,light:lite});
  }
  function railCar(loco){
    var g=new THREE.Group();
    var body=new THREE.MeshStandardMaterial({color:loco?0x16335e:0x394250,roughness:0.45,metalness:0.55});
    var dark=new THREE.MeshStandardMaterial({color:0x10141c,roughness:0.7});
    var yellow=new THREE.MeshStandardMaterial({color:0xf0c020,emissive:0x3a2e06,roughness:0.5});
    var glass=new THREE.MeshStandardMaterial({color:0x223149,roughness:0.2,metalness:0.8,emissive:0x16356e,emissiveIntensity:0.2});
    function bb(w,h,d,x,y,z,m){ var mm=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); mm.position.set(x,y,z); mm.castShadow=true; g.add(mm); }
    bb(4.4,0.6,loco?9.2:8.2,0,0.55,0,dark);
    bb(4.2,loco?3.2:2.7,loco?9:8,0,(loco?2.1:1.85),0,body);
    bb(4.4,0.5,loco?9:8,0,0.5,0,yellow);
    if(loco){ bb(3.4,1.3,2.2,0,3.5,-2.8,glass); bb(4.4,0.4,9.4,0,3.95,0,yellow);
      bb(0.7,0.7,0.7,0,1.6,4.7,new THREE.MeshStandardMaterial({color:0xffe9b0,emissive:0xffd070,emissiveIntensity:1.0})); }  // headlight
    else { for(var w2=-1;w2<=1;w2+=2) bb(0.2,1.0,5,w2*2.0,2.2,0,glass); }
    for(var wz=-1;wz<=1;wz+=2){ bb(0.7,0.7,0.7, 1.7,0.4,wz*2.8,dark); bb(0.7,0.7,0.7,-1.7,0.4,wz*2.8,dark); }
    scene.add(g); return g;
  }
  var cars=[railCar(true)]; for(var w=0;w<4;w++) cars.push(railCar(false));
  train={cx:cx,cz:cz,rx:rx,rz:rz,theta:Math.random()*6.28,speed:0.11,cars:cars,crossings:crossings};
}
function updateTrain(dt){
  if(!train) return; var T=train;
  T.theta+=T.speed*dt; var spacing=0.05;
  for(var i=0;i<T.cars.length;i++){
    var a=T.theta-i*spacing, x=T.cx+T.rx*Math.cos(a), z=T.cz+T.rz*Math.sin(a);
    var a2=a+0.01, x2=T.cx+T.rx*Math.cos(a2), z2=T.cz+T.rz*Math.sin(a2);
    var g=T.cars[i]; g.position.set(x,0,z); g.rotation.y=Math.atan2(x2-x,z2-z);
  }
  var hx=T.cx+T.rx*Math.cos(T.theta), hz=T.cz+T.rz*Math.sin(T.theta);
  for(var c=0;c<T.crossings.length;c++){ var cr=T.crossings[c]; var near=Math.hypot(hx-cr.x,hz-cr.z)<70; cr.light.material.emissiveIntensity=near?(0.25+1.0*(Math.floor(clock.elapsedTime*5)%2)):0.12; }
  function struck(e){ if(!e||!e.group||(e.y||0)>3) return false; var p=e.group.position; for(var i=0;i<T.cars.length;i++){ var cp=T.cars[i].position; if(Math.hypot(p.x-cp.x,p.z-cp.z)<3.6) return true; } return false; }
  if(ending) return;
  if(struck(player) && !onFoot){ spawnShockwave(player.group.position.x,player.group.position.z); thud(); endGame(false,"You were hit by the train!","busted"); return; }
  if(theRobber && theRobber!==player && struck(theRobber)){ spawnShockwave(theRobber.group.position.x,theRobber.group.position.z); thud(); endGame(role==="cop","The suspect was hit by a train!","busted"); }
}
function makeStaticChopper(x,y,z){
  var g=new THREE.Group();
  var navy=new THREE.MeshStandardMaterial({color:0x16203a,roughness:0.5,metalness:0.5});
  var white=new THREE.MeshStandardMaterial({color:0xdfe6f0,roughness:0.5,metalness:0.3});
  var dark=new THREE.MeshStandardMaterial({color:0x0c1018,roughness:0.6,metalness:0.4});
  function b(w,h,d,bx,by,bz,m){ var mm=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); mm.position.set(bx,by,bz); mm.castShadow=true; g.add(mm); }
  b(2.0,1.3,3.0,0,0,0.2,navy); b(2.02,0.5,1.4,0,0.18,1.0,dark); b(2.04,0.34,3.05,0,-0.55,0.2,white);
  b(0.5,0.5,3.4,0,0.2,-2.6,navy); b(0.18,1.3,0.7,0,0.5,-4.1,navy);
  b(0.12,0.1,2.6,0.8,-1.05,0.2,dark); b(0.12,0.1,2.6,-0.8,-1.05,0.2,dark);
  b(0.12,0.55,0.12,0.8,-0.8,1.0,dark); b(0.12,0.55,0.12,-0.8,-0.8,1.0,dark);
  var rotor=new THREE.Group(); for(var rb=0;rb<2;rb++){ var bl=new THREE.Mesh(new THREE.BoxGeometry(0.34,0.05,7.6),dark); bl.rotation.y=rb*Math.PI/2; rotor.add(bl);} rotor.position.set(0,0.95,0.1); g.add(rotor);
  var bc=new THREE.Mesh(new THREE.SphereGeometry(0.16,8,6),new THREE.MeshStandardMaterial({color:0xff3b4e,emissive:0xff3b4e,emissiveIntensity:1.0})); bc.position.set(0,0.78,0.2); g.add(bc);
  g.position.set(x,y,z); scene.add(g); return g;
}
function buildStation(){
  garageCars=[]; selectedGarage=-1; roofHeli=null; heliTaken=false;
  var sx=0, sz=WEDGE-30; stationPos={x:sx,z:sz}; stationR=36;
  var wall=new THREE.MeshStandardMaterial({color:0x29323f,roughness:0.7,metalness:0.2});
  var wall2=new THREE.MeshStandardMaterial({color:0x222a36,roughness:0.8,metalness:0.15});
  var trim=new THREE.MeshStandardMaterial({color:0x2b6fd6,emissive:0x163a8a,emissiveIntensity:0.6,roughness:0.4,metalness:0.3});
  var red=new THREE.MeshStandardMaterial({color:0xd23b3b,emissive:0x6a1414,emissiveIntensity:0.6,roughness:0.5});
  var glass=new THREE.MeshStandardMaterial({color:0x2a3d57,roughness:0.16,metalness:0.9,emissive:0x16356e,emissiveIntensity:0.25});
  var barM=new THREE.MeshStandardMaterial({color:0x9aa3ad,roughness:0.5,metalness:0.7});
  var floorMat=new THREE.MeshStandardMaterial({color:0x2b3340,roughness:0.95});
  var roofH=16, gN=sz-4, gS=sz-22, bN=sz+22, bMid=(bN+gN)/2;
  // ---- main building shell (hollow): office + jail ----
  scene.add(posBox(86,roofH,1.2, 0, roofH/2, bN, wall));                 // back wall
  scene.add(posBox(1.2,roofH,bN-gN, -43, roofH/2, bMid, wall));          // west wall
  scene.add(posBox(1.2,roofH,bN-gN, 43, roofH/2, bMid, wall));           // east wall
  scene.add(posBox(26,roofH,1.2, -29, roofH/2, gN, wall));               // front-left (door gap centre)
  scene.add(posBox(26,roofH,1.2, 29, roofH/2, gN, wall));                // front-right
  scene.add(posBox(86,3,1.2, 0, roofH-1.5, gN, trim));                   // front lintel
  scene.add(posBox(20,7,0.3, -29, 5, gN-0.2, glass)); scene.add(posBox(20,7,0.3, 29, 5, gN-0.2, glass));
  scene.add(posBox(1.0,roofH,bN-gN-2, 0, roofH/2, bMid, wall2));         // office|jail divider
  // ---- roof slab (overhangs east to cover the stairs) + parapet ----
  scene.add(posBox(96,1.0,bN-gN+2, 5, roofH, bMid, new THREE.MeshStandardMaterial({color:0x323b48,roughness:0.9})));
  decks.push({x:5, z:bMid, w:96, d:bN-gN+2, h:roofH});
  scene.add(posBox(96,1.3,1, 5, roofH+1, bN+0.6, trim)); scene.add(posBox(96,1.3,1, 5, roofH+1, gN-0.6, wall));
  scene.add(posBox(1,1.3,bN-gN+2, -43, roofH+1, bMid, wall)); scene.add(posBox(1,1.3,bN-gN+2, 53, roofH+1, bMid, wall));
  scene.add(posBox(34,3.2,1, 0, roofH-3, gN-0.7, trim));                 // POLICE sign over the door
  scene.add(posBox(7,1.6,1.6, -32, roofH+2.2, bMid, red));               // rooftop beacon
  // ---- office furniture (west) ----
  function desk(x,z){ scene.add(posBox(4,0.3,2.2,x,1.5,z,wall2)); scene.add(posBox(3.4,0.9,0.2,x,2.2,z+0.5,glass)); scene.add(posBox(1.1,1.1,1.1,x,0.55,z-1.4,wall2)); }
  desk(-32,sz+4); desk(-18,sz+4); desk(-32,sz+15); desk(-18,sz+15);
  scene.add(posBox(22,2.2,1.4,-28,1.1,gN+4,wall2));
  // ---- jail (east): two barred cells ----
  function cell(x0,x1){ var z0=sz+6, z1=sz+19;
    scene.add(posBox(x1-x0,roofH-2,0.6,(x0+x1)/2,(roofH-2)/2,z1,wall2));
    scene.add(posBox(0.6,roofH-2,z1-z0,x0,(roofH-2)/2,(z0+z1)/2,wall2));
    for(var bxx=x0+0.7; bxx<x1; bxx+=0.9) scene.add(posBox(0.12,roofH-2,0.12,bxx,(roofH-2)/2,z0,barM));
    scene.add(posBox(x1-x0,0.22,0.22,(x0+x1)/2,roofH-2,z0,barM));
    scene.add(posBox(2.6,0.5,1.1,(x0+x1)/2-1,0.5,z1-1.2,wall2));
  }
  cell(8,24); cell(27,41);
  // ---- external stairs (east side) up to the roof ----
  makeRamp(48, bMid, 0, bN-gN, 7, roofH);                                // hidden ramp gives the climb height
  var steps=12; for(var st2=0; st2<steps; st2++){ var sf=(st2+0.5)/steps; scene.add(posBox(7,0.7,(bN-gN)/steps+0.2, 48, roofH*sf, gN+(bN-gN)*sf, wall2)); }
  scene.add(posBox(8,3.4,7, 48, roofH+1.7, bN-5, wall2));                // rooftop stair hut
  // ---- helipad + the chopper ----
  var pad=new THREE.Mesh(new THREE.CircleGeometry(8,28),new THREE.MeshStandardMaterial({color:0x1a1f27,roughness:0.9})); pad.rotation.x=-Math.PI/2; pad.position.set(0,roofH+0.55,bMid); scene.add(pad);
  var ring=new THREE.Mesh(new THREE.RingGeometry(7,7.7,28),new THREE.MeshBasicMaterial({color:0xf0d24a,side:THREE.DoubleSide})); ring.rotation.x=-Math.PI/2; ring.position.set(0,roofH+0.57,bMid); scene.add(ring);
  function hMark(w,d,x){ var m=new THREE.Mesh(new THREE.PlaneGeometry(w,d),new THREE.MeshBasicMaterial({color:0xf0d24a})); m.rotation.x=-Math.PI/2; m.position.set(x,roofH+0.58,bMid); scene.add(m); }
  hMark(1,6,-2); hMark(1,6,2); hMark(4,1,0);
  roofHeli={ group:makeStaticChopper(0,roofH+1.1,bMid), x:0, z:bMid, y:roofH };
  // ---- parking garage (front), covered, your cars inside ----
  scene.add(posBox(92,1.0,22, 0, 8, sz-13, wall)); scene.add(posBox(94,0.7,23, 0, 8.6, sz-13, trim));
  for(var gp=-42; gp<=42; gp+=21) scene.add(posBox(1.6,8,1.6, gp, 4, gS+1, wall));   // front pillars
  scene.add(posBox(1.2,8,20,-46,4,sz-13,wall)); scene.add(posBox(1.2,8,20,46,4,sz-13,wall));
  var bayf=new THREE.Mesh(new THREE.PlaneGeometry(90,20),floorMat); bayf.rotation.x=-Math.PI/2; bayf.position.set(0,0.05,sz-13); bayf.receiveShadow=true; scene.add(bayf);
  for(var pl=-36; pl<=36; pl+=12){ var pln=new THREE.Mesh(new THREE.PlaneGeometry(0.3,16),new THREE.MeshBasicMaterial({color:0xeae0b0})); pln.rotation.x=-Math.PI/2; pln.position.set(pl,0.07,sz-13); scene.add(pln); }
  aabbs.push({minX:-44,maxX:54,minZ:gN,maxZ:bN+1});                      // building (incl. stair side) is solid to cars
  var HEAVY=["truck","tank","bus","monster","semi","swat"];
  var ownedAll=["supercar"]; for(var s in PRICES.style){ if(prog.unlocks["style:"+s]) ownedAll.push(s); }
  var owned=ownedAll.filter(function(s){ return HEAVY.indexOf(s)<0; });   // standard bay — no cap, every owned car shows
  var heavyOwned=ownedAll.filter(function(s){ return HEAVY.indexOf(s)>=0; }); // heavy bay — big trucks live here
  var n=owned.length, span=Math.min(84,Math.max(22,n*11));
  for(var i=0;i<n;i++){ var stl=owned[i], cfg={}; for(var k in COP_CONFIG) cfg[k]=COP_CONFIG[k]; cfg.style=stl;
    var cg=makeCar(true,cfg); var cxp=-span/2+span*(i+0.5)/Math.max(1,n); cg.position.set(cxp,0,sz-13); cg.rotation.y=Math.PI; scene.add(cg);
    garageCars.push({style:stl, group:cg, x:cxp, z:sz-13}); }
  // ---- second, bigger garage for heavy vehicles (semi, bus, tank, monster, swat, truck) ----
  if(heavyOwned.length){
    var hz=sz-13, hx=-95;                                                     // west annex, well clear of the main building/garage
    var hn=heavyOwned.length, hspan=Math.max(30,hn*22);
    scene.add(posBox(hspan+14,1.0,26,hx,10,hz,wall)); scene.add(posBox(hspan+16,0.7,27,hx,10.6,hz,trim));   // taller/wider roof for trucks
    for(var hp=-hspan/2-4; hp<=hspan/2+4; hp+=Math.max(18,hspan/(hn||1))) scene.add(posBox(1.8,10,1.8,hx+hp,5,hz-12,wall));
    scene.add(posBox(1.4,10,24,hx-hspan/2-7,5,hz,wall)); scene.add(posBox(1.4,10,24,hx+hspan/2+7,5,hz,wall));
    var hFloor=new THREE.Mesh(new THREE.PlaneGeometry(hspan+12,24),floorMat); hFloor.rotation.x=-Math.PI/2; hFloor.position.set(hx,0.05,hz); hFloor.receiveShadow=true; scene.add(hFloor);
    scene.add(posBox(hspan+16,3.6,1,hx,roofH-4.5,hz-13,trim));                // "HEAVY VEHICLES" header wall
    aabbs.push({minX:hx-hspan/2-8,maxX:hx+hspan/2+8,minZ:hz-13,maxZ:hz+11});
    for(var j=0;j<hn;j++){ var hstl=heavyOwned[j], hcfg={}; for(var hk in COP_CONFIG) hcfg[hk]=COP_CONFIG[hk]; hcfg.style=hstl;
      var hg=makeCar(true,hcfg); var hxp=hx-hspan/2+hspan*(j+0.5)/hn; hg.position.set(hxp,0,hz); hg.rotation.y=Math.PI; scene.add(hg);
      garageCars.push({style:hstl, group:hg, x:hxp, z:hz}); }
  }
}
function enterRoofHeli(){
  if(roofHeli) roofHeli.group.visible=false;
  if(human) human.visible=false;
  onFoot=false; leftStation=false; chaseStarted=true; heliTaken=true;
  buildPlayerChopper();
  player.group.position.set(0,CHOP_ALT,stationPos.z+9);
  camTargetPitch=0.72; camPitch=0.72; camTargetDist=28; camDist=28;
  elObjective.textContent="Spotlight the suspect to pin them from the air";
  if(elAlert){ elAlert.textContent="Air unit airborne!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1700); }
  beep(500,0.12,"square");
}
function spawnHuman(){
  if(!human){ human=makeHuman(); scene.add(human); }
  human.visible=true;
  human.position.set(stationPos.x, 0, stationPos.z-26); human.rotation.y=0;
  player={group:human, isPlayer:true, isHuman:true, role:"cop", heading:0, speed:0, y:0, boost:100, shield:0, cbBurst:0, health:100};
  onFoot=true;
  if(engineGain) engineGain.gain.value=0; if(skidGain) skidGain.gain.value=0; if(sirenGain) sirenGain.gain.value=0;
  camTargetPitch=0.5; camPitch=0.5; camTargetDist=11; camDist=11;
}
function walkPlayer(inp,dt){
  var spd=inp.boost?13:7, g=player.group;
  player.heading += inp.steer*2.6*dt;
  var fx=Math.sin(player.heading), fz=Math.cos(player.heading), mv=inp.throttle*spd*dt;
  var nx=Math.max(-CITY+4,Math.min(CITY-4,g.position.x+fx*mv)), nz=Math.max(-CITY+4,Math.min(CITY-4,g.position.z+fz*mv));
  var rs=rampHeightAt(nx,nz); if(rs>player.y+1.4) rs=0;          // climb stairs only by a reachable step
  var support=Math.max(rs, deckHeightAt(player), 0); player.y=support;
  var bob=(Math.abs(inp.throttle)>0.1?Math.abs(Math.sin(clock.elapsedTime*10))*0.07:0);
  g.position.set(nx, support+bob, nz); g.rotation.y=player.heading; player.speed=Math.abs(inp.throttle)*spd;
  if(roofHeli && roofHeli.group.visible && player.y>10 && Math.hypot(nx-roofHeli.x,nz-roofHeli.z)<4.5){ enterRoofHeli(); return; }
  var near=-1, nd=999;
  for(var i=0;i<garageCars.length;i++){ if(!garageCars[i].group.visible) continue; var d=Math.hypot(nx-garageCars[i].x,nz-garageCars[i].z); if(d<nd){nd=d;near=i;} }
  nearGarageIdx=(near>=0 && nd<10)?near:-1;
  if(near>=0 && nd<3.0){ enterVehicle(near); return; }
  if(player.y>10) elObjective.textContent="Walk into the helicopter to take off";
  else if(near>=0 && nd<10){ var lb=(STYLES[garageCars[near].style]||{}).label||garageCars[near].style; elObjective.textContent="Walk into the "+lb+" to drive it"; }
  else elObjective.textContent="Pick a vehicle — or take the east stairs up to the roof chopper";
}
function enterVehicle(idx){
  var gc=garageCars[idx]; selectedGarage=idx; gc.group.visible=false;
  if(human) human.visible=false; onFoot=false; leftStation=false; chaseStarted=true;
  var stl=gc.style, st=STYLES[stl]||STYLES.supercar, cfg={}; for(var k in COP_CONFIG) cfg[k]=COP_CONFIG[k]; cfg.style=stl;
  var pg=makeCar(true,cfg); pg.position.set(stationPos.x,0,stationPos.z-26); pg.rotation.y=Math.PI; scene.add(pg);
  player=makeCarEntity({group:pg, role:"cop", isPlayer:true, heading:Math.PI, maxSpeed:P_MAXSPEED*st.speed, turn:TURN_RATE*st.turn, bumpKeep:st.bump, mass:st.mass});
  applyUpgrades(player);
  camTargetPitch=0.62; camPitch=0.62; camTargetDist=15; camDist=15;
  elObjective.textContent="Catch the robber — return to the station to swap vehicles";
  if(elAlert){ elAlert.textContent="Driving the "+(st.label||stl)+"!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1700); }
  beep(440,0.12,"square");
}
function exitToStation(){
  if(selectedGarage>=0 && garageCars[selectedGarage]) garageCars[selectedGarage].group.visible=true;
  if(heliTaken){ if(roofHeli) roofHeli.group.visible=true;
    if(pChop){ [pChop.spot,pChop.starget,pChop.pool,pChop.cone].forEach(function(o){ if(o){ disposeDeep(o); scene.remove(o); } }); }
    pChop=null; heliTaken=false; }
  if(player&&player.group && player.group!==human){ disposeDeep(player.group); scene.remove(player.group); }
  spawnHuman();
  if(elAlert){ elAlert.textContent="Back at the station — pick a vehicle"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1700); }
  beep(300,0.12,"sine");
}

function spawnRoadblock(){
  if(roadblocks.length>=3) return;
  var R=theRobber.group.position, h=theRobber.heading, fx=Math.sin(h), fz=Math.cos(h), rx=fz, rz=-fx;
  var depth=rand(26,40), cxp=R.x+fx*depth, czp=R.z+fz*depth;
  if(Math.abs(cxp)>CITY-6||Math.abs(czp)>CITY-6){ cxp=R.x+fx*20; czp=R.z+fz*20; }
  if(Math.abs(cxp)>CITY-6||Math.abs(czp)>CITY-6) return;
  var bars=[], n=4, span=4.0, ang=Math.atan2(rx,rz);
  for(var i=0;i<n;i++){
    var off=(i-(n-1)/2)*span, bx2=cxp+rx*off, bz2=czp+rz*off;
    var grp=new THREE.Group(); grp.position.set(bx2,0,bz2); grp.rotation.y=ang;
    var body=new THREE.Mesh(new THREE.BoxGeometry(3.4,1.0,0.9), new THREE.MeshStandardMaterial({color:0xe23030,roughness:0.6})); body.position.y=0.5; body.castShadow=true; grp.add(body);
    grp.add(new THREE.Mesh(new THREE.BoxGeometry(3.42,0.32,0.92), new THREE.MeshStandardMaterial({color:0xf4f4f4,roughness:0.6})));
    var lt=new THREE.Mesh(new THREE.SphereGeometry(0.16,8,6), new THREE.MeshStandardMaterial({color:0x2f6bff,emissive:0x2f6bff,emissiveIntensity:1.2})); lt.position.y=1.1; grp.add(lt);
    grp.children[1].position.y=0.5; scene.add(grp);
    var hw=1.9, hd=0.8, aabb={minX:bx2-hw,maxX:bx2+hw,minZ:bz2-hd,maxZ:bz2+hd}; aabbs.push(aabb);
    bars.push({grp:grp, light:lt, aabb:aabb, x:bx2, z:bz2, broken:false});
  }
  roadblocks.push({bars:bars, life:18, t:0});
  if(elAlert){ elAlert.textContent="Roadblock ahead!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1600); }
  beep(300,0.12,"square");
}
function breakBar(bar){ if(bar.broken) return; bar.broken=true; bar.grp.visible=false; var i=aabbs.indexOf(bar.aabb); if(i>=0) aabbs.splice(i,1); }
function updateRoadblocks(dt){
  var cars=[player].concat(agents);
  for(var i=roadblocks.length-1;i>=0;i--){
    var rb=roadblocks[i]; rb.life-=dt; rb.t+=dt; var flash=(Math.floor(rb.t*4)%2)===0;
    for(var b=0;b<rb.bars.length;b++){
      var bar=rb.bars[b]; if(bar.broken) continue;
      bar.light.material.emissiveIntensity=flash?1.4:0.3;
      for(var ci=0;ci<cars.length;ci++){ var e=cars[ci]; if(!e||!e.group.visible) continue;
        if(Math.hypot(e.group.position.x-bar.x,e.group.position.z-bar.z)<2.6 && Math.abs(e.speed)>9){
          breakBar(bar); explode(bar.x,0.6,bar.z); if(e.isPlayer) thud(); e.speed*=0.7; if(damageOn) damageCar(e,7); break;
        }
      }
    }
    if(rb.life<=0){ for(var b2=0;b2<rb.bars.length;b2++) breakBar(rb.bars[b2]); roadblocks.splice(i,1); }
  }
}
function deployCopSpikes(){
  var R=theRobber.group.position, h=theRobber.heading, d=rand(15,26);
  var x=R.x+Math.sin(h)*d, z=R.z+Math.cos(h)*d;
  if(Math.abs(x)>CITY-6||Math.abs(z)>CITY-6) return;
  spawnHazard(x,z,"spike"); var H=hazards[hazards.length-1]; H.cop=true; H.life=10;
  if(elAlert){ elAlert.textContent="Spikes deployed!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1400); }
  beep(340,0.1,"square");
}
function copPIT(dt){
  var R=theRobber; if(!R||!R.group.visible) return;
  if(R.pitCd>0){ R.pitCd-=dt; return; }
  if(Math.abs(R.speed)<6) return;
  var rp=R.group.position, fx=Math.sin(R.heading), fz=Math.cos(R.heading), gx=fz, gz=-fx;
  for(var i=0;i<agents.length;i++){
    var c=agents[i]; if(c.role!=="cop") continue;
    var cp=c.group.position, relx=cp.x-rp.x, relz=cp.z-rp.z, d=Math.hypot(relx,relz);
    if(d>4.2||Math.abs(c.speed)<7) continue;
    var fwd=relx*fx+relz*fz, side=relx*gx+relz*gz;
    if(fwd<-2.6||fwd>1.2) continue;                       // cop alongside / at the rear quarter
    if(Math.abs(side)<0.7||Math.abs(side)>3.2) continue;
    var sgn=side>0?1:-1;
    R.heading+=sgn*PIT_KICK; R.speed*=0.4;                // rear steps out -> spin
    rp.x+=gx*sgn*0.6; rp.z+=gz*sgn*0.6; c.speed*=0.6;
    sparks(rp.x,0.6,rp.z); thud(); spawnShockwave(rp.x,rp.z); if(damageOn) damageCar(R,6);
    R.pitCd=2.8;
    if(role==="robber"){ flashScreen(); if(elAlert){ elAlert.textContent="PIT maneuver!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1300); } }
    beep(180,0.16,"sawtooth"); break;
  }
}
// ---- Robber crew: an AI ally that blocks the cops ----
function buildCrew(){
  var st=STYLES["suv"];
  var cfg={ color:0x22d3a0, roof:0x0a0e16, wheel:0x14171d, finish:"gloss", style:"suv" };
  var g=makeCar(false,cfg); g.position.set(5,0,CITY-13); g.rotation.y=Math.PI; scene.add(g);
  crew=makeCarEntity({group:g,role:"crew",heading:Math.PI,maxSpeed:P_MAXSPEED*st.speed*0.96,turn:TURN_RATE*st.turn,bumpKeep:st.bump,mass:st.mass+0.5});
}
function driveCrewAI(c,dt){
  var cp=c.group.position, R=theRobber.group.position;
  var target=null, best=1e9;
  for(var i=0;i<agents.length;i++){ var a=agents[i]; if(a.role!=="cop"||a.stun>0||!a.group.visible) continue;
    var d=Math.hypot(a.group.position.x-R.x,a.group.position.z-R.z); if(d<best){ best=d; target=a; } }
  var gx,gz, ram=false;
  if(target && best<75){
    var tp=target.group.position, dc=Math.hypot(tp.x-cp.x,tp.z-cp.z);
    if(dc<13){ gx=tp.x; gz=tp.z; ram=true; }          // close enough — body-block / shove the cop
    else { gx=tp.x*0.62+R.x*0.38; gz=tp.z*0.62+R.z*0.38; }   // slot between cop and robber
  } else {
    var fx=Math.sin(theRobber.heading), fz=Math.cos(theRobber.heading);
    gx=R.x - fx*6 + fz*4.5; gz=R.z - fz*6 - fx*4.5;    // pace alongside the robber
  }
  var desired=Math.atan2(gx-cp.x, gz-cp.z), sp=Math.abs(c.speed);
  if(acrossWater(c,gz)){ var ba=bridgeAim(c,gz); desired=Math.atan2(ba.x-cp.x, ba.z-cp.z); }
  var sr=steerAround(cp,c.heading,desired,7+sp*0.4);
  var dh=angleDiff(c.heading,sr.h), steer=Math.max(-1,Math.min(1,dh*2.4));
  var imminent=pathBlocked(cp,c.heading,2,4+sp*0.2,2.3), sharp=Math.abs(dh)>0.55;
  var throttle=(imminent&&sharp)?0.3:((sr.blocked||sharp)?0.85:1.0);
  var boost=ram && !sr.blocked && Math.abs(dh)<0.5;
  driveCar(c, throttle, steer, imminent&&sharp, boost, dt);
}
// ---- Heist mode: the cash truck ----
function buildCashTruck(){
  var g=new THREE.Group();
  var body=new THREE.MeshStandardMaterial({color:0x1f6b3a,roughness:0.6,metalness:0.4});
  var dark=new THREE.MeshStandardMaterial({color:0x0c1018,roughness:0.6,metalness:0.4});
  var gold=new THREE.MeshStandardMaterial({color:0xffd24a,emissive:0x4a3a06,emissiveIntensity:0.4,roughness:0.4,metalness:0.6});
  function pb(w,h,d,x,y,z,m){ var b=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m); b.position.set(x,y,z); b.castShadow=true; b.receiveShadow=true; g.add(b); return b; }
  pb(2.6,1.95,4.2,0,1.3,-0.4,body);                       // armored vault box
  pb(2.66,0.34,4.22,0,0.85,-0.4,gold);                    // gold stripe
  pb(1.2,1.2,0.1,0,1.45,-2.55,gold);                      // rear money panel
  pb(2.4,1.15,1.6,0,1.0,2.0,dark);                        // cab
  pb(2.44,0.5,1.0,0,1.42,2.45,new THREE.MeshPhysicalMaterial({color:0x070b12,roughness:0.05,metalness:0.1,clearcoat:1}));
  pb(2.7,0.28,4.3,0,2.32,-0.4,dark);                      // roof rim
  pb(0.18,0.6,0.18,0.95,2.55,-0.4,gold); pb(0.18,0.6,0.18,-0.95,2.55,-0.4,gold);   // beacons
  for(var s=-1;s<=1;s+=2){ for(var zz=0; zz<3; zz++){ var W=makeWheel(0.6,0.5,0x14171d); W.position.set(s*1.28,0.6,1.4-zz*1.7); g.add(W); } }
  var start=(map==="custom"&&!activeCustomBase)?((role==="robber")?{x:customSpawnP.x,z:customSpawnP.z}:{x:customSpawnR.x,z:customSpawnR.z}):((role==="robber")?{x:0,z:WEDGE-16}:{x:0,z:-WEDGE+20});
  var spots=pickClearSpots(30), pos={x:0,z:0};
  for(var i=0;i<spots.length;i++){ if(Math.hypot(spots[i].x-start.x,spots[i].z-start.z)>50){ pos=spots[i]; break; } }
  g.position.set(pos.x,0,pos.z); g.rotation.y=Math.random()*6.28; scene.add(g);
  cashTruck=makeCarEntity({group:g,role:"cash",maxSpeed:P_MAXSPEED*0.6,turn:TURN_RATE*0.7,bumpKeep:0.5,heading:g.rotation.y,mass:2.6});
  cashTruck.tx=pos.x; cashTruck.tz=pos.z; cashTruck.retime=2;
}
function updateCashTruck(dt){
  if(!cashTruck) return;
  if(hijacked){ driveCar(cashTruck,0,0,true,false,dt); return; }
  var cp=cashTruck.group.position, R=theRobber.group.position, dR=Math.hypot(R.x-cp.x,R.z-cp.z), dx,dz;
  cashTruck.retime-=dt;
  if(dR<26){ dx=cp.x-R.x; dz=cp.z-R.z; }                 // flee whoever is hijacking
  else { if(Math.hypot(cashTruck.tx-cp.x,cashTruck.tz-cp.z)<6 || cashTruck.retime<=0){ cashTruck.tx=rand(-CITY+10,CITY-10); cashTruck.tz=rand(-CITY+10,CITY-10); cashTruck.retime=rand(5,9); } dx=cashTruck.tx-cp.x; dz=cashTruck.tz-cp.z; }
  var sp=Math.abs(cashTruck.speed), sr=steerAround(cp,cashTruck.heading,Math.atan2(dx,dz),6+sp*0.4);
  var dh=angleDiff(cashTruck.heading,sr.h), steer=Math.max(-1,Math.min(1,dh*2.2));
  driveCar(cashTruck, Math.abs(dh)>0.6?0.6:1.0, steer, false, false, dt);
}
function doHijack(){
  hijacked=true; var cp=cashTruck.group.position;
  explode(cp.x,1.0,cp.z); spawnShockwave(cp.x,cp.z); flashScreen();
  if(role==="robber") score+=1200;
  heat=Math.min(100,heat+28);
  for(var o=0;o<objectives.length;o++) if(objectives[o].safe) objectives[o].marker.visible=true;
  updateObjectiveMarkers();
  var bt=document.getElementById("bigtext"); if(bt){ bt.textContent="CASH SECURED"; bt.className="bigtext show win"; setTimeout(function(){ bt.className="bigtext"; },1400); }
  if(elAlert){ elAlert.textContent=(role==="robber")?"Cash secured — reach the safehouse!":"Robber grabbed the cash!"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1900); }
  beep(720,0.1); setTimeout(function(){beep(960,0.1);},90); setTimeout(function(){beep(1240,0.12);},190);
}
// ---- Objectives (getaway waypoints + safehouse) ----
function makeObjectiveMarker(isSafe){
  var col=isSafe?0x46e07a:0xffb020, g=new THREE.Group();
  var beam=new THREE.Mesh(new THREE.CylinderGeometry(isSafe?2.6:1.5, isSafe?2.6:1.5, 16, 22, 1, true),
    new THREE.MeshBasicMaterial({color:col,transparent:true,opacity:0.16,side:THREE.DoubleSide}));
  beam.position.y=8; g.add(beam);
  var ring=new THREE.Mesh(new THREE.TorusGeometry(isSafe?2.8:1.7,0.2,8,30),
    new THREE.MeshStandardMaterial({color:col,emissive:col,emissiveIntensity:0.85,roughness:0.4}));
  ring.rotation.x=Math.PI/2; ring.position.y=0.5; g.add(ring);
  if(isSafe){ var base=new THREE.Mesh(new THREE.BoxGeometry(5,3,5),
    new THREE.MeshStandardMaterial({color:0x16202a,emissive:0x123a1f,emissiveIntensity:0.35,roughness:0.6}));
    base.position.y=1.5; g.add(base); }
  g.userData.ring=ring; return g;
}
function buildObjectives(){
  objectives=[]; objIndex=0;
  var start=(map==="custom"&&!activeCustomBase)?((role==="robber")?{x:customSpawnP.x,z:customSpawnP.z}:{x:customSpawnR.x,z:customSpawnR.z}):((role==="robber")?{x:0,z:WEDGE-16}:{x:0,z:-WEDGE+20});
  if(mode==="heist"){
    var hpool=pickClearSpots(40), hs=null;
    for(var hi=0;hi<hpool.length;hi++){ if(Math.hypot(hpool[hi].x-start.x,hpool[hi].z-start.z)>45){ hs=hpool[hi]; break; } }
    if(!hs) hs=hpool[0]||{x:0,z:-start.z};
    var hmk=makeObjectiveMarker(true); hmk.position.set(hs.x,0,hs.z); hmk.visible=false; scene.add(hmk);
    objectives.push({x:hs.x,z:hs.z,marker:hmk,safe:true,reached:false});
    return;
  }
  var pool;
  if(map==="world"){
    var perIsland=Math.ceil((mode==="heistrun"?6:11)/3)+2;
    var mainSpots=pickSpotsInRect(-1750,1750,-15,810,perIsland);
    var secondSpots=pickSpotsInRect(-800,800,-810,-330,perIsland);
    var thirdSpots=pickSpotsInRect(-800,800,1035,CITY-15,perIsland);
    pool=[]; var maxLen=Math.max(mainSpots.length,secondSpots.length,thirdSpots.length);
    for(var zi=0;zi<maxLen;zi++){ if(mainSpots[zi]) pool.push(mainSpots[zi]); if(secondSpots[zi]) pool.push(secondSpots[zi]); if(thirdSpots[zi]) pool.push(thirdSpots[zi]); }   // interleaved so the greedy pick below can't exhaust all slots on one island
  } else pool=pickClearSpots(50);
  var picked=[], wantStops=(mode==="heistrun")?6:(map==="world"?11:4);
  for(var i=0;i<pool.length && picked.length<wantStops;i++){
    var s=pool[i]; if(Math.hypot(s.x-start.x,s.z-start.z)<30) continue;
    var ok=true; for(var j=0;j<picked.length;j++) if(Math.hypot(s.x-picked[j].x,s.z-picked[j].z)<26){ok=false;break;}
    if(ok) picked.push(s);
  }
  while(picked.length<wantStops && pool.length) picked.push(pool[picked.length%pool.length]);
  picked.sort(function(a,b){ return Math.hypot(a.x-start.x,a.z-start.z)-Math.hypot(b.x-start.x,b.z-start.z); });
  for(var k=0;k<picked.length;k++){
    var isSafe=(k===picked.length-1), mk=makeObjectiveMarker(isSafe);
    mk.position.set(picked[k].x,0,picked[k].z); scene.add(mk);
    objectives.push({x:picked[k].x,z:picked[k].z,marker:mk,safe:isSafe,reached:false});
  }
  updateObjectiveMarkers();
}
function updateObjectiveMarkers(){ for(var i=0;i<objectives.length;i++) objectives[i].marker.visible=(i===objIndex); }
function copAgentCount(){ var n=0; for(var i=0;i<agents.length;i++) if(agents[i].role==="cop") n++; return n; }
function spawnBackupCop(){
  if(!spawnCfg || copAgentCount()>=spawnCfg.maxCops) return false;
  var rp=theRobber.group.position;
  var ex=(rp.x>0?-1:1)*(CITY-8), ez=rand(-CITY+8,CITY-8);
  var g=makeCar(true, spawnCfg.cfg); var hd=Math.atan2(rp.x-ex,rp.z-ez);
  g.position.set(ex,0,ez); g.rotation.y=hd; scene.add(g);
  agents.push(makeCarEntity({group:g,role:"cop",maxSpeed:spawnCfg.max,turn:spawnCfg.turn,bumpKeep:spawnCfg.bump,heading:hd,mass:spawnCfg.mass}));
  flashScreen(); beep(360,0.1,"square"); setTimeout(function(){beep(300,0.12,"square");},90);
  if(elAlert){ elAlert.textContent="Backup incoming"; elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1600); }
  return true;
}
function pickSpotsInRect(x0,x1,z0,z1,n){
  var out=[], tries=0;
  while(out.length<n && tries<n*40){
    tries++; var x=rand(x0,x1), z=rand(z0,z1);
    if(crossChan>0 && Math.abs(z-crossZc)<crossChan+5) continue;
    if(map==="world" && z>815 && z<1025) continue;
    if(circleHitsAABB(x,z,2.0)) continue;
    out.push({x:x,z:z});
  }
  return out;
}
function pickClearSpots(n){
  var out=[],tries=0;
  while(out.length<n && tries<400){
    tries++; var bx=boundHalfX||CITY, bz=boundHalfZ||CITY; var x=rand(-bx+6,bx-6), z=rand(-bz+6,bz-6);
    if(crossChan>0 && Math.abs(z-crossZc)<crossChan+5) continue;   // no spawning in the bay
    if(map==="world" && z>815 && z<1025) continue;                // no spawning in the third island's channel
    if(circleHitsAABB(x,z,2.0)) continue;
    out.push({x:x,z:z});
  }
  return out;
}
// ---- Civilian traffic ----
function buildTraffic(n){
  traffic=[];
  var styleKeys=["supercar","offroad","hypercar","truck","muscle","interceptor","van","buggy","suv","sedan","moto","hothatch","limo","bus","garbage"];
  var forestStyles=["offroad","monster","van","suv","buggy","truck","garbage"];
  var onRoad=((map==="world"||map==="custom") && roadNodes.length>0), made=0, spots=onRoad?[]:pickClearSpots(n*3);
  for(var i=0; (onRoad? (made<n && i<n*60) : i<spots.length) && made<n; i++){
    var px,pz,hd,fromN,toN,forestEdge=false;
    if(onRoad){
      fromN=(Math.random()*roadNodes.length)|0; var nb=roadAdj[fromN]; if(!nb.length) continue;
      toN=nb[(Math.random()*nb.length)|0];
      if(crossChan>0 && (Math.abs(roadNodes[fromN].z-crossZc)<crossChan+6 || Math.abs(roadNodes[toN].z-crossZc)<crossChan+6)) continue;   // don't spawn out on the bridge/water (they still route across it)
      forestEdge=roadNodes[fromN].forest||roadNodes[toN].forest;
      if(forestEdge && Math.random()>0.25) continue;                                            // forest traffic is much rarer
      var A=roadNodes[fromN], B=roadNodes[toN], ux=B.x-A.x, uz=B.z-A.z, L=Math.hypot(ux,uz)||1; ux/=L; uz/=L;
      var t=rand(0.2,0.8); px=A.x+ux*L*t+uz*2.6; pz=A.z+uz*L*t-ux*2.6; hd=Math.atan2(ux,uz);
      if(Math.hypot(px,pz-(WEDGE-16))<24) continue;
    } else {
      var s=spots[i]; if(Math.hypot(s.x,s.z-(WEDGE-16))<24 || Math.hypot(s.x,s.z-(-WEDGE+20))<24) continue;
      px=s.x; pz=s.z; hd=Math.random()*Math.PI*2;
    }
    var pool=forestEdge?forestStyles:styleKeys;
    var style=pool[(Math.random()*pool.length)|0], st=STYLES[style];
    var cfg={ color:CIV_PAINTS[(Math.random()*CIV_PAINTS.length)|0], roof:0x0a0e16, wheel:0x14171d, finish:(Math.random()<0.5?"gloss":"matte"), style:style };
    var g=makeCar(false,cfg); g.position.set(px,0,pz); g.rotation.y=hd; g.traverse(function(o){ if(o.isMesh) o.castShadow=false; }); scene.add(g);
    var e=makeCarEntity({group:g,role:"civ",maxSpeed:P_MAXSPEED*st.speed*0.5,turn:TURN_RATE*st.turn,bumpKeep:st.bump,heading:hd,mass:st.mass});
    e.offroad=(forestStyles.indexOf(style)>=0);                                                 // only these may enter the forest tracks
    if(onRoad){ e.onRoad=true; e.fromN=fromN; e.toN=toN; }
    else { e.tx=rand(-CITY+8,CITY-8); e.tz=rand(-CITY+8,CITY-8); e.retime=rand(4,9); }
    traffic.push(e); made++;
  }
}
function driveCivRoad(c,dt){
  var A=roadNodes[c.fromN], B=roadNodes[c.toN], cp=c.group.position;
  var ux=B.x-A.x, uz=B.z-A.z, segLen=Math.hypot(ux,uz)||1; ux/=segLen; uz/=segLen;
  var rx=uz, rz=-ux;                                   // right-hand side (North-American rule)
  var t=(cp.x-A.x)*ux+(cp.z-A.z)*uz;
  if(t>segLen-5){                                      // reached the next junction — pick a new road
    var allnb=roadAdj[c.toN], nb=[];
    for(var ni=0;ni<allnb.length;ni++){ if(c.offroad || !roadNodes[allnb[ni]].forest) nb.push(allnb[ni]); }   // only trucks/off-roaders may take forest tracks
    if(!nb.length) nb=allnb;
    if(roadNodes[c.toN].forest){ var fnb=[]; for(var fi=0;fi<nb.length;fi++) if(roadNodes[nb[fi]].forest) fnb.push(nb[fi]); if(fnb.length && Math.random()<0.85) nb=fnb; }  // tend to keep weaving through the woods
    var prev=c.fromN, next=nb[(Math.random()*nb.length)|0], tries=0;
    while(nb.length>1 && next===prev && tries<6){ next=nb[(Math.random()*nb.length)|0]; tries++; }
    c.fromN=c.toN; c.toN=next; A=roadNodes[c.fromN]; B=roadNodes[c.toN];
    ux=B.x-A.x; uz=B.z-A.z; segLen=Math.hypot(ux,uz)||1; ux/=segLen; uz/=segLen; rx=uz; rz=-ux; t=0;
  }
  var ahead=Math.min(segLen, Math.max(0,t)+9);
  var tx=A.x+ux*ahead+rx*2.6, tz=A.z+uz*ahead+rz*2.6;  // aim down the right lane
  var desired=Math.atan2(tx-cp.x, tz-cp.z), sp=Math.abs(c.speed);
  var sr=steerAround(cp,c.heading,desired,6+sp*0.4);
  var dh=angleDiff(c.heading,sr.h), steer=Math.max(-1,Math.min(1,dh*2.2));
  driveCar(c, Math.abs(dh)>0.6?0.55:0.9, steer, false, false, dt);
}
function updateTraffic(dt){
  for(var i=0;i<traffic.length;i++){
    var c=traffic[i];
    if(c.onRoad){ driveCivRoad(c,dt); continue; }
    var cp=c.group.position;
    c.retime-=dt;
    var dx=c.tx-cp.x, dz=c.tz-cp.z, dd=Math.hypot(dx,dz);
    if(dd<6 || c.retime<=0){ c.tx=rand(-CITY+8,CITY-8); c.tz=rand(-CITY+8,CITY-8); c.retime=rand(5,10); dx=c.tx-cp.x; dz=c.tz-cp.z; }
    var desired=Math.atan2(dx,dz), sp=Math.abs(c.speed), look=6+sp*0.4;
    var sr=steerAround(cp,c.heading,desired,look);
    var dh=angleDiff(c.heading,sr.h), sharp=Math.abs(dh)>0.6;
    var steer=Math.max(-1,Math.min(1,dh*2.2));
    var imminent=pathBlocked(cp,c.heading,2,4+sp*0.2,2.2);
    var throttle=(imminent&&sharp)?0.25:(sharp?0.6:0.9);
    driveCar(c,throttle,steer,false,false,dt);
  }
}
function carPush(a,b,minD){
  var dx=a.group.position.x-b.group.position.x, dz=a.group.position.z-b.group.position.z;
  var d=Math.hypot(dx,dz)||0.0001;
  if(d<minD){
    var ov=(minD-d)*0.5, nx=dx/d, nz=dz/d;
    a.group.position.x+=nx*ov; a.group.position.z+=nz*ov;
    b.group.position.x-=nx*ov; b.group.position.z-=nz*ov;
    return true;
  }
  return false;
}
// ---- Physics-based damage ----
function applyCrush(e){
  var c=Math.min(1,e.crush);
  e.group.scale.set(1-c*0.12, 1-c*0.30, 1-c*0.10);   // panels cave, roof drops
  e.group.rotation.z = c*0.10*e.leanSign;            // bent chassis lean
}
function spawnDebris(x,y,z,n){
  for(var i=0;i<n;i++){
    var s=0.16+Math.random()*0.22;
    var m=new THREE.Mesh(new THREE.BoxGeometry(s,s,s),
      new THREE.MeshStandardMaterial({color:0x2a2f38,metalness:0.6,roughness:0.5}));
    m.position.set(x,y,z); m.rotation.set(Math.random()*6,Math.random()*6,Math.random()*6); scene.add(m);
    var a=Math.random()*6.283, sp=2.5+Math.random()*5;
    debris.push({m:m,vx:Math.cos(a)*sp,vy:3+Math.random()*4.5,vz:Math.sin(a)*sp,
      rx:(Math.random()-0.5)*9,ry:(Math.random()-0.5)*9,life:0,max:2.4+Math.random()*1.2});
  }
}
function damageCar(e,amount){
  if(!damageOn || amount<=0 || e.shield>0) return;
  amount*=(e.dmgTakenMul!=null?e.dmgTakenMul:1);   // armor upgrade absorbs hits
  e.dmg=Math.min(100,e.dmg+amount); e.crush=e.dmg/100; applyCrush(e);
  if(e.debrisCd<=0 && amount>5){ spawnDebris(e.group.position.x,0.8,e.group.position.z,2+(Math.random()*3|0)); e.debrisCd=0.2; }
}
// elastic-ish impact between two cars: momentum exchange + energy-based damage
function carImpact(a,b){
  var ap=a.group.position, bp=b.group.position;
  var dx=ap.x-bp.x, dz=ap.z-bp.z, d=Math.hypot(dx,dz)||1e-4, minD=2.3;
  if(d>=minD) return 0;
  var nx=dx/d, nz=dz/d, ma=a.mass, mb=b.mass, wa=mb/(ma+mb), wb=ma/(ma+mb);
  var ov=(minD-d);
  ap.x+=nx*ov*wa; ap.z+=nz*ov*wa; bp.x-=nx*ov*wb; bp.z-=nz*ov*wb;   // separate by inverse mass
  var rvx=Math.sin(a.heading)*a.speed-Math.sin(b.heading)*b.speed;
  var rvz=Math.cos(a.heading)*a.speed-Math.cos(b.heading)*b.speed;
  var vn=rvx*nx+rvz*nz, impactSpeed=Math.abs(vn);
  if(vn<0){ a.speed*=(1-0.55*wa); b.speed*=(1-0.55*wb); }            // heavier keeps momentum
  if(impactSpeed>4) sparks((ap.x+bp.x)/2,0.7,(ap.z+bp.z)/2);
  if(impactSpeed>10) thud();
  if(damageOn && impactSpeed>3){
    var energy=impactSpeed*impactSpeed*0.06;
    damageCar(a, energy*wa); damageCar(b, energy*wb);             // lighter car takes the bigger share
  }
  return impactSpeed;
}
// ---- Ramps / jumps ----
function makeRamp(cx,cz,yaw,L,Wd,H){
  var shape=new THREE.Shape();
  shape.moveTo(-L/2,0); shape.lineTo(L/2,0); shape.lineTo(L/2,H); shape.lineTo(-L/2,0);
  var geo=new THREE.ExtrudeGeometry(shape,{depth:Wd,bevelEnabled:false}); geo.translate(0,0,-Wd/2);
  var g=new THREE.Group();
  g.add(new THREE.Mesh(geo, new THREE.MeshStandardMaterial({color:0x39414d,roughness:0.65,metalness:0.35})));
  var lip=new THREE.Mesh(new THREE.BoxGeometry(0.5,0.5,Wd),
    new THREE.MeshStandardMaterial({color:0xffb020,emissive:0xffb020,emissiveIntensity:0.7,roughness:0.4}));
  lip.position.set(L/2,H,0); g.add(lip);
  var stripe=new THREE.Mesh(new THREE.BoxGeometry(L,0.06,0.5),
    new THREE.MeshStandardMaterial({color:0xeef2f7,emissive:0x222a33,roughness:0.6}));
  stripe.position.set(0,H/2+0.05,0); stripe.rotation.z=Math.atan2(H,L); g.add(stripe);
  g.position.set(cx,0,cz); g.rotation.y=yaw-Math.PI/2; scene.add(g);
  ramps.push({x:cx,z:cz,yaw:yaw,L:L,Wd:Wd,H:H});
  return g;
}
function buildRamps(){
  var L=11, Wd=6, H=2.6, want=5, spots=pickClearSpots(want*5), made=0;
  var yaws=[0,Math.PI/2,Math.PI,-Math.PI/2];
  for(var i=0;i<spots.length && made<want;i++){
    var s=spots[i];
    if(Math.hypot(s.x, s.z-(WEDGE-16))<26) continue;            // not on player's spawn
    var yaw=yaws[(Math.random()*yaws.length)|0], fx=Math.sin(yaw), fz=Math.cos(yaw), clear=true;
    for(var d=-L/2; d<=L/2+12; d+=2){ if(circleHitsAABB(s.x+fx*d, s.z+fz*d, 3)){ clear=false; break; } }
    if(!clear) continue;
    makeRamp(s.x,s.z,yaw,L,Wd,H); made++;
  }
}
function rampHeightAt(x,z){
  for(var i=0;i<ramps.length;i++){
    var r=ramps[i], dx=x-r.x, dz=z-r.z;
    var u=dx*Math.sin(r.yaw)+dz*Math.cos(r.yaw);
    var lat=dx*Math.cos(r.yaw)-dz*Math.sin(r.yaw);
    if(u>=-r.L/2 && u<=r.L/2 && Math.abs(lat)<=r.Wd/2) return r.H*((u+r.L/2)/r.L);
  }
  return 0;
}
// ---- Overpasses / bridges (support-only, so the road underneath stays open) ----
function makeOverpass(axis,pos,h){
  var L=16, W=8, span=CITY*2-2*L-8;
  var cx=(axis==="x")?0:pos, cz=(axis==="x")?pos:0;
  var w=(axis==="x")?span:W, d=(axis==="x")?W:span;
  var deckMat=new THREE.MeshStandardMaterial({color:0x2b3038,roughness:0.85,metalness:0.12});
  var deck=posBox(w,0.4,d,cx,h-0.2,cz,deckMat); deck.castShadow=true; deck.receiveShadow=true; scene.add(deck);
  // centre lane line on the deck
  var lineMat=new THREE.MeshStandardMaterial({color:0xd8c24a,emissive:0x2a2206,roughness:0.7});
  if(axis==="x") for(var lx=-w/2+4; lx<w/2-4; lx+=7) scene.add(posBox(3,0.05,0.3,cx+lx,h+0.02,cz,lineMat));
  else           for(var lz=-d/2+4; lz<d/2-4; lz+=7) scene.add(posBox(0.3,0.05,3,cx,h+0.02,cz+lz,lineMat));
  // railings (visual only — you CAN fly off the side)
  var railMat=new THREE.MeshStandardMaterial({color:0x3a4150,roughness:0.7,metalness:0.3});
  if(axis==="x"){ scene.add(posBox(w,0.5,0.22,cx,h+0.25,cz+d/2,railMat)); scene.add(posBox(w,0.5,0.22,cx,h+0.25,cz-d/2,railMat)); }
  else          { scene.add(posBox(0.22,0.5,d,cx+w/2,h+0.25,cz,railMat)); scene.add(posBox(0.22,0.5,d,cx-w/2,h+0.25,cz,railMat)); }
  // support pillars (visual only — never block the 2D road under them)
  var pilMat=new THREE.MeshStandardMaterial({color:0x222831,roughness:0.9});
  var n=6;
  for(var i=0;i<=n;i++){
    var f=(i/n-0.5);
    var px=(axis==="x")?cx+f*w:cx, pz=(axis==="x")?cz:cz+f*d;
    for(var s=-1;s<=1;s+=2){
      var ox=(axis==="x")?0:s*(W/2-0.6), oz=(axis==="x")?s*(W/2-0.6):0;
      var pil=posBox(0.7,h,0.7,px+ox,h/2,pz+oz,pilMat); pil.castShadow=true; scene.add(pil);
    }
  }
  decks.push({x:cx,z:cz,w:w,d:d,h:h});
  // connecting ramps (top edge meets the deck edge at the same height)
  if(axis==="x"){ makeRamp(-w/2-L/2,cz,Math.PI/2,L,W,h); makeRamp(w/2+L/2,cz,-Math.PI/2,L,W,h); }
  else          { makeRamp(cx,-d/2-L/2,0,L,W,h);          makeRamp(cx,d/2+L/2,Math.PI,L,W,h); }
}
function buildOverpasses(){
  makeOverpass("z",-22,4.6);
  makeOverpass("x", 24,4.6);
}
function deckHeightAt(e){
  var x=e.group.position.x, z=e.group.position.z, best=0;
  for(var i=0;i<decks.length;i++){ var d=decks[i];
    if(Math.abs(x-d.x)<=d.w/2 && Math.abs(z-d.z)<=d.d/2 && e.y>d.h-2.0 && d.h>best) best=d.h;
  }
  return best;
}
// ---- Enclosed tunnel (real walls channel you through at ground level) ----
function makeTunnel(axis,pos,center,length,h,halfW,lean){
  var wallMat=new THREE.MeshStandardMaterial({color:0x2a2f38,roughness:0.92,metalness:0.08});
  var roofMat=new THREE.MeshStandardMaterial({color:0x232830,roughness:0.9,metalness:0.1});
  var frameMat=new THREE.MeshStandardMaterial({color:0x3a4150,roughness:0.8,metalness:0.2});
  var stripMat=new THREE.MeshStandardMaterial({color:0xfff2c4,emissive:0xffe9a8,emissiveIntensity:1.1,roughness:0.5});
  var thick=1.0, ph=h+1.2, step=lean?16:10;
  function wallBox(cx,cz,xext,zext){
    var m=posBox(xext,h,zext,cx,h/2,cz,wallMat); m.castShadow=true; m.receiveShadow=true; scene.add(m);
    aabbs.push({minX:cx-xext/2,maxX:cx+xext/2,minZ:cz-zext/2,maxZ:cz+zext/2});
    buildings.push({x:cx,z:cz,w:xext,d:zext});
  }
  if(axis==="x"){
    var cx=center, cz=pos, w=length, interior=halfW*2;
    wallBox(cx,cz+halfW,w,thick); wallBox(cx,cz-halfW,w,thick);
    scene.add(posBox(w,0.6,interior+thick*2,cx,h+0.3,cz,roofMat));
    for(var lx=-w/2+5; lx<w/2-5; lx+=step) scene.add(posBox(0.6,0.12,interior*0.7,cx+lx,h-0.05,cz,stripMat));
    [-1,1].forEach(function(end){ var ex=cx+end*w/2;
      scene.add(posBox(1.4,ph,1.4,ex,ph/2,cz+halfW,frameMat)); scene.add(posBox(1.4,ph,1.4,ex,ph/2,cz-halfW,frameMat));
      scene.add(posBox(1.4,1.4,interior+2.8,ex,ph,cz,frameMat)); });
    if(!lean){ var pa=new THREE.PointLight(0xffe9a8,0.9,30,2); pa.position.set(cx-w/4,h-0.4,cz); scene.add(pa);
      var pb=new THREE.PointLight(0xffe9a8,0.9,30,2); pb.position.set(cx+w/4,h-0.4,cz); scene.add(pb); }
  } else {
    var cxz=pos, cc=center, dd=length, iz=halfW*2;
    wallBox(cxz+halfW,cc,thick,dd); wallBox(cxz-halfW,cc,thick,dd);
    scene.add(posBox(iz+thick*2,0.6,dd,cxz,h+0.3,cc,roofMat));
    for(var lz=-dd/2+5; lz<dd/2-5; lz+=step) scene.add(posBox(iz*0.7,0.12,0.6,cxz,h-0.05,cc+lz,stripMat));
    [-1,1].forEach(function(end){ var ez=cc+end*dd/2;
      scene.add(posBox(1.4,ph,1.4,cxz+halfW,ph/2,ez,frameMat)); scene.add(posBox(1.4,ph,1.4,cxz-halfW,ph/2,ez,frameMat));
      scene.add(posBox(iz+2.8,1.4,1.4,cxz,ph,ez,frameMat)); });
    if(!lean){ var pc=new THREE.PointLight(0xffe9a8,0.9,30,2); pc.position.set(cxz,h-0.4,cc-dd/4); scene.add(pc);
      var pd=new THREE.PointLight(0xffe9a8,0.9,30,2); pd.position.set(cxz,h-0.4,cc+dd/4); scene.add(pd); }
  }
}
function buildTunnels(){ makeTunnel("x",-30,25,64,3.6,5); }
function landDust(x,z){ for(var i=0;i<8;i++){ var a=Math.random()*6.283,s=2+Math.random()*3; addParticle(x,0.2,z,0xb9b29a,0.24+Math.random()*0.2,Math.cos(a)*s,1+Math.random()*1.6,Math.sin(a)*s,0.5); } }
function applyAir(e,dt){
  var p=e.group.position, rs=rampHeightAt(p.x,p.z);
  if(rs>e.y+0.9) rs=0;                                 // a ramp only lifts you when its surface is within a climbable step of where you already are — side/back/edge clip-ins can't teleport you up onto it
  var support=Math.max(rs, deckHeightAt(e));
  var rise=Math.min(30,(support-e.prevSupport)/Math.max(dt,1e-4));   // a sudden support jump can never fling the car upward
  if(e.prevSupport>1.0 && support<e.prevSupport-0.3 && e.vy>=0){   // launched off the lip
    e.vy += 2 + Math.abs(e.speed)*0.18;
    if(e.isPlayer) beep(520,0.12,"square");
  }
  e.vy -= GRAVITY*dt; e.y += e.vy*dt;
  if(e.y<=support){
    var land=e.vy; e.y=support; e.vy=Math.max(0,rise);
    if(e.airborne && land<-7){ landDust(p.x,p.z); if(e.isPlayer) thud(); }
    e.airborne=false;
  } else e.airborne=true;
  e.prevSupport=support; p.y=e.y;
  var tp = e.airborne ? Math.max(-0.5,Math.min(0.5,-e.vy*0.045))
        : (support>0.2 && Math.abs(e.speed)>1 ? -Math.atan2(Math.max(0,rise),Math.abs(e.speed)) : 0);
  e.pitch += (tp-e.pitch)*Math.min(1,dt*9);
  e.group.rotation.x=e.pitch;
}

// ----------------------------------------------------------------------------
//  Collision
// ----------------------------------------------------------------------------
function circleHitsAABB(x,z,r){
  var q=sQuery(x,z,r+4);
  for(var i=0;i<q.a.length;i++){
    var b=aabbs[q.a[i]];
    var cx=Math.max(b.minX,Math.min(x,b.maxX)), cz=Math.max(b.minZ,Math.min(z,b.maxZ));
    if((x-cx)*(x-cx)+(z-cz)*(z-cz)<r*r) return true;
  }
  for(var j=0;j<q.c.length;j++){
    var c=circles[q.c[j]], dx=x-c.x, dz=z-c.z, rr=r+c.r;
    if(dx*dx+dz*dz<rr*rr) return true;
  }
  return false;
}
function resolveCollision(pos,r){
  var hit=false;
  var q=sQuery(pos.x,pos.z,r+4);
  for(var i=0;i<q.a.length;i++){
    var b=aabbs[q.a[i]];
    var cx=Math.max(b.minX,Math.min(pos.x,b.maxX)), cz=Math.max(b.minZ,Math.min(pos.z,b.maxZ));
    var dx=pos.x-cx, dz=pos.z-cz, d2=dx*dx+dz*dz;
    if(d2<r*r){
      var d=Math.sqrt(d2)||0.0001, push=(r-d)/d;
      pos.x+=dx*push; pos.z+=dz*push; hit=true;
    }
  }
  for(var j=0;j<q.c.length;j++){
    var c=circles[q.c[j]], ex=pos.x-c.x, ez=pos.z-c.z, rr=r+c.r, e2=ex*ex+ez*ez;
    if(e2<rr*rr){ var ed=Math.sqrt(e2)||0.0001, ep=(rr-ed)/ed; pos.x+=ex*ep; pos.z+=ez*ep; hit=true; }
  }
  var lim=CITY-2;
  if(pos.x<-lim){pos.x=-lim;hit=true;} if(pos.x>lim){pos.x=lim;hit=true;}
  if(pos.z<-lim){pos.z=-lim;hit=true;} if(pos.z>lim){pos.z=lim;hit=true;}
  return hit;
}
function pathBlocked(pos,h,from,to,rad){
  var sx=Math.sin(h), sz=Math.cos(h);
  for(var d=from; d<=to; d+=2.0){
    if(circleHitsAABB(pos.x+sx*d, pos.z+sz*d, rad)) return true;
  }
  return false;
}
function steerAround(pos,heading,desired,look){
  look = Math.max(look||6, 7);
  function clearAt(h){
    var sx=Math.sin(h), sz=Math.cos(h);
    var d=[2.6,4.6,look*0.5,look*0.78,look];   // fixed near + scaled far
    for(var k=0;k<d.length;k++){
      if(circleHitsAABB(pos.x+sx*d[k], pos.z+sz*d[k], 2.4)) return false;
    }
    return true;
  }
  if(clearAt(desired)) return {h:desired, blocked:false};
  for(var a=12;a<=175;a+=12){
    var ra=a*Math.PI/180;
    if(clearAt(desired+ra)) return {h:desired+ra, blocked:true};
    if(clearAt(desired-ra)) return {h:desired-ra, blocked:true};
  }
  return {h:desired+Math.PI*0.5, blocked:true};  // boxed in: hard turn-out
}

// ----------------------------------------------------------------------------
//  Driving physics
// ----------------------------------------------------------------------------
function driveCar(e, throttle, steer, brake, boost, dt){
  // throttle: -1..1, steer: -1..1, brake/handbrake bool, boost bool
  var maxS = e.maxSpeed;
  var accel = e.isPlayer ? P_ACCEL : P_ACCEL*0.92;
  accel *= (e.accelMul||1);
  if(boost && e.boost>0){ maxS*=BOOST_MULT*(e.boostPow||1); accel*=1.5; e.boost=Math.max(0,e.boost-(34/(e.boostEcon||1))*dt); e.boosting=true; }
  else { e.boosting=false; if(e.isPlayer) e.boost=Math.min(100,e.boost+12*(e.boostRegen||1)*dt); else e.boost=100; }
  if(e.cbBurst>0){ maxS*=1.9; accel*=2.0; }       // chase-breaker nitro
  if(e.stun>0){ maxS*=0.30; accel*=0.4; }          // hit by a chase-breaker

  if(throttle>0){ e.speed += accel*throttle*dt; }
  else if(throttle<0){ e.speed += P_REVERSE*throttle*dt*1.6; } // reverse / decelerate
  // drag
  e.speed *= (1 - 0.6*dt);
  if(brake){ e.speed *= (1 - 3.0*dt); }
  e.speed = Math.max(-P_REVERSE, Math.min(maxS, e.speed));

  // steering scales with speed; reverse inverts
  var spd=Math.abs(e.speed);
  var turnFactor=Math.min(1, spd/6);
  var grip = brake ? 1.7 : 1.0; // handbrake = tighter turn
  if(e.oiled>0) grip*=0.3;       // oil slick: car loses bite and slides
  grip*=mapGrip;                 // rain/ice make every car looser
  grip*=(e.gripMul||1);          // tire upgrade improves bite
  e.heading += steer * (e.turn||TURN_RATE) * turnFactor * grip * (e.speed<0?-1:1) * dt;

  var fx=Math.sin(e.heading), fz=Math.cos(e.heading);
  var p=e.group.position;
  p.x += fx*e.speed*dt; p.z += fz*e.speed*dt;
  var preSp=e.speed;
  if(resolveCollision(p, e.radius)){
    var imp=Math.abs(preSp); e.speed *= (e.bumpKeep||0.35); e.bumped=imp>9;
    if(damageOn && imp>8){ damageCar(e, imp*imp*0.03); sparks(p.x,0.7,p.z); }
  } else e.bumped=false;
  if(e.debrisCd>0) e.debrisCd-=dt;
  if(e.spikeCd>0) e.spikeCd-=dt;
  if(e.oiled>0) e.oiled-=dt;
  e.group.rotation.y = e.heading;
  applyAir(e,dt);
}

// ----------------------------------------------------------------------------
//  AI driving
// ----------------------------------------------------------------------------
function angleDiff(a,b){ return Math.atan2(Math.sin(b-a),Math.cos(b-a)); }

function updateAI(dt){
  for(var i=0;i<agents.length;i++){
    if(agents[i].role==="cop") driveCopAI(agents[i],i,dt);
    else driveRobberAI(agents[i],dt);
  }
}
function driveCopAI(c,idx,dt){
  var cp=c.group.position, tp=theRobber.group.position;
  var gx=tp.x, gz=tp.z, crossing=acrossWater(c,tp.z);
  if(crossing){ var ba=bridgeAim(c,tp.z); gx=ba.x; gz=ba.z; }
  var toX=gx-cp.x, toZ=gz-cp.z, dist=Math.hypot(toX,toZ)||0.0001;
  var off=crossing?0:((idx%4)-1.5)*8;
  var perpX=-toZ/dist*off, perpZ=toX/dist*off;
  var desired=Math.atan2(toX+perpX, toZ+perpZ);
  var aggro=heat/100;                                                          // heat-driven aggression: faster, bolder cops as the heat rises
  var sp=Math.abs(c.speed), look=7+sp*0.45;
  var sr=steerAround(cp, c.heading, desired, look);
  var dh=angleDiff(c.heading, sr.h);
  var steer=Math.max(-1,Math.min(1, dh*2.4));
  var near=4+sp*0.22, sharp=Math.abs(dh)>0.55;
  var imminent=pathBlocked(cp, c.heading, 2, near, 2.3);
  var throttle=(imminent&&sharp)?0.3:((sr.blocked||sharp)?0.85:1.0);
  var boost=(dist>(16-aggro*8) && !sr.blocked && !imminent && Math.abs(dh)<0.45);
  var oldMax=c.maxSpeed; c.maxSpeed=oldMax*(1+aggro*0.18);
  driveCar(c, throttle, steer, imminent&&sharp, boost, dt);
  c.maxSpeed=oldMax;
}
function acrossWater(e,gz){
  if(crossChan<=0) return false;
  var pz=e.group.position.z;
  if((pz>crossZc)!==(gz>crossZc)) return true;                       // goal is on the far shore — head for the bridge
  return e.y>1.5 && Math.abs(pz-crossZc)<crossChan+30;               // already up on the bridge — stay centered until off the far ramp
}
function bridgeAim(e,gz){
  var pz=e.group.position.z, px=e.group.position.x;
  if(Math.abs(px)>6 && Math.abs(pz-crossZc)>crossChan-10)                              // still on land and off-axis: line up with the corridor on our shore
    return {x:0, z:(pz>crossZc?crossZc+crossChan+18:crossZc-crossChan-18)};
  return {x:0, z:(gz>crossZc?crossZc+crossChan+18:crossZc-crossChan-18)};              // aligned / on the span: drive straight across
}
function driveRobberAI(r,dt){
  var rp=r.group.position;
  var nx=0,nz=0,ndist=1e9;
  for(var i=0;i<agents.length;i++){ if(agents[i].role!=="cop") continue;
    var d=Math.hypot(agents[i].group.position.x-rp.x, agents[i].group.position.z-rp.z);
    if(d<ndist){ ndist=d; nx=agents[i].group.position.x; nz=agents[i].group.position.z; } }
  var tgt=objectives[objIndex], gx, gz;
  if(mode==="heist" && !hijacked && cashTruck){ gx=cashTruck.group.position.x; gz=cashTruck.group.position.z; }
  else { gx=tgt?tgt.x:0; gz=tgt?tgt.z:0; }
  if(acrossWater(r,gz)){ var ba=bridgeAim(r,gz); gx=ba.x; gz=ba.z; }                // route via the bridge if the goal is across the ocean
  var toGX=gx-rp.x, toGZ=gz-rp.z, gl=Math.hypot(toGX,toGZ)||0.0001; toGX/=gl; toGZ/=gl;
  var ax=rp.x-nx, az=rp.z-nz, al=Math.hypot(ax,az)||0.0001; ax/=al; az/=al;
  var flee=Math.max(0, 1-ndist/26);                 // dodge cops harder when close
  if(crossChan>0 && Math.abs(rp.z-crossZc)<crossChan+10) flee*=0.25;                    // on/near the bridge: stay in the lane, don't swerve into the sea
  var dx=toGX*(1-flee)+ax*flee*1.3, dz=toGZ*(1-flee)+az*flee*1.3;
  var sp=Math.abs(r.speed), look=7+sp*0.5;
  var sr=steerAround(rp, r.heading, Math.atan2(dx,dz), look);
  var dh=angleDiff(r.heading, sr.h);
  var steer=Math.max(-1,Math.min(1, dh*2.4));
  var near=4+sp*0.22, sharp=Math.abs(dh)>0.55;
  var imminent=pathBlocked(rp, r.heading, 2, near, 2.3);
  var throttle=(imminent&&sharp)?0.3:((sr.blocked||sharp)?0.85:1.0);
  var boost=(!sr.blocked && !imminent && Math.abs(dh)<0.4 && (flee>0.3 || gl>20));
  driveCar(r, throttle, steer, imminent&&sharp, boost, dt);
}

// ----------------------------------------------------------------------------
//  Input
// ----------------------------------------------------------------------------
function bindInput(){
  window.addEventListener("keydown",function(e){
    keys[e.key.toLowerCase()]=true;
    if(["arrowup","arrowdown","arrowleft","arrowright"," "].indexOf(e.key.toLowerCase())>=0) e.preventDefault();
    if(e.key.toLowerCase()==="c") recenterCamera();
    if(e.key===" ") deployChaseBreaker();
  });
  window.addEventListener("keyup",function(e){ keys[e.key.toLowerCase()]=false; });

  isTouch=("ontouchstart" in window)||navigator.maxTouchPoints>0;
  if(isTouch){
    document.getElementById("touch").style.display="block";
    bindStick("moveStick","moveKnob",moveVec);
    bindStick("lookStick","lookKnob",lookVec);
    var bb=document.getElementById("boostBtn");
    bb.addEventListener("touchstart",function(e){touchBoost=true;e.preventDefault();},{passive:false});
    bb.addEventListener("touchend",function(e){touchBoost=false;e.preventDefault();},{passive:false});
    document.getElementById("recenterBtn").addEventListener("touchstart",function(e){recenterCamera();e.preventDefault();},{passive:false});
    document.getElementById("breakerBtn").addEventListener("touchstart",function(e){deployChaseBreaker();e.preventDefault();},{passive:false});
  }
}
function bindStick(stickId,knobId,vec){
  var stick=document.getElementById(stickId), knob=document.getElementById(knobId), sid=null, origin=null;
  stick.addEventListener("touchstart",function(e){
    var t=e.changedTouches[0]; sid=t.identifier;
    var r=stick.getBoundingClientRect(); origin={x:r.left+r.width/2,y:r.top+r.height/2}; e.preventDefault();
  },{passive:false});
  stick.addEventListener("touchmove",function(e){
    for(var i=0;i<e.changedTouches.length;i++){
      var t=e.changedTouches[i]; if(t.identifier!==sid) continue;
      var dx=t.clientX-origin.x, dy=t.clientY-origin.y, max=52, d=Math.hypot(dx,dy);
      if(d>max){dx=dx/d*max;dy=dy/d*max;}
      knob.style.transform="translate("+dx+"px,"+dy+"px)";
      vec.x=dx/max; vec.z=dy/max;
    }
    e.preventDefault();
  },{passive:false});
  function end(e){ for(var i=0;i<e.changedTouches.length;i++){ if(e.changedTouches[i].identifier===sid){ sid=null; vec.x=0; vec.z=0; knob.style.transform="translate(0,0)"; } } }
  stick.addEventListener("touchend",end); stick.addEventListener("touchcancel",end);
}

// camera orbit controls (mouse + wheel + touch on the look stick + pinch)
function bindCameraControls(el){
  el.addEventListener("mousedown",function(e){ dragging=true; lastPX=e.clientX; lastPY=e.clientY; });
  window.addEventListener("mouseup",function(){ dragging=false; });
  window.addEventListener("mousemove",function(e){
    if(!dragging) return;
    camTargetYaw -= (e.clientX-lastPX)*0.006*settings.camSens;
    camTargetPitch -= (e.clientY-lastPY)*0.005*settings.camSens*(settings.invertY?-1:1);
    camTargetPitch=Math.max(0.18,Math.min(1.3,camTargetPitch));
    lastPX=e.clientX; lastPY=e.clientY;
  });
  el.addEventListener("wheel",function(e){
    camTargetDist=Math.max(7,Math.min(34,camTargetDist+e.deltaY*0.02)); e.preventDefault();
  },{passive:false});
  // pinch zoom
  el.addEventListener("touchmove",function(e){
    if(e.touches.length===2){
      var d=Math.hypot(e.touches[0].clientX-e.touches[1].clientX, e.touches[0].clientY-e.touches[1].clientY);
      if(pinchStart) camTargetDist=Math.max(7,Math.min(34,camTargetDist+(pinchStart-d)*0.05));
      pinchStart=d;
    }
  },{passive:true});
  el.addEventListener("touchend",function(){ pinchStart=0; });
}
function recenterCamera(){ camTargetYaw=0; camTargetPitch=0.62; camTargetDist=15; }

function readDrive(){
  var throttle=0, steer=0;
  if(keys["w"]||keys["arrowup"]) throttle+=1;
  if(keys["s"]||keys["arrowdown"]) throttle-=1;
  if(keys["a"]||keys["arrowleft"]) steer+=1;
  if(keys["d"]||keys["arrowright"]) steer-=1;
  if(isTouch && (moveVec.x||moveVec.z)){ throttle=-moveVec.z; steer=-moveVec.x; }
  var brake = false;             // space is now the chase breaker
  var boost = !!keys["shift"] || touchBoost;
  // keyboard camera nudge
  if(keys["q"]) camTargetYaw+=0.03*settings.camSens;
  if(keys["e"]) camTargetYaw-=0.03*settings.camSens;
  return {throttle:throttle,steer:steer,brake:brake,boost:boost};
}

// ----------------------------------------------------------------------------
//  Audio
// ----------------------------------------------------------------------------
function initAudio(){
  if(audio) return;
  try{
    audio=new (window.AudioContext||window.webkitAudioContext)();
    masterGain=audio.createGain(); masterGain.gain.value=settings.volume; masterGain.connect(audio.destination);
    sirenGain=audio.createGain(); sirenGain.gain.value=0; sirenGain.connect(masterGain);
    sirenOsc=audio.createOscillator(); sirenOsc.type="sawtooth"; sirenOsc.frequency.value=600;
    sirenOsc.connect(sirenGain); sirenOsc.start();
    engineGain=audio.createGain(); engineGain.gain.value=0; engineGain.connect(masterGain);
    engineOsc=audio.createOscillator(); engineOsc.type="sawtooth"; engineOsc.frequency.value=60;
    engineOsc.connect(engineGain); engineOsc.start();
    skidGain=audio.createGain(); skidGain.gain.value=0; skidGain.connect(masterGain);
    skidOsc=audio.createOscillator(); skidOsc.type="sawtooth"; skidOsc.frequency.value=1100;
    skidOsc.connect(skidGain); skidOsc.start();
  }catch(e){ audio=null; }
}
function setEngine(speed){
  if(!audio||!soundOn){ if(engineGain) engineGain.gain.value=0; return; }
  var t=audio.currentTime;
  engineGain.gain.setTargetAtTime(0.045,t,0.1);
  engineOsc.frequency.setTargetAtTime(52+speed*7,t,0.05);
}
function setSkid(amt){
  if(!audio||!soundOn){ if(skidGain) skidGain.gain.value=0; return; }
  var t=audio.currentTime;
  skidGain.gain.setTargetAtTime(amt*0.05,t,0.05);
  skidOsc.frequency.setValueAtTime(900+amt*500,t);
}
function thud(){ beep(70,0.16,"sawtooth"); beep(110,0.10,"square"); }
function setSiren(v){
  if(!audio||!soundOn){ if(sirenGain) sirenGain.gain.value=0; return; }
  var t=audio.currentTime;
  sirenGain.gain.setTargetAtTime(v*0.05,t,0.1);
  sirenOsc.frequency.setValueAtTime(560+Math.sin(t*8)*180,t);
}
function beep(freq,dur,type){
  if(!audio||!soundOn) return;
  try{
    var o=audio.createOscillator(), g=audio.createGain();
    o.type=type||"square"; o.frequency.value=freq; g.gain.value=0.0001;
    o.connect(g); g.connect(masterGain||audio.destination);
    var t=audio.currentTime;
    g.gain.exponentialRampToValueAtTime(0.18,t+0.02);
    g.gain.exponentialRampToValueAtTime(0.0001,t+dur);
    o.start(t); o.stop(t+dur);
  }catch(e){}
}

// ----------------------------------------------------------------------------
//  Flow
// ----------------------------------------------------------------------------
function disposeDeep(obj){
  if(!obj) return;
  obj.traverse(function(o){
    if(o.geometry && !o.geometry.shared) o.geometry.dispose();
    if(o.material){ var mats=Array.isArray(o.material)?o.material:[o.material]; for(var i=0;i<mats.length;i++) if(mats[i] && !mats[i].shared) mats[i].dispose(); }
  });
}
function clearWorld(){ while(scene.children.length){ var c=scene.children[0]; disposeDeep(c); scene.remove(c); } aabbs=[];circles=[];buildings=[];agents=[];loot=[];traffic=[];ramps=[];powerups=[];hazards=[];shieldBubble=null;smokeZones=[];objectives=[];weatherObj=null;decks=[];roadNodes=[];roadAdj=[];chopper=null;pChop=null;roadblocks=[];copDeployTimer=0;cashTruck=null;props=[];skidPool=[];skidIdx=0;crew=null;crossChan=0;drawbridge=null;train=null;onFoot=false;human=null;garageCars=[];selectedGarage=-1;leftStation=false;chaseStarted=false;roofHeli=null;heliTaken=false;customWater=[];customDraws=[];boundHalfX=0;boundHalfZ=0;customTrain=null;liftSpans=[];thirdDraw=null;instPools={};sGrid={};sGridBuilt=-1; }

function startGame(){
  clearWorld();
  onDutyMode=(mode==="precinct"); if(onDutyMode){ map="world"; role="cop"; }
  if(mode==="heistrun") role="robber";
  if(map!=="custom") activeCustomBase=null;   // only custom-map plays (set by playCustomWorld, just before startGame) keep it
  applyQuality(settings.quality);
  buildLights(); buildWorld();
  if(map!=="custom"){ buildRamps(); if(map!=="world"){ buildOverpasses(); buildTunnels(); } buildProps(); }
  cbCooldown=0; shockwaves=[]; particles=[]; debris=[]; ending=null;
  document.getElementById("bigtext").className="bigtext";
  driftCombo=0; driftScore=0; driftActive=false; driftHold=0;
  timeLeft=ROUND_TIME; score=0;
  var cfg=DIFF[diff];
  chopperMode=(mode==="chopper"); if(chopperMode) role="cop";
  playerConfig = (role==="cop") ? COP_CONFIG : ROB_CONFIG;
  var oppConfig = (role==="cop") ? ROB_CONFIG : COP_CONFIG;
  oppConfig.style = playerConfig.style;              // same car type both sides = fair fight

  var st = STYLES[playerConfig.style] || STYLES.supercar;
  var pMax = P_MAXSPEED*st.speed, pTurn = TURN_RATE*st.turn;
  var aMax = pMax*cfg.aiFactor;                       // AI matches your car, nudged by difficulty

  if(onDutyMode){
    buildStation(); spawnHuman();
  } else if(chopperMode){
    buildPlayerChopper();
  } else {
    var pg=makeCar(role==="cop", playerConfig);
    pg.position.set((map==="custom"&&!activeCustomBase)?customSpawnP.x:0, 0, (map==="custom"&&!activeCustomBase)?customSpawnP.z:(WEDGE-16)); scene.add(pg);
    player=makeCarEntity({group:pg,role:role,isPlayer:true,heading:Math.PI,
      maxSpeed:pMax, turn:pTurn, bumpKeep:st.bump, mass:st.mass});
    applyUpgrades(player);
  }
  camTargetYaw=0; camYaw=0; camTargetPitch=0.62; camPitch=0.62; camTargetDist=15; camDist=15;
  if(chopperMode){ camTargetPitch=0.72; camPitch=0.72; camTargetDist=28; camDist=28; }
  else if(onDutyMode){ camTargetPitch=0.5; camPitch=0.5; camTargetDist=11; camDist=11; }

  if(role==="robber"){
    var xs=[-10,10,0], oppZ=(crossChan>0)?WEDGE-40:-WEDGE+20;
    for(var i=0;i<cfg.cops;i++){
      var g=makeCar(true,oppConfig); g.position.set(xs[i]||0,0,oppZ); scene.add(g);
      agents.push(makeCarEntity({group:g,role:"cop",maxSpeed:aMax,turn:pTurn,bumpKeep:st.bump,heading:0,mass:st.mass}));
    }
    buildLoot();
    elObjective.textContent="Survive the timer";
    elRoleText.textContent="Robber"; elRoleDot.className="dot rob"; elScore.style.display="";
  } else {
    var oppZ2=onDutyMode?60:((crossChan>0)?WEDGE-40:-WEDGE+20);
    var g2=makeCar(false,oppConfig); g2.position.set((map==="custom"&&!activeCustomBase)?customSpawnR.x:0, 0, (map==="custom"&&!activeCustomBase)?customSpawnR.z:oppZ2); scene.add(g2);
    agents.push(makeCarEntity({group:g2,role:"robber",maxSpeed:aMax,turn:pTurn,bumpKeep:st.bump,heading:0,mass:st.mass}));
    elObjective.textContent="Wreck the robber's car";
    elRoleText.textContent="Cop"; elRoleDot.className="dot cop"; elScore.style.display="none";
  }

  if(chopperMode){
    var cspots=[-14,14,0], cz0=(crossChan>0)?WEDGE-44:-WEDGE+24;
    for(var ci=0;ci<3;ci++){ var cg=makeCar(true,COP_CONFIG); cg.position.set(cspots[ci],0,cz0); scene.add(cg);
      agents.push(makeCarEntity({group:cg,role:"cop",maxSpeed:aMax,turn:pTurn,bumpKeep:st.bump,mass:st.mass,heading:0})); }
    elObjective.textContent="Spotlight the suspect to pin them — ground units will close in";
    elRoleText.textContent="Air Unit"; elRoleDot.className="dot cop"; elScore.style.display="none";
  }

  buildTraffic(map==="world"?150:10);
  buildPowerups();
  smokeZones=[];
  document.getElementById("breakerBtn").textContent=GADGETS[gadget].short;
  shieldBubble=new THREE.Mesh(new THREE.SphereGeometry(2.0,20,16),
    new THREE.MeshBasicMaterial({color:0x52d6ff,transparent:true,opacity:0.16,side:THREE.DoubleSide}));
  shieldBubble.visible=false; scene.add(shieldBubble);

  theRobber=(role==="robber")?player:agents[0];
  theRobber.health=100; theRobber.dmgCd=0;
  elHealthLabel.textContent=chopperMode?"Suspect Pinned":((role==="cop")?"Robber Car":"Your Car");
  elHealth.style.width="100%"; elHealth.className="health";

  spawnCfg={ cfg:(role==="robber"?oppConfig:playerConfig), max:aMax, turn:pTurn, bump:st.bump, mass:st.mass,
             maxCops:(role==="robber"?cfg.cops+4:4) };
  heat=0; heatSpawned=0; copDeployTimer=8; pinMeter=0; heistStreak=0;
  if(elStreak) elStreak.classList.toggle("hidden", mode!=="heistrun");
  hijackMeter=0; hijacked=false; cashTruck=null;
  buildObjectives();
  if(!chopperMode) buildChopper();
  if(mode==="heist") buildCashTruck();
  if(role==="robber") buildCrew();

  state="playing";
  document.getElementById("menu").classList.add("hidden");
  document.getElementById("end").classList.add("hidden");
  elHud.classList.remove("hidden");
  initAudio(); if(audio&&audio.state==="suspended") audio.resume();
  clock.start();
}
function endGame(win,reason,kind){
  if(ending || state!=="playing") return;
  if(sirenGain) sirenGain.gain.value=0;
  if(engineGain) engineGain.gain.value=0; if(skidGain) skidGain.gain.value=0;
  var f=theRobber?theRobber.group.position:player.group.position;
  ending={ t:0, dur:1.8, win:win, reason:reason,
    fx:f.x, fz:f.z, baseAng:Math.atan2(camera.position.x-f.x, camera.position.z-f.z) };
  var earn = 80 + objIndex*100 + Math.floor(driftScore*0.1) + score + (win?500:120) + ((kind==="busted"&&role==="cop")?400:0);
  lastEarn=earn; prog.bank+=earn; if(earn>prog.bestCash) prog.bestCash=earn;
  if(win && role==="robber"){ var ut=ROUND_TIME-timeLeft; if(!prog.bestEscape || ut<prog.bestEscape) prog.bestEscape=ut; }
  persist(); updateBankUI();
  var big=document.getElementById("bigtext"), busted=(kind==="busted");
  big.textContent=busted?"Busted!":"Escaped!";
  big.className="bigtext show "+(busted?"busted":"escaped");
  if(busted){ beep(150,0.3,"sawtooth"); setTimeout(function(){beep(90,0.45,"sawtooth");},150); }
  else { beep(740,0.16); setTimeout(function(){beep(990,0.22);},150); }
}
function showEndScreen(win,reason){
  state="over";
  if(win){ beep(660,0.12); setTimeout(function(){beep(880,0.18);},130); }
  else { beep(300,0.2,"sawtooth"); setTimeout(function(){beep(180,0.3,"sawtooth");},160); }
  var v=document.getElementById("verdict");
  v.textContent=win?"You Win":"Busted"; v.className="verdict "+(win?"win":"lose");
  document.getElementById("endSub").textContent=reason+"  ·  Earned $"+lastEarn+"  ·  Cash $"+prog.bank;
  document.getElementById("bigtext").className="bigtext";
  elHud.classList.add("hidden");
  document.getElementById("end").classList.remove("hidden");
}
function quitToMenu(){
  state="menu"; ending=null; document.getElementById("bigtext").className="bigtext";
  if(sirenGain) sirenGain.gain.value=0;
  if(engineGain) engineGain.gain.value=0; if(skidGain) skidGain.gain.value=0;
  elHud.classList.add("hidden"); document.getElementById("end").classList.add("hidden");
  document.getElementById("menu").classList.remove("hidden");
  updateBankUI();
}

// ---- Chase Breaker ----
function deployChaseBreaker(){
  if(state!=="playing" || cbCooldown>0 || ending) return;
  var G=GADGETS[gadget]; cbCooldown=G.cd;
  var px=player.group.position.x, pz=player.group.position.z;
  var bx=Math.sin(player.heading), bz=Math.cos(player.heading);
  if(gadget==="nitro"){
    player.cbBurst=2.6; player.boost=Math.min(100,player.boost+40);
    flashScreen(); beep(300,0.16,"sawtooth"); setTimeout(function(){beep(540,0.18,"square");},90);
  } else if(gadget==="emp"){
    for(var i=0;i<agents.length;i++){ if(Math.hypot(agents[i].group.position.x-px,agents[i].group.position.z-pz)<24) agents[i].stun=2.5; }
    spawnShockwave(px,pz); flashScreen(); beep(220,0.18,"sawtooth"); setTimeout(function(){beep(140,0.26,"sawtooth");},80);
  } else if(gadget==="smoke"){
    smokeZones.push({x:px-bx*2.5,z:pz-bz*2.5,r:5.5,life:0,max:6});
    beep(180,0.34,"sawtooth");
  } else {                                   // drop spikes
    spawnHazard(px-bx*3.0, pz-bz*3.0, "spike");
    beep(160,0.12,"square"); setTimeout(function(){beep(120,0.14,"square");},70);
  }
}
function spawnShockwave(x,z){
  var ring=new THREE.Mesh(new THREE.RingGeometry(1.2,1.9,40),
    new THREE.MeshBasicMaterial({color:0x7fe0ff,transparent:true,opacity:0.9,side:THREE.DoubleSide}));
  ring.rotation.x=-Math.PI/2; ring.position.set(x,0.25,z);
  scene.add(ring); shockwaves.push({mesh:ring,life:0});
}
function flashScreen(){
  if(!flashEl) flashEl=document.getElementById("flash");
  flashEl.classList.remove("on"); void flashEl.offsetWidth; flashEl.classList.add("on");
}
// ---- Particles (sparks / smoke / explosion) ----
function addParticle(x,y,z,col,size,vx,vy,vz,max){
  var m=new THREE.Mesh(new THREE.SphereGeometry(size,6,6), new THREE.MeshBasicMaterial({color:col,transparent:true,opacity:1}));
  m.position.set(x,y,z); scene.add(m);
  particles.push({m:m,vx:vx,vy:vy,vz:vz,life:0,max:max});
}
function sparks(x,y,z){ for(var i=0;i<10;i++){ var a=Math.random()*6.283, s=2+Math.random()*4; addParticle(x,y,z,0xffcf6a,0.12,Math.cos(a)*s,1+Math.random()*3,Math.sin(a)*s,0.4); } }
function smoke(x,y,z){ addParticle(x,y+0.3,z,0x4a4f5a,0.4+Math.random()*0.35,(Math.random()-0.5)*1.2,1.2+Math.random(),(Math.random()-0.5)*1.2,1.0); }
function explode(x,y,z){
  for(var i=0;i<26;i++){ var a=Math.random()*6.283, s=3+Math.random()*7; addParticle(x,y,z,(i%2?0xff5a2a:0xffd23a),0.18+Math.random()*0.22,Math.cos(a)*s,2+Math.random()*5,Math.sin(a)*s,0.7); }
  spawnShockwave(x,z); flashScreen();
}
// ---- Persistent skid marks ----
function addSkidStamp(x,z,ang,len){
  if(!skidMat) skidMat=new THREE.MeshBasicMaterial({color:0x0a0a0c,transparent:true,opacity:0.42,depthWrite:false});
  var m;
  if(skidPool.length<SKID_CAP){ m=new THREE.Mesh(new THREE.BoxGeometry(1,0.02,1),skidMat); scene.add(m); skidPool.push(m); }
  else { m=skidPool[skidIdx]; skidIdx=(skidIdx+1)%SKID_CAP; }
  m.visible=true; m.position.set(x,0.045,z); m.rotation.y=ang; m.scale.set(0.34,1,Math.max(0.35,len));
}
function stampTrack(e,side,x,z){
  var key="_sk"+side, last=e[key];
  if(last){ var dx=x-last.x, dz=z-last.z, d=Math.hypot(dx,dz);
    if(d>=0.5){ addSkidStamp((x+last.x)/2,(z+last.z)/2,Math.atan2(dx,dz),d+0.3); e[key]={x:x,z:z}; } }
  else e[key]={x:x,z:z};
}
function updateSkids(dt){
  var cars=[player].concat(agents);
  for(var i=0;i<cars.length;i++){
    var e=cars[i]; if(!e||!e.group.visible) continue;
    var sp=Math.abs(e.speed);
    var yr=(e._prevH!=null)?Math.abs(angleDiff(e.heading,e._prevH))/Math.max(dt,0.001):0;
    e._prevH=e.heading;
    var drift=sp>11 && (yr>1.05 || (e.isPlayer && e._wantSkid));
    if(drift && e.y<0.6){
      var h=e.heading, fx=Math.sin(h), fz=Math.cos(h), rxv=Math.cos(h), rzv=-Math.sin(h);
      var bxp=e.group.position.x-fx*1.3, bzp=e.group.position.z-fz*1.3;
      stampTrack(e,"L",bxp+rxv*0.82,bzp+rzv*0.82);
      stampTrack(e,"R",bxp-rxv*0.82,bzp-rzv*0.82);
    } else { e._skL=null; e._skR=null; }
  }
}
// ---- Destructible props (hydrants, signs, fences) ----
function makeHydrant(){
  var g=new THREE.Group(); var red=new THREE.MeshStandardMaterial({color:0xd23b2e,roughness:0.5,metalness:0.3});
  var b=new THREE.Mesh(new THREE.CylinderGeometry(0.18,0.2,0.55,12),red); b.position.y=0.3; b.castShadow=true; g.add(b);
  var cap=new THREE.Mesh(new THREE.SphereGeometry(0.2,12,8),red); cap.position.y=0.58; g.add(cap);
  for(var s=-1;s<=1;s+=2){ var nz=new THREE.Mesh(new THREE.CylinderGeometry(0.07,0.07,0.18,8),red); nz.rotation.z=Math.PI/2; nz.position.set(s*0.22,0.36,0); g.add(nz); }
  return {group:g, r:0.5, type:"hydrant"};
}
function makeStreetSign(){
  var g=new THREE.Group();
  var post=new THREE.Mesh(new THREE.CylinderGeometry(0.06,0.06,1.5,8), new THREE.MeshStandardMaterial({color:0x8a909a,roughness:0.5,metalness:0.6})); post.position.y=0.75; post.castShadow=true; g.add(post);
  var board=new THREE.Mesh(new THREE.BoxGeometry(0.9,0.6,0.06), new THREE.MeshStandardMaterial({color:(Math.random()<0.5?0xf2b21f:0x2f8f4e),roughness:0.5})); board.position.y=1.4; board.castShadow=true; g.add(board);
  return {group:g, r:0.5, type:"sign"};
}
function makeFenceSeg(){
  var g=new THREE.Group(); var wood=new THREE.MeshStandardMaterial({color:0xb7c0cc,roughness:0.7,metalness:0.2});
  g.add(posBox(2.2,0.85,0.1,0,0.55,0,wood));
  g.add(posBox(0.12,1.0,0.12,-1.0,0.5,0,wood)); g.add(posBox(0.12,1.0,0.12,1.0,0.5,0,wood));
  return {group:g, r:1.25, type:"fence"};
}
function buildProps(){
  props=[];
  var n=(map==="world")?26:14, spots=pickClearSpots(n*2), made=0, builders=[makeHydrant,makeStreetSign,makeStreetSign,makeFenceSeg];
  for(var i=0;i<spots.length && made<n;i++){
    var s=spots[i];
    if(Math.hypot(s.x,s.z-(WEDGE-16))<22 || Math.hypot(s.x,s.z-(-WEDGE+20))<22) continue;
    var p=builders[(Math.random()*builders.length)|0]();
    p.group.position.set(s.x,0,s.z); p.group.rotation.y=Math.random()*6.28; scene.add(p.group);
    p.x=s.x; p.z=s.z; p.broken=false; props.push(p); made++;
  }
}
function breakProp(p,e){
  if(p.broken) return; p.broken=true; p.group.visible=false; var x=p.x, z=p.z;
  spawnDebris(x,0.5,z, p.type==="fence"?8:5);
  if(p.type==="hydrant"){ for(var i=0;i<16;i++){ var a=Math.random()*6.283, sp=1+Math.random()*3; addParticle(x,0.4,z,0xbfe6ff,0.16,Math.cos(a)*sp,4+Math.random()*4,Math.sin(a)*sp,0.9); } beep(220,0.1,"sine"); }
  else sparks(x,0.6,z);
  smoke(x,0.3,z); thud();
  if(e) e.speed*=0.92;
}
function updateProps(dt){
  var cars=[player].concat(agents);
  for(var i=0;i<props.length;i++){ var p=props[i]; if(p.broken) continue;
    for(var c=0;c<cars.length;c++){ var e=cars[c]; if(!e||!e.group.visible) continue;
      if(Math.hypot(e.group.position.x-p.x,e.group.position.z-p.z)<p.r+1.1 && Math.abs(e.speed)>4){ breakProp(p,e); break; }
    }
  }
}

// ----------------------------------------------------------------------------
//  Update
// ----------------------------------------------------------------------------
function animateFX(dt){
  for(var sw=shockwaves.length-1;sw>=0;sw--){
    var W=shockwaves[sw]; W.life+=dt; var kk=W.life/0.6;
    W.mesh.scale.setScalar(1+kk*16); W.mesh.material.opacity=Math.max(0,0.9*(1-kk));
    if(W.life>=0.6){ disposeDeep(W.mesh); scene.remove(W.mesh); shockwaves.splice(sw,1); }
  }
  for(var pi=particles.length-1;pi>=0;pi--){
    var P=particles[pi]; P.life+=dt;
    P.m.position.x+=P.vx*dt; P.m.position.y+=P.vy*dt; P.m.position.z+=P.vz*dt; P.vy-=6*dt;
    P.m.material.opacity=Math.max(0,1-P.life/P.max);
    if(P.life>=P.max){ disposeDeep(P.m); scene.remove(P.m); particles.splice(pi,1); }
  }
  for(var di=debris.length-1;di>=0;di--){
    var D=debris[di]; D.life+=dt;
    D.vy-=GRAVITY*dt;
    D.m.position.x+=D.vx*dt; D.m.position.y+=D.vy*dt; D.m.position.z+=D.vz*dt;
    if(D.m.position.y<=0.12){ D.m.position.y=0.12; D.vy*=-0.42; D.vx*=0.7; D.vz*=0.7; }
    D.m.rotation.x+=D.rx*dt; D.m.rotation.z+=D.ry*dt;
    if(D.life>D.max-0.6){ D.m.material.transparent=true; D.m.material.opacity=Math.max(0,(D.max-D.life)/0.6); }
    if(D.life>=D.max){ disposeDeep(D.m); scene.remove(D.m); debris.splice(di,1); }
  }
}
function stepEnding(dt){
  ending.t+=dt;
  animateFX(dt*0.4);                       // slow-motion effects
  var f=ending, k=Math.min(1,3.2*dt);
  var ang=f.baseAng + f.t*0.5, dist=Math.max(4, 8.5 - f.t*2.4);
  var hor=dist*0.82, hgt=dist*0.6+1.2;
  var tx=f.fx+Math.sin(ang)*hor, tz=f.fz+Math.cos(ang)*hor;
  camera.position.x+=(tx-camera.position.x)*k;
  camera.position.y+=(Math.max(2.2,hgt)-camera.position.y)*k;
  camera.position.z+=(tz-camera.position.z)*k;
  camera.lookAt(f.fx,1.0,f.fz);
  if(f.t>=f.dur){ var w=f.win, r=f.reason; ending=null; showEndScreen(w,r); }
}
function update(dt){
  if(state!=="playing") return;
  if(ending){ stepEnding(dt); return; }
  // chase-breaker timers
  if(cbCooldown>0) cbCooldown=Math.max(0,cbCooldown-dt);
  if(player.cbBurst>0) player.cbBurst-=dt;
  for(var sti=0;sti<agents.length;sti++){ if(agents[sti].stun>0) agents[sti].stun-=dt; }
  animateFX(dt);
  updateWeather(dt);
  var inp=readDrive();
  if(onFoot){ walkPlayer(inp,dt); }
  else if(chopperMode || (player&&player.isHeli)){ flyChopper(inp,dt); }
  else {
  driveCar(player, inp.throttle, inp.steer, inp.brake, inp.boost, dt);
  // --- feel: engine, skid, drift, smoke, flames, thud ---
  var pspeed=Math.abs(player.speed);
  setEngine(pspeed);
  if(player.bumped){ thud(); sparks(player.group.position.x,0.7,player.group.position.z); player.bumped=false; }
  var hx=Math.sin(player.heading), hz=Math.cos(player.heading);
  var rearX=player.group.position.x-hx*1.5, rearZ=player.group.position.z-hz*1.5;
  var drifting=pspeed>11 && Math.abs(inp.steer)>0.6;
  player._wantSkid=drifting;
  setSkid(drifting?Math.min(1,pspeed/22):0);
  if(drifting){
    smoke(rearX+(Math.random()-0.5)*1.2, 0.35, rearZ+(Math.random()-0.5)*1.2);
    driftCombo+=pspeed*dt*2; driftActive=true; driftHold=0.5;
  } else if(driftActive){
    driftHold-=dt;
    if(driftHold<=0){ driftScore+=Math.floor(driftCombo); driftActive=false; driftCombo=0; }
  }
  if((inp.boost&&player.boost>0)||player.cbBurst>0){
    for(var bf=0;bf<2;bf++) addParticle(rearX+(Math.random()-0.5)*0.6,0.45,rearZ+(Math.random()-0.5)*0.6,
      (player.cbBurst>0?0x7fe0ff:0xff7a2a),0.18+Math.random()*0.12,-hx*4+(Math.random()-0.5)*2,0.5,-hz*4+(Math.random()-0.5)*2,0.3);
  }
  }
  if(onDutyMode && !onFoot && player && player.group){
    var dxs=player.group.position.x-stationPos.x, dzs=player.group.position.z-stationPos.z, ds=Math.hypot(dxs,dzs);
    if(ds>stationR+10) leftStation=true;
    if(leftStation && ds<stationR && Math.abs(player.speed)<7){ exitToStation(); }
  }
  if(!onFoot) updateAI(dt);
  updateTraffic(dt);
  for(var ti=0;ti<traffic.length;ti++){
    var cc=traffic[ti];
    if(!chopperMode && !onFoot) carImpact(player,cc);
    for(var ai=0;ai<agents.length;ai++) carImpact(agents[ai],cc);
  }
  for(var ca=0;ca<traffic.length;ca++) for(var cb=ca+1;cb<traffic.length;cb++) carImpact(traffic[ca],traffic[cb]);

  if(cashTruck){
    updateCashTruck(dt);
    if(!chopperMode && !onFoot) carImpact(player,cashTruck); for(var ag=0;ag<agents.length;ag++) carImpact(agents[ag],cashTruck); hitHazards(cashTruck);
    if(mode==="heist" && !hijacked){
      var Rp=theRobber.group.position, Cp=cashTruck.group.position, dC=Math.hypot(Rp.x-Cp.x,Rp.z-Cp.z);
      if(dC<4.7){ hijackMeter=Math.min(100, hijackMeter+(dC<3?40:24)*dt); if(Math.random()<0.3) sparks(Cp.x,0.8,Cp.z); if(hijackMeter>=100) doHijack(); }
      else hijackMeter=Math.max(0, hijackMeter-14*dt);
    }
  }

  // loot
  var t=clock.elapsedTime;
  for(var i=0;i<loot.length;i++){
    var L=loot[i]; if(L.taken) continue;
    L.mesh.rotation.y=t*1.6+L.spin; L.mesh.position.y=0.6+Math.sin(t*2+L.spin)*0.15;
    if(!chopperMode && !onFoot && Math.hypot(player.group.position.x-L.x, player.group.position.z-L.z)<2.4){
      L.taken=true; L.mesh.visible=false; score+=150; player.boost=100;
      beep(1040,0.08); beep(1320,0.08);
    }
  }
  // power-ups
  for(var u=0;u<powerups.length;u++){
    var U=powerups[u];
    if(U.taken){ U.respawn-=dt; if(U.respawn<=0){ U.taken=false; U.mesh.visible=true; } continue; }
    U.mesh.rotation.y=t*1.8+U.spin; U.mesh.position.y=0.85+Math.sin(t*2.4+U.spin)*0.18;
    if(!chopperMode && !onFoot && Math.hypot(player.group.position.x-U.x, player.group.position.z-U.z)<2.4){
      applyPowerup(U.type); U.taken=true; U.mesh.visible=false; U.respawn=12;
    }
  }
  // road hazards affect every car
  if(!chopperMode && !onFoot) hitHazards(player);
  for(var hg=0;hg<agents.length;hg++) hitHazards(agents[hg]);
  for(var ht=0;ht<traffic.length;ht++) hitHazards(traffic[ht]);
  // shield bubble
  if(player.shield>0){ player.shield-=dt; if(shieldBubble){ shieldBubble.visible=true; shieldBubble.position.set(player.group.position.x,0.8,player.group.position.z); shieldBubble.material.opacity=0.12+0.07*Math.sin(t*9); } }
  else if(shieldBubble) shieldBubble.visible=false;
  // smoke screens
  for(var smz=smokeZones.length-1;smz>=0;smz--){
    var Z=smokeZones[smz]; Z.life+=dt;
    if(Math.random()<0.9){ var aa=Math.random()*6.283, rr=Math.random()*Z.r; smoke(Z.x+Math.cos(aa)*rr,0.4,Z.z+Math.sin(aa)*rr); }
    var cars=[player].concat(agents);
    for(var ic=0;ic<cars.length;ic++){ var ce=cars[ic];
      if(Math.hypot(ce.group.position.x-Z.x,ce.group.position.z-Z.z)<Z.r){ ce.oiled=Math.max(ce.oiled,0.3); ce.speed*=0.97; } }
    if(Z.life>=Z.max) smokeZones.splice(smz,1);
  }
  // objectives + heat
  if(objectives.length && objIndex<objectives.length){
    var tgt=objectives[objIndex];
    tgt.marker.rotation.y+=dt*1.3;
    if(tgt.marker.userData.ring) tgt.marker.userData.ring.material.emissiveIntensity=0.6+0.4*Math.sin(t*5);
    var dd=Math.hypot(theRobber.group.position.x-tgt.x, theRobber.group.position.z-tgt.z);
    var blockSafe=(mode==="heist" && tgt.safe && !hijacked);
    if(!onFoot && !blockSafe && dd<(tgt.safe?5.5:6.5)){
      tgt.reached=true; tgt.marker.visible=false; objIndex++;
      beep(880,0.1); setTimeout(function(){beep(1180,0.12);},90);
      if(tgt.safe){
        if(role==="robber") endGame(true,"You reached the safehouse!","escaped");
        else endGame(false,"The robber reached the safehouse.","escaped");
        return;
      } else {
        heat=Math.min(100,heat+15); updateObjectiveMarkers();
        if(mode==="heistrun" && role==="robber"){
          var mult=1+heistStreak*0.5, reward=Math.round(500*mult);
          score+=reward; heistStreak++;
          if(elStreak){ elStreak.textContent="Streak x"+mult.toFixed(1)+"  (+$"+reward+")"; elStreak.classList.remove("pop"); void elStreak.offsetWidth; elStreak.classList.add("pop"); }
          if(elAlert){ elAlert.textContent="Heist "+heistStreak+" — streak x"+mult.toFixed(1); elAlert.classList.add("show"); setTimeout(function(){elAlert.classList.remove("show");},1700); }
        }
      }
    }
  }
  heat=Math.min(100, heat+HEAT_RATE*dt);
  var want=Math.floor(heat/25);
  while(heatSpawned<want){ if(!spawnBackupCop()) break; heatSpawned++; }
  updateChopper(dt);
  copDeployTimer-=dt;
  if(heat>35 && copDeployTimer<=0){
    if(heat>55 && Math.random()<0.45) spawnRoadblock(); else deployCopSpikes();
    copDeployTimer=Math.max(3.5, rand(8,12)-heat*0.05);
  }
  if(crew){
    driveCrewAI(crew,dt);
    carImpact(player,crew); for(var cw=0;cw<agents.length;cw++) carImpact(agents[cw],crew); hitHazards(crew);
  }
  if(crossChan>0) crossingUpdate(dt);
  if(map==="custom"){ updateCustomDraws(dt); customWaterUpdate(dt); updateCustomTrain(dt); updateLiftSpans(dt); }
  if(drawbridge) updateDrawbridge(dt);
  if(thirdDraw) updateThirdDrawbridge(dt);
  if(train) updateTrain(dt);
  updateSkids(dt);
  updateProps(dt);
  updateRoadblocks(dt);
  for(var hl=hazards.length-1;hl>=0;hl--){ var HZ=hazards[hl]; if(HZ.life!=null){ HZ.life-=dt; if(HZ.life<=0){ disposeDeep(HZ.group); scene.remove(HZ.group); hazards.splice(hl,1); } } }
  // beacons + nearest cop
  var nearest=Infinity, flash=(Math.floor(t*3)%2===0);
  for(var c=0;c<agents.length;c++){
    var a=agents[c];
    if(a.group.userData.bL){
      a.group.userData.bL.material.emissiveIntensity=flash?1.6:0.2;
      a.group.userData.rL.material.emissiveIntensity=flash?0.2:1.6;
      a.group.userData.plB.intensity=flash?1.8:0.2;
      a.group.userData.plR.intensity=flash?0.2:1.8;
    }
    var d=Math.hypot(player.group.position.x-a.group.position.x, player.group.position.z-a.group.position.z);
    if(d<nearest) nearest=d;
  }
  // physics impacts between the chase cars; robber's car wrecks when damage maxes out
  var rcops=(role==="robber"||chopperMode)?agents:(onFoot?[]:[player]);
  var rpos=theRobber.group.position;
  for(var ci=0;ci<rcops.length;ci++){ if(rcops[ci]!==theRobber) carImpact(rcops[ci], theRobber); }
  if(damageOn){
    theRobber.health=Math.max(0,100-theRobber.dmg);
    if(theRobber.dmg>=100){
      explode(rpos.x,0.9,rpos.z); theRobber.group.visible=false;
      if(role==="cop") endGame(true,"You wrecked the robber's car with "+formatTime(timeLeft)+" left.","busted");
      else endGame(false,"Your car was wrecked. Banked $"+score+".","busted");
      return;
    }
  } else theRobber.health=100;
  if(damageOn && theRobber.health<35 && Math.random()<0.45) smoke(rpos.x,0.9,rpos.z);
  // siren / alert
  if(role==="robber"){
    setSiren(nearest<14?(1-nearest/14):0);   // robber: louder as cops close in
    var lit=(chopper&&chopper.lit);
    if(lit) elAlert.textContent="In the spotlight!";
    else if(nearest<8) elAlert.textContent="Cops closing in!";
    elAlert.classList.toggle("show", lit || nearest<8);
  } else {
    setSiren(0.6);                            // cop: your own lights always on
  }
  // timer
  if(!onFoot) timeLeft-=dt;
  if(timeLeft<=0){
    timeLeft=0;
    if(role==="cop") endGame(true,"Time ran out — the robber never reached the safehouse.","busted");
    else endGame(false,"You ran out of time before reaching the safehouse.","busted");
    return;
  }
  updateCamera(dt);
  updateHUD();
}

function updateCamera(dt){
  if(isTouch && (lookVec.x||lookVec.z)){
    camTargetYaw  -= lookVec.x*0.045*settings.camSens;
    camTargetPitch = Math.max(0.18,Math.min(1.3, camTargetPitch - lookVec.z*0.03*settings.camSens*(settings.invertY?-1:1)));
  }
  // smooth toward targets
  var k=Math.min(1,8*dt);
  camYaw+=angleDiff(camYaw,camTargetYaw)*k;
  camPitch+=(camTargetPitch-camPitch)*k;
  camDist+=(camTargetDist-camDist)*k;

  var p=player.group.position;
  var camHeading=player.heading+camYaw;       // yaw offset relative to car heading
  var hor=camDist*Math.cos(camPitch), hgt=camDist*Math.sin(camPitch);
  var cx=p.x - Math.sin(camHeading)*hor;
  var cz=p.z - Math.cos(camHeading)*hor;
  if(player.isHeli){
    camera.position.set(cx, p.y + Math.max(4,hgt*0.45), cz);
    camera.lookAt(p.x, p.y-15, p.z);
  } else if(player.isHuman){
    camera.position.set(cx, Math.max(2.5, p.y+hgt), cz);
    camera.lookAt(p.x, p.y+1.1, p.z);
  } else {
    camera.position.set(cx, Math.max(2.5,hgt), cz);
    camera.lookAt(p.x, 1.2, p.z);
  }
}

function formatTime(s){ s=Math.max(0,Math.ceil(s)); var m=Math.floor(s/60), ss=s%60; return m+":"+(ss<10?"0":"")+ss; }

function updateHUD(){
  elTimer.textContent=formatTime(timeLeft);
  elTimer.className="timer"+(timeLeft<=15?" crit":(timeLeft<=60?" warn":""));
  var pct=player.boost; elBoost.style.width=pct+"%"; elBoost.className="boost"+(pct<25?" low":"");
  elSpeed.textContent=Math.round(Math.abs(player.speed)*3.0); // arbitrary MPH-ish scale
  if(role==="robber") elScore.textContent="$"+score;
  if(chopperMode || (player&&player.isHeli)){ elHealth.style.width=pinMeter.toFixed(0)+"%"; elHealth.className="health"+(player&&player.lit?" crit":""); }
  else if(theRobber){ var hp=damageOn?Math.max(0,theRobber.health):100; elHealth.style.width=hp+"%"; elHealth.className="health"+(damageOn&&hp<35?" crit":""); }
  if(elHeat) elHeat.style.width=heat+"%";
  if(mode==="heist" && theRobber){
    if(!hijacked && cashTruck){
      var dc=Math.round(Math.hypot(theRobber.group.position.x-cashTruck.group.position.x, theRobber.group.position.z-cashTruck.group.position.z));
      if(role==="robber") elObjective.textContent="Hijack the cash truck — "+dc+"m"+(hijackMeter>0?"  ["+Math.floor(hijackMeter)+"%]":"");
      else elObjective.textContent="Stop the robber reaching the cash truck";
    } else {
      var os=objectives[objIndex], dd2=os?Math.round(Math.hypot(theRobber.group.position.x-os.x, theRobber.group.position.z-os.z)):0;
      if(role==="robber") elObjective.textContent="Escape with the cash — "+dd2+"m";
      else elObjective.textContent="Wreck the robber before the safehouse!";
    }
  } else if(objectives.length && theRobber){
    var ot=objectives[objIndex];
    if(!ot) elObjective.textContent="";
    else {
      var od=Math.round(Math.hypot(theRobber.group.position.x-ot.x, theRobber.group.position.z-ot.z));
      if(role==="robber") elObjective.textContent=(ot.safe?"Reach the safehouse":"Getaway point "+(objIndex+1)+"/"+(objectives.length-1))+" — "+od+"m";
      else elObjective.textContent="Wreck the robber — "+(ot.safe?"heading to safehouse!":"point "+(objIndex+1)+"/"+(objectives.length-1));
    }
  }
  var gl=GADGETS[gadget].label;
  if(cbCooldown>0){ elBreaker.textContent=gl+" "+Math.ceil(cbCooldown)+"s"; elBreaker.className="breaker cooling"; }
  else { elBreaker.textContent=(isTouch?"":"Space — ")+gl; elBreaker.className="breaker ready"; }
  if(driftActive && driftCombo>50){ elDrift.textContent="Drift +"+Math.floor(driftCombo); elDrift.className="drift on"; }
  else elDrift.className="drift";
  var goB=document.getElementById("getOutBtn"), giB=document.getElementById("getInBtn");
  if(onDutyMode){
    if(goB) goB.classList.toggle("hidden", !(!onFoot && player && !player.isHeli));
    if(giB){ var canGetIn=(onFoot && nearGarageIdx>=0); giB.classList.toggle("hidden", !canGetIn);
      if(canGetIn){ var gcar=garageCars[nearGarageIdx]; giB.textContent="🚗 Get In — "+((STYLES[gcar.style]||{}).label||gcar.style); } }
  } else { if(goB) goB.classList.add("hidden"); if(giB) giB.classList.add("hidden"); }
  drawTach();
}
function drawTach(){
  var W=tachCanvas.width, H=tachCanvas.height, cx=W/2, cy=H-8, R=H-20;
  var a0=Math.PI*1.2, span=Math.PI*0.6;
  tachCtx.clearRect(0,0,W,H);
  tachCtx.lineCap="round";
  tachCtx.lineWidth=7; tachCtx.strokeStyle="rgba(230,238,250,0.12)";
  tachCtx.beginPath(); tachCtx.arc(cx,cy,R,a0,a0+span,false); tachCtx.stroke();
  var f=Math.min(1, Math.abs(player?player.speed:0)/(P_MAXSPEED*1.5));
  tachCtx.strokeStyle = f>0.8?"#ff3b4e":(f>0.5?"#ffb020":"#2f6bff");
  tachCtx.beginPath(); tachCtx.arc(cx,cy,R,a0,a0+span*f,false); tachCtx.stroke();
  var na=a0+span*f;
  tachCtx.strokeStyle="#eef2f7"; tachCtx.lineWidth=2.5;
  tachCtx.beginPath(); tachCtx.moveTo(cx,cy); tachCtx.lineTo(cx+Math.cos(na)*R*0.9, cy+Math.sin(na)*R*0.9); tachCtx.stroke();
  tachCtx.fillStyle="#eef2f7"; tachCtx.beginPath(); tachCtx.arc(cx,cy,3.5,0,6.2832); tachCtx.fill();
}

// ----------------------------------------------------------------------------
//  Minimap
// ----------------------------------------------------------------------------
function drawMinimap(){
  var W=miniCanvas.width, H=miniCanvas.height;
  miniCtx.clearRect(0,0,W,H);
  function toMap(x,z){ return {x:(x+CITY)/(CITY*2)*W, y:(z+CITY)/(CITY*2)*H}; }
  miniCtx.fillStyle="rgba(120,140,180,0.18)";
  for(var i=0;i<buildings.length;i++){
    var b=buildings[i], a=toMap(b.x-b.w/2,b.z-b.d/2);
    miniCtx.fillRect(a.x,a.y,b.w/(CITY*2)*W,b.d/(CITY*2)*H);
  }
  for(var rr=0;rr<ramps.length;rr++){ var r=ramps[rr], mp=toMap(r.x,r.z);
    miniCtx.strokeStyle="#ffb020"; miniCtx.lineWidth=2; miniCtx.beginPath();
    miniCtx.moveTo(mp.x-Math.sin(r.yaw)*3.2, mp.y-Math.cos(r.yaw)*3.2);
    miniCtx.lineTo(mp.x+Math.sin(r.yaw)*3.2, mp.y+Math.cos(r.yaw)*3.2); miniCtx.stroke();
  }
  if(state==="playing"){
    for(var dk=0;dk<decks.length;dk++){ var D=decks[dk], a=toMap(D.x-D.w/2,D.z-D.d/2), b=toMap(D.x+D.w/2,D.z+D.d/2);
      miniCtx.fillStyle="rgba(150,165,185,0.5)"; miniCtx.fillRect(Math.min(a.x,b.x),Math.min(a.y,b.y),Math.abs(b.x-a.x),Math.abs(b.y-a.y)); }
    if(objectives.length && objIndex<objectives.length){ var ob=objectives[objIndex], obp=toMap(ob.x,ob.z);
      miniCtx.fillStyle=ob.safe?"#46e07a":"#ffb020"; dot(obp.x,obp.y,4.2);
      miniCtx.strokeStyle=ob.safe?"#46e07a":"#ffb020"; miniCtx.lineWidth=1.5; miniCtx.beginPath(); miniCtx.arc(obp.x,obp.y,7,0,6.283); miniCtx.stroke(); }
    for(var hz=0;hz<hazards.length;hz++){ var hzp=toMap(hazards[hz].x,hazards[hz].z);
      miniCtx.fillStyle=hazards[hz].type==="spike"?"#c8ccd2":"#11131a"; miniCtx.fillRect(hzp.x-2,hzp.y-2,4,4); }
    for(var pw=0;pw<powerups.length;pw++){ if(powerups[pw].taken) continue; var pwp=toMap(powerups[pw].x,powerups[pw].z);
      miniCtx.fillStyle="#"+PU_COL[powerups[pw].type].toString(16).padStart(6,"0"); dot(pwp.x,pwp.y,2.7); }
    for(var tk=0;tk<traffic.length;tk++){ var tp=toMap(traffic[tk].group.position.x,traffic[tk].group.position.z);
      miniCtx.fillStyle="#7e8a9c"; dot(tp.x,tp.y,2.4); }
    for(var l=0;l<loot.length;l++){ if(loot[l].taken) continue; var lp=toMap(loot[l].x,loot[l].z);
      miniCtx.fillStyle="#ffb020"; miniCtx.fillRect(lp.x-1.5,lp.y-1.5,3,3); }
    for(var rbk=0;rbk<roadblocks.length;rbk++){ var BRS=roadblocks[rbk].bars; for(var bb=0;bb<BRS.length;bb++){ if(BRS[bb].broken) continue; var bp=toMap(BRS[bb].x,BRS[bb].z); miniCtx.fillStyle="#f03030"; miniCtx.fillRect(bp.x-1.6,bp.y-1.6,3.2,3.2); } }
    if(chopper && chopper.active){
      var sp=toMap(chopper.tx,chopper.tz); miniCtx.strokeStyle=chopper.lit?"#ffd24a":"rgba(255,233,150,0.55)"; miniCtx.lineWidth=1.2; miniCtx.beginPath(); miniCtx.arc(sp.x,sp.y,POOL_R/(CITY*2)*W,0,6.283); miniCtx.stroke();
      var hpc=toMap(chopper.group.position.x,chopper.group.position.z); miniCtx.fillStyle="#fff2cf"; dot(hpc.x,hpc.y,3.0); miniCtx.strokeStyle="#fff2cf"; miniCtx.lineWidth=1; miniCtx.beginPath(); miniCtx.arc(hpc.x,hpc.y,5.5,0,6.283); miniCtx.stroke();
    }
    if(mode==="heist" && cashTruck && !hijacked){ var ctp=toMap(cashTruck.group.position.x,cashTruck.group.position.z);
      miniCtx.fillStyle="#ffd24a"; dot(ctp.x,ctp.y,4.5); miniCtx.strokeStyle="#ffd24a"; miniCtx.lineWidth=1.5; miniCtx.beginPath(); miniCtx.arc(ctp.x,ctp.y,8,0,6.283); miniCtx.stroke(); }
    if(crew){ var cwp=toMap(crew.group.position.x,crew.group.position.z); miniCtx.fillStyle="#22d3a0"; dot(cwp.x,cwp.y,3.2); }
    for(var c=0;c<agents.length;c++){
      var ap=toMap(agents[c].group.position.x,agents[c].group.position.z);
      miniCtx.fillStyle=agents[c].role==="cop"?"#2f6bff":"#ff3b4e"; dot(ap.x,ap.y,3.4);
    }
    var pp=toMap(player.group.position.x,player.group.position.z);
    miniCtx.fillStyle="#9be15d"; dot(pp.x,pp.y,3.8);
  }
}
function dot(x,y,r){ miniCtx.beginPath(); miniCtx.arc(x,y,r,0,Math.PI*2); miniCtx.fill(); }

// ----------------------------------------------------------------------------
//  Loop
// ----------------------------------------------------------------------------
function loop(){
  requestAnimationFrame(loop);
  var dt=Math.min(clock.getDelta(),0.05);
  if(state==="menu") return;              // the last match is fully hidden behind the menu — don't keep simulating/rendering it
  update(dt);
  drawMinimap();
  renderer.render(scene,camera);
}

// ----------------------------------------------------------------------------
//  UI wiring
// ----------------------------------------------------------------------------
var QNOTE={low:"Low — shadows off, lighter for older devices", medium:"Medium — soft shadows, balanced", high:"High — full shadows & resolution"};
function syncSettingsUI(){
  var vol=document.getElementById("volRange"); if(vol){ vol.value=Math.round(settings.volume*100); document.getElementById("volVal").textContent=vol.value+"%"; }
  var sens=document.getElementById("sensRange"); if(sens){ sens.value=Math.round(settings.camSens*100); document.getElementById("sensVal").textContent=settings.camSens.toFixed(1)+"\u00d7"; }
  var invEls=document.querySelectorAll("#invertSeg button"); Array.prototype.forEach.call(invEls,function(b){ b.classList.toggle("sel",(b.getAttribute("data-inv")==="on")===settings.invertY); });
  var qEls=document.querySelectorAll("#qualitySeg button"); Array.prototype.forEach.call(qEls,function(b){ b.classList.toggle("sel",b.getAttribute("data-q")===settings.quality); });
  var qn=document.getElementById("qualityNote"); if(qn) qn.textContent=QNOTE[settings.quality];
}
function wireSettings(){
  var vol=document.getElementById("volRange"), volV=document.getElementById("volVal");
  vol.addEventListener("input",function(){ settings.volume=vol.value/100; volV.textContent=vol.value+"%"; if(masterGain) masterGain.gain.value=settings.volume; });
  vol.addEventListener("change",persist);
  var sens=document.getElementById("sensRange"), sensV=document.getElementById("sensVal");
  sens.addEventListener("input",function(){ settings.camSens=sens.value/100; sensV.textContent=settings.camSens.toFixed(1)+"\u00d7"; });
  sens.addEventListener("change",persist);
  var invEls=document.querySelectorAll("#invertSeg button");
  Array.prototype.forEach.call(invEls,function(el){ el.addEventListener("click",function(){ Array.prototype.forEach.call(invEls,function(b){b.classList.remove("sel");}); el.classList.add("sel"); settings.invertY=(el.getAttribute("data-inv")==="on"); persist(); }); });
  var qEls=document.querySelectorAll("#qualitySeg button"), qn=document.getElementById("qualityNote");
  Array.prototype.forEach.call(qEls,function(el){ el.addEventListener("click",function(){ Array.prototype.forEach.call(qEls,function(b){b.classList.remove("sel");}); el.classList.add("sel"); settings.quality=el.getAttribute("data-q"); if(qn) qn.textContent=QNOTE[settings.quality]; applyQuality(settings.quality); persist(); beep(520,0.06); }); });
  document.getElementById("settingsBtn").addEventListener("click",openSettings);
  document.getElementById("setDone").addEventListener("click",closeSettings);
  initBuilder();
}
function openSettings(){ syncSettingsUI(); document.getElementById("menu").classList.add("hidden"); document.getElementById("settings").classList.remove("hidden"); }
function closeSettings(){ document.getElementById("settings").classList.add("hidden"); document.getElementById("menu").classList.remove("hidden"); }

// ---- World Builder: two levels. World = grid of big plates; tap into a plate to fill it with details ----
var B_KEY="nightpatrol_worlds_v3", PW=72, IW=6;   // 72x72 plate grid (pan/zoom), each plate 6x6 interior
var PLATES=[ {k:"water",label:"Water",color:"#1c5f86"},
  {k:"city",label:"City",color:"#3a4250"},
  {k:"plane",label:"Plains",color:"#3f6b34"},
  {k:"desert",label:"Desert",color:"#c79b5e"},
  {k:"station",label:"Police HQ",color:"#2b6fd6"},
  {k:"bridge",label:"Bridge",color:"#8a93a0"},
  {k:"drawbridge",label:"Drawbridge",color:"#c8a83a"},
  {k:"snow",label:"Snow",color:"#dce6f0"},
  {k:"harbor",label:"Harbor",color:"#2e3a44"} ];
var FEATS=[ {label:"Road",color:"#20242c"},{label:"Building",color:"#5a6472"},{label:"Tree",color:"#2f6d2a"},
  {label:"Rock",color:"#8a6a44"},{label:"Desert Rock",color:"#9c6b3a"},{label:"Railroad",color:"#6a5436"},{label:"Avenue",color:"#2a2e35"},
  {label:"Container",color:"#c0522a"},{label:"Barrier",color:"#d8d4c8"},
  {label:"Bridge Deck",color:"#8a93a0"},{label:"Lift Span",color:"#c8a83a"} ];
var world=new Uint8Array(PW*PW), interiors={}, irot={};
var bView="world", bPlateIdx=-1, bTerr=2, bFeat=1, bEditMode=false, bSlot=0, bLastSi=-1, bBuilderBase=null;
function bReservedZone(){ var mid=Math.floor(PW/2);
  if(bBuilderBase==="mega") return {x0:mid-7,x1:mid+6,y0:mid-7,y1:mid+6};
  if(bBuilderBase==="city") return {x0:mid-1,x1:mid+1,y0:mid-1,y1:mid+1};
  return null; }
function bInReserved(px,py){ var z=bReservedZone(); return z && px>=z.x0 && px<=z.x1 && py>=z.y0 && py<=z.y1; }
var bWZoom=4, bWPanX=0, bWPanY=0, bPtrs={}, bPinchD=0, bLastMid=null;
var bCanvas=null, bCtx=null, bAnim=null, bDownX=0, bDownY=0, bMoved=false;
function bInterior(idx){ if(!interiors[idx]) interiors[idx]=new Uint8Array(IW*IW); return interiors[idx]; }
function bInteriorRot(idx){ if(!irot[idx]) irot[idx]=new Uint8Array(IW*IW); return irot[idx]; }
function bInterEmpty(idx){ var a=interiors[idx]; if(!a) return true; for(var i=0;i<a.length;i++) if(a[i]) return false; return true; }
function bLoadWorlds(){ try{ return JSON.parse(localStorage.getItem(B_KEY))||[]; }catch(e){ return []; } }
function bSaveWorlds(a){ try{ localStorage.setItem(B_KEY, JSON.stringify(a)); }catch(e){} }
function bArrStr(a){ var s=""; for(var i=0;i<a.length;i++) s+=a[i]; return s; }
function bStrArr(str,len){ var a=new Uint8Array(len); if(str) for(var i=0;i<len&&i<str.length;i++) a[i]=parseInt(str.charAt(i),10)||0; return a; }
function bGlyph(x,y,cp,fc,r){ bCtx.save(); bCtx.translate(x+cp/2,y+cp/2); bCtx.rotate((r||0)*Math.PI/2);
  if(fc===1){ bCtx.fillStyle="#3b4049"; bCtx.fillRect(-cp/2-0.3,-cp/2-0.3,cp+0.6,cp+0.6); bCtx.fillStyle="#e6cf5a"; bCtx.fillRect(-cp*0.05,-cp*0.34,cp*0.1,cp*0.68); }
  else if(fc===7){ bCtx.fillStyle="#2e333c"; bCtx.fillRect(-cp/2-0.3,-cp/2-0.3,cp+0.6,cp+0.6); bCtx.fillStyle="#e6cf5a"; bCtx.fillRect(-cp*0.16,-cp/2-0.3,cp*0.07,cp+0.6); bCtx.fillRect(cp*0.09,-cp/2-0.3,cp*0.07,cp+0.6); }
  else if(fc===6){ bCtx.fillStyle="#3a2f22"; bCtx.fillRect(-cp/2-0.3,-cp/2-0.3,cp+0.6,cp+0.6); bCtx.strokeStyle="#9a7c4c"; bCtx.lineWidth=Math.max(1,cp*0.1);
    bCtx.beginPath(); bCtx.moveTo(-cp*0.18,-cp/2); bCtx.lineTo(-cp*0.18,cp/2); bCtx.moveTo(cp*0.18,-cp/2); bCtx.lineTo(cp*0.18,cp/2); bCtx.stroke(); }
  else if(fc===2){ bCtx.fillStyle="#5a6472"; bCtx.fillRect(-cp*0.2,-cp*0.34,cp*0.4,cp*0.68); }
  else if(fc===5){ bCtx.fillStyle="#9c6b3a"; bCtx.fillRect(-cp*0.32,-cp*0.32,cp*0.64,cp*0.64); }
  else if(fc===8){ bCtx.fillStyle="#c0522a"; bCtx.fillRect(-cp*0.3,-cp*0.22,cp*0.6,cp*0.44); bCtx.fillStyle="#8f3d1e"; bCtx.fillRect(-cp*0.3,-cp*0.02,cp*0.6,cp*0.06); }
  else if(fc===9){ bCtx.fillStyle="#d8d4c8"; bCtx.fillRect(-cp*0.36,-cp*0.1,cp*0.72,cp*0.2); bCtx.fillStyle="#c0392b"; for(var bs=-2;bs<=2;bs++) bCtx.fillRect(bs*cp*0.14-cp*0.03,-cp*0.1,cp*0.06,cp*0.2); }
  else if(fc===10){ bCtx.fillStyle="#5a6472"; bCtx.fillRect(-cp/2-0.3,-cp*0.3,cp+0.6,cp*0.6); bCtx.fillStyle="#dfe3e8"; bCtx.fillRect(-cp/2-0.3,-cp*0.34,cp+0.6,cp*0.07); bCtx.fillRect(-cp/2-0.3,cp*0.27,cp+0.6,cp*0.07); }
  else if(fc===11){ bCtx.fillStyle="#c8a83a"; bCtx.fillRect(-cp/2-0.3,-cp*0.3,cp+0.6,cp*0.6); bCtx.fillStyle="#8a6a1e"; bCtx.fillRect(-cp*0.08,-cp*0.3,cp*0.16,cp*0.6); }
  else { bCtx.fillStyle={3:"#2f6d2a",4:"#8a6a44"}[fc]; bCtx.beginPath(); bCtx.arc(0,0,cp*0.32,0,6.283); bCtx.fill(); }
  bCtx.restore(); }
function bWClamp(){ var cp=(bCanvas.width/PW)*bWZoom, wpx=PW*cp, m=Math.max(0,wpx-bCanvas.width); bWPanX=Math.max(0,Math.min(m,bWPanX)); bWPanY=Math.max(0,Math.min(m,bWPanY)); }
function bPinchDist(){ var k=Object.keys(bPtrs); if(k.length<2) return 0; var a=bPtrs[k[0]],b=bPtrs[k[1]]; return Math.hypot(a.x-b.x,a.y-b.y); }
function bPinchMid(){ var k=Object.keys(bPtrs); var a=bPtrs[k[0]],b=bPtrs[k[1]]; return {x:(a.x+b.x)/2,y:(a.y+b.y)/2}; }
function bWZoomAt(z,cxp,cyp){ if(!bCanvas) return; var W=bCanvas.width, r=bCanvas.getBoundingClientRect(); var px=(cxp!=null)?((cxp-r.left)/r.width*W):W/2, py=(cyp!=null)?((cyp-r.top)/r.height*W):W/2; var old=(W/PW)*bWZoom, wx=px+bWPanX, wy=py+bWPanY; bWZoom=Math.max(1,Math.min(7,z)); var nc=(W/PW)*bWZoom; bWPanX=wx*(nc/old)-px; bWPanY=wy*(nc/old)-py; bWClamp(); bDraw(); }
function bDrawWorld(t){
  if(!bCtx) return; var W=bCanvas.width, cp=(W/PW)*bWZoom;
  bCtx.fillStyle="#0c0f17"; bCtx.fillRect(0,0,W,W);
  var c0=Math.max(0,Math.floor(bWPanX/cp)), c1=Math.min(PW,Math.ceil((bWPanX+W)/cp)), r0=Math.max(0,Math.floor(bWPanY/cp)), r1=Math.min(PW,Math.ceil((bWPanY+W)/cp));
  var rz=bReservedZone();
  if(rz){ var zx=rz.x0*cp-bWPanX, zy=rz.y0*cp-bWPanY, zw=(rz.x1-rz.x0+1)*cp, zh=(rz.y1-rz.y0+1)*cp;
    bCtx.save(); bCtx.beginPath(); bCtx.rect(zx,zy,zw,zh); bCtx.clip();
    bCtx.fillStyle="#182338"; bCtx.fillRect(zx,zy,zw,zh);
    bCtx.strokeStyle="rgba(120,160,230,0.35)"; bCtx.lineWidth=1;
    for(var hx=zx-zh; hx<zx+zw; hx+=10){ bCtx.beginPath(); bCtx.moveTo(hx,zy); bCtx.lineTo(hx+zh,zy+zh); bCtx.stroke(); }
    bCtx.restore();
    bCtx.strokeStyle="#4a7fd6"; bCtx.lineWidth=2; bCtx.setLineDash([6,4]); bCtx.strokeRect(zx,zy,zw,zh); bCtx.setLineDash([]);
    bCtx.fillStyle="#dbe6fb"; bCtx.font="bold "+Math.max(10,Math.min(16,zw*0.05))+"px sans-serif"; bCtx.textAlign="center";
    bCtx.fillText((bBuilderBase==="mega"?"MEGA WORLD":"CITY")+" — already built, tap to add on top", zx+zw/2, zy+16); }
  for(var ry=r0;ry<r1;ry++) for(var cx=c0;cx<c1;cx++){ var i=ry*PW+cx, code=world[i]; if(!code) continue; var x=cx*cp-bWPanX, y=ry*cp-bWPanY;
    if(bEditMode){ bCtx.save(); bCtx.translate(x+cp/2,y+cp/2); bCtx.rotate(t); var sgn=cp*0.6;
      bCtx.fillStyle=PLATES[code-1].color; bCtx.fillRect(-sgn/2,-sgn/2,sgn,sgn);
      if(!bInterEmpty(i)){ bCtx.fillStyle="rgba(255,255,255,.85)"; bCtx.fillRect(-sgn/2,-sgn/2,sgn,3); } bCtx.restore(); }
    else { bCtx.fillStyle=PLATES[code-1].color; bCtx.fillRect(x,y,cp+0.6,cp+0.6);
      if(!bInterEmpty(i)){ bCtx.fillStyle="rgba(255,255,255,.5)"; for(var d=0;d<3;d++) bCtx.fillRect(x+cp*0.2+d*cp*0.22, y+cp*0.78, cp*0.12, cp*0.12); } }
  }
  bCtx.strokeStyle="rgba(255,255,255,0.08)"; bCtx.lineWidth=1; bCtx.beginPath();
  for(var gx=c0;gx<=c1;gx++){ var X=gx*cp-bWPanX; bCtx.moveTo(X,0); bCtx.lineTo(X,W); }
  for(var gy=r0;gy<=r1;gy++){ var Y=gy*cp-bWPanY; bCtx.moveTo(0,Y); bCtx.lineTo(W,Y); } bCtx.stroke();
}
function bDrawPlate(){
  if(!bCtx) return; var cp=bCanvas.width/IW, W=bCanvas.width; var terr=world[bPlateIdx]||2;
  bCtx.fillStyle=PLATES[terr-1]?PLATES[terr-1].color:"#23303a"; bCtx.fillRect(0,0,W,W);
  bCtx.fillStyle="rgba(0,0,0,0.28)"; bCtx.fillRect(0,0,W,W);
  var inter=bInterior(bPlateIdx), rot=bInteriorRot(bPlateIdx);
  for(var sy=0;sy<IW;sy++) for(var sx=0;sx<IW;sx++){ var fc=inter[sy*IW+sx]; if(fc) bGlyph(sx*cp,sy*cp,cp,fc,rot[sy*IW+sx]); }
  bCtx.strokeStyle="rgba(255,255,255,0.14)"; bCtx.lineWidth=1; bCtx.beginPath();
  for(var g=0;g<=IW;g++){ var q=g*cp; bCtx.moveTo(q,0); bCtx.lineTo(q,W); bCtx.moveTo(0,q); bCtx.lineTo(W,q); } bCtx.stroke();
}
function bDraw(){ if(bView==="world") bDrawWorld(0); else bDrawPlate(); }
function bStartSpin(){ if(bAnim) return; function loop(){ if(bEditMode&&bView==="world"){ bDrawWorld((performance.now()/650)%6.283); bAnim=requestAnimationFrame(loop); } else bAnim=null; } bAnim=requestAnimationFrame(loop); }
function bStopSpin(){ if(bAnim){ cancelAnimationFrame(bAnim); bAnim=null; } }
function bUpdateTool(){ var t=document.getElementById("buildTool"); if(!t) return;
  if(bView==="plate"){ var terr=world[bPlateIdx]; var tn=terr?PLATES[terr-1].label:"plate";
    t.textContent="Inside the "+tn+" plate — "+(bFeat===-1?"Eraser, tap to remove":("tap to add "+FEATS[bFeat-1].label+", tap it again to rotate")); }
  else if(bEditMode) t.textContent="Tap a spinning plate to go inside and add buildings, trees, roads…";
  else if(bTerr===-1) t.textContent="Eraser — tap a placed plate to remove it entirely";
  else t.textContent="Plate: "+PLATES[bTerr-1].label+" — tap the map to place it";
  bRenderPalette(); }
function bRenderPalette(){ var p=document.getElementById("palette"); if(!p) return; p.innerHTML="";
  function mk(label,color,sel,fn){ var b=document.createElement("button"); b.className="pal-btn"+(sel?" sel":"");
    b.innerHTML=(color?'<span class="pal-sw" style="background:'+color+'"></span>':'')+label; b.onclick=fn; p.appendChild(b); }
  if(bView==="plate"){
    mk("Terrain: "+(PLATES[(world[bPlateIdx]||2)-1].label)+" ▸","", false, function(){
      world[bPlateIdx]=(((world[bPlateIdx]||2))%PLATES.length)+1; bDraw(); bRenderPalette(); });
    for(var j=0;j<FEATS.length;j++){ (function(idx){ mk(FEATS[idx].label,FEATS[idx].color, bFeat===idx+1, function(){ bFeat=idx+1; bUpdateTool(); }); })(j); }
    mk("Eraser","", bFeat===-1, function(){ bFeat=-1; bUpdateTool(); });
  } else {
    for(var i=0;i<PLATES.length;i++){ (function(idx){ mk(PLATES[idx].label,PLATES[idx].color, bTerr===idx+1, function(){ bTerr=idx+1; bUpdateTool(); }); })(i); }
    mk("Eraser","", bTerr===-1, function(){ bTerr=-1; bUpdateTool(); });
  }
}
function bSyncButtons(){ var be=document.getElementById("bEdit"), bb=document.getElementById("bBack"), br=document.getElementById("bRotate"), zo=document.getElementById("bWZoomOut"), zi=document.getElementById("bWZoomIn");
  if(bView==="plate"){ if(be) be.classList.add("hidden"); if(zo) zo.classList.add("hidden"); if(zi) zi.classList.add("hidden"); if(bb) bb.classList.remove("hidden"); if(br) br.classList.remove("hidden"); }
  else { if(bb) bb.classList.add("hidden"); if(br) br.classList.add("hidden"); if(zo) zo.classList.remove("hidden"); if(zi) zi.classList.remove("hidden"); if(be){ be.classList.remove("hidden"); be.classList.toggle("editing",bEditMode); be.textContent=bEditMode?"✎ Editing — tap a plate to enter":"✎ Edit plates"; } }
}
function bRenderSlots(){ var w=bLoadWorlds(), s=document.getElementById("slots"); if(!s) return; s.innerHTML="";
  for(var i=0;i<10;i++){ (function(idx){ var used=w[idx]&&w[idx].world; var b=document.createElement("button");
    b.className="slot-btn"+(used?" used":"")+(bSlot===idx?" sel":""); b.textContent=(idx+1);
    b.onclick=function(){ bSlot=idx; bRenderSlots(); }; s.appendChild(b); })(i); }
}
function bOpenPlate(i){ bView="plate"; bPlateIdx=i; bLastSi=-1; bStopSpin(); bRenderPalette(); bSyncButtons(); bUpdateTool(); bDraw(); }
function bBackToWorld(){ bView="world"; bPlateIdx=-1; bRenderPalette(); bSyncButtons(); bUpdateTool(); bDraw(); if(bEditMode) bStartSpin(); }
function bSetEdit(on){ bEditMode=on; bSyncButtons(); bUpdateTool(); if(on&&bView==="world") bStartSpin(); else { bStopSpin(); bDraw(); } }
function bTapCell(e){ var r=bCanvas.getBoundingClientRect(); var x=(e.clientX-r.left)/r.width*bCanvas.width, y=(e.clientY-r.top)/r.height*bCanvas.height;
  if(bView==="world"){ var cp=(bCanvas.width/PW)*bWZoom, px=Math.floor((x+bWPanX)/cp), py=Math.floor((y+bWPanY)/cp); if(px<0||py<0||px>=PW||py>=PW) return; var i=py*PW+px;
    if(bEditMode){ if(world[i]) bOpenPlate(i); else bFlash("Place a plate here first"); }
    else if(bTerr===-1){ world[i]=0; delete interiors[i]; delete irot[i]; bDraw(); }
    else { world[i]=bTerr; bDraw(); } }
  else { var cp2=bCanvas.width/IW, sx=Math.floor(x/cp2), sy=Math.floor(y/cp2); if(sx<0||sy<0||sx>=IW||sy>=IW) return; var inter=bInterior(bPlateIdx), rot=bInteriorRot(bPlateIdx); var si=sy*IW+sx;
    if(bFeat===-1){ inter[si]=0; rot[si]=0; bLastSi=-1; }
    else if(inter[si]===bFeat){ rot[si]=(rot[si]+1)%4; bLastSi=si; }   // tap the same detail again to rotate it
    else { inter[si]=bFeat; rot[si]=0; bLastSi=si; }
    bDraw(); }
}
function bFlash(msg){ var t=document.getElementById("buildTool"); if(t){ t.textContent=msg; setTimeout(bUpdateTool,1100); } }
function openBuilder(){ document.getElementById("menu").classList.add("hidden"); document.getElementById("builder").classList.remove("hidden");
  bView="world"; bPlateIdx=-1; bEditMode=false; bStopSpin(); bWZoom=4; if(bCanvas){ var cp=(bCanvas.width/PW)*bWZoom; bWPanX=(PW*cp-bCanvas.width)/2; bWPanY=(PW*cp-bCanvas.width)/2; bWClamp(); } bRenderPalette(); bRenderSlots(); bSyncButtons(); bUpdateTool(); bDraw(); }
function closeBuilder(){ bStopSpin(); document.getElementById("builder").classList.add("hidden"); document.getElementById("menu").classList.remove("hidden"); }
function initBuilder(){
  bCanvas=document.getElementById("buildCanvas"); if(!bCanvas) return; bCtx=bCanvas.getContext("2d");
  bCanvas.addEventListener("pointerdown",function(e){ bPtrs[e.pointerId]={x:e.clientX,y:e.clientY}; try{bCanvas.setPointerCapture(e.pointerId);}catch(x){} var n=Object.keys(bPtrs).length; if(n===1){ bDownX=e.clientX; bDownY=e.clientY; bMoved=false; } else if(n===2){ bPinchD=bPinchDist(); bLastMid=bPinchMid(); } });
  bCanvas.addEventListener("pointermove",function(e){ if(!bPtrs[e.pointerId]) return; var prev=bPtrs[e.pointerId]; bPtrs[e.pointerId]={x:e.clientX,y:e.clientY}; var n=Object.keys(bPtrs).length, r=bCanvas.getBoundingClientRect(), sc=bCanvas.width/r.width;
    if(n>=2 && bView==="world"){ var d=bPinchDist(), m=bPinchMid(); if(bLastMid){ bWPanX-=(m.x-bLastMid.x)*sc; bWPanY-=(m.y-bLastMid.y)*sc; } if(bPinchD>0){ bWZoomAt(bWZoom*(d/bPinchD), m.x, m.y); } else { bWClamp(); bDraw(); } bPinchD=d; bLastMid=m; bMoved=true; return; }
    if(Math.abs(e.clientX-bDownX)+Math.abs(e.clientY-bDownY)>8) bMoved=true;
    if(e.pointerType==="mouse" && bMoved && bView==="world"){ bWPanX-=(e.clientX-prev.x)*sc; bWPanY-=(e.clientY-prev.y)*sc; bWClamp(); bDraw(); } });
  function bEndPtr(e){ if(bPtrs[e.pointerId]){ if(Object.keys(bPtrs).length===1 && !bMoved) bTapCell(e); delete bPtrs[e.pointerId]; } if(Object.keys(bPtrs).length<2){ bPinchD=0; bLastMid=null; } }
  bCanvas.addEventListener("pointerup",bEndPtr); bCanvas.addEventListener("pointercancel",bEndPtr);
  document.getElementById("bWZoomOut").onclick=function(){ bWZoomAt(bWZoom/1.4); };
  document.getElementById("bWZoomIn").onclick=function(){ bWZoomAt(bWZoom*1.4); };
  document.getElementById("bEdit").onclick=function(){ bSetEdit(!bEditMode); };
  document.getElementById("bBack").onclick=bBackToWorld;
  document.getElementById("bRotate").onclick=function(){ if(bView!=="plate"){ return; } var inter=bInterior(bPlateIdx), rot=bInteriorRot(bPlateIdx); if(bLastSi>=0 && inter[bLastSi]){ rot[bLastSi]=(rot[bLastSi]+1)%4; bDraw(); } else bFlash("Place a detail first, then Rotate"); };
  document.getElementById("bSave").onclick=function(){ var w=bLoadWorlds(); var inter={}, rots={}; for(var k in interiors){ if(!bInterEmpty(k)){ inter[k]=bArrStr(interiors[k]); rots[k]=bArrStr(bInteriorRot(k)); } }
    w[bSlot]={name:"World "+(bSlot+1),world:bArrStr(world),interiors:inter,rot:rots,base:bBuilderBase}; bSaveWorlds(w); bRenderSlots(); bFlash("Saved World "+(bSlot+1)); };
  document.getElementById("bLoad").onclick=function(){ var w=bLoadWorlds(), d=w[bSlot]; if(d&&d.world){ world=bStrArr(d.world,PW*PW); interiors={}; irot={}; if(d.interiors) for(var k in d.interiors){ interiors[k]=bStrArr(d.interiors[k],IW*IW); irot[k]=bStrArr(d.rot?d.rot[k]:"",IW*IW); } bBuilderBase=d.base||null; bView="world"; bPlateIdx=-1; bSyncButtons(); bDraw(); bUpdateTool(); bFlash("Loaded World "+(bSlot+1)); } else bFlash("World "+(bSlot+1)+" is empty"); };
  function fillCityInterior(px,py){ var idx=py*PW+px, arr=bInterior(idx);                 // dense city grid — towers between the streets
  for(var sy=0;sy<IW;sy++) for(var sx=0;sx<IW;sx++){
    var isAve=(sx===0||sy===0), isRoad=(sx===3||sy===3);
    arr[sy*IW+sx]=isAve?7:(isRoad?1:2); } }
  function fillPlainInterior(px,py){ var idx=py*PW+px, arr=bInterior(idx);                // one road through, scattered trees — matches the real "plain" scatter density
  for(var sy=0;sy<IW;sy++) for(var sx=0;sx<IW;sx++){
    var isRoad=(sx===2||sx===3); arr[sy*IW+sx]=isRoad?1:(Math.random()<0.4?3:0); } }
  function fillDesertInterior(px,py){ var idx=py*PW+px, arr=bInterior(idx);               // one road through, sparse rocks/mesas
  for(var sy=0;sy<IW;sy++) for(var sx=0;sx<IW;sx++){
    var isRoad=(sx===2||sx===3); arr[sy*IW+sx]=isRoad?1:(Math.random()<0.22?(Math.random()<0.3?5:4):0); } }
  function fillForestInterior(px,py){ var idx=py*PW+px, arr=bInterior(idx);
  for(var sy=0;sy<IW;sy++) for(var sx=0;sx<IW;sx++) arr[sy*IW+sx]=(sy===2)?0:3; }          // dense trees with a bare clearing/trail through the middle
  function railEdge(px,py,edge){ var idx=py*PW+px, arr=bInterior(idx);
  if(edge==="top") for(var sx=0;sx<IW;sx++) arr[0*IW+sx]=6;
  else if(edge==="bottom") for(sx=0;sx<IW;sx++) arr[(IW-1)*IW+sx]=6;
  else if(edge==="left") for(var sy=0;sy<IW;sy++) arr[sy*IW+0]=6;
  else if(edge==="right") for(sy=0;sy<IW;sy++) arr[sy*IW+(IW-1)]=6; }
  function fillRect(x0,x1,y0,y1,code){ for(var py=y0;py<=y1;py++) for(var px=x0;px<=x1;px++) world[py*PW+px]=code; }
function seedBlank(){ world=new Uint8Array(PW*PW); interiors={}; irot={}; bBuilderBase=null; bView="world"; bPlateIdx=-1; bSyncButtons(); bDraw(); bUpdateTool(); }
function seedCity(){
  // matches buildCity(): a flat 160-unit downtown, seeded as real, fully editable plates
  world=new Uint8Array(PW*PW); interiors={}; irot={}; bBuilderBase=null;
  var mid=Math.floor(PW/2), r=1;
  fillRect(mid-r,mid+r,mid-r,mid+r,2);
  for(var py=mid-r;py<=mid+r;py++) for(var px=mid-r;px<=mid+r;px++) fillCityInterior(px,py);
  bView="world"; bPlateIdx=-1; bSyncButtons(); bDraw(); bUpdateTool();
}
function seedMega(){
  // the Mega World broken into real, fully editable plates. The ocean is a genuine Water plate the whole way
  // across (so there's real water everywhere under the crossing); the bridge and drawbridge are new "mini
  // plates" — Bridge Deck and Lift Span — placed as details on top of that water, with real solid side rails
  // so you can't just drive off into the sea. Tap into any plate to edit, retype, or erase what's inside it.
  world=new Uint8Array(PW*PW); interiors={}; irot={}; bBuilderBase=null;
  var mid=Math.floor(PW/2), px,py, W=7;
  var mN=mid+3, mS=mid+9;                                                                 // main island: 7 rows
  var oN=mid-2, oS=mid+2;                                                                 // ocean: 5 rows (300 units, exact)
  var sN=mid-4, sS=mid-3;                                                                 // second island: 2 rows
  var mCode={city:2,plain:3,desert:4,highway:2,ice:8,harbor:9};
  var mainGrid=[["city","desert","plain"],["highway","city","ice"],["harbor","plain","desert"]];
  var mColB=[[mid-W,mid-3],[mid-2,mid+1],[mid+2,mid+W]], mRowB=[[mN,mN+1],[mN+2,mN+4],[mN+5,mN+6]];
  for(var mr=0;mr<3;mr++) for(var mc=0;mc<3;mc++) fillRect(mColB[mc][0],mColB[mc][1],mRowB[mr][0],mRowB[mr][1],mCode[mainGrid[mr][mc]]);
  fillRect(mid-W,mid+W,oN,oS,1);                                                          // real, unbroken ocean — water the whole way across
  var islGrid=[["city","highway"],["plain","desert"]];
  fillRect(mid-W,mid,sN,sN,mCode[islGrid[0][0]]); fillRect(mid+1,mid+W,sN,sN,mCode[islGrid[0][1]]);
  fillRect(mid-W,mid,sS,sS,mCode[islGrid[1][0]]); fillRect(mid+1,mid+W,sS,sS,mCode[islGrid[1][1]]);
  // the bridge — a continuous chain of Bridge Deck mini-plates across the water, with one Lift Span at the real center
  for(py=oN;py<=oS;py++){ var bArr=bInterior(py*PW+mid);
    for(var bsy=0;bsy<IW;bsy++) bArr[bsy*IW+2]=(py===mid&&bsy===2)?11:10; }
  // roads through every land plate — each biome gets its own look (city towers / plain trees / desert rock)
  function fillByBiome(px,py){ var t=world[py*PW+px]; if(t===2) fillCityInterior(px,py); else if(t===3) fillPlainInterior(px,py); else if(t===4) fillDesertInterior(px,py); }
  for(py=mN;py<=mS;py++) for(px=mid-W;px<=mid+W;px++) fillByBiome(px,py);
  for(py=sN;py<=sS;py++) for(px=mid-W;px<=mid+W;px++) fillByBiome(px,py);
  // forest — the west side of the second island, matching FX0..FX1/FZ0..FZ1
  for(py=sN;py<=sS;py++) for(px=mid-W;px<=mid-4;px++) fillForestInterior(px,py);
  // harbor plates get a few containers scattered through their interior
  for(py=mN;py<=mS;py++) for(px=mid-W;px<=mid+W;px++){ if(world[py*PW+px]===9){ var hia=bInterior(py*PW+px); for(var hs=0;hs<6;hs++){ var hcell=(Math.random()*IW*IW)|0; if(!hia[hcell]) hia[hcell]=8; } } }
  // railway loop — an inset ring inside the main island (TRK is centered in the island, not on its edge)
  var rN=mN+1, rS=mS-1, rW=mid-W+1, rE=mid+W-1;
  for(px=rW;px<=rE;px++){ railEdge(px,rN,"top"); railEdge(px,rS,"bottom"); }
  for(py=rN;py<=rS;py++){ railEdge(rW,py,"left"); railEdge(rE,py,"right"); }
  bView="world"; bPlateIdx=-1; bSyncButtons(); bDraw(); bUpdateTool();
}
function bCenterView(){ if(!bCanvas) return; var cp=(bCanvas.width/PW)*bWZoom; bWPanX=(PW*cp-bCanvas.width)/2; bWPanY=(PW*cp-bCanvas.width)/2; bWClamp(); }
function bHasContent(){ if(bBuilderBase) return true; for(var i=0;i<world.length;i++) if(world[i]) return true; return false; }
document.getElementById("bSeedBlank").onclick=function(){ if(!bHasContent() || confirm("Clear the current world and start blank?")){ seedBlank(); bCenterView(); bDraw(); } };
document.getElementById("bSeedCity").onclick=function(){ if(!bHasContent() || confirm("Replace the current world with the City layout? Anything you've placed will be cleared.")){ seedCity(); bCenterView(); bDraw(); } };
document.getElementById("bSeedMega").onclick=function(){ if(!bHasContent() || confirm("Replace the current world with the Mega World 2 layout? Anything you've placed will be cleared.")){ seedMega(); bCenterView(); bDraw(); } };
document.getElementById("bClear").onclick=function(){ world=new Uint8Array(PW*PW); interiors={}; irot={}; bBuilderBase=null; bView="world"; bPlateIdx=-1; bSyncButtons(); bDraw(); bUpdateTool(); };
  document.getElementById("bDelete").onclick=function(){ var w=bLoadWorlds(); w[bSlot]=null; bSaveWorlds(w); bRenderSlots(); bFlash("Cleared World "+(bSlot+1)); };
  document.getElementById("bPlay").onclick=function(){ playCustomWorld(); };
  document.getElementById("buildDone").onclick=closeBuilder;
  document.getElementById("builderBtn").addEventListener("click",openBuilder);
}
function wireMenu(){
  var roleEls=document.querySelectorAll(".role");
  roleEls.forEach(function(el){ el.addEventListener("click",function(){
    roleEls.forEach(function(r){r.classList.remove("sel");}); el.classList.add("sel"); role=el.getAttribute("data-role"); }); });
  var diffEls=document.querySelectorAll(".diff button");
  diffEls.forEach(function(el){ el.addEventListener("click",function(){
    diffEls.forEach(function(d){d.classList.remove("sel");}); el.classList.add("sel"); diff=el.getAttribute("data-diff"); }); });
  var mapEls=document.querySelectorAll(".mapsel button");
  mapEls.forEach(function(el){ el.addEventListener("click",function(){
    mapEls.forEach(function(d){d.classList.remove("sel");}); el.classList.add("sel"); map=el.getAttribute("data-map"); }); });
  var gadgetEls=document.querySelectorAll(".gadgetsel button");
  gadgetEls.forEach(function(el){ el.addEventListener("click",function(){
    gadgetEls.forEach(function(d){d.classList.remove("sel");}); el.classList.add("sel"); gadget=el.getAttribute("data-gadget"); }); });
  var modeEls=document.querySelectorAll("#modeSel button");
  modeEls.forEach(function(el){ el.addEventListener("click",function(){
    modeEls.forEach(function(d){d.classList.remove("sel");}); el.classList.add("sel"); mode=el.getAttribute("data-mode"); }); });
  document.getElementById("playBtn").addEventListener("click",startGame);
  document.getElementById("againBtn").addEventListener("click",quitToMenu);
  document.getElementById("quitBtn").addEventListener("click",function(){ if(state==="playing") quitToMenu(); });
  document.getElementById("getOutBtn").addEventListener("click",function(){ if(onDutyMode && !onFoot && player && !player.isHeli) exitToStation(); });
  document.getElementById("getInBtn").addEventListener("click",function(){ if(onDutyMode && onFoot && nearGarageIdx>=0) enterVehicle(nearGarageIdx); });
  document.getElementById("soundBtn").addEventListener("click",function(){
    soundOn=!soundOn; this.textContent="Sound: "+(soundOn?"On":"Off"); if(!soundOn&&sirenGain) sirenGain.gain.value=0; });
  document.getElementById("dmgBtn").addEventListener("click",function(){
    damageOn=!damageOn; this.textContent="Damage: "+(damageOn?"On":"Off"); beep(damageOn?420:240,0.08,"square"); });
}

// ----------------------------------------------------------------------------
//  Garage (customization)
// ----------------------------------------------------------------------------
var garageRole="cop", garageOpen=false;
var pRenderer, pScene, pCamera, pHolder, pCar, pClock;

function cfgFor(r){ return r==="cop" ? COP_CONFIG : ROB_CONFIG; }
function hex6(n){ return "#"+("000000"+n.toString(16)).slice(-6); }

function previewInit(){
  var canvas=document.getElementById("carPreview");
  pRenderer=new THREE.WebGLRenderer({canvas:canvas,antialias:true,alpha:true});
  pRenderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  pRenderer.outputEncoding=THREE.sRGBEncoding;
  pRenderer.toneMapping=THREE.ACESFilmicToneMapping; pRenderer.toneMappingExposure=1.12;
  pScene=new THREE.Scene();
  pScene.environment=ENV;
  pCamera=new THREE.PerspectiveCamera(45,1,0.1,100);
  pCamera.position.set(5.6,3.5,6.6); pCamera.lookAt(0,0.8,0);
  pScene.add(new THREE.HemisphereLight(0x9fb6ff,0x10131a,0.95));
  var dl=new THREE.DirectionalLight(0xffffff,1.15); dl.position.set(4,8,5); pScene.add(dl);
  var dl2=new THREE.DirectionalLight(0x6f8bff,0.5); dl2.position.set(-5,4,-4); pScene.add(dl2);
  var disc=new THREE.Mesh(new THREE.CylinderGeometry(4.2,4.2,0.2,40),
    new THREE.MeshStandardMaterial({color:0x141a26,roughness:0.7,metalness:0.2}));
  disc.position.y=-0.1; pScene.add(disc);
  pHolder=new THREE.Group(); pScene.add(pHolder);
  pClock=new THREE.Clock();
}
function previewResize(){
  var c=document.getElementById("carPreview");
  var w=c.clientWidth||320, h=c.clientHeight||230;
  pRenderer.setSize(w,h,false);
  pCamera.aspect=w/h; pCamera.updateProjectionMatrix();
}
function rebuildPreviewCar(){
  if(pCar) pHolder.remove(pCar);
  pCar=makeCar(garageRole==="cop", cfgFor(garageRole));
  pHolder.add(pCar);
}
function previewLoop(){
  if(!garageOpen) return;
  requestAnimationFrame(previewLoop);
  pHolder.rotation.y += pClock.getDelta()*0.55;
  pRenderer.render(pScene,pCamera);
}

function buildSwatches(id,colors,get,set,cat){
  var c=document.getElementById(id); c.innerHTML="";
  colors.forEach(function(hex){
    var d=document.createElement("div"); d.className="swatch"; d.style.background=hex6(hex);
    if(!isUnlocked(cat,hex)){ d.classList.add("locked"); d.title="$"+priceOf(cat,hex); }
    d.addEventListener("click",function(){
      if(!isUnlocked(cat,hex)){ if(!buyItem(cat,hex)) return; d.classList.remove("locked"); }
      set(hex); markSwatch(id,colors,get); rebuildPreviewCar(); persist();
    });
    c.appendChild(d);
  });
  markSwatch(id,colors,get);
}
function markSwatch(id,colors,get){
  var c=document.getElementById(id), cur=get();
  Array.prototype.forEach.call(c.children,function(ch,i){ ch.classList.toggle("sel",colors[i]===cur); });
}
function buildSeg(id,items,get,set,after,cat){
  var c=document.getElementById(id); c.innerHTML="";
  items.forEach(function(it){
    var b=document.createElement("button"); b.setAttribute("data-key",it.key);
    var locked=cat && !isUnlocked(cat,it.key);
    b.textContent= locked ? (it.label+" $"+priceOf(cat,it.key)) : it.label;
    if(locked) b.classList.add("locked");
    b.addEventListener("click",function(){
      if(cat && !isUnlocked(cat,it.key)){ if(!buyItem(cat,it.key)) return; b.classList.remove("locked"); b.textContent=it.label; }
      set(it.key); markSeg(id,get); if(after)after(); rebuildPreviewCar(); persist();
    });
    c.appendChild(b);
  });
  markSeg(id,get);
}
function markSeg(id,get){
  var c=document.getElementById(id), cur=get();
  Array.prototype.forEach.call(c.children,function(b){ b.classList.toggle("sel",b.getAttribute("data-key")===cur); });
}
function updateStyleHint(){
  document.getElementById("styleHint").textContent=(STYLES[cfgFor(garageRole).style]||STYLES.supercar).hint;
}
function refreshGarageControls(){
  var cfg=cfgFor(garageRole);
  buildSwatches("paintSwatches",PAINTS,function(){return cfg.color;},function(v){cfg.color=v;},"paint");
  buildSwatches("wheelSwatches",RIMS,function(){return cfg.wheel;},function(v){cfg.wheel=v;},"rim");
  buildSeg("finishSeg",
    [{key:"gloss",label:"Gloss"},{key:"matte",label:"Matte"},{key:"chrome",label:"Chrome"}],
    function(){return cfg.finish;},function(v){cfg.finish=v;},null,"finish");
  buildSeg("styleSeg",
    [{key:"supercar",label:"Supercar"},{key:"offroad",label:"Off-road"},{key:"hypercar",label:"Hypercar"},{key:"truck",label:"Truck"},
     {key:"muscle",label:"Muscle"},{key:"interceptor",label:"Interceptor"},{key:"van",label:"Van"},{key:"buggy",label:"Buggy"},{key:"suv",label:"SUV"},{key:"sedan",label:"Sedan"},{key:"tank",label:"Tank"},{key:"moto",label:"Motorcycle"},{key:"hothatch",label:"Hot Hatch"},{key:"limo",label:"Limo"},{key:"bus",label:"Bus"},{key:"monster",label:"Monster Truck"},{key:"swat",label:"SWAT Truck"},{key:"semi",label:"Semi Truck"}],
    function(){return cfg.style;},function(v){cfg.style=v;},updateStyleHint,"style");
  updateStyleHint();
  refreshUpgrades();
  rebuildPreviewCar();
}
function openGarage(){
  garageRole=role;
  if(!pRenderer) previewInit();
  document.getElementById("menu").classList.add("hidden");
  document.getElementById("customize").classList.remove("hidden");
  var btns=document.querySelectorAll("#custRole button");
  Array.prototype.forEach.call(btns,function(b){ b.classList.toggle("sel",b.getAttribute("data-role")===garageRole); });
  garageOpen=true;
  updateBankUI();
  requestAnimationFrame(function(){ previewResize(); refreshGarageControls(); previewLoop(); });
}
function closeGarage(){
  garageOpen=false;
  document.getElementById("customize").classList.add("hidden");
  document.getElementById("menu").classList.remove("hidden");
}
function wireGarage(){
  document.getElementById("customizeBtn").addEventListener("click",openGarage);
  document.getElementById("custDone").addEventListener("click",closeGarage);
  var btns=document.querySelectorAll("#custRole button");
  Array.prototype.forEach.call(btns,function(b){
    b.addEventListener("click",function(){
      garageRole=b.getAttribute("data-role");
      Array.prototype.forEach.call(btns,function(x){x.classList.remove("sel");}); b.classList.add("sel");
      refreshGarageControls();
    });
  });
  window.addEventListener("resize",function(){ if(garageOpen) previewResize(); });
}

loadSave();
initThree(); applyQuality(settings.quality); bindInput(); wireMenu(); wireGarage(); wireSettings(); syncSettingsUI(); updateBankUI(); loop();

})();
</script>
</body>
</html>

