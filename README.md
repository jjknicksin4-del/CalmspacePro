# CalmspacePro
"CalmSpace Pro: your all-in-one stress relief app. Track moods, log thoughts, enjoy guided breathing exercises, see streaks and charts, and improve mental wellness every day"
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>CalmSpace Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
:root {
  --bg:#f0f8ff;
  --card:#fff;
  --accent:#5db1ff;
  --text:#222;
}
body.dark {
  --bg:#121212;
  --card:#1e1e1e;
  --accent:#5db1ff;
  --text:#f0f0f0;
}
body { font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); color: var(--text); margin:0; padding:20px; display:flex; flex-direction:column; align-items:center;}
h1 {margin-bottom:10px;text-align:center;}
.card {background:var(--card); padding:20px; border-radius:20px; max-width:450px; margin:15px 0; box-shadow:0 8px 20px rgba(0,0,0,0.12);}
button {background:var(--accent); color:#fff; border:none; border-radius:14px; padding:12px 18px; margin:6px; cursor:pointer; transition:0.2s;}
button:hover {opacity:0.85;}
.nav {display:flex; justify-content:space-around; flex-wrap:wrap;}
.circle {width:180px; height:180px; border-radius:50%; background: #b3d9ff; display:flex; align-items:center; justify-content:center; font-size:24px; margin:15px auto; transition: transform 4s ease-in-out;}
textarea {width:100%;height:100px;border-radius:12px;padding:10px;margin-top:10px;}
.small {font-size:13px;opacity:.7;}
.streak {margin-top:10px;font-size:14px;color:#555;}
.hidden {display:none;}
</style>
</head>
<body>

<h1>🌿 CalmSpace Pro</h1>

<div class="nav">
<button onclick="show('breathe')">Breathe</button>
<button onclick="show('journal')">Journal</button>
<button onclick="show('mood')">Mood</button>
<button onclick="show('stats')">Stats</button>
<button onclick="show('settings')">Settings</button>
</div>

<!-- Breathing -->
<div class="card" id="breathe">
<h2>Breathing</h2>
<div class="circle" id="circle">Inhale</div>
<button onclick="startBreathing()">Start</button>
<button onclick="toggleSound()">🔊 Sound</button>
<audio id="breathSound" loop>
  <source src="https://www.soundjay.com/nature/sounds/water-flow-1.mp3" type="audio/mpeg">
</audio>
</div>

<!-- Journal -->
<div class="card hidden" id="journal">
<h2>Journal</h2>
<textarea id="journalText" placeholder="Write your thoughts..."></textarea>
<button onclick="saveJournal()">Save</button>
<p class="small" id="journalStatus"></p>
</div>

<!-- Mood -->
<div class="card hidden" id="mood">
<h2>Mood Check</h2>
<button onclick="saveMood(2)">😊 Good</button>
<button onclick="saveMood(1)">😐 Okay</button>
<button onclick="saveMood(0)">😟 Stressed</button>
<p class="small" id="moodStatus"></p>
<p class="streak" id="streakText"></p>
</div>

<!-- Stats -->
<div class="card hidden" id="stats">
<h2>Mood Chart</h2>
<canvas id="chart"></canvas>
</div>

<!-- Settings -->
<div class="card hidden" id="settings">
<h2>Settings</h2>
<button onclick="toggleDark()">Toggle Dark Mode</button>
<p class="small">⚠️ Educational stress-relief app. Not medical advice.</p>
</div>

<script>
// NAVIGATION
function show(id){
  document.querySelectorAll('.card').forEach(c=>c.classList.add('hidden'));
  document.getElementById(id).classList.remove('hidden');
  if(id==='stats') loadChart();
  if(id==='mood') showStreak();
}

// BREATHING + SOUND
let inhale=true, breathingInterval=null;
function startBreathing(){
  const c=document.getElementById("circle");
  if(breathingInterval) clearInterval(breathingInterval);
  breathingInterval=setInterval(()=>{
    inhale=!inhale;
    c.innerText=inhale?"Inhale":"Exhale";
    c.style.transform=inhale?"scale(1.2)":"scale(.8)";
  },4000);
}
function toggleSound(){
  const s=document.getElementById("breathSound");
  s.paused ? s.play() : s.pause();
}

// JOURNAL
function saveJournal(){
  localStorage.setItem("journal",journalText.value);
  document.getElementById("journalStatus").innerText="Saved ✔";
}

// MOOD + STREAKS
function saveMood(val){
  const today=new Date().toLocaleDateString();
  localStorage.setItem("mood_"+today,val);
  document.getElementById("moodStatus").innerText="Mood saved ✔";
  showStreak();
}
function showStreak(){
  let streak=0;
  const keys=Object.keys(localStorage).filter(k=>k.startsWith("mood_")).sort();
  keys.forEach((k,i)=>{
    if(i===0) streak=1;
    else{
      const d1=new Date(keys[i].replace("mood_",""));
      const d2=new Date(keys[i-1].replace("mood_",""));
      const diff=(d1-d2)/(1000*60*60*24);
      if(diff===1) streak++; else streak=1;
    }
  });
  document.getElementById("streakText").innerText="Mood tracking streak: "+streak+" days";
}

// CHART
function loadChart(){
  const labels=[]; const data=[];
  for(let k in localStorage){
    if(k.startsWith("mood_")){
      labels.push(k.replace("mood_",""));
      data.push(localStorage[k]);
    }
  }
  new Chart(document.getElementById("chart"),{
    type:'line',
    data:{labels,datasets:[{label:'Mood (2=Good,1=Ok,0=Stressed)',data,fill:true,backgroundColor:'rgba(93,177,255,0.2)',borderColor:'#5db1ff'}]}
  });
}

// DARK MODE
function toggleDark(){document.body.classList.toggle("dark");}
</script>

</body>
</html>
