
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#071426">
<title>LSM NOVA | Traductor experimental</title>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/drawing_utils/drawing_utils.js"></script>
<style>
:root{--bg:#071426;--panel:#10233a;--panel2:#0a1a2d;--line:#25415f;--text:#e8f2ff;--muted:#9bb0c8;--cyan:#38bdf8;--green:#4ade80;--yellow:#fbbf24;--red:#fb7185}
*{box-sizing:border-box}
body{margin:0;background:radial-gradient(circle at 20% 0%,#123252 0,#071426 42%);color:var(--text);font-family:Inter,Segoe UI,Arial,sans-serif;min-height:100vh}
header{padding:24px 18px 20px;border-bottom:1px solid var(--line);background:#08182bdc}
.top{max-width:1250px;margin:auto;display:flex;justify-content:space-between;align-items:center;gap:16px;flex-wrap:wrap}
.brand{display:flex;gap:13px;align-items:center}
.logo{width:48px;height:48px;border-radius:15px;background:linear-gradient(135deg,#0ea5e9,#2563eb);display:grid;place-items:center;font-weight:900;font-size:20px;box-shadow:0 8px 28px #0284c755}
h1{font-size:24px;margin:0;letter-spacing:.5px}
header p{margin:5px 0 0;color:var(--muted);font-size:13px}
.pill{border:1px solid #256047;color:#86efac;background:#123426;padding:8px 12px;border-radius:999px;font-size:12px}
main{max-width:1250px;margin:auto;padding:22px 16px 40px}
.layout{display:grid;grid-template-columns:minmax(0,1.35fr) minmax(330px,.85fr);gap:18px}
.panel{background:linear-gradient(180deg,#11263e,#0d1e32);border:1px solid var(--line);border-radius:17px;padding:17px;box-shadow:0 12px 35px #02081755;margin-bottom:17px}
.panel h2{font-size:16px;margin:0 0 14px;color:#bae6fd;display:flex;align-items:center;gap:9px}
.camera{position:relative;background:#020817;border-radius:13px;overflow:hidden;aspect-ratio:16/10;border:1px solid #29445f}
video,canvas{position:absolute;inset:0;width:100%;height:100%;object-fit:contain}
video{transform:scaleX(-1)}
canvas{pointer-events:none}
.camera-empty{position:absolute;inset:0;display:grid;place-items:center;text-align:center;color:#7891ac;padding:20px;font-size:14px}
.row{display:flex;gap:9px;flex-wrap:wrap;align-items:center}
.between{display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap}
button{border:0;border-radius:10px;padding:11px 14px;background:#0284c7;color:white;font-weight:700;cursor:pointer;transition:.15s;font-size:13px}
button:hover{filter:brightness(1.12);transform:translateY(-1px)}
button:disabled{opacity:.45;cursor:not-allowed;transform:none}
button.secondary{background:#263d58}
button.green{background:#15803d}
button.orange{background:#a16207}
button.red{background:#be123c}
.status{font-size:13px;color:var(--yellow);margin:11px 0 0;line-height:1.5}
.metrics{display:grid;grid-template-columns:repeat(3,1fr);gap:9px;margin-top:13px}
.metric{background:#081a2d;border:1px solid #1e3854;border-radius:11px;padding:11px}
.metric small{display:block;color:var(--muted);font-size:11px}
.metric strong{display:block;font-size:19px;margin-top:5px;color:#7dd3fc}
label{display:block;color:#c7d8ea;font-size:12px;font-weight:700;margin:12px 0 6px}
input,select,textarea{width:100%;background:#07182a;border:1px solid #34516f;border-radius:9px;color:white;padding:11px;font:inherit;outline:none}
input:focus,select:focus,textarea:focus{border-color:var(--cyan);box-shadow:0 0 0 2px #38bdf833}
textarea{min-height:90px;resize:vertical;font-size:17px;line-height:1.5}
.help{font-size:12px;color:var(--muted);line-height:1.55;margin:9px 0 0}
.result{border:1px solid #25618a;background:linear-gradient(135deg,#092943,#0a1b30);border-radius:13px;padding:16px;text-align:center;margin-bottom:12px}
.result small{color:#a9c5dc;font-size:11px;letter-spacing:1px}
.result h3{font-size:27px;color:#7dd3fc;margin:9px 0}
.result p{margin:0;color:var(--muted);font-size:12px}
.bar{height:7px;background:#20364f;border-radius:99px;overflow:hidden;margin-top:11px}
.bar span{height:100%;display:block;background:linear-gradient(90deg,#0ea5e9,#4ade80);width:0%}
.tabs{display:flex;gap:7px;flex-wrap:wrap;margin-bottom:12px}
.tab{background:#1b324b;color:#cbd5e1}
.tab.active{background:#0369a1;color:white}
.section{display:none}
.section.active{display:block}
.fingergrid{display:grid;grid-template-columns:repeat(4,1fr);gap:7px;margin-top:10px}
.finger{background:#081a2d;border:1px solid #203b56;padding:9px;border-radius:9px;font-size:11px;color:var(--muted)}
.finger b{display:block;margin-top:4px;color:#e2e8f0}
.list{max-height:190px;overflow:auto}
.item{display:flex;justify-content:space-between;gap:10px;border-bottom:1px solid #20364f;padding:9px 2px;font-size:13px}
.item small{color:var(--muted)}
.tag{display:inline-block;padding:4px 7px;border-radius:7px;background:#153b56;color:#bae6fd;font-size:10px}
.warning{background:#3a2c0c;border:1px solid #7c5b13;color:#fde68a;padding:11px;border-radius:10px;font-size:12px;line-height:1.55;margin-top:12px}
.success{color:#86efac}
.muted{color:var(--muted)}
footer{text-align:center;color:#7890aa;font-size:11px;padding:15px}
@media(max-width:850px){.layout{grid-template-columns:1fr}.top{align-items:flex-start}.metrics{grid-template-columns:repeat(3,1fr)}}
@media(max-width:480px){main{padding:13px 10px}.panel{padding:13px}.metrics{grid-template-columns:1fr 1fr}.fingergrid{grid-template-columns:repeat(2,1fr)}h1{font-size:21px}}
</style>
</head>
<body>

<header>
<div class="top">
  <div class="brand">
    <div class="logo">N</div>
    <div>
      <h1>LSM NOVA</h1>
      <p>Plataforma experimental de reconocimiento de señas</p>
    </div>
  </div>
  <span class="pill" id="globalState">● Sistema sin iniciar</span>
</div>
</header>

<main>
<div class="layout">
<section>

  <div class="panel">
    <div class="between">
      <h2>◉ Cámara y seguimiento</h2>
      <span class="tag">Hasta 2 manos</span>
    </div>

    <div class="camera">
      <video id="video" autoplay muted playsinline></video>
      <canvas id="canvas"></canvas>
      <div class="camera-empty" id="cameraEmpty">
        Activa la cámara para comenzar el seguimiento de manos.
      </div>
    </div>

    <div class="row" style="margin-top:13px">
      <button id="startBtn">Iniciar cámara</button>
      <button id="stopBtn" class="red" disabled>Detener</button>
      <button id="recognitionBtn" class="green" disabled>Iniciar reconocimiento</button>
    </div>

    <p class="status" id="cameraStatus">Esperando que inicies la cámara.</p>

    <div class="metrics">
      <div class="metric"><small>Manos visibles</small><strong id="handsMetric">0 / 2</strong></div>
      <div class="metric"><small>FPS aproximados</small><strong id="fpsMetric">0</strong></div>
      <div class="metric"><small>Ejemplos guardados</small><strong id="samplesMetric">0</strong></div>
    </div>

    <div class="fingergrid">
      <div class="finger">Pulgar<b id="thumbState">—</b></div>
      <div class="finger">Índice<b id="indexState">—</b></div>
      <div class="finger">Medio<b id="middleState">—</b></div>
      <div class="finger">Anular<b id="ringState">—</b></div>
      <div class="finger">Meñique<b id="pinkyState">—</b></div>
      <div class="finger">Mano izquierda<b id="leftState">—</b></div>
      <div class="finger">Mano derecha<b id="rightState">—</b></div>
      <div class="finger">Seguimiento<b id="trackingState">Inactivo</b></div>
    </div>
  </div>

  <div class="panel">
    <h2>▤ Resultado de reconocimiento</h2>
    <div class="result">
      <small>SEÑA IDENTIFICADA POR TU DATASET</small>
      <h3 id="prediction">Esperando datos…</h3>
      <p id="predictionInfo">Primero agrega ejemplos etiquetados y activa el reconocimiento.</p>
      <div class="bar"><span id="confidenceBar"></span></div>
    </div>
    <div class="between">
      <small class="muted">Confianza estimada del clasificador</small>
      <strong id="confidenceText">—</strong>
    </div>
    <p class="help">La confianza es una puntuación interna de similitud, no una garantía de traducción correcta. El sistema se abstiene cuando no encuentra una coincidencia suficientemente cercana.</p>
  </div>

  <div class="panel">
    <h2>✎ Texto y voz</h2>
    <label for="translation">Texto construido</label>
    <textarea id="translation" placeholder="Las señas reconocidas y confirmadas aparecerán aquí…"></textarea>

    <div class="row" style="margin-top:10px">
      <button id="speakBtn">🔊 Reproducir voz</button>
      <button id="copyBtn" class="secondary">Copiar texto</button>
      <button id="undoBtn" class="secondary">Deshacer última</button>
      <button id="clearTextBtn" class="red">Limpiar</button>
    </div>

    <div class="row" style="margin-top:8px">
      <label style="margin:0;display:flex;align-items:center;gap:7px">
        <input type="checkbox" id="autoSpeak" style="width:auto"> Voz automática al confirmar
      </label>
      <label style="margin:0;display:flex;align-items:center;gap:7px">
        <input type="checkbox" id="autoAppend" checked style="width:auto"> Agregar resultado confirmado
      </label>
    </div>
    <p class="help">La salida de voz lee el texto construido. No se inventan palabras: solo se incorporan las etiquetas que hayas entrenado y confirmado.</p>
  </div>

</section>

<aside>

  <div class="panel">
    <h2>⚙ Entrenamiento del reconocedor</h2>

    <div class="tabs">
      <button class="tab active" data-tab="staticTab">Seña estática</button>
      <button class="tab" data-tab="dynamicTab">Seña dinámica</button>
    </div>

    <div id="staticTab" class="section active">
      <label for="staticLabel">Etiqueta de la seña</label>
      <input id="staticLabel" maxlength="35" placeholder="Ejemplo: A, HOLA, GRACIAS">
      <p class="help">Coloca la seña frente a la cámara, mantén la posición y guarda varios ejemplos. El modelo aprenderá de las muestras que registres.</p>
      <button id="captureStaticBtn" style="width:100%;margin-top:12px" disabled>＋ Guardar ejemplo estático</button>
    </div>

    <div id="dynamicTab" class="section">
      <label for="dynamicLabel">Etiqueta de la seña en movimiento</label>
      <input id="dynamicLabel" maxlength="35" placeholder="Ejemplo: BUENOS DIAS">
      <p class="help">Prepara la seña y pulsa grabar. El sistema recopilará una secuencia de aproximadamente 2 segundos. Repite varias veces el movimiento.</p>
      <button id="captureDynamicBtn" class="orange" style="width:100%;margin-top:12px" disabled>● Grabar secuencia (2 s)</button>
      <p class="help" id="recordStatus">No se está grabando.</p>
    </div>

    <div class="warning">
      Este es un clasificador experimental por ejemplos (k-NN), no un modelo LSM preentrenado. Necesitas registrar ejemplos reales, etiquetados y validados con personas conocedoras de LSM. Las señas dinámicas y la gramática requieren más desarrollo.
    </div>
  </div>

  <div class="panel">
    <div class="between">
      <h2>▣ Dataset local</h2>
      <span class="tag" id="datasetTag">0 muestras</span>
    </div>

    <div class="metrics" style="grid-template-columns:1fr 1fr">
      <div class="metric"><small>Clases distintas</small><strong id="classesMetric">0</strong></div>
      <div class="metric"><small>Estáticas / dinámicas</small><strong id="typesMetric">0 / 0</strong></div>
    </div>

    <div class="list" id="datasetList" style="margin-top:10px">
      <p class="help">Todavía no hay ejemplos guardados.</p>
    </div>

    <div class="row" style="margin-top:12px">
      <button id="exportBtn" class="secondary">Exportar dataset</button>
      <button id="importBtn" class="secondary">Importar dataset</button>
      <input type="file" id="importFile" accept=".json,application/json" hidden>
      <button id="deleteDatasetBtn" class="red">Borrar dataset</button>
    </div>
    <p class="help">Las muestras se guardan en el almacenamiento local de este navegador. Exporta una copia para respaldarlas o moverlas a otra computadora.</p>
  </div>

  <div class="panel">
    <h2>◷ Historial de traducciones</h2>
    <div class="list" id="historyList">
      <p class="help">Aún no hay traducciones confirmadas.</p>
    </div>
    <button id="clearHistoryBtn" class="secondary" style="margin-top:10px">Limpiar historial</button>
  </div>

  <div class="panel">
    <h2>ⓘ Diagnóstico</h2>
    <div class="item"><span>MediaPipe</span><strong id="mpStatus">No iniciado</strong></div>
    <div class="item"><span>Cámara</span><strong id="camStatus">Desconectada</strong></div>
    <div class="item"><span>Clasificador</span><strong id="classifierStatus">Esperando muestras</strong></div>
    <div class="item"><span>Conexión</span><strong id="networkStatus">—</strong></div>
  </div>

</aside>
</div>
</main>

<footer>LSM NOVA · Prototipo académico experimental · No sustituye a intérpretes ni a una traducción profesional de LSM.</footer>

<script>
"use strict";

const $ = id => document.getElementById(id);

const STORAGE_DATA = "lsmNovaDatasetV1";
const STORAGE_HISTORY = "lsmNovaHistoryV1";

const MAX_SAMPLES = 500;
const FRAMES_PER_SEQUENCE = 20;
const K_NEIGHBORS = 3;
const MIN_SIMILARITY = 0.68;

let cameraStream = null;
let handsModel = null;
let cameraRunning = false;
let recognitionRunning = false;
let sendingFrame = false;

let currentHands = [];
let currentFeatures = null;
let currentFingerInfo = null;

let dataset = loadDataset();
let history = loadHistory();

let recordTimer = null;
let sequenceRecording = false;
let sequenceFrames = [];

let lastPredictionLabel = "";
let stablePredictionCount = 0;
let lastCommittedAt = 0;

let fpsFrames = 0;
let fpsStart = performance.now();

const video = $("video");
const canvas = $("canvas");
const ctx = canvas.getContext("2d");

function loadDataset(){
  try{
    const parsed = JSON.parse(localStorage.getItem(STORAGE_DATA) || "[]");
    return Array.isArray(parsed)
      ? parsed.filter(x => x && x.type && x.label && x.features)
      : [];
  }catch(e){
    return [];
  }
}

function loadHistory(){
  try{
    const parsed = JSON.parse(localStorage.getItem(STORAGE_HISTORY) || "[]");
    return Array.isArray(parsed) ? parsed : [];
  }catch(e){
    return [];
  }
}

function persistDataset(){
  try{
    localStorage.setItem(STORAGE_DATA, JSON.stringify(dataset));
    renderDataset();
  }catch(e){
    alert("No hay espacio suficiente en el almacenamiento del navegador. Exporta o elimina muestras.");
  }
}

function persistHistory(){
  try{
    localStorage.setItem(STORAGE_HISTORY,JSON.stringify(history));
  }catch(e){}
  renderHistory();
}

function setStatus(message,color="var(--yellow)"){
  $("cameraStatus").textContent = message;
  $("cameraStatus").style.color = color;
}

function setGlobal(message,ok=false){
  $("globalState").textContent = "● " + message;
  $("globalState").style.color = ok ? "#86efac" : "#fde68a";
  $("globalState").style.borderColor = ok ? "#256047" : "#7c5b13";
}

function updateNetwork(){
  $("networkStatus").textContent = navigator.onLine ? "En línea" : "Sin conexión";
}

window.addEventListener("online",updateNetwork);
window.addEventListener("offline",updateNetwork);
updateNetwork();

async function startCamera(){
  if(cameraRunning) return;

  try{
    if(!navigator.mediaDevices?.getUserMedia){
      throw new Error("El navegador requiere HTTPS o localhost para utilizar la cámara.");
    }

    setStatus("Solicitando permiso de cámara…");

    cameraStream = await navigator.mediaDevices.getUserMedia({
      audio:false,
      video:{
        facingMode:"user",
        width:{ideal:1280},
        height:{ideal:720},
        frameRate:{ideal:30,max:30}
      }
    });

    video.srcObject = cameraStream;
    await video.play();

    if(typeof Hands === "undefined"){
      throw new Error("No se cargó MediaPipe. Revisa Internet y vuelve a cargar la página.");
    }

    handsModel = new Hands({
      locateFile:file=>"https://cdn.jsdelivr.net/npm/@mediapipe/hands/"+file
    });

    handsModel.setOptions({
      maxNumHands:2,
      modelComplexity:1,
      minDetectionConfidence:0.65,
      minTrackingConfidence:0.6
    });

    handsModel.onResults(onResults);

    cameraRunning = true;

    $("cameraEmpty").style.display = "none";
    $("startBtn").disabled = true;
    $("stopBtn").disabled = false;
    $("recognitionBtn").disabled = false;
    $("captureStaticBtn").disabled = false;
    $("captureDynamicBtn").disabled = false;

    $("camStatus").textContent = "Conectada";
    $("mpStatus").textContent = "Activo";

    setGlobal("Cámara activa",true);
    setStatus("Cámara activa. Coloca una o ambas manos dentro del recuadro.","var(--green)");

    processFrames();

  }catch(error){
    console.error(error);
    setStatus("No se pudo iniciar: "+error.message,"var(--red)");
    stopCamera();
  }
}

async function processFrames(){
  if(!cameraRunning) return;

  if(video.readyState >= 2 && !sendingFrame){
    sendingFrame = true;

    try{
      await handsModel.send({image:video});
    }catch(error){
      console.error("MediaPipe:",error);
    }

    sendingFrame = false;
  }

  if(cameraRunning){
    requestAnimationFrame(processFrames);
  }
}

function onResults(results){

  if(!canvas.width || canvas.width !== video.videoWidth){
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
  }

  ctx.clearRect(0,0,canvas.width,canvas.height);

  const landmarks = results.multiHandLandmarks || [];
  const handed = results.multiHandedness || [];

  currentHands = [];

  for(let i=0;i<landmarks.length;i++){

    const side = (handed[i]?.label || (i===0?"Right":"Left")).toLowerCase();

    currentHands.push({
      side,
      landmarks:landmarks[i]
    });

    if(typeof drawConnectors==="function"){
      drawConnectors(ctx,landmarks[i],HAND_CONNECTIONS,{
        color:"#38bdf8",
        lineWidth:3
      });

      drawLandmarks(ctx,landmarks[i],{
        color:"#facc15",
        lineWidth:1,
        radius:3
      });
    }
  }

  $("handsMetric").textContent = landmarks.length+" / 2";

  $("trackingState").textContent = landmarks.length ? "Activo" : "Sin manos";

  $("leftState").textContent =
    currentHands.some(h=>h.side==="left") ? "Detectada" : "—";

  $("rightState").textContent =
    currentHands.some(h=>h.side==="right") ? "Detectada" : "—";

  if(landmarks.length){

    currentFingerInfo = getFingerInfo(landmarks[0]);

    $("thumbState").textContent =
      currentFingerInfo.thumb ? "Extendido" : "Flexionado";

    $("indexState").textContent =
      currentFingerInfo.index ? "Extendido" : "Flexionado";

    $("middleState").textContent =
      currentFingerInfo.middle ? "Extendido" : "Flexionado";

    $("ringState").textContent =
      currentFingerInfo.ring ? "Extendido" : "Flexionado";

    $("pinkyState").textContent =
      currentFingerInfo.pinky ? "Extendido" : "Flexionado";

  }else{

    ["thumbState","indexState","middleState","ringState","pinkyState"]
      .forEach(id=>$(id).textContent="—");

    currentFingerInfo = null;
  }

  currentFeatures = extractFeatures(currentHands);

  if(sequenceRecording && currentFeatures){
    sequenceFrames.push(currentFeatures);

    if(sequenceFrames.length >= FRAMES_PER_SEQUENCE){
      finishDynamicCapture();
    }
  }

  fpsFrames++;

  const now = performance.now();

  if(now-fpsStart>=1000){
    $("fpsMetric").textContent =
      Math.round(fpsFrames*1000/(now-fpsStart));

    fpsFrames=0;
    fpsStart=now;
  }

  if(recognitionRunning && dataset.length){
    runRecognition(currentFeatures);
  }
}

function getFingerInfo(lm){

  const distance=(a,b)=>
    Math.hypot(lm[a].x-lm[b].x,lm[a].y-lm[b].y);

  return {
    thumb: distance(4,5)>distance(3,5)*1.12,
    index: lm[8].y<lm[6].y,
    middle: lm[12].y<lm[10].y,
    ring: lm[16].y<lm[14].y,
    pinky: lm[20].y<lm[18].y
  };
}

function extractFeatures(hands){

  if(!hands || !hands.length) return null;

  const ordered = {
    left:null,
    right:null
  };

  hands.forEach(h=>{
    if(h.side==="left" || h.side==="right"){
      ordered[h.side]=h.landmarks;
    }
  });

  if(!ordered.left && !ordered.right){
    ordered.right=hands[0].landmarks;
  }

  const output=[];

  ["left","right"].forEach(side=>{

    const lm=ordered[side];

    if(!lm){
      output.push(0);
      for(let i=0;i<63;i++) output.push(0);
      return;
    }

    output.push(1);

    const wrist=lm[0];

    const scale=Math.max(
      0.035,
      Math.hypot(lm[5].x-wrist.x,lm[5].y-wrist.y)
    );

    for(let i=0;i<21;i++){
      output.push((lm[i].x-wrist.x)/scale);
      output.push((lm[i].y-wrist.y)/scale);
      output.push((lm[i].z-(wrist.z||0))/scale);
    }
  });

  return output;
}

function distanceVectors(a,b){

  if(!a || !b || a.length!==b.length){
    return Infinity;
  }

  let sum=0;
  let count=0;

  for(let i=0;i<a.length;i++){

    if(i===0 || i===64){

      if(a[i]!==b[i]){
        sum+=0.8;
      }

      count++;

    }else{

      const slot=i<64?0:1;
      const present=a[slot] && b[slot];

      if(present){
        const d=a[i]-b[i];
        sum+=d*d;
        count++;
      }
    }
  }

  return Math.sqrt(sum/Math.max(1,count));
}

function runRecognition(features){

  if(!features){

    $("prediction").textContent="Sin manos";
    $("predictionInfo").textContent="Coloca las manos frente a la cámara.";
    $("confidenceText").textContent="—";
    $("confidenceBar").style.width="0%";

    lastPredictionLabel="";
    stablePredictionCount=0;

    return;
  }

  const typeSamples=dataset.filter(s=>s.type==="static");

  if(!typeSamples.length){

    $("prediction").textContent="Entrena una seña";
    $("predictionInfo").textContent="Agrega ejemplos estáticos para comenzar.";

    return;
  }

  const nearest=typeSamples
    .map(s=>({
      label:s.label,
      d:distanceVectors(features,s.features)
    }))
    .sort((a,b)=>a.d-b.d)
    .slice(0,K_NEIGHBORS);

  const votes={};

  nearest.forEach(n=>{
    votes[n.label]=(votes[n.label]||0)+1/(n.d+0.04);
  });

  const ranking=Object.entries(votes)
    .sort((a,b)=>b[1]-a[1]);

  if(!ranking.length) return;

  const [label,score]=ranking[0];

  const bestDistance=nearest[0].d;

  const similarity=Math.max(
    0,
    Math.min(1,1-bestDistance/1.5)
  );

  const confidence=Math.round(similarity*100);

  $("confidenceText").textContent=confidence+"%";
  $("confidenceBar").style.width=confidence+"%";

  if(similarity<MIN_SIMILARITY){

    $("prediction").textContent="Seña no reconocida";

    $("predictionInfo").textContent=
      "La muestra no se parece suficientemente a las clases registradas.";

    lastPredictionLabel="";
    stablePredictionCount=0;

    return;
  }

  $("prediction").textContent=label;

  $("predictionInfo").textContent=
    "Coincidencia experimental con ejemplos guardados.";

  if(label===lastPredictionLabel){
    stablePredictionCount++;
  }else{
    lastPredictionLabel=label;
    stablePredictionCount=1;
  }

  if(stablePredictionCount>=12 && Date.now()-lastCommittedAt>1800){

    lastCommittedAt=Date.now();
    stablePredictionCount=0;

    if($("autoAppend").checked){
      commitTranslation(label);
    }
  }
}

function captureStatic(){

  const label=$("staticLabel").value
    .trim()
    .toLocaleUpperCase("es-MX");

  if(!label){
    alert("Escribe la etiqueta de la seña.");
    return;
  }

  if(!currentFeatures){
    alert("No se detecta ninguna mano. Coloca la seña frente a la cámara.");
    return;
  }

  if(dataset.length>=MAX_SAMPLES){
    alert("Alcanzaste el límite de muestras. Exporta el dataset o elimina algunas muestras.");
    return;
  }

  dataset.push({
    id:crypto.randomUUID?crypto.randomUUID():String(Date.now()+Math.random()),
    label,
    type:"static",
    features:currentFeatures,
    createdAt:new Date().toISOString()
  });

  persistDataset();

  setStatus("Ejemplo estático guardado para: "+label,"var(--green)");
}

function startDynamicCapture(){

  if(sequenceRecording) return;

  const label=$("dynamicLabel").value
    .trim()
    .toLocaleUpperCase("es-MX");

  if(!label){
    alert("Escribe la etiqueta de la seña dinámica.");
    return;
  }

  if(!currentFeatures){
    alert("Primero coloca las manos frente a la cámara.");
    return;
  }

  sequenceFrames=[];
  sequenceRecording=true;

  $("captureDynamicBtn").disabled=true;

  $("recordStatus").textContent=
    "Grabando movimiento… mueve las manos de forma natural.";

  recordTimer=setTimeout(finishDynamicCapture,2600);
}

function finishDynamicCapture(){

  if(!sequenceRecording) return;

  sequenceRecording=false;

  clearTimeout(recordTimer);

  const label=$("dynamicLabel").value
    .trim()
    .toLocaleUpperCase("es-MX");

  if(sequenceFrames.length<5){

    $("recordStatus").textContent=
      "No se capturaron suficientes cuadros. Inténtalo otra vez.";

    $("captureDynamicBtn").disabled=!cameraRunning;

    return;
  }

  const fixed=resampleSequence(
    sequenceFrames,
    FRAMES_PER_SEQUENCE
  );

  if(dataset.length<MAX_SAMPLES){

    dataset.push({
      id:crypto.randomUUID?crypto.randomUUID():String(Date.now()+Math.random()),
      label,
      type:"dynamic",
      features:fixed,
      createdAt:new Date().toISOString()
    });

    persistDataset();

    $("recordStatus").textContent=
      "Secuencia guardada: "+sequenceFrames.length+" cuadros originales.";

  }else{

    $("recordStatus").textContent=
      "Dataset lleno. Exporta o elimina muestras.";
  }

  sequenceFrames=[];
  $("captureDynamicBtn").disabled=!cameraRunning;
}

function resampleSequence(frames,target){

  const result=[];

  for(let i=0;i<target;i++){

    const index=Math.round(
      i*(frames.length-1)/(target-1)
    );

    result.push(frames[index]);
  }

  return result;
}

function commitTranslation(label){

  const field=$("translation");
  const current=field.value.trim();

  field.value=current ? current+" "+label : label;

  history.unshift({
    label,
    date:new Date().toLocaleString("es-MX")
  });

  history=history.slice(0,100);

  persistHistory();

  if($("autoSpeak").checked){
    speakText();
  }
}

function speakText(){

  const text=$("translation").value.trim();

  if(!text){
    alert("No hay texto para reproducir.");
    return;
  }

  if(!("speechSynthesis" in window)){
    alert("Este navegador no admite síntesis de voz.");
    return;
  }

  speechSynthesis.cancel();

  const utterance=new SpeechSynthesisUtterance(text);

  utterance.lang="es-MX";
  utterance.rate=0.92;
  utterance.pitch=1;

  speechSynthesis.speak(utterance);
}

function stopCamera(){

  recognitionRunning=false;

  $("recognitionBtn").textContent="Iniciar reconocimiento";
  $("recognitionBtn").classList.add("green");

  cameraRunning=false;
  sendingFrame=false;

  if(sequenceRecording){
    sequenceRecording=false;
    clearTimeout(recordTimer);
    sequenceFrames=[];
  }

  if(cameraStream){
    cameraStream.getTracks().forEach(t=>t.stop());
    cameraStream=null;
  }

  if(handsModel){
    handsModel.close();
    handsModel=null;
  }

  video.srcObject=null;

  ctx.clearRect(0,0,canvas.width,canvas.height);

  $("cameraEmpty").style.display="grid";

  $("startBtn").disabled=false;
  $("stopBtn").disabled=true;
  $("recognitionBtn").disabled=true;
  $("captureStaticBtn").disabled=true;
  $("captureDynamicBtn").disabled=true;

  $("camStatus").textContent="Desconectada";
  $("mpStatus").textContent="Detenido";
  $("trackingState").textContent="Inactivo";
  $("handsMetric").textContent="0 / 2";

  setStatus("Cámara detenida.");
  setGlobal("Sistema detenido");

  currentHands=[];
  currentFeatures=null;
}

function renderDataset(){

  const labels=[...new Set(dataset.map(s=>s.label))];

  const stat=dataset.filter(s=>s.type==="static").length;
  const dyn=dataset.filter(s=>s.type==="dynamic").length;

  $("samplesMetric").textContent=dataset.length;
  $("datasetTag").textContent=dataset.length+" muestras";
  $("classesMetric").textContent=labels.length;
  $("typesMetric").textContent=stat+" / "+dyn;

  $("classifierStatus").textContent=
    stat?"Con "+stat+" muestras estáticas":"Esperando muestras";

  const counts={};

  dataset.forEach(s=>{
    const key=s.label+"|"+s.type;
    counts[key]=(counts[key]||0)+1;
  });

  const container=$("datasetList");

  container.innerHTML="";

  if(!Object.keys(counts).length){

    container.innerHTML=
      '<p class="help">Todavía no hay ejemplos guardados.</p>';

    return;
  }

  Object.entries(counts).sort().forEach(([key,count])=>{

    const [label,type]=key.split("|");

    const row=document.createElement("div");
    row.className="item";

    const name=document.createElement("span");
    name.textContent=label+" ";

    const tag=document.createElement("span");
    tag.className="tag";
    tag.textContent=type==="static"?"Estática":"Dinámica";

    name.appendChild(tag);

    const number=document.createElement("strong");
    number.textContent=count+" ej.";

    row.append(name,number);

    container.appendChild(row);
  });
}

function renderHistory(){

  const container=$("historyList");

  container.innerHTML="";

  if(!history.length){

    container.innerHTML=
      '<p class="help">Aún no hay traducciones confirmadas.</p>';

    return;
  }

  history.slice(0,30).forEach(item=>{

    const row=document.createElement("div");
    row.className="item";

    const label=document.createElement("strong");
    label.textContent=item.label;

    const date=document.createElement("small");
    date.textContent=item.date;

    row.append(label,date);

    container.appendChild(row);
  });
}

/* EVENTOS DE LOS BOTONES */

$("startBtn").addEventListener("click",startCamera);

$("stopBtn").addEventListener("click",stopCamera);

$("captureStaticBtn").addEventListener("click",captureStatic);

$("captureDynamicBtn").addEventListener("click",startDynamicCapture);

$("recognitionBtn").addEventListener("click",()=>{

  recognitionRunning=!recognitionRunning;

  $("recognitionBtn").textContent=
    recognitionRunning
      ?"Detener reconocimiento"
      :"Iniciar reconocimiento";

  $("recognitionBtn").classList.toggle(
    "green",
    !recognitionRunning
  );

  if(recognitionRunning){

    if(!dataset.some(s=>s.type==="static")){

      recognitionRunning=false;

      $("recognitionBtn").textContent="Iniciar reconocimiento";

      alert("Primero guarda ejemplos estáticos de una o más señas.");

      return;
    }

    setGlobal("Reconocimiento activo",true);

    setStatus(
      "Reconocimiento experimental activo.",
      "var(--green)"
    );

  }else{

    setGlobal("Cámara activa",true);

    $("prediction").textContent="Reconocimiento pausado";
  }
});

$("speakBtn").addEventListener("click",speakText);

$("copyBtn").addEventListener("click",async()=>{

  try{

    await navigator.clipboard.writeText(
      $("translation").value
    );

    alert("Texto copiado.");

  }catch(e){

    $("translation").select();

    alert("Selecciona y copia el texto manualmente.");
  }
});

$("undoBtn").addEventListener("click",()=>{

  const parts=$("translation").value.trim().split(/\s+/);

  parts.pop();

  $("translation").value=parts.join(" ");
});

$("clearTextBtn").addEventListener("click",()=>{
  $("translation").value="";
});

$("clearHistoryBtn").addEventListener("click",()=>{

  if(confirm("¿Borrar el historial de traducciones?")){

    history=[];

    persistHistory();
  }
});

$("deleteDatasetBtn").addEventListener("click",()=>{

  if(confirm("¿Borrar todas las muestras entrenadas en este navegador?")){

    dataset=[];

    persistDataset();

    $("prediction").textContent="Dataset vacío";

    $("predictionInfo").textContent=
      "Registra ejemplos nuevos para comenzar.";
  }
});

$("exportBtn").addEventListener("click",()=>{

  const blob=new Blob([
    JSON.stringify({
      app:"LSM NOVA",
      version:1,
      exportedAt:new Date().toISOString(),
      dataset
    },null,2)
  ],{
    type:"application/json"
  });

  const url=URL.createObjectURL(blob);

  const a=document.createElement("a");

  a.href=url;
  a.download="lsm-nova-dataset.json";

  a.click();

  URL.revokeObjectURL(url);
});

$("importBtn").addEventListener("click",()=>{
  $("importFile").click();
});

$("importFile").addEventListener("change",async e=>{

  const file=e.target.files[0];

  if(!file) return;

  try{

    const parsed=JSON.parse(await file.text());

    const incoming=Array.isArray(parsed)
      ?parsed
      :parsed.dataset;

    if(!Array.isArray(incoming)){
      throw new Error("Formato no válido");
    }

    const valid=incoming.filter(s=>
      s &&
      typeof s.label==="string" &&
      ["static","dynamic"].includes(s.type) &&
      Array.isArray(s.features)
    );

    dataset=[...dataset,...valid].slice(0,MAX_SAMPLES);

    persistDataset();

    alert("Se importaron "+valid.length+" muestras válidas.");

  }catch(err){

    alert("No se pudo importar: "+err.message);
  }

  e.target.value="";
});

/* PESTAÑAS */

document.querySelectorAll(".tab").forEach(btn=>{

  btn.addEventListener("click",()=>{

    document.querySelectorAll(".tab").forEach(b=>
      b.classList.remove("active")
    );

    document.querySelectorAll(".section").forEach(s=>
      s.classList.remove("active")
    );

    btn.classList.add("active");

    $(btn.dataset.tab).classList.add("active");
  });
});

/* CIERRE SEGURO DE CÁMARA */

window.addEventListener("beforeunload",()=>{

  if(cameraStream){
    cameraStream.getTracks().forEach(t=>t.stop());
  }
});

/* INICIALIZACIÓN */

renderDataset();
renderHistory();

setGlobal("Sistema listo");

$("classifierStatus").textContent=
  dataset.length
    ?"Dataset cargado"
    :"Esperando muestras";

</script>
</body>
</html>
