<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Data Sutram — Asset Inventory Dashboard</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Sora:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0f14;
    --surface: #13161d;
    --surface2: #1a1e28;
    --border: #252936;
    --accent: #4f8ef7;
    --accent2: #38d9a9;
    --warn: #ffa94d;
    --danger: #ff6b6b;
    --text: #e8eaf0;
    --muted: #6b7280;
    --mac: #a78bfa;
    --win: #60a5fa;
    --linux: #34d399;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { background: var(--bg); color: var(--text); font-family: 'Sora', sans-serif; font-size: 14px; min-height: 100vh; }

  /* HEADER */
  .header { background: var(--surface); border-bottom: 1px solid var(--border); padding: 16px 28px; display: flex; align-items: center; justify-content: space-between; position: sticky; top: 0; z-index: 100; }
  .logo { display: flex; align-items: center; gap: 12px; }
  .logo-dot { width: 10px; height: 10px; background: var(--accent); border-radius: 50%; box-shadow: 0 0 10px var(--accent); }
  .logo-text { font-size: 15px; font-weight: 600; letter-spacing: 0.02em; }
  .logo-sub { font-size: 11px; color: var(--muted); font-family: 'DM Mono', monospace; margin-top: 1px; }
  .header-right { font-family: 'DM Mono', monospace; font-size: 11px; color: var(--muted); }

  /* LAYOUT */
  .main { padding: 24px 28px; max-width: 1400px; margin: 0 auto; }

  /* STAT CARDS */
  .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(160px, 1fr)); gap: 14px; margin-bottom: 24px; }
  .stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 18px; position: relative; overflow: hidden; transition: border-color 0.2s; }
  .stat-card:hover { border-color: var(--accent); }
  .stat-card::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px; background: var(--card-color, var(--accent)); }
  .stat-label { font-size: 11px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 8px; }
  .stat-value { font-size: 32px; font-weight: 700; line-height: 1; color: var(--card-color, var(--text)); }
  .stat-sub { font-size: 11px; color: var(--muted); margin-top: 6px; font-family: 'DM Mono', monospace; }

  /* TABS */
  .tabs { display: flex; gap: 4px; margin-bottom: 20px; background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 4px; width: fit-content; }
  .tab { padding: 7px 18px; border-radius: 7px; font-size: 12px; font-weight: 500; cursor: pointer; transition: all 0.15s; color: var(--muted); border: none; background: none; font-family: 'Sora', sans-serif; }
  .tab.active { background: var(--accent); color: white; }
  .tab:hover:not(.active) { color: var(--text); background: var(--surface2); }

  /* SEARCH & FILTER */
  .controls { display: flex; gap: 12px; margin-bottom: 18px; flex-wrap: wrap; align-items: center; }
  .search-wrap { position: relative; flex: 1; min-width: 220px; }
  .search-wrap svg { position: absolute; left: 12px; top: 50%; transform: translateY(-50%); opacity: 0.4; }
  .search { width: 100%; background: var(--surface); border: 1px solid var(--border); border-radius: 8px; padding: 9px 12px 9px 36px; color: var(--text); font-family: 'Sora', sans-serif; font-size: 13px; outline: none; transition: border-color 0.2s; }
  .search:focus { border-color: var(--accent); }
  .search::placeholder { color: var(--muted); }
  .filter-select { background: var(--surface); border: 1px solid var(--border); border-radius: 8px; padding: 9px 14px; color: var(--text); font-family: 'Sora', sans-serif; font-size: 12px; outline: none; cursor: pointer; }
  .filter-select:focus { border-color: var(--accent); }

  /* TABLE */
  .table-wrap { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; overflow: hidden; }
  .table-head { display: flex; padding: 0; border-bottom: 1px solid var(--border); }
  table { width: 100%; border-collapse: collapse; }
  thead th { padding: 12px 16px; text-align: left; font-size: 11px; text-transform: uppercase; letter-spacing: 0.08em; color: var(--muted); font-weight: 500; border-bottom: 1px solid var(--border); white-space: nowrap; }
  tbody tr { border-bottom: 1px solid var(--border); transition: background 0.1s; }
  tbody tr:last-child { border-bottom: none; }
  tbody tr:hover { background: var(--surface2); }
  tbody td { padding: 11px 16px; font-size: 13px; vertical-align: middle; }
  .mono { font-family: 'DM Mono', monospace; font-size: 11px; }

  /* BADGES */
  .badge { display: inline-block; padding: 3px 10px; border-radius: 20px; font-size: 11px; font-weight: 500; white-space: nowrap; }
  .badge-mac { background: rgba(167,139,250,0.15); color: var(--mac); border: 1px solid rgba(167,139,250,0.3); }
  .badge-win { background: rgba(96,165,250,0.15); color: var(--win); border: 1px solid rgba(96,165,250,0.3); }
  .badge-linux { background: rgba(52,211,153,0.15); color: var(--linux); border: 1px solid rgba(52,211,153,0.3); }
  .badge-spare { background: rgba(255,169,77,0.15); color: var(--warn); border: 1px solid rgba(255,169,77,0.3); }
  .badge-faulty { background: rgba(255,107,107,0.15); color: var(--danger); border: 1px solid rgba(255,107,107,0.3); }
  .badge-active { background: rgba(56,217,169,0.15); color: var(--accent2); border: 1px solid rgba(56,217,169,0.3); }
  .badge-warranty { background: rgba(79,142,247,0.15); color: var(--accent); border: 1px solid rgba(79,142,247,0.3); }
  .badge-oow { background: rgba(107,114,128,0.15); color: var(--muted); border: 1px solid rgba(107,114,128,0.3); }

  /* USER VIEW */
  .user-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 14px; }
  .user-card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 18px; transition: border-color 0.2s; }
  .user-card:hover { border-color: var(--accent); }
  .user-name { font-weight: 600; font-size: 14px; margin-bottom: 4px; }
  .user-count { font-size: 11px; color: var(--muted); font-family: 'DM Mono', monospace; margin-bottom: 14px; }
  .user-assets { display: flex; flex-direction: column; gap: 6px; }
  .asset-row { display: flex; align-items: center; gap: 8px; padding: 7px 10px; background: var(--surface2); border-radius: 7px; font-size: 12px; }
  .asset-icon { width: 20px; text-align: center; font-size: 14px; }
  .asset-info { flex: 1; }
  .asset-id { font-family: 'DM Mono', monospace; font-size: 10px; color: var(--muted); }

  /* EMPTY */
  .empty { text-align: center; padding: 60px 20px; color: var(--muted); }
  .empty-icon { font-size: 40px; margin-bottom: 12px; }

  /* PAGINATION */
  .pagination { display: flex; align-items: center; gap: 8px; margin-top: 16px; justify-content: flex-end; }
  .page-btn { background: var(--surface); border: 1px solid var(--border); color: var(--text); border-radius: 6px; padding: 6px 12px; font-size: 12px; cursor: pointer; font-family: 'Sora', sans-serif; transition: all 0.15s; }
  .page-btn:hover:not(:disabled) { border-color: var(--accent); color: var(--accent); }
  .page-btn:disabled { opacity: 0.4; cursor: not-allowed; }
  .page-info { font-size: 12px; color: var(--muted); font-family: 'DM Mono', monospace; }

  /* WARRANTY CHART */
  .mini-bar { height: 4px; background: var(--border); border-radius: 2px; margin-top: 6px; overflow: hidden; }
  .mini-fill { height: 100%; border-radius: 2px; background: var(--accent2); }
  .section-title { font-size: 13px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 14px; }

  .tag { display: inline-block; font-size: 10px; font-family: 'DM Mono', monospace; padding: 2px 7px; border-radius: 4px; background: var(--surface2); border: 1px solid var(--border); color: var(--muted); margin: 2px; }
