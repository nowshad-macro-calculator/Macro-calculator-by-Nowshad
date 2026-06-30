<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
 <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover, user-scalable=yes, maximum-scale=5" />
  <title>Nowshad's Macro Calculator</title>

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap" rel="stylesheet">

  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

  <style>
    :root{
      --bg1:#0b3aa8; --bg2:#071a4b;
      --card:rgba(255,255,255,.10);
      --stroke:rgba(255,255,255,.18);
      --text:#eef3ff; --muted:rgba(238,243,255,.75);
      --good:#3ddc97; --warn:#ffcc66; --bad:#ff6b6b;
      --shadow:0 18px 55px rgba(0,0,0,.35);
      --radius:18px;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family:Inter,system-ui,-apple-system,Segoe UI,Roboto,Arial,sans-serif;
      color:var(--text);
      background: radial-gradient(1200px 700px at 15% 0%, #1b63ff33 0%, transparent 60%),
                  radial-gradient(900px 600px at 90% 10%, #3ddc9730 0%, transparent 55%),
                  linear-gradient(145deg, var(--bg1), var(--bg2));
      min-height:100vh;
    }
    .wrap{max-width:1100px; margin:0 auto; padding:18px 14px 70px;}
    .topbar{
      display:flex; gap:12px; align-items:center; justify-content:space-between;
      padding:14px 16px; border:1px solid var(--stroke); background:var(--card);
      border-radius:var(--radius); box-shadow:var(--shadow);
      position:sticky; top:10px; z-index:50; backdrop-filter: blur(10px);
    }
    .brand{display:flex; flex-direction:column; gap:2px}
    .brand h1{font-size:16px; margin:0; letter-spacing:.2px; font-weight:800}
    .brand span{font-size:12px; color:var(--muted)}
    .tabs{display:flex; gap:8px; flex-wrap:wrap; justify-content:flex-end}
    .tabbtn{
      padding:10px 12px; border-radius:12px;
      border:1px solid var(--stroke); background:rgba(255,255,255,.08);
      color:var(--text); cursor:pointer; font-weight:700; font-size:13px;
      transition:.15s transform, .15s background;
    }
    .tabbtn:hover{transform:translateY(-1px); background:rgba(255,255,255,.12)}
    .tabbtn.active{background:rgba(255,255,255,.18)}
    .grid{display:grid; gap:14px}
    @media (min-width: 920px){
      .grid.cols2{grid-template-columns:1.05fr .95fr}
      .grid.cols3{grid-template-columns:1fr 1fr 1fr}
    }
    .card{
      border:1px solid var(--stroke);
      background:var(--card);
      border-radius:var(--radius);
      box-shadow:var(--shadow);
      padding:14px;
    }
    .card h2{margin:0 0 10px; font-size:15px}
    .card h3{margin:0 0 10px; font-size:14px; color:#d9e7ff}
    .sub{color:var(--muted); font-size:12px; line-height:1.35}
    .row{display:grid; gap:10px}
    @media (min-width: 640px){
      .row.cols2{grid-template-columns:1fr 1fr}
      .row.cols3{grid-template-columns:1fr 1fr 1fr}
    }
    label{font-size:12px; color:var(--muted); display:block; margin-bottom:6px}
    input, select, textarea{
      width:100%;
      padding:11px 12px;
      border-radius:12px;
      border:1px solid rgba(255,255,255,.20);
      background:rgba(10,20,60,.45);
      color:var(--text);
      outline:none;
      font-size:14px;
    }
    textarea{min-height:74px; resize:vertical}
    input::placeholder{color:rgba(238,243,255,.55)}
    input[readonly]{opacity:.95}
    .btn{
      padding:11px 12px; border-radius:12px; border:1px solid var(--stroke);
      background:rgba(255,255,255,.14); color:var(--text);
      cursor:pointer; font-weight:800;
    }
    .btn:hover{background:rgba(255,255,255,.18)}
    .btn.danger{background:rgba(255,107,107,.18)}
    .btn.danger:hover{background:rgba(255,107,107,.26)}
    .pill{
      display:inline-flex; align-items:center; gap:8px;
      border:1px solid var(--stroke); border-radius:999px;
      padding:8px 10px; background:rgba(255,255,255,.08);
      font-size:12px; color:var(--muted)
    }
    .kpi{
      display:flex; flex-direction:column; gap:6px; padding:12px;
      border:1px solid var(--stroke); border-radius:16px;
      background:rgba(255,255,255,.10);
    }
    .kpi .big{font-size:18px; font-weight:800; letter-spacing:.2px}
    .kpi .cap{font-size:12px; color:var(--muted)}
    .accentGood{color:var(--good)}
    .accentBad{color:var(--bad)}
    .divider{height:1px; background:rgba(255,255,255,.14); margin:10px 0}
    .hide{display:none !important}
    .bmiBar{
      height:10px; border-radius:999px; overflow:hidden; background:rgba(255,255,255,.14);
      border:1px solid rgba(255,255,255,.16);
    }
    .bmiFill{height:100%; width:0%; background:linear-gradient(90deg, var(--bad), var(--warn), var(--good), var(--warn), var(--bad))}
    .bmiMarker{position:relative; height:14px; margin-top:6px;}
    .bmiMarker span{
      position:absolute; top:0; transform:translateX(-50%);
      font-size:11px; color:var(--muted)
    }
    .chartBox{
      height:260px;
      border:1px solid rgba(255,255,255,.16);
      border-radius:16px;
      background:rgba(255,255,255,.06);
      padding:10px;
    }
    canvas{max-width:100% !important}
    .note{
      margin-top:10px;
      padding:10px 12px;
      border-radius:14px;
      background:rgba(61,220,151,.12);
      border:1px solid rgba(61,220,151,.28);
      color:#dffaf0;
      font-size:12px;
    }
    .warnNote{
      margin-top:10px;
      padding:10px 12px;
      border-radius:14px;
      background:rgba(255,204,102,.14);
      border:1px solid rgba(255,204,102,.30);
      color:#fff4dc;
      font-size:12px;
    }
    .tableWrap{
      border:1px solid rgba(255,255,255,.16);
      border-radius:16px;
      overflow:auto;
      background:rgba(255,255,255,.06);
    }
    table{width:100%; border-collapse:collapse; min-width:920px;}
    th, td{
      padding:10px 10px;
      border-bottom:1px solid rgba(255,255,255,.10);
      text-align:left;
      font-size:12px;
      color:rgba(238,243,255,.92);
      vertical-align:middle;
    }
    th{color:rgba(238,243,255,.75); font-weight:800; background:rgba(255,255,255,.06)}
    .miniInput{
      width:90px; padding:8px 8px; border-radius:10px; font-size:12px;
    }
    .actionBtn{
      padding:8px 10px; border-radius:10px; border:1px solid rgba(255,255,255,.18);
      background:rgba(255,255,255,.10); color:var(--text); cursor:pointer; font-weight:800; font-size:12px;
    }
    .actionBtn:hover{background:rgba(255,255,255,.14)}
    .twoBtnRow{display:flex; gap:10px; flex-wrap:wrap}
    .smallBtn{padding:9px 10px; font-size:12px}
    .chipRow{display:flex; gap:8px; flex-wrap:wrap}
    .chipBtn{
      padding:8px 10px; border-radius:999px; border:1px solid rgba(255,255,255,.18);
      background:rgba(255,255,255,.10); color:var(--text); cursor:pointer; font-weight:800; font-size:12px;
    }
    .chipBtn:hover{background:rgba(255,255,255,.14)}
    /* ===========================
   PATCH M (Mobile UX)
=========================== */
@media (max-width: 720px){
  .wrap{ padding:12px 10px 80px; }

  /* Topbar: tabs should scroll horizontally (like app tabs) */
  .topbar{
    gap:10px;
    padding:12px;
  }
  .tabs{
    flex-wrap:nowrap;
    overflow-x:auto;
    -webkit-overflow-scrolling:touch;
    gap:8px;
    padding-bottom:2px;
    max-width:55vw;
  }
  .tabs::-webkit-scrollbar{ display:none; }
  .tabbtn{
    flex:0 0 auto;
    padding:9px 11px;
    font-size:12px;
    border-radius:12px;
    white-space:nowrap;
  }

  /* Inputs easier to tap */
  input, select, textarea{ padding:12px 12px; font-size:15px; }

  /* Reduce card padding a bit */
  .card{ padding:12px; }

  /* Tables: keep horizontal scroll inside wrapper, not page */
  .tableWrap{
    overflow:auto;
    -webkit-overflow-scrolling:touch;
    overscroll-behavior:contain;
  }
  table{ min-width:760px; } /* was 920px (too wide for phones) */

  /* KPIs stack tighter */
  .kpi{ padding:10px; }
  .kpi .big{ font-size:17px; }
}

/* Make form grids stack nicely on small phones */
@media (max-width: 420px){
  .row.cols2, .row.cols3{ grid-template-columns:1fr !important; }
}

/* Food log top bar (added in Patch 3) */
.foodBar{
  position:sticky;
  top:72px; /* sits below main topbar */
  z-index:40;
  border:1px solid var(--stroke);
  background:rgba(10,20,60,.55);
  backdrop-filter: blur(10px);
  border-radius:16px;
  padding:12px;
  box-shadow:0 10px 28px rgba(0,0,0,.25);
}

.foodBarTop{
  display:flex;
  gap:10px;
  align-items:center;
  justify-content:space-between;
  flex-wrap:wrap;
}

.foodDateWrap{
  display:flex;
  align-items:center;
  gap:8px;
  flex-wrap:wrap;
}
.foodDateWrap label{ margin:0; }
.foodDate{
  width:auto;
  min-width:150px;
  padding:10px 12px;
  border-radius:999px;
}

/* Big, distinct search bar like your screenshot */
.searchBig{
  margin-top:10px;
  position:relative;
}
.searchBig input{
  padding:14px 14px 14px 44px;
  border-radius:18px;
  font-size:16px;
  background:rgba(255,255,255,.08);
  border:1px solid rgba(120,170,255,.55);
  box-shadow:0 8px 20px rgba(0,0,0,.22);
}
.searchBig:before{
  content:"🔎";
  position:absolute;
  left:14px;
  top:50%;
  transform:translateY(-50%);
  opacity:.85;
}

/* Make the “Filter” row feel secondary vs Search */
.filterRow{
  margin-top:10px;
  display:grid;
  gap:10px;
}
@media (min-width: 640px){
  .filterRow{ grid-template-columns:1fr 1fr; }
}

/* Keep FoodBar spacing correct under sticky topbar on very small screens */
@media (max-width: 720px){
  .foodBar{ top:86px; }
}
/* =====================================================
   FINAL MOBILE LAYOUT FIX — SAFE, ISOLATED, STABLE
   ===================================================== */

/* 1) NEVER lock the viewport */
html, body {
  overflow-x: auto;
  overflow-y: auto;
}

/* 2) App wrapper must never clip content */
.wrap {
  width: 100%;
  max-width: 1100px;
  margin: 0 auto;
  overflow: visible;
}

/* 3) Tabs behave SAME on all pages */
@media (max-width: 768px) {

  .tabs {
    display: flex;
    flex-wrap: wrap;              /* allows 2 rows */
    gap: 10px;
    width: 100%;
  }

  .tabbtn {
    flex: 1 1 calc(50% - 10px);   /* 2 buttons per row */
    text-align: center;
    white-space: nowrap;
    padding: 12px 10px;
    font-size: 13px;
  }
}

/* 4) Tables scroll INSIDE, page never breaks */
@media (max-width: 768px) {

  .tableWrap {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
  }

  .tableWrap table {
    min-width: 720px;
  }
}

/* 5) Inputs & buttons always fit screen */
input, select, textarea, button {
  max-width: 100%;
  box-sizing: border-box;
}
/* =====================================================
   FIX: Food Log buttons cut on mobile
   Stack buttons vertically
   ===================================================== */

@media (max-width: 768px) {

  /* Wrapper that holds Add Entry + Add Custom Food */
  .btnRow,
  .buttonRow,
  .actionsRow,
  .twoBtnRow {
    display: flex !important;
    flex-direction: column !important;
    gap: 10px !important;
  }

  /* Buttons must be full width */
  .btnRow .btn,
  .buttonRow .btn,
  .actionsRow .btn,
  .twoBtnRow .btn {
    width: 100% !important;
  }
}
/* =====================================================
   FIX: Food Log action buttons cut on mobile
   Targets ONLY the Add Entry + Add Custom Food row
   ===================================================== */
@media (max-width: 768px) {

  /* This matches your exact wrapper: <div class="row cols2"> */
  #page-food .row.cols2 {
    grid-template-columns: 1fr !important;  /* stack vertically */
  }

  #page-food .row.cols2 .btn {
    width: 100% !important;
  }
}


  </style>
</head>

<body>
  <div class="wrap">
    <div class="topbar">
      <div class="brand">
        <h1>Nowshad's Macro Calculator</h1>
        <span>Profile → Food Log → Lifestyle → Dashboard + PDF</span>
      </div>
      <div class="tabs">
        <button class="tabbtn active" data-page="dashboard">Dashboard</button>
        <button class="tabbtn" data-page="profile">Profile</button>
        <button class="tabbtn" data-page="food">Food Log</button>
        <button class="tabbtn" data-page="lifestyle">Lifestyle</button>
      </div>
    </div>

    <!-- DASHBOARD -->
    <section id="page-dashboard" class="grid cols2" style="margin-top:14px;">
      <div class="card">
        <h2>Today’s Dashboard</h2>
        <div class="sub">Targets use selected Profile. Dashboard uses your “day start hour” to define “today”.</div>

        <div class="divider"></div>

        <div class="twoBtnRow">
          <button class="btn smallBtn danger" onclick="resetOnlyToday()">Reset Only Today</button>
          <button class="btn smallBtn danger" onclick="resetEverything()">Reset Everything</button>
        </div>

        <div class="warnNote" id="needProfileNote" style="display:none;">
          No saved profile selected. Go to <b>Profile</b> tab → create/select a profile → press <b>Calculate Targets</b> (or Save Profile).
        </div>

        <div class="divider"></div>

        <div class="row cols3">
          <div class="kpi">
            <div class="cap">Calories eaten</div>
            <div class="big" id="dashCalories">0 kcal</div>
            <div class="cap">Target: <span id="dashCalTarget">0</span> kcal</div>
          </div>
          <div class="kpi">
            <div class="cap">Workout burned</div>
            <div class="big" id="dashBurn">0 kcal</div>
            <div class="cap">Net: <span id="dashNet">0</span> kcal</div>
          </div>
          <div class="kpi">
            <div class="cap">Protein eaten</div>
            <div class="big" id="dashProtein">0 g</div>
            <div class="cap">Target: <span id="dashProtTarget">0</span> g</div>
          </div>
        </div>

        <div class="row cols3" style="margin-top:10px">
          <div class="kpi">
            <div class="cap">Carbs eaten</div>
            <div class="big" id="dashCarbs">0 g</div>
            <div class="cap">Target: <span id="dashCarbTarget">0</span> g</div>
          </div>
          <div class="kpi">
            <div class="cap">Fat eaten</div>
            <div class="big" id="dashFat">0 g</div>
            <div class="cap">Target: <span id="dashFatTarget">0</span> g</div>
          </div>
          <div class="kpi">
            <div class="cap">Net vs Target (calories)</div>
            <div class="big" id="dashCalBalance">—</div>
            <div class="cap" id="dashCalBalanceMsg">Set profile targets first.</div>
          </div>
        </div>

        <div class="divider"></div>

        <div class="row cols3">
          <div class="kpi">
            <div class="cap">Protein balance</div>
            <div class="big" id="dashProtBalance">—</div>
            <div class="cap" id="dashProtBalanceMsg">—</div>
          </div>
          <div class="kpi">
            <div class="cap">Carbs balance</div>
            <div class="big" id="dashCarbBalance">—</div>
            <div class="cap" id="dashCarbBalanceMsg">—</div>
          </div>
          <div class="kpi">
            <div class="cap">Fat balance</div>
            <div class="big" id="dashFatBalance">—</div>
            <div class="cap" id="dashFatBalanceMsg">—</div>
          </div>
        </div>

        <div class="divider"></div>

        <h3>Lifestyle Summary (Today)</h3>
        <div class="sub">This pulls from Lifestyle tab for the same “day start hour”.</div>
        <div class="row cols2" style="margin-top:10px">
          <div class="kpi">
            <div class="cap">Workout (multiple supported)</div>
            <div class="big" id="dashLifeWorkout">—</div>
            <div class="cap" id="dashLifeWorkoutSub">—</div>
          </div>
          <div class="kpi">
            <div class="cap">Sleep & Water</div>
            <div class="big" id="dashLifeSleepWater">—</div>
            <div class="cap" id="dashLifeSleepWaterSub">—</div>
          </div>
        </div>

        <div class="divider"></div>

        <div class="row cols3">
          <div class="pill">BMI: <b id="dashBMI">—</b></div>
          <div class="pill">BMR: <b id="dashBMR">—</b> kcal</div>
          <div class="pill">TDEE: <b id="dashTDEE">—</b> kcal</div>
        </div>

        <div style="margin-top:10px" class="sub">
          Recommended BMI range: <b>18.5–24.9</b>. Your BMI marker is shown below.
        </div>

        <div style="margin-top:10px">
          <div class="bmiBar"><div class="bmiFill" id="bmiFill"></div></div>
          <div class="bmiMarker">
            <span style="left:18.5%">18.5</span>
            <span style="left:24.9%">24.9</span>
            <span id="bmiPin" style="left:0%">▲ You</span>
          </div>
        </div>

        <div class="note" id="profileSavedNote" style="display:none;">
          Profile saved ✔️ You won’t need to re-enter your details on this device.
        </div>

        <div class="divider"></div>

        <button class="btn" onclick="savePDF()">Save PDF Report</button>
      </div>

      <div class="card">
        <h2>Macro Chart</h2>
        <div class="sub">Colors are distinct (no blue).</div>
        <div class="chartBox">
          <canvas id="macroChart"></canvas>
        </div>

        <div class="divider"></div>

        <h3>Quick Profile (Read-only)</h3>
        <div class="sub">Profile details are managed in the Profile tab.</div>
        <div class="row cols2" style="margin-top:10px">
          <div class="kpi">
            <div class="cap">Selected profile</div>
            <div class="big" id="dashProfileName">—</div>
            <div class="cap" id="dashProfileMeta">—</div>
          </div>
          <div class="kpi">
            <div class="cap">Protein multiplier</div>
            <div class="big" id="dashProtMult">—</div>
            <div class="cap">g/kg</div>
          </div>
        </div>
      </div>
    </section>

    <!-- PROFILE -->
    <section id="page-profile" class="grid cols2 hide" style="margin-top:14px;">
      <div class="card">
        <h2>Profiles (Multi-user)</h2>
        <div class="sub">Create multiple profiles and switch anytime.</div>

        <div class="divider"></div>

        <div class="row cols2">
          <div>
            <label>Select profile</label>
            <select id="profileSelect"></select>
          </div>
          <div>
            <label>New profile name</label>
            <input id="newProfileName" placeholder="e.g., Nowshad / Yeasmin/ Aymaan / Guest">
          </div>
        </div>

        <div class="row cols3" style="margin-top:10px">
          <button class="btn" onclick="createProfile()">Create Profile</button>
          <button class="btn" onclick="calculateTargetsOnly()">Calculate Targets</button>
          <button class="btn danger" onclick="resetCurrentProfile()">Reset Current Profile</button>
        </div>

        <div class="divider"></div>

        <h3>Profile Details</h3>
        <div class="sub">
          You can calculate targets without saving. Save only to keep it permanently.
          <br><b>Day starts at (hour)</b> means: your “today” starts at that hour (e.g., 4 = day runs 4:00 AM → 3:59 AM).
        </div>

        <div class="divider"></div>

        <div class="row cols2">
          <div>
            <label>Name</label>
            <input id="p_name" placeholder="Enter your name">
          </div>
          <div>
            <label>Age</label>
            <input type="number" min="1" step="1" id="p_age" placeholder="Enter your age">
          </div>
        </div>

        <div class="row cols2" style="margin-top:10px">
          <div>
            <label>Height unit</label>
            <select id="p_height_unit">
              <option value="ftin" selected>Feet/Inches</option>
              <option value="cm">Centimeters (cm)</option>
            </select>
          </div>
          <div>
            <label>Weight unit</label>
            <select id="p_weight_unit">
              <option value="kg" selected>Kilograms (kg)</option>
              <option value="lbs">Pounds (lbs)</option>
            </select>
          </div>
        </div>

        <div class="row cols2 hide" style="margin-top:10px" id="height_cm_row">
          <div>
            <label>Height (cm)</label>
            <input type="number" min="0" step="0.1" id="p_height_cm" placeholder="e.g., 175">
          </div>
          <div class="hide"></div>
        </div>

        <div class="row cols2" style="margin-top:10px" id="height_ftin_row">
          <div>
            <label>Height (ft)</label>
            <input type="number" min="0" step="1" id="p_height_ft" placeholder="e.g., 5">
          </div>
          <div>
            <label>Height (in)</label>
            <input type="number" min="0" step="1" id="p_height_in" placeholder="e.g., 9">
          </div>
        </div>

        <div class="row cols2" style="margin-top:10px">
          <div id="weight_kg_row">
            <label>Weight (kg)</label>
            <input type="number" min="0" step="0.1" id="p_weight_kg" placeholder="e.g., 75">
          </div>
          <div id="weight_lbs_row" class="hide">
            <label>Weight (lbs)</label>
            <input type="number" min="0" step="0.1" id="p_weight_lbs" placeholder="e.g., 165">
          </div>
        </div>

        <div class="divider"></div>

        <div class="row cols2">
          <div>
            <label>Goal</label>
            <select id="p_goal">
              <option value="loss" selected>Weight loss</option>
              <option value="maintain">Maintenance</option>
              <option value="gain">Weight gain</option>
            </select>
          </div>
          <div>
            <label>Activity level</label>
            <select id="p_activity">
              <option value="1.2">Sedentary — little or no exercise</option>
              <option value="1.375">Light — exercise 1–3 times/week</option>
              <option value="1.55">Moderate — exercise 4–5 times/week</option>
              <option value="1.725">Active — daily exercise or intense 3–4 times/week</option>
              <option value="1.9">Very Active — intense exercise 6–7 times/week</option>
              <option value="2.0">Extra Active — very intense daily or physical job</option>
            </select>
          </div>
        </div>

        <div class="row cols3" style="margin-top:10px">
          <div>
            <label>Day starts at (hour)</label>
            <input type="number" min="0" max="23" step="1" id="p_dayStartHour" value="4">
          </div>
          <div>
            <label>Sex (for BMR)</label>
            <select id="p_sex">
              <option value="male" selected>Male</option>
              <option value="female">Female</option>
            </select>
          </div>
          <div>
            <label>Macro style</label>
            <select id="p_macroPreset">
              <option value="balanced">Balanced</option>
              <option value="higherCarb">Higher carb (bulking friendly)</option>
              <option value="higherProtein" selected>Higher protein</option>
            </select>
          </div>
        </div>

        <div class="divider"></div>

        <h3>Targets</h3>
        <div class="sub">
          Protein auto = weight × multiplier (default 2.2 g/kg).
          Calories/Fats/Carbs can be auto OR manual (manual stays editable).
        </div>

        <div class="row cols3" style="margin-top:10px">
          <div>
            <label>Target calories (auto, editable)</label>
            <input type="number" min="0" step="1" id="p_targetCalories" placeholder="Auto">
          </div>
          <div>
            <label>Target protein (g) — auto</label>
            <input type="number" id="p_targetProtein" readonly>
          </div>
          <div>
            <label>Target fats (g) (auto, editable)</label>
            <input type="number" min="0" step="1" id="p_targetFats" placeholder="Auto">
          </div>
        </div>

        <div class="row cols2" style="margin-top:10px">
          <div>
            <label>Target carbs (g) (auto, editable)</label>
            <input type="number" min="0" step="1" id="p_targetCarbs" placeholder="Auto">
          </div>
          <div>
            <label>Protein multiplier (g/kg) — default 2.2</label>
            <select id="p_protMult">
              <option value="1.5">1.5 g/kg</option>
              <option value="1.6">1.6 g/kg</option>
              <option value="1.8">1.8 g/kg</option>
              <option value="2.0">2.0 g/kg</option>
              <option value="2.2" selected>2.2 g/kg</option>
            </select>
          </div>
        </div>

        <div class="divider"></div>

        <div class="row cols2">
          <button class="btn" onclick="saveProfile()">Save Profile</button>
          <button class="btn danger" onclick="unlockManualTargets()">Unlock Manual Targets</button>
        </div>

        <div class="note" id="profileCalcHint" style="display:none;">
          Targets calculated ✔️ (not saved yet). Save Profile to keep permanently.
        </div>

        <div class="note" id="profileSaveHint" style="display:none;">
          Saved ✔️ Go to Food Log and add entries.
        </div>
      </div>

      <div class="card">
        <h2>Metrics (BMI • BMR • TDEE)</h2>
        <div class="row cols3">
          <div class="kpi"><div class="cap">BMI</div><div class="big" id="p_bmiLine">—</div><div class="cap">Recommended 18.5–24.9</div></div>
          <div class="kpi"><div class="cap">BMR</div><div class="big" id="p_bmrLine">—</div><div class="cap">Mifflin–St Jeor</div></div>
          <div class="kpi"><div class="cap">TDEE</div><div class="big" id="p_tdeeLine">—</div><div class="cap" id="p_targetLine">Target: —</div></div>
        </div>
        <div class="divider"></div>
        <div class="sub">TDEE = BMR × Activity Level. Target Calories = TDEE + goal adjustment.</div>
      </div>
    </section>

    <!-- FOOD LOG -->
    <section id="page-food" class="grid cols2 hide" style="margin-top:14px;">
      <div class="card">
        <h2>Food Log (Multiple entries/day)</h2>
        <div class="sub">Search + Category + Food → Unit → Qty → Add Entry (repeat anytime).</div>

        <div class="divider"></div>
<!-- ===== PATCH: Food Log Header (date separate + big search) ===== -->
<div class="foodBar">
  <div class="foodBarTop">
    <div class="foodDateWrap">
      <label class="sub" style="margin:0;">Log date</label>
      <input class="foodDate" type="date" id="logDate">
    </div>

    <div style="min-width:180px; flex:1; max-width:260px;">
      <label class="sub" style="margin:0;">Meal</label>
      <select id="mealType">
        <option>Breakfast</option>
        <option>Brunch</option>
        <option selected>Lunch</option>
        <option>Dinner</option>
        <option>Supper</option>
        <option>Pre-workout meal</option>
        <option>Post-workout meal</option>
        <option>Snacks</option>
      </select>
    </div>
  </div>

  <div class="searchBig">
    <input id="foodSearch" placeholder="Search for a food (biryani, dates, chicken…)" />
  </div>

  <div class="filterRow">
    <div>
      <label>Filter (Category)</label>
      <select id="entryCategory"></select>
    </div>
    <div class="pill" style="justify-content:center;">
      Quick add → choose food, unit, qty, then Add Entry
    </div>
  </div>
</div>
<!-- ===== END PATCH ===== -->

<div class="divider"></div>

<h3>Add Food Entry</h3>


        <div class="row cols2" style="margin-top:10px">
          <div>
            <label>Food item</label>
            <select id="entryFood"></select>
          </div>
          <div>
            <label>Unit</label>
            <select id="entryUnit"></select>
          </div>
        </div>

        <div class="row cols2" style="margin-top:10px">
          <div>
            <label>Quantity</label>
            <input type="number" min="0" step="0.1" id="entryQty" placeholder="Enter qty">
          </div>
          <div>
            <!-- >>> START PATCH: Oil absorption inputs -->
<div class="row cols2" style="margin-top:10px">
  <div>
    <label>Cooking oil used (grams) — optional</label>
    <input type="number" min="0" step="1" id="oilUsedG" placeholder="e.g., 10">
  </div>
  <div>
    <label>Oil absorption (%) — optional</label>
    <input type="number" min="0" max="100" step="1" id="oilAbsorbPct" placeholder="e.g., 30">
  </div>
</div>
<!-- <<< END PATCH: Oil absorption inputs -->

            <label>Portion presets</label>
            <div class="chipRow">
              <button class="chipBtn" type="button" onclick="applyPortionPreset(0.5)">½ plate</button>
              <button class="chipBtn" type="button" onclick="applyPortionPreset(1)">1 plate</button>
              <button class="chipBtn" type="button" onclick="applyPortionPreset(1.5)">1.5 plate</button>
            </div>
          </div>
        </div>

        <div class="row cols2" style="margin-top:10px">
          <button class="btn" onclick="addEntry()">Add Entry</button>
          <button class="btn" onclick="openCustomFoodPanel()">Add Custom Food</button>
        </div>

        <div class="pill" style="margin-top:10px">
          Instant preview: <b><span id="entryPrevP">0</span>g P</b> • <b><span id="entryPrevF">0</span>g F</b> • <b><span id="entryPrevC">0</span>g C</b> • <b><span id="entryPrevK">0</span> kcal</b>
          <span style="margin-left:10px">|</span>
          <span>Source: <b id="entryPrevSrc">—</b></span>
          <span>Conf: <b id="entryPrevConf">—</b></span>
        </div>

        <!-- PATCH 4/6: Custom food entry + source/confidence -->
        <div id="customFoodPanel" class="card hide" style="margin-top:12px; padding:12px; background:rgba(255,255,255,.06);">
          <h3 style="margin-top:0;">Custom Food Entry</h3>
          <div class="sub">Add your own food once, then it appears in search + categories. Macros are per selected unit.</div>
          <div class="divider"></div>

          <div class="row cols2">
            <div>
              <label>Food name</label>
              <input id="cf_name" placeholder="e.g., Kacchi (My portion), Homemade Khichuri">
            </div>
            <div>
              <label>Category</label>
              <select id="cf_category"></select>
            </div>
          </div>

          <div class="row cols3" style="margin-top:10px">
            <div>
              <label>Unit</label>
              <select id="cf_unit">
                <option value="g">g</option>
                <option value="pcs">pcs</option>
                <option value="cup">cup</option>
                <option value="plate" selected>plate</option>
                <option value="slice">slice</option>
                <option value="bowl">bowl</option>
                <option value="serving">serving</option>
              </select>
            </div>
            <div>
              <label>Protein (g)</label>
              <input type="number" min="0" step="0.1" id="cf_p" placeholder="e.g., 25">
            </div>
            <div>
              <label>Carbs (g)</label>
              <input type="number" min="0" step="0.1" id="cf_c" placeholder="e.g., 85">
            </div>
          </div>

          <div class="row cols3" style="margin-top:10px">
            <div>
              <label>Fats (g)</label>
              <input type="number" min="0" step="0.1" id="cf_f" placeholder="e.g., 18">
            </div>
            <div>
              <label>Calories (kcal)</label>
              <input type="number" min="0" step="1" id="cf_k" placeholder="e.g., 650">
            </div>
            <div>
              <label>Confidence</label>
              <select id="cf_conf">
                <option>High</option>
                <option selected>Medium</option>
                <option>Low</option>
              </select>
            </div>
          </div>

          <div class="row cols2" style="margin-top:10px">
            <div>
              <label>Source tag</label>
              <input id="cf_source" placeholder="e.g., label, USDA avg, restaurant avg, recipe estimate">
            </div>
            <div>
              <label>Notes (optional)</label>
              <input id="cf_notes" placeholder="Optional">
            </div>
          </div>

          <div class="row cols3" style="margin-top:10px">
            <button class="btn" onclick="saveCustomFood()">Save Custom Food</button>
            <button class="btn danger" onclick="resetCustomFoods()">Reset Custom Foods</button>
            <button class="btn" onclick="closeCustomFoodPanel()">Close</button>
          </div>

          <div class="note" id="cf_savedNote" style="display:none;">Custom food saved ✔️</div>
        </div>

        <div class="divider"></div>

        <div class="row cols2">
          <button class="btn" onclick="saveDay()">Save for the Day</button>
          <button class="btn danger" onclick="resetFoodLogs()">Reset Food Logs</button>
        </div>

        <div class="divider"></div>

   <h3>Daily Entries</h3>
<p class="sub">Edit quantity inline or delete any row. Includes source/confidence.</p>

<div class="tableWrap">
          <table>
            <thead>
              <tr>
                <th>Meal</th>
                <th>Category</th>
                <th>Food</th>
                <th>Qty</th>
                <th>Unit</th>
                <th>P (g)</th>
                <th>C (g)</th>
                <th>F (g)</th>
                <th>Kcal</th>
                <th>Source</th>
                <th>Conf</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody id="entriesTbody"></tbody>
          </table>
        </div>
      </div>

      <div class="card">
        <h2>Live Totals (Auto)</h2>
        <div class="sub">Totals are based on the entries table for the selected day.</div>

        <div class="divider"></div>

        <div class="row cols2">
          <div class="kpi"><div class="cap">Protein</div><div class="big" id="liveP">0 g</div></div>
          <div class="kpi"><div class="cap">Carbs</div><div class="big" id="liveC">0 g</div></div>
          <div class="kpi"><div class="cap">Fat</div><div class="big" id="liveF">0 g</div></div>
          <div class="kpi"><div class="cap">Calories</div><div class="big" id="liveK">0 kcal</div></div>
        </div>
      </div>
    </section>

    <!-- LIFESTYLE -->
    <section id="page-lifestyle" class="grid cols2 hide" style="margin-top:14px;">
      <div class="card">
        <h2>Lifestyle (Multiple Workouts • Sleep • Water)</h2>
        <div class="sub">Add multiple workouts (e.g., Cardio + Strength). Total burn updates Dashboard net calories.</div>

        <div class="divider"></div>

        <div class="row cols2">
          <div>
            <label>Date</label>
            <input type="date" id="lifeDate">
          </div>
          <div class="pill">
            Total burn today: <b><span id="burnTotalPreview">0</span> kcal</b>
          </div>
        </div>

        <div class="divider"></div>

        <h3>Add Workout</h3>
        <div class="row cols2">
          <div>
            <label>Workout type</label>
            <select id="workoutType">
              <option value="walking">Walking</option>
              <option value="running">Running</option>
              <option value="cycling">Cycling</option>
              <option value="aerobic">Aerobics</option>
              <option value="cardio">Cardio (general)</option>
              <option value="hiit">HIIT</option>
              <option value="strength" selected>Strength training</option>
            </select>
          </div>
          <div id="strengthSplitWrap">
            <label>Strength split</label>
            <select id="strengthSplit">
              <option value="chest_triceps">Chest + Triceps</option>
              <option value="back_biceps">Back + Biceps</option>
              <option value="shoulders_forearms">Shoulders + Forearms</option>
              <option value="legs">Legs</option>
              <option value="fullbody">Full body</option>
            </select>
          </div>
          <div>
            <label>Duration (minutes)</label>
            <input type="number" min="0" step="1" id="workoutMins" placeholder="e.g., 45">
          </div>
          <div>
            <label>&nbsp;</label>
            <button class="btn" onclick="addWorkout()">Add Workout</button>
          </div>
        </div>

        <div class="pill" style="margin-top:10px">
          This workout burn estimate: <b><span id="burnPreview">0</span> kcal</b>
        </div>

        <div class="divider"></div>

        <h3>Workouts List (Today)</h3>
        <div class="sub">You can add multiple workouts and delete any one.</div>
        <div class="tableWrap" style="margin-top:10px">
          <table style="min-width:640px;">
            <thead>
              <tr>
                <th>Type</th>
                <th>Split</th>
                <th>Minutes</th>
                <th>Burn (kcal)</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody id="workoutsTbody"></tbody>
          </table>
        </div>

        <div class="divider"></div>

        <h3>Sleep</h3>
        <div class="row cols2">
          <div>
            <label>Hours slept</label>
            <input type="number" min="0" step="0.1" id="sleepHours" placeholder="e.g., 7.5">
          </div>
          <div>
            <label>Sleep goal (hours)</label>
            <select id="sleepGoal">
              <option value="7">7</option>
              <option value="8" selected>8</option>
              <option value="9">9</option>
            </select>
          </div>
        </div>

        <div class="divider"></div>

        <h3>Water</h3>
        <div class="row cols2">
          <div>
            <label>Water intake (liters)</label>
            <input type="number" min="0" step="0.1" id="waterLiters" placeholder="e.g., 2.5">
          </div>
          <div>
            <label>Water goal (liters)</label>
            <select id="waterGoal">
              <option value="2">2.0</option>
              <option value="2.5">2.5</option>
              <option value="3" selected>3.0</option>
              <option value="3.5">3.5</option>
              <option value="4">4.0</option>
            </select>
          </div>
        </div>

        <div class="divider"></div>

        <div class="row cols2">
          <button class="btn" onclick="saveLifestyle()">Save Lifestyle</button>
          <button class="btn danger" onclick="resetLifestyle()">Reset Lifestyle</button>
        </div>

        <div class="note" id="lifeSavedNote" style="display:none;">
          Lifestyle saved ✔️ Dashboard updated.
        </div>
      </div>

      <div class="card">
        <h2>Today’s Lifestyle Summary</h2>
        <div class="divider"></div>

        <div class="row cols2">
          <div class="kpi">
            <div class="cap">Workout summary</div>
            <div class="big" id="lifeWorkoutLine">—</div>
            <div class="cap" id="lifeWorkoutLineSub">—</div>
          </div>
          <div class="kpi">
            <div class="cap">Sleep / Water</div>
            <div class="big" id="lifeSleepWaterLine">—</div>
            <div class="cap" id="lifeSleepWaterLineSub">—</div>
          </div>
        </div>
      </div>
    </section>
  </div>

<script>
/* ===========================
   Storage Keys
=========================== */
const LS_PROFILES = "nowshad_profiles_v2";
const LS_ACTIVE_PROFILE = "nowshad_active_profile_v2";
const LS_LOG = "nowshad_macro_dailylog_v11";
const LS_LIFE = "nowshad_macro_lifestyle_v11";
const LS_CUSTOM_FOOD = "nowshad_custom_food_v1";

/* ===== Helpers ===== */
const $ = (id)=>document.getElementById(id);
const clamp = (v,min,max)=>Math.max(min,Math.min(max,v));
const n = (v)=>isFinite(parseFloat(v)) ? parseFloat(v) : 0;
const round1 = (x)=>Math.round(x*10)/10;
const round2 = (x)=>Math.round(x*100)/100;

// --- Precision-safe helpers (prevents 100 showing as 98 due to float drift) ---
const SCALE = 100;                     // 2-decimal precision as integer
const toInt = (x)=>Math.round(n(x)*SCALE);
const fromInt = (i)=>i/SCALE;

function sumTotalsSafe(entriesTotals){
  // entriesTotals = array of {P,C,F,K} (can be decimals)
  const acc = {P:0, C:0, F:0, K:0};
  entriesTotals.forEach(t=>{
    acc.P += toInt(t.P);
    acc.C += toInt(t.C);
    acc.F += toInt(t.F);
    acc.K += toInt(t.K);
  });
  return { P: fromInt(acc.P), C: fromInt(acc.C), F: fromInt(acc.F), K: fromInt(acc.K) };
}
  function mergeFoodItem(base, patch){
  // If no base exists, patch becomes the base
  if(!base) return patch;

  const out = { ...base };

  // Merge unit options (union)
  const a = Array.isArray(base.unitOptions) ? base.unitOptions : [];
  const b = Array.isArray(patch.unitOptions) ? patch.unitOptions : [];
  out.unitOptions = Array.from(new Set([...a, ...b]));

  // Merge perUnit nested keys (don’t delete existing; add missing units)
  out.perUnit = { ...(base.perUnit || {}) };
  const patchPU = patch.perUnit || {};
  Object.keys(patchPU).forEach(unit=>{
    if(!out.perUnit[unit]) out.perUnit[unit] = patchPU[unit];
    else {
      // if both exist, merge macro keys (patch fills missing)
      out.perUnit[unit] = { ...out.perUnit[unit], ...patchPU[unit] };
      // ^ if you want patch to override existing numbers, swap order:
      // out.perUnit[unit] = { ...out.perUnit[unit], ...patchPU[unit] };
    }
  });

  // Prefer patch metadata if base missing
  out.source = base.source || patch.source || "";
  out.confidence = base.confidence || patch.confidence || "";

  // Keep any other fields base had; allow patch to add new fields
  Object.keys(patch).forEach(k=>{
    if(out[k] === undefined) out[k] = patch[k];
  });

  return out;
}

function mergeFoodPatch(patchObj){
  Object.keys(patchObj).forEach(k=>{
    FOOD[k] = mergeFoodItem(FOOD[k], patchObj[k]);
  });
}
function uid(){
  return (crypto?.randomUUID
    ? crypto.randomUUID()
    : String(Date.now()) + Math.random());
}

/* ===== START: Oil absorption helpers ===== */
function oilForEntry(entry){
  // Supports multiple possible field names
  const oilAbsorbG = n(
    entry.oilAbsorbG ??
    entry.oilAbsorb ??
    entry.oil_g ??
    0
  );

  const oilKcalPerG = n(
    entry.oilKcalPerG ??
    entry.oilKcalG ??
    9            // default: fat kcal/g
  );

  return {
    oilAbsorbG,
    oilF: oilAbsorbG,
    oilK: oilAbsorbG * oilKcalPerG
  };
}

function rowMacrosWithOil(entry, baseMacros){
  const o = oilForEntry(entry);
  return {
    P: n(baseMacros.P),
    C: n(baseMacros.C),
    F: n(baseMacros.F) + o.oilF,
    K: n(baseMacros.K) + o.oilK
  };
}
/* ===== END: Oil absorption helpers ===== */
function mergeFoodDef(baseDef, patchDef){
  if(!baseDef) return structuredClone ? structuredClone(patchDef) : JSON.parse(JSON.stringify(patchDef));

  // Merge unitOptions
  const baseUnits = Array.isArray(baseDef.unitOptions) ? baseDef.unitOptions : [];
  const patchUnits = Array.isArray(patchDef.unitOptions) ? patchDef.unitOptions : [];
  const units = Array.from(new Set([...baseUnits, ...patchUnits]));

  // Merge perUnit (add missing units + missing macro keys)
  const perUnit = {...(baseDef.perUnit || {})};
  const patchPerUnit = patchDef.perUnit || {};
  for(const unit of Object.keys(patchPerUnit)){
    perUnit[unit] = perUnit[unit] || {};
    perUnit[unit] = { ...patchPerUnit[unit], ...perUnit[unit] };
    // NOTE: above keeps existing values if already present. If you want patch to override numbers, flip order:
    // perUnit[unit] = { ...perUnit[unit], ...patchPerUnit[unit] };
  }

  // Merge metadata (prefer patch if provided)
  return {
    ...baseDef,
    ...patchDef,
    unitOptions: units,
    perUnit,
    source: patchDef.source ?? baseDef.source,
    confidence: patchDef.confidence ?? baseDef.confidence
  };
}

function applyFoodPatch(PATCH){
  for(const k in PATCH){
    FOOD[k] = mergeFoodDef(FOOD[k], PATCH[k]);
  }
}

/* ===========================
   Tabs
=========================== */
document.querySelectorAll(".tabbtn").forEach(btn=>{
  btn.addEventListener("click", ()=>{
    document.querySelectorAll(".tabbtn").forEach(b=>b.classList.remove("active"));
    btn.classList.add("active");
    showPage(btn.dataset.page);
    scheduleRefresh();
  });
});
function showPage(name){
  ["dashboard","profile","food","lifestyle"].forEach(p=>{
    $("page-"+p).classList.toggle("hide", p!==name);
  });
}

/* ===========================
   Chart (no blue)
=========================== */
let macroChart=null;
(function initChart(){
  const ctx=$("macroChart").getContext("2d");
  macroChart=new Chart(ctx,{
    type:"bar",
    data:{
      labels:["Protein (g)","Carbs (g)","Fat (g)"],
      datasets:[{label:"Macros", data:[0,0,0], backgroundColor:["#3ddc97","#ffcc66","#ff6b6b"]}]
    },
    options:{
      responsive:true, maintainAspectRatio:false, animation:false,
      scales:{
        y:{beginAtZero:true, ticks:{color:"rgba(238,243,255,.8)"}, grid:{color:"rgba(255,255,255,.10)"}},
        x:{ticks:{color:"rgba(238,243,255,.8)"}, grid:{display:false}}
      },
      plugins:{ legend:{labels:{color:"rgba(238,243,255,.85)"}} }
    }
  });
})();

/* ===== Debounced refresh ===== */
let refreshTimer=null;
function scheduleRefresh(){
  if(refreshTimer) clearTimeout(refreshTimer);
  refreshTimer=setTimeout(()=>{ refreshAll(); }, 120);
}

/* ===========================
   Profiles DB
=========================== */
function loadProfiles(){ try { return JSON.parse(localStorage.getItem(LS_PROFILES) || "{}"); } catch(e){ return {}; } }
function saveProfiles(map){ localStorage.setItem(LS_PROFILES, JSON.stringify(map)); }
function getActiveProfileId(){ return localStorage.getItem(LS_ACTIVE_PROFILE) || ""; }
function setActiveProfileId(id){ localStorage.setItem(LS_ACTIVE_PROFILE, id); }
function getActiveProfile(){
  const map = loadProfiles();
  const id = getActiveProfileId();
  const p = (id && map[id]) ? map[id] : null;
  return fixProfileIfNeeded(p);
}

function ensureDefaultProfile(){
  const map = loadProfiles();
  const active = getActiveProfileId();
  const ids = Object.keys(map);

  if(ids.length === 0){
    const id = uid();
    map[id] = { id, name:"Default", label:"Default", createdAt:Date.now() };
    saveProfiles(map);
    setActiveProfileId(id);
    return;
  }
  if(!active || !map[active]) setActiveProfileId(ids[0]);
}

/* ===========================
   Manual overrides (targets)
=========================== */
function setDirty(id, val=true){ $(id).dataset.dirty = val ? "1" : ""; }
function isDirty(id){ return $(id).dataset.dirty === "1"; }
function unlockManualTargets(){
  // lets user type again even if previously saved/dirty
  setDirty("p_targetCalories", true);
  setDirty("p_targetFats", true);
  setDirty("p_targetCarbs", true);
  $("profileCalcHint").style.display="none";
  $("profileSaveHint").style.display="none";
  alert("Manual targets unlocked. You can edit Calories/Fats/Carbs now.");
}

/* ===========================
   Unit readers + convert
=========================== */
/* ===== PATCH A: Robust unit readers (prevents wrong protein like 15g) ===== */
function readWeightKg(){
  const u = $("p_weight_unit").value;
  const kgField = n($("p_weight_kg").value);
  const lbsField = n($("p_weight_lbs").value);

  // If user selected lbs but typed in kg (or vice versa), still read the filled field.
  if(u === "lbs"){
    if(lbsField > 0) return lbsField * 0.45359237;
    if(kgField > 0) return kgField; // fallback
    return 0;
  }else{ // kg
    if(kgField > 0) return kgField;
    if(lbsField > 0) return lbsField * 0.45359237; // fallback
    return 0;
  }
}

function readHeightCm(){
  const u = $("p_height_unit").value;
  const cmField = n($("p_height_cm").value);
  const ftField = n($("p_height_ft").value);
  const inField = n($("p_height_in").value);

  if(u === "cm"){
    if(cmField > 0) return cmField;
    // fallback: if user typed ft/in but selector is cm
    if(ftField > 0 || inField > 0) return (ftField * 30.48 + inField * 2.54);
    return 0;
  }else{ // ftin
    if(ftField > 0 || inField > 0) return (ftField * 30.48 + inField * 2.54);
    // fallback: if user typed cm but selector is ft/in
    if(cmField > 0) return cmField;
    return 0;
  }
}

function convertWeight(from,to){
  const kg = (from==="kg") ? n($("p_weight_kg").value) : n($("p_weight_lbs").value)*0.45359237;
  if(!kg) return;
  if(to==="kg"){ $("p_weight_kg").value = round1(kg); $("p_weight_lbs").value=""; }
  else { $("p_weight_lbs").value = round1(kg/0.45359237); $("p_weight_kg").value=""; }
}
function convertHeight(from,to){
  let cm = 0;
  if(from==="cm"){ cm = n($("p_height_cm").value); }
  else { cm = n($("p_height_ft").value)*30.48 + n($("p_height_in").value)*2.54; }
  if(!cm) return;
  if(to==="cm"){ $("p_height_cm").value = round1(cm); $("p_height_ft").value=""; $("p_height_in").value=""; }
  else {
    const totalIn = cm/2.54;
    const ft = Math.floor(totalIn/12);
    const inch = Math.round(totalIn - ft*12);
    $("p_height_ft").value = ft;
    $("p_height_in").value = inch;
    $("p_height_cm").value="";
  }
}

/* ===========================
   Profile calc (works WITHOUT saving)
=========================== */
/* ===== PATCH B: Migrate old saved profiles with wrong protein targets ===== */
function computeTargetProteinFrom(wkg, protMult){
  const pm = isFinite(protMult) && protMult > 0 ? protMult : 2.2;
  const w = isFinite(wkg) ? wkg : 0;
  return (w > 0) ? Math.round((w * pm)/5)*5 : 0;
}

function fixProfileIfNeeded(p){
  if(!p) return p;

  // Derive wkg if missing
  const wkg = (p.wkg && p.wkg > 0) ? p.wkg : (
    (p.weightUnit==="lbs" && p.weight_lbs) ? (n(p.weight_lbs)*0.45359237) :
    (p.weight_kg ? n(p.weight_kg) : 0)
  );

  const correctedProtein = computeTargetProteinFrom(wkg, p.protMult || 2.2);

  // If targetProtein is missing or suspiciously low while weight exists, fix it.
  if(wkg > 20 && (!p.targetProtein || p.targetProtein < 50)){
    p.wkg = wkg;
    p.targetProtein = correctedProtein;
  }

  return p;
}

function getProfileDraft(){
  const sex=$("p_sex").value;
  const age=n($("p_age").value);

  const wkg = readWeightKg();
  const hcm = readHeightCm();
  const hm = hcm/100;

  const bmi=(wkg>0&&hm>0)?(wkg/(hm*hm)):0;

  let bmr=0;
  if(wkg>0 && hcm>0 && age>0){
    bmr = (10*wkg) + (6.25*hcm) - (5*age) + (sex==="male"?5:-161);
  }

  const activity=n($("p_activity").value);
  const tdee=bmr?bmr*activity:0;
  const basicsOk = (wkg>0 && hcm>0 && age>0 && tdee>0);

  const protMult = n($("p_protMult").value) || 2.2;
  const targetProtein = (wkg>0) ? Math.round((wkg * protMult)/5)*5 : 0;

  let targetCaloriesAuto = 0;
  if (basicsOk) {
    const goal = $("p_goal").value;
    const adj = goal === "gain" ? 400 : goal === "loss" ? -500 : 0;
    targetCaloriesAuto = Math.round(tdee + adj);
  }

  const manualCal = n($("p_targetCalories").value);
  const manualFat = n($("p_targetFats").value);
  const manualCarb = n($("p_targetCarbs").value);

  const targetCalories = (isDirty("p_targetCalories") && manualCal>0)
    ? Math.max(800, Math.round(manualCal))
    : targetCaloriesAuto;

  let fatPct = 0.30; // balanced
  const preset=$("p_macroPreset").value;
  // FIX: higherCarb and higherProtein previously both used 0.25 (identical results,
  // so "Higher carb" and "Higher protein" produced the same macro split). Now distinct.
  if(preset==="higherCarb") fatPct = 0.20;
  if(preset==="higherProtein") fatPct = 0.25;

  let fatsAuto = 0;
  if(targetCalories>0) fatsAuto = Math.round((targetCalories * fatPct)/9);

  let targetFats = (isDirty("p_targetFats") && manualFat>=0)
    ? Math.max(0, Math.round(manualFat))
    : fatsAuto;

  let carbsAuto = 0;
  if(targetCalories>0 && targetProtein>0){
    let remaining = targetCalories - (targetProtein*4) - (targetFats*9);
    if(remaining < 0){
      targetFats = Math.max(0, Math.floor((targetCalories - targetProtein*4)/9));
      remaining = Math.max(0, targetCalories - (targetProtein*4) - (targetFats*9));
    }
    carbsAuto = Math.max(0, Math.round(remaining/4));
  }

  const targetCarbs = (isDirty("p_targetCarbs") && manualCarb>=0)
    ? Math.max(0, Math.round(manualCarb))
    : carbsAuto;

  return {
    id: getActiveProfileId(),
    label: ($("p_name").value || "").trim() || "Profile",
    name:$("p_name").value||"",
    sex, age,
    heightUnit:$("p_height_unit").value,
    weightUnit:$("p_weight_unit").value,
    height_cm:n($("p_height_cm").value),
    height_ft:n($("p_height_ft").value),
    height_in:n($("p_height_in").value),
    weight_kg:n($("p_weight_kg").value),
    weight_lbs:n($("p_weight_lbs").value),
    wkg, hm, bmi, bmr, tdee,
    goal:$("p_goal").value,
    activity,
    dayStartHour:clamp(parseInt($("p_dayStartHour").value||"4",10),0,23),
    macroPreset:$("p_macroPreset").value,
    protMult,
    targetCalories,
    targetProtein,
    targetFats,
    targetCarbs,
    dirtyCalories:isDirty("p_targetCalories"),
    dirtyFats:isDirty("p_targetFats"),
    dirtyCarbs:isDirty("p_targetCarbs"),
  };
}

/* ===== Profile UI toggles ===== */
function toggleHeightUI(doRecalc=true){
  const u=$("p_height_unit").value;
  $("height_cm_row").classList.toggle("hide", u!=="cm");
  $("height_ftin_row").classList.toggle("hide", u!=="ftin");
  if(doRecalc) updateProfilePreviewOnly();
}
function toggleWeightUI(doRecalc=true){
  const u=$("p_weight_unit").value;
  $("weight_kg_row").classList.toggle("hide", u!=="kg");
  $("weight_lbs_row").classList.toggle("hide", u!=="lbs");
  if(doRecalc) updateProfilePreviewOnly();
}

$("p_height_unit").addEventListener("change", ()=>{
  const to = $("p_height_unit").value;
  convertHeight(to==="cm" ? "ftin" : "cm", to);
  toggleHeightUI(true);
});
$("p_weight_unit").addEventListener("change", ()=>{
  const to = $("p_weight_unit").value;
  convertWeight(to==="kg" ? "lbs" : "kg", to);
  toggleWeightUI(true);
});

/* ===== Profile preview updates ===== */
function updateProfilePreviewOnly(){
  const p=getProfileDraft();

  $("p_bmiLine").textContent = p.bmi? round2(p.bmi) : "—";
  $("p_bmrLine").textContent = p.bmr? Math.round(p.bmr)+" kcal" : "—";
  $("p_tdeeLine").textContent = p.tdee? Math.round(p.tdee)+" kcal" : "—";
  $("p_targetLine").textContent = "Target: " + (p.targetCalories?Math.round(p.targetCalories)+" kcal":"—");

  $("p_targetProtein").value = p.targetProtein ? p.targetProtein : "";

  // auto-fill only if NOT dirty (so manual stays editable)
  if(!isDirty("p_targetCalories") && p.targetCalories) $("p_targetCalories").value = p.targetCalories;
  if(!isDirty("p_targetFats") && p.targetFats>=0) $("p_targetFats").value = p.targetFats;
  if(!isDirty("p_targetCarbs") && p.targetCarbs>=0) $("p_targetCarbs").value = p.targetCarbs;

  scheduleRefresh();
}

function calculateTargetsOnly(){
  // Make sure targets compute even before saving
  setDirty("p_targetCalories", false);
  setDirty("p_targetFats", false);
  setDirty("p_targetCarbs", false);

  $("p_targetCalories").value = "";
  $("p_targetFats").value = "";
  $("p_targetCarbs").value = "";

  $("profileCalcHint").style.display="block";
  $("profileSaveHint").style.display="none";

  updateProfilePreviewOnly();
  refreshAll();
}

function saveProfile(){
  try{
    const p=getProfileDraft();
    const map = loadProfiles();
    if(!p.id){
      const id = uid();
      p.id = id;
      setActiveProfileId(id);
    }
    map[p.id] = { ...p, id:p.id, label:(p.name || map[p.id]?.label || "Profile"), updatedAt:Date.now() };
    saveProfiles(map);

    $("profileSaveHint").style.display="block";
    $("profileCalcHint").style.display="none";
    $("profileSavedNote").style.display="block";

    rebuildProfileSelect();
    refreshAll();
  }catch(e){
    alert("Could not save profile. Your browser storage may be restricted.");
  }
}

function loadProfileToUI(){
  const p = getActiveProfile();
  // PATCH B: persist migration fix (so it stays correct permanently)
try{
  const map = loadProfiles();
  if(p?.id && map[p.id]){
    map[p.id] = { ...map[p.id], ...p, updatedAt: Date.now() };
    saveProfiles(map);
  }
}catch(e){}

  if(!p) return null;

  $("p_name").value=p.name||"";
  $("p_age").value=p.age||"";
  $("p_sex").value=p.sex||"male";

  $("p_height_unit").value=p.heightUnit||"ftin";
  $("p_weight_unit").value=p.weightUnit||"kg";

  $("p_height_cm").value=p.height_cm||"";
  $("p_height_ft").value=p.height_ft||"";
  $("p_height_in").value=p.height_in||"";

  $("p_weight_kg").value=p.weight_kg||"";
  $("p_weight_lbs").value=p.weight_lbs||"";

  $("p_goal").value=p.goal||"loss";
  $("p_activity").value=(p.activity||1.2).toString();
  $("p_dayStartHour").value=(p.dayStartHour ?? 4);
  $("p_macroPreset").value=p.macroPreset||"higherProtein";
  $("p_protMult").value=(p.protMult || 2.2).toString();

  setDirty("p_targetCalories", !!p.dirtyCalories);
  setDirty("p_targetFats", !!p.dirtyFats);
  setDirty("p_targetCarbs", !!p.dirtyCarbs);

  // keep manual values if dirty, else blank so auto shows
  $("p_targetCalories").value = p.dirtyCalories && p.targetCalories ? Math.round(p.targetCalories) : "";
  $("p_targetFats").value = p.dirtyFats && (p.targetFats>=0) ? Math.round(p.targetFats) : "";
  $("p_targetCarbs").value = p.dirtyCarbs && (p.targetCarbs>=0) ? Math.round(p.targetCarbs) : "";

  $("profileSavedNote").style.display="block";

  toggleHeightUI(false);
  toggleWeightUI(false);
  updateProfilePreviewOnly();
  return p;
}

function rebuildProfileSelect(){
  const sel = $("profileSelect");
  const map = loadProfiles();
  const active = getActiveProfileId();

  sel.innerHTML = "";
  const ids = Object.keys(map);
  if(ids.length === 0){
    const opt = document.createElement("option");
    opt.value = "";
    opt.textContent = "No profiles yet";
    sel.appendChild(opt);
    return;
  }
  ids.forEach(id=>{
    const opt = document.createElement("option");
    opt.value = id;
    opt.textContent = map[id]?.name ? map[id].name : (map[id]?.label || "Profile");
    sel.appendChild(opt);
  });
  sel.value = active || ids[0];
}

$("profileSelect").addEventListener("change", ()=>{
  const id = $("profileSelect").value;
  if(!id) return;
  setActiveProfileId(id);
  loadProfileToUI();
  scheduleRefresh();
});

function createProfile(){
  const name = ($("newProfileName").value || "").trim() || "New Profile";
  const map = loadProfiles();
  const id = uid();
  map[id] = { id, name, label:name, createdAt:Date.now() };
  saveProfiles(map);
  setActiveProfileId(id);

  $("newProfileName").value = "";
  rebuildProfileSelect();
  loadProfileToUI();

  $("profileCalcHint").style.display="none";
  $("profileSaveHint").style.display="none";
  scheduleRefresh();
}

function resetCurrentProfile(){
  const id = getActiveProfileId();
  if(!id) return;
  if(!confirm("Reset the current profile data?")) return;

  const map = loadProfiles();
  map[id] = { id, name: (map[id]?.name || "Profile"), label:(map[id]?.label || "Profile"), createdAt:(map[id]?.createdAt||Date.now()) };
  saveProfiles(map);

  ["p_name","p_age","p_height_cm","p_height_ft","p_height_in","p_weight_kg","p_weight_lbs"].forEach(x=>$(x).value="");
  $("p_goal").value="loss";
  $("p_activity").value="1.2";
  $("p_dayStartHour").value="4";
  $("p_sex").value="male";
  $("p_macroPreset").value="higherProtein";
  $("p_protMult").value="2.2";

  setDirty("p_targetCalories", false);
  setDirty("p_targetFats", false);
  setDirty("p_targetCarbs", false);
  $("p_targetCalories").value="";
  $("p_targetFats").value="";
  $("p_targetCarbs").value="";
  $("p_targetProtein").value="";

  $("profileSavedNote").style.display="none";
  $("profileCalcHint").style.display="none";
  $("profileSaveHint").style.display="none";

  toggleHeightUI(false);
  toggleWeightUI(false);
  updateProfilePreviewOnly();
  rebuildProfileSelect();
  scheduleRefresh();
}

[
 "p_name","p_age","p_height_cm","p_height_ft","p_height_in","p_weight_kg","p_weight_lbs",
 "p_goal","p_activity","p_dayStartHour","p_sex","p_macroPreset","p_protMult"
].forEach(id=>{
  $(id).addEventListener("input", updateProfilePreviewOnly);
  $(id).addEventListener("change", updateProfilePreviewOnly);
});
["p_targetCalories","p_targetFats","p_targetCarbs"].forEach(id=>{
  $(id).addEventListener("input", ()=>{
    setDirty(id, true);
    $("profileCalcHint").style.display="none";
    $("profileSaveHint").style.display="none";
    scheduleRefresh();
  });
});

/* ===========================
   Day start hour (defines "today")
=========================== */
function getDefaultLogDate(profile){
  const now=new Date();
  const dayStart=profile?.dayStartHour ?? 4;
  const adjusted=new Date(now);
  if(now.getHours()<dayStart) adjusted.setDate(now.getDate()-1);
  return adjusted.toISOString().slice(0,10);
}

/* ===========================
   Food Logs storage
=========================== */
function loadAllLogs(){ try{ return JSON.parse(localStorage.getItem(LS_LOG) || "{}"); }catch(e){ return {}; } }
function saveAllLogs(obj){ localStorage.setItem(LS_LOG, JSON.stringify(obj)); }
function getDayLog(dateKey){ const all=loadAllLogs(); return all[dateKey] || { entries: [] }; }
function setDayLog(dateKey, dayLog){ const all=loadAllLogs(); all[dateKey]=dayLog; saveAllLogs(all); }

/* ===========================
   FOOD DB (Base) + PATCHES 1..6
   Patch list:
   1) Missing foods (BD meals + chicken parts + nuts/dates)
   2) Fruits separated from Snacks
   3) Search + filter works across categories (incl. All)
   4) Custom food entry (saved to localStorage)
   5) Portion presets (½, 1, 1.5 plate)
   6) Confidence score + source tag shown in UI/table
=========================== */

/* Base foods (you can expand anytime) */
let FOOD = {
  // Chicken & Poultry
  "Chicken Breast (skinless)": { unitOptions:["g"], perUnit:{ g:{P:31/100,C:0,F:3.6/100,K:165/100} }, source:"USDA avg", confidence:"High" },
  "Chicken Thigh (skinless)": { unitOptions:["g"], perUnit:{ g:{P:26/100,C:0,F:8/100,K:180/100} }, source:"USDA avg", confidence:"High" },
  "Chicken Leg (drumstick)": { unitOptions:["g"], perUnit:{ g:{P:25/100,C:0,F:7/100,K:170/100} }, source:"USDA avg", confidence:"High" },
  "Chicken Wing": { unitOptions:["g"], perUnit:{ g:{P:24/100,C:0,F:13/100,K:203/100} }, source:"USDA avg", confidence:"High" },
  "Chicken (with skin)": { unitOptions:["g"], perUnit:{ g:{P:23/100,C:0,F:14/100,K:215/100} }, source:"USDA avg", confidence:"High" },
  /* ===== BREAD & BUN (CARBS) ===== */
  /* ===== BREAD (WHITE vs BROWN) ===== */

  "White Bread Slice": {
    unitOptions:["pcs","g"],
    perUnit:{
      pcs:{P:3, C:15, F:1.2, K:80},
      g:{P:0.09, C:0.45, F:0.035, K:2.65}
    },
    source:"USDA/bakery avg",
    confidence:"Medium"
  },

  "Brown Bread Slice": {
    unitOptions:["pcs","g"],
    perUnit:{
      pcs:{P:4, C:14, F:1.4, K:85},
      g:{P:0.11, C:0.41, F:0.04, K:2.45}
    },
    source:"USDA/bakery avg",
    confidence:"Medium"
  },

  "Bread Slice": {
    unitOptions:["pcs","g"],
    perUnit:{
      pcs:{P:3, C:15, F:1.5, K:80},     // 1 standard slice
      g:{P:0.09, C:0.45, F:0.04, K:2.7}
    },
    source:"Bakery / USDA avg",
    confidence:"Medium"
  },

  "Butter Bun": {
    unitOptions:["pcs","g"],
    perUnit:{
      pcs:{P:6, C:35, F:9, K:250},      // 1 butter bun / pav
      g:{P:0.12, C:0.70, F:0.18, K:5.0}
    },
    source:"BD bakery avg",
    confidence:"Low"
  },

  // Meat
  "Beef (cooked)": { unitOptions:["g"], perUnit:{ g:{P:26/100,C:0,F:15/100,K:250/100} }, source:"USDA avg", confidence:"High" },
  "Mutton (cooked)": { unitOptions:["g"], perUnit:{ g:{P:25/100,C:0,F:21/100,K:294/100} }, source:"USDA avg", confidence:"High" },
  "Lamb (cooked)": { unitOptions:["g"], perUnit:{ g:{P:25/100,C:0,F:20/100,K:282/100} }, source:"USDA avg", confidence:"High" },
  "Duck (cooked)": { unitOptions:["g"], perUnit:{ g:{P:19/100,C:0,F:28/100,K:337/100} }, source:"USDA avg", confidence:"High" },

  // Fish
  "Fish (generic)": { unitOptions:["g"], perUnit:{ g:{P:22/100,C:0,F:5/100,K:120/100} }, source:"USDA avg", confidence:"Medium" },
  "Hilsa Fish": { unitOptions:["g"], perUnit:{ g:{P:22/100,C:0,F:12/100,K:180/100} }, source:"BD fish avg", confidence:"Medium" },

  // Eggs & Dairy
    "Cheddar Cheese": { unitOptions:["g"], perUnit:{ g:{P:25/100,C:1.3/100,F:33/100,K:403/100} }, source:"USDA avg", confidence:"High" },
  "Mozzarella Cheese": { unitOptions:["g"], perUnit:{ g:{P:22/100,C:2.2/100,F:22/100,K:300/100} }, source:"USDA avg", confidence:"High" },
  "Processed Cheese Slice": { unitOptions:["slice"], perUnit:{ slice:{P:4,C:1,F:6,K:80} }, source:"label avg", confidence:"Medium" },
  "Egg (whole)": { unitOptions:["pcs"], perUnit:{ pcs:{P:6,C:0.6,F:5,K:72} }, source:"USDA", confidence:"High" },
  "Egg White": { unitOptions:["pcs"], perUnit:{ pcs:{P:3.6,C:0.2,F:0,K:17} }, source:"USDA", confidence:"High" },
  "Yogurt / Curd": { unitOptions:["g"], perUnit:{ g:{P:3.5/100,C:4.7/100,F:3.3/100,K:61/100} }, source:"USDA avg", confidence:"High" },

  // Daal
  "Daal (cooked)": { unitOptions:["cup"], perUnit:{ cup:{P:18,C:40,F:1,K:230} }, source:"lentils avg", confidence:"Medium" },
  "Daal (fried/tadka)": { unitOptions:["cup"], perUnit:{ cup:{P:17,C:38,F:6,K:290} }, source:"lentils+oil avg", confidence:"Medium" },

  // Rice & Meals
    "Polau / Pulao": { unitOptions:["plate"], perUnit:{ plate:{P:10,C:85,F:12,K:520} }, source:"BD home avg", confidence:"Low" },
  // Rice
"Rice (cooked)": { 
  unitOptions:["g","plate"], 
  perUnit:{
    g:{P:2.7/100,C:28/100,F:0.3/100,K:130/100},
    plate:{P:8,C:90,F:1,K:420}
  }, 
  source:"USDA + portion est", 
  confidence:"Medium" 
},

  "Chicken Biryani": { unitOptions:["plate"], perUnit:{ plate:{P:25,C:85,F:18,K:650} }, source:"BD restaurant avg", confidence:"Low" },
  "Kacchi Biryani": { unitOptions:["plate"], perUnit:{ plate:{P:28,C:90,F:28,K:800} }, source:"BD restaurant avg", confidence:"Low" },
  "Beef Biryani": { unitOptions:["plate"], perUnit:{ plate:{P:30,C:85,F:25,K:780} }, source:"BD restaurant avg", confidence:"Low" },

  // Roti
  "Roti": { unitOptions:["pcs"], perUnit:{ pcs:{P:3.5,C:18,F:3,K:120} }, source:"avg", confidence:"Medium" },
  "Paratha": { unitOptions:["pcs"], perUnit:{ pcs:{P:6,C:30,F:12,K:260} }, source:"avg", confidence:"Medium" },

  // Vegetables
    "Potato (boiled)": { unitOptions:["g"], perUnit:{ g:{P:2/100,C:17/100,F:0.1/100,K:87/100} }, source:"USDA avg", confidence:"High" },
  "Potato (fried/fries)": { unitOptions:["g"], perUnit:{ g:{P:3.4/100,C:41/100,F:15/100,K:312/100} }, source:"USDA avg", confidence:"Medium" },
  "Mixed Vegetables": { unitOptions:["cup"], perUnit:{ cup:{P:2,C:5,F:0.2,K:35} }, source:"avg", confidence:"Medium" },
  "Salad": { unitOptions:["cup"], perUnit:{ cup:{P:0.5,C:2,F:0.1,K:15} }, source:"avg", confidence:"High" },

  // Fruits
  "Banana": { unitOptions:["pcs"], perUnit:{ pcs:{P:1.3,C:27,F:0.4,K:105} }, source:"USDA", confidence:"High" },
  "Apple": { unitOptions:["pcs"], perUnit:{ pcs:{P:0.5,C:25,F:0.3,K:95} }, source:"USDA", confidence:"High" },
  "Orange": { unitOptions:["pcs"], perUnit:{ pcs:{P:1.2,C:15.4,F:0.2,K:62} }, source:"USDA", confidence:"High" },
  "Mango": { unitOptions:["cup"], perUnit:{ cup:{P:1.4,C:25,F:0.6,K:99} }, source:"USDA", confidence:"High" },

  // Snacks (base)
  "Peanuts": { unitOptions:["g"], perUnit:{ g:{P:25.8/100,C:16.1/100,F:49.2/100,K:567/100} }, source:"USDA", confidence:"High" },
  "Almonds": { unitOptions:["g"], perUnit:{ g:{P:21.2/100,C:21.6/100,F:49.9/100,K:579/100} }, source:"USDA", confidence:"High" },
  "Biscuits": { unitOptions:["serving"], perUnit:{ serving:{P:2,C:20,F:6,K:140} }, source:"label avg", confidence:"Medium" },

  // Drinks
  "Milk Tea": { unitOptions:["cup"], perUnit:{ cup:{P:2,C:12,F:3,K:90} }, source:"recipe avg", confidence:"Medium" },
  "Black Tea (with sugar)": { unitOptions:["cup"], perUnit:{ cup:{P:0,C:10,F:0,K:40} }, source:"recipe avg", confidence:"Medium" },
  "Black Coffee": { unitOptions:["cup"], perUnit:{ cup:{P:0, C:0, F:0, K:2} }, source:"USDA", confidence:"High" },
  "Coffee with milk & sugar": { unitOptions:["cup"], perUnit:{ cup:{P:3,C:14,F:4,K:120} }, source:"recipe avg", confidence:"Medium" },

  // Desserts
  "Cake slice": { unitOptions:["slice"], perUnit:{ slice:{P:4,C:45,F:15,K:330} }, source:"bakery avg", confidence:"Low" },
  "Chocolate cube": { unitOptions:["pcs"], perUnit:{ pcs:{P:1,C:8,F:6,K:90} }, source:"label avg", confidence:"Medium" },
  "Ice Cream": { unitOptions:["cup"], perUnit:{ cup:{P:5,C:30,F:14,K:270} }, source:"label avg", confidence:"Medium" },
  "Firni": { unitOptions:["cup"], perUnit:{ cup:{P:6,C:45,F:8,K:280} }, source:"BD dessert avg", confidence:"Low" },
  "Jorda": { unitOptions:["plate"], perUnit:{ plate:{P:6,C:65,F:12,K:380} }, source:"BD dessert avg", confidence:"Low" }
};

/* Base categories */
let CATEGORY_ITEMS = {
  "All": [], // filled at runtime

  "Chicken & Poultry": [
    "Chicken Breast (skinless)","Chicken Thigh (skinless)",
    "Chicken Leg (drumstick)","Chicken Wing","Chicken (with skin)"
  ],
  "Meat": ["Beef (cooked)","Mutton (cooked)","Lamb (cooked)","Duck (cooked)"],
  "Fish": ["Fish (generic)","Hilsa Fish"],

  "Eggs & Dairy": [
    "Egg (whole)",
    "Egg White",
    "Yogurt / Curd",
    "Cheddar Cheese",
    "Mozzarella Cheese",
    "Processed Cheese Slice"
  ],

  "Daal / Lentils": ["Daal (cooked)","Daal (fried/tadka)"],

 "Rice & Meals": [
  "Khichuri (plain)",
  "Chicken Biryani",
  "Kacchi Biryani",
  "Beef Biryani"
],


"Carbs": [
  "Rice (cooked)",
  "Polau / Pulao",
  "Roti",
  "Paratha",
  "White Bread Slice",
  "Brown Bread Slice",
  "Butter Bun"
],



  "Roti / Bread": ["Roti","Paratha"],
  "Vegetables": ["Mixed Vegetables","Salad","Potato (boiled)","Potato (fried/fries)"],
  "Fruits": ["Banana","Apple","Orange","Mango"],
  "Snacks": ["Peanuts","Almonds","Biscuits"],
  "Drinks": ["Milk Tea","Black Tea (with sugar)","Black Coffee","Coffee with milk & sugar"],
  "Desserts": ["Cake slice","Chocolate cube","Ice Cream","Firni","Jorda"]
};
  
/* Step 3 — Build “All” automatically (critical) */
(function buildAllCategory(){
  CATEGORY_ITEMS["All"] = Object.keys(FOOD).slice().sort();
})();

/* ===========================
   CATEGORY VIEW (Simplify UX) — SAFE
   - DOES NOT overwrite CATEGORY_ITEMS
   - Builds CATEGORY_VIEW for dropdown only
   - Guarantees no missing foods via "Uncategorized"
=========================== */

let CATEGORY_VIEW = {}; // MUST exist globally
const CATEGORY_MERGE_MAP = {};

const CATEGORY_VIEW_MAP = {
  "All": ["All"],

  /* Core buckets */
  "Carbs": ["Carbs", "Carbs (BD)"],
  "Rice & Meals": ["Rice & Meals", "Meals (BD)", "Meals (Mixed)", "BD Takeout"],
  "Eggs & Dairy": ["Eggs & Dairy", "Cheese & Dairy"],

  "Chicken & Poultry": ["Chicken & Poultry", "Chicken (Parts)", "Poultry / Game"],
  "Meat": ["Meat", "Offal / Traditional"],
  "Fish": ["Fish", "Sea Fish", "River Fish", "Small Fish", "Seafood (Shellfish)"],

  "Vegetables": ["Vegetables", "Potato", "BD Sides / Bhaji", "Soups"],
  "Fruits": ["Fruits"],

  "Daal / Lentils": ["Daal / Lentils"],

  "Snacks": ["Snacks", "BD Street Food / Snacks", "BD Snacks", "Nuts & Dates"],
  "Drinks": ["Drinks"],
  "Desserts": ["Desserts", "Pitha / Sweets"],

  /* Optional: show oils as its own dropdown bucket.
     If you don't want a separate dropdown item, map "Fats & Oils" into Snacks or Meals instead. */
  "Fats & Oils": ["Fats & Oils"]
};


function buildCategoryView(){
  const uniq = (arr)=>Array.from(new Set(arr)).filter(Boolean);

  // Always rebuild "All" from FOOD keys (single source of truth)
  const allFoods = Object.keys(FOOD).slice().sort((a,b)=>a.localeCompare(b));
  CATEGORY_VIEW = { "All": allFoods };

  // Build each view category by mapping to CATEGORY_ITEMS categories
  Object.keys(CATEGORY_VIEW_MAP).forEach(viewCat=>{
    if(viewCat === "All") return;

    const rawCats = CATEGORY_VIEW_MAP[viewCat] || [];
    let items = [];

    rawCats.forEach(rc=>{
      if(Array.isArray(CATEGORY_ITEMS[rc])) items = items.concat(CATEGORY_ITEMS[rc]);
    });

    // keep only items that exist in FOOD
    CATEGORY_VIEW[viewCat] = uniq(items).filter(name => FOOD[name]);
  });

  // Create Uncategorized = food keys not used in any view category
  const used = new Set();
  Object.keys(CATEGORY_VIEW).forEach(vc=>{
    if(vc === "All") return;
    (CATEGORY_VIEW[vc] || []).forEach(x=>used.add(x));
  });

  const uncategorized = allFoods.filter(x=>!used.has(x));
  if(uncategorized.length) CATEGORY_VIEW["Uncategorized"] = uncategorized;
}


function mergeCategories(){
  const uniq = (arr) => Array.from(new Set(arr)).filter(Boolean);

  // Keep a snapshot of current categories before we rebuild
  const old = CATEGORY_ITEMS || {};

  const merged = {};
  merged["All"] = [];

  // 1) Build merged categories strictly from CATEGORY_MERGE_MAP
  Object.keys(CATEGORY_MERGE_MAP).forEach(newCat=>{
    const oldCats = CATEGORY_MERGE_MAP[newCat] || [];
    let items = [];
    oldCats.forEach(oc=>{
      if(Array.isArray(old[oc])) items = items.concat(old[oc]);
    });
    merged[newCat] = uniq(items.filter(name => FOOD[name]));
  });

  // 2) Preserve any old categories NOT referenced by CATEGORY_MERGE_MAP
  const mappedOldCats = new Set(Object.values(CATEGORY_MERGE_MAP).flat());
  Object.keys(old).forEach(oldCat=>{
    if(oldCat === "All") return;
    if(mappedOldCats.has(oldCat)) return;
    merged[oldCat] = uniq((old[oldCat] || []).filter(name => FOOD[name]));
  });

  // 3) FAIL-SAFE: If a critical category went empty, restore it from old (prevents “Eggs & Dairy disappeared”)
  const criticalCats = ["Eggs & Dairy", "Vegetables", "Fruits", "Snacks", "Drinks", "Desserts & Sweets", "Desserts"];
  criticalCats.forEach(cat=>{
    if(!merged[cat] || merged[cat].length === 0){
      if(Array.isArray(old[cat]) && old[cat].length){
        merged[cat] = uniq(old[cat].filter(name => FOOD[name]));
      }
    }
  });

  // 4) Rebuild "All"
  merged["All"] = Object.keys(FOOD).sort((a,b)=>a.localeCompare(b));

  // 5) Create "Uncategorized" ONLY for foods not present in ANY category (real uncategorized)
  const used = new Set();
  Object.keys(merged).forEach(cat=>{
    if(cat === "All") return;
    (merged[cat] || []).forEach(name=>used.add(name));
  });
  const uncategorized = merged["All"].filter(name => !used.has(name));
  if(uncategorized.length){
    merged["Uncategorized"] = uncategorized;
  }else{
    delete merged["Uncategorized"];
  }

  CATEGORY_ITEMS = merged;
 
}

/* PATCH 1: Extensions (adds missing foods without overwriting existing keys) */
const FOOD_PATCH_1 = {
  // more chicken parts (cooked)
  "Chicken Thigh (cooked)": { unitOptions:["g"], perUnit:{ g:{P:26/100,C:0,F:8/100,K:209/100} }, source:"USDA avg", confidence:"High" },
  "Chicken Wing (cooked)": { unitOptions:["g"], perUnit:{ g:{P:24/100,C:0,F:13/100,K:290/100} }, source:"USDA avg", confidence:"High" },
  "Chicken Leg/Drumstick (cooked)": { unitOptions:["g"], perUnit:{ g:{P:25/100,C:0,F:9/100,K:216/100} }, source:"USDA avg", confidence:"High" },

  // popular BD meals
  "Khichuri (plain)": { unitOptions:["plate"], perUnit:{ plate:{P:14,C:70,F:10,K:420} }, source:"BD home avg", confidence:"Low" },

  // nuts + dates (commonly missing)
  "Cashews": { unitOptions:["g"], perUnit:{ g:{P:18.2/100,C:30.2/100,F:43.9/100,K:553/100} }, source:"USDA", confidence:"High" },
  "Walnuts": { unitOptions:["g"], perUnit:{ g:{P:15.2/100,C:13.7/100,F:65.2/100,K:654/100} }, source:"USDA", confidence:"High" },
  "Pistachios": { unitOptions:["g"], perUnit:{ g:{P:20.2/100,C:27.2/100,F:45.4/100,K:562/100} }, source:"USDA", confidence:"High" },
  "Hazelnuts": { unitOptions:["g"], perUnit:{ g:{P:15/100,C:17/100,F:61/100,K:628/100} }, source:"USDA", confidence:"High" },

  "Dates (Deglet Noor)": { unitOptions:["pcs","g"], perUnit:{ pcs:{P:0.2,C:5.3,F:0,K:20}, g:{P:1.8/100,C:75/100,F:0.2/100,K:282/100} }, source:"USDA avg", confidence:"Medium" },
  "Dates (Medjool)": { unitOptions:["pcs","g"], perUnit:{ pcs:{P:0.4,C:18,F:0,K:66}, g:{P:1.8/100,C:75/100,F:0.2/100,K:277/100} }, source:"USDA avg", confidence:"Medium" },

  // drinks
  "Coffee w/ Milk + Sugar": { unitOptions:["cup"], perUnit:{ cup:{P:3,C:14,F:4,K:120} }, source:"recipe avg", confidence:"Medium" }
};

const CATEGORY_PATCH_1 = {
  "Chicken (Parts)": ["Chicken Thigh (cooked)","Chicken Wing (cooked)","Chicken Leg/Drumstick (cooked)"],
  "Meals (BD)": [
  "Chicken Biryani",
  "Kacchi Biryani",
  "Beef Biryani"
],

  "Nuts & Dates": ["Cashews","Walnuts","Pistachios","Hazelnuts","Dates (Deglet Noor)","Dates (Medjool)"],
  "Drinks": ["Coffee w/ Milk + Sugar"]
};
  /* PATCH 3: Mixed Meals + BD Takeout + Soft Drinks */
const FOOD_PATCH_3 = {

  /* ===== MEALS (MIXED) ===== */
  "Beef Steak (grilled)": {
    unitOptions:["g","serving"],
    perUnit:{
      g:{P:0.27,C:0,F:0.12,K:2.5},         // ~250 kcal /100g
      serving:{P:40,C:0,F:18,K:420}        // ~150g cooked portion
    },
    source:"USDA avg (grilled steak)",
    confidence:"Medium"
  },

  "Grilled Chicken (mixed pieces)": {
    unitOptions:["g","serving"],
    perUnit:{
      g:{P:0.27,C:0,F:0.07,K:1.65},        // ~165 kcal /100g
      serving:{P:40,C:0,F:10,K:250}        // ~150g cooked portion
    },
    source:"USDA avg (grilled chicken)",
    confidence:"Medium"
  },

  "Noodles (cooked)": {
    unitOptions:["g","bowl","plate"],
    perUnit:{
      g:{P:0.05,C:0.25,F:0.01,K:1.38},     // ~138 kcal /100g
      bowl:{P:12,C:60,F:4,K:320},          // bowl estimate
      plate:{P:14,C:75,F:6,K:390}          // plate estimate
    },
    source:"Avg cooked noodles + oil estimate",
    confidence:"Low"
  },

  "Pasta (cooked)": {
    unitOptions:["g","bowl","plate"],
    perUnit:{
      g:{P:0.05,C:0.30,F:0.01,K:1.58},     // ~158 kcal /100g
      bowl:{P:11,C:55,F:4,K:310},
      plate:{P:14,C:75,F:6,K:420}
    },
    source:"Avg cooked pasta + oil estimate",
    confidence:"Low"
  },

  "Pizza (1 slice)": {
    unitOptions:["slice"],
    perUnit:{ slice:{P:12,C:34,F:10,K:285} },
    source:"Label/restaurant avg",
    confidence:"Low"
  },

  "Burger (single, generic)": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:25,C:33,F:18,K:430} },
    source:"Fast food avg",
    confidence:"Low"
  },

  "Burger King Whopper": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:28,C:49,F:18,K:660} },
    source:"Restaurant nutrition avg",
    confidence:"Medium"
  },

  "Burger King Double Whopper": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:48,C:49,F:32,K:900} },
    source:"Restaurant nutrition avg",
    confidence:"Medium"
  },

  "KFC Chicken Bucket (8 pcs estimate)": {
    unitOptions:["bucket","pcs"],
    perUnit:{
      bucket:{P:160,C:60,F:140,K:2200},    // very rough bucket estimate
      pcs:{P:20,C:7.5,F:17.5,K:275}        // per piece rough avg
    },
    source:"Takeout estimate (varies a lot by pieces/breading)",
    confidence:"Low"
  },

  /* ===== CARBS (BD) ===== */
  "Polau / Pulao": {
    unitOptions:["plate","g"],
    perUnit:{
      g:{P:0.03,C:0.25,F:0.04,K:1.65},     // ~165 kcal /100g (oil + rice)
      plate:{P:9,C:80,F:12,K:520}          // typical plate estimate
    },
    source:"BD home/restaurant avg",
    confidence:"Low"
  },

  /* ===== BD TAKEOUT (BRAND ITEMS - ESTIMATES) ===== */
  "Chilox Burger (avg)": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:28,C:40,F:22,K:560} },
    source:"BD takeout estimate",
    confidence:"Low"
  },

  "Madchef Burger (avg)": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:30,C:42,F:24,K:600} },
    source:"BD takeout estimate",
    confidence:"Low"
  },

  /* ===== DRINKS (SOFT DRINKS) ===== */
  "Coke (330ml can)": {
    unitOptions:["can"],
    perUnit:{ can:{P:0,C:35,F:0,K:140} },
    source:"Label avg",
    confidence:"High"
  },

  "Sprite (330ml can)": {
    unitOptions:["can"],
    perUnit:{ can:{P:0,C:38,F:0,K:150} },
    source:"Label avg",
    confidence:"High"
  },

  "Diet Coke (330ml can)": {
    unitOptions:["can"],
    perUnit:{ can:{P:0,C:0,F:0,K:1} },
    source:"Label avg",
    confidence:"High"
  }
};

