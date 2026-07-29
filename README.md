<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>warm isolation — webxr hangout</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Instrument+Serif:ital@0;1&family=Space+Grotesk:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<style>
:root{--bg:#0b0705;--cream:#f3e5d0;--amber:#ffb45e;--amber2:#ff9a3c;--muted:#a08a72;--dim:#8d7a63;}
*{margin:0;padding:0;box-sizing:border-box}
html,body{height:100%;overflow:hidden;background:var(--bg);color:var(--cream);font-family:"Space Grotesk",system-ui,sans-serif;-webkit-font-smoothing:antialiased}
#gl{position:fixed;inset:0;display:block}
#grain{position:fixed;inset:-120px;z-index:40;pointer-events:none;opacity:.055;background-image:url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="300" height="300"><filter id="n"><feTurbulence type="fractalNoise" baseFrequency="0.9" numOctaves="2"/></filter><rect width="300" height="300" filter="url(%23n)"/></svg>');animation:gr 1.1s steps(3) infinite}
@keyframes gr{0%{transform:translate(0,0)}33%{transform:translate(-42px,28px)}66%{transform:translate(30px,-46px)}100%{transform:translate(0,0)}}
#menu{position:fixed;inset:0;z-index:60;display:flex;flex-direction:column;justify-content:space-between;padding:clamp(16px,3.5vw,44px);transition:opacity .7s,visibility .7s;background:linear-gradient(100deg,rgba(10,6,4,.94) 0%,rgba(10,6,4,.6) 42%,rgba(10,6,4,.12) 74%,transparent 100%)}
#menu::after{content:"";position:absolute;inset:auto 0 0 0;height:36%;pointer-events:none;background:linear-gradient(to top,rgba(10,6,4,.9),transparent)}
body.playing #menu{opacity:0;visibility:hidden}
.m-top{display:flex;justify-content:space-between;align-items:center;gap:16px;position:relative;z-index:2}
.m-tag{font-size:11px;letter-spacing:.22em;text-transform:uppercase;color:var(--amber);display:flex;align-items:center;gap:10px}
.led{width:8px;height:8px;border-radius:50%;background:var(--amber2);display:inline-block;box-shadow:0 0 12px rgba(255,154,60,.9);animation:pulse 2.2s ease-in-out infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.4;transform:scale(.78)}}
.m-main{position:relative;z-index:2;max-width:760px}
.m-title{font-family:"Instrument Serif",serif;font-weight:400;font-size:clamp(50px,10vw,126px);line-height:.88;letter-spacing:-.02em;text-shadow:0 4px 60px rgba(0,0,0,.6)}
.m-title em{font-style:italic;color:var(--amber);text-shadow:0 0 44px rgba(255,160,80,.45)}
.m-sub{margin-top:12px;font-size:13.5px;font-weight:300;letter-spacing:.04em;color:#d8c6ac}
.m-ctrl{margin-top:12px;display:flex;flex-wrap:wrap;gap:7px 16px;font-size:12px;color:var(--muted);align-items:center}
kbd{font-family:"Space Grotesk",sans-serif;font-size:10px;letter-spacing:.12em;color:var(--cream);border:1px solid rgba(243,229,208,.28);background:rgba(255,255,255,.045);padding:3px 8px;border-radius:5px;margin-right:7px;text-transform:uppercase}
.panel{background:rgba(16,9,5,.72);border:1px solid rgba(255,180,94,.25);border-left:3px solid var(--amber);padding:12px 15px;border-radius:8px}
.m-net{display:flex;align-items:center;gap:12px;flex-wrap:wrap;max-width:620px;margin-top:16px;cursor:default}
.m-net label{font-size:10px;letter-spacing:.2em;text-transform:uppercase;color:var(--dim);display:flex;flex-direction:column;gap:6px}
.m-net input{font-family:"Space Grotesk",sans-serif;font-size:16px;letter-spacing:.14em;text-transform:uppercase;color:var(--amber);background:#0d0704;border:1px solid rgba(255,180,94,.35);border-radius:6px;padding:12px 14px;outline:none;width:150px;transition:border-color .2s,box-shadow .2s}
.m-net input#nameIn{text-transform:none}
.m-net input:focus{border-color:var(--amber);box-shadow:0 0 0 3px rgba(255,180,94,.18)}
.m-net button{font-family:"Space Grotesk",sans-serif;font-size:11px;font-weight:600;letter-spacing:.16em;text-transform:uppercase;color:#1c0f05;background:var(--amber);border:none;border-radius:6px;padding:14px 18px;cursor:pointer;transition:background .18s,transform .18s}
.m-net button:hover{background:#ffc57e;transform:translateY(-2px)}
#netStatus{font-size:11px;color:var(--muted);font-style:italic;width:100%}
.m-actions{position:relative;z-index:2;display:flex;align-items:center;gap:16px;flex-wrap:wrap;margin-top:16px}
.btn{font-family:"Space Grotesk",sans-serif;font-size:14px;font-weight:600;letter-spacing:.16em;text-transform:uppercase;padding:16px 28px;border-radius:9px;cursor:pointer;border:none;display:inline-flex;align-items:center;gap:13px;color:#1c0f05;background:var(--amber);box-shadow:0 6px 30px rgba(255,160,80,.3);transition:transform .18s,box-shadow .18s,background .18s}
.btn:hover{transform:translateY(-3px);background:#ffc57e;box-shadow:0 16px 46px rgba(255,160,80,.5)}
#xrWarn{position:relative;z-index:2;margin-top:12px;max-width:52ch;font-size:12px;line-height:1.6;color:#ffcf9a;border-left:2px solid var(--amber2);padding:8px 14px;background:rgba(255,140,50,.07);border-radius:0 6px 6px 0;display:none}
#xrWarn.show{display:block}
#xrLive{position:fixed;left:50%;bottom:26px;z-index:55;transform:translateX(-50%);display:flex;align-items:center;gap:10px;font-size:11px;letter-spacing:.18em;text-transform:uppercase;color:#ffd9a8;background:rgba(13,7,4,.85);border:1px solid rgba(255,180,94,.35);padding:10px 18px;border-radius:20px;opacity:0;transition:opacity .5s;pointer-events:none}
body.playing #xrLive{opacity:1}
#xrLive i{width:8px;height:8px;border-radius:50%;background:#ff6a3c;box-shadow:0 0 10px rgba(255,106,60,.9);animation:pulse 1.4s ease-in-out infinite}
#toast{position:fixed;left:50%;top:26px;z-index:70;transform:translate(-50%,-90px);background:#1a110a;border:1px solid rgba(255,180,94,.45);color:#ffd9a8;padding:11px 20px;border-radius:8px;font-size:13px;font-style:italic;transition:transform .4s cubic-bezier(.2,.9,.3,1.2);pointer-events:none;box-shadow:0 10px 40px rgba(0,0,0,.5);max-width:84vw;text-align:center}
#toast.show{transform:translate(-50%,0)}
#fatal{position:fixed;inset:0;z-index:80;display:none;place-items:center;text-align:center;background:#0b0705}
#fatal h2{font-family:"Instrument Serif",serif;font-style:italic;font-weight:400;font-size:52px;color:#ff7a4a}
#fatal p{margin:14px auto 0;max-width:52ch;font-size:13px;line-height:1.7;color:var(--muted);font-family:ui-monospace,monospace}
</style>
</head>
<body>
<canvas id="gl"></canvas><div id="grain"></div>
<div id="menu">
<div class="m-top"><span class="m-tag"><i class="led"></i>webxr &middot; shared-night hangout</span></div>
<div class="m-main">
<h1 class="m-title">warm<br><em>isolation</em></h1>
<p class="m-sub">one warm room &middot; one shared wallet &middot; twenty-four floors of staircase down</p>
<div class="m-ctrl">
<span><kbd>grip</kbd>grab</span><span><kbd>trigger</kbd>use / eat</span>
<span><kbd>l-stick</kbd>walk</span><span><kbd>r-stick</kbd>turn</span>
<span><kbd>kitchen</kbd>cook &amp; eat</span><span><kbd>phone</kbd>911 &middot; 666 &middot; 555-7499</span>
</div>
<div class="panel m-net" id="netBox">
<label>your name<input id="nameIn" maxlength="14" placeholder="wanderer"></label>
<label>room code<input id="codeIn" maxlength="4" placeholder="WARM"></label>
<button id="netBtn">link up</button>
<span id="netStatus">same code = same room, same wallet, anywhere on earth.</span>
</div>
<div class="m-actions">
<button id="btnVR" class="btn">
<svg width="24" height="15" viewBox="0 0 24 15" fill="none" aria-hidden="true"><path d="M2 3.5C2 2.1 3.1 1 4.5 1h15C20.9 1 22 2.1 22 3.5v6c0 1.4-1.1 2.5-2.5 2.5h-3.2c-.9 0-1.7-.5-2.1-1.3l-.8-1.5c-.6-1.2-2.2-1.2-2.8 0l-.8 1.5c-.4.8-1.2 1.3-2.1 1.3H4.5C3.1 12 2 10.9 2 9.5v-6z" stroke="currentColor" stroke-width="1.7"/></svg>
enter vr
</button>
</div>
<div id="xrWarn">no webxr detected &mdash; open this page in a headset browser (meta quest browser, wolvic&hellip;).</div>
</div>
<div></div>
</div>
<div id="xrLive"><i></i>vr session active &mdash; headset view mirrored here</div>
<div id="toast"></div>
<div id="fatal"><div><h2>signal lost</h2><p id="fatalMsg"></p></div></div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){var urls=['https://cdnjs.cloudflare.com/ajax/libs/mqtt/5.3.5/mqtt.min.js','https://unpkg.com/mqtt@5.3.5/dist/mqtt.min.js','https://cdn.jsdelivr.net/npm/mqtt@5.3.5/dist/mqtt.min.js'];
(function next(i){if(i>=urls.length)return;var s=document.createElement('script');s.src=urls[i];s.onerror=function(){next(i+1);};document.head.appendChild(s);})(0);})();
</script>
<script>
(function(){
'use strict';
var booted=false;
function fatal(m){document.getElementById('fatal').style.display='grid';document.getElementById('fatalMsg').textContent=m;}
window.addEventListener('error',function(e){if(!booted)fatal(String(e.message||e.error));});
if(!window.THREE){fatal('three.js could not load — this viewer may block external scripts.');return;}
try{main();}catch(err){fatal(String(err&&err.message||err));}
function main(){
var clamp=THREE.MathUtils.clamp,lerp=THREE.MathUtils.lerp;
var easeOut=function(k){return 1-(1-k)*(1-k);};
var rand=function(a,b){return a+Math.random()*(b-a);};
var r1=function(v){return Math.round(v*10)/10;},r2=function(v){return Math.round(v*100)/100;};
var pick=function(a){return a[Math.floor(Math.random()*a.length)];};
var META={};try{META=JSON.parse(localStorage.getItem('wi_meta'))||{};}catch(e){}
var UA=navigator.userAgent||'';
var isQ2=/quest 2/i.test(UA),isQ3=/quest 3/i.test(UA);
var PERF=(META.q!==undefined&&META.q!==null)?META.q===1:(isQ2||(!isQ3&&/mobile|android|oculus/i.test(UA)));
var CNT=function(n){return PERF?Math.max(2,Math.ceil(n*.5)):n;};
function save(){try{localStorage.setItem('wi_meta',JSON.stringify({last:net.code,name:net.name,q:PERF?1:0}));}catch(e){}}
var canvas=document.getElementById('gl'),renderer;
try{renderer=new THREE.WebGLRenderer({canvas:canvas,antialias:!PERF,powerPreference:'high-performance'});}
catch(e){fatal('WebGL unavailable: '+e.message);return;}
renderer.setPixelRatio(PERF?1:Math.min(window.devicePixelRatio,2));
renderer.setSize(innerWidth,innerHeight);
renderer.outputEncoding=THREE.sRGBEncoding;
renderer.toneMapping=THREE.ACESFilmicToneMapping;renderer.toneMappingExposure=1.25;
renderer.shadowMap.enabled=true;renderer.shadowMap.type=THREE.PCFSoftShadowMap;
renderer.xr.enabled=true;renderer.xr.setReferenceSpaceType('local-floor');
if(renderer.xr.setFramebufferScaleFactor)renderer.xr.setFramebufferScaleFactor(PERF?0.72:1.0);
if(renderer.xr.setFoveation)renderer.xr.setFoveation(PERF?1:0.6);
var scene=new THREE.Scene();
scene.background=new THREE.Color(0x0a0705);
scene.fog=new THREE.FogExp2(0x0a0705,0.045);
var camera=new THREE.PerspectiveCamera(70,innerWidth/innerHeight,0.05,900);
var rig=new THREE.Group();rig.add(camera);scene.add(rig);
var rigY=0,rigYT=0,poseBaseY=0,heightOff=0,heightLocked=false,heightFrames=0,slipT=0,curFloor=1,lastFloor=1;
var clock=new THREE.Clock();
var rc=new THREE.Raycaster(),tmpM=new THREE.Matrix4(),UP=new THREE.Vector3(0,1,0);
var camPos=new THREE.Vector3(),camDir=new THREE.Vector3(),v3=new THREE.Vector3();
var tp=new THREE.Vector3(),tq=new THREE.Quaternion(),tsV=new THREE.Vector3(),tv=new THREE.Vector3();
var bq=new THREE.Quaternion(),bqi=new THREE.Quaternion(),pv2=new THREE.Vector2();
var R=0.3,SPAWN={x:0.5,z:1.5};
var state='lobby',menuT=0;
/* ===== registries — ALL declared first so nothing ever pushes into undefined ===== */
var interactables=[],hitMeshes=[];
var worldObjs={},physObjs=[];
/* ===== the staircase — 24 floors down ===== */
var ST={z0:30,flight:5,landing:1.6,drop:2.75,floors:24};
ST.unit=ST.flight+ST.landing;ST.slope=ST.drop/ST.flight;ST.zEnd=ST.z0+ST.floors*ST.unit;
var LOBBY_Y=-ST.floors*ST.drop;
var LOB=ST.zEnd+0.2;
function groundAt(x,z){
if(z<ST.z0)return 0;
var zc=z-ST.z0;
if(zc>=ST.floors*ST.unit)return LOBBY_Y;
var fl=Math.floor(zc/ST.unit),rem=zc-fl*ST.unit;
if(rem<ST.flight)return -(fl*ST.drop+rem*ST.slope);
return -(fl+1)*ST.drop;}
function ctex(w,h,draw){var c=document.createElement('canvas');c.width=w;c.height=h;
draw(c.getContext('2d'),w,h);var t=new THREE.CanvasTexture(c);t.encoding=THREE.sRGBEncoding;t.anisotropy=PERF?2:8;return t;}
var floorTex=ctex(512,512,function(x){x.fillStyle='#6e4a2c';x.fillRect(0,0,512,512);
for(var r=0;r<8;r++){var y=r*64,l=20+Math.random()*10;x.fillStyle='hsl(27,34%,'+l+'%)';x.fillRect(0,y,512,64);
for(var i=0;i<24;i++){x.strokeStyle='hsla(25,40%,'+(l-8+Math.random()*4)+'%,'+(.25+Math.random()*.3)+')';
x.lineWidth=1+Math.random()*1.5;x.beginPath();var gy=y+Math.random()*64;x.moveTo(0,gy);
for(var gx=0;gx<=512;gx+=64)x.lineTo(gx,gy+Math.sin(gx*.02+r)*2+(Math.random()-.5)*3);x.stroke();}
x.fillStyle='rgba(18,9,4,.85)';x.fillRect(0,y,512,2);x.fillRect(Math.random()*512,y,2,64);}});
floorTex.wrapS=floorTex.wrapT=THREE.RepeatWrapping;floorTex.repeat.set(2.2,1.8);
var wallTex=ctex(256,256,function(x){x.fillStyle='#2c211a';x.fillRect(0,0,256,256);
for(var i=0;i<800;i++){x.fillStyle='rgba('+(Math.random()>.5?255:60)+','+(Math.random()>.5?215:40)+',170,'+(Math.random()*.035)+')';
x.fillRect(Math.random()*256,Math.random()*256,1.5,1.5);}
var g=x.createLinearGradient(0,0,0,256);g.addColorStop(0,'rgba(0,0,0,0)');g.addColorStop(1,'rgba(0,0,0,.28)');
x.fillStyle=g;x.fillRect(0,0,256,256);});
wallTex.wrapS=wallTex.wrapT=THREE.RepeatWrapping;wallTex.repeat.set(3,1.6);
var tileTex=ctex(256,256,function(x){x.fillStyle='#9fb0b8';x.fillRect(0,0,256,256);
for(var i=0;i<4;i++)for(var j=0;j<4;j++){x.fillStyle='hsl('+(195+Math.random()*14)+',14%,'+(66+Math.random()*8)+'%)';
x.fillRect(i*64+3,j*64+3,58,58);}
x.fillStyle='rgba(40,50,55,.8)';for(i=0;i<=4;i++){x.fillRect(i*64-2,0,4,256);x.fillRect(0,i*64-2,256,4);}});
tileTex.wrapS=tileTex.wrapT=THREE.RepeatWrapping;tileTex.repeat.set(2,2);
var plaidTex=ctex(256,256,function(x){x.fillStyle='#b0763a';x.fillRect(0,0,256,256);
x.fillStyle='rgba(90,50,22,.85)';for(var i=0;i<4;i++){x.fillRect(0,i*64+20,256,22);x.fillRect(i*64+20,0,22,256);}
x.fillStyle='rgba(240,220,180,.5)';for(i=0;i<4;i++){x.fillRect(0,i*64+52,256,4);x.fillRect(i*64+52,0,4,256);}});
plaidTex.wrapS=plaidTex.wrapT=THREE.RepeatWrapping;plaidTex.repeat.set(2,2);
var rugTex=ctex(512,512,function(x){var cols=['#5d4732','#3c2d21','#6b5138','#33261c'];
for(var i=10;i>0;i--){x.fillStyle=cols[i%cols.length];var m=(10-i)*22;x.fillRect(m,m,512-2*m,512-2*m);}
x.strokeStyle='#c8a877';x.lineWidth=6;x.setLineDash([18,12]);x.strokeRect(40,40,432,432);x.setLineDash([]);
x.fillStyle='#c8a877';x.save();x.translate(256,256);x.rotate(Math.PI/4);x.fillRect(-40,-40,80,80);x.restore();});
var skyTex=ctex(1024,512,function(x){var g=x.createLinearGradient(0,0,0,512);
g.addColorStop(0,'#04060d');g.addColorStop(.55,'#0a1220');g.addColorStop(1,'#141d33');
x.fillStyle=g;x.fillRect(0,0,1024,512);
for(var i=0;i<240;i++){x.fillStyle='rgba(220,230,255,'+(Math.random()*.8+.1)+')';
x.fillRect(Math.random()*1024,Math.random()*330,Math.random()<.9?1:2,1);}
x.fillStyle='#e9edf7';x.beginPath();x.arc(700,130,34,0,7);x.fill();});
var carpetTex=ctex(256,256,function(x){x.fillStyle='#4a2226';x.fillRect(0,0,256,256);
for(var i=0;i<400;i++){x.fillStyle='rgba('+(Math.random()>.5?120:30)+','+(Math.random()>.5?50:15)+','+(Math.random()>.5?55:20)+','+(Math.random()*.25)+')';
x.fillRect(Math.random()*256,Math.random()*256,2,2);}
x.strokeStyle='rgba(200,150,90,.14)';x.lineWidth=3;
for(i=0;i<4;i++){x.strokeRect(i*32+16,i*32+16,256-i*64,256-i*64);}});
carpetTex.wrapS=carpetTex.wrapT=THREE.RepeatWrapping;carpetTex.repeat.set(2,20);
var paperTex=ctex(256,256,function(x){x.fillStyle='#5a5344';x.fillRect(0,0,256,256);
x.fillStyle='rgba(90,100,70,.35)';for(var i=0;i<8;i++)x.fillRect(i*32,0,14,256);
for(i=0;i<300;i++){x.fillStyle='rgba(20,15,10,'+(Math.random()*.2)+')';
x.fillRect(Math.random()*256,Math.random()*256,Math.random()*8,Math.random()*3);}});
paperTex.wrapS=paperTex.wrapT=THREE.RepeatWrapping;paperTex.repeat.set(4,14);
var stepTex=ctex(128,256,function(x){x.fillStyle='#3a342e';x.fillRect(0,0,128,256);
for(var i=0;i<8;i++){x.fillStyle=i%2?'#453d35':'#37312b';x.fillRect(0,i*32,128,32);
x.fillStyle='rgba(255,220,170,.12)';x.fillRect(0,i*32,128,3);}});
stepTex.wrapS=stepTex.wrapT=THREE.RepeatWrapping;stepTex.repeat.set(1,2);
var lobbyFloorTex=ctex(256,256,function(x){x.fillStyle='#7a5a38';x.fillRect(0,0,256,256);
for(var i=0;i<4;i++)for(var j=0;j<4;j++){x.fillStyle=(i+j)%2?'#8a6a44':'#6a4c30';x.fillRect(i*64,j*64,64,64);}
x.strokeStyle='rgba(30,18,8,.5)';for(i=0;i<=4;i++){x.lineWidth=2;x.fillRect(i*64-1,0,2,256);x.fillRect(0,i*64-1,256,2);}});
lobbyFloorTex.wrapS=lobbyFloorTex.wrapT=THREE.RepeatWrapping;lobbyFloorTex.repeat.set(3,4);
var kFloorTex=ctex(256,256,function(x){x.fillStyle='#8a6a4a';x.fillRect(0,0,256,256);
for(var i=0;i<4;i++)for(var j=0;j<4;j++){x.fillStyle=(i+j)%2?'#9a7a56':'#7a5c40';x.fillRect(i*64+2,j*64+2,60,60);}});
kFloorTex.wrapS=kFloorTex.wrapT=THREE.RepeatWrapping;kFloorTex.repeat.set(3,2);
var posterATex=ctex(256,340,function(x){var g=x.createLinearGradient(0,0,0,340);
g.addColorStop(0,'#f2b159');g.addColorStop(.5,'#c96f35');g.addColorStop(1,'#4a2318');
x.fillStyle=g;x.fillRect(0,0,256,340);
x.fillStyle='#ffe9b8';x.beginPath();x.arc(128,150,52,0,7);x.fill();
x.fillStyle='#3a1c12';x.fillRect(0,230,256,110);
x.fillStyle='#ffe9b8';x.font='italic 44px Georgia';x.textAlign='center';
x.fillText('stay',128,287);x.fillText('warm',128,327);});
var posterBTex=ctex(256,340,function(x){x.fillStyle='#0d1322';x.fillRect(0,0,256,340);
for(var i=0;i<60;i++){x.fillStyle='rgba(220,230,255,'+Math.random()*.8+')';x.fillRect(Math.random()*256,Math.random()*200,1.5,1.5);}
x.fillStyle='#e9edf7';x.beginPath();x.arc(190,70,22,0,7);x.fill();
x.fillStyle='#1a2438';x.beginPath();x.moveTo(0,340);x.lineTo(90,150);x.lineTo(180,340);x.fill();
x.fillStyle='#131b2d';x.beginPath();x.moveTo(90,340);x.lineTo(200,120);x.lineTo(256,230);x.lineTo(256,340);x.fill();});
var pizzaPosterTex=ctex(256,340,function(x){var g=x.createLinearGradient(0,0,0,340);
g.addColorStop(0,'#c03030');g.addColorStop(1,'#701818');
x.fillStyle=g;x.fillRect(0,0,256,340);
x.fillStyle='#f2c14e';x.beginPath();x.arc(128,150,62,0,7);x.fill();
x.fillStyle='#e8a030';x.beginPath();x.arc(128,150,54,0,7);x.fill();
x.fillStyle='#c03030';
[[110,130],[148,140],[128,172],[105,160],[150,168]].forEach(function(p){x.beginPath();x.arc(p[0],p[1],8,0,7);x.fill();});
x.fillStyle='#fff3d0';x.font='700 40px "Space Grotesk"';x.textAlign='center';
x.fillText("TONY'S",128,52);
x.font='700 30px "Space Grotesk"';x.fillText('VOID PIZZA',128,252);
x.fillStyle='#1c0f05';x.fillRect(28,272,200,44);
x.fillStyle='#ffb45e';x.font='700 26px "Space Grotesk",monospace';x.fillText('CALL 555-7499',128,302);
x.fillStyle='#ffd9a8';x.font='italic 15px Georgia';x.fillText('open all night · left at the door',128,332);});
var glowTex=ctex(128,128,function(x){var g=x.createRadialGradient(64,64,2,64,64,62);
g.addColorStop(0,'rgba(255,255,255,1)');g.addColorStop(.35,'rgba(255,255,255,.45)');g.addColorStop(1,'rgba(255,255,255,0)');
x.fillStyle=g;x.fillRect(0,0,128,128);});
var streakTex=ctex(256,256,function(x){for(var i=0;i<42;i++){var sx=Math.random()*256,sy=Math.random()*256;
x.strokeStyle='rgba(200,220,255,'+(.05+Math.random()*.12)+')';x.lineWidth=1+Math.random()*2;
x.beginPath();x.moveTo(sx,sy);x.lineTo(sx+(Math.random()-.5)*6,sy+40+Math.random()*120);x.stroke();}});
streakTex.wrapS=streakTex.wrapT=THREE.RepeatWrapping;streakTex.repeat.set(2,1);
var zzzTex=ctex(64,64,function(x){x.fillStyle='rgba(255,230,190,.95)';x.font='italic 46px Georgia';
x.textAlign='center';x.textBaseline='middle';x.fillText('z',32,34);});
var ballTex=ctex(256,128,function(x){var cols=['#ff6a6a','#f2f2f2','#6ad1ff','#f2f2f2','#ffd166','#f2f2f2'];
for(var i=0;i<6;i++){x.fillStyle=cols[i];x.fillRect(i*42.7,0,43,128);}});
function floorSignTex(n){return ctex(256,128,function(x){
x.fillStyle='#161009';x.fillRect(0,0,256,128);
x.strokeStyle='rgba(255,180,94,.55)';x.lineWidth=4;x.strokeRect(6,6,244,116);
x.fillStyle='#cdb98f';x.font='600 30px "Space Grotesk"';x.textAlign='center';x.textBaseline='middle';
x.fillText('F L O O R',128,38);
x.font='700 58px "Space Grotesk"';x.fillStyle='#ffb45e';x.fillText(String(n),128,88);});}
var M={floor:new THREE.MeshStandardMaterial({map:floorTex,roughness:.75,metalness:.05}),
wall:new THREE.MeshStandardMaterial({map:wallTex,roughness:.95}),
tile:new THREE.MeshStandardMaterial({map:tileTex,roughness:.4,metalness:.1}),
ceil:new THREE.MeshStandardMaterial({color:0x241a12,roughness:1}),
darkwood:new THREE.MeshStandardMaterial({color:0x3a281c,roughness:.7}),
wood2:new THREE.MeshStandardMaterial({color:0x5a3d24,roughness:.65}),
cream:new THREE.MeshStandardMaterial({color:0xe6d8c2,roughness:.9}),
pillow:new THREE.MeshStandardMaterial({color:0xefe4cf,roughness:1}),
blanket:new THREE.MeshStandardMaterial({map:plaidTex,roughness:.95}),
metal:new THREE.MeshStandardMaterial({color:0x2a211b,roughness:.5,metalness:.6}),
porcelain:new THREE.MeshStandardMaterial({color:0xd8d4cc,roughness:.25,metalness:.05}),
fabric:new THREE.MeshStandardMaterial({color:0x7a3b28,roughness:1,side:THREE.DoubleSide}),
carpet:new THREE.MeshStandardMaterial({map:carpetTex,roughness:1}),
paper:new THREE.MeshStandardMaterial({map:paperTex,roughness:.95}),
step:new THREE.MeshStandardMaterial({map:stepTex,roughness:.9}),
kfloor:new THREE.MeshStandardMaterial({map:kFloorTex,roughness:.6})};
var SHARED_MATS={};for(var smk in M)SHARED_MATS[M[smk].id]=true;
function box(w,h,d,mat,x,y,z,cast,rec){var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),mat);
m.position.set(x,y,z);m.castShadow=cast!==false;m.receiveShadow=rec!==false;scene.add(m);return m;}
function cyl(rt,rb,h,mat,x,y,z,seg,open){var m=new THREE.Mesh(new THREE.CylinderGeometry(rt,rb,h,seg||(PERF?8:16),1,!!open),mat);
m.position.set(x,y,z);m.castShadow=true;m.receiveShadow=true;scene.add(m);return m;}
function glow(color,scale,x,y,z,op){var s=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:color,transparent:true,
opacity:(op===undefined)?0.85:op,blending:THREE.AdditiveBlending,depthWrite:false,fog:false}));
s.scale.set(scale,scale,1);s.position.set(x,y,z);scene.add(s);return s;}
function glowChild(parent,color,scale,y,op){var s=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:color,transparent:true,
opacity:(op===undefined)?0.85:op,blending:THREE.AdditiveBlending,depthWrite:false,fog:false}));
s.scale.set(scale,scale,1);s.position.y=y;parent.add(s);return s;}
function proxy(w,h,d,x,y,z,parent){var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),new THREE.MeshBasicMaterial({visible:false}));
m.position.set(x,y,z);(parent||scene).add(m);return m;}
function proxyS(r,x,y,z,parent){var m=new THREE.Mesh(new THREE.SphereGeometry(r,8,8),new THREE.MeshBasicMaterial({visible:false}));
m.position.set(x,y,z);(parent||scene).add(m);return m;}
function addI(hit,label,run){var ix={hit:hit,label:label,run:run};
hit.userData.ix=interactables.length;interactables.push(ix);hitMeshes.push(hit);return ix;}
/* ===== the room ===== */
box(6.5,.12,5.3,M.floor,0,-.06,0,false,true);
box(6.5,.12,5.3,M.ceil,0,3.06,0,false,true);
box(6.5,3,.12,M.wall,0,1.5,-2.66);
box(.12,3,3.46,M.wall,3.26,1.5,-.93);
box(.12,3,.46,M.wall,3.26,1.5,2.43);
box(.12,.8,1.4,M.wall,3.26,2.6,1.5);
box(.07,2.2,.14,M.darkwood,3.2,1.1,.8);box(.07,2.2,.14,M.darkwood,3.2,1.1,2.2);box(.14,.07,1.4,M.darkwood,3.2,2.22,1.5);
box(.12,3,1.35,M.wall,-3.26,1.5,-1.925);
box(.12,.95,1.7,M.wall,-3.26,.475,-.4);
box(.12,.55,1.7,M.wall,-3.26,2.725,-.4);
/* left wall — carved for the kitchen doorway (z 1.3..2.1) */
box(.12,3,.85,M.wall,-3.26,1.5,.875);
box(.12,3,.5,M.wall,-3.26,1.5,2.35);
box(.12,.9,.8,M.wall,-3.26,2.55,1.7);
box(.07,2.1,.14,M.darkwood,-3.2,1.05,1.3);box(.07,2.1,.14,M.darkwood,-3.2,1.05,2.1);box(.14,.07,.8,M.darkwood,-3.2,2.13,1.7);
box(1.32,3,.12,M.wall,-2.59,1.5,2.66);
box(4.32,3,.12,M.wall,1.09,1.5,2.66);
box(.86,.85,.12,M.wall,-1.5,2.575,2.66);
box(.07,2.15,.14,M.darkwood,-1.93,1.075,2.6);box(.07,2.15,.14,M.darkwood,-1.07,1.075,2.6);box(.93,.07,.14,M.darkwood,-1.5,2.17,2.6);
box(6.4,.1,.025,M.darkwood,0,.05,-2.59,false,false);
box(.025,.1,5.2,M.darkwood,3.19,.05,0,false,false);
box(.025,.1,5.2,M.darkwood,-3.19,.05,0,false,false);
box(6.4,.16,.18,M.darkwood,0,2.92,-.9);box(6.4,.16,.18,M.darkwood,0,2.92,1.3);
/* ===== bathroom — the big wet sanctuary ===== */
var chrome=new THREE.MeshStandardMaterial({color:0xd8dde6,metalness:1,roughness:.12});
var bathWood=new THREE.MeshStandardMaterial({color:0x4a3524,roughness:.7});
var bathTileTex=tileTex.clone();bathTileTex.needsUpdate=true;bathTileTex.repeat.set(3.4,3);
var Mtile2=new THREE.MeshStandardMaterial({map:bathTileTex,roughness:.35,metalness:.1});
box(3.9,.12,3.5,Mtile2,5.15,-.06,1.05,false,true);
box(3.9,.12,3.5,M.ceil,5.15,3.06,1.05,false,true);
box(3.9,3,.14,M.tile,5.15,1.5,-.67);
box(3.9,3,.14,M.tile,5.15,1.5,2.77);
box(.14,3,3.58,M.tile,7.07,1.5,1.05);
var tubG=new THREE.Group();tubG.position.set(6.35,0,.7);scene.add(tubG);
var tubBody=new THREE.Mesh(new THREE.BoxGeometry(.82,.55,1.85),M.porcelain);
tubBody.position.y=.3;tubBody.castShadow=true;tubG.add(tubBody);
var tubRim=new THREE.Mesh(new THREE.TorusGeometry(.5,.06,8,PERF?12:22),M.porcelain);
tubRim.rotation.x=Math.PI/2;tubRim.scale.set(.86,1.9,1);tubRim.position.y=.575;tubG.add(tubRim);
var tubWaterBox=new THREE.Mesh(new THREE.BoxGeometry(.7,.4,1.7),
new THREE.MeshStandardMaterial({color:0x6fa8c8,roughness:.05,metalness:.3,transparent:true,opacity:0}));
tubWaterBox.position.y=.13;tubWaterBox.scale.y=.04;tubG.add(tubWaterBox);
[[-.32,-.8],[.32,-.8],[-.32,.8],[.32,.8]].forEach(function(fp){
var f=new THREE.Mesh(new THREE.SphereGeometry(.05,8,8),chrome);f.position.set(fp[0],.05,fp[1]);tubG.add(f);});
cyl(.022,.022,.75,chrome,5.98,.375,-.35);
box(.03,.03,.3,chrome,5.98,.76,-.22);
var bathFill=.35,bathFilling=false,bathDraining=false;
var rimDuck=new THREE.Group();rimDuck.position.set(6.1,.63,1.35);scene.add(rimDuck);
var rdMat=new THREE.MeshStandardMaterial({color:0xf2c94c,roughness:.5});
var rdb=new THREE.Mesh(new THREE.SphereGeometry(.05,8,8),rdMat);rdb.scale.set(1,.8,1.2);rimDuck.add(rdb);
var rdh=new THREE.Mesh(new THREE.SphereGeometry(.032,8,8),rdMat);rdh.position.set(0,.05,.04);rimDuck.add(rdh);
var rimDuckWob=0;
var bombUsed=false;
var bomb=new THREE.Mesh(new THREE.SphereGeometry(.035,8,8),new THREE.MeshStandardMaterial({color:0x8a5ad8,roughness:.6}));
bomb.position.set(6.05,.64,.05);scene.add(bomb);
var shGlassMat=new THREE.MeshPhongMaterial({color:0xaac8d8,transparent:true,opacity:.1,shininess:120,depthWrite:false,side:THREE.DoubleSide});
var sg1=new THREE.Mesh(new THREE.PlaneGeometry(.72,2.2),shGlassMat);
sg1.rotation.y=Math.PI/2;sg1.position.set(6.14,1.1,-.25);scene.add(sg1);
var sg2=new THREE.Mesh(new THREE.PlaneGeometry(.5,2.2),shGlassMat);
sg2.position.set(6.75,1.1,.11);scene.add(sg2);
box(.03,2.2,.03,chrome,6.14,1.1,.1);
cyl(.12,.12,.03,chrome,6.6,2.32,-.2);
var SHN=PERF?40:70,shPos=new Float32Array(SHN*3);
for(var shi=0;shi<SHN;shi++){shPos[shi*3]=6.6+rand(-.28,.28);shPos[shi*3+1]=rand(.2,2.25);shPos[shi*3+2]=-.2+rand(-.28,.28);}
var shGeo=new THREE.BufferGeometry();shGeo.setAttribute('position',new THREE.BufferAttribute(shPos,3));
var showerPts=new THREE.Points(shGeo,new THREE.PointsMaterial({color:0x9fc8e8,size:.02,transparent:true,opacity:0,depthWrite:false}));
scene.add(showerPts);
var showerSteam=glow(0xbfd8e8,1.2,6.6,1.9,-.2,0);
var bathSteam1=glow(0xcfe0e8,.7,6.35,1.0,.4,0),bathSteam2=glow(0xcfe0e8,.7,6.35,1.0,1.0,0);
box(.8,.85,.5,bathWood,3.95,.425,-.35);
box(.86,.05,.56,new THREE.MeshStandardMaterial({color:0xd8d4cc,roughness:.3}),3.95,.9,-.35);
var basin=new THREE.Mesh(new THREE.CylinderGeometry(.17,.13,.09,16),M.porcelain);
basin.position.set(3.95,.95,-.33);basin.castShadow=true;scene.add(basin);
cyl(.018,.018,.22,chrome,3.95,1.06,-.52);
box(.03,.03,.16,chrome,3.95,1.17,-.45);
box(.8,1.02,.05,bathWood,3.95,1.85,-.63);
var mirror=new THREE.Mesh(new THREE.PlaneGeometry(.72,.94),
new THREE.MeshStandardMaterial({color:0x9fb4c4,metalness:1,roughness:.05}));
mirror.position.set(3.95,1.85,-.6);scene.add(mirror);
var fogCv=document.createElement('canvas');fogCv.width=256;fogCv.height=320;
var fogCtx=fogCv.getContext('2d');
var fogTex=new THREE.CanvasTexture(fogCv);fogTex.encoding=THREE.sRGBEncoding;
var fogPlane=new THREE.Mesh(new THREE.PlaneGeometry(.72,.94),
new THREE.MeshBasicMaterial({map:fogTex,transparent:true,depthWrite:false}));
fogPlane.position.set(3.95,1.85,-.595);scene.add(fogPlane);
var mirrorFog=true,fogBackT=0;
function drawFog(){var x=fogCtx;x.clearRect(0,0,256,320);
if(mirrorFog){x.fillStyle='rgba(220,235,240,.85)';x.fillRect(0,0,256,320);
for(var i=0;i<40;i++){x.fillStyle='rgba(255,255,255,'+(Math.random()*.25)+')';
x.beginPath();x.arc(Math.random()*256,Math.random()*320,10+Math.random()*30,0,7);x.fill();}}
else{x.fillStyle='rgba(220,235,240,.1)';x.fillRect(0,0,256,320);
x.fillStyle='rgba(240,250,255,.9)';x.font='italic 30px Georgia';x.textAlign='center';
x.fillText('it\u2019s you.',128,150);x.fillText('it was always you.',128,192);}
fogTex.needsUpdate=true;}
drawFog();
box(.5,.03,.03,chrome,4.9,1.35,-.6,false,false);
box(.22,.5,.02,new THREE.MeshStandardMaterial({color:0xe8dcc8,roughness:1}),4.78,1.08,-.58);
box(.22,.5,.02,new THREE.MeshStandardMaterial({color:0x7a9a8a,roughness:1}),5.02,1.08,-.58);
box(.42,.4,.6,M.porcelain,3.8,.2,2.42);
box(.4,.5,.2,M.porcelain,3.8,.55,2.66);
box(.44,.05,.5,M.porcelain,3.8,.44,2.38);
var washG=new THREE.Group();washG.position.set(6.5,0,2.35);scene.add(washG);
var wbox=new THREE.Mesh(new THREE.BoxGeometry(.62,.85,.6),new THREE.MeshStandardMaterial({color:0xe8e4dc,roughness:.4}));
wbox.position.y=.425;wbox.castShadow=true;washG.add(wbox);
var wdoor=new THREE.Mesh(new THREE.TorusGeometry(.17,.03,8,20),chrome);
wdoor.position.set(0,.42,-.31);washG.add(wdoor);
var wglass=new THREE.Mesh(new THREE.CircleGeometry(.15,16),
new THREE.MeshStandardMaterial({color:0x2a3a44,metalness:.5,roughness:.1,transparent:true,opacity:.55}));
wglass.position.set(0,.42,-.3);wglass.rotation.y=Math.PI;washG.add(wglass);
var drumG=new THREE.Group();drumG.position.set(0,.42,-.27);washG.add(drumG);
for(var wdi=0;wdi<3;wdi++){var piv=new THREE.Group();piv.rotation.z=wdi*(Math.PI*2/3);
var pad=new THREE.Mesh(new THREE.BoxGeometry(.02,.12,.02),M.metal);pad.position.y=.08;piv.add(pad);drumG.add(piv);}
var washT=0,washPaid=false,washSlosh=0;
box(.05,.04,1.2,bathWood,6.95,1.5,.9);
box(.05,.04,1.2,bathWood,6.95,1.95,.9);
var candleOn=false;
var candleFlame=new THREE.Mesh(new THREE.ConeGeometry(.008,.028,6),new THREE.MeshBasicMaterial({color:0xffc86a}));
candleFlame.material.toneMapped=false;candleFlame.position.set(6.93,1.63,.35);candleFlame.visible=false;scene.add(candleFlame);
var candleGlowSpr=glow(0xffa050,.16,6.93,1.63,.35,0);
var candleLight=new THREE.PointLight(0xffa050,0,1.6,2);candleLight.position.set(6.9,1.65,.35);scene.add(candleLight);
box(.36,.05,.36,new THREE.MeshStandardMaterial({color:0xcfd4d8,roughness:.3}),3.6,.025,.15);
box(.6,.02,.9,new THREE.MeshStandardMaterial({color:0x7a9a8a,roughness:1}),5.75,.01,.7,false,false);
cyl(.14,.14,.04,chrome,5.1,2.98,1.05);
var bathLight=new THREE.PointLight(0xffd0a0,0,5.5,2);bathLight.position.set(5.1,2.6,1.05);scene.add(bathLight);
var bathBulbMat=new THREE.MeshBasicMaterial({color:0x33241a,fog:false});bathBulbMat.toneMapped=false;
var bathBulb=new THREE.Mesh(new THREE.CylinderGeometry(.12,.12,.02,12),bathBulbMat);
bathBulb.position.set(5.1,2.95,1.05);scene.add(bathBulb);
var bathOn=false;
var showerOn=false;
/* ===== kitchen — the warm heart ===== */
box(3.8,.12,2.36,M.kfloor,-5.1,-.06,1.48,false,true);
box(3.8,.12,2.36,M.ceil,-5.1,3.06,1.48,false,true);
box(3.8,3,.14,M.wall,-5.1,1.5,.24);
box(3.8,3,.14,M.wall,-5.1,1.5,2.72);
box(.14,3,2.5,M.wall,-7.06,1.5,1.48);
box(2.2,.9,.55,new THREE.MeshStandardMaterial({color:0x6a5240,roughness:.5}),-5.9,.45,.62);
box(2.2,.04,.55,new THREE.MeshStandardMaterial({color:0x9a8a76,roughness:.3,metalness:.2}),-5.9,.92,.62);
box(.7,.05,.5,new THREE.MeshStandardMaterial({color:0x1a1a1e,roughness:.4,metalness:.5}),-6.25,.965,.62);
var burnerMat=new THREE.MeshStandardMaterial({color:0x2a2a30,emissive:0xff4400,emissiveIntensity:0,roughness:.6});
var burner=new THREE.Mesh(new THREE.CylinderGeometry(.11,.11,.02,16),burnerMat);
burner.position.set(-6.25,.99,.62);scene.add(burner);
var burnerOn=false,cookT=0,potSteam=null;
var stoveLight=new THREE.PointLight(0xff6a2a,0,2.5,2);stoveLight.position.set(-6.25,1.2,.62);scene.add(stoveLight);
var potG=new THREE.Group();potG.position.set(-6.25,1.0,.62);scene.add(potG);
var potBody=new THREE.Mesh(new THREE.CylinderGeometry(.12,.1,.1,16,1,true),
new THREE.MeshStandardMaterial({color:0x8a8a92,metalness:.7,roughness:.3,side:THREE.DoubleSide}));
potBody.position.y=.05;potG.add(potBody);
var potBottom=new THREE.Mesh(new THREE.CylinderGeometry(.1,.1,.01,16),
new THREE.MeshStandardMaterial({color:0x8a8a92,metalness:.7,roughness:.3}));
potG.add(potBottom);
var potLiquid=new THREE.Mesh(new THREE.CylinderGeometry(.105,.105,.01,16),
new THREE.MeshStandardMaterial({color:0xb05a2a,roughness:.4,transparent:true,opacity:0}));
potLiquid.position.y=.08;potG.add(potLiquid);
potSteam=glow(0xf5e8d8,.4,-6.25,1.25,.62,0);
box(.62,1.8,.62,new THREE.MeshStandardMaterial({color:0xd8d8dc,roughness:.3,metalness:.3}),-6.65,.9,2.32);
box(.03,.5,.03,chrome,-6.36,1.1,2.02,false,false);
var fridgeCd=0;
box(.5,.06,.4,new THREE.MeshStandardMaterial({color:0x9a9aa2,metalness:.6,roughness:.3}),-5.3,.95,.62);
cyl(.015,.015,.25,chrome,-5.3,1.1,.45);
box(.02,.02,.15,chrome,-5.3,1.22,.52,false,false);
box(.9,.05,.7,M.wood2,-4.5,.75,1.7);
[[-4.85,1.4],[-4.15,1.4],[-4.85,2.0],[-4.15,2.0]].forEach(function(c){
cyl(.03,.03,.72,M.wood2,c[0],.36,c[1]);});
cyl(.16,.16,.05,M.wood2,-4.9,.5,1.0);
cyl(.16,.16,.05,M.wood2,-4.1,.5,1.0);
box(.05,.04,1.6,bathWood,-6.98,1.6,1.4);
var jarCols=[0xc03030,0x3a8a4a,0xd8a030,0x7a4a8a];
for(var ji=0;ji<4;ji++){cyl(.045,.045,.12,new THREE.MeshStandardMaterial({color:jarCols[ji],transparent:true,opacity:.7,roughness:.2}),-6.95,1.68,.8+ji*.4,8);}
var kitchenLight=new THREE.PointLight(0xffc890,1.5,6,2);kitchenLight.position.set(-5.1,2.6,1.5);scene.add(kitchenLight);
var kitchenGlowSpr=glow(0xffc890,.5,-5.1,2.75,1.5,.6);
/* ===== cooking system ===== */
var ING={
tomato:{c:0xd03030,n:'tomato'},cheese:{c:0xf2c14e,n:'cheese'},bread:{c:0xb0783a,n:'bread'},
egg:{c:0xf2ede0,n:'egg'},mushroom:{c:0x8a6a4a,n:'mushroom'},lettuce:{c:0x5aa04a,n:'lettuce'},
onion:{c:0xb0709a,n:'onion'},carrot:{c:0xe8862c,n:'carrot'}};
var ING_KEYS=Object.keys(ING);
var ING_FLAVOR={tomato:'juicy. a little sour.',cheese:'sharp. perfect.',bread:'still warm somehow.',
egg:'raw? cooked? yes.',mushroom:'earthy. mysterious.',lettuce:'crunchy. barely anything.',
onion:'your eyes water. worth it.',carrot:'sweet. satisfying.'};
var RECIPES={
'bread+cheese+tomato':'void pizza','cheese+egg':'fluffy omelette','mushroom+onion+tomato':'hearty soup',
'bread+lettuce+tomato':'crunchy sandwich','carrot+mushroom+onion':'slow stew','egg+tomato':'shakshuka',
'carrot+tomato':'garden soup','bread+cheese':'grilled cheese','egg+mushroom':'forest scramble',
'cheese+lettuce':'weird salad','bread+egg':'breakfast toast','carrot+lettuce':'rabbit food'};
var DISH_COL={'void pizza':0xe8a030,'fluffy omelette':0xf2d06a,'hearty soup':0xb05a2a,
'crunchy sandwich':0x8aa04a,'slow stew':0x7a4a2a,'shakshuka':0xd05030,'garden soup':0xc07030,
'grilled cheese':0xd8a050,'forest scramble':0x9a7a4a,'weird salad':0x6aa05a,'rabbit food':0x8ac05a,'mystery stew':0x6a5a4a};
var potContents=[],potState='empty',potDish='';
function spawnIngredient(kind,x,y,z){
var d=ING[kind],g=new THREE.Group(),mm=new THREE.MeshStandardMaterial({color:d.c,roughness:.6});
if(kind==='tomato'||kind==='onion'){var s=new THREE.Mesh(new THREE.SphereGeometry(.05,10,8),mm);s.scale.y=.85;g.add(s);}
else if(kind==='egg'){var e=new THREE.Mesh(new THREE.SphereGeometry(.035,10,8),mm);e.scale.y=1.25;g.add(e);}
else if(kind==='cheese'){g.add(new THREE.Mesh(new THREE.BoxGeometry(.09,.05,.07),mm));}
else if(kind==='bread'){g.add(new THREE.Mesh(new THREE.BoxGeometry(.11,.06,.08),mm));}
else if(kind==='lettuce'){var l=new THREE.Mesh(new THREE.SphereGeometry(.06,10,8),mm);l.scale.y=.35;g.add(l);}
else if(kind==='carrot'){var c=new THREE.Mesh(new THREE.ConeGeometry(.03,.12,8),mm);c.rotation.z=Math.PI/2;g.add(c);}
else if(kind==='mushroom'){
var cap=new THREE.Mesh(new THREE.SphereGeometry(.045,10,8,0,Math.PI*2,0,Math.PI/2),mm);cap.position.y=.03;g.add(cap);
var stem=new THREE.Mesh(new THREE.CylinderGeometry(.015,.02,.04,8),new THREE.MeshStandardMaterial({color:0xe8dcc8,roughness:.7}));stem.position.y=.01;g.add(stem);}
g.position.set(x,y,z);scene.add(g);
var o=addPhys(g,.05,.2);o.type='ingredient';o.ingKind=kind;
var ix=addI(g,function(){return o.heldPeer?o.heldPeer+' has it':'grab the '+d.n+' (trigger = eat)';},null);
ix.grab=true;ix.obj=o;o.ix=ix;
return o;}
function spawnDish(name,x,y,z){
var g=new THREE.Group();
var plate=new THREE.Mesh(new THREE.CylinderGeometry(.1,.08,.02,16),
new THREE.MeshStandardMaterial({color:0xf2ede0,roughness:.3}));g.add(plate);
var dome=new THREE.Mesh(new THREE.SphereGeometry(.07,12,8,0,Math.PI*2,0,Math.PI/2),
new THREE.MeshStandardMaterial({color:DISH_COL[name]||0x8a7a5a,roughness:.5}));
dome.position.y=.015;g.add(dome);
glowChild(g,0xffc890,.25,.06,.4);
g.position.set(x,y,z);scene.add(g);
var o=addPhys(g,.09,.2);o.type='dish';o.dishName=name;o.bites=0;
var ix=addI(g,function(){return o.heldPeer?o.heldPeer+' has it':'grab the '+name+' (trigger = eat)';},null);
ix.grab=true;ix.obj=o;o.ix=ix;
return o;}
function matchRecipe(contents){
var s=contents.slice().sort().join('+');
return RECIPES[s]||'mystery stew';}
function eatIngredient(o){
if(!o||o.dead)return;
biteS();killObj(o,false);
toast(pick([ING_FLAVOR[o.ingKind]||'edible. technically.','you eat the '+ING[o.ingKind].n+'. no regrets.']));
netPublishEvent({k:'req',a:'earn',a2:1});}
function biteDish(o){
if(!o||o.dead)return;
o.bites++;biteS();o.m.scale.multiplyScalar(.7);
if(o.bites>=3){killObj(o,false);
toast(pick(['the '+o.dishName+' is gone. you are warm inside.','delicious. the kitchen approves.','+3 \u25C8 well earned.']));
netPublishEvent({k:'req',a:'earn',a2:3});}
else{var left=3-o.bites;toast(left+' bite'+(left>1?'s':'')+' of '+o.dishName+' left.')}}
addI(proxyS(.16,-6.25,1.12,.62),function(){
if(potState==='done')return 'serve the '+potDish;
if(potContents.length>0)return 'the pot: '+potContents.length+' ingredient'+(potContents.length>1?'s':'')+' (cook it!)';
return 'the pot (add ingredients)';},function(){
var held=null;
[ctl0,ctl1,hand0,hand1].forEach(function(s){if(s.userData.grabO&&s.userData.grabO.type==='ingredient')held=s.userData.grabO;});
if(potState==='done'){
spawnDish(potDish,-5.7,1.0,.62);
toast('you ladle out the '+potDish+'. it steams beautifully.');
chime();potContents=[];potState='empty';potDish='';potLiquid.material.opacity=0;burnerOn=false;cookT=0;
return;}
if(held){
if(potContents.length>=3){toast('the pot is full. cook it or serve it.');return;}
potContents.push(held.ingKind);
toast('you drop the '+ING[held.ingKind].n+' into the pot. ('+potContents.length+'/3)');
killObj(held,false);thunk();
potState='raw';potLiquid.material.opacity=.85;potLiquid.material.color.set(0xb08050);
return;}
if(potContents.length>0){toast('turn on the burner to cook. ('+potContents.length+' ingredient'+(potContents.length>1?'s':'')+')');return;}
toast('the pot is empty. grab an ingredient and trigger here.');});
addI(proxyS(.14,-5.9,1.05,.62),function(){return burnerOn?'turn the burner off':'turn the burner on';},function(){
if(burnerOn){burnerOn=false;cookT=0;toast('the burner clicks off.');return;}
if(potState==='done'){toast('serve the dish first.');return;}
if(potContents.length<2){toast('the pot needs at least 2 ingredients.');return;}
burnerOn=true;cookT=0;clickSound();
toast('the burner roars to life. the pot begins to sing.');});
addI(proxyS(.4,-6.65,1.1,2.0),function(){return fridgeCd>0?'the fridge is thinking\u2026':'open the fridge';},function(){
if(fridgeCd>0)return;
fridgeCd=4;popS();
var kind=pick(ING_KEYS);
spawnIngredient(kind,-6.2,.05,1.9);
toast('the fridge offers you a '+ING[kind].n+'. it hums, pleased with itself.');});
/* seed the counter with ingredients */
spawnIngredient('tomato',-5.6,.97,.62);
spawnIngredient('cheese',-5.45,.97,.62);
spawnIngredient('bread',-5.3,.97,.85);
spawnIngredient('egg',-5.75,.97,.85);
spawnIngredient('mushroom',-4.7,.8,1.5);
spawnIngredient('lettuce',-4.3,.8,1.9);
/* ===== door ===== */
box(.9,.12,2.4,M.carpet,-1.5,-.06,3.8,false,true);
box(.12,3,2.4,M.paper,-1.99,1.5,3.8);box(.12,3,2.4,M.paper,-1.01,1.5,3.8);
box(.9,.12,2.4,M.ceil,-1.5,3.06,3.8,false,true);
var doorPivot=new THREE.Group();doorPivot.position.set(-1.91,0,2.57);scene.add(doorPivot);
var doorPanel=new THREE.Mesh(new THREE.BoxGeometry(.82,2.1,.05),new THREE.MeshStandardMaterial({color:0x41301f,roughness:.7}));
doorPanel.position.set(.41,1.05,0);doorPanel.castShadow=true;doorPivot.add(doorPanel);
var knob2=new THREE.Mesh(new THREE.SphereGeometry(.035,10,10),M.metal);knob2.position.set(.74,1.0,.05);doorPivot.add(knob2);
var knob3=knob2.clone();knob3.position.z=-.05;doorPivot.add(knob3);
var doorK=0;
/* ===== the apartment hallway ===== */
var hallLightA=new THREE.PointLight(0xffc890,1.3,9,2);scene.add(hallLightA);
var hallLightB=new THREE.PointLight(0xffc890,1.0,9,2);scene.add(hallLightB);
(function(){
box(2.35,.1,27.4,M.carpet,-1.5,-.05,16.4,false,true);
box(2.35,.1,27.4,M.ceil,-1.5,2.7,16.4,false,false);
box(.12,2.8,27.4,M.paper,-2.61,1.4,16.4);
box(.12,2.8,27.4,M.paper,-0.39,1.4,16.4);
for(var fz=4;fz<=28;fz+=4){
var fx=new THREE.Mesh(new THREE.BoxGeometry(.5,.05,.24),M.metal);
fx.position.set(-1.5,2.62,fz);scene.add(fx);
var bm=new THREE.MeshBasicMaterial({color:0xffd9a0,fog:false});bm.toneMapped=false;
var bp=new THREE.Mesh(new THREE.PlaneGeometry(.4,.14),bm);
bp.rotation.x=Math.PI/2;bp.position.set(-1.5,2.59,fz);scene.add(bp);}
var pz2=new THREE.Mesh(new THREE.PlaneGeometry(.9,.6),new THREE.MeshBasicMaterial({map:ctex(256,160,function(x){
x.fillStyle='#161009';x.fillRect(0,0,256,160);x.fillStyle='#ffb45e';
x.font='700 60px "Space Grotesk"';x.textAlign='center';x.textBaseline='middle';
x.fillText('STAIRS',128,58);x.fillText('\u2193',128,122);})}));
pz2.position.set(-1.5,2.25,29.9);pz2.rotation.y=Math.PI;scene.add(pz2);
var pf=new THREE.Mesh(new THREE.PlaneGeometry(.7,.9),new THREE.MeshStandardMaterial({map:posterBTex,roughness:.9}));
pf.position.set(-0.385,1.6,11);pf.rotation.y=-Math.PI/2;scene.add(pf);})();
function aptDoor(x,z,ry,num){
var g=new THREE.Group();g.position.set(x,0,z);g.rotation.y=ry;scene.add(g);
var frame=new THREE.Mesh(new THREE.BoxGeometry(1.06,2.16,.08),M.darkwood);frame.position.y=1.08;frame.castShadow=true;g.add(frame);
var panel=new THREE.Mesh(new THREE.BoxGeometry(.92,2.02,.06),
new THREE.MeshStandardMaterial({color:0x4a3524,roughness:.75}));panel.position.set(0,1.01,.03);panel.castShadow=true;g.add(panel);
var kn=new THREE.Mesh(new THREE.SphereGeometry(.03,8,8),M.metal);kn.position.set(.33,1.0,.08);g.add(kn);
var plate=new THREE.Mesh(new THREE.PlaneGeometry(.2,.1),
new THREE.MeshBasicMaterial({map:ctex(128,64,function(cx){cx.fillStyle='#c8a050';cx.fillRect(0,0,128,64);
cx.fillStyle='#1a120c';cx.font='700 40px "Space Grotesk"';cx.textAlign='center';cx.textBaseline='middle';cx.fillText(num,64,34);})}));
plate.position.set(0,1.5,.065);g.add(plate);
addI(proxyS(.55,x+Math.sin(ry)*.45,1.2,z+Math.cos(ry)*.45),'knock on '+num,function(){
knockS();toast(pick(['no answer. something shuffled away from the door.','knock knock. the apartment holds its breath.',
'a chain slides. then nothing.','the peephole was already watching you.','far below, something knocked back.']));});}
var dz=5.5,dn=101;
while(dz<=26){aptDoor(-2.52,dz,Math.PI/2,String(dn));aptDoor(-0.48,dz+2,-Math.PI/2,String(dn+1));dn+=2;dz+=4;}
/* ===== the infinite staircase — 24 floors, each flight darker than the last ===== */
(function(){
var wallLen=ST.zEnd-22,wallCz=(22+ST.zEnd)/2;
var stairWallMat=new THREE.MeshStandardMaterial({map:paperTex,roughness:.95,color:new THREE.Color(.5,.5,.52)});
box(.12,110,wallLen,stairWallMat,-2.61,-35,wallCz);
box(.12,110,wallLen,stairWallMat,-0.39,-35,wallCz);
var ang=Math.atan(ST.slope);
for(var fl=0;fl<ST.floors;fl++){
var zs=ST.z0+fl*ST.unit;
/* baked-in descent: every floor eats more light */
var dimK=Math.max(.035,Math.pow(.8,fl));
var stepMat=new THREE.MeshStandardMaterial({map:stepTex,roughness:.9,color:new THREE.Color(dimK,dimK,dimK)});
var railK=Math.max(.06,Math.pow(.84,fl));
var railMat=new THREE.MeshStandardMaterial({color:new THREE.Color(0x3a281c).multiplyScalar(railK),roughness:.7});
var ramp=new THREE.Mesh(new THREE.BoxGeometry(2.1,.14,Math.sqrt(ST.flight*ST.flight+ST.drop*ST.drop)+.1),stepMat);
ramp.rotation.x=ang;
ramp.position.set(-1.5,-(fl*ST.drop+ST.drop/2)-.05,zs+ST.flight/2);
ramp.receiveShadow=true;scene.add(ramp);
var land=new THREE.Mesh(new THREE.BoxGeometry(2.1,.14,ST.landing),stepMat);
land.position.set(-1.5,-(fl+1)*ST.drop-.06,zs+ST.flight+ST.landing/2);
land.receiveShadow=true;scene.add(land);
[-1,1].forEach(function(s){
var rail=new THREE.Mesh(new THREE.BoxGeometry(.05,.05,ST.flight+.3),railMat);
rail.rotation.x=ang;rail.position.set(-1.5+s*1.0,-(fl*ST.drop+ST.drop/2)+.82,zs+ST.flight/2);scene.add(rail);
var post=new THREE.Mesh(new THREE.CylinderGeometry(.025,.025,.85,6),railMat);
post.position.set(-1.5+s*1.0,-(fl+1)*ST.drop+.42,zs+ST.flight+ST.landing-.15);scene.add(post);});
var sign=new THREE.Mesh(new THREE.PlaneGeometry(.5,.25),
new THREE.MeshBasicMaterial({map:floorSignTex(fl+2)}));
sign.position.set(-0.385,-(fl+1)*ST.drop+1.45,zs+ST.flight+ST.landing/2);
sign.rotation.y=-Math.PI/2;scene.add(sign);}
var sign1=new THREE.Mesh(new THREE.PlaneGeometry(.5,.25),new THREE.MeshBasicMaterial({map:floorSignTex(1)}));
sign1.position.set(-0.385,1.5,4.2);sign1.rotation.y=-Math.PI/2;scene.add(sign1);})();
/* ===== the lobby at the bottom ===== */
var lobbyL1=new THREE.PointLight(0xffd0a0,1.7,10,2);lobbyL1.position.set(-1.5,LOBBY_Y+2.3,LOB+3.8);scene.add(lobbyL1);
var lobbyL2=new THREE.PointLight(0xffb878,1.4,9,2);lobbyL2.position.set(-1.5,LOBBY_Y+2.3,LOB+7.8);scene.add(lobbyL2);
var lobbySeen=false;
(function(){
var y0=LOBBY_Y;
box(6.2,.12,10.4,new THREE.MeshStandardMaterial({map:lobbyFloorTex,roughness:.6}),-1.5,y0-.06,LOB+5.2,false,true);
box(6.2,.12,10.4,M.ceil,-1.5,y0+2.8,LOB+5.2,false,false);
box(.14,3,10.4,M.paper,-4.63,y0+1.4,LOB+5.2);
box(.14,3,10.4,M.paper,1.63,y0+1.4,LOB+5.2);
box(6.4,3,.14,M.paper,-1.5,y0+1.4,LOB+10.35);
box(2.05,2.9,.14,M.paper,-3.6,y0+1.45,LOB);
box(2.05,2.9,.14,M.paper,.6,y0+1.45,LOB);
box(2.2,.9,.14,M.paper,-1.5,y0+2.45,LOB);
box(1.5,1.0,.55,M.wood2,-2.9,y0+.5,LOB+6.9);
box(1.6,.05,.62,M.darkwood,-2.9,y0+1.03,LOB+6.9);
cyl(.05,.06,.05,M.metal,-2.5,y0+1.1,LOB+6.8);
var mail=new THREE.Group();mail.position.set(1.55,y0+1.4,LOB+5.8);mail.rotation.y=-Math.PI/2;scene.add(mail);
var mback=new THREE.Mesh(new THREE.BoxGeometry(1.4,1.0,.06),M.darkwood);mail.add(mback);
for(var mi=0;mi<4;mi++)for(var mj=0;mj<3;mj++){
var mb=new THREE.Mesh(new THREE.BoxGeometry(.3,.26,.05),
new THREE.MeshStandardMaterial({color:0x6a5238,metalness:.4,roughness:.5}));
mb.position.set(-.48+mi*.32,-.36+mj*.34,.05);mail.add(mb);}
var rug2=new THREE.Mesh(new THREE.PlaneGeometry(2.6,1.8),new THREE.MeshStandardMaterial({map:rugTex,roughness:1}));
rug2.rotation.x=-Math.PI/2;rug2.position.set(-1.2,y0+.01,LOB+4.1);rug2.receiveShadow=true;scene.add(rug2);
box(1.7,.42,.7,new THREE.MeshStandardMaterial({color:0x6a3a28,roughness:1}),-.6,y0+.21,LOB+9.5);
box(1.7,.5,.18,new THREE.MeshStandardMaterial({color:0x6a3a28,roughness:1}),-.6,y0+.62,LOB+9.85);
box(.5,.14,.55,M.pillow,-1.1,y0+.48,LOB+9.55);box(.5,.14,.55,M.blanket,-.15,y0+.48,LOB+9.55);
cyl(.02,.03,1.5,M.metal,.9,y0+.75,LOB+9.1);
var lsh=new THREE.Mesh(new THREE.CylinderGeometry(.12,.2,.28,12,1,true),
new THREE.MeshStandardMaterial({color:0xc89050,roughness:.6,side:THREE.DoubleSide}));
lsh.position.set(.9,y0+1.6,LOB+9.1);scene.add(lsh);
glow(0xffc88a,.7,.9,y0+1.55,LOB+9.1,.9);
var wsign=new THREE.Mesh(new THREE.PlaneGeometry(1.6,.4),new THREE.MeshBasicMaterial({map:ctex(512,128,function(x){
x.fillStyle='#161009';x.fillRect(0,0,512,128);x.strokeStyle='rgba(255,180,94,.6)';x.lineWidth=5;x.strokeRect(8,8,496,112);
x.fillStyle='#ffd9a8';x.font='italic 54px Georgia';x.textAlign='center';x.textBaseline='middle';
x.fillText('welcome home',256,66);})}));
wsign.position.set(-1.5,y0+2.1,LOB+10.3);wsign.rotation.y=Math.PI;scene.add(wsign);
addI(proxyS(.3,-2.5,y0+1.2,LOB+6.8),'ring the front desk bell',function(){bellRing();
toast(pick(['the bell rings. somewhere far away, it always rings back.','no one comes. the bell knows.','the night auditor clocked out in 1987.']));});
addI(proxy(.2,1.1,1.5,1.5,y0+1.4,LOB+5.8),'check the mailboxes',function(){clickSound();
toast(pick(['all of it is addressed to previous tenants. all of them are you.','a postcard from the lobby. it says: \u201Cstay.\u201D',
'nothing but bills for heat you can\u2019t feel.','box 3C is warm for no reason.']));});})();
/* ===== window + 3D city ===== */
var glass=new THREE.Mesh(new THREE.PlaneGeometry(1.7,1.5),
new THREE.MeshPhongMaterial({color:0x9db8d8,transparent:true,opacity:.1,shininess:120,depthWrite:false}));
glass.rotation.y=Math.PI/2;glass.position.set(-3.21,1.7,-.4);scene.add(glass);
var streakMat=new THREE.MeshBasicMaterial({map:streakTex,transparent:true,opacity:.55,depthWrite:false});
var streak=new THREE.Mesh(new THREE.PlaneGeometry(1.7,1.5),streakMat);
streak.rotation.y=Math.PI/2;streak.position.set(-3.19,1.7,-.4);scene.add(streak);
box(.14,.07,1.84,M.darkwood,-3.2,2.47,-.4);box(.14,.07,1.84,M.darkwood,-3.2,.93,-.4);
box(.14,1.6,.07,M.darkwood,-3.2,1.7,-1.27);box(.14,1.6,.07,M.darkwood,-3.2,1.7,.47);
box(.1,1.5,.045,M.darkwood,-3.2,1.7,-.4);box(.1,.045,1.7,M.darkwood,-3.2,1.7,-.4);
box(.24,.06,1.9,M.wood2,-3.12,.92,-.4);
var sky=new THREE.Mesh(new THREE.PlaneGeometry(50,25),new THREE.MeshBasicMaterial({map:skyTex,fog:false}));
sky.rotation.y=Math.PI/2;sky.position.set(-30,5,-.4);scene.add(sky);
var cityG=new THREE.Group();scene.add(cityG);
var cityTex=ctex(128,256,function(x){x.fillStyle='#0a0e16';x.fillRect(0,0,128,256);
for(var wy=8;wy<250;wy+=14){for(var wx=6;wx<122;wx+=12){
if(Math.random()<.32){x.fillStyle=Math.random()<.75?'rgba(255,180,90,.85)':'rgba(120,200,255,.8)';x.fillRect(wx,wy,6,8);}
else{x.fillStyle='rgba(30,40,60,.6)';x.fillRect(wx,wy,6,8);}}}});
cityTex.wrapS=cityTex.wrapT=THREE.RepeatWrapping;
var cityMats=[];
for(var cmi=0;cmi<3;cmi++){var ct=cityTex.clone();ct.needsUpdate=true;ct.repeat.set(1+cmi,2);
cityMats.push(new THREE.MeshStandardMaterial({map:ct,emissive:0xffffff,emissiveMap:ct,emissiveIntensity:.55,color:0x1a2030,roughness:.9}));}
for(var bi=0;bi<40;bi++){
var bw=rand(.8,2.4),bd=rand(.8,2.4),bh=rand(2,9);
var bx=rand(-22,-6),bz=rand(-14,14);
var bm=new THREE.Mesh(new THREE.BoxGeometry(bw,bh,bd),cityMats[bi%3]);
bm.position.set(bx,-4+bh/2,bz);cityG.add(bm);}
var beacons=[];
for(var bci=0;bci<7;bci++){var bc=new THREE.Mesh(new THREE.SphereGeometry(.07,6,6),new THREE.MeshBasicMaterial({color:0xff3030}));
bc.material.toneMapped=false;bc.position.set(rand(-20,-7),rand(2,5.5),rand(-12,12));cityG.add(bc);beacons.push(bc);}
var RN=CNT(300),rPos=new Float32Array(RN*6),rSp=[],rLen=[];
for(var ri=0;ri<RN;ri++){var rx=-4.8+Math.random()*1.4,rz=-1.7+Math.random()*2.45,ry=Math.random()*3.4,rl=.12+Math.random()*.12;
rPos[ri*6]=rx;rPos[ri*6+1]=ry;rPos[ri*6+2]=rz;rPos[ri*6+3]=rx;rPos[ri*6+4]=ry-rl;rPos[ri*6+5]=rz;
rSp.push(4.5+Math.random()*3.5);rLen.push(rl);}
var rainGeo=new THREE.BufferGeometry();
rainGeo.setAttribute('position',new THREE.BufferAttribute(rPos,3));
scene.add(new THREE.LineSegments(rainGeo,new THREE.LineBasicMaterial({color:0x8fb0e8,transparent:true,opacity:.35,fog:false})));
function curtainPanel(){var g=new THREE.PlaneGeometry(.95,2,22,1),p=g.attributes.position;
for(var i=0;i<p.count;i++)p.setZ(i,Math.sin((p.getX(i)/.95)*Math.PI*4)*.05);
g.computeVertexNormals();var m=new THREE.Mesh(g,M.fabric);m.rotation.y=Math.PI/2;m.castShadow=true;scene.add(m);return m;}
var curtA=curtainPanel(),curtB=curtainPanel();
cyl(.015,.015,2.5,M.metal,-3.03,2.53,-.4).rotation.x=Math.PI/2;
var ZA={open:-1.42,closed:-.83},ZB={open:.62,closed:.03};
/* ===== bed ===== */
box(1.75,.28,2.2,M.darkwood,2.28,.2,-1.45);box(1.75,.95,.09,M.darkwood,2.28,.62,-2.53);
box(1.62,.22,2.02,M.cream,2.28,.46,-1.42);box(1.68,.1,1.25,M.blanket,2.28,.61,-.85);
box(1.68,.42,.07,M.blanket,2.28,.42,-.26);
function pil(x,z,ryy){var p=box(.62,.14,.4,M.pillow,x,.62,z);p.rotation.y=ryy;p.rotation.z=.06;}
pil(1.95,-2.15,.12);pil(2.6,-2.18,-.1);
/* ===== desk + monitor ===== */
box(1.7,.06,.72,M.wood2,-1.75,.74,-2.22);
[[-2.55,-2.52],[-.95,-2.52],[-2.55,-1.92],[-.95,-1.92]].forEach(function(c){box(.06,.72,.06,M.wood2,c[0],.36,c[1]);});
var monG=new THREE.Group();monG.position.set(-1.75,.77,-2.42);scene.add(monG);
function monPart(w,h,d,y){var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),M.metal);m.position.y=y;m.castShadow=true;monG.add(m);return m;}
monPart(.24,.02,.16,.01);monPart(.05,.28,.05,.15);monPart(.68,.4,.04,.42);
var shopCv=document.createElement('canvas');shopCv.width=512;shopCv.height=288;
var shopCtx=shopCv.getContext('2d');
var screenTex=new THREE.CanvasTexture(shopCv);screenTex.encoding=THREE.sRGBEncoding;
var screenMat=new THREE.MeshBasicMaterial({map:screenTex,fog:false});screenMat.toneMapped=false;
var screenMesh=new THREE.Mesh(new THREE.PlaneGeometry(.62,.35),screenMat);
screenMesh.position.set(0,.42,.022);monG.add(screenMesh);
box(.44,.025,.15,M.metal,-1.75,.782,-1.98);box(.07,.02,.11,M.metal,-1.42,.782,-1.96);
box(.2,.035,.14,M.darkwood,-2.35,.8,-2.05);
var lampG=new THREE.Group();lampG.position.set(-2.42,.77,-2.38);scene.add(lampG);
var lampBulbMat=new THREE.MeshBasicMaterial({color:0xffd9a0,fog:false});lampBulbMat.toneMapped=false;
(function(){var base=new THREE.Mesh(new THREE.CylinderGeometry(.09,.1,.03,16),M.metal);base.position.y=.015;lampG.add(base);
var stem=new THREE.Mesh(new THREE.CylinderGeometry(.014,.014,.28,8),M.metal);stem.position.y=.15;lampG.add(stem);
var arm=new THREE.Mesh(new THREE.CylinderGeometry(.012,.012,.36,8),M.metal);
arm.position.set(.15,.42,0);arm.rotation.z=-1.05;lampG.add(arm);
var shade=new THREE.Mesh(new THREE.CylinderGeometry(.045,.105,.12,16,1,true),
new THREE.MeshStandardMaterial({color:0x8a5a34,roughness:.6,side:THREE.DoubleSide}));
shade.position.set(.31,.48,0);shade.rotation.z=-2.15;shade.castShadow=true;lampG.add(shade);
var bulb=new THREE.Mesh(new THREE.SphereGeometry(.03,10,10),lampBulbMat);bulb.position.set(.34,.44,0);lampG.add(bulb);})();
var lampGlowSpr=glow(0xffb066,.5,-2.06,1.2,-2.38,0);
var deskLight=new THREE.PointLight(0xffb070,0,5,2);deskLight.position.set(-2.05,1.18,-2.36);scene.add(deskLight);
var goldCol=new THREE.Color(0xffd08a),warmCol=new THREE.Color(0xffb070);
(function(){var g=new THREE.Group();g.position.set(-1.75,0,-1.5);g.rotation.y=.25;scene.add(g);
function part(w,h,d,y,z){var m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),M.wood2);m.position.set(0,y,z||0);m.castShadow=true;g.add(m);}
part(.5,.05,.5,.45);part(.46,.05,.46,.5);part(.5,.55,.05,.76,-.24);
[[-.21,-.21],[.21,-.21],[-.21,.21],[.21,.21]].forEach(function(c){
var l=new THREE.Mesh(new THREE.CylinderGeometry(.02,.02,.45,8),M.wood2);l.position.set(c[0],.225,c[1]);g.add(l);});})();
(function(){var x=3.08;
box(.28,1.9,.05,M.darkwood,x,.95,.52);box(.28,1.9,.05,M.darkwood,x,.95,2.28);
box(.03,1.9,1.8,M.darkwood,3.2,.95,.42,false,false);
var cols=['#8a4a3a','#b0803f','#5a6a52','#4a5a6a','#7a5a70','#a06a4a','#c0a070','#6a4a3a'];
[.06,.55,1.05,1.55].forEach(function(sy){box(.28,.05,.5,M.darkwood,x,sy,.42);
var z=.1;while(z<.72){var w=.035+Math.random()*.03,h=.26+Math.random()*.12;
box(.2,h,w,new THREE.MeshStandardMaterial({color:cols[Math.floor(Math.random()*cols.length)],roughness:.85}),3.05,sy+.05+h/2,z+w/2,false,false);
z+=w+.006;}});})();
cyl(.3,.26,.05,M.wood2,-2.65,.53,1.45);cyl(.05,.07,.5,M.wood2,-2.65,.26,1.45);
var glassMat,liquidMat,lavaLight,blobs=[],lavaAnchor;
(function(){var lx=-2.65,ly=.56,lz=1.45;
cyl(.085,.11,.07,M.metal,lx,ly+.035,lz);
glassMat=new THREE.MeshPhongMaterial({color:0xff8a4a,transparent:true,opacity:.3,shininess:90,side:THREE.DoubleSide,depthWrite:false});
var gl=new THREE.Mesh(new THREE.CylinderGeometry(.055,.085,.34,16,1,true),glassMat);gl.position.set(lx,ly+.24,lz);scene.add(gl);
liquidMat=new THREE.MeshStandardMaterial({color:0x801a00,emissive:0xff5a1f,emissiveIntensity:1.5,transparent:true,opacity:.85});
var liq=new THREE.Mesh(new THREE.CylinderGeometry(.05,.08,.3,12),liquidMat);liq.position.set(lx,ly+.22,lz);scene.add(liq);
cyl(.02,.058,.09,M.metal,lx,ly+.455,lz);
for(var i=0;i<(PERF?3:4);i++){var b=new THREE.Mesh(new THREE.SphereGeometry(.022+Math.random()*.013,8,8),
new THREE.MeshStandardMaterial({color:0xff5a1f,emissive:0xff7a30,emissiveIntensity:2}));
b.userData={ph:Math.random()*9,sp:.4+Math.random()*.5,ox:rand(-.05,.05),oz:rand(-.05,.05)};
scene.add(b);blobs.push(b);}
lavaLight=new THREE.PointLight(0xff6a2a,1.3,2.8,2);lavaLight.position.set(lx,ly+.25,lz);scene.add(lavaLight);
lavaAnchor={x:lx,y:ly+.66,z:lz};})();
(function(){cyl(.16,.12,.26,new THREE.MeshStandardMaterial({color:0x7a4a34,roughness:.8}),-2.85,.13,2.25);
var leafMat=new THREE.MeshStandardMaterial({color:0x3f5a34,roughness:.9});
for(var i=0;i<7;i++){var l=new THREE.Mesh(new THREE.ConeGeometry(.05,.55,6),leafMat);
l.scale.z=.3;l.position.set(-2.85,.5,2.25);
l.rotation.set((Math.random()-.5)*.9,i*(Math.PI*2/7),(Math.random()-.3)*.7);
l.translateY(.25);l.castShadow=true;scene.add(l);}})();
var rug=new THREE.Mesh(new THREE.PlaneGeometry(2.7,2),new THREE.MeshStandardMaterial({map:rugTex,roughness:1}));
rug.rotation.x=-Math.PI/2;rug.position.set(.3,.006,.55);rug.receiveShadow=true;scene.add(rug);
/* ===== cat ===== */
var catG=new THREE.Group();catG.position.set(.85,0,.9);catG.rotation.y=-.5;scene.add(catG);
var fur=new THREE.MeshStandardMaterial({color:0x4a403a,roughness:1});
var catBody=new THREE.Mesh(new THREE.SphereGeometry(.3,12,10),fur);
catBody.scale.set(1,.6,.75);catBody.position.y=.17;catBody.castShadow=true;catG.add(catBody);
var catHead=new THREE.Mesh(new THREE.SphereGeometry(.14,10,8),fur);
catHead.position.set(.2,.22,.12);catHead.castShadow=true;catG.add(catHead);
var earG=[];
[-1,1].forEach(function(sx){var g=new THREE.Group();g.position.set(.2+sx*.07,.33,.12);catG.add(g);
var e=new THREE.Mesh(new THREE.ConeGeometry(.045,.09,6),fur);e.position.y=.03;e.rotation.z=-sx*.25;g.add(e);earG.push(g);});
var tail=new THREE.Mesh(new THREE.TorusGeometry(.2,.045,6,14,Math.PI*1.2),fur);
tail.rotation.x=-Math.PI/2;tail.position.set(.05,.05,.18);tail.rotation.z=2.4;catG.add(tail);
[-1,1].forEach(function(sx){var eye=new THREE.Mesh(new THREE.BoxGeometry(.035,.006,.008),new THREE.MeshBasicMaterial({color:0x1a1410}));
eye.position.set(.2+sx*.055,.23,.245);catG.add(eye);});
var zzz=[];
for(var zi=0;zi<3;zi++){var zs=new THREE.Sprite(new THREE.SpriteMaterial({map:zzzTex,transparent:true,opacity:0,depthWrite:false}));
zs.userData.ph=zi/3;catG.add(zs);zzz.push(zs);}
var twitchT=-1,nextTwitch=4,catWoke=false,catK=0;
var pianoNotes=[];
(function(){box(.5,.32,.3,M.wood2,1.35,.16,1.95);
box(.46,.07,.2,M.darkwood,1.35,.36,1.95);
for(var i=0;i<5;i++){var k=box(.08,.02,.14,new THREE.MeshStandardMaterial({color:'#e8e0d0',roughness:.4}),1.17+i*.09,.4,1.92,false,false);
pianoNotes.push(k);}})();
var pianoSeq=[],pianoPaid=false;
function stringLights(pts,n){var curve=new THREE.CatmullRomCurve3(pts);
scene.add(new THREE.Mesh(new THREE.TubeGeometry(curve,32,.006,5),new THREE.MeshStandardMaterial({color:0x1a130d,roughness:.8})));
var arr=[];
for(var i=1;i<=n;i++){var p=curve.getPoint(i/(n+1));p.y-=.035;
var mat=new THREE.MeshBasicMaterial({fog:false});mat.toneMapped=false;
var b=new THREE.Mesh(new THREE.SphereGeometry(.022,8,8),mat);b.position.copy(p);scene.add(b);
var sm=new THREE.SpriteMaterial({map:glowTex,color:0xffa050,transparent:true,opacity:.85,
blending:THREE.AdditiveBlending,depthWrite:false,fog:false});
var s=new THREE.Sprite(sm);s.scale.set(.22,.22,1);s.position.copy(p);scene.add(s);
arr.push({mat:mat,sm:sm,ph:Math.random()*9,base:new THREE.Color(i%3?0xffb066:0xffd9a0)});}
return arr;}
var allBulbs=stringLights([new THREE.Vector3(-2.95,2.42,-2.56),new THREE.Vector3(-2.2,2.26,-2.56),
new THREE.Vector3(-1.5,2.38,-2.56),new THREE.Vector3(-.75,2.26,-2.56)],PERF?7:11)
.concat(stringLights([new THREE.Vector3(3.16,2.45,-2.35),new THREE.Vector3(3.16,2.28,-1.5),
new THREE.Vector3(3.16,2.4,-.65)],PERF?5:8));
var fairyL1=new THREE.PointLight(0xffa050,.9,3.5,2);fairyL1.position.set(-1.8,2.25,-2.3);scene.add(fairyL1);
var fairyL2=new THREE.PointLight(0xffa050,.8,3,2);fairyL2.position.set(2.9,2.3,-1.5);
if(!PERF)scene.add(fairyL2);
var ceilG=new THREE.Group();ceilG.position.set(.2,3,.2);scene.add(ceilG);
var ceilBulbMat=new THREE.MeshBasicMaterial({color:0x33241a,fog:false});ceilBulbMat.toneMapped=false;
(function(){var cord=new THREE.Mesh(new THREE.CylinderGeometry(.008,.008,.55,6),M.metal);cord.position.y=-.275;ceilG.add(cord);
var shade=new THREE.Mesh(new THREE.CylinderGeometry(.09,.24,.2,PERF?10:20,1,true),
new THREE.MeshStandardMaterial({color:0x8a5a34,roughness:.6,side:THREE.DoubleSide}));
shade.position.y=-.62;shade.castShadow=true;ceilG.add(shade);
var bulb=new THREE.Mesh(new THREE.SphereGeometry(.045,10,10),ceilBulbMat);bulb.position.y=-.66;ceilG.add(bulb);})();
var ceilGlowSpr=glow(0xffc27a,.6,.2,2.32,.2,0);
var spotCeil=new THREE.SpotLight(0xffc27a,0,0,1.15,.55,1.8);
spotCeil.position.set(.2,2.32,.2);spotCeil.castShadow=!PERF;
spotCeil.shadow.mapSize.set(512,512);spotCeil.shadow.bias=-.002;
spotCeil.target.position.set(.2,0,.2);scene.add(spotCeil);scene.add(spotCeil.target);
function poster(tex,w,h,x,y,z,ryy){var f=new THREE.Mesh(new THREE.PlaneGeometry(w+.06,h+.06),M.darkwood);
f.position.set(x,y,z);f.rotation.y=ryy;f.translateZ(-.005);scene.add(f);
var p=new THREE.Mesh(new THREE.PlaneGeometry(w,h),new THREE.MeshStandardMaterial({map:tex,roughness:.9}));
p.position.set(x,y,z);p.rotation.y=ryy;scene.add(p);return p;}
poster(posterATex,.55,.72,2.3,1.95,-2.59,0);
poster(posterBTex,.5,.66,3.19,1.75,-1.35,-Math.PI/2);
poster(pizzaPosterTex,.55,.72,1.9,1.7,2.59,Math.PI);
var clockG=new THREE.Group();clockG.position.set(.9,2.25,2.585);clockG.rotation.y=Math.PI;scene.add(clockG);
var handM=new THREE.Group(),handH=new THREE.Group(),handS=new THREE.Group();
(function(){var face=new THREE.Mesh(new THREE.CylinderGeometry(.16,.16,.03,PERF?12:24),
new THREE.MeshStandardMaterial({color:0xe8e0d0,roughness:.8}));
face.rotation.x=Math.PI/2;clockG.add(face);
clockG.add(new THREE.Mesh(new THREE.TorusGeometry(.16,.015,6,PERF?14:28),M.darkwood));
function mk(g,w,l,mat){var m=new THREE.Mesh(new THREE.BoxGeometry(w,l,.008),mat);
m.position.y=l/2;g.add(m);g.position.z=.02;clockG.add(g);}
mk(handM,.012,.13,M.metal);mk(handH,.016,.09,M.metal);
mk(handS,.005,.14,new THREE.MeshBasicMaterial({color:0xff9a3c}));})();
/* ===== collision — kitchen doorway open, staircase fully walkable ===== */
var colliders=[
{x0:1.42,x1:3.22,z0:-2.62,z1:-.3},{x0:-2.68,x1:-.82,z0:-2.62,z1:-1.86},
{x0:-2.06,x1:-1.4,z0:-1.82,z1:-1.2},{x0:-2.98,x1:-2.32,z0:1.12,z1:1.78},
{x0:2.9,x1:3.24,z0:.02,z1:.78},
{x0:-3.4,x1:3.4,z0:-2.76,z1:-2.6},
{x0:-3.4,x1:-1.93,z0:2.6,z1:2.76},{x0:-1.07,x1:3.4,z0:2.6,z1:2.76},
/* left wall split around the kitchen doorway (opening z 1.26..2.14) */
{x0:-3.34,x1:-3.16,z0:-2.76,z1:1.26},
{x0:-3.34,x1:-3.16,z0:2.14,z1:2.76},
{x0:-3.24,x1:-3.16,z0:1.26,z1:1.34},
{x0:-3.24,x1:-3.16,z0:2.06,z1:2.14},
{x0:3.2,x1:3.4,z0:-2.76,z1:.8},{x0:3.2,x1:3.4,z0:2.2,z1:2.76},
{x0:-2.07,x1:-1.93,z0:2.6,z1:5.05},{x0:-1.07,x1:-.93,z0:2.6,z1:5.05},
/* bathroom */
{x0:3.2,x1:7.1,z0:-.74,z1:-.6},{x0:3.2,x1:7.1,z0:2.7,z1:2.86},{x0:6.94,x1:7.1,z0:-.74,z1:2.86},
{x0:5.9,x1:6.85,z0:-.25,z1:1.65},{x0:3.55,x1:4.35,z0:-.6,z1:-.12},
{x0:3.5,x1:4.15,z0:2.1,z1:2.72},{x0:6.15,x1:6.85,z0:2.0,z1:2.72},
{x0:3.4,x1:3.85,z0:-.1,z1:.4},
/* kitchen */
{x0:-7.12,x1:-3.2,z0:.18,z1:.32},{x0:-7.12,x1:-3.2,z0:2.66,z1:2.8},{x0:-7.14,x1:-7.0,z0:.18,z1:2.8},
{x0:-7.0,x1:-4.8,z0:.35,z1:.9},{x0:-7.0,x1:-6.3,z0:2.0,z1:2.65},{x0:-4.95,x1:-4.05,z0:1.35,z1:2.05},
/* hallway + stairs */
{x0:-2.8,x1:-2.55,z0:2.72,z1:ST.zEnd+1},{x0:-0.45,x1:-0.2,z0:2.72,z1:ST.zEnd+1},
/* lobby */
{x0:-4.7,x1:-4.5,z0:LOB-.1,z1:LOB+10.5},{x0:1.5,x1:1.7,z0:LOB-.1,z1:LOB+10.5},
{x0:-4.7,x1:-2.6,z0:LOB-.1,z1:LOB+.15},{x0:-0.4,x1:1.7,z0:LOB-.1,z1:LOB+.15},
{x0:-4.7,x1:1.7,z0:LOB+10.3,z1:LOB+10.5}];
var doorCol={x0:-1.93,x1:-1.07,z0:2.56,z1:2.72};
function pushOut(p,rad,list){
for(var i=0;i<list.length;i++){var b=list[i];
var cx=clamp(p.x,b.x0,b.x1),cz=clamp(p.z,b.z0,b.z1);
var dx=p.x-cx,dz=p.z-cz,d2=dx*dx+dz*dz;
if(d2<rad*rad){if(d2<1e-6){var pl=p.x-b.x0,pr=b.x1-p.x,pf=p.z-b.z0,pb=b.z1-p.z,m=Math.min(pl,pr,pf,pb);
if(m===pl)p.x=b.x0-rad;else if(m===pr)p.x=b.x1+rad;else if(m===pf)p.z=b.z0-rad;else p.z=b.z1+rad;}
else{var d=Math.sqrt(d2),push=(rad-d)/d;p.x+=dx*push;p.z+=dz*push;}}}}
function collide(p){
p.x=clamp(p.x,-300,300);
/* door closed = stay in the room; door open = the whole building is yours */
p.z=clamp(p.z,-2.22,doorK<.6?2.22:LOB+9.8);
var all=colliders;
if(doorK<.6)all=colliders.concat([doorCol]);
pushOut(p,R,all);}
/* ===== shared money displays ===== */
var counterCv=document.createElement('canvas');counterCv.width=256;counterCv.height=80;
var counterCtx=counterCv.getContext('2d');
var counterTex=new THREE.CanvasTexture(counterCv);counterTex.encoding=THREE.sRGBEncoding;
var counterMesh=new THREE.Mesh(new THREE.PlaneGeometry(.44,.135),new THREE.MeshBasicMaterial({map:counterTex,fog:false}));
counterMesh.position.set(-1.75,1.66,-2.59);scene.add(counterMesh);
function drawCounter(){var x=counterCtx;
x.fillStyle='#0d0805';x.fillRect(0,0,256,80);
x.strokeStyle='rgba(255,180,94,.4)';x.lineWidth=3;x.strokeRect(3,3,250,74);
x.fillStyle='#ffb45e';x.font='700 40px "Space Grotesk",monospace';
x.textAlign='center';x.textBaseline='middle';
x.fillText('\u25C8 '+Math.floor(shared.money),128,42);counterTex.needsUpdate=true;}
var ledgerCv=document.createElement('canvas');ledgerCv.width=512;ledgerCv.height=360;
var ledgerCtx=ledgerCv.getContext('2d');
var ledgerTex=new THREE.CanvasTexture(ledgerCv);ledgerTex.encoding=THREE.sRGBEncoding;
var ledgerMesh=new THREE.Mesh(new THREE.PlaneGeometry(.92,.64),new THREE.MeshBasicMaterial({map:ledgerTex,fog:false}));
ledgerMesh.position.set(3.14,1.75,-.2);ledgerMesh.rotation.y=-Math.PI/2;scene.add(ledgerMesh);
function peerNames(){var a=[];for(var k in net.peers)a.push(net.peers[k].n);return a.slice(0,4);}
function incomeRate(){return 1+peerNames().length+(shared.owned.cat?1:0);}
function isHost(){var ids=[myId];for(var k in net.peers)ids.push(k);ids.sort();return ids[0]===myId;}
function drawLedger(){var x=ledgerCtx;
x.fillStyle='#100a06';x.fillRect(0,0,512,360);
x.fillStyle='#ffb45e';x.fillRect(0,0,8,360);
x.textAlign='left';x.textBaseline='alphabetic';
x.fillStyle='#ffd9a8';x.font='600 26px "Space Grotesk",sans-serif';
x.fillText('T H E   L E D G E R',34,44);
x.fillStyle='#a08a72';x.font='17px "Space Grotesk"';
x.fillText('room '+net.code+' — one shared wallet for everyone inside',34,76);
x.fillStyle='#d8c6ac';x.font='20px "Space Grotesk"';
var names=peerNames();
x.fillText('in the room:  you'+(names.length?'  +  '+names.join(', '):'')+(isHost()?'   (you keep the night)':''),34,120);
x.fillStyle='#9fd8a8';x.font='600 24px "Space Grotesk"';
x.fillText('income   +'+incomeRate()+' \u25C8 / min   to the shared wallet',34,162);
x.fillStyle='#ffb45e';x.font='700 58px "Space Grotesk",monospace';
x.fillText('\u25C8 '+Math.floor(shared.money),34,236);
x.fillStyle='#8d7a63';x.font='italic 16px Georgia';
x.fillText('the building pays you to stay. the staircase pays nothing.',34,284);
x.fillText('pizza hotline 555-7499 · kitchen is through the left door',34,312);
ledgerTex.needsUpdate=true;}
/* ===== lights ===== */
var hemi=new THREE.HemisphereLight(0x2e2a33,0x1a120c,.55);scene.add(hemi);
var amb=new THREE.AmbientLight(0x33261a,PERF?.95:.8);scene.add(amb);
var moon=new THREE.DirectionalLight(0x93b4ff,.95);
moon.position.set(-8,5,-.4);moon.target.position.set(2,.5,-.4);
moon.castShadow=true;moon.shadow.mapSize.set(PERF?512:1024,PERF?512:1024);moon.shadow.bias=-.0006;
moon.shadow.camera.left=-5;moon.shadow.camera.right=5;moon.shadow.camera.top=5;moon.shadow.camera.bottom=-3;
moon.shadow.camera.near=1;moon.shadow.camera.far=20;moon.shadow.camera.updateProjectionMatrix();
scene.add(moon);scene.add(moon.target);
var moonFill=new THREE.PointLight(0x7fa0e8,PERF?0:1,4,2);moonFill.position.set(-2.9,1.7,-.4);if(!PERF)scene.add(moonFill);
var monLight=new THREE.PointLight(0xffb066,PERF?0:.85,2.6,2);monLight.position.set(-1.75,1.2,-2.05);if(!PERF)scene.add(monLight);
var rigLight=new THREE.PointLight(0xffb070,0,6,2);rigLight.position.set(0,1.2,.2);rig.add(rigLight);
var lighterLight=new THREE.PointLight(0xffa050,0,3.5,2);scene.add(lighterLight);
var DN=CNT(130),dPos=new Float32Array(DN*3),dSp=[],dPh=[];
for(var di=0;di<DN;di++){dPos[di*3]=(Math.random()-.5)*5.6;dPos[di*3+1]=.3+Math.random()*2.4;dPos[di*3+2]=(Math.random()-.5)*4.4;
dSp.push(.02+Math.random()*.05);dPh.push(Math.random()*9);}
var dustGeo=new THREE.BufferGeometry();
dustGeo.setAttribute('position',new THREE.BufferAttribute(dPos,3));
scene.add(new THREE.Points(dustGeo,new THREE.PointsMaterial({color:0xffcf9a,size:.018,transparent:true,opacity:.5,blending:THREE.AdditiveBlending,depthWrite:false})));
var spawnRing=new THREE.Mesh(new THREE.RingGeometry(.28,.34,PERF?20:40),
new THREE.MeshBasicMaterial({color:0xffb45e,transparent:true,opacity:0,side:THREE.DoubleSide,depthWrite:false}));
spawnRing.rotation.x=-Math.PI/2;spawnRing.position.set(SPAWN.x,.012,SPAWN.z);scene.add(spawnRing);
var ringT=-1;
var S={lamp:true,fairy:true,ceil:false,lava:true,curtains:false,door:false};
var vLamp=1,vFairy=1,vCeil=0,vCurt=0,vDark=0,burstT=0;
var bodyMode=null,poseTween=null;
var prevPos=new THREE.Vector3(),prevRotY=0;
var toastEl=document.getElementById('toast'),toastTO;
function toast(msg){toastEl.textContent=msg;toastEl.classList.add('show');
clearTimeout(toastTO);toastTO=setTimeout(function(){toastEl.classList.remove('show');},3800);}
/* ===== audio ===== */
var AC=null,master,rainGain,rainLP,tickTimer=0,radioT=4,showerGain=null,ufoHumOsc=null;
function initAudio(){if(AC){AC.resume();return;}
try{AC=new (window.AudioContext||window.webkitAudioContext)();}catch(e){return;}
master=AC.createGain();master.gain.value=.7;master.connect(AC.destination);
var nb=AC.createBuffer(1,AC.sampleRate*2,AC.sampleRate),d=nb.getChannelData(0);
for(var i=0;i<d.length;i++)d[i]=Math.random()*2-1;
var ns=AC.createBufferSource();ns.buffer=nb;ns.loop=true;
rainLP=AC.createBiquadFilter();rainLP.type='lowpass';rainLP.frequency.value=900;rainLP.Q.value=.6;
rainGain=AC.createGain();rainGain.gain.value=.13;
ns.connect(rainLP);rainLP.connect(rainGain);rainGain.connect(master);ns.start();
var last=0,bb=AC.createBuffer(1,AC.sampleRate*2,AC.sampleRate),bd=bb.getChannelData(0);
for(i=0;i<bd.length;i++){last=(last+.02*(Math.random()*2-1))/1.02;bd[i]=last*3;}
var bs=AC.createBufferSource();bs.buffer=bb;bs.loop=true;
var blp=AC.createBiquadFilter();blp.type='lowpass';blp.frequency.value=180;
var bg=AC.createGain();bg.gain.value=.05;
bs.connect(blp);blp.connect(bg);bg.connect(master);bs.start();
var sb=AC.createBufferSource();sb.buffer=nb;sb.loop=true;
var sf=AC.createBiquadFilter();sf.type='highpass';sf.frequency.value=1200;
showerGain=AC.createGain();showerGain.gain.value=0;
sb.connect(sf);sf.connect(showerGain);showerGain.connect(master);sb.start();
var padGain=AC.createGain();padGain.gain.value=0;
var pf=AC.createBiquadFilter();pf.type='lowpass';pf.frequency.value=620;
[[110,'sine',1],[164.81,'sine',.7],[220,'triangle',.32],[329.63,'sine',.16]].forEach(function(v){
var o=AC.createOscillator();o.type=v[1];o.frequency.value=v[0];o.detune.value=(Math.random()-.5)*8;
var og=AC.createGain();og.gain.value=v[2];o.connect(og);og.connect(pf);o.start();});
pf.connect(padGain);padGain.connect(master);
padGain.gain.linearRampToValueAtTime(.045,AC.currentTime+4);
var lfo=AC.createOscillator();lfo.frequency.value=.07;
var lg=AC.createGain();lg.gain.value=140;lfo.connect(lg);lg.connect(pf.frequency);lfo.start();
tickTimer=.5;
if(shared.owned.ufo&&ufoG)startUfoHum();}
function tone(f,t0,dur,vol,type){var o=AC.createOscillator(),g=AC.createGain();
o.type=type||'sine';o.frequency.value=f;
g.gain.setValueAtTime(.0001,t0);g.gain.linearRampToValueAtTime(vol,t0+.02);
g.gain.exponentialRampToValueAtTime(.0001,t0+dur);
o.connect(g);g.connect(master);o.start(t0);o.stop(t0+dur+.05);}
function clickSound(){if(!AC)return;tone(1900,AC.currentTime,.04,.08,'square');}
function thunk(){if(!AC)return;var t=AC.currentTime,o=AC.createOscillator(),g=AC.createGain();
o.type='sine';o.frequency.setValueAtTime(240,t);o.frequency.exponentialRampToValueAtTime(130,t+.12);
g.gain.setValueAtTime(.1,t);g.gain.exponentialRampToValueAtTime(.0001,t+.14);
o.connect(g);g.connect(master);o.start(t);o.stop(t+.15);}
function coinTick(){if(!AC)return;tone(1320,AC.currentTime,.12,.03);tone(1760,AC.currentTime+.06,.1,.03);}
function squeak(){if(!AC)return;var t=AC.currentTime,o=AC.createOscillator(),g=AC.createGain();
o.type='sine';o.frequency.setValueAtTime(900,t);o.frequency.linearRampToValueAtTime(1500,t+.08);
o.frequency.linearRampToValueAtTime(700,t+.18);
g.gain.setValueAtTime(.07,t);g.gain.exponentialRampToValueAtTime(.0001,t+.22);
o.connect(g);g.connect(master);o.start(t);o.stop(t+.25);}
function flushS(){if(!AC)return;noiseHit(AC.currentTime,.7,500,.12,true);thump(90,AC.currentTime+.3,.15);}
function sinkS(){if(!AC)return;noiseHit(AC.currentTime,.5,1500,.05,true);}
function splash(){if(!AC)return;noiseHit(AC.currentTime,.4,600,.14);thump(80,AC.currentTime+.05,.2);}
function hornBlast(){if(!AC)return;var t=AC.currentTime;
tone(398,t,.8,.14,'sawtooth');tone(405,t,.8,.14,'sawtooth');noiseHit(t,.3,2000,.1);}
function pianoNote(f){if(!AC)return;var t=AC.currentTime;tone(f,t,.9,.07);tone(f*2,t,.5,.02);}
function creak(){if(!AC)return;var t=AC.currentTime;
var o=AC.createOscillator(),g=AC.createGain(),f=AC.createBiquadFilter(),lfo=AC.createOscillator(),lg=AC.createGain();
o.type='sawtooth';o.frequency.setValueAtTime(160,t);o.frequency.linearRampToValueAtTime(70,t+.6);
lfo.frequency.value=7;lg.gain.value=25;lfo.connect(lg);lg.connect(o.frequency);
f.type='lowpass';f.frequency.value=500;
g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(.06,t+.08);g.gain.linearRampToValueAtTime(.0001,t+.65);
o.connect(f);f.connect(g);g.connect(master);o.start(t);lfo.start(t);o.stop(t+.7);lfo.stop(t+.7);}
function purr(){if(!AC)return;var t=AC.currentTime,o=AC.createOscillator(),g=AC.createGain(),l=AC.createOscillator(),lg=AC.createGain();
o.type='sine';o.frequency.value=62;l.frequency.value=22;lg.gain.value=18;
l.connect(lg);lg.connect(o.frequency);
g.gain.setValueAtTime(.001,t);g.gain.linearRampToValueAtTime(.06,t+.08);g.gain.linearRampToValueAtTime(0,t+.5);
o.connect(g);g.connect(master);o.start(t);l.start(t);o.stop(t+.55);l.stop(t+.55);}
function chime(){if(!AC)return;var t=AC.currentTime;tone(660,t,.6,.05);tone(990,t+.07,.6,.05);}
function thump(f,when,vol){var o=AC.createOscillator(),g=AC.createGain();
o.type='sine';o.frequency.setValueAtTime(f,when);o.frequency.exponentialRampToValueAtTime(f*.6,when+.12);
g.gain.setValueAtTime(vol,when);g.gain.exponentialRampToValueAtTime(.0001,when+.16);
o.connect(g);g.connect(master);o.start(when);o.stop(when+.2);}
function noiseHit(t,dur,freq,vol,swell){
var nb=AC.createBuffer(1,Math.max(1,Math.floor(AC.sampleRate*dur)),AC.sampleRate),d=nb.getChannelData(0);
for(var i=0;i<d.length;i++)d[i]=Math.random()*2-1;
var s=AC.createBufferSource();s.buffer=nb;
var f=AC.createBiquadFilter();f.type='bandpass';f.frequency.value=freq||1000;f.Q.value=1;
var g=AC.createGain();var v=vol||.15;
if(swell){g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(v,t+dur*.5);g.gain.linearRampToValueAtTime(.0001,t+dur);}
else{g.gain.setValueAtTime(v,t);g.gain.exponentialRampToValueAtTime(.0001,t+dur);}
s.connect(f);f.connect(g);g.connect(master);s.start(t);}
function bellRing(){if(!AC)return;var t=AC.currentTime;
[880,1320,1760].forEach(function(f,i){tone(f,t,2.2,.06/(i+1));});}
function whoosh(){if(!AC)return;var t=AC.currentTime;
var nb=AC.createBuffer(1,Math.floor(AC.sampleRate*.4),AC.sampleRate),d=nb.getChannelData(0);
for(var i=0;i<d.length;i++)d[i]=Math.random()*2-1;
var s=AC.createBufferSource();s.buffer=nb;
var f=AC.createBiquadFilter();f.type='bandpass';f.Q.value=1.5;
f.frequency.setValueAtTime(300,t);f.frequency.linearRampToValueAtTime(2200,t+.32);
var g=AC.createGain();
g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(.14,t+.2);g.gain.exponentialRampToValueAtTime(.0001,t+.4);
s.connect(f);f.connect(g);g.connect(master);s.start(t);}
function boom(){if(!AC)return;thump(36,AC.currentTime,.35);}
function musicNote(f,when){tone(f,when,1.8,.06);tone(f*2,when,1.2,.015);}
function dtmfBeep(k){if(!AC)return;var t=AC.currentTime;
var rows={'1':697,'2':697,'3':697,'4':770,'5':770,'6':770,'7':852,'8':852,'9':852,'*':941,'0':941,'#':941};
var cols={'1':1209,'2':1336,'3':1452,'4':1209,'5':1336,'6':1452,'7':1209,'8':1336,'9':1452,'*':1209,'0':1336,'#':1452};
tone(rows[k],t,.12,.05);tone(cols[k],t,.12,.05);}
function ringWarble(){if(!AC)return;var t=AC.currentTime;
for(var i=0;i<2;i++){var st=t+i*1.2;tone(440,st,.9,.05);tone(480,st,.9,.05);}}
function sirenS(){if(!AC)return;var t=AC.currentTime;
for(var i=0;i<3;i++){var o=AC.createOscillator(),g=AC.createGain();o.type='square';
o.frequency.setValueAtTime(700,t+i*.5);o.frequency.linearRampToValueAtTime(1000,t+i*.5+.25);o.frequency.linearRampToValueAtTime(700,t+i*.5+.5);
g.gain.setValueAtTime(.0001,t+i*.5);g.gain.linearRampToValueAtTime(.05,t+i*.5+.05);g.gain.linearRampToValueAtTime(.0001,t+i*.5+.5);
o.connect(g);g.connect(master);o.start(t+i*.5);o.stop(t+i*.5+.55);}}
function knockS(){if(!AC)return;var t=AC.currentTime;for(var i=0;i<3;i++)thump(140,t+i*.2,.28);}
function biteS(){if(!AC)return;var t=AC.currentTime;tone(190,t,.1,.09);tone(120,t+.05,.1,.08);noiseHit(t,.06,900,.06);}
function boingS(){if(!AC)return;var t=AC.currentTime,o=AC.createOscillator(),g=AC.createGain();
o.type='sine';o.frequency.setValueAtTime(120,t);o.frequency.linearRampToValueAtTime(460,t+.18);
o.frequency.linearRampToValueAtTime(200,t+.4);
g.gain.setValueAtTime(.12,t);g.gain.exponentialRampToValueAtTime(.0001,t+.45);
o.connect(g);g.connect(master);o.start(t);o.stop(t+.5);}
function popS(){if(!AC)return;var t=AC.currentTime;noiseHit(t,.08,2500,.12);tone(900,t,.05,.06);}
function padS(){if(!AC)return;tone(520,AC.currentTime,.05,.06,'square');}
function wallS(){if(!AC)return;tone(330,AC.currentTime,.05,.05,'square');}
function scoreS(){if(!AC)return;var t=AC.currentTime;tone(220,t,.15,.07,'square');tone(160,t+.12,.2,.07,'square');}
function sipS(){if(!AC)return;var t=AC.currentTime;tone(300,t,.12,.05);tone(240,t+.1,.14,.04);noiseHit(t,.15,700,.03);}
function flickS(){if(!AC)return;var t=AC.currentTime;noiseHit(t,.05,3000,.06);tone(2400,t,.03,.04,'square');}
function sizzleS(){if(!AC)return;noiseHit(AC.currentTime,.3,3000,.03);}
function whoopeeS(){if(!AC)return;var t=AC.currentTime;
var o=AC.createOscillator(),g=AC.createGain(),f=AC.createBiquadFilter(),l=AC.createOscillator(),lg=AC.createGain();
o.type='sawtooth';o.frequency.setValueAtTime(160,t);o.frequency.linearRampToValueAtTime(55,t+.5);
l.frequency.value=16;lg.gain.value=40;l.connect(lg);lg.connect(o.frequency);
f.type='lowpass';f.frequency.value=400;
g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(.16,t+.05);
g.gain.setValueAtTime(.16,t+.3);g.gain.exponentialRampToValueAtTime(.0001,t+.55);
o.connect(f);f.connect(g);g.connect(master);o.start(t);l.start(t);o.stop(t+.6);l.stop(t+.6);}
function whisperS(){if(!AC)return;var t=AC.currentTime;
noiseHit(t,1.4,2500,.05,true);
var o=AC.createOscillator(),g=AC.createGain();o.type='sine';
o.frequency.setValueAtTime(180,t);o.frequency.linearRampToValueAtTime(90,t+1.3);
g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(.04,t+.6);g.gain.linearRampToValueAtTime(.0001,t+1.4);
o.connect(g);g.connect(master);o.start(t);o.stop(t+1.5);}
function demonS(){if(!AC)return;var t=AC.currentTime;
[55,58.5,110].forEach(function(f){var o=AC.createOscillator(),g=AC.createGain();
o.type='sawtooth';o.frequency.setValueAtTime(f*2,t);o.frequency.exponentialRampToValueAtTime(f,t+.7);
g.gain.setValueAtTime(.0001,t);g.gain.linearRampToValueAtTime(.12,t+.05);g.gain.exponentialRampToValueAtTime(.0001,t+.9);
o.connect(g);g.connect(master);o.start(t);o.stop(t+1);});
noiseHit(t,.5,300,.2);}
function startUfoHum(){if(!AC||ufoHumOsc)return;
var o=AC.createOscillator(),g=AC.createGain(),l=AC.createOscillator(),lg=AC.createGain();
o.type='triangle';o.frequency.value=110;l.frequency.value=5;lg.gain.value=14;
l.connect(lg);lg.connect(o.frequency);g.gain.value=.02;
o.connect(g);g.connect(master);o.start();l.start();ufoHumOsc=o;}
/* ===== shared world objects ===== */
var hlObj=null,hlStore=[];
function setHighlight(o){
if(hlObj===o)return;
clearHighlight();
if(!o)return;
hlObj=o;
o.m.traverse(function(ch){
if(ch.isMesh&&ch.material&&ch.material.emissive&&!SHARED_MATS[ch.material.id]){
hlStore.push({m:ch.material,e:ch.material.emissive.getHex(),i:ch.material.emissiveIntensity});
ch.material.emissive.setHex(0x2f8fff);ch.material.emissiveIntensity=.65;}});}
function clearHighlight(){
for(var i=0;i<hlStore.length;i++){hlStore[i].m.emissive.setHex(hlStore[i].e);hlStore[i].m.emissiveIntensity=hlStore[i].i;}
hlStore=[];hlObj=null;}
function killObj(o,silent){if(!o||o.dead)return;o.dead=true;scene.remove(o.m);
if(o.ix)o.ix.dead=true;
if(hlObj===o)clearHighlight();
if(o.sid){delete worldObjs[o.sid];if(!silent)netPublishEvent({k:'gone',o:o.sid});}}
function findPeerByName(n){for(var k in net.peers)if(net.peers[k].n===n)return net.peers[k];return null;}
function peerHandWorld(p,which,out){
var h=which?p.hr:p.hl;if(!h||h.length<7||!p.avatar)return false;
var g=p.avatar.g,yw=g.rotation.y,cy=Math.cos(yw),sy=Math.sin(yw);
out.set(g.position.x+cy*h[0]+sy*h[2],g.position.y+h[1],g.position.z-sy*h[0]+cy*h[2]);
return true;}
function addPhys(m,r,rest){var o={m:m,r:r,rest:rest,v:new THREE.Vector3(),held:null,
lastP:m.position.clone(),vel:new THREE.Vector3(),dead:false,
closed:false,slice:false,peel:false,bites:0,lastBite:0,size:'medium',fx:null,ix:null,
sid:null,type:null,heldPeer:null,heldHand:0,lastA:0,static:false,born:Date.now()};
physObjs.push(o);return o;}
function holdPos(o,out){var h=o.held;if(!h)return false;
if(h.type==='ctl'){if(!h.src.visible)return false;h.src.getWorldPosition(out);out.y+=.02;return true;}
if(!h.src.userData.active)return false;
var hj=h.src.joints&&h.src.joints['index-finger-tip'];
if(!hj||!hj.visible)return false;
hj.getWorldPosition(out);return true;}
function nearestGrabbable(pos,maxD){var best=null,bd=maxD;
for(var i=0;i<physObjs.length;i++){var o=physObjs[i];
if(o.dead||o.held||o.heldPeer)continue;
var d=o.m.position.distanceTo(pos);
if(d<bd){bd=d;best=o;}}
return best;}
function useHeld(o,src){
if(!o||o.dead)return false;
var t=clock.elapsedTime;
if(o.type==='pizza'){
if(o.closed)openBox(o,false);
else toast('already open \u2014 point at a slice and trigger to grab it.');
return true;}
if(o.type==='slice'){o.lastBite=t;biteSlice(o);return true;}
if(o.type==='ingredient'){eatIngredient(o);return true;}
if(o.type==='dish'){biteDish(o);return true;}
if(o.type==='eight'){
if(t-o.lastA>1.2){o.lastA=t;var ans=pick(ANSWERS);
toast('the 8-ball says: '+ans);thunk();netPublishEvent({k:'eight',a:ans});}
return true;}
if(o.type==='lighter'){
o.flameOn=o.flameOn===false;
flickS();
toast(o.flameOn?'the flame stands up, eager.':'you snap it shut. the dark leans closer.');
if(src&&src.userData)hapticPulse(src.userData.inputSource,.4,50);
return true;}
if(o.type==='duck'){squeak();toast('quack.');return true;}
if(o.type==='rock'){purr();toast(pick(['the rock is doing its best.','the rock believes in you.']));return true;}
if(o.type==='glow'){popS();toast('it hums through another color.');return true;}
return false;}
function biteSlice(o){
if(!o||o.dead)return;
o.bites++;biteS();
o.m.scale.multiplyScalar(.62);
if(o.bites>=3){killObj(o,false);
toast(pick(['delicious.','so worth it.','tony outdid himself.','warm. cheesy. perfect.']));}
else{var left=3-o.bites;toast(left+' bite'+(left>1?'s':'')+' left.')}}
function startGrab(o,type,src){if(o.held||o.dead)return;
if(o.heldPeer){toast(o.heldPeer+' has it right now.');return;}
o.static=false;
o.held={type:type,src:src};src.userData.grabO=o;
o.lastP.copy(o.m.position);o.vel.set(0,0,0);
if(o.fx==='quack'){squeak();toast('quack.');}
if(o.fx==='comfort'){purr();toast(pick(['the rock is doing its best.','the rock believes in you.']));}
if(o.sid)netPublishEvent({k:'grab',o:o.sid,h:(src===ctl1?1:0)});
hapticPulse(type==='ctl'?src.userData.inputSource:null,.3,40);}
function endGrab(src){var o=src.userData.grabO;if(!o)return;
src.userData.grabO=null;
if(o.held&&o.held.src===src){o.held=null;
o.v.copy(o.vel);if(o.v.length()>9)o.v.setLength(9);
if(o.sid)netPublishEvent({k:'drop',o:o.sid,
p:[r2(o.m.position.x),r2(o.m.position.y),r2(o.m.position.z)],
v:[r2(o.v.x),r2(o.v.y),r2(o.v.z)]});}}
function physStep(dt,t){
for(var i=physObjs.length-1;i>=0;i--){var o=physObjs[i];
if(o.dead){physObjs.splice(i,1);continue;}
if(o.heldPeer){var pp=findPeerByName(o.heldPeer);
if(pp&&peerHandWorld(pp,o.heldHand,tp)){o.m.position.copy(tp);o.lastP.copy(tp);o.vel.set(0,0,0);o.v.set(0,0,0);}
else if(!pp)o.heldPeer=null;
continue;}
if(o.held){
if(!holdPos(o,tp)){endGrab(o.held.src);continue;}
o.m.position.copy(tp);
var iv=tv.copy(tp).sub(o.lastP).multiplyScalar(1/Math.max(dt,.001));
o.vel.lerp(iv,.5);o.lastP.copy(tp);
if(o.type==='eight'&&o.vel.length()>2.2&&t-o.lastA>3.5){o.lastA=t;
var ans=pick(ANSWERS);toast('the 8-ball says: '+ans);thunk();
netPublishEvent({k:'eight',a:ans});}
}else if(!o.static){
var grav=(o.type==='plane')?1.4:5;
o.v.y-=grav*dt;
if(o.type==='plane'){o.v.multiplyScalar(Math.max(0,1-.5*dt));
o.m.rotation.x+=dt*2.2;o.m.rotation.z+=dt*1.4;}
o.m.position.x+=o.v.x*dt;o.m.position.y+=o.v.y*dt;o.m.position.z+=o.v.z*dt;
var gnd=groundAt(o.m.position.x,o.m.position.z)+o.r;
if(o.m.position.y<gnd){o.m.position.y=gnd;
if(Math.abs(o.v.y)>.4)o.v.y=-o.v.y*o.rest;else o.v.y=0;
o.v.x*=Math.max(0,1-3*dt);o.v.z*=Math.max(0,1-3*dt);}
pushOut(o.m.position,o.r,doorK<.6?colliders.concat([doorCol]):colliders);
o.m.position.x=clamp(o.m.position.x,-60,60);
o.m.position.z=clamp(o.m.position.z,-2.5,LOB+11);}
if(o.type!=='plane')o.m.rotation.y+=o.v.length()*.02*dt*60*(o.slice?0:1);
if(o.glowMat)o.glowMat.emissive.setHSL((t*.12+(o.glowPh||0))%1,1,.55);
if(o.slice&&!o.dead){
var ed=o.m.position.distanceTo(camPos);
if(o.held&&ed<.17&&t-o.lastBite>.5){o.lastBite=t;biteSlice(o);}}
if(o.peel&&!o.dead){
var pdx=o.m.position.x-rig.position.x,pdz=o.m.position.z-rig.position.z;
if(pdx*pdx+pdz*pdz<.3&&state==='xr'&&!bodyMode){
killObj(o,false);slipT=1;whoosh();squeak();
toast('you slipped. the banana peel is proud of itself.');
hapticPulse(ctl0.userData.inputSource,.6,120);hapticPulse(ctl1.userData.inputSource,.6,120);}}}}
var ANSWERS=['it is certain.','ask again later.','the void says yes.','outlook foggy.','signs point to warm.',
'don\u2019t count on it.','yes, obviously.','the night refuses to answer.','take the lighter. trust me.'];
/* ===== spawnable toys ===== */
function spawnThrowToy(kind,sid){
var m,rest=.3,fx=null,o,fg=null;
if(kind==='duck'){var g=new THREE.Group();
var dm=new THREE.MeshStandardMaterial({color:0xf2c94c,roughness:.5});
var b=new THREE.Mesh(new THREE.SphereGeometry(.07,10,8),dm);b.scale.set(1,.8,1.25);b.castShadow=true;g.add(b);
var h=new THREE.Mesh(new THREE.SphereGeometry(.045,8,8),dm);h.position.set(0,.075,.06);g.add(h);
var bk=new THREE.Mesh(new THREE.ConeGeometry(.016,.045,6),new THREE.MeshStandardMaterial({color:0xe8862c,roughness:.5}));
bk.rotation.x=Math.PI/2;bk.position.set(0,.075,.115);g.add(bk);
var tl=new THREE.Mesh(new THREE.ConeGeometry(.03,.06,6),dm);
tl.rotation.x=-2.2;tl.position.set(0,.05,-.1);g.add(tl);
[-1,1].forEach(function(s){var w=new THREE.Mesh(new THREE.SphereGeometry(.032,6,6),dm);
w.scale.set(.5,1,1.3);w.position.set(s*.06,.01,0);g.add(w);});
[-1,1].forEach(function(s){var e=new THREE.Mesh(new THREE.SphereGeometry(.008,6,6),
new THREE.MeshBasicMaterial({color:0x1a1410}));e.position.set(s*.02,.09,.095);g.add(e);});
g.position.set(3.6,.07,1.8);scene.add(g);m=g;rest=.45;fx='quack';}
else if(kind==='rock'){m=new THREE.Group();
var rb=new THREE.Mesh(new THREE.DodecahedronGeometry(.055),
new THREE.MeshStandardMaterial({color:0x6a6258,roughness:1}));rb.castShadow=true;m.add(rb);
[-1,1].forEach(function(s){var e=new THREE.Mesh(new THREE.SphereGeometry(.011,6,6),
new THREE.MeshBasicMaterial({color:0xf5f5f0}));
var p2=new THREE.Mesh(new THREE.SphereGeometry(.005,6,6),new THREE.MeshBasicMaterial({color:0x111111}));
e.position.set(s*.022,.03,-.045);p2.position.set(s*.022,.03,-.054);m.add(e);m.add(p2);});
m.position.set(-2.08,.85,-1.92);scene.add(m);rest=.25;fx='comfort';}
else if(kind==='soap'){m=new THREE.Mesh(new THREE.BoxGeometry(.07,.025,.05),
new THREE.MeshStandardMaterial({color:0xbfd8c8,roughness:.3}));
m.position.set(3.6,.9,1.0);scene.add(m);rest=.35;}
else if(kind==='ball'){m=new THREE.Mesh(new THREE.SphereGeometry(.16,PERF?10:16,PERF?8:12),
new THREE.MeshStandardMaterial({map:ballTex,roughness:.5}));
m.castShadow=true;m.position.set(0,1.2,.3);scene.add(m);rest=.82;}
else if(kind==='peel'){var pg=new THREE.Group();
var pm=new THREE.MeshStandardMaterial({color:0xe8c84a,roughness:.6,side:THREE.DoubleSide});
var base=new THREE.Mesh(new THREE.CylinderGeometry(.05,.06,.012,8),pm);pg.add(base);
for(var pi=0;pi<3;pi++){var peel=new THREE.Mesh(new THREE.TorusGeometry(.05,.014,5,8,1.9),pm);
peel.rotation.y=pi*(Math.PI*2/3);peel.rotation.x=-.9;peel.position.y=.015;pg.add(peel);}
var stem=new THREE.Mesh(new THREE.CylinderGeometry(.008,.012,.04,5),
new THREE.MeshStandardMaterial({color:0x8a6a2a,roughness:.7}));stem.position.y=.03;pg.add(stem);
pg.position.set(rig.position.x-camDir.x*.6,.02,rig.position.z-camDir.z*.6);
scene.add(pg);m=pg;rest=.05;}
else if(kind==='plane'){var pg2=new THREE.Group();
var paperM=new THREE.MeshStandardMaterial({color:0xf0e8d8,roughness:.9,side:THREE.DoubleSide});
var nose=new THREE.Mesh(new THREE.ConeGeometry(.035,.24,4),paperM);
nose.rotation.x=-Math.PI/2;nose.rotation.y=Math.PI/4;pg2.add(nose);
var wing=new THREE.Mesh(new THREE.BoxGeometry(.26,.004,.1),paperM);wing.position.z=.03;pg2.add(wing);
var fin=new THREE.Mesh(new THREE.BoxGeometry(.004,.05,.08),paperM);fin.position.set(0,.025,.08);pg2.add(fin);
pg2.position.set(0,1.2,.3);scene.add(pg2);m=pg2;rest=.2;}
else if(kind==='glow'){var gm=new THREE.MeshStandardMaterial({color:0x66ffcc,emissive:0x44ffaa,emissiveIntensity:1.8,roughness:.3});
var gg=new THREE.Group();
var bar=new THREE.Mesh(new THREE.CylinderGeometry(.016,.016,.18,8),gm);gg.add(bar);
var cap1=new THREE.Mesh(new THREE.SphereGeometry(.016,6,6),gm);cap1.position.y=.09;gg.add(cap1);
var cap2=cap1.clone();cap2.position.y=-.09;gg.add(cap2);
if(!PERF){var gl=new THREE.PointLight(0x44ffaa,.9,2.2,2);gg.add(gl);}
gg.position.set(0,1.2,.3);scene.add(gg);m=gg;rest=.3;}
else if(kind==='eight'){var eg=new THREE.Group();
var eb=new THREE.Mesh(new THREE.SphereGeometry(.06,12,10),
new THREE.MeshStandardMaterial({color:0x141414,roughness:.25}));eb.castShadow=true;eg.add(eb);
var ec=new THREE.Mesh(new THREE.CircleGeometry(.026,12),new THREE.MeshBasicMaterial({color:0xf5f5f0}));
ec.position.set(0,0,-.055);ec.rotation.y=Math.PI;eg.add(ec);
eg.position.set(0,1.2,.3);scene.add(eg);m=eg;rest=.35;}
else if(kind==='lighter'){var lg=new THREE.Group();
var lb=new THREE.Mesh(new THREE.BoxGeometry(.028,.062,.016),
new THREE.MeshStandardMaterial({color:0x8a2020,metalness:.6,roughness:.35}));
lb.position.y=.031;lb.castShadow=true;lg.add(lb);
var lt=new THREE.Mesh(new THREE.CylinderGeometry(.014,.014,.012,8),
new THREE.MeshStandardMaterial({color:0xc8c0b0,metalness:.8,roughness:.3}));
lt.position.y=.068;lg.add(lt);
fg=new THREE.Group();fg.position.y=.082;lg.add(fg);
var fl=new THREE.Mesh(new THREE.ConeGeometry(.011,.035,6),new THREE.MeshBasicMaterial({color:0xffc86a}));
fl.material.toneMapped=false;fl.position.y=.015;fg.add(fl);
var fl2=new THREE.Mesh(new THREE.ConeGeometry(.006,.02,6),new THREE.MeshBasicMaterial({color:0x8ac0ff}));
fl2.material.toneMapped=false;fl2.position.y=.008;fg.add(fl2);
lg.position.set(-2.28,.8,-2.02);scene.add(lg);m=lg;rest=.3;}
o=addPhys(m,kind==='ball'?.16:(kind==='peel'?.06:.07),rest);
if(fx)o.fx=fx;
if(kind==='peel')o.peel=true;
o.type=kind;
if(kind==='glow'){o.glowMat=m.children[0].material;o.glowPh=Math.random();}
if(kind==='lighter'&&fg){o.flameG=fg;o.flameOn=true;}
if(sid){o.sid=sid;worldObjs[sid]=o;}
var ix=addI(m,function(){return o.heldPeer?o.heldPeer+' has it':'grab the '+kind;},null);
ix.grab=true;ix.obj=o;o.ix=ix;
return o;}
function spawnSharedObj(t,id,sz){
if(t==='pizza')spawnPizzaBox(sz||'medium',id,true);
else spawnThrowToy(t,id);}
/* ===== confetti & fireworks ===== */
var confetti=[],CONF_COLS=[0xff6a6a,0xffd166,0x6ad1ff,0x8aff8a,0xff8ae0,0xffa05e];
function burst(origin,n,spread,up,add){
n=PERF?Math.ceil(n*.6):n;
var p=new Float32Array(n*3),c=new Float32Array(n*3),vel=[],col=new THREE.Color();
for(var i=0;i<n;i++){p[i*3]=origin.x;p[i*3+1]=origin.y;p[i*3+2]=origin.z;
var th=Math.random()*6.283,ph=Math.acos(rand(-1,1)),sp=rand(.4,spread);
vel.push(new THREE.Vector3(Math.sin(ph)*Math.cos(th)*sp,Math.abs(Math.cos(ph))*sp*up+rand(.3,1.2),Math.sin(ph)*Math.sin(th)*sp));
col.set(CONF_COLS[i%CONF_COLS.length]);c[i*3]=col.r;c[i*3+1]=col.g;c[i*3+2]=col.b;}
var g=new THREE.BufferGeometry();
g.setAttribute('position',new THREE.BufferAttribute(p,3));
g.setAttribute('color',new THREE.BufferAttribute(c,3));
var pts=new THREE.Points(g,new THREE.PointsMaterial({size:.035,vertexColors:true,transparent:true,opacity:1,
depthWrite:false,blending:add?THREE.AdditiveBlending:THREE.NormalBlending}));
scene.add(pts);confetti.push({pts:pts,vel:vel,t:0,n:n,life:add?1.7:2.6});}
function stepConfetti(dt){
for(var i=confetti.length-1;i>=0;i--){var c=confetti[i];c.t+=dt;
var p=c.pts.geometry.attributes.position.array;
for(var j=0;j<c.n;j++){c.vel[j].y-=2.6*dt;
p[j*3]+=c.vel[j].x*dt;p[j*3+1]+=c.vel[j].y*dt;p[j*3+2]+=c.vel[j].z*dt;}
c.pts.geometry.attributes.position.needsUpdate=true;
c.pts.material.opacity=Math.max(0,1-c.t/c.life);
if(c.t>c.life){scene.remove(c.pts);c.pts.geometry.dispose();c.pts.material.dispose();confetti.splice(i,1);}}}
var fwQueue=[],fwLight=new THREE.PointLight(0xffc87a,0,20,2);scene.add(fwLight);
/* ===== pizza phone ===== */
var PX=-1.12,PZ=-2.26,PIZZA_NUM='5557499';
var phone={num:'',state:'idle',ringT:0,menuT:0,wrongT:0};
var phoneCv=document.createElement('canvas');phoneCv.width=256;phoneCv.height=160;
var phoneCtx=phoneCv.getContext('2d');
var phoneTex=new THREE.CanvasTexture(phoneCv);phoneTex.encoding=THREE.sRGBEncoding;
(function(){
box(.2,.06,.24,M.darkwood,PX,.8,PZ+.03);
var scr=new THREE.Mesh(new THREE.PlaneGeometry(.17,.1),new THREE.MeshBasicMaterial({map:phoneTex,fog:false}));
scr.material.toneMapped=false;
scr.position.set(PX,.9,PZ-.085);scr.rotation.x=-.5;scene.add(scr);
var keys=['1','2','3','4','5','6','7','8','9','*','0','#'];
keys.forEach(function(k,i){var col=i%3,row=(i/3)|0;
var kx=PX+(col-1)*.037,kz=PZ+.02+row*.037;
var mat=new THREE.MeshStandardMaterial({color:k==='*'?0xb03030:(k==='#'?0x2a8a50:0x241a12),roughness:.5});
var b=new THREE.Mesh(new THREE.CylinderGeometry(.013,.013,.012,8),mat);
b.position.set(kx,.836,kz);scene.add(b);
addI(proxyS(.024,kx,.87,kz),'press '+k,function(){phoneKey(k);});});})();
var orderDefs=[['small',5,-.13],['medium',8,0],['large',12,.13]];
var orderBtns=[];
orderDefs.forEach(function(od,i){
var bg=new THREE.Group();bg.position.set(PX+od[2],.93,PZ+.02);bg.visible=false;scene.add(bg);
var bm=new THREE.MeshStandardMaterial({color:0x14532d,emissive:0x34d399,emissiveIntensity:1.3,roughness:.35});
var b=new THREE.Mesh(new THREE.CylinderGeometry(.042,.05,.028,12),bm);bg.add(b);
var top=new THREE.Mesh(new THREE.CylinderGeometry(.036,.036,.006,12),
new THREE.MeshStandardMaterial({color:0x6ee7b7,emissive:0x6ee7b7,emissiveIntensity:1.6}));
top.position.y=.017;bg.add(top);
glowChild(bg,0x5fe88a,.17,.03,.75);
orderBtns.push({g:bg,mat:bm,ph:i*2.1});
var ix=addI(proxyS(.065,PX+od[2],.99,PZ+.02),'order '+od[0]+' pizza  \u25C8'+od[1]+'  (shared wallet)',
function(){orderPizza(od[0],od[1]);});
ix.enabled=false;
orderBtns[i].ix=ix;});
function fmtNum(n){return n.length>3?n.slice(0,3)+'-'+n.slice(3):n;}
function drawPhone(){var x=phoneCtx;
x.fillStyle='#0c2415';x.fillRect(0,0,256,160);
x.strokeStyle='#1e4a2c';x.lineWidth=6;x.strokeRect(3,3,250,154);
x.fillStyle='#9fe8a0';x.textAlign='center';x.textBaseline='alphabetic';
x.font='700 32px "Space Grotesk",monospace';
x.fillText(fmtNum(phone.num)||'- - -',128,56);
x.font='16px "Space Grotesk"';
var st='dial: 555-7499 \u00B7 911 \u00B7 666';
if(phone.state==='ring')st='ringing\u2026';
else if(phone.state==='menu')st='press a GLOWING button';
else if(phone.state==='ordered'&&shared.pizza.st==='ordered'){
var s=Math.max(0,Math.ceil((shared.pizza.due-Date.now())/1000));
st='one '+shared.pizza.size+' \u00B7 here in 0:'+(s<10?'0':'')+s;}
else if(phone.state==='wrong')st='the line clicks dead.';
x.fillText(st,128,96);
x.fillStyle='#4a8a5c';x.font='14px "Space Grotesk"';
x.fillText('* clear      # call',128,132);
phoneTex.needsUpdate=true;}
function phoneKey(k){
if(k==='*'){phone.num='';if(phone.state==='menu')hangUp('you hung up. tony waits.');else phone.state='idle';clickSound();return;}
if(k==='#'){
if(phone.state!=='idle')return;
var num=phone.num;
if(num===PIZZA_NUM||num==='7499'){phone.state='ring';phone.ringT=2.6;ringWarble();thunk();}
else if(num==='911'){sirenS();phone.state='wrong';phone.wrongT=3.5;
toast(pick(['911. what\u2019s your emergency? \u2026the building? \u2026we\u2019ll send someone up. they never arrive.',
'911. please remain calm. the staircase is not an emergency. the staircase is a lifestyle.',
'dispatch: all units are at the lobby. the lobby does not exist. over.']));}
else if(num==='666'){whisperS();warned4=true;phone.state='wrong';phone.wrongT=3.5;
toast('you have reached the basement. we have been expecting you. the lighter. remember the lighter.');}
else if(num==='5550199'||num==='0199'){thunk();phone.state='wrong';phone.wrongT=3.5;
toast('front desk. the lobby is at the bottom. yes, the stairs. no, the elevator has been out of service since 1987.');}
else if(num==='5552537'){thunk();phone.state='wrong';phone.wrongT=3.5;
toast('maintenance. the elevator has been out of service since 1987. please use the stairs. please do not count the floors.');}
else if(num==='5551234'){thunk();phone.state='wrong';phone.wrongT=3.5;
toast('you\u2019ve reached tony. tony is not available. tony is always available. tony is a lie.');}
else if(num==='0'){clickSound();phone.state='wrong';phone.wrongT=3.5;
toast('operator. there is no outside line. there is no inside line. there is only the building.');}
else{phone.state='wrong';phone.wrongT=3;thunk();
toast(pick(['the line crackles. wrong number.','a fax machine screams at you. wrong number.','static, then a jingle for a mattress store. wrong number.','\u201Cwe\u2019re closed. forever.\u201D click.']));}
return;}
if(phone.state==='ring'||phone.state==='ordered')return;
phone.state='idle';
if(phone.num.length<7){phone.num+=k;dtmfBeep(k);}}
function hangUp(msg){phone.state='idle';phone.num='';
orderBtns.forEach(function(b){b.ix.enabled=false;b.g.visible=false;});
if(msg)toast(msg);}
function orderPizza(size,price){
if(phone.state!=='menu')return;
if(shared.money<price){toast('the shared wallet is short \u25C8'+price+'. wait for the room to earn.');return;}
netPublishEvent({k:'req',a:'pizza',size:size,price:price});
toast('order sent to the night\u2026');thunk();}
function spawnPizzaBox(size,oid,silent){
var g=new THREE.Group();
var bot=new THREE.Mesh(new THREE.BoxGeometry(.34,.05,.34),new THREE.MeshStandardMaterial({color:0xe8e0d0,roughness:.8}));
bot.position.y=.025;bot.castShadow=true;g.add(bot);
var lidG=new THREE.Group();lidG.position.set(0,.05,-.17);lidG.rotation.x=0;g.add(lidG);
var lid=new THREE.Mesh(new THREE.BoxGeometry(.34,.02,.34),new THREE.MeshStandardMaterial({color:0xc03030,roughness:.7}));
lid.position.set(0,.01,.17);lid.castShadow=true;lidG.add(lid);
var logo=new THREE.Mesh(new THREE.CylinderGeometry(.07,.07,.004,12),new THREE.MeshStandardMaterial({color:0xf2c14e,roughness:.6}));
logo.position.set(0,.022,.17);lidG.add(logo);
g.position.set(-1.5,.05,3.6);scene.add(g);
var o=addPhys(g,.17,.12);o.closed=true;o.lidG=lidG;o.size=size;o.type='pizza';
if(oid){o.sid=oid;worldObjs[oid]=o;}
var ix=addI(g,function(){return o.heldPeer?o.heldPeer+' has the pizza':(o.closed?'use trigger to open the pizza':'grab the pizza box');},null);
ix.grab=true;ix.obj=o;o.ix=ix;
if(!silent)knockS();
return o;}
function openBox(o,silent){
if(!o||o.dead||!o.closed)return;
o.closed=false;if(o.lidG)o.lidG.rotation.x=-2.2;
popS();thunk();
toast('steam rises. it\u2019s still warm.');
if(!silent&&o.sid)netPublishEvent({k:'open',o:o.sid});
var n=o.size==='small'?4:(o.size==='medium'?6:8);
for(var i=0;i<n;i++){
var sg=new THREE.Group();
var wedge=new THREE.Mesh(new THREE.CylinderGeometry(.095,.095,.02,5,1,false,0,1.05),
new THREE.MeshStandardMaterial({color:0xe8b04a,roughness:.6}));
sg.add(wedge);
for(var pp=0;pp<3;pp++){var pep=new THREE.Mesh(new THREE.CylinderGeometry(.013,.013,.006,6),
new THREE.MeshStandardMaterial({color:0xb03030,roughness:.5}));
pep.position.set(rand(.02,.06),.012,rand(.01,.06));sg.add(pep);}
var a=i/n*Math.PI*2+rand(-.2,.2);
sg.position.set(o.m.position.x+Math.cos(a)*.09,o.m.position.y+.07,o.m.position.z+Math.sin(a)*.09);
scene.add(sg);
var so=addPhys(sg,.045,.05);so.slice=true;so.type='slice';
var sid2=o.sid?(o.sid+'_s'+i):null;
if(sid2){so.sid=sid2;worldObjs[sid2]=so;}
var six=addI(sg,function(){return 'grab a slice \u00B7 trigger = bite (3 bites)';},null);
six.grab=true;six.obj=so;so.ix=six;}}
function phoneTick(dt){
if(phone.state==='ring'){phone.ringT-=dt;
if(phone.ringT<=0){phone.state='menu';phone.menuT=25;
orderBtns.forEach(function(b){b.ix.enabled=true;b.g.visible=true;});
toast('tony\u2019s void pizza. the buttons are glowing. literally.');}}
else if(phone.state==='menu'){phone.menuT-=dt;if(phone.menuT<=0)hangUp('tony hung up. try again.');}
else if(phone.state==='ordered'){if(shared.pizza.st!=='ordered'){phone.state='idle';phone.num='';}}
else if(phone.state==='wrong'){phone.wrongT-=dt;if(phone.wrongT<=0)phone.state='idle';}}
/* ===== shop ===== */
var ITEMS=[
{id:'sprint',name:'worn sneakers',price:8,desc:'walk 40% faster. it helps with everything.'},
{id:'boots',name:'squeaky boots',price:9,desc:'+10% speed. -100% dignity. squeak squeak.'},
{id:'nose',name:'clown nose',price:4,desc:'everyone will see it. everyone.'},
{id:'hat',name:'party hat',price:5,desc:'it is someone\u2019s birthday. it is always someone\u2019s birthday.'},
{id:'duck',name:'rubber duck',price:6,desc:'grip grabs it. trigger makes it quack.',inst:1,t:'duck'},
{id:'rock',name:'support rock',price:5,desc:'throwable. it has eyes now.',inst:1,t:'rock'},
{id:'soap',name:'bar of soap',price:3,desc:'throwable. for the sink.',inst:1,t:'soap'},
{id:'banana',name:'banana peel',price:7,desc:'drops behind you. whoever steps on it learns humility.',inst:1,t:'peel'},
{id:'horn',name:'air horn',price:12,desc:'stuns the room. tasteful.'},
{id:'bell',name:'dinner bell',price:10,desc:'ring it when the pizza lands.'},
{id:'cat',name:'cat breakfast',price:30,desc:'wake the cat. +1 \u25C8/min to the shared wallet, forever.'},
{id:'gold',name:'golden bulb',price:18,desc:'the desk lamp burns gold. fancy.'},
{id:'radio',name:'night radio',price:10,desc:'lofi blips from the void. toggle on the desk.'},
{id:'ball',name:'beach ball',price:8,desc:'a very bouncy ball, for the whole room.',inst:1,t:'ball'},
{id:'confetti',name:'confetti cannon',price:10,desc:'on the desk. press trigger. party.'},
{id:'fireworks',name:'fireworks',price:14,desc:'launch a show past the window. ooh. aah.'},
{id:'disco',name:'disco ball',price:22,desc:'hangs from the ceiling. spins. thumps.'},
{id:'boombox',name:'boombox',price:15,desc:'floor lofi. toggle to vibe.'},
{id:'balloons',name:'balloon bundle',price:6,desc:'five balloons by the bed. they bob.'},
{id:'tramp',name:'trampoline',price:25,desc:'it appears in the room. step on. boing. (feet stay on the floor.)'},
{id:'lighter',name:'the lighter',price:14,desc:'floor 5 and below belong to it. keep it LIT and close, or the dark takes you.',inst:1,t:'lighter'},
{id:'console',name:'game console',price:28,desc:'console + crt beside the pc. VOID PONG. trigger serves.'},
{id:'plane',name:'paper plane',price:6,desc:'throwable. it glides, sort of.',inst:1,t:'plane'},
{id:'cocoa',name:'cocoa mug',price:7,desc:'steaming on the desk. sip it. +warmth, not money.'},
{id:'glowstick',name:'glow stick',price:9,desc:'throwable light. cycles colors.',inst:1,t:'glow'},
{id:'whoopee',name:'whoopee cushion',price:8,desc:'on the chair. sit down. everyone hears.'},
{id:'eight',name:'magic 8-ball',price:10,desc:'trigger while holding shakes it.',inst:1,t:'eight'},
{id:'ufo',name:'ufo drone',price:18,desc:'patrols the room. hums. judges.'}];
function findItem(id){for(var i=0;i<ITEMS.length;i++)if(ITEMS[i].id===id)return ITEMS[i];return null;}
var shopOpen=false,shopIdx=0,bellCd=0,hornCd=0,confCd=0,fwCd=0;
var radioMesh=null,bellMesh=null,bowlMesh=null,hornMesh=null,ownNose=null,ownHat=null,
confMesh=null,fwMesh=null,discoG=null,discoOn=false,discoL1=null,discoL2=null,discoT=0,
boomG=null,boomOn=false,boomT=0,trampG=null,trampK=0,trampLast=0,balloonG=null,
consoleG=null,tvG=null,tvOn=false,lastTV=0,tvLight=null,
whoopeeG=null,cocoaG=null,steamSprs=[],ufoG=null,ufoLamps=[],ufoLight=null,
shopBtnPrev=null,shopBtnBuy=null,shopBtnNext=null,buyBtnMat=null;
var tvLedMat=new THREE.MeshBasicMaterial({color:0x552222,fog:false});tvLedMat.toneMapped=false;
var tvCv=document.createElement('canvas');tvCv.width=256;tvCv.height=192;
var tvCtx=tvCv.getContext('2d');
var tvTex=new THREE.CanvasTexture(tvCv);tvTex.encoding=THREE.sRGBEncoding;
var pong={st:'title',py:.5,ay:.5,bx:.5,by:.5,vx:0,vy:0,ps:0,as:0};
/* buttons now perch on TOP of the monitor */
function makeShopBtn(x,y,z,r,color,emissive){
var g=new THREE.Group();g.position.set(x,y,z);scene.add(g);
var bm=new THREE.MeshStandardMaterial({color:color,emissive:emissive,emissiveIntensity:.7,roughness:.4});
var b=new THREE.Mesh(new THREE.CylinderGeometry(r,r*1.15,.03,14),bm);g.add(b);
var top=new THREE.Mesh(new THREE.CylinderGeometry(r*.8,r*.8,.008,14),
new THREE.MeshStandardMaterial({color:emissive,emissive:emissive,emissiveIntensity:1.2}));
top.position.y=.018;g.add(top);
glowChild(g,emissive,r*3.4,.02,.5);
return {g:g,mat:bm};}
(function(){
radioMesh=new THREE.Group();
radioMesh.add(new THREE.Mesh(new THREE.BoxGeometry(.16,.1,.08),M.darkwood));
radioMesh.position.set(-2.4,.82,-2.08);radioMesh.visible=false;scene.add(radioMesh);
bellMesh=new THREE.Group();
bellMesh.add(new THREE.Mesh(new THREE.ConeGeometry(.045,.07,8),
new THREE.MeshStandardMaterial({color:0xc8a050,metalness:.7,roughness:.3})));
bellMesh.position.set(-2.22,.82,-2.02);bellMesh.visible=false;scene.add(bellMesh);
bowlMesh=cyl(.07,.05,.04,new THREE.MeshStandardMaterial({color:0x9a4a34,roughness:.6}),.55,.02,.55);
bowlMesh.visible=false;
hornMesh=new THREE.Mesh(new THREE.CylinderGeometry(.02,.035,.12,8),
new THREE.MeshStandardMaterial({color:0xc03030,roughness:.4}));
hornMesh.position.set(-1.45,.83,-1.92);hornMesh.visible=false;scene.add(hornMesh);
confMesh=new THREE.Mesh(new THREE.BoxGeometry(.06,.09,.06),
new THREE.MeshStandardMaterial({color:0xd06030,roughness:.5}));
confMesh.position.set(-1.3,.845,-2.1);confMesh.visible=false;scene.add(confMesh);
fwMesh=new THREE.Mesh(new THREE.BoxGeometry(.09,.06,.09),
new THREE.MeshStandardMaterial({color:0x3050a0,roughness:.5}));
fwMesh.position.set(-1.0,.83,-2.0);fwMesh.visible=false;scene.add(fwMesh);
discoG=new THREE.Group();discoG.position.set(1.0,2.5,-.6);discoG.visible=false;scene.add(discoG);
var db=new THREE.Mesh(new THREE.SphereGeometry(.16,10,10),
new THREE.MeshStandardMaterial({color:0xcfd4dd,metalness:1,roughness:.15}));
discoG.add(db);
var dcord=new THREE.Mesh(new THREE.CylinderGeometry(.006,.006,.4,6),M.metal);dcord.position.y=.3;discoG.add(dcord);
discoL1=new THREE.PointLight(0xff6a6a,0,6,2);discoL1.position.set(1.0,2.3,-.6);scene.add(discoL1);
discoL2=new THREE.PointLight(0x6ad1ff,0,6,2);discoL2.position.set(1.0,2.3,-.6);scene.add(discoL2);
boomG=new THREE.Group();boomG.position.set(-.85,.14,1.15);boomG.visible=false;scene.add(boomG);
var bb=new THREE.Mesh(new THREE.BoxGeometry(.4,.24,.16),M.darkwood);boomG.add(bb);
[-1,1].forEach(function(s){var sp=new THREE.Mesh(new THREE.CylinderGeometry(.07,.07,.02,10),M.metal);
sp.rotation.x=Math.PI/2;sp.position.set(s*.12,0,.085);boomG.add(sp);});
trampG=new THREE.Group();trampG.position.set(-2.45,0,-.5);trampG.visible=false;scene.add(trampG);
var tp2=new THREE.Mesh(new THREE.CylinderGeometry(.55,.55,.08,PERF?12:20),
new THREE.MeshStandardMaterial({color:0x14141a,roughness:.9}));tp2.position.y=.04;trampG.add(tp2);
var rim=new THREE.Mesh(new THREE.TorusGeometry(.55,.05,6,PERF?14:24),
new THREE.MeshStandardMaterial({color:0x3070d0,roughness:.5}));
rim.rotation.x=Math.PI/2;rim.position.y=.08;trampG.add(rim);
balloonG=new THREE.Group();balloonG.position.set(1.45,0,-.4);balloonG.visible=false;scene.add(balloonG);
var bcols=[0xff6a6a,0xffd166,0x6ad1ff,0x8aff8a,0xff8ae0];
for(var bi=0;bi<5;bi++){var bg2=new THREE.Group();
var bl=new THREE.Mesh(new THREE.SphereGeometry(.09,8,8),
new THREE.MeshStandardMaterial({color:bcols[bi],roughness:.4}));
bl.scale.set(1,1.2,1);bl.position.y=1.55+Math.random()*.4;bg2.add(bl);
var str=new THREE.Mesh(new THREE.CylinderGeometry(.003,.003,bl.position.y-.1,4),
new THREE.MeshStandardMaterial({color:0x888070,roughness:1}));
str.position.y=(bl.position.y-.1)/2;bg2.add(str);
bg2.position.set(rand(-.15,.15),0,rand(-.15,.15));
bg2.userData={ph:Math.random()*9,baseY:bl.position.y,bl:bl};
balloonG.add(bg2);}
ownNose=new THREE.Mesh(new THREE.SphereGeometry(.035,8,8),
new THREE.MeshStandardMaterial({color:0xd03030,roughness:.4}));
ownNose.position.set(0,-.13,-.3);ownNose.visible=false;camera.add(ownNose);
ownHat=new THREE.Group();ownHat.position.set(0,.05,-.18);ownHat.rotation.x=.35;ownHat.visible=false;camera.add(ownHat);
var cone=new THREE.Mesh(new THREE.ConeGeometry(.09,.24,8),new THREE.MeshStandardMaterial({color:0xff9a3c,roughness:.5}));
cone.position.y=.12;ownHat.add(cone);
var pom=new THREE.Mesh(new THREE.SphereGeometry(.03,6,6),new THREE.MeshStandardMaterial({color:0xfff0cf,roughness:.6}));
pom.position.y=.26;ownHat.add(pom);
/* prev / buy / next — sitting on top of the monitor */
shopBtnPrev=makeShopBtn(-1.98,1.41,-2.42,.032,0x3a3128,0xcbb98f);
shopBtnBuy=makeShopBtn(-1.75,1.41,-2.42,.05,0x7a4a1c,0xffb45e);
shopBtnNext=makeShopBtn(-1.52,1.41,-2.42,.032,0x3a3128,0xcbb98f);
buyBtnMat=shopBtnBuy.mat;
})();
function buildConsoleSet(){if(consoleG)return;
consoleG=new THREE.Group();consoleG.position.set(-1.15,.77,-2.12);scene.add(consoleG);
var cb=new THREE.Mesh(new THREE.BoxGeometry(.24,.06,.16),
new THREE.MeshStandardMaterial({color:0x232028,roughness:.5}));cb.position.y=.03;cb.castShadow=true;consoleG.add(cb);
var slot=new THREE.Mesh(new THREE.BoxGeometry(.14,.008,.02),new THREE.MeshBasicMaterial({color:0x0a0a0e}));
slot.position.set(0,.062,-.02);consoleG.add(slot);
var led=new THREE.Mesh(new THREE.SphereGeometry(.008,6,6),tvLedMat);led.position.set(.09,.062,.06);consoleG.add(led);
[-1,1].forEach(function(s){var pad=new THREE.Mesh(new THREE.BoxGeometry(.07,.015,.05),
new THREE.MeshStandardMaterial({color:0x2e2a34,roughness:.6}));pad.position.set(s*.17,.008,.1);consoleG.add(pad);});
tvG=new THREE.Group();tvG.position.set(-1.15,.77,-2.44);scene.add(tvG);
var shell=new THREE.Mesh(new THREE.BoxGeometry(.36,.28,.14),
new THREE.MeshStandardMaterial({color:0x3a332c,roughness:.7}));shell.position.y=.17;shell.castShadow=true;tvG.add(shell);
var tvScreenMat=new THREE.MeshBasicMaterial({map:tvTex,fog:false});tvScreenMat.toneMapped=false;
var scr=new THREE.Mesh(new THREE.PlaneGeometry(.27,.2),tvScreenMat);scr.position.set(0,.17,.071);tvG.add(scr);
[-1,1].forEach(function(s){var k=new THREE.Mesh(new THREE.CylinderGeometry(.014,.014,.012,8),M.metal);
k.rotation.x=Math.PI/2;k.position.set(s*.13,.05,.071);tvG.add(k);});
[-1,1].forEach(function(s){var a=new THREE.Mesh(new THREE.CylinderGeometry(.004,.004,.24,4),M.metal);
a.position.set(s*.07,.42,0);a.rotation.z=s*.5;tvG.add(a);});
tvLight=new THREE.PointLight(0x9fe870,0,2.2,2);tvLight.position.set(-1.15,1.0,-2.2);scene.add(tvLight);
addI(proxyS(.2,-1.15,.86,-2.12),function(){return tvOn?'turn the console off':'turn the console on';},
function(){toggleTV();});
addI(proxy(.32,.24,.06,-1.15,.95,-2.38),function(){
return tvOn?(pong.st==='play'?'watch void pong':'serve — pull trigger'):'the crt is dark';},
function(){if(!tvOn){toast('turn the console on first. the void pong awaits.');return;}
if(pong.st!=='play')serveBall();});
drawTV();}
function toggleTV(){tvOn=!tvOn;clickSound();
tvLedMat.color.set(tvOn?0x66ff88:0x552222);
if(tvOn){pong.st='title';pong.ps=0;pong.as=0;
toast('VOID PONG boots up. left stick (or left hand height) moves. trigger serves. First to 7.');}
else toast('the crt clicks off. silence settles back in.');}
function serveBall(){pong.st='play';pong.bx=.5;pong.by=.5;
pong.vx=(Math.random()<.5?-1:1)*.38;pong.vy=rand(-.25,.25);
pong.ay=.5;if(AC)tone(440,AC.currentTime,.06,.05,'square');}
function pongStep(dt){
if(pong.st!=='play')return;
pong.bx+=pong.vx*dt;pong.by+=pong.vy*dt;
if(pong.by<.06){pong.by=.06;pong.vy=Math.abs(pong.vy);wallS();}
if(pong.by>.94){pong.by=.94;pong.vy=-Math.abs(pong.vy);wallS();}
if(pong.vx<0&&pong.bx<.11&&pong.bx>.05&&Math.abs(pong.by-pong.py)<.14){
pong.vx=Math.min(.95,Math.abs(pong.vx)*1.07);pong.vy+=(pong.by-pong.py)*2.4;pong.bx=.11;padS();}
if(pong.vx>0&&pong.bx>.89&&pong.bx<.95&&Math.abs(pong.by-pong.ay)<.14){
pong.vx=-Math.min(.95,Math.abs(pong.vx)*1.07);pong.vy+=(pong.by-pong.ay)*2.4;pong.bx=.89;padS();}
pong.ay+=clamp(pong.by-pong.ay,-dt*.42,dt*.42);
if(pong.bx<0){pong.as++;scoreS();if(pong.as>=7)pong.st='win';else serveBall();}
else if(pong.bx>1){pong.ps++;scoreS();if(pong.ps>=7)pong.st='win';else serveBall();}}
function drawTV(){var x=tvCtx,W=256,H=192,i;
x.fillStyle='#05080a';x.fillRect(0,0,W,H);
if(!tvOn){x.fillStyle='rgba(140,180,200,.06)';x.fillRect(28,34,90,5);x.fillRect(60,52,50,4);
tvTex.needsUpdate=true;return;}
x.fillStyle='#08140a';x.fillRect(0,0,W,H);
x.fillStyle='#8fe86a';
x.font='700 34px "Space Grotesk",monospace';x.textAlign='center';x.textBaseline='alphabetic';
x.fillText(pong.ps,86,46);x.fillText(pong.as,170,46);
for(i=0;i<8;i++)x.fillRect(126,16+i*22,4,12);
if(pong.st==='title'){x.font='700 30px "Space Grotesk"';x.fillText('VOID PONG',128,92);
x.font='14px "Space Grotesk"';x.fillText('first to 7 beats the void',128,120);
if(Math.sin(clock.elapsedTime*4)>-.3)x.fillText('trigger to serve',128,146);}
else if(pong.st==='win'){x.font='700 22px "Space Grotesk"';
x.fillText(pong.ps>pong.as?'YOU BEAT THE VOID':'THE VOID WINS',128,92);
x.font='14px "Space Grotesk"';x.fillText('trigger to rematch',128,124);}
else{var ph=.11;
x.fillRect(.055*W,(pong.py-ph)*H,8,ph*2*H);
x.fillRect(.945*W-8,(pong.ay-ph)*H,8,ph*2*H);
x.beginPath();x.arc(pong.bx*W,pong.by*H,6,0,7);x.fill();}
x.fillStyle='rgba(0,0,0,.16)';for(i=0;i<H;i+=4)x.fillRect(0,i,W,1);
tvTex.needsUpdate=true;}
function buildWhoopee(){if(whoopeeG)return;
whoopeeG=new THREE.Group();whoopeeG.position.set(-1.72,.53,-1.42);whoopeeG.rotation.y=.25;scene.add(whoopeeG);
var wm=new THREE.MeshStandardMaterial({color:0xb03030,roughness:.6});
var cush=new THREE.Mesh(new THREE.CylinderGeometry(.12,.15,.035,14),wm);cush.scale.z=.72;whoopeeG.add(cush);
var noz=new THREE.Mesh(new THREE.CylinderGeometry(.014,.02,.05,6),wm);
noz.rotation.x=Math.PI/2;noz.position.set(0,.005,.13);whoopeeG.add(noz);}
function buildCocoa(){if(cocoaG)return;
cocoaG=new THREE.Group();cocoaG.position.set(-2.15,.77,-1.95);scene.add(cocoaG);
var mm=new THREE.MeshStandardMaterial({color:0xb0563a,roughness:.5});
var mug=new THREE.Mesh(new THREE.CylinderGeometry(.045,.04,.09,12),mm);mug.position.y=.045;mug.castShadow=true;cocoaG.add(mug);
var top=new THREE.Mesh(new THREE.CylinderGeometry(.041,.041,.008,12),
new THREE.MeshStandardMaterial({color:0x3a2317,roughness:.4}));top.position.y=.086;cocoaG.add(top);
var hd=new THREE.Mesh(new THREE.TorusGeometry(.026,.008,6,12),mm);
hd.position.set(.052,.05,0);hd.rotation.y=Math.PI/2;cocoaG.add(hd);
for(var i=0;i<3;i++){var s=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:0xf5e8d8,
transparent:true,opacity:0,depthWrite:false}));s.userData.ph=i/3;cocoaG.add(s);steamSprs.push(s);}
addI(proxyS(.09,-2.15,.9,-1.95),'sip the cocoa',function(){sipS();
toast(pick(['warm all the way down.','the cocoa understands you.','+10 warmth. not money. warmth.']));});}
function buildUfo(){if(ufoG)return;
ufoG=new THREE.Group();scene.add(ufoG);
var disc=new THREE.Mesh(new THREE.SphereGeometry(.16,14,10),
new THREE.MeshStandardMaterial({color:0x8a92a8,metalness:.8,roughness:.3}));
disc.scale.y=.3;disc.castShadow=true;ufoG.add(disc);
var dome=new THREE.Mesh(new THREE.SphereGeometry(.08,10,8),
new THREE.MeshStandardMaterial({color:0x9fd8e8,transparent:true,opacity:.7,roughness:.1}));
dome.position.y=.05;ufoG.add(dome);
for(var i=0;i<3;i++){var lm=new THREE.MeshBasicMaterial({fog:false});lm.toneMapped=false;
var l=new THREE.Mesh(new THREE.SphereGeometry(.015,6,6),lm);
var a=i/3*Math.PI*2;l.position.set(Math.cos(a)*.13,-.02,Math.sin(a)*.13);ufoG.add(l);
ufoLamps.push({m:lm,ph:i});}
glowChild(ufoG,0x66ffd8,.5,-.06,.5);
if(!PERF){ufoLight=new THREE.PointLight(0x66ffd8,.8,3,2);ufoG.add(ufoLight);}
if(AC)startUfoHum();}
function applyOwned(){
var o=shared.owned;
if(radioMesh)radioMesh.visible=!!o.radio;
if(bellMesh)bellMesh.visible=!!o.bell;
if(bowlMesh)bowlMesh.visible=!!o.cat;
if(lampBulbMat&&o.gold)lampBulbMat.color.set(0xffd08a);
if(hornMesh)hornMesh.visible=!!o.horn;
if(confMesh)confMesh.visible=!!o.confetti;
if(fwMesh)fwMesh.visible=!!o.fireworks;
if(discoG)discoG.visible=!!o.disco;
if(boomG)boomG.visible=!!o.boombox;
if(trampG)trampG.visible=!!o.tramp;
if(balloonG)balloonG.visible=!!o.balloons;
if(ownNose)ownNose.visible=!!o.nose;
if(ownHat)ownHat.visible=!!o.hat;
if(o.whoopee)buildWhoopee();
if(o.cocoa)buildCocoa();
if(o.ufo)buildUfo();
if(o.console)buildConsoleSet();
if(whoopeeG)whoopeeG.visible=!!o.whoopee;
if(cocoaG)cocoaG.visible=!!o.cocoa;
if(ufoG)ufoG.visible=!!o.ufo;
if(consoleG)consoleG.visible=!!o.console;
if(tvG)tvG.visible=!!o.console;}
function shareState(k,v){netPublishEvent({k:'rs',a:k,v:v?1:0});}
function applyShared(k,v){if(k==='door')S.door=!!v;else if(k==='lamp')S.lamp=!!v;
else if(k==='ceil')S.ceil=!!v;else if(k==='fairy')S.fairy=!!v;
else if(k==='curt')S.curtains=!!v;else if(k==='shower')showerOn=!!v;
else if(k==='bath')bathOn=!!v;}
/* room interactables */
addI(proxyS(.18,-2.08,1.2,-2.38),'toggle desk lamp',function(){
S.lamp=!S.lamp;burstT=.3;thunk();shareState('lamp',S.lamp);});
addI(proxy(1.9,.35,.25,-1.85,2.35,-2.5),'toggle fairy lights',function(){S.fairy=!S.fairy;clickSound();shareState('fairy',S.fairy);});
addI(proxyS(.22,.2,2.34,.2),'toggle ceiling light',function(){S.ceil=!S.ceil;thunk();shareState('ceil',S.ceil);});
addI(proxy(.15,.22,.12,-.75,1.25,2.52),'flip the switch',function(){S.ceil=!S.ceil;thunk();shareState('ceil',S.ceil);});
addI(proxy(.62,.35,.05,0,.42,.022,monG),'change the channel',function(){ch=(ch+1)%3;clickSound();});
addI(proxy(.78,.48,.2,-1.75,1.19,-2.4),function(){return shopOpen?'close the shop':'open the shop';},
function(){shopOpen=!shopOpen;clickSound();});
/* shop buttons live on top of the monitor now */
addI(proxyS(.07,-1.98,1.47,-2.42),'previous item',function(){if(shopOpen){shopIdx=(shopIdx+ITEMS.length-1)%ITEMS.length;clickSound();}});
addI(proxyS(.07,-1.52,1.47,-2.42),'next item',function(){if(shopOpen){shopIdx=(shopIdx+1)%ITEMS.length;clickSound();}});
addI(proxyS(.1,-1.75,1.48,-2.42),function(){
if(!shopOpen)return 'open the shop first';
var it2=ITEMS[shopIdx];
return (shared.owned[it2.id]&&!it2.inst)?'already owned — room-wide':'buy '+it2.name+'  \u25C8'+it2.price;},
function(){
if(!shopOpen){shopOpen=true;return;}
var it2=ITEMS[shopIdx];
if(!it2.inst&&shared.owned[it2.id]){toast('the room already owns it.');return;}
if(shared.money<it2.price){toast('the shared wallet is short. the room pays \u25C8'+incomeRate()+'/min — wait it out.');return;}
netPublishEvent({k:'req',a:'buy',id:it2.id});
toast('purchase sent to the night server\u2026');});
addI(proxyS(.12,-2.22,.86,-2.02),'ring the dinner bell',function(){
if(!shared.owned.bell)return;
if(bellCd>0){toast('the bell is still humming. '+Math.ceil(bellCd)+'s.');return;}
bellCd=15;bellRing();toast('ding ding! dinner time. (pizza: dial 555-7499)');});
addI(proxyS(.12,-1.45,.86,-1.92),'the air horn',function(){
if(!shared.owned.horn)return;
if(hornCd>0){toast('the horn recharges. '+Math.ceil(hornCd)+'s.');return;}
hornCd=20;hornBlast();netPublishEvent({k:'horn'});
toast('BWAMP.');});
addI(proxyS(.1,-1.3,.87,-2.1),'fire the confetti cannon',function(){
if(!shared.owned.confetti)return;
if(confCd>0){toast('reloading\u2026 '+Math.ceil(confCd)+'s.');return;}
confCd=3;popS();
v3.set(-1.3,1.0,-2.05);burst(v3,70,2.2,1,false);
toast('PARTY.');});
addI(proxyS(.12,-1.0,.86,-2.0),'launch fireworks',function(){
if(!shared.owned.fireworks)return;
if(fwCd>0){toast('the tubes cool down. '+Math.ceil(fwCd)+'s.');return;}
fwCd=8;var now=clock.elapsedTime;
for(var i=0;i<4;i++)fwQueue.push(now+.5+i*.9);
toast('fireworks over the nothing. ooh. aah.');});
addI(proxyS(.2,1.0,2.5,-.6),'toggle disco',function(){
if(!shared.owned.disco)return;
discoOn=!discoOn;clickSound();
toast(discoOn?'the void has a beat now.':'disco off. the void rests.');});
addI(proxyS(.3,-.85,.4,1.15),'toggle boombox',function(){
if(!shared.owned.boombox)return;
boomOn=!boomOn;clickSound();
toast(boomOn?'floor lofi engaged.':'boombox off.');});
addI(proxyS(.17,-2.65,.8,1.45),'toggle lava lamp',function(){S.lava=!S.lava;clickSound();});
addI(proxy(.3,2,1.95,-3.02,1.5,-.4),function(){return S.curtains?'open the curtains':'draw the curtains';},
function(){S.curtains=!S.curtains;thunk();shareState('curt',S.curtains);
if(AC)rainLP.frequency.setTargetAtTime(S.curtains?380:900,AC.currentTime,.25);});
addI(proxyS(.5,.41,1.1,0,doorPivot),function(){return S.door?'close the door':'open the door';},
function(){S.door=!S.door;creak();shareState('door',S.door);
toast(S.door?'the hallway hums its one low note. doors, doors, doors.':'you shut it tight. the warmth settles back in.');});
/* poses */
function goPose(mode){prevPos.copy(rig.position);prevRotY=rig.rotation.y;bodyMode=mode;
var headH=heightOff+camera.position.y;
var des=mode==='sit'?1.05:(mode==='bath'?0.95:0.72);
var px=mode==='sit'?-1.75:(mode==='bath'?6.35:2.28);
var pz=mode==='sit'?-1.48:(mode==='bath'?0.7:-1.35);
var r=mode==='sit'?0.25:(mode==='bath'?Math.PI/2:0);
poseBaseY=des-headH;
var gy=groundAt(px,pz);
poseTween={t:0,d:.9,p0:rig.position.clone(),p1:new THREE.Vector3(px,poseBaseY+gy+heightOff,pz),r0:rig.rotation.y,r1:r};
chairIx.enabled=false;bedIx.enabled=false;standIx.enabled=true;
if(mode==='bath'){splash();toast('the water is exactly warm enough.');}
else if(mode==='sit'){
if(shared.owned.whoopee){whoopeeS();netPublishEvent({k:'fart'});
toast('you sat on the whoopee cushion. everyone heard. everyone always hears.');}
else toast('you sink into the chair. the monitor hums for you.');}
else toast('the blanket is warm. stay a while.');}
function standUp(){bodyMode=null;
poseTween={t:0,d:.8,p0:rig.position.clone(),p1:prevPos.clone(),r0:rig.rotation.y,r1:prevRotY};
chairIx.enabled=true;bedIx.enabled=true;standIx.enabled=false;
toast('back on your feet.');}
var chairIx=addI(proxy(.6,.5,.6,-1.75,.55,-1.48),'sit in the chair',function(){if(!bodyMode&&!poseTween)goPose('sit');});
var bedIx=addI(proxy(1.6,.6,1.9,2.28,.6,-1.4),'lie on the bed',function(){if(!bodyMode&&!poseTween)goPose('lie');});
var standProxy=proxyS(.38,0,1.05,0,rig);
var standIx=addI(standProxy,'stand up',function(){if(bodyMode&&!poseTween)standUp();});
standIx.enabled=false;
/* bathroom interactables */
addI(proxyS(.3,6.0,.95,-.3),function(){
if(bathFilling)return 'the tub is filling\u2026';
return bathFill>=1?'pull the plug':'turn on the tub faucet';},function(){
if(bathFill>=1){bathDraining=true;bathFilling=false;sinkS();
toast('you pull the plug. the drain glugs approvingly.');}
else if(!bathFilling){bathFilling=true;bathDraining=false;splash();
toast('the faucet clears its throat and gets to work.');}});
addI(proxy(.85,.7,1.9,6.35,.5,.7),'take a bath',function(){if(!bodyMode&&!poseTween)goPose('bath');});
addI(proxyS(.14,6.1,.72,1.35),'poke the rim duck',function(){squeak();rimDuckWob=1;toast('quack. it nearly fell in. worth it.');});
addI(proxyS(.12,6.05,.72,.05),'drop the bath bomb',function(){
if(bombUsed){toast('you already galaxy\u2019d the water.');return;}
bombUsed=true;bomb.visible=false;
tubWaterBox.material.color.set(0x8a6ad8);
v3.set(6.35,.55,.7);burst(v3,40,1.2,.6,true);
if(AC)noiseHit(AC.currentTime,.8,2600,.06,true);
toast('the water turns galaxy-colored. very spa. very void.');});
addI(proxyS(.4,6.6,1.4,-.2),'toggle the rain shower',function(){showerOn=!showerOn;shareState('shower',showerOn);
toast(showerOn?'the rain head hisses to life.':'the shower drips its last.');});
addI(proxyS(.35,3.95,1.1,-.33),'use the sink',function(){sinkS();toast('you wash your hands. the night does not.');});
addI(proxy(.75,1.0,.1,3.95,1.85,-.55),function(){return mirrorFog?'wipe the fogged mirror':'look in the mirror';},
function(){if(mirrorFog){mirrorFog=false;fogBackT=15;drawFog();
toast('fog gone. it\u2019s you. it was always you.');}
else toast('still you. still warm.');});
addI(proxyS(.3,3.8,.7,2.42),'flush the toilet',function(){flushS();toast('flushed. no comment.');});
addI(proxyS(.4,6.5,.6,2.05),function(){return washT>0?'the wash is running\u2026 '+Math.ceil(washT)+'s':'run the wash';},
function(){if(washT>0)return;washT=12;clickSound();
toast('the machine shudders awake. 12 seconds of industrial calm.');});
addI(proxyS(.14,6.9,1.62,.35),function(){return candleOn?'blow out the candle':'light the candle';},function(){
candleOn=!candleOn;flickS();candleFlame.visible=candleOn;
toast(candleOn?'a small warm eye opens on the shelf.':'you pinch it out. the dark sulks.');});
addI(proxyS(.12,3.45,1.25,2.71),'bathroom light switch',function(){bathOn=!bathOn;shareState('bath',bathOn);
bathLight.intensity=bathOn?1.7:0;bathBulbMat.color.set(bathOn?0xffd9a0:0x33241a);clickSound();});
/* piano + cat */
var pianoFreqs=[523,587,659,698,784];
pianoNotes.forEach(function(k,i){
addI(proxy(.09,.06,.16,k.position.x,.44,k.position.z),'play the small piano',function(){
pianoNote(pianoFreqs[i]);pianoSeq.push(i);if(pianoSeq.length>4)pianoSeq.shift();
if(!pianoPaid&&pianoSeq.join()==='0,2,4,2,0'){pianoPaid=true;
netPublishEvent({k:'req',a:'earn',a2:3});
toast('the room hums along. +\u25C83 to the shared wallet.');chime();}});});
addI(proxyS(.38,.85,.3,.9),'the cat',function(){
if(catWoke){toast('it looks at you like you\u2019re the one who\u2019s been away.');return;}
toast('shh — the cat is asleep.');twitchT=0;purr();});
/* ===== the night server ===== */
var myId=Math.random().toString(16).slice(2,10);
var net={client:null,ok:false,base:'',joined:'',
code:((META.last||'WARM').toUpperCase().slice(0,4))||'WARM',
name:META.name||('wanderer-'+Math.floor(rand(100,999))),peers:{}};
var shared={money:0,owned:{},pizza:{st:'idle',due:0,size:'medium'}};
var stateTs=0,hostSeq=0,stateTimer=0;
var netStatusEl=document.getElementById('netStatus');
document.getElementById('nameIn').value=net.name;
document.getElementById('codeIn').value=net.code;
function netStatus(m){netStatusEl.textContent=m;}
document.getElementById('netBtn').addEventListener('click',function(e){
e.stopPropagation();
net.name=(document.getElementById('nameIn').value||net.name).slice(0,14);
var nc=((document.getElementById('codeIn').value||net.code).toUpperCase().replace(/[^A-Z0-9]/g,'').slice(0,4))||'WARM';
if(nc!==net.code){net.code=nc;resetShared();drawCounter();drawLedger();drawHud();
toast('room '+nc+' — fresh night, fresh shared wallet.');}
save();connectMQTT();});
document.getElementById('codeIn').addEventListener('keydown',function(e){
if(e.key==='Enter'){e.preventDefault();document.getElementById('netBtn').click();}});
document.getElementById('netBox').addEventListener('click',function(e){e.stopPropagation();});
function resetShared(){
stateTs=0;shared={money:0,owned:{},pizza:{st:'idle',due:0,size:'medium'}};
for(var k in worldObjs)killObj(worldObjs[k],true);
applyOwned();}
function publishState(){
if(!net.client||!net.ok||!net.base)return;
var ol=[],k;for(k in shared.owned)if(shared.owned[k])ol.push(k);
var objs=[];for(k in worldObjs){var o=worldObjs[k];if(!o.dead)objs.push({t:o.type,id:k,sz:o.size||'medium'});}
try{net.client.publish(net.base+'/s',JSON.stringify({ts:Date.now(),
money:r1(shared.money),owned:ol,objs:objs,pz:shared.pizza}),{retain:true,qos:1});}catch(e){}}
function applyState(d){
if(!d||!d.ts||d.ts<=stateTs)return;stateTs=d.ts;
shared.money=d.money||0;
var no={},i;for(i=0;i<(d.owned||[]).length;i++)no[d.owned[i]]=true;
shared.owned=no;
if(d.pz)shared.pizza=d.pz;
applyOwned();
var seen={};
for(i=0;i<(d.objs||[]).length;i++){var od=d.objs[i];seen[od.id]=true;
if(!worldObjs[od.id])spawnSharedObj(od.t,od.id,od.sz);}
for(var k in worldObjs){
if(!seen[k]&&!worldObjs[k].dead&&Date.now()-worldObjs[k].born>8000)killObj(worldObjs[k],true);}
drawCounter();drawLedger();}
function hostReq(d){
if(d.a==='buy'){var it=findItem(d.id);if(!it)return;
if(!it.inst&&shared.owned[it.id])return;
if(shared.money<it.price)return;
shared.money-=it.price;
var oid=null;
if(it.inst){oid='o'+(++hostSeq)+myId.slice(0,3);spawnThrowToy(it.t,oid);
if(it.id==='lighter')shared.owned.lighter=true;}
else shared.owned[it.id]=true;
applyOwned();
netPublishEvent({k:'bought',i:it.name,id:it.id,o:oid,t:it.t||null});
publishState();drawCounter();drawLedger();
toast('the room bought the '+it.name+'. one wallet, one joy.');chime();}
else if(d.a==='pizza'){
if(shared.pizza.st==='ordered'){netPublishEvent({k:'pizzaNo'});return;}
if(shared.money<d.price){netPublishEvent({k:'pizzaNo'});return;}
shared.money-=d.price;
shared.pizza={st:'ordered',due:Date.now()+60000,size:d.size};
phone.state='ordered';phone.num='';
orderBtns.forEach(function(b){b.ix.enabled=false;b.g.visible=false;});
netPublishEvent({k:'pizzaOrd',size:d.size,price:d.price});
publishState();drawCounter();}
else if(d.a==='earn'){shared.money+=d.a2||0;publishState();drawCounter();}}
function connectMQTT(){
if(net.client){
if(net.joined===net.code){netStatus('already linked · room '+net.code);return;}
try{net.client.end(true);}catch(e){}
net.client=null;net.ok=false;
for(var pk in net.peers){scene.remove(net.peers[pk].avatar.g);delete net.peers[pk];}
drawLedger();}
if(!window.mqtt){netStatus('relay client still loading — click again in a second.');return;}
net.joined=net.code;
net.base='wi26x/'+net.code;
netStatus('linking to room '+net.code+'\u2026');
try{
net.client=window.mqtt.connect('wss://broker.emqx.io:8084/mqtt',
{clientId:'wi_'+myId,keepalive:30,clean:true,reconnectPeriod:2500,connectTimeout:8000});
}catch(e){netStatus('relay refused: '+e.message);net.client=null;return;}
var c=net.client,base=net.base;
c.on('connect',function(){
net.ok=true;
netStatus('linked \u00B7 room '+net.code+' \u00B7 floor 1 — one night, one wallet, one building.');
c.subscribe([base+'/p/+',base+'/e',base+'/hi',base+'/s'],{qos:1});
announce();
drawLedger();});
c.on('reconnect',function(){netStatus('relinking to room '+net.code+'\u2026');});
c.on('offline',function(){net.ok=false;netStatus('relay offline — retrying\u2026');});
c.on('error',function(){netStatus('relay error — retrying\u2026');});
c.on('message',function(top,pay){
var d;try{d=JSON.parse(pay.toString());}catch(e){return;}
var parts=top.split('/');
if(parts.length<3||parts[1]!==net.code)return;
if(parts[2]==='s'){applyState(d);return;}
if(parts[2]==='hi'){
if(d.id===myId)return;
if(!net.peers[d.id]){
net.peers[d.id]={n:d.n||'wanderer',avatar:makeAvatar(d.n||'wanderer'),last:Date.now(),
tx:SPAWN.x,ty:0,tz:SPAWN.z,tr:0};
toast((d.n||'someone')+' stepped into the room. income is now +'+incomeRate()+'\u25C8/min.');
drawLedger();}
publishPresence();
setTimeout(publishPresence,400);
setTimeout(publishPresence,900);
if(isHost()){publishState();
c.publish(base+'/e',JSON.stringify({k:'state',
d:S.door?1:0,l:S.lamp?1:0,c:S.ceil?1:0,f:S.fairy?1:0,u:S.curtains?1:0,
s:showerOn?1:0,b:bathOn?1:0}),{qos:1});}
return;}
if(parts[2]==='e'){
if(d.k==='req'){if(isHost())hostReq(d);return;}
if(d.k==='bought'){if(isHost())return;
toast((d.n||'the room')+' bought the '+d.i+'. one wallet, one joy.');
if(d.o&&d.t)spawnThrowToy(d.t,d.o);
else if(d.id){shared.owned[d.id]=true;applyOwned();}
if(d.id==='lighter')shared.owned.lighter=true;
chime();drawLedger();return;}
if(d.k==='pizzaOrd'){if(isHost())return;
phone.state='ordered';phone.num='';
orderBtns.forEach(function(b){b.ix.enabled=false;b.g.visible=false;});
toast('one '+d.size+' void pizza for the room. \u25C8'+d.price+'. knock in 60 seconds.');
thunk();chime();return;}
if(d.k==='pizzaNo'){if(phone.state==='menu')toast('tony is busy, or the shared wallet is short.');return;}
if(d.k==='pizzaLand'){if(worldObjs[d.o])return;
spawnPizzaBox(d.size,d.o,false);
toast('knock knock\u2026 the pizza is at the door. no one is there.');return;}
if(d.f===myId)return;
if(d.k==='grab'){var go=worldObjs[d.o];
if(go&&!go.held){go.heldPeer=d.n;go.heldHand=d.h||0;}return;}
if(d.k==='drop'){var dro=worldObjs[d.o];
if(dro){dro.heldPeer=null;
if(d.p)dro.m.position.set(d.p[0],d.p[1],d.p[2]);
if(d.v)dro.v.set(d.v[0],d.v[1],d.v[2]);}return;}
if(d.k==='gone'){killObj(worldObjs[d.o],true);return;}
if(d.k==='open'){var oo=worldObjs[d.o];if(oo)openBox(oo,true);return;}
if(d.k==='fart'){whoopeeS();toast((d.n||'someone')+' sat on the whoopee cushion.');return;}
if(d.k==='eight'){toast('the 8-ball told '+(d.n||'someone')+': '+d.a);thunk();return;}
if(d.k==='horn'){hornBlast();toast((d.n||'someone')+' blasted the air horn.');return;}
if(d.k==='state'){applyShared('door',d.d);applyShared('lamp',d.l);applyShared('ceil',d.c);
applyShared('fairy',d.f);applyShared('curt',d.u);applyShared('shower',d.s);applyShared('bath',d.b);return;}
if(d.k==='rs'){applyShared(d.a,d.v);return;}
return;}
if(parts[2]==='p'){
var id=parts[3];if(!id||id===myId)return;
var p=net.peers[id];
if(!p){p={avatar:makeAvatar(d.n||'wanderer')};net.peers[id]=p;
toast((d.n||'someone')+' stepped into the room. income is now +'+incomeRate()+'\u25C8/min.');drawLedger();}
else if(d.n&&p.n!==d.n){p.avatar.setName(d.n);}
p.n=d.n||'wanderer';p.m=d.m||0;p.tx=d.x;p.ty=d.y;p.tz=d.z;p.tr=d.r;
p.pose=d.po||0;p.c=d.c||0;p.ht=d.ht||0;
p.hd=d.hd||null;p.hl=d.hl||null;p.hr=d.hr||null;
p.last=Date.now();}});}
function announce(){
if(!net.client||!net.ok||!net.base)return;
try{net.client.publish(net.base+'/hi',JSON.stringify({id:myId,n:net.name}),{qos:1});}catch(e){}
publishPresence();}
setInterval(announce,4000);
(function presenceLoop(){
setTimeout(function(){
publishPresence();
var now=Date.now(),changed=false;
for(var k in net.peers){if(now-net.peers[k].last>12000){
var goneName=net.peers[k].n;
scene.remove(net.peers[k].avatar.g);delete net.peers[k];changed=true;
for(var ok in worldObjs)if(worldObjs[ok].heldPeer===goneName)worldObjs[ok].heldPeer=null;}}
if(changed){toast('someone faded out. income is now +'+incomeRate()+'\u25C8/min.');drawLedger();}
presenceLoop();},700+Math.random()*300);})();
function handLocal(ctl,hand){
var src=null;
if(hand&&hand.userData.active&&hand.joints&&hand.joints['wrist']&&hand.joints['wrist'].visible)src=hand.joints['wrist'];
else if(ctl.visible)src=ctl;
if(!src)return null;
src.getWorldPosition(tp);tp.applyMatrix4(tmpM);
src.getWorldQuaternion(tq);tq.premultiply(bqi);
return [r2(tp.x),r2(tp.y),r2(tp.z),r2(tq.x),r2(tq.y),r2(tq.z),r2(tq.w)];}
function publishPresence(){
if(!net.client||!net.ok||!net.base)return;
var po=bodyMode==='sit'?1:(bodyMode==='lie'?2:(bodyMode==='bath'?3:0));
rig.updateMatrixWorld(true);
tmpM.copy(rig.matrixWorld).invert();
rig.getWorldQuaternion(bq);bqi.copy(bq).invert();
camera.getWorldPosition(tp);tp.applyMatrix4(tmpM);
camera.getWorldQuaternion(tq);tq.premultiply(bqi);
var hd=[r2(tp.x),r2(tp.y),r2(tp.z),r2(tq.x),r2(tq.y),r2(tq.z),r2(tq.w)];
try{
net.client.publish(net.base+'/p/'+myId,JSON.stringify({
n:net.name,m:Math.floor(shared.money),
x:r2(rig.position.x),y:r2(rig.position.y),z:r2(rig.position.z),
r:r2(rig.rotation.y),po:po,c:shared.owned.nose?1:0,ht:shared.owned.hat?1:0,
hd:hd,hl:handLocal(ctl0,hand0),hr:handLocal(ctl1,hand1)}),{qos:1});
}catch(e){}}
function netPublishEvent(d){
if(!net.client||!net.ok||!net.base)return;
d.n=net.name;d.f=myId;
try{net.client.publish(net.base+'/e',JSON.stringify(d),{qos:1});}catch(e){}}
setInterval(function(){drawLedger();},PERF?2000:1000);
/* ===== avatars ===== */
var RIGHTV=new THREE.Vector3(1,0,0);
var SLp=new THREE.Vector3(),SRp=new THREE.Vector3(),TL=new THREE.Vector3(),TR=new THREE.Vector3(),
EL=new THREE.Vector3(),ER=new THREE.Vector3(),KL=new THREE.Vector3(),KR=new THREE.Vector3(),
HLP=new THREE.Vector3(),HRP=new THREE.Vector3(),FL=new THREE.Vector3(),FR=new THREE.Vector3(),
HV=new THREE.Vector3(),dv=new THREE.Vector3(),pv=new THREE.Vector3(),hq=new THREE.Quaternion();
function limb(mesh,from,to){
var d=from.distanceTo(to);
mesh.scale.y=Math.max(.02,d);
mesh.position.copy(from).add(to).multiplyScalar(.5);
if(d>1e-5)mesh.quaternion.setFromUnitVectors(UP,tv.copy(to).sub(from).normalize());}
function ikJoint(S2,T2,l1,l2,bend,spread,out){
var d=S2.distanceTo(T2);d=clamp(d,.04,l1+l2-.006);
var a=(l1*l1-l2*l2+d*d)/(2*d);
var h=Math.sqrt(Math.max(0,l1*l1-a*a));
dv.copy(T2).sub(S2);
if(dv.lengthSq()<1e-9)dv.set(0,-1,0);else dv.normalize();
pv.crossVectors(dv,RIGHTV);
if(pv.lengthSq()<1e-6)pv.set(0,0,1);
pv.normalize();
out.copy(S2).addScaledVector(dv,a).addScaledVector(pv,h*bend);
out.x+=h*spread;}
function makeAvatar(name){
var g=new THREE.Group();
var skin=new THREE.MeshStandardMaterial({color:0xd9a077,roughness:.8});
var shirt=new THREE.MeshStandardMaterial({color:0x8a5a3a,roughness:.9});
var legsM=new THREE.MeshStandardMaterial({color:0x3a2c22,roughness:.9});
var torso=new THREE.Mesh(new THREE.CylinderGeometry(.14,.17,1,10),shirt);torso.castShadow=true;g.add(torso);
var hipM=new THREE.Mesh(new THREE.CylinderGeometry(.165,.15,.18,10),legsM);hipM.castShadow=true;g.add(hipM);
var headG=new THREE.Group();g.add(headG);
var head=new THREE.Mesh(new THREE.SphereGeometry(.115,12,10),skin);head.castShadow=true;headG.add(head);
var face=new THREE.Mesh(new THREE.BoxGeometry(.15,.06,.03),new THREE.MeshStandardMaterial({color:0x1a120c,roughness:.6}));
face.position.set(0,.02,-.095);headG.add(face);
var eyeWhite=new THREE.MeshBasicMaterial({color:0xf5f7ff,fog:false});eyeWhite.toneMapped=false;
[-1,1].forEach(function(sx){var e=new THREE.Mesh(new THREE.SphereGeometry(.013,6,6),eyeWhite);
e.position.set(sx*.045,.035,-.105);headG.add(e);});
var nose=new THREE.Mesh(new THREE.SphereGeometry(.03,8,8),new THREE.MeshStandardMaterial({color:0xd03030,roughness:.4}));
nose.position.set(0,-.015,-.125);nose.visible=false;headG.add(nose);
var hat=new THREE.Group();hat.position.set(0,.15,0);hat.visible=false;headG.add(hat);
var cone=new THREE.Mesh(new THREE.ConeGeometry(.07,.2,8),new THREE.MeshStandardMaterial({color:0xff9a3c,roughness:.5}));
cone.position.y=.1;hat.add(cone);
var pom=new THREE.Mesh(new THREE.SphereGeometry(.025,6,6),new THREE.MeshStandardMaterial({color:0xfff0cf,roughness:.6}));
pom.position.y=.21;hat.add(pom);
var armMat=shirt.clone();
var aLU=new THREE.Mesh(new THREE.CylinderGeometry(.05,.045,1,6),armMat);g.add(aLU);
var aLF=new THREE.Mesh(new THREE.CylinderGeometry(.045,.038,1,6),armMat);g.add(aLF);
var aRU=new THREE.Mesh(new THREE.CylinderGeometry(.05,.045,1,6),armMat);g.add(aRU);
var aRF=new THREE.Mesh(new THREE.CylinderGeometry(.045,.038,1,6),armMat);g.add(aRF);
var handL=new THREE.Mesh(new THREE.BoxGeometry(.07,.035,.11),skin);g.add(handL);
var handR=new THREE.Mesh(new THREE.BoxGeometry(.07,.035,.11),skin);g.add(handR);
var legLU=new THREE.Mesh(new THREE.CylinderGeometry(.062,.055,1,6),legsM);g.add(legLU);
var legLF=new THREE.Mesh(new THREE.CylinderGeometry(.055,.046,1,6),legsM);g.add(legLF);
var legRU=new THREE.Mesh(new THREE.CylinderGeometry(.062,.055,1,6),legsM);g.add(legRU);
var legRF=new THREE.Mesh(new THREE.CylinderGeometry(.055,.046,1,6),legsM);g.add(legRF);
var footMat=new THREE.MeshStandardMaterial({color:0x241a12,roughness:.8});
var footL=new THREE.Mesh(new THREE.BoxGeometry(.09,.05,.17),footMat);footL.castShadow=true;g.add(footL);
var footR=new THREE.Mesh(new THREE.BoxGeometry(.09,.05,.17),footMat);footR.castShadow=true;g.add(footR);
var halo=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:0xffa050,transparent:true,opacity:.35,
blending:THREE.AdditiveBlending,depthWrite:false,fog:false}));
halo.scale.set(1,1,1);g.add(halo);
var nc=document.createElement('canvas');nc.width=256;nc.height=64;
var nx=nc.getContext('2d');
var nt=new THREE.CanvasTexture(nc);nt.encoding=THREE.sRGBEncoding;
var ns=new THREE.Sprite(new THREE.SpriteMaterial({map:nt,transparent:true,depthTest:false,fog:false}));
ns.scale.set(.5,.125,1);ns.renderOrder=996;g.add(ns);
function paintName(nm){nx.clearRect(0,0,256,64);
nx.fillStyle='rgba(12,7,4,.78)';
if(nx.roundRect){nx.beginPath();nx.roundRect(28,8,200,48,10);nx.fill();}
else nx.fillRect(28,8,200,48);
nx.fillStyle='#ffd9a8';nx.font='600 27px "Space Grotesk",sans-serif';
nx.textAlign='center';nx.textBaseline='middle';nx.fillText(String(nm).slice(0,12),128,33);
nt.needsUpdate=true;}
paintName(name);
scene.add(g);
return {g:g,torso:torso,hipM:hipM,headG:headG,nose:nose,hat:hat,
aLU:aLU,aLF:aLF,aRU:aRU,aRF:aRF,handL:handL,handR:handR,
legLU:legLU,legLF:legLF,legRU:legRU,legRF:legRF,footL:footL,footR:footR,
halo:halo,ns:ns,setName:paintName};}
function updAvatar(p,dt,t){
var a=p.avatar;if(!a)return;
if(p.px===undefined){p.px=p.tx||0;p.py=p.ty||0;p.pz=p.tz||0;
p.spd=0;p.wph=0;p.hN=1.55;p.hlS=new THREE.Vector3(0,1.5,0);}
var k=Math.min(1,dt*7),ox=p.px,oz=p.pz;
p.px+=(p.tx-p.px)*k;p.py+=(p.ty-p.py)*k;p.pz+=(p.tz-p.pz)*k;
var inst=Math.sqrt((p.px-ox)*(p.px-ox)+(p.pz-oz)*(p.pz-oz))/Math.max(dt,.001);
p.spd+=(inst-p.spd)*Math.min(1,dt*4);
a.g.position.set(p.px,p.py,p.pz);
var dyr=(p.tr||0)-a.g.rotation.y;
while(dyr>Math.PI)dyr-=Math.PI*2;while(dyr<-Math.PI)dyr+=Math.PI*2;
a.g.rotation.y+=dyr*Math.min(1,dt*8);
var pose=p.pose||0,lying=pose===2,sit=pose===1||pose===3;
var headIn=(p.hd&&p.hd.length===7)?p.hd[1]:1.55;
p.hN+=(headIn-p.hN)*Math.min(1,dt*8);
var headY=clamp(p.hN,.7,2.4);
var s=clamp(headY/1.55,.5,1.45);
var moveAmt=clamp((p.spd-.08)/1.4,0,1)*(pose===0?1:0);
var hdOk=p.hd&&p.hd.length===7&&!lying;
if(hdOk)HV.set(p.hd[0],p.hd[1],p.hd[2]);
else HV.set(0,lying?.60:headY*.97,lying?-.78*s:0);
p.hlS.lerp(HV,Math.min(1,dt*14));
a.headG.position.copy(p.hlS);
if(hdOk){hq.set(p.hd[3],p.hd[4],p.hd[5],p.hd[6]);
a.headG.quaternion.slerp(hq,Math.min(1,dt*14));}
else{hq.setFromAxisAngle(RIGHTV,lying?Math.PI/2:0);
a.headG.quaternion.slerp(hq,Math.min(1,dt*6));}
var hipY,neckY,shoulderY,bob=Math.sin(p.wph*2)*.022*moveAmt,hipW=.095*s;
var br=1+Math.sin(t*1.9)*.012;
if(lying){
hipY=.5;neckY=.58;shoulderY=.56;
HLP.set(0,hipY+.03,.12);HRP.set(0,neckY,-.52*s);
limb(a.torso,HLP,HRP);
a.hipM.position.set(0,hipY,.1);a.hipM.rotation.x=Math.PI/2;
}else{
hipY=sit?headY-.62*s:headY*.5;
neckY=headY-.07;shoulderY=headY-.19;
HLP.set(0,hipY+.03+bob,0);HRP.set(0,neckY+bob,-.06*moveAmt);
limb(a.torso,HLP,HRP);
a.hipM.position.set(0,hipY+bob,0);a.hipM.rotation.x=0;}
a.torso.scale.x=a.torso.scale.z=s*br;
a.hipM.scale.set(s,1,s);
var legL1=headY*.30,legL2=headY*.30;
if(pose===0&&p.spd>.12)p.wph+=dt*(3.6+p.spd*2.4);
var stride=.36*moveAmt,liftA=.11*moveAmt;
var hipLX=hipW*1.6;
if(lying){
FL.set(-hipW,.06,.85*s);FR.set(hipW,.06,.85*s);
HLP.set(-hipW,hipY,.12);HRP.set(hipW,hipY,.12);
}else if(sit){
var fy=Math.max(.02,headY-1.12*s);
FL.set(-hipLX,fy,.36*s);FR.set(hipLX,fy,.36*s);
HLP.set(-hipW,hipY+bob,0);HRP.set(hipW,hipY+bob,0);
}else{
var sl=Math.sin(p.wph),sr=Math.sin(p.wph+Math.PI);
FL.set(-hipLX,Math.max(0,sl)*liftA,sl*stride);
FR.set(hipLX,Math.max(0,sr)*liftA,sr*stride);
HLP.set(-hipW,hipY+bob,0);HRP.set(hipW,hipY+bob,0);}
ikJoint(HLP,FL,legL1,legL2,-1,0,KL);
ikJoint(HRP,FR,legL1,legL2,-1,0,KR);
limb(a.legLU,HLP,KL);limb(a.legLF,KL,FL);
limb(a.legRU,HRP,KR);limb(a.legRF,KR,FR);
a.footL.position.set(FL.x,FL.y+.028,FL.z-.03);
a.footR.position.set(FR.x,FR.y+.028,FR.z-.03);
if(lying){a.footL.rotation.set(0,Math.PI,0);a.footR.rotation.set(0,Math.PI,0);}
else{a.footL.rotation.set(Math.max(0,Math.sin(p.wph))*liftA*3,0,0);
a.footR.rotation.set(Math.max(0,Math.sin(p.wph+Math.PI))*liftA*3,0,0);}
var armL1=headY*.19,armL2=headY*.17;
var shY=lying?.56:shoulderY+bob;
SLp.set(-.19*s,shY,lying?-.38*s:-.02);
SRp.set(.19*s,shY,lying?-.38*s:-.02);
var hlOk=p.hl&&p.hl.length===7,hrOk=p.hr&&p.hr.length===7;
if(hlOk)TL.set(p.hl[0],p.hl[1],p.hl[2]);
else if(lying)TL.set(-.26*s,.5,.1);
else if(sit)TL.set(-.13*s,hipY-.02,.34*s);
else TL.set(-.26*s,hipY+.12+bob,.05+Math.sin(p.wph+Math.PI)*stride*.55);
if(hrOk)TR.set(p.hr[0],p.hr[1],p.hr[2]);
else if(lying)TR.set(.26*s,.5,.1);
else if(sit)TR.set(.13*s,hipY-.02,.34*s);
else TR.set(.26*s,hipY+.12+bob,.05+Math.sin(p.wph)*stride*.55);
ikJoint(SLp,TL,armL1,armL2,1,-.3,EL);
ikJoint(SRp,TR,armL1,armL2,1,.3,ER);
limb(a.aLU,SLp,EL);limb(a.aLF,EL,TL);
limb(a.aRU,SRp,ER);limb(a.aRF,ER,TR);
a.handL.position.copy(TL);a.handR.position.copy(TR);
if(hlOk){hq.set(p.hl[3],p.hl[4],p.hl[5],p.hl[6]);a.handL.quaternion.copy(hq);}
else a.handL.quaternion.identity();
if(hrOk){hq.set(p.hr[3],p.hr[4],p.hr[5],p.hr[6]);a.handR.quaternion.copy(hq);}
else a.handR.quaternion.identity();
a.nose.visible=!!p.c;
a.hat.visible=!!p.ht;
a.ns.position.set(0,p.hlS.y+.27,0);
a.halo.position.set(0,hipY+.35,0);
a.halo.material.opacity=.28+Math.sin(t*2.1)*.08;}
/* ===== your own body — always riding with your head ===== */
var selfB=null,selfWph=0,selfSpd=0,selfLast=new THREE.Vector3();
var fwdSelf=new THREE.Vector3(),rightSelf=new THREE.Vector3(),
neckP=new THREE.Vector3(),hipP=new THREE.Vector3(),
shL=new THREE.Vector3(),shR=new THREE.Vector3(),
hlT=new THREE.Vector3(),hrT=new THREE.Vector3(),
hipLp=new THREE.Vector3(),hipRp=new THREE.Vector3(),
ftL=new THREE.Vector3(),ftR=new THREE.Vector3(),
kneeL=new THREE.Vector3(),kneeR=new THREE.Vector3(),
elbL=new THREE.Vector3(),elbR=new THREE.Vector3();
function buildSelfBody(){
if(selfB)return;
var skin=new THREE.MeshStandardMaterial({color:0xd9a077,roughness:.8});
var shirt=new THREE.MeshStandardMaterial({color:0x8a5a3a,roughness:.9});
var legsM=new THREE.MeshStandardMaterial({color:0x3a2c22,roughness:.9});
selfB={};
selfB.torso=new THREE.Mesh(new THREE.CylinderGeometry(.14,.17,1,10),shirt);
selfB.hipM=new THREE.Mesh(new THREE.CylinderGeometry(.165,.15,.18,10),legsM);
selfB.aLU=new THREE.Mesh(new THREE.CylinderGeometry(.05,.045,1,6),shirt);
selfB.aLF=new THREE.Mesh(new THREE.CylinderGeometry(.045,.038,1,6),shirt);
selfB.aRU=new THREE.Mesh(new THREE.CylinderGeometry(.05,.045,1,6),shirt);
selfB.aRF=new THREE.Mesh(new THREE.CylinderGeometry(.045,.038,1,6),shirt);
selfB.handL=new THREE.Mesh(new THREE.BoxGeometry(.07,.035,.11),skin);
selfB.handR=new THREE.Mesh(new THREE.BoxGeometry(.07,.035,.11),skin);
selfB.legLU=new THREE.Mesh(new THREE.CylinderGeometry(.062,.055,1,6),legsM);
selfB.legLF=new THREE.Mesh(new THREE.CylinderGeometry(.055,.046,1,6),legsM);
selfB.legRU=new THREE.Mesh(new THREE.CylinderGeometry(.062,.055,1,6),legsM);
selfB.legRF=new THREE.Mesh(new THREE.CylinderGeometry(.055,.046,1,6),legsM);
selfB.footL=new THREE.Mesh(new THREE.BoxGeometry(.09,.05,.17),new THREE.MeshStandardMaterial({color:0x241a12,roughness:.8}));
selfB.footR=new THREE.Mesh(new THREE.BoxGeometry(.09,.05,.17),new THREE.MeshStandardMaterial({color:0x241a12,roughness:.8}));
for(var k in selfB){selfB[k].castShadow=false;selfB[k].receiveShadow=false;selfB[k].visible=false;scene.add(selfB[k]);}}
function setSelfVisible(v){if(!selfB)return;for(var k in selfB)selfB[k].visible=v;}
function ctlWorld(ctl,hand,out){
if(hand&&hand.userData.active&&hand.joints&&hand.joints['wrist']&&hand.joints['wrist'].visible){
out.setFromMatrixPosition(hand.joints['wrist'].matrixWorld);return true;}
if(ctl.visible){out.setFromMatrixPosition(ctl.matrixWorld);return true;}
return false;}
function updateSelfBody(dt,t){
if(!selfB)return;
if(state!=='xr'||!renderer.xr.isPresenting){setSelfVisible(false);return;}
setSelfVisible(true);
selfB.torso.visible=false;   /* torso stays hidden — no clipping into your view */
selfB.hipM.visible=false;
var inst=rig.position.distanceTo(selfLast)/Math.max(dt,.001);
selfSpd+=(inst-selfSpd)*Math.min(1,dt*4);
selfLast.copy(rig.position);
camera.getWorldPosition(camPos);
camera.getWorldDirection(fwdSelf);fwdSelf.y=0;
if(fwdSelf.lengthSq()<1e-6)fwdSelf.set(0,0,-1);fwdSelf.normalize();
rightSelf.crossVectors(fwdSelf,UP).normalize();
/* the body lives wherever your head is — everything hangs from the headset */
neckP.copy(camPos).addScaledVector(UP,-.10);
hipP.set(neckP.x,neckP.y-.60,neckP.z);
shL.copy(neckP).addScaledVector(rightSelf,-.19).addScaledVector(UP,-.06);
shR.copy(neckP).addScaledVector(rightSelf,.19).addScaledVector(UP,-.06);
var hlOk=ctlWorld(ctl0,hand0,hlT);
var hrOk=ctlWorld(ctl1,hand1,hrT);
if(!hlOk)hlT.copy(shL).addScaledVector(UP,-.5).addScaledVector(fwdSelf,.15);
if(!hrOk)hrT.copy(shR).addScaledVector(UP,-.5).addScaledVector(fwdSelf,.15);
ikJoint(shL,hlT,.30,.27,1,-.3,elbL);
ikJoint(shR,hrT,.30,.27,1,.3,elbR);
limb(selfB.aLU,shL,elbL);limb(selfB.aLF,elbL,hlT);
limb(selfB.aRU,shR,elbR);limb(selfB.aRF,elbR,hrT);
selfB.handL.position.copy(hlT);selfB.handR.position.copy(hrT);
var mv=clamp(selfSpd/1.4,0,1);
if(selfSpd>.12)selfWph+=dt*(3.6+selfSpd*2.4);
var stride=.36*mv,liftA=.11*mv;
var sl=Math.sin(selfWph),sr=Math.sin(selfWph+Math.PI);
hipLp.copy(hipP).addScaledVector(rightSelf,-.095);
hipRp.copy(hipP).addScaledVector(rightSelf,.095);
ftL.copy(hipLp).addScaledVector(fwdSelf,sl*stride);ftL.y=hipP.y-.88+Math.max(0,sl)*liftA;
ftR.copy(hipRp).addScaledVector(fwdSelf,sr*stride);ftR.y=hipP.y-.88+Math.max(0,sr)*liftA;
ikJoint(hipLp,ftL,.5,.5,-1,0,kneeL);
ikJoint(hipRp,ftR,.5,.5,-1,0,kneeR);
limb(selfB.legLU,hipLp,kneeL);limb(selfB.legLF,kneeL,ftL);
limb(selfB.legRU,hipRp,kneeR);limb(selfB.legRF,kneeR,ftR);
selfB.footL.position.copy(ftL);selfB.footR.position.copy(ftR);}
buildSelfBody();
/* ===== VR session / controllers / hands ===== */
if(!navigator.xr)document.getElementById('xrWarn').classList.add('show');
var starting=false;
function enterVR(){if(starting||renderer.xr.isPresenting)return;
starting=true;initAudio();
if(!net.client)connectMQTT();
if(!navigator.xr){toast('no webxr here — open this page in a headset browser');starting=false;return;}
navigator.xr.requestSession('immersive-vr',{optionalFeatures:['local-floor','bounded-floor','hand-tracking']})
.then(function(s){return renderer.xr.setSession(s);})
.catch(function(){toast('couldn\u2019t start a vr session on this device');})
.then(function(){starting=false;});}
document.getElementById('btnVR').addEventListener('click',function(e){e.stopPropagation();enterVR();});
renderer.xr.addEventListener('sessionstart',function(){
state='xr';document.body.classList.add('playing');
rig.position.set(SPAWN.x,0,SPAWN.z);rig.rotation.y=0;rigY=0;heightOff=0;heightLocked=false;heightFrames=0;
bodyMode=null;poseTween=null;
chairIx.enabled=true;bedIx.enabled=true;standIx.enabled=false;
ringT=0;labelSpr.visible=false;selfLast.copy(rig.position);});
renderer.xr.addEventListener('sessionend',function(){
state='lobby';document.body.classList.remove('playing');
labelSpr.visible=false;clearHighlight();setSelfVisible(false);
heightLocked=false;heightFrames=0;heightOff=0;
[ctl0,ctl1,hand0,hand1].forEach(function(s){if(s.userData.grabO)endGrab(s);});
toast('vr session ended — enter vr to go back in');});
function hapticPulse(src,a,ms){try{var gp=src&&src.gamepad;
if(gp&&gp.hapticActuators&&gp.hapticActuators[0])gp.hapticActuators[0].pulse(a,ms);}catch(e){}}
function useActivate(ix,src,type){
if(ix.grab&&ix.obj){var o=ix.obj;
if(o.type==='pizza'&&o.closed){openBox(o,false);return;}
if(o.type==='slice'){biteSlice(o);return;}
if(useHeld(o,src))return;
toast('grip to grab it.');return;}
if(ix.run){ix.run();clickSound();
if(type==='ctl')hapticPulse(src.userData.inputSource,.4,60);}}
function buildController(i){var c=renderer.xr.getController(i);
var ray=new THREE.Line(new THREE.BufferGeometry().setFromPoints([new THREE.Vector3(),new THREE.Vector3(0,0,-1)]),
new THREE.LineBasicMaterial({color:0xffc27a,transparent:true,opacity:.6}));
ray.scale.z=3;c.add(ray);
var tipMat=new THREE.MeshBasicMaterial({color:0xffc27a});tipMat.toneMapped=false;
var tip=new THREE.Mesh(new THREE.SphereGeometry(.012,6,6),tipMat);c.add(tip);
var body=new THREE.Mesh(new THREE.CylinderGeometry(.017,.023,.11,8),
new THREE.MeshStandardMaterial({color:0x2a211b,roughness:.55}));
body.rotation.x=Math.PI/2.4;body.position.z=.03;c.add(body);
c.visible=false;
c.userData={ray:ray,tip:tip,hit:null,lastHit:null,inputSource:null,grabO:null};
c.addEventListener('connected',function(e){if(e.data&&e.data.hand)return;
c.visible=true;c.userData.inputSource=e.data;});
c.addEventListener('disconnected',function(){c.visible=false;c.userData.inputSource=null;});
c.addEventListener('squeezestart',function(){
if(state!=='xr')return;
var hit=c.userData.hit;
if(hit&&hit.grab&&hit.obj){startGrab(hit.obj,'ctl',c);return;}
c.getWorldPosition(pv2);
var o=nearestGrabbable(pv2,.42);
if(o)startGrab(o,'ctl',c);});
c.addEventListener('squeezeend',function(){endGrab(c);});
c.addEventListener('selectstart',function(){
if(state!=='xr')return;
if(c.userData.grabO&&useHeld(c.userData.grabO,c))return;
if(c.userData.hit)useActivate(c.userData.hit,c,'ctl');});
rig.add(c);return c;}
var ctl0=buildController(0),ctl1=buildController(1);
var handMatB=new THREE.MeshStandardMaterial({color:0x3a2c22,roughness:.6});
var handTipMat=new THREE.MeshStandardMaterial({color:0xffb45e,emissive:0xff9a3c,emissiveIntensity:.8,roughness:.4});
var handGeo=new THREE.SphereGeometry(1,6,6);
function buildHand(i,ctl){var h=renderer.xr.getHand(i);
h.userData={active:false,built:false,hover:null,grabO:null};
h.addEventListener('connected',function(){h.userData.active=true;ctl.visible=false;});
h.addEventListener('disconnected',function(){h.userData.active=false;h.userData.built=false;h.userData.hover=null;});
h.addEventListener('squeezestart',function(){
if(state!=='xr')return;
var hov=h.userData.hover;
if(hov&&hov.grab&&hov.obj){startGrab(hov.obj,'hand',h);return;}
if(h.joints&&h.joints['wrist']&&h.joints['wrist'].visible){
h.joints['wrist'].getWorldPosition(pv2);
var o=nearestGrabbable(pv2,.35);
if(o)startGrab(o,'hand',h);}});
h.addEventListener('squeezeend',function(){endGrab(h);});
h.addEventListener('selectstart',function(){
if(state!=='xr')return;
if(h.userData.grabO&&useHeld(h.userData.grabO,h))return;
if(h.userData.hover)useActivate(h.userData.hover,h,'hand');});
rig.add(h);return h;}
var hand0=buildHand(0,ctl0),hand1=buildHand(1,ctl1);
var ftip=new THREE.Vector3(),hp=new THREE.Vector3();
function handHover(h){if(!h.userData.active||!h.joints){h.userData.hover=null;return null;}
if(!h.userData.built){var names=Object.keys(h.joints);
if(names.length){h.userData.built=true;
for(var j=0;j<names.length;j++){var jn=names[j],isTip=/-tip$/.test(jn);
var s=new THREE.Mesh(handGeo,isTip?handTipMat:handMatB);
s.scale.setScalar(isTip?.011:(jn==='wrist'?.015:.008));
h.joints[jn].add(s);}}}
var tipJ=h.joints['index-finger-tip'];
if(!tipJ||!tipJ.visible){h.userData.hover=null;return null;}
tipJ.getWorldPosition(ftip);
var near=null,nd=.55;
for(var i=0;i<interactables.length;i++){var ix=interactables[i];
if(ix.enabled===false||ix.dead||!ix.hit.parent)continue;
ix.hit.getWorldPosition(hp);
var dd=ftip.distanceTo(hp);
if(dd<nd){nd=dd;near=ix;}}
h.userData.hover=near;
return near?{ix:near,point:ftip}:null;}
var reachSpr=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:0x7fc4ff,transparent:true,opacity:0,
blending:THREE.AdditiveBlending,depthWrite:false,fog:false}));
reachSpr.scale.set(.2,.2,1);scene.add(reachSpr);
var labelCv=document.createElement('canvas');labelCv.width=512;labelCv.height=96;
var lctx=labelCv.getContext('2d');
var labelTex=new THREE.CanvasTexture(labelCv);labelTex.encoding=THREE.sRGBEncoding;
var labelSpr=new THREE.Sprite(new THREE.SpriteMaterial({map:labelTex,transparent:true,depthTest:false}));
labelSpr.scale.set(.5,.094,1);labelSpr.renderOrder=999;labelSpr.visible=false;scene.add(labelSpr);
function setLabel(text,pos){lctx.clearRect(0,0,512,96);
lctx.fillStyle='rgba(14,8,4,.92)';
lctx.beginPath();
if(lctx.roundRect)lctx.roundRect(56,10,400,76,14);else lctx.rect(56,10,400,76);
lctx.fill();
lctx.strokeStyle='rgba(255,180,94,.6)';lctx.lineWidth=3;lctx.stroke();
lctx.fillStyle='#ffd9a8';lctx.font='30px "Space Grotesk",sans-serif';
lctx.textAlign='center';lctx.textBaseline='middle';lctx.fillText(text,256,50);
labelTex.needsUpdate=true;
labelSpr.position.copy(pos);labelSpr.position.y+=.14;labelSpr.visible=true;}
/* ===== HUD ===== */
var hudCv=document.createElement('canvas');hudCv.width=512;hudCv.height=128;
var hctx=hudCv.getContext('2d');
var hudTex=new THREE.CanvasTexture(hudCv);hudTex.encoding=THREE.sRGBEncoding;
var hudSpr=new THREE.Sprite(new THREE.SpriteMaterial({map:hudTex,transparent:true,depthTest:false}));
hudSpr.scale.set(.36,.09,1);hudSpr.position.set(0,-.27,-.65);hudSpr.renderOrder=998;hudSpr.visible=false;
camera.add(hudSpr);
function nearLitLighter(){
for(var i=0;i<physObjs.length;i++){var o=physObjs[i];
if(o.type==='lighter'&&!o.dead&&o.flameOn!==false){
if(o.m.position.distanceTo(rig.position)<2.5)return true;}}
return false;}
function drawHud(){var x=hctx;x.clearRect(0,0,512,128);
x.fillStyle='rgba(12,7,4,.82)';
x.beginPath();
if(x.roundRect)x.roundRect(6,16,500,96,10);else x.rect(6,16,500,96);
x.fill();
x.fillStyle='#ffb45e';x.fillRect(6,16,5,96);
x.textBaseline='middle';
x.textAlign='left';
x.fillStyle='#ffd9a8';x.font='700 32px "Space Grotesk",monospace';
x.fillText('\u25C8 '+Math.floor(shared.money)+' shared',36,64);
x.textAlign='right';x.font='22px "Space Grotesk"';
if(curFloor>1){
var safeNow=nearLitLighter();
x.fillStyle=(curFloor>=5&&!safeNow)?'#ff6a5a':'#ffb45e';
x.fillText('FLOOR '+curFloor+((curFloor>=5&&!safeNow)?' — it is coming. LIGHT the lighter.':''),476,64);}
else if(shared.pizza.st==='ordered'){x.fillStyle='#ffb45e';
var s=Math.max(0,Math.ceil((shared.pizza.due-Date.now())/1000));
x.fillText('pizza in 0:'+(s<10?'0':'')+s,476,64);}
else{x.fillStyle='#a08a72';x.fillText('tony\u2019s void pizza \u00B7 dial 555-7499',476,64);}
hudTex.needsUpdate=true;}
/* ===== monitor screen + shop icons ===== */
var ch=0;
var fishArr=[{y:70,sp:46,ph:0,col:'#ff9a4c',s:1},{y:150,sp:30,ph:200,col:'#5ac8c8',s:.8},
{y:110,sp:60,ph:380,col:'#ffd07a',s:.65},{y:200,sp:38,ph:120,col:'#7aa0ff',s:1.1}];
var termLines=['> rain.status ......... cozy','> door.status ......... open to the hallway',
'> pizza.hotline ....... 555-7499','> cat.status .......... asleep (do not disturb)',
'> wallet.type ......... shared, honest','> staircase.status .... 24 floors and counting',
'> demon.status ........ afraid of fire','> lobby.status ........ warm, if you make it','>'];
function dIcon(x,id,cx,cy){
x.save();x.translate(cx,cy);
x.strokeStyle='#ffd9a8';x.fillStyle='#ffd9a8';x.lineWidth=3;x.lineCap='round';x.lineJoin='round';
function cir(px,py,r){x.beginPath();x.arc(px,py,r,0,7);}
function tri(x1,y1,x2,y2,x3,y3){x.beginPath();x.moveTo(x1,y1);x.lineTo(x2,y2);x.lineTo(x3,y3);x.closePath();}
switch(id){
case 'sprint':x.fillStyle='#ffb45e';x.fillRect(-18,0,30,10);x.fillRect(-18,-8,10,10);
x.fillStyle='#0d0805';x.fillRect(-18,10,36,3);x.fillRect(2,-4,4,3);x.fillRect(9,-4,4,3);break;
case 'boots':x.fillStyle='#ffb45e';x.fillRect(-12,-14,16,20);x.fillRect(-12,4,26,6);
x.fillStyle='#0d0805';x.fillRect(-12,10,26,3);break;
case 'nose':x.fillStyle='#d03030';cir(0,0,14);x.fill();
x.fillStyle='rgba(255,255,255,.5)';cir(-5,-5,4);x.fill();break;
case 'hat':x.fillStyle='#ffb45e';tri(0,-16,13,12,-13,12);x.fill();
x.fillStyle='#fff0cf';cir(0,-16,4);x.fill();break;
case 'duck':x.fillStyle='#f2c94c';cir(-2,4,11);x.fill();cir(6,-8,7);x.fill();
x.fillStyle='#e8862c';tri(12,-9,19,-7,12,-5);x.fill();
x.fillStyle='#0d0805';cir(8,-10,1.6);x.fill();break;
case 'rock':x.fillStyle='#8a8278';x.beginPath();x.moveTo(-13,6);x.lineTo(-7,-10);x.lineTo(8,-12);
x.lineTo(14,2);x.lineTo(6,12);x.lineTo(-8,12);x.closePath();x.fill();
x.fillStyle='#f5f5f0';cir(-3,-2,3);x.fill();cir(5,-3,3);x.fill();
x.fillStyle='#111';cir(-3,-2,1.3);x.fill();cir(5,-3,1.3);x.fill();break;
case 'soap':x.fillStyle='#bfd8c8';x.fillRect(-13,-7,26,14);
x.fillStyle='rgba(255,255,255,.8)';cir(8,-11,3);x.fill();cir(14,-6,2);x.fill();break;
case 'banana':x.strokeStyle='#e8c84a';x.lineWidth=7;x.beginPath();x.arc(0,-4,13,.3,Math.PI-.3);x.stroke();break;
case 'horn':x.fillStyle='#c03030';tri(-14,6,10,-2,10,10);x.fill();
x.strokeStyle='#ffb45e';x.lineWidth=3;x.beginPath();x.arc(12,4,7,-1.2,1.2);x.stroke();break;
case 'bell':x.fillStyle='#c8a050';x.beginPath();x.arc(0,2,11,Math.PI,0);x.lineTo(14,8);x.lineTo(-14,8);x.closePath();x.fill();
cir(0,12,3);x.fill();x.fillRect(-2,-12,4,5);break;
case 'cat':x.fillStyle='#8a7a6a';cir(0,2,11);x.fill();
tri(-11,-4,-9,-14,-3,-7);x.fill();tri(11,-4,9,-14,3,-7);x.fill();
x.fillStyle='#ffd9a8';cir(-4,0,1.6);x.fill();cir(4,0,1.6);x.fill();break;
case 'gold':x.fillStyle='#ffd08a';cir(0,-3,9);x.fill();
x.fillStyle='#c8a050';x.fillRect(-4,6,8,6);break;
case 'radio':x.fillStyle='#ffb45e';x.fillRect(-14,-8,28,18);
x.fillStyle='#0d0805';cir(-6,1,5);x.fill();x.fillRect(3,-4,8,3);x.fillRect(3,2,8,3);break;
case 'ball':cir(0,0,13);x.fillStyle='#ff6a6a';x.fill();
x.strokeStyle='#f2f2f2';x.lineWidth=4;x.beginPath();x.arc(0,0,13,-.6,.6);x.stroke();
x.beginPath();x.arc(0,0,13,Math.PI-.6,Math.PI+.6);x.stroke();break;
case 'confetti':x.fillStyle='#d06030';x.save();x.rotate(-.6);x.fillRect(-4,-2,20,9);x.restore();
x.fillStyle='#ffd166';cir(-10,-8,2.5);x.fill();
x.fillStyle='#6ad1ff';cir(-2,-13,2.5);x.fill();
x.fillStyle='#8aff8a';cir(6,-10,2.5);x.fill();break;
case 'fireworks':x.strokeStyle='#ffb45e';x.lineWidth=2.5;
for(var wi=0;wi<8;wi++){var wa=wi/8*Math.PI*2;
x.beginPath();x.moveTo(Math.cos(wa)*5,Math.sin(wa)*5);x.lineTo(Math.cos(wa)*14,Math.sin(wa)*14);x.stroke();}
x.fillStyle='#ff8ae0';cir(0,0,3);x.fill();break;
case 'disco':x.fillStyle='#cfd4dd';cir(0,0,12);x.fill();
x.strokeStyle='#5a6270';x.lineWidth=1.5;
x.beginPath();x.moveTo(-12,0);x.lineTo(12,0);x.moveTo(0,-12);x.lineTo(0,12);x.stroke();break;
case 'boombox':x.fillStyle='#ffb45e';x.fillRect(-15,-8,30,18);
x.fillStyle='#0d0805';cir(-8,1,5);x.fill();cir(8,1,5);x.fill();break;
case 'balloons':x.fillStyle='#ff6a6a';cir(-8,-6,6);x.fill();
x.fillStyle='#ffd166';cir(2,-9,6);x.fill();
x.fillStyle='#6ad1ff';cir(9,-3,6);x.fill();
x.strokeStyle='#ffd9a8';x.lineWidth=1.5;
x.beginPath();x.moveTo(-8,0);x.lineTo(0,14);x.moveTo(2,-3);x.lineTo(0,14);x.moveTo(9,3);x.lineTo(0,14);x.stroke();break;
case 'tramp':x.strokeStyle='#ffb45e';x.lineWidth=3;x.beginPath();x.ellipse(0,-2,14,6,0,0,7);x.stroke();
x.beginPath();x.moveTo(-10,2);x.lineTo(-12,12);x.moveTo(10,2);x.lineTo(12,12);x.stroke();break;
case 'lighter':x.fillStyle='#8a2020';x.fillRect(-6,-6,12,20);
x.fillStyle='#c8c0b0';x.fillRect(-6,-10,12,5);
x.fillStyle='#ffc86a';tri(0,-20,5,-9,-5,-9);x.fill();
x.fillStyle='#8ac0ff';tri(0,-15,2.5,-9,-2.5,-9);x.fill();break;
case 'console':x.fillStyle='#2e2a34';x.fillRect(-15,-6,30,16);
x.fillStyle='#ffd9a8';x.fillRect(-10,-2,3,8);x.fillRect(-12.5,1,8,3);
x.fillStyle='#ff6a6a';cir(8,0,2.5);x.fill();
x.fillStyle='#6ad1ff';cir(12,4,2.5);x.fill();break;
case 'plane':x.fillStyle='#f0e8d8';tri(-14,8,14,-2,-4,-4);x.fill();
x.fillStyle='#c8bda8';tri(-4,-4,14,-2,2,-12);x.fill();break;
case 'cocoa':x.fillStyle='#b0563a';x.fillRect(-9,-4,18,16);
x.strokeStyle='#b0563a';x.lineWidth=3;x.beginPath();x.arc(11,3,5,-1.2,1.2);x.stroke();
x.strokeStyle='#f5e8d8';x.lineWidth=2;
x.beginPath();x.moveTo(-4,-8);x.quadraticCurveTo(-6,-12,-4,-16);
x.moveTo(3,-8);x.quadraticCurveTo(1,-12,3,-16);x.stroke();break;
case 'glowstick':x.save();x.rotate(.7);
x.fillStyle='#66ffcc';x.fillRect(-4,-14,8,28);
x.fillStyle='rgba(102,255,204,.35)';x.fillRect(-8,-18,16,36);x.restore();break;
case 'whoopee':x.fillStyle='#b03030';x.beginPath();x.ellipse(0,2,14,7,0,0,7);x.fill();
x.fillRect(10,-2,8,4);
x.fillStyle='rgba(255,255,255,.25)';x.beginPath();x.ellipse(-4,-1,6,2.5,0,0,7);x.fill();break;
case 'eight':x.fillStyle='#141414';cir(0,0,13);x.fill();
x.fillStyle='#f5f5f0';cir(0,0,7);x.fill();
x.fillStyle='#141414';x.font='700 12px "Space Grotesk"';x.textAlign='center';x.textBaseline='middle';
x.fillText('8',0,1);break;
case 'ufo':x.fillStyle='#8a92a8';x.beginPath();x.ellipse(0,2,15,5,0,0,7);x.fill();
x.fillStyle='#9fd8e8';x.beginPath();x.arc(0,-1,7,Math.PI,0);x.fill();
x.fillStyle='#66ffd8';cir(-9,4,2);x.fill();cir(0,6,2);x.fill();cir(9,4,2);x.fill();break;
default:x.fillStyle='#ffb45e';cir(0,0,10);x.fill();}
x.restore();}
function drawScreen(t){var x=shopCtx,W=512,H=288,i;
if(shopOpen){
x.fillStyle='#100a06';x.fillRect(0,0,W,H);
x.fillStyle='#ffb45e';x.fillRect(0,0,W,4);x.fillRect(0,H-4,W,4);
x.textAlign='left';x.textBaseline='alphabetic';
x.fillStyle='#ffd9a8';x.font='600 24px "Space Grotesk"';
x.fillText('N I G H T   S U P P L I E S',24,38);
x.fillStyle='#a08a72';x.font='18px "Space Grotesk"';
x.fillText('shared wallet  \u25C8 '+Math.floor(shared.money)+'      +'+incomeRate()+'/min',24,66);
var it2=ITEMS[shopIdx];
x.fillStyle='#1c120a';x.fillRect(24,86,W-48,150);
x.strokeStyle='rgba(255,180,94,.4)';x.lineWidth=2;x.strokeRect(24,86,W-48,150);
x.fillStyle='rgba(255,180,94,.08)';x.beginPath();x.arc(420,161,44,0,7);x.fill();
x.strokeStyle='rgba(255,180,94,.35)';x.beginPath();x.arc(420,161,44,0,7);x.stroke();
dIcon(x,it2.id,420,161);
x.fillStyle='#ffb45e';x.font='700 30px "Space Grotesk"';
x.fillText(it2.name.toUpperCase(),44,128);
x.fillStyle='#d8c6ac';x.font='italic 19px Georgia';
x.fillText(it2.desc.length>44?it2.desc.slice(0,43)+'\u2026':it2.desc,44,162);
var own2=shared.owned[it2.id]&&!it2.inst;
var st2=own2?'OWNED — ROOM-WIDE'
:'\u25C8 '+it2.price+'   \u2014   press the amber button';
x.fillStyle=own2?'#9fd8a8':(shared.money>=it2.price?'#ffb45e':'#8d5a3a');
x.font='700 24px "Space Grotesk"';x.fillText(st2,44,206);
x.fillStyle='#8d7a63';x.font='17px "Space Grotesk"';
x.fillText('\u25C0 / \u25B6 buttons on the monitor   '+(shopIdx+1)+'/'+ITEMS.length+'   server-verified',24,266);
screenTex.needsUpdate=true;return;}
x.fillStyle='#14100c';x.fillRect(0,0,W,H);
if(ch===0){
x.save();x.translate(110,140);x.rotate(t*1.2);
x.fillStyle='#0d0a08';x.beginPath();x.arc(0,0,78,0,7);x.fill();
x.strokeStyle='rgba(255,255,255,.07)';
for(var r=20;r<76;r+=7){x.beginPath();x.arc(0,0,r,0,7);x.stroke();}
x.fillStyle='#e8a04c';x.beginPath();x.arc(0,0,24,0,7);x.fill();
x.restore();
x.fillStyle='#f0e2c8';x.font='italic 30px Georgia';x.fillText('midnight radiator',218,100);
x.fillStyle='#9a8468';x.font='16px Georgia';x.fillText('warm isolation fm',220,128);
for(i=0;i<14;i++){var h=8+Math.abs(Math.sin(t*3+i*1.7))*34;
x.fillStyle='#e8a04c';x.fillRect(222+i*15,210-h,9,h);}}
else if(ch===1){
x.fillStyle='#0d0f0a';x.fillRect(0,0,W,H);
x.font='15px "Courier New",monospace';
var live=termLines.slice();
live.splice(3,0,'> friends.online ....... '+(1+peerNames().length));
live.splice(5,0,'> wallet.balance ....... \u25C8 '+Math.floor(shared.money));
var off=Math.floor(t*.5);
for(i=0;i<9;i++){x.fillStyle='rgba(216,192,112,'+(i===8?.95:.35+i*.07)+')';
x.fillText(live[(i+off)%live.length],26,40+i*27);}}
else{
var g=x.createLinearGradient(0,0,0,H);
g.addColorStop(0,'#0a1e33');g.addColorStop(1,'#05101f');
x.fillStyle=g;x.fillRect(0,0,W,H);
for(i=0;i<12;i++){var by=H-((t*30+i*47)%(H+40)),bx=(i*43)%W+Math.sin(t+i)*8;
x.fillStyle='rgba(180,220,255,.35)';x.beginPath();x.arc(bx,by,2+(i%3),0,7);x.fill();}
fishArr.forEach(function(f){var fx=((t*f.sp+f.ph)%(W+120))-60,fy=f.y+Math.sin(t*2+f.ph)*10;
x.fillStyle=f.col;
x.beginPath();x.ellipse(fx,fy,22*f.s,10*f.s,0,0,7);x.fill();});}
screenTex.needsUpdate=true;}
drawScreen(0);
/* ===== the demon — only a LIT lighter held close keeps it away ===== */
var demonG=new THREE.Group();demonG.visible=false;scene.add(demonG);
var dBody=new THREE.Mesh(new THREE.ConeGeometry(.35,1.6,8),
new THREE.MeshStandardMaterial({color:0x050505,roughness:1}));
dBody.position.y=.8;demonG.add(dBody);
var dEyeM=new THREE.MeshBasicMaterial({color:0xff2020});dEyeM.toneMapped=false;
[-1,1].forEach(function(s){var e=new THREE.Mesh(new THREE.SphereGeometry(.035,6,6),dEyeM);
e.position.set(s*.09,1.35,-.28);demonG.add(e);});
var demonGlowSpr=glowChild(demonG,0xff2010,1.8,1.0,0);
var camFlash=new THREE.Sprite(new THREE.SpriteMaterial({map:glowTex,color:0xff2010,transparent:true,opacity:0,depthTest:false}));
camFlash.scale.set(1.8,1.8,1);camFlash.position.set(0,0,-.8);camFlash.renderOrder=1200;camera.add(camFlash);
var demonT=-1,demonCd=0,flashT=0,warned4=false,demonDanger=0,demonWarned=false;
/* ===== main loop ===== */
var cWarm=new THREE.Color(0xffd9a0);
var snapLast=0,lastScreen=0,phoneDrawT=0,lookTarget=new THREE.Vector3();
var ledgerT=0,lastMoneyShown=-1,squeakT=0,sizzleT=0;
applyOwned();drawCounter();drawLedger();drawHud();drawPhone();drawTV();
renderer.setAnimationLoop(function(){
var dt=Math.min(clock.getDelta(),.05),t=clock.elapsedTime;
var presenting=renderer.xr.isPresenting;
camera.getWorldPosition(camPos);
camera.getWorldDirection(camDir);
if(isHost()&&net.ok){shared.money+=incomeRate()/60*dt;
stateTimer-=dt;if(stateTimer<=0){stateTimer=2;publishState();}}
if(isHost()&&net.ok&&shared.pizza.st==='ordered'&&Date.now()>shared.pizza.due){
var poid='pz'+(++hostSeq)+myId.slice(0,3);
spawnPizzaBox(shared.pizza.size,poid,false);
netPublishEvent({k:'pizzaLand',o:poid,size:shared.pizza.size});
shared.pizza={st:'idle',due:0,size:'medium'};publishState();
toast('knock knock\u2026 the pizza is at the door. no one is there.');}
if(Math.floor(shared.money)!==lastMoneyShown){lastMoneyShown=Math.floor(shared.money);
drawCounter();coinTick();drawHud();}
bellCd=Math.max(0,bellCd-dt);hornCd=Math.max(0,hornCd-dt);
confCd=Math.max(0,confCd-dt);fwCd=Math.max(0,fwCd-dt);fridgeCd=Math.max(0,fridgeCd-dt);
phoneTick(dt);
phoneDrawT+=dt;if(phoneDrawT>.12){phoneDrawT=0;drawPhone();}
orderBtns.forEach(function(b,i){
if(phone.state==='menu'){b.g.visible=true;
b.mat.emissiveIntensity=1+Math.sin(t*4+b.ph)*.7;
var ps=1+Math.sin(t*4+b.ph)*.08;b.g.scale.set(ps,1,ps);}
else if(b.g.visible&&phone.state!=='ordered')b.g.visible=false;});
if(shopOpen){buyBtnMat.emissiveIntensity=.9+Math.sin(t*3)*.5;
var bs=1+Math.sin(t*3)*.05;shopBtnBuy.g.scale.set(bs,1,bs);}
else buyBtnMat.emissiveIntensity=.55;
doorK+=((S.door?1:0)-doorK)*Math.min(1,dt*1.8);
doorPivot.rotation.y=doorK*1.85;
/* ground, floors, one-time height calibration */
var gH=groundAt(rig.position.x,rig.position.z);
curFloor=1+Math.max(0,Math.round(-gH/ST.drop));
if(curFloor!==lastFloor){lastFloor=curFloor;drawHud();}
rigYT=(bodyMode?poseBaseY:0)+gH;
rigY+=(rigYT-rigY)*Math.min(1,dt*10);
if(presenting&&!bodyMode){
if(!heightLocked){heightFrames++;
if(heightFrames>20){heightOff=clamp(1.6-camera.position.y,-.7,.7);heightLocked=true;}}
if(shared.owned.tramp&&gH===0){
var tdx=rig.position.x+2.45,tdz=rig.position.z+.5;
if(tdx*tdx+tdz*tdz<.3&&t-trampLast>.6){trampLast=t;boingS();trampK=1;
hapticPulse(ctl0.userData.inputSource,.5,90);hapticPulse(ctl1.userData.inputSource,.5,90);}}}
if(poseTween){poseTween.t+=dt;
var pk=easeOut(Math.min(1,poseTween.t/poseTween.d));
rig.position.lerpVectors(poseTween.p0,poseTween.p1,pk);
rig.rotation.y=lerp(poseTween.r0,poseTween.r1,pk);
if(poseTween.t>=poseTween.d)poseTween=null;}
else rig.position.y=rigY+heightOff;
if(slipT>0)slipT=Math.max(0,slipT-dt);
if(trampK>0){trampK=Math.max(0,trampK-dt*3);trampG.scale.y=1-trampK*.5;}
else if(trampG)trampG.scale.y=1;
/* bathroom life */
if(bathFilling){bathFill=Math.min(1,bathFill+dt*.25);
if(bathFill>=1){bathFilling=false;toast('tub\u2019s full. perfect temperature, as always.');}}
if(bathDraining){bathFill=Math.max(0,bathFill-dt*.35);if(bathFill<=0)bathDraining=false;}
tubWaterBox.scale.y=Math.max(.04,bathFill);
tubWaterBox.position.y=.12+.2*bathFill;
tubWaterBox.material.opacity=bathFill>.05?.92:0;
var steamOn=(bathFill>.55)||showerOn;
bathSteam1.material.opacity=steamOn?(.16+.06*Math.sin(t*2.6)):0;
bathSteam2.material.opacity=steamOn?(.14+.05*Math.sin(t*2.1+2)):0;
bathSteam1.position.y=1.0+Math.sin(t*.9)*.08;
bathSteam2.position.y=1.05+Math.sin(t*.7+1)*.08;
showerSteam.material.opacity=showerOn?(.2+Math.sin(t*3)*.06):0;
if(washT>0){washT-=dt;drumG.rotation.z+=dt*9;
washSlosh-=dt;if(washSlosh<=0){washSlosh=1.1;if(AC)noiseHit(AC.currentTime,.3,700,.04);}
if(washT<=0){bellRing();toast('ding. warm towels. the machine accepts your thanks.');
if(!washPaid){washPaid=true;netPublishEvent({k:'req',a:'earn',a2:2});}}}
else drumG.rotation.z*=Math.max(0,1-dt*2);
candleLight.intensity=candleOn?(.7+.2*Math.sin(t*13)+.1*Math.sin(t*31)):0;
candleGlowSpr.material.opacity=candleOn?.8:0;
if(candleOn)candleFlame.scale.set(1,.85+.25*Math.sin(t*19),1);
if(!mirrorFog&&fogBackT>0){fogBackT-=dt;if(fogBackT<=0){mirrorFog=true;drawFog();}}
if(rimDuckWob>0){rimDuckWob=Math.max(0,rimDuckWob-dt*1.5);
rimDuck.rotation.z=Math.sin(t*25)*.3*rimDuckWob;}
/* kitchen — the burner cooks */
burnerMat.emissiveIntensity=burnerOn?(.8+.3*Math.sin(t*9)):0;
stoveLight.intensity=burnerOn?(1.2+.4*Math.sin(t*11)):0;
if(burnerOn&&potState==='raw'&&potContents.length>=2){
cookT+=dt;
sizzleT-=dt;if(sizzleT<=0){sizzleT=.4;sizzleS();}
potSteam.material.opacity=.3+.2*Math.sin(t*5);
potSteam.position.y=1.25+Math.sin(t*2)*.05;
potLiquid.material.color.set(0xb05a2a);
if(cookT>5){potState='done';potDish=matchRecipe(potContents);burnerOn=false;
chime();toast('ding! the pot holds a '+potDish+'. trigger the pot to serve it.');
potLiquid.material.color.set(DISH_COL[potDish]||0x8a7a5a);}}
else if(!burnerOn){potSteam.material.opacity=0;}
/* hallway lights follow you, and dim as you descend */
var darkF=Math.pow(.62,curFloor-1);
var lz=clamp(rig.position.z,3.5,ST.zEnd+4);
hallLightA.position.set(-1.5,Math.min(2.35,gH+2.5),lz+2.5);
hallLightB.position.set(-1.5,Math.min(2.35,gH+2.5),lz-3.5);
var hi2=1.3*darkF*(.88+.09*Math.sin(t*17)+.05*Math.sin(t*41));
hallLightA.intensity=hi2;hallLightB.intensity=hi2*.8;
var holdLight=false;
for(var hli=0;hli<physObjs.length;hli++){var lobj=physObjs[hli];
if(lobj.type==='lighter'&&!lobj.dead&&lobj.held&&lobj.flameOn!==false){holdLight=true;break;}}
rigLight.intensity=holdLight?(.85+.1*Math.sin(t*21)+.05*Math.sin(t*53)):0;
/* the demon */
demonCd=Math.max(0,demonCd-dt);
var safe=nearLitLighter();
if(presenting&&state==='xr'&&!bodyMode&&rig.position.z>ST.z0+1){
if(curFloor>=4&&!warned4){warned4=true;whisperS();
toast('the dark below is breathing. floor 5 is its mouth. carry a LIT lighter — it is the only thing it fears.');}
if(curFloor<2)warned4=false;
if(curFloor>=5&&demonCd<=0&&demonT<0){
if(!safe){demonDanger+=dt;
if(demonDanger>1&&!demonWarned){demonWarned=true;whisperS();
toast('the flame is not near you. it is coming. LIGHT IT. NOW.');
hapticPulse(ctl0.userData.inputSource,.4,200);hapticPulse(ctl1.userData.inputSource,.4,200);}
if(demonDanger>3.5){demonT=0;demonS();
hapticPulse(ctl0.userData.inputSource,.6,300);hapticPulse(ctl1.userData.inputSource,.6,300);}}
else{demonDanger=Math.max(0,demonDanger-dt*2);demonWarned=false;}}}
if(demonT>=0){demonT+=dt;
var dp=Math.min(1,demonT/1.5);
demonG.visible=true;
demonG.position.set(rig.position.x+Math.sin(t*15)*.06*(1-dp),
groundAt(rig.position.x,rig.position.z+2.5)+lerp(0,.4,dp),
rig.position.z+lerp(3.4,.8,dp));
demonG.rotation.y=Math.atan2(rig.position.x-demonG.position.x,rig.position.z-demonG.position.z);
demonG.scale.setScalar(lerp(.6,1.25,dp));
demonGlowSpr.material.opacity=dp*.8;
if(demonT>=1.5){demonT=-1;demonCd=8;demonG.visible=false;
flashT=.5;demonS();demonDanger=0;demonWarned=false;
hapticPulse(ctl0.userData.inputSource,.9,400);hapticPulse(ctl1.userData.inputSource,.9,400);
rig.position.set(-1.5,0,29.4);rigY=0;
toast('cold fingers closed around your ankle. you are back on floor 1. keep the lighter LIT and close.');}}
else if(safe||curFloor<5){demonG.visible=false;}
if(flashT>0){flashT-=dt;camFlash.material.opacity=Math.max(0,flashT/.5)*.95;}
else camFlash.material.opacity=0;
if(rig.position.z>LOB+2&&!lobbySeen){lobbySeen=true;chime();
toast('the lobby. impossibly warm. nothing follows you down here.');}
if(rig.position.z<LOB-5)lobbySeen=false;
/* lighter flames flicker; one shared light follows the nearest lit one */
var anyFlame=false;
for(var lfi=0;lfi<physObjs.length;lfi++){var fo=physObjs[lfi];
if(fo.type==='lighter'&&!fo.dead&&fo.flameG){
var fon=fo.flameOn!==false;
fo.flameG.visible=fon;
if(fon){var fk=.85+.15*Math.sin(t*23+lfi)+.08*Math.sin(t*57);
fo.flameG.scale.set(1,fk*.9+.1,1);
if(!anyFlame){anyFlame=true;
lighterLight.position.copy(fo.m.position);lighterLight.position.y+=.12;
lighterLight.intensity=(fo.held||fo.heldPeer?1.6:1.0)*fk;}}}}
if(!anyFlame)lighterLight.intensity=0;
for(var bci2=0;bci2<beacons.length;bci2++)beacons[bci2].visible=Math.sin(t*2+bci2*1.7)>0;
if(state==='lobby'&&!presenting){menuT+=dt;
camera.position.set(clamp(.5+Math.sin(menuT*.13)*1.6,-2.6,2.6),
1.45+Math.sin(menuT*.09)*.25,
clamp(1.1+Math.cos(menuT*.1)*1.2,-2.1,2.1));
lookTarget.set(-1+Math.sin(menuT*.06)*.9,1.05+Math.sin(menuT*.043)*.15,-1.4);
camera.lookAt(lookTarget);}
physStep(dt,t);
stepConfetti(dt);
for(var fi=fwQueue.length-1;fi>=0;fi--){if(t>=fwQueue[fi]){fwQueue.splice(fi,1);
v3.set(rand(-9,-5),rand(3.5,6.5),-.4+rand(-3,3));
burst(v3,60,3,.3,true);boom();
fwLight.position.copy(v3);fwLight.intensity=7;}}
fwLight.intensity=Math.max(0,fwLight.intensity-dt*14);
for(var pk2 in net.peers)updAvatar(net.peers[pk2],dt,t);
updateSelfBody(dt,t);
if(tvOn&&consoleG){pongStep(dt);
if(t-lastTV>.05){drawTV();lastTV=t;}
tvLight.intensity=.5+(pong.st==='play'?.35:0)+Math.sin(t*37)*.05;}
else if(tvLight)tvLight.intensity=0;
if(presenting&&state==='xr'){
if(tvOn&&pong.st==='play'){
var srcY=null;
if(hand0.userData.active&&hand0.joints&&hand0.joints['wrist']&&hand0.joints['wrist'].visible)srcY=hand0.joints['wrist'];
else if(ctl0.visible)srcY=ctl0;
if(srcY){pv2.setFromMatrixPosition(srcY.matrixWorld);
pong.py=clamp((pv2.y-.75)/.7,0,1);}}
var leftAxes=null,rightAxes=null,session=renderer.xr.getSession();
if(session){for(var si=0;si<session.inputSources.length;si++){
var src=session.inputSources[si],gp=src.gamepad;
if(gp&&gp.axes&&gp.axes.length>=4){
if(src.handedness==='right')rightAxes=gp.axes;else leftAxes=gp.axes;}}}
if(!leftAxes&&rightAxes){leftAxes=rightAxes;rightAxes=null;}
if(tvOn&&pong.st==='play'&&leftAxes&&Math.abs(leftAxes[3])>.2&&!ctl0.visible)
pong.py=clamp(pong.py-leftAxes[3]*dt*1.6,0,1);
if(rightAxes&&Math.abs(rightAxes[2])>.75&&t-snapLast>.35){
rig.rotation.y-=Math.sign(rightAxes[2])*Math.PI/6;snapLast=t;}
if(leftAxes&&!bodyMode&&(Math.abs(leftAxes[2])>.2||Math.abs(leftAxes[3])>.2)){
var fwd=new THREE.Vector3();camera.getWorldDirection(fwd);fwd.y=0;fwd.normalize();
var rightV=new THREE.Vector3().crossVectors(fwd,UP);
var mv=new THREE.Vector3().addScaledVector(fwd,-leftAxes[3]).addScaledVector(rightV,leftAxes[2]);
if(mv.lengthSq()>1)mv.normalize();
var spd=(shared.owned.sprint?2.5:1.8)*(shared.owned.boots?1.1:1);
rig.position.addScaledVector(mv,spd*dt);
collide(rig.position);
if(shared.owned.boots){squeakT-=dt;if(squeakT<=0){squeakT=.45;squeak();}}}
var best=null,bestD=4;
[ctl0,ctl1].forEach(function(c){if(!c.visible)return;
tmpM.identity().extractRotation(c.matrixWorld);
rc.ray.origin.setFromMatrixPosition(c.matrixWorld);
rc.ray.direction.set(0,0,-1).applyMatrix4(tmpM);
var hits=rc.intersectObjects(hitMeshes,true);
var found=null;
for(var hi=0;hi<hits.length;hi++){
var obj=hits[hi].object;while(obj&&obj.userData.ix===undefined)obj=obj.parent;
if(!obj)continue;
var ixx=interactables[obj.userData.ix];
if(ixx.enabled!==false&&!ixx.dead&&ixx.hit.parent){found={ix:ixx,d:hits[hi].distance,point:hits[hi].point};break;}}
if(found&&found.d<4){c.userData.hit=found.ix;c.userData.ray.scale.z=found.d;
c.userData.tip.position.set(0,0,-found.d);
if(found.d<bestD){bestD=found.d;best=found;}}
else{c.userData.hit=null;c.userData.ray.scale.z=3;c.userData.tip.position.set(0,0,-3);}
if(c.userData.hit!==c.userData.lastHit){
if(c.userData.hit)hapticPulse(c.userData.inputSource,.15,30);
c.userData.lastHit=c.userData.hit;}});
var hBest=null;
[hand0,hand1].forEach(function(h){var r=handHover(h);if(r&&!hBest)hBest=r;});
if(best)setLabel(typeof best.ix.label==='function'?best.ix.label():best.ix.label,best.point);
else if(hBest)setLabel(typeof hBest.ix.label==='function'?hBest.ix.label():hBest.ix.label,hBest.point);
else labelSpr.visible=false;
/* reach glow — grabbable things near your hands turn BLUE */
var reachO=null,rd=.45;
[ctl0,ctl1].forEach(function(c){if(!c.visible)return;c.getWorldPosition(pv2);
var o=nearestGrabbable(pv2,rd);if(o){rd=o.m.position.distanceTo(pv2);reachO=o;}});
[hand0,hand1].forEach(function(h){if(!h.userData.active||!h.joints)return;
var w=h.joints['wrist'];if(!w||!w.visible)return;w.getWorldPosition(pv2);
var o=nearestGrabbable(pv2,rd);if(o){rd=o.m.position.distanceTo(pv2);reachO=o;}});
setHighlight(reachO);
if(reachO){reachSpr.material.opacity=.35+.2*Math.sin(t*6);
reachSpr.position.copy(reachO.m.position);}
else reachSpr.material.opacity=0;
hudSpr.visible=true;
}else{hudSpr.visible=false;labelSpr.visible=false;}
/* ambience */
if(catWoke)catK=Math.min(1,catK+dt/2.5);
catBody.scale.y=lerp(.6,.82,catK)*(1+(catK>0?.02*Math.sin(t*2.2):.035*Math.sin(t*1.7)));
catHead.position.y=lerp(.22,.42,catK);
catHead.position.z=lerp(.12,.22,catK);
tail.rotation.z=2.4+(catK>0?Math.sin(t*1.6)*.35:0);
if(!catWoke){
if(twitchT>=0){twitchT+=dt;
earG[0].rotation.z=Math.sin(Math.min(1,twitchT/.35)*Math.PI)*.3;
if(twitchT>.35)twitchT=-1;}
else if(t>nextTwitch){twitchT=0;nextTwitch=t+5+Math.random()*8;}
zzz.forEach(function(s){var p=(t*.22+s.userData.ph)%1;
s.position.set(.24+Math.sin(p*6)*.05,.42+p*.4,.14);
s.material.opacity=Math.sin(p*Math.PI)*.75;
var sc=.06+p*.06;s.scale.set(sc,sc,1);});}
else zzz.forEach(function(s){s.material.opacity=0;});
if(balloonG&&balloonG.visible)balloonG.children.forEach(function(bg3){
bg3.userData.bl.position.y=bg3.userData.baseY+Math.sin(t*.8+bg3.userData.ph)*.06;
bg3.rotation.z=Math.sin(t*.5+bg3.userData.ph)*.06;});
if(discoG&&discoG.visible&&discoOn){
discoG.rotation.y+=dt*1.6;
discoL1.color.setHSL((t*.13)%1,1,.55);
discoL2.color.setHSL((t*.13+.5)%1,1,.55);
discoL1.intensity=discoL2.intensity=1.4+Math.sin(t*8)*.4;
discoT-=dt;if(discoT<=0){discoT=.46;thump(52,AC?AC.currentTime:0,.14);}}
else{discoL1.intensity=0;discoL2.intensity=0;}
if(boomG&&boomG.visible&&boomOn){boomT-=dt;
if(boomT<=0){boomT=1.9;var mel=[220,277.2,329.6,277.2];
if(AC)mel.forEach(function(f,i){musicNote(f,AC.currentTime+i*.45);});}}
if(AC&&shared.owned.radio&&radioMesh.visible){radioT-=dt;
if(radioT<=0){radioT=rand(3,7);
tone([392,440,494,587][Math.floor(Math.random()*4)],AC.currentTime,1.6,.03);}}
if(cocoaG&&cocoaG.visible)steamSprs.forEach(function(s){
var fr=(t*.25+s.userData.ph)%1;
s.position.set(Math.sin(fr*6+s.userData.ph*9)*.02,.1+fr*.22,0);
s.material.opacity=Math.sin(fr*Math.PI)*.35;
var sc2=.05+fr*.05;s.scale.set(sc2,sc2,1);});
if(ufoG&&ufoG.visible){
ufoG.position.set(Math.cos(t*.35)*2.3,2.15+Math.sin(t*1.1)*.12,Math.sin(t*.35)*1.5);
ufoG.rotation.y=t*1.5;
ufoLamps.forEach(function(l){l.m.color.setHSL((t*.5+l.ph*.33)%1,1,.6);});}
var nowD=new Date(),sec=nowD.getSeconds()+nowD.getMilliseconds()/1000,
min=nowD.getMinutes()+sec/60,hr=(nowD.getHours()%12)+min/60;
handS.rotation.z=-sec/60*Math.PI*2;handM.rotation.z=-min/60*Math.PI*2;handH.rotation.z=-hr/12*Math.PI*2;
if(t-lastScreen>(PERF?.15:.08)){drawScreen(t);lastScreen=t;}
ledgerT+=dt;
if(ledgerT>(PERF?2:1)){ledgerT=0;drawLedger();drawHud();}
if(curFloor>=5)drawHud();
if(ringT>=0){ringT+=dt;
spawnRing.material.opacity=Math.max(0,.75*(1-ringT/4))*(0.7+0.3*Math.sin(t*3));
if(ringT>4)ringT=-1;}
vLamp+=((S.lamp?1:0)-vLamp)*Math.min(1,dt*8);
vFairy+=((S.fairy?1:0)-vFairy)*Math.min(1,dt*6);
vCeil+=((S.ceil?1:0)-vCeil)*Math.min(1,dt*7);
vCurt+=((S.curtains?1:0)-vCurt)*Math.min(1,dt*2.5);
/* every room light off = the building swallows you; the deeper you go, the thicker the dark */
var darkTgt=(S.lamp||S.fairy||S.ceil)?0:1;
vDark+=(darkTgt-vDark)*Math.min(1,dt*2.5);
hemi.intensity=lerp(.55,.035,vDark);
amb.intensity=lerp(PERF?.95:.8,.03,vDark);
kitchenLight.intensity=lerp(1.5,.2,vDark);
kitchenGlowSpr.material.opacity=.6*(1-vDark*.85);
scene.fog.density=.045+clamp((rig.position.z-24)/70,0,1)*.055;
var flick=.92+.05*Math.sin(t*11.3)+.03*Math.sin(t*23.7);
var lampI=1.7*vLamp*flick;
if(burstT>0){burstT-=dt;lampI+=Math.random()*2.4*vLamp;}
deskLight.intensity=lampI;
deskLight.color.copy(warmCol).lerp(goldCol,shared.owned.gold?.7:0);
lampBulbMat.color.copy(cWarm).multiplyScalar(Math.max(.1+.9*vLamp,0));
lampGlowSpr.material.opacity=.9*vLamp;
allBulbs.forEach(function(b){var k=vFairy*(.75+.25*Math.sin(t*5+b.ph));
b.mat.color.copy(b.base).multiplyScalar(clamp(.05+.95*k,0,1.4));
b.sm.opacity=clamp(.9*k,0,1);});
fairyL1.intensity=.9*vFairy;
if(!PERF)fairyL2.intensity=.8*vFairy;
spotCeil.intensity=2.4*vCeil;
ceilBulbMat.color.copy(cWarm).multiplyScalar(.08+.92*vCeil);
ceilGlowSpr.material.opacity=.85*vCeil;
ceilG.rotation.x=Math.sin(t*.6)*.015;ceilG.rotation.z=Math.cos(t*.5)*.015;
if(!PERF&&monLight)monLight.intensity=.85+Math.sin(t*31)*.04;
var vLv=S.lava?1:0;
liquidMat.emissiveIntensity=1.5*vLv;
glassMat.opacity=.18+.14*vLv;
lavaLight.intensity=1.3*vLv*(.9+.1*Math.sin(t*2.2));
blobs.forEach(function(b){b.visible=vLv>.05;
var u=b.userData,k=.5+.5*Math.sin(t*u.sp+u.ph);
b.position.set(lavaAnchor.x+u.ox*Math.sin(t*.7+u.ph),lavaAnchor.y+k*.22,lavaAnchor.z+u.oz*Math.cos(t*.6+u.ph));
b.scale.setScalar(1+.3*Math.sin(t*1.3+u.ph));});
curtA.position.set(-3.06,1.5,lerp(ZA.open,ZA.closed,vCurt));curtA.scale.x=lerp(.42,1,vCurt);
curtB.position.set(-3.06,1.5,lerp(ZB.open,ZB.closed,vCurt));curtB.scale.x=lerp(.42,1,vCurt);
streakTex.offset.y-=dt*.02;
moon.intensity=lerp(lerp(.95,.12,vCurt),.05,vDark);
if(!PERF)moonFill.intensity=(1-vCurt*.85)*(1-vDark*.92);
if(AC&&rainGain)rainGain.gain.value=S.curtains?.06:.13;
if(AC&&showerGain)showerGain.gain.value=showerOn?.06:0;
showerPts.material.opacity=showerOn?.7:0;
if(showerOn){var sp2=shGeo.attributes.position.array;
for(var sj=0;sj<SHN;sj++){sp2[sj*3+1]-=dt*3.2;
if(sp2[sj*3+1]<.15)sp2[sj*3+1]=2.25;}
shGeo.attributes.position.needsUpdate=true;}
var rp=rainGeo.attributes.position.array;
for(var rri=0;rri<RN;rri++){var y=rp[rri*6+1]-rSp[rri]*dt;
if(y<0){y=3.4;
var nx=-4.8+Math.random()*1.4,nz=-1.7+Math.random()*2.45;
rp[rri*6]=nx;rp[rri*6+3]=nx;rp[rri*6+2]=nz;rp[rri*6+5]=nz;}
rp[rri*6+1]=y;rp[rri*6+4]=y-rLen[rri];}
rainGeo.attributes.position.needsUpdate=true;
var dp2=dustGeo.attributes.position.array;
for(var ddi=0;ddi<DN;ddi++){
dp2[ddi*3+1]+=dSp[ddi]*dt;
dp2[ddi*3]+=Math.sin(t*.5+dPh[ddi])*dt*.03;
if(dp2[ddi*3+1]>2.8)dp2[ddi*3+1]=.25;}
dustGeo.attributes.position.needsUpdate=true;
if(AC&&tickTimer!==null){tickTimer-=dt;
if(tickTimer<=0){tickTimer=.06+Math.random()*.3;
tone(1400+Math.random()*3200,AC.currentTime,.06,.012+Math.random()*.03);}}
renderer.render(scene,camera);
if(!booted){booted=true;drawLedger();drawCounter();drawHud();drawPhone();}
});
addEventListener('resize',function(){
camera.aspect=innerWidth/innerHeight;camera.updateProjectionMatrix();
renderer.setSize(innerWidth,innerHeight);});
}
})();
</script>
</body>
</html>