</style>
</head>
<body>

<div class="header">
  <div class="logo">
    <div class="logo-dot"></div>
    <div>
      <div class="logo-text">Data Sutram</div>
      <div class="logo-sub">Asset Inventory Dashboard</div>
    </div>
  </div>
  <div class="header-right">Last updated: April 2025 &nbsp;|&nbsp; 92 records</div>
</div>

<div class="main">
  <!-- STATS -->
  <div class="stats-grid" id="statsGrid"></div>

  <!-- TABS -->
  <div class="tabs">
    <button class="tab active" onclick="switchTab('all')">All Assets</button>
    <button class="tab" onclick="switchTab('user')">By User</button>
    <button class="tab" onclick="switchTab('spare')">Spare / IT</button>
    <button class="tab" onclick="switchTab('faulty')">Faulty</button>
  </div>

  <!-- CONTROLS -->
  <div class="controls">
    <div class="search-wrap">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg>
      <input class="search" id="searchInput" placeholder="Search by name, asset ID, serial, brand..." oninput="filterData()">
    </div>
    <select class="filter-select" id="brandFilter" onchange="filterData()">
      <option value="">All Brands</option>
    </select>
    <select class="filter-select" id="statusFilter" onchange="filterData()">
      <option value="">All Status</option>
      <option value="active">Active (In Use)</option>
      <option value="spare">Spare / With IT</option>
      <option value="faulty">Faulty / Repair</option>
      <option value="mac">MacBook</option>
      <option value="windows">Windows</option>
      <option value="linux">Linux</option>
    </select>
    <div style="font-family:'DM Mono',monospace;font-size:11px;color:var(--muted)" id="resultCount"></div>
  </div>

  <!-- TABLE VIEW -->
  <div id="tableView">
    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Employee</th>
            <th>Asset ID</th>
            <th>Brand</th>
            <th>Type</th>
            <th>Serial No.</th>
            <th>RAM</th>
            <th>Warranty</th>
            <th>Status</th>
            <th>Remark</th>
          </tr>
        </thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
    <div class="pagination">
      <button class="page-btn" id="prevBtn" onclick="changePage(-1)">← Prev</button>
      <span class="page-info" id="pageInfo"></span>
      <button class="page-btn" id="nextBtn" onclick="changePage(1)">Next →</button>
    </div>
  </div>

  <!-- USER VIEW -->
  <div id="userView" style="display:none">
    <div class="user-grid" id="userGrid"></div>
  </div>