const CATEGORY_PATCH_3 = {
  "Meals (Mixed)": [
    "Beef Steak (grilled)",
    "Grilled Chicken (mixed pieces)",
    "Noodles (cooked)",
    "Pasta (cooked)",
    "Pizza (1 slice)",
    "Burger (single, generic)",
    "Burger King Whopper",
    "Burger King Double Whopper",
    "KFC Chicken Bucket (8 pcs estimate)"
  ],
  "Carbs (BD)": ["Polau / Pulao"],
  "BD Takeout": ["Chilox Burger (avg)", "Madchef Burger (avg)"],
  "Drinks": ["Coke (330ml can)", "Sprite (330ml can)", "Diet Coke (330ml can)"]
};

  /* ===========================
   PATCH 2: BD Street Foods + Seafood + Poultry/Game + Soups + Sweets
   (Adds new foods without overwriting existing keys)
=========================== */

const FOOD_PATCH_2 = {
  // ===== BD Street Food / Snacks =====
  "Alur Chop (Aloo r Chop)": { unitOptions:["pcs"], perUnit:{ pcs:{P:2, C:15, F:7, K:130} }, source:"BD street food avg", confidence:"Low" },
  "Fuchka (Fuchsia / Pani Puri)": { unitOptions:["pcs"], perUnit:{ pcs:{P:1, C:7, F:1, K:45} }, source:"BD street food avg", confidence:"Low" },
  "Chotpoti": { unitOptions:["bowl"], perUnit:{ bowl:{P:10, C:35, F:12, K:320} }, source:"BD street food avg", confidence:"Low" },
  "Jhal Muri": { unitOptions:["bowl"], perUnit:{ bowl:{P:7, C:45, F:10, K:300} }, source:"BD street food avg", confidence:"Low" },
  "Bhel Puri": { unitOptions:["bowl"], perUnit:{ bowl:{P:7, C:55, F:12, K:360} }, source:"South Asian street food avg", confidence:"Low" },
  "Momo (Chicken) — 6 pcs": { unitOptions:["serving"], perUnit:{ serving:{P:12, C:36, F:6, K:270} }, source:"restaurant avg", confidence:"Low" },

  // ===== BD Curries / Sides =====
  "Alu Bhaji (Alu Vaji)": { unitOptions:["cup"], perUnit:{ cup:{P:3, C:20, F:6, K:150} }, source:"BD home avg", confidence:"Low" },

  // ===== Organ / Offal / Traditional =====
  "Kolija (Chicken Liver, cooked)": { unitOptions:["g"], perUnit:{ g:{P:24/100, C:1/100, F:7/100, K:167/100} }, source:"USDA/avg", confidence:"Medium" },
  "Vuri (Tripe, cooked)": { unitOptions:["g"], perUnit:{ g:{P:12/100, C:0/100, F:4/100, K:85/100} }, source:"avg", confidence:"Medium" },
  "Paya (Beef trotters soup)": { unitOptions:["bowl"], perUnit:{ bowl:{P:20, C:3, F:12, K:210} }, source:"traditional recipe avg", confidence:"Low" },
  "Nehari (Beef)": { unitOptions:["bowl"], perUnit:{ bowl:{P:28, C:12, F:20, K:350} }, source:"restaurant avg", confidence:"Low" },

  // ===== Seafood (Shellfish) =====
  "Shrimp (cooked)": { unitOptions:["g"], perUnit:{ g:{P:24/100, C:0/100, F:0.3/100, K:99/100} }, source:"USDA avg", confidence:"High" },
  "Prawn (cooked)": { unitOptions:["g"], perUnit:{ g:{P:24/100, C:0/100, F:0.5/100, K:105/100} }, source:"USDA/avg", confidence:"High" },
  "Crab (cooked)": { unitOptions:["g"], perUnit:{ g:{P:19/100, C:0/100, F:1.5/100, K:97/100} }, source:"USDA/avg", confidence:"High" },

  // ===== Fish Types =====
  "Sea Fish (generic)": { unitOptions:["g"], perUnit:{ g:{P:22/100, C:0/100, F:6/100, K:140/100} }, source:"avg", confidence:"Medium" },
  "River Fish (generic)": { unitOptions:["g"], perUnit:{ g:{P:20/100, C:0/100, F:5/100, K:125/100} }, source:"avg", confidence:"Medium" },
  "Small Fish (fried)": { unitOptions:["g"], perUnit:{ g:{P:28/100, C:0/100, F:12/100, K:220/100} }, source:"BD small fish avg", confidence:"Low" },

  // ===== Poultry / Game =====
  "Quail Meat (cooked)": { unitOptions:["g"], perUnit:{ g:{P:25/100, C:0/100, F:6/100, K:170/100} }, source:"avg", confidence:"Medium" },
  "Pigeon Meat (cooked)": { unitOptions:["g"], perUnit:{ g:{P:21/100, C:0/100, F:7/100, K:142/100} }, source:"avg", confidence:"Medium" },

  // ===== Soups =====
  "Mushroom Soup": { unitOptions:["cup"], perUnit:{ cup:{P:3, C:8, F:4, K:80} }, source:"recipe avg", confidence:"Low" },
  "Thai Soup": { unitOptions:["cup"], perUnit:{ cup:{P:6, C:10, F:6, K:120} }, source:"recipe avg", confidence:"Low" },
  "Vegetable Soup": { unitOptions:["cup"], perUnit:{ cup:{P:2, C:10, F:2, K:60} }, source:"recipe avg", confidence:"Low" },
  "Corn Soup": { unitOptions:["cup"], perUnit:{ cup:{P:6, C:20, F:4, K:140} }, source:"recipe avg", confidence:"Low" },

  // ===== Mixed / Meals =====
  "Fish and Chips": { unitOptions:["plate"], perUnit:{ plate:{P:25, C:70, F:25, K:700} }, source:"restaurant avg", confidence:"Low" },

  // ===== Pitha / Sweets =====
  "Pitha (sweet, generic)": { unitOptions:["pcs"], perUnit:{ pcs:{P:3, C:30, F:8, K:200} }, source:"BD sweet avg", confidence:"Low" },
  "Bhapa Pitha (Vapa Pitha)": { unitOptions:["pcs"], perUnit:{ pcs:{P:3, C:35, F:4, K:190} }, source:"BD pitha avg", confidence:"Low" },
  "Mishti (sweet, generic)": { unitOptions:["pcs"], perUnit:{ pcs:{P:3, C:20, F:6, K:150} }, source:"BD sweet avg", confidence:"Low" },
  "Mishti Doi": { unitOptions:["cup"], perUnit:{ cup:{P:6, C:30, F:8, K:220} }, source:"BD dairy sweet avg", confidence:"Low" }
};

