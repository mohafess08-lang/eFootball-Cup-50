<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>eFootball Cup</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
:root{
  --bg:#0b0b0b;
  --gold:#d4af37;
  --green:#1fd66b;
  --white:#ffffff;
  --card:#151515;
}

*{box-sizing:border-box}
body{
  margin:0;
  background:var(--bg);
  color:var(--white);
  font-family:Arial, Helvetica, sans-serif;
}

header{
  display:flex;
  align-items:center;
  padding:15px 20px;
  border-bottom:1px solid #222;
}

header img{
  height:55px;
  margin-right:15px;
}

header h1{
  color:var(--gold);
  margin:0;
  font-size:22px;
}

nav{
  display:flex;
  justify-content:center;
  gap:25px;
  margin:20px 0;
}

nav button{
  background:none;
  border:none;
  color:#aaa;
  font-size:16px;
  cursor:pointer;
}

nav button.active{
  color:var(--gold);
  border-bottom:2px solid var(--gold);
}

.page{
  display:none;
  max-width:1100px;
  margin:auto;
  padding:0 15px 60px;
}

.page.active{display:block}

h2{
  color:var(--gold);
  margin:25px 0 15px;
}

/* ---------- TABLE ---------- */
table{
  width:100%;
  border-collapse:collapse;
  background:var(--card);
}

th,td{
  padding:10px;
  text-align:center;
  border-bottom:1px solid #222;
}

th{
  color:var(--gold);
  font-size:14px;
}

td.team{
  text-align:left;
  color:white;
}

tr.qualified{
  position:relative;
}

tr.qualified::before{
  content:"";
  position:absolute;
  left:0;
  top:0;
  bottom:0;
  width:5px;
  background:var(--green);
}

/* ---------- GROUPS ---------- */
.group{
  margin-bottom:40px;
}

/* ---------- KNOCKOUT ---------- */
.bracket{
  display:grid;
  grid-template-columns:repeat(4,1fr);
  gap:15px;
}

.match{
  background:var(--card);
  padding:10px;
  border-radius:6px;
  min-height:60px;
  display:flex;
  align-items:center;
  justify-content:center;
  color:#777;
}

/* ---------- ADMIN ---------- */
#adminBtn{
  position:fixed;
  bottom:15px;
  right:15px;
  background:var(--gold);
  color:black;
  border:none;
  padding:10px 15px;
  border-radius:6px;
  cursor:pointer;
}

#admin{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,.9);
  display:none;
  align-items:center;
  justify-content:center;
}

#admin .box{
  background:#111;
  padding:20px;
  width:350px;
  border-radius:8px;
}

input,select{
  width:100%;
  margin-bottom:10px;
  padding:8px;
  background:#222;
  border:1px solid #333;
  color:white;
}

</style>
</head>
<body>

<header>
  <img src="logo.png" alt="eFootball Cup">
  <h1>eFootball Cup</h1>
</header>

<nav>
  <button class="active" onclick="showPage('matches')">Matches</button>
  <button onclick="showPage('groups')">Teams</button>
  <button onclick="showPage('stats')">Stats</button>
</nav>

<!-- MATCHES -->
<div id="matches" class="page active">
  <h2>Matches</h2>
  <p style="color:#777">Matches will appear here after being added from Admin Panel.</p>
</div>

<!-- GROUPS -->
<div id="groups" class="page">
  <h2>Group Stage</h2>
  <div id="groupsContainer"></div>

  <h2>Knockout Stage</h2>
  <div id="knockout" class="bracket"></div>
</div>

<!-- STATS -->
<div id="stats" class="page">
  <h2>Best Defense</h2>
  <div id="defense"></div>

  <h2>Best Attack</h2>
  <div id="attack"></div>
</div>

<!-- ADMIN -->
<button id="adminBtn" onclick="openAdmin()">Admin Panel</button>

<div id="admin">
  <div class="box">
    <h2>Admin</h2>
    <input id="teamName" placeholder="Team name">
    <select id="groupSelect">
      <option>A</option><option>B</option><option>C</option><option>D</option>
      <option>E</option><option>F</option><option>G</option><option>H</option>
    </select>
    <button onclick="addTeam()">Add Team</button>
    <button onclick="closeAdmin()">Close</button>
  </div>
</div>

<script>
const pages=document.querySelectorAll('.page');
function showPage(id){
  pages.forEach(p=>p.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  event.target.classList.add('active');
}

/* ---------- DATA ---------- */
let groups = JSON.parse(localStorage.getItem('groups')) || {
  A:[],B:[],C:[],D:[],E:[],F:[],G:[],H:[]
};

function save(){
  localStorage.setItem('groups',JSON.stringify(groups));
  renderGroups();
}

/* ---------- GROUPS ---------- */
function renderGroups(){
  const container=document.getElementById('groupsContainer');
  container.innerHTML='';
  for(let g in groups){
    if(groups[g].length===0) continue;

    let sorted=[...groups[g]].sort((a,b)=>{
      if(b.pts!==a.pts) return b.pts-a.pts;
      if(b.gd!==a.gd) return b.gd-a.gd;
      return b.gf-a.gf;
    });

    let html=`<div class="group"><h3 style="color:var(--gold)">Group ${g}</h3>
    <table>
    <tr>
      <th>#</th><th>Team</th><th>P</th><th>W</th><th>D</th><th>L</th>
      <th>GF</th><th>GA</th><th>GD</th><th>Pts</th>
    </tr>`;

    sorted.forEach((t,i)=>{
      html+=`
      <tr class="${i<2?'qualified':''}">
        <td>${i+1}</td>
        <td class="team">${t.name}</td>
        <td>${t.p}</td>
        <td>${t.w}</td>
        <td>${t.d}</td>
        <td>${t.l}</td>
        <td>${t.gf}</td>
        <td>${t.ga}</td>
        <td>${t.gd}</td>
        <td>${t.pts}</td>
      </tr>`;
    });

    html+='</table></div>';
    container.innerHTML+=html;
  }
}
renderGroups();

/* ---------- ADMIN ---------- */
function openAdmin(){document.getElementById('admin').style.display='flex'}
function closeAdmin(){document.getElementById('admin').style.display='none'}

function addTeam(){
  const name=document.getElementById('teamName').value.trim();
  const g=document.getElementById('groupSelect').value;
  if(!name) return;

  groups[g].push({
    name,
    p:0,w:0,d:0,l:0,
    gf:0,ga:0,
    gd:0,pts:0
  });

  document.getElementById('teamName').value='';
  save();
}
</script>
</body>
</html>