</div>

<script>
const raw = [
  {n:"Alok Anil Mani",id:"DS/MAC/25/11",brand:"Apple",serial:"FNJVHKDX0N",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Aisik Paul",id:"DS/MAC/25/13",brand:"Apple",serial:"J5GW0MH4WTM",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"With IT (Anshul Kashyap) — Damage",id:"DS/LP/02",brand:"HP",serial:"5CD2118XCN",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"Damaged",category:"faulty"},
  {n:"Arnab Samiran Nandi",id:"DS/LP/16",brand:"—",serial:"—",ram:"—",os:"windows",warranty:"—",remark:"",category:"active"},
  {n:"Vikas Yadav",id:"DS/CD/01",brand:"—",serial:"—",ram:"—",os:"windows",warranty:"—",remark:"Company Desktop",category:"active"},
  {n:"Souvik China",id:"DS/MAC/25/16",brand:"Apple",serial:"LVL441D652",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Dipayan Basu",id:"DS/MAC/25/15",brand:"Apple",serial:"JWDWTXQTM7",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Manikandan Mohandas",id:"DS/LP/17",brand:"Asus",serial:"N2NXCV15Z29808A",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Payal Rodrigues",id:"DS/LP/25",brand:"Asus",serial:"N2NXCV15Z21408B",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Akanksha Walia",id:"DS/LP/62",brand:"LENOVO",serial:"PF3A2JKT",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Spare With IT (Ritam Deb)",id:"DS/LP/18",brand:"Asus",serial:"N2NXCV15Z441084",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Yashraj Dangre",id:"DS/LP/50",brand:"Asus",serial:"K2308N0117655",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Amneek Singh",id:"DS/LP/43",brand:"Asus",serial:"N2NXCV15Z409088",ram:"16GB",os:"linux",warranty:"Out of Warranty",remark:"Take Handover",category:"active"},
  {n:"Ankit Das",id:"—",brand:"HP",serial:"CND2243LPZ",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Snehal Salvi",id:"DS/LP/22",brand:"Asus",serial:"N2NXCV15Z18908E",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Spare With IT",id:"DS/LP/33",brand:"Asus",serial:"N2NXCV15Z48808A",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Kaushik Vedpathak",id:"DS/LP/55",brand:"Asus Vivobook",serial:"R9N0CV14Z77038D",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Yasmin Ansari",id:"DS/LP/70",brand:"Asus Vivobook",serial:"R8N0CV05Y36732D",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Manthan Jadhav",id:"DS/LP/66",brand:"Asus",serial:"R7N0CV12A210298",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"In Warranty",category:"active"},
  {n:"Annu Tejpal Sharma",id:"DS/LP/57",brand:"Asus Vivobook",serial:"R7N0CV21X05031E",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"Take Handover",category:"active"},
  {n:"Indranil Partha Sarathi Chowdhury",id:"DS/LP/30",brand:"Asus",serial:"N9N0CV116283389",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Take Handover",category:"active"},
  {n:"Archisha Chidumalla",id:"DS/MAC/25/02",brand:"Apple",serial:"FX9T395V6G",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Ravit Kyal",id:"DS/LP/74",brand:"LENOVO",serial:"PF4YVP91",ram:"8GB",os:"windows",warranty:"In Warranty",remark:"In Warranty",category:"active"},
  {n:"Faiz Alam",id:"DS/LP/39",brand:"Asus",serial:"N2NXCV15Z453084",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Sanjay Kumar",id:"DS/LP/73",brand:"Asus Vivobook",serial:"R3N0CV16T041138",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"Take Handover",category:"active"},
  {n:"Priyanshi Soni",id:"DS/MAC/25/07",brand:"Apple",serial:"FG26Y6P94X",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"With IT",id:"DS/LP/42",brand:"Asus",serial:"N2NXCV15Z38008E",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Ritam Deb",id:"DS/LP/20",brand:"Asus",serial:"N2XNXCV15Z23408B",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Gaurav Soni",id:"DS/MAC/25/01",brand:"Apple",serial:"LFVQ9VY6K0",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Sagar Das",id:"DS/LP/31",brand:"Asus",serial:"N2NXCV15Z50108A",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"With IT",id:"DS/LP/41",brand:"Asus",serial:"N2NXCV15Z326088",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Akshay Kapoor",id:"DS/LP/29",brand:"Asus Vivobook",serial:"N9N0CV11621938B",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Dev Naliapara",id:"DS/LP/24",brand:"Asus",serial:"N2NXCV15Z318088",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Vikas Yadav",id:"DS/LP/38",brand:"Asus",serial:"N2NXCV15Z59108A",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Vikrant Patil",id:"DS/LP/51",brand:"Asus Vivobook",serial:"NBN0CV00948344G",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Tarun Kumar",id:"DS/LP/67",brand:"Asus",serial:"R8N0CV05Y067324",ram:"16GB",os:"linux",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"With IT — Motherboard BIOS Problem",id:"DS/LP/19",brand:"Asus",serial:"N2NXCV15Z333087",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Motherboard/BIOS issue",category:"faulty"},
  {n:"Ketan Purohit",id:"DS/LP/46",brand:"Asus Vivobook",serial:"N2NXCV15Z38508H",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Vinod Patil",id:"DS/LP/34",brand:"Asus",serial:"N2NXCV15Z563089",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Omkar Shankar Deshmukh",id:"DS/LP/48",brand:"Asus Vivobook",serial:"NBN0CV009487448",ram:"16GB",os:"linux",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Santosh Hanumanta Kamble",id:"DS/LP/64",brand:"Asus Vivobook",serial:"R3N0CV144105127",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Akshay Ashok Suralkar",id:"DS/LP/23",brand:"Asus",serial:"N2NXCV15Z192084",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Prasad Rajendra Mahadik",id:"DS/LP/53",brand:"Asus Vivobook",serial:"NBN0CV00954444E",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Mitul Shashank Desai",id:"DS/LP/52",brand:"Asus Vivobook",serial:"R5N0CV14L68321E",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Rahul Varma Nidadavolu",id:"DS/LP/54",brand:"Asus Vivobook",serial:"R3N0CV16T55013F",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Sumaiya Muskan",id:"DS/LP/60",brand:"Asus Vivobook",serial:"R3N0CV16T021137",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Deepak Manik Gore",id:"DS/LP/56",brand:"Asus Vivobook",serial:"R7N0CV21X058319",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Ishika Sethi",id:"DS/LP/71",brand:"Asus Vivobook",serial:"R3N0CV16T40213A",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Saad Shaikh",id:"DS/LP/72",brand:"Asus Vivobook",serial:"R3N0CV16T070139",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"Take handover",category:"active"},
  {n:"Taha Saifee",id:"DS/LP/68",brand:"Asus Vivobook",serial:"R6N0CV13625825B",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Chinmay Kulkarni",id:"DS/LP/69",brand:"Asus Vivobook",serial:"R8N0CV05Y319323",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Siddhesh Kedari",id:"DS-LP-45",brand:"Asus",serial:"N2NXCV15Z409088",ram:"16GB",os:"linux",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"WITH IT",id:"DS/LP/35",brand:"Asus",serial:"N2NXCV15Z407085",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Spare With IT",id:"DS/LP/54",brand:"—",serial:"—",ram:"—",os:"windows",warranty:"—",remark:"",category:"spare"},
  {n:"Rishab Jain (Bangalore)",id:"DS/LP/32",brand:"Asus",serial:"N2NXCV15Z394080",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Mouse pad issue",category:"active"},
  {n:"With IT — Display Cable / Fabrication",id:"DS/LP/21",brand:"Asus",serial:"N2NXCV15Z235083",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Display cable issue",category:"faulty"},
  {n:"Shreyan Ghosh",id:"DS/LP/44",brand:"Asus",serial:"N2NXCV15Z355084",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Given to Rakshit (7-12-2024)",id:"DS/LP/14",brand:"Asus",serial:"M9N0CV16B60838B",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Punit Pal",id:"DS/LP/49",brand:"Asus",serial:"9S714JK12218ZN8000033",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Varad Deshpande",id:"DS/LP/09",brand:"HP",serial:"5CD2253ZL2",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Rajit Bhattacharya",id:"DS/MAC/02",brand:"Mac Mini",serial:"KGHMQX6JR4",ram:"8GB",os:"mac",warranty:"Out of Warranty",remark:"Apple M3 Mac Mini",category:"active"},
  {n:"Vijaya Likhita (Delhi)",id:"DS/LP/13",brand:"HP",serial:"5CD224F9XQ",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"With IT — Faulty Motherboard",id:"DS/LP/47",brand:"Asus Vivobook",serial:"N9N0CV116291386",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Motherboard issue",category:"faulty"},
  {n:"With IT — Faulty Touchpad & Keyboard",id:"DS/LP/40",brand:"Asus",serial:"N2NXCV15Z32308D",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"Touchpad & Keyboard issue",category:"faulty"},
  {n:"Sachin Bagade",id:"DS/LP/04",brand:"HP",serial:"5CD2118MHP",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Abhishek Kadam",id:"DS/LP/61",brand:"Asus Vivobook",serial:"NBN0CV00952144C",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Niladri Paramanik",id:"DS/LP/63",brand:"Asus Vivobook",serial:"R3N0CV144124129",ram:"16GB",os:"linux",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Mihir Ketkar — With Ravit",id:"DS/LP/65",brand:"LENOVO",serial:"PF48Y8QV",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Bharat Kadam",id:"DS/LP/11",brand:"HP",serial:"5CD212BZXH",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Mayank Khurana",id:"DS/LP/15",brand:"HP",serial:"5CD2118N2N",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Sakshi Yerunkar",id:"DS/LP/03",brand:"LENOVO",serial:"PF3A3CQG",ram:"8GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Preeti (Bangalore)",id:"DS/LP/05",brand:"HP",serial:"5CD2253ZW9",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Sudeep Pathak",id:"DS/LP/06",brand:"HP",serial:"5CD224F9TZ",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Atharva Pathak",id:"DS/LP/58",brand:"Asus Vivobook",serial:"R7N0CV21X16931D",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Nishal Dbritto",id:"DS/LP/08",brand:"Lenovo",serial:"PF3A1B1Q",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Shaiban Bhiwandiwalla (With IT)",id:"DS/LP/10",brand:"LENOVO",serial:"PF3A1MQE",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
  {n:"Piyusha Gupta (Delhi)",id:"DS/LP/01",brand:"LENOVO",serial:"PF3A1DMT",ram:"8GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Vishal Shaw",id:"DS/LP/27",brand:"Asus",serial:"N9N0CV10K627389",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"active"},
  {n:"Rakshit Lakhani",id:"DS/LP/76",brand:"Dell",serial:"BMOM344",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"Dell warranty",category:"active"},
  {n:"Kranti Kumar",id:"DS/MAC/25/08",brand:"Apple",serial:"GW3F00FV2Q",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Rahul Maskara",id:"DS/MAC/25/06",brand:"Apple",serial:"F6R9XCF7CC",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Sarfaraz Ahamed",id:"DS/MAC/25/09",brand:"Apple",serial:"D6WGCDFCWY",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Ketan Samant",id:"DS/LP/59",brand:"Asus Vivobook",serial:"R3N0CV16598513D",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"2yr warranty",category:"active"},
  {n:"Siddharth Kakkad",id:"DS/MAC/25/14",brand:"Apple",serial:"JLQ04J41TQ",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Anshul Kashyap",id:"DS/LP/75",brand:"Asus",serial:"—",ram:"—",os:"windows",warranty:"—",remark:"",category:"active"},
  {n:"Spare With IT (Saad Shaikh)",id:"DS/LP/72",brand:"Asus Vivobook",serial:"R3N0CV16T070139",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"",category:"spare"},
  {n:"Reshma Jain",id:"DS/LP/77",brand:"HP",serial:"5CG51148WJ",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"3yr warranty",category:"active"},
  {n:"Rajesh Wadhawa",id:"—",brand:"—",serial:"—",ram:"—",os:"windows",warranty:"—",remark:"",category:"active"},
  {n:"Malavika Mallya",id:"DS/MAC/25/17",brand:"Apple",serial:"KGWM4LPGPW",ram:"16GB",os:"mac",warranty:"In Warranty",remark:"MacBook Air M4",category:"active"},
  {n:"Spare With IT",id:"DS/LP/37",brand:"Asus",serial:"N2NXCV15Z34308A",ram:"16GB",os:"windows",warranty:"—",remark:"",category:"spare"},
  {n:"Spare With IT (Annu Sharma)",id:"DS/LP/57",brand:"Asus Vivobook",serial:"R7N0CV21X05031E",ram:"16GB",os:"windows",warranty:"In Warranty",remark:"",category:"spare"},
  {n:"Spare (Indranil Partha)",id:"DS/LP/30",brand:"Asus",serial:"N9N0CV116283389",ram:"16GB",os:"windows",warranty:"Out of Warranty",remark:"",category:"spare"},
];

let currentTab = 'all';
let currentPage = 1;
const pageSize = 20;
let filtered = [...raw];

function getOsBadge(os) {
  if (os === 'mac') return '<span class="badge badge-mac">MacBook</span>';
  if (os === 'linux') return '<span class="badge badge-linux">Linux</span>';
  return '<span class="badge badge-win">Windows</span>';
}

function getWarrantyBadge(w) {
  if (w === 'In Warranty') return '<span class="badge badge-warranty">In Warranty</span>';
  if (w === 'Out of Warranty') return '<span class="badge badge-oow">Out of Warranty</span>';
  return '<span class="badge" style="background:var(--surface2);color:var(--muted);border:1px solid var(--border)">—</span>';
}

function getStatusBadge(cat) {
  if (cat === 'spare') return '<span class="badge badge-spare">Spare / IT</span>';
  if (cat === 'faulty') return '<span class="badge badge-faulty">Faulty</span>';
  return '<span class="badge badge-active">In Use</span>';
}

function renderStats() {
  const total = raw.length;
  const macs = raw.filter(r => r.os === 'mac').length;
  const windows = raw.filter(r => r.os === 'windows').length;
  const linux = raw.filter(r => r.os === 'linux').length;
  const spare = raw.filter(r => r.category === 'spare').length;
  const faulty = raw.filter(r => r.category === 'faulty').length;
  const inWarranty = raw.filter(r => r.warranty === 'In Warranty').length;
  const active = raw.filter(r => r.category === 'active').length;

  const cards = [
    {label:'Total Assets', value:total, sub:'across all employees', color:'#4f8ef7'},
    {label:'Active / In Use', value:active, sub:'assigned to employees', color:'#38d9a9'},
    {label:'MacBooks', value:macs, sub:'Apple devices', color:'#a78bfa'},
    {label:'Windows', value:windows, sub:'Windows laptops', color:'#60a5fa'},
    {label:'Linux', value:linux, sub:'Linux machines', color:'#34d399'},
    {label:'Spare with IT', value:spare, sub:'available for allocation', color:'#ffa94d'},
    {label:'Faulty', value:faulty, sub:'needs repair/attention', color:'#ff6b6b'},
    {label:'In Warranty', value:inWarranty, sub:'under active warranty', color:'#4f8ef7'},
  ];

  document.getElementById('statsGrid').innerHTML = cards.map(c => `
    <div class="stat-card" style="--card-color:${c.color}">
      <div class="stat-label">${c.label}</div>
      <div class="stat-value">${c.value}</div>
      <div class="stat-sub">${c.sub}</div>
    </div>
  `).join('');
}

function populateFilters() {
  const brands = [...new Set(raw.map(r => r.brand).filter(b => b && b !== '—'))].sort();
  const sel = document.getElementById('brandFilter');
  brands.forEach(b => {
    const opt = document.createElement('option');
    opt.value = b; opt.textContent = b;
    sel.appendChild(opt);
  });
}

function filterData() {
  const q = document.getElementById('searchInput').value.toLowerCase();
  const brand = document.getElementById('brandFilter').value;
  const status = document.getElementById('statusFilter').value;

  filtered = raw.filter(r => {
    const matchQ = !q || r.n.toLowerCase().includes(q) || r.id.toLowerCase().includes(q) || r.serial.toLowerCase().includes(q) || r.brand.toLowerCase().includes(q);
    const matchBrand = !brand || r.brand === brand;
    let matchStatus = true;
    if (status === 'active') matchStatus = r.category === 'active';
    else if (status === 'spare') matchStatus = r.category === 'spare';
    else if (status === 'faulty') matchStatus = r.category === 'faulty';
    else if (status === 'mac') matchStatus = r.os === 'mac';
    else if (status === 'windows') matchStatus = r.os === 'windows';
    else if (status === 'linux') matchStatus = r.os === 'linux';
    return matchQ && matchBrand && matchStatus;
  });

  currentPage = 1;
  if (currentTab === 'user') renderUserView();
  else renderTable();
}

function renderTable() {
  const start = (currentPage - 1) * pageSize;
  const page = filtered.slice(start, start + pageSize);
  const totalPages = Math.ceil(filtered.length / pageSize);

  document.getElementById('resultCount').textContent = `${filtered.length} results`;
  document.getElementById('pageInfo').textContent = `Page ${currentPage} of ${Math.max(1, totalPages)}`;
  document.getElementById('prevBtn').disabled = currentPage === 1;
  document.getElementById('nextBtn').disabled = currentPage >= totalPages;

  document.getElementById('tableBody').innerHTML = page.map((r, i) => `
    <tr>
      <td class="mono" style="color:var(--muted)">${start + i + 1}</td>
      <td><div style="font-weight:500;font-size:13px">${r.n}</div></td>
      <td><span class="mono" style="color:var(--accent)">${r.id}</span></td>
      <td style="color:var(--muted);font-size:12px">${r.brand}</td>
      <td>${getOsBadge(r.os)}</td>
      <td><span class="mono" style="color:var(--muted);font-size:10px">${r.serial}</span></td>
      <td class="mono" style="font-size:11px">${r.ram}</td>
      <td>${getWarrantyBadge(r.warranty)}</td>
      <td>${getStatusBadge(r.category)}</td>
      <td style="font-size:11px;color:var(--muted);max-width:150px">${r.remark}</td>
    </tr>
  `).join('') || `<tr><td colspan="10"><div class="empty"><div class="empty-icon">🔍</div><div>No assets found</div></div></td></tr>`;
}

function renderUserView() {
  // Group active users (not spare/faulty IT entries)
  const userMap = {};
  raw.forEach(r => {
    const name = r.n;
    const isItEntry = name.toLowerCase().startsWith('with it') || name.toLowerCase().startsWith('spare') || name.toLowerCase().startsWith('sapre');
    if (!isItEntry) {
      if (!userMap[name]) userMap[name] = [];
      userMap[name].push(r);
    }
  });

  const q = document.getElementById('searchInput').value.toLowerCase();
  const entries = Object.entries(userMap).filter(([name]) => !q || name.toLowerCase().includes(q));

  const icons = {mac:'💻', windows:'🖥️', linux:'🐧'};

  document.getElementById('resultCount').textContent = `${entries.length} users`;
  document.getElementById('userGrid').innerHTML = entries.map(([name, assets]) => `
    <div class="user-card">
      <div class="user-name">${name}</div>
      <div class="user-count">${assets.length} asset${assets.length > 1 ? 's' : ''} assigned</div>
      <div class="user-assets">
        ${assets.map(a => `
          <div class="asset-row">
            <div class="asset-icon">${icons[a.os] || '📦'}</div>
            <div class="asset-info">
              <div style="font-size:12px;font-weight:500">${a.brand !== '—' ? a.brand : 'Unknown Brand'}</div>
              <div class="asset-id">${a.id} &nbsp;|&nbsp; ${a.serial}</div>
            </div>
            ${getOsBadge(a.os)}
          </div>
        `).join('')}
      </div>
    </div>
  `).join('') || `<div class="empty"><div class="empty-icon">👤</div><div>No users found</div></div>`;
}

function switchTab(tab) {
  currentTab = tab;
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  event.target.classList.add('active');

  const statusFilter = document.getElementById('statusFilter');

  if (tab === 'spare') { statusFilter.value = 'spare'; filterData(); }
  else if (tab === 'faulty') { statusFilter.value = 'faulty'; filterData(); }
  else { statusFilter.value = ''; filterData(); }

  if (tab === 'user') {
    document.getElementById('tableView').style.display = 'none';
    document.getElementById('userView').style.display = 'block';
    renderUserView();
  } else {
    document.getElementById('tableView').style.display = 'block';
    document.getElementById('userView').style.display = 'none';
    renderTable();
  }
}

function changePage(dir) {
  currentPage += dir;
  renderTable();
  window.scrollTo({top: 0, behavior: 'smooth'});
}

// INIT
renderStats();
populateFilters();
filterData();
</script>
</body>
</html>