const CATEGORY_PATCH_2 = {
  "BD Street Food / Snacks": [
    "Alur Chop (Aloo r Chop)",
    "Fuchka (Fuchsia / Pani Puri)",
    "Chotpoti",
    "Jhal Muri",
    "Bhel Puri",
    "Momo (Chicken) — 6 pcs"
  ],
  "BD Sides / Bhaji": [
    "Alu Bhaji (Alu Vaji)"
  ],
  "Offal / Traditional": [
    "Kolija (Chicken Liver, cooked)",
    "Vuri (Tripe, cooked)",
    "Paya (Beef trotters soup)",
    "Nehari (Beef)"
  ],
  "Seafood (Shellfish)": [
    "Shrimp (cooked)",
    "Prawn (cooked)",
    "Crab (cooked)"
  ],
  "Sea Fish": [
    "Sea Fish (generic)"
  ],
  "River Fish": [
    "River Fish (generic)"
  ],
  "Small Fish": [
    "Small Fish (fried)"
  ],
  "Poultry / Game": [
    "Quail Meat (cooked)",
    "Pigeon Meat (cooked)"
  ],
  "Soups": [
    "Mushroom Soup",
    "Thai Soup",
    "Vegetable Soup",
    "Corn Soup"
  ],
  "Meals (Mixed)": [
    "Fish and Chips"
  ],
  "Pitha / Sweets": [
    "Pitha (sweet, generic)",
    "Bhapa Pitha (Vapa Pitha)",
    "Mishti (sweet, generic)",
    "Mishti Doi"
  ]
};

