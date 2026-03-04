<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>1M DA — Suivi quotidien</title>
<style>
  :root{--bg:#071018;--card:#0f1720;--muted:#94a3b8;--accent:#16a34a}
  *{box-sizing:border-box} body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial;background:var(--bg);color:#e6eef6;padding:16px}
  h1{margin:0 0 6px 0;font-size:20px} .muted{color:var(--muted);font-size:13px;margin-bottom:10px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:12px;border-radius:10px;margin-bottom:12px;border:1px solid rgba(255,255,255,0.03)}
  .row{display:flex;justify-content:space-between;align-items:center} .small{color:var(--muted);font-size:13px} .big{font-weight:700;font-size:18px}
  .progress{position:relative;height:14px;background:#0c1620;border-radius:999px;overflow:hidden;margin-top:10px;border:1px solid rgba(255,255,255,0.02)}
  .bar{height:100%;width:0;background:linear-gradient(90deg,#22c55e,#16a34a);transition:width 900ms cubic-bezier(.22,.9,.3,1)}
  .arrow{position:absolute;top:-30px;left:0;transform:translateX(-50%);display:flex;flex-direction:column;align-items:center;pointer-events:none;transition:left 900ms cubic-bezier(.22,.9,.3,1)}
  .bubble{background:#08121a;padding:6px 8px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);font-size:13px}
  .tip{width:0;height:0;border-left:8px solid transparent;border-right:8px solid transparent;border-top:10px solid #08121a;margin-top:2px}
  button{background:#1f2937;color:#fff;border:0;padding:8px 10px;margin:6px 6px 0 0;border-radius:8px;cursor:pointer;font-size:14px}
  button.green{background:#15803d} button.red{background:#b91c1c}
  .grid{display:grid;gap:8px;margin-top:10px} .day{display:flex;justify-content:space-between;align-items:center;background:#0b1520;padding:8px;border-radius:8px;font-size:13px;border:1px solid rgba(255,255,255,0.02)}
  input[type=checkbox]{width:18px;height:18px} footer{color:var(--muted);font-size:12px;text-align:center;margin-top:10px}
</style>
</head>
<body>
  <h1>1M DA — Suivi quotidien</h1>
  <div class="muted">Début : 02/03/2026 • Gain : 3 500 DA / jour</div>

  <div class="card" aria-label="Progression">
    <div class="row">
      <div><div class="small">Solde actuel</div><div class="big" id="solde">-- DA</div></div>
      <div class="small">Objectif : 1 000 000 DA</div>
    </div>

    <div class="progress" id="progress">
      <div class="bar" id="bar"></div>
      <div class="arrow" id="arrow" style="left:0%"><div class="bubble" id="bubble">0 %</div><div class="tip"></div></div>
    </div>

    <div class="small" id="jours">Jours : -- / --</div>
    <div style="margin-top:8px">
      <button class="green" id="mark">✅ Marquer aujourd'hui</button>
      <button id="export">⬇️ Exporter CSV</button>
      <button class="red" id="reset">Réinitialiser</button>
    </div>
  </div>

  <div class="card"><div><b>Motivation :</b> <span id="mot">Chaque jour compte — continue !</span></div>
    <div style="font-size:12px;color:var(--muted);margin-top:6px">Date de fin estimée : <span id="end">--</span></div>
  </div>

  <div class="card"><div class="grid" id="liste"></div></div>
  <footer>Les données sont stockées localement sur ton appareil.</footer>

<script>
(function(){
  const START=550000,GAIN=3500,TARGET=1000000,DEBUT=new Date(2026,2,2);
  const KEY='1MDA_v_final'; const TOTAL=Math.ceil((TARGET-START)/GAIN);
  const LAST=new Date(DEBUT); LAST.setDate(LAST.getDate()+TOTAL-1);
  let done=new Set();
  try{ const r=localStorage.getItem(KEY); if(r) JSON.parse(r).forEach(i=>done.add(i)); }catch(e){}
  const solde=document.getElementById('solde'),bar=document.getElementById('bar'),bubble=document.getElementById('bubble'),
        arrow=document.getElementById('arrow'),jours=document.getElementById('jours'),liste=document.getElementById('liste'),
        mot=document.getElementById('mot'),end=document.getElementById('end');
  const msgs=["Chaque jour compte — continue !","Ton objectif se rapproche.","Petits pas, grand résultat.","Bravo, garde le cap.","Souviens-toi de ton but."];
  function fmtDA(n){return n.toLocaleString('fr-FR')+' DA'} function fmtDate(d){return d.toLocaleDateString('fr-FR')}
  function calc(){const n=done.size,b=START+n*GAIN,p=Math.min(100,(b/TARGET)*100);const rem=Math.max(0,TARGET-b);return{n,b,p,remDays:Math.ceil(rem/GAIN)}}
  function save(){try{localStorage.setItem(KEY,JSON.stringify([...done]));}catch(e){}}
  function move(p){arrow.style.left=p+'%';bubble.textContent=Math.round(p*100)/100+' %';if(p<6){arrow.style.transform='translateX(0)';bubble.style.transform='translateX(8px)'}else if(p>94){arrow.style.transform='translateX(-100%)';bubble.style.transform='translateX(-8px)'}else{arrow.style.transform='translateX(-50%)';bubble.style.transform='translateX(0)'}}
  function render(){const s=calc();solde.textContent=fmtDA(s.b);bar.style.width='0%';setTimeout(()=>bar.style.width=s.p+'%',50);move(s.p);jours.textContent='Jours : '+s.n+' / '+TOTAL+' • Restants : '+s.remDays;mot.textContent=msgs[s.n%msgs.length];end.textContent=fmtDate(LAST);
    liste.innerHTML='';for(let i=0;i<TOTAL;i++){const d=new Date(DEBUT);d.setDate(d.getDate()+i);const ch=done.has(i)?'checked':'';const div=document.createElement('div');div.className='day';div.innerHTML='<div>'+ (i+1) +'. '+ fmtDate(d) +'</div><div><input type=\"checkbox\" data-i=\"'+i+'\" '+ch+'></div>';liste.appendChild(div)}
    liste.querySelectorAll('input[type=checkbox]').forEach(cb=>cb.onchange=e=>{const idx=Number(e.target.dataset.i);if(e.target.checked)done.add(idx);else done.delete(idx);save();render()})}
  document.getElementById('mark').onclick=()=>{const now=new Date();const idx=[...Array(TOTAL).keys()].find(i=>{const d=new Date(DEBUT);d.setDate(d.getDate()+i);return d.getFullYear()===now.getFullYear()&&d.getMonth()===now.getMonth()&&d.getDate()===now.getDate()});if(idx===undefined){alert('Date hors période (02/03/2026 → '+fmtDate(LAST)+')');return}done.has(idx)?done.delete(idx):done.add(idx);save();render()}
  document.getElementById('reset').onclick=()=>{if(confirm('Réinitialiser tout le suivi ?')){done.clear();save();render()}}
  document.getElementById('export').onclick=()=>{let csv='jour,date,fait,gain,cumul\\n';let cumul=START;for(let i=0;i<TOTAL;i++){const d=new Date(DEBUT);d.setDate(d.getDate()+i);const f=done.has(i)?1:0;if(f)cumul+=GAIN;csv+=`${i+1},${fmtDate(d)},${f},${GAIN},${cumul}\\n`}const a=document.createElement('a');a.href=URL.createObjectURL(new Blob([csv],{type:'text/csv'}));a.download='1MDA_progress.csv';a.click()}
  // initial render
  render();
})();
</script>
</body>
</html>