/* ===========================
   >>> START PATCH X: Oils/Fats + Dairy + Veg + Snacks + Oil Absorption
=========================== */

const FOOD_PATCH_X = {
  /* ===== FATS & OILS ===== */
  "Butter": {
    unitOptions:["g","tbsp"],
    perUnit:{
      g:{P:0.1,C:0,F:0.81,K:7.17},
      tbsp:{P:0.1,C:0,F:11.5,K:102}
    },
    source:"USDA",
    confidence:"High"
  },
  "Ghee": {
    unitOptions:["g","tbsp"],
    perUnit:{
      g:{P:0,C:0,F:1,K:9},
      tbsp:{P:0,C:0,F:13.5,K:120}
    },
    source:"USDA",
    confidence:"High"
  },
  "Olive Oil": {
    unitOptions:["g","tbsp"],
    perUnit:{
      g:{P:0,C:0,F:1,K:9},
      tbsp:{P:0,C:0,F:13.5,K:119}
    },
    source:"USDA",
    confidence:"High"
  },
  "Soybean Oil": {
    unitOptions:["g","tbsp"],
    perUnit:{
      g:{P:0,C:0,F:1,K:9},
      tbsp:{P:0,C:0,F:13.6,K:120}
    },
    source:"USDA",
    confidence:"High"
  },

  /* ===== CHEESE & DAIRY ===== */
  "Cheddar Cheese": {
    unitOptions:["g","slice"],
    perUnit:{
      g:{P:0.25,C:0.013,F:0.33,K:4.02},
      slice:{P:7,C:1,F:9,K:113}
    },
    source:"USDA",
    confidence:"High"
  },
  "Mozzarella Cheese": {
    unitOptions:["g","slice"],
    perUnit:{
      g:{P:0.22,C:0.022,F:0.22,K:2.8},
      slice:{P:6,C:1,F:6,K:85}
    },
    source:"USDA",
    confidence:"High"
  },
  "Processed Cheese Slice": {
    unitOptions:["slice"],
    perUnit:{ slice:{P:4,C:2,F:5,K:70} },
    source:"Label avg",
    confidence:"Medium"
  },

  /* ===== VEGETABLES ===== */
  "Mushroom": {
    unitOptions:["g","cup"],
    perUnit:{
      g:{P:0.031,C:0.033,F:0.003,K:0.22},
      cup:{P:2.2,C:2.3,F:0.2,K:15}
    },
    source:"USDA",
    confidence:"High"
  },
  "Avocado": {
    unitOptions:["g","pcs"],
    perUnit:{
      g:{P:0.02,C:0.085,F:0.15,K:1.6},
      pcs:{P:3,C:12,F:21,K:234}
    },
    source:"USDA",
    confidence:"High"
  },

  /* ===== POTATO: BOILED vs FRIED ===== */
  "Potato (boiled)": {
    unitOptions:["g","pcs"],
    perUnit:{
      g:{P:0.02,C:0.20,F:0.001,K:0.87},
      pcs:{P:4,C:37,F:0.2,K:160}
    },
    source:"USDA",
    confidence:"High"
  },
  "Potato (fried/fries)": {
    unitOptions:["g","serving"],
    perUnit:{
      g:{P:0.035,C:0.41,F:0.15,K:3.12},
      serving:{P:3,C:35,F:15,K:320}
    },
    source:"Restaurant avg",
    confidence:"Medium"
  },

  /* ===== POPULAR SNACKS ===== */
  "Potato Chips": {
    unitOptions:["g","serving"],
    perUnit:{
      g:{P:0.06,C:0.53,F:0.35,K:5.36},
      serving:{P:2,C:15,F:10,K:160}
    },
    source:"Label avg",
    confidence:"Medium"
  },
  "Popcorn (air popped)": {
    unitOptions:["cup"],
    perUnit:{ cup:{P:1.2,C:6,F:0.1,K:31} },
    source:"USDA",
    confidence:"High"
  },
  "Popcorn (buttered)": {
    unitOptions:["cup"],
    perUnit:{ cup:{P:1.5,C:7,F:4,K:75} },
    source:"Label avg",
    confidence:"Medium"
  },

  /* ===== BANGLADESHI SNACKS ===== */
  "Singara": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:4,C:22,F:10,K:190} },
    source:"BD street food avg",
    confidence:"Low"
  },
  "Samosa (BD)": {
    unitOptions:["pcs"],
    perUnit:{ pcs:{P:3,C:18,F:9,K:165} },
    source:"BD street food avg",
    confidence:"Low"
  },
  "Chanachur": {
    unitOptions:["g","serving"],
    perUnit:{
      g:{P:0.12,C:0.55,F:0.25,K:4.8},
      serving:{P:3,C:14,F:6,K:120}   // ~25g
    },
    source:"Label avg / BD brands",
    confidence:"Medium"
  }
};

const CATEGORY_PATCH_X = {
  "Fats & Oils": ["Butter","Ghee","Olive Oil","Soybean Oil"],
  "Cheese & Dairy": ["Cheddar Cheese","Mozzarella Cheese","Processed Cheese Slice"],
  "Vegetables": ["Mushroom","Avocado","Potato (boiled)"],
  "Potato": ["Potato (boiled)","Potato (fried/fries)"],
  "Snacks": ["Potato Chips","Popcorn (air popped)","Popcorn (buttered)","Chanachur"],
  "BD Snacks": ["Singara","Samosa (BD)","Chanachur"]
};

/* Oil absorption helpers */
function calcAbsorbedOilGrams(oilUsedGrams, absorptionPct){
  const g = n(oilUsedGrams);
  const pct = clamp(n(absorptionPct), 0, 100);
  return (g * pct) / 100;
}
function oilMacrosFromAbsorbed(absorbedOilGrams){
  const g = Math.max(0, n(absorbedOilGrams));
  return { P:0, C:0, F:g, K:g*9 };
}

/* ===========================
   <<< END PATCH X
=========================== */

/* PATCH 2: Split Fruits from Snacks (already separate; keep, but also ensure category arrays are clean) */
function applyFruitSnackSplit(){
  // ensure category arrays exist
  if(!CATEGORY_ITEMS["Fruits"]) CATEGORY_ITEMS["Fruits"] = [];
  if(!CATEGORY_ITEMS["Snacks"]) CATEGORY_ITEMS["Snacks"] = [];
}

/* PATCH 4: Custom food (loaded from storage and merged) */
function loadCustomFoods(){
  try{ return JSON.parse(localStorage.getItem(LS_CUSTOM_FOOD) || "{}"); }catch(e){ return {}; }
}
function saveCustomFoods(map){
  localStorage.setItem(LS_CUSTOM_FOOD, JSON.stringify(map));
}

function applyAllFoodPatches(){
  // Ensure base arrays exist early
  applyFruitSnackSplit();

  // 1) Merge food extensions (MERGE, not no-overwrite)
  mergeFoodPatch(FOOD_PATCH_1);

  // X) Merge PATCH X foods
  mergeFoodPatch(FOOD_PATCH_X);

  // X) Merge PATCH X categories
  Object.keys(CATEGORY_PATCH_X).forEach(cat=>{
    if(!CATEGORY_ITEMS[cat]) CATEGORY_ITEMS[cat]=[];
    CATEGORY_PATCH_X[cat].forEach(item=>{
      if(FOOD[item] && !CATEGORY_ITEMS[cat].includes(item)) CATEGORY_ITEMS[cat].push(item);
    });
  });

  // 1) Merge category extensions
  Object.keys(CATEGORY_PATCH_1).forEach(cat=>{
    if(!CATEGORY_ITEMS[cat]) CATEGORY_ITEMS[cat]=[];
    CATEGORY_PATCH_1[cat].forEach(item=>{
      if(FOOD[item] && !CATEGORY_ITEMS[cat].includes(item)) CATEGORY_ITEMS[cat].push(item);
    });
  });

  // 2) Merge PATCH 2 foods
  mergeFoodPatch(FOOD_PATCH_2);

  // 2) Merge PATCH 2 categories
  Object.keys(CATEGORY_PATCH_2).forEach(cat=>{
    if(!CATEGORY_ITEMS[cat]) CATEGORY_ITEMS[cat]=[];
    CATEGORY_PATCH_2[cat].forEach(item=>{
      if(FOOD[item] && !CATEGORY_ITEMS[cat].includes(item)) CATEGORY_ITEMS[cat].push(item);
    });
  });

  // 3) Merge PATCH 3 foods
  mergeFoodPatch(FOOD_PATCH_3);

  // 3) Merge PATCH 3 categories
  Object.keys(CATEGORY_PATCH_3).forEach(cat=>{
    if(!CATEGORY_ITEMS[cat]) CATEGORY_ITEMS[cat] = [];
    CATEGORY_PATCH_3[cat].forEach(item=>{
      if(FOOD[item] && !CATEGORY_ITEMS[cat].includes(item)){
        CATEGORY_ITEMS[cat].push(item);
      }
    });
  });

  // 4) Merge custom foods (custom overrides by design)
  const custom = loadCustomFoods();
  Object.keys(custom).forEach(k=>{
    FOOD[k] = custom[k];
    const c = custom[k]?.category || "Custom";
    if(!CATEGORY_ITEMS[c]) CATEGORY_ITEMS[c]=[];
    if(!CATEGORY_ITEMS[c].includes(k)) CATEGORY_ITEMS[c].push(k);
  });

  // Rebuild All AFTER all patches + custom foods
  CATEGORY_ITEMS["All"] = Object.keys(FOOD).slice().sort((a,b)=>a.localeCompare(b));

  // Rebuild dropdown view categories now that categories + FOOD are final
  buildCategoryView();
}

/* ===== Food compute ===== */
function computeFood(foodName, qty, unit){
  const item=FOOD[foodName];
  if(!item) return {P:0,C:0,F:0,K:0};
  const map=item.perUnit?.[unit];
  if(!map) return {P:0,C:0,F:0,K:0};
  return { P: map.P*qty, C: map.C*qty, F: map.F*qty, K: map.K*qty };
}
function rowMacrosWithOil(entry, baseMacros){
  const o = oilForEntry(entry);
  return {
    P: n(baseMacros.P),
    C: n(baseMacros.C),
    F: n(baseMacros.F) + o.oilF,
    K: n(baseMacros.K) + o.oilK
  };
}

function foodMeta(foodName){
  const item = FOOD[foodName] || {};
  return {
    source: item.source || "—",
    confidence: item.confidence || "—"
  };
}

/* ===========================
   Food UI (PATCH 3: search + filter)
=========================== */
function buildCategorySelect(){
  const sel=$("entryCategory");
  sel.innerHTML="";

  // Ensure view exists
  if(!CATEGORY_VIEW || !CATEGORY_VIEW["All"]) buildCategoryView();

  Object.keys(CATEGORY_VIEW).forEach(cat=>{
    const opt=document.createElement("option");
    opt.value=cat; opt.textContent=cat;
    sel.appendChild(opt);
  });

  sel.value = "All";
}

function buildFoodSelect(){
  const category = $("entryCategory").value || "All";
  const q = ($("foodSearch").value || "").trim().toLowerCase();

    // Ensure view exists
  if(!CATEGORY_VIEW || !CATEGORY_VIEW["All"]) buildCategoryView();

  let list = (CATEGORY_VIEW[category] || []).slice();
  if(category==="All") list = CATEGORY_VIEW["All"].slice();


  // If search typed, search across ALL foods regardless of category (best UX)
  let filtered = list;
  if(q){
   filtered = (CATEGORY_VIEW["All"] || []).filter(name => name.toLowerCase().includes(q));

  }

  const sel=$("entryFood");
  sel.innerHTML="";
  filtered.forEach(name=>{
    const opt=document.createElement("option");
    opt.value=name; opt.textContent=name;
    sel.appendChild(opt);
  });

  if(filtered.length===0){
    const opt=document.createElement("option");
    opt.value=""; opt.textContent="No matches (try another search)";
    sel.appendChild(opt);
  }

  const first = sel.value || filtered[0] || "";
  buildUnitSelect(first);
  updateEntryPreview();
}
function buildUnitSelect(foodName){
  const uSel=$("entryUnit");
  uSel.innerHTML="";
  (FOOD[foodName]?.unitOptions || ["g"]).forEach(u=>{
    const opt=document.createElement("option");
    opt.value=u; opt.textContent=u;
    uSel.appendChild(opt);
  });
}
function updateEntryPreview(){
  const food=$("entryFood").value;
  const unit=$("entryUnit").value;
  const qty=n($("entryQty").value);

  const t=computeFood(food, qty, unit);

  const oilUsedG = n($("oilUsedG")?.value);
  const oilAbsorbPct = n($("oilAbsorbPct")?.value);
  const absorbed = calcAbsorbedOilGrams(oilUsedG, oilAbsorbPct);
  const oilT = oilMacrosFromAbsorbed(absorbed);

  const P = t.P + oilT.P;
  const C = t.C + oilT.C;
  const F = t.F + oilT.F;
  const K = t.K + oilT.K;

  $("entryPrevP").textContent=round1(P);
  $("entryPrevC").textContent=round1(C);
  $("entryPrevF").textContent=round1(F);
  $("entryPrevK").textContent=Math.round(K);

  const m = foodMeta(food);
  $("entryPrevSrc").textContent = m.source;
  $("entryPrevConf").textContent = m.confidence;
}


/* PATCH 5: Portion presets (prefer plate if available) */
function applyPortionPreset(mult){
  const food = $("entryFood").value;
  if(!food) return;
  const units = FOOD[food]?.unitOptions || ["g"];

  if(units.includes("plate")){
    $("entryUnit").value="plate";
    $("entryQty").value = String(mult);
  }else if(units.includes("cup")){
    $("entryUnit").value="cup";
    $("entryQty").value = String(mult);
  }else if(units.includes("pcs")){
    // 0.5 pcs doesn't make sense; round to nearest
    $("entryUnit").value="pcs";
    $("entryQty").value = String(Math.max(1, Math.round(mult)));
  }else{
    // fallback to grams: assume 250g as "plate" baseline
    $("entryUnit").value=units[0];
    $("entryQty").value = String(Math.round(250*mult));
  }
  updateEntryPreview();
}

/* Hook UI events */
$("entryCategory").addEventListener("change", buildFoodSelect);
$("foodSearch").addEventListener("input", buildFoodSelect);
$("entryFood").addEventListener("change", ()=>{ buildUnitSelect($("entryFood").value); updateEntryPreview(); });
$("entryUnit").addEventListener("change", updateEntryPreview);
$("entryQty").addEventListener("input", updateEntryPreview);
  $("oilUsedG")?.addEventListener("input", updateEntryPreview);
$("oilAbsorbPct")?.addEventListener("input", updateEntryPreview);


/* ===========================
   Food log actions
=========================== */
function computeTotalsFromEntries(dateKey){
  const day=getDayLog(dateKey);

  const list = day.entries.map(e=>{
    const t = computeFood(e.food, n(e.qty), e.unit);

    const absorbed = calcAbsorbedOilGrams(e.oilUsedG || 0, e.oilAbsorbPct || 0);
    const oilT = oilMacrosFromAbsorbed(absorbed);

    return {
      P: (t.P + oilT.P),
      C: (t.C + oilT.C),
      F: (t.F + oilT.F),
      K: (t.K + oilT.K)
    };
  });

  return sumTotalsSafe(list);
}

function inferCategoryFromFood(food){
  for(const cat in CATEGORY_ITEMS){
    if(cat==="All") continue;
    if((CATEGORY_ITEMS[cat]||[]).includes(food)) return cat;
  }
  return "Uncategorized";
}

function addEntry(){
  const profile=getActiveProfile() || getProfileDraft();
  const dateKey=$("logDate").value || getDefaultLogDate(profile);
  const day=getDayLog(dateKey);

  const food=$("entryFood").value;
  const unit=$("entryUnit").value;
  const qty=n($("entryQty").value);
  if(!food || qty<=0){ alert("Select a food and enter a quantity > 0."); return; }

  let category = $("entryCategory").value;
  if(category === "All"){
    category = inferCategoryFromFood(food);
  }

    const oilUsedG = n($("oilUsedG") ? $("oilUsedG").value : 0);
  const oilAbsorbPct = n($("oilAbsorbPct") ? $("oilAbsorbPct").value : 0);

  day.entries.push({
    id: uid(),
    meal: $("mealType").value,
    category,
    food,
    unit,
    qty,
    oilUsedG,
    oilAbsorbPct
  });

  if($("oilUsedG")) $("oilUsedG").value = "";
  if($("oilAbsorbPct")) $("oilAbsorbPct").value = "";

  setDayLog(dateKey, day);

  $("entryQty").value="";
  renderEntries();
  scheduleRefresh();
}
function normalizeEntryCategory(cat){
  if(!cat) return "";
  if(CATEGORY_ITEMS && CATEGORY_ITEMS[cat]) return cat;

  if(typeof CATEGORY_MERGE_MAP === "object" && CATEGORY_MERGE_MAP){
    for(const newCat in CATEGORY_MERGE_MAP){
      if((CATEGORY_MERGE_MAP[newCat] || []).includes(cat)) return newCat;
    }
  }
  return cat;
}

function renderEntries(){
  const profile = getActiveProfile() || getProfileDraft();
  const dateKey = $("logDate").value || getDefaultLogDate(profile);
  const day = getDayLog(dateKey);

  const tb = $("entriesTbody");
  tb.innerHTML = "";

  day.entries.forEach(e=>{
    const base = computeFood(e.food, n(e.qty), e.unit);
    co
