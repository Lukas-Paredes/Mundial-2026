"use strict";
/* ---------- state ---------- */
const KEY="albumMundial2026_v2";
let state = load();
function load(){ try{ const r=JSON.parse(localStorage.getItem(KEY)); if(r&&r.counts) return normState(r); }catch(e){} return normState({}); }
const GROUP_PAGES={A:"8–15",B:"16–23",C:"24–31",D:"32–39",E:"40–47",F:"48–55",G:"58–65",H:"66–73",I:"74–81",J:"82–89",K:"90–97",L:"98–105"};
const SPECIAL_PAGES={"00":"1","FWC9":"106","FWC10":"106","FWC11":"106","FWC12":"107","FWC13":"107","FWC14":"107","FWC15":"108","FWC16":"108","FWC17":"108","FWC18":"109","FWC19":"109"};
function teamPageRange(code){
  if(typeof GROUP_PAGES==="undefined") return "";
  let g=null, idx=-1;
  for(const gr of GROUPS){ const i=gr.teams.findIndex(t=>t[0]===code); if(i>=0){ g=gr; idx=i; break; } }
  if(!g) return "";
  const gp=(state.config.groupPages&&state.config.groupPages[g.id])||GROUP_PAGES[g.id]||"";
  const m=gp.match(/\d+/); if(!m) return "";
  const start=parseInt(m[0],10)+idx*2;
  return start+"–"+(start+1);
}
/* ====== motor de sonido (Web Audio, sintetizado, sin archivos) ====== */
let _actx=null;
function audioCtx(){
  if(!_actx){ try{ _actx=new (window.AudioContext||window.webkitAudioContext)(); }catch(e){ return null; } }
  if(_actx && _actx.state==="suspended"){ _actx.resume().catch(()=>{}); }
  return _actx;
}
function _tone(freq,start,dur,type,vol){
  const c=audioCtx(); if(!c) return;
  const o=c.createOscillator(), g=c.createGain();
  o.type=type||"sine"; o.frequency.value=freq;
  const t=c.currentTime+start;
  g.gain.setValueAtTime(0.0001,t);
  g.gain.linearRampToValueAtTime(vol||0.16,t+0.012);
  g.gain.exponentialRampToValueAtTime(0.0001,t+dur);
  o.connect(g); g.connect(c.destination);
  o.start(t); o.stop(t+dur+0.03);
}
function soundOn(){ try{ return state.config.sound!==false; }catch(e){ return true; } }
function sfxPop(){ if(!soundOn()) return; _tone(660,0,0.10,"triangle",0.15); _tone(990,0.05,0.12,"triangle",0.12); }
function _brass(freq,start,dur,vol,cut){
  const c=audioCtx(); if(!c) return;
  const t=c.currentTime+start;
  const filt=c.createBiquadFilter(); filt.type="lowpass"; filt.frequency.value=cut||1850; filt.Q.value=0.6; // corta lo chillón
  const g=c.createGain();
  g.gain.setValueAtTime(0.0001,t);
  g.gain.linearRampToValueAtTime(vol,t+0.03);                 // ataque suave (como soplido)
  g.gain.setValueAtTime(vol,t+Math.max(0.05,dur*0.6));
  g.gain.exponentialRampToValueAtTime(0.0001,t+dur);
  filt.connect(g); g.connect(c.destination);
  [0,-7,7].forEach(cents=>{                                   // varios osc detune = cuerpo de metal cálido
    const o=c.createOscillator(); o.type="sawtooth"; o.frequency.value=freq;
    try{ o.detune.value=cents; }catch(e){}
    o.connect(filt); o.start(t); o.stop(t+dur+0.03);
  });
}
/* === fanfarrias medievales por tier (escalan en grandeza) === */
function _fanBronce(){   // 🥉 humilde y grave: 2 notas, cuarta ascendente
  _brass(196.00,0.00,0.16,0.13);
  _brass(261.63,0.16,0.36,0.14);
}
function _fanPlata(){    // 🥈 arpegio mayor ascendente, más luminoso
  _brass(261.63,0.00,0.14,0.13);
  _brass(329.63,0.15,0.14,0.13);
  _brass(392.00,0.30,0.42,0.14);
}
function _fanOro(){      // 🥇 heraldo clásico de realeza (nota repetida + llegada + octava grave)
  _brass(523.25,0.00,0.14,0.13);
  _brass(523.25,0.16,0.14,0.13);
  _brass(523.25,0.32,0.14,0.13);
  _brass(392.00,0.48,0.16,0.13);
  _brass(523.25,0.66,0.50,0.15);
  _brass(261.63,0.66,0.50,0.10);
}
function _fanDiamante(){ // 💎 más largo, sube más alto + destello (filtro más abierto)
  _brass(392.00,0.00,0.12,0.12,2400);
  _brass(523.25,0.13,0.12,0.12,2400);
  _brass(659.25,0.26,0.12,0.12,2400);
  _brass(783.99,0.40,0.16,0.13,2400);
  _brass(523.25,0.60,0.55,0.14,2400);
  _brass(659.25,0.60,0.55,0.12,2400);
  _brass(261.63,0.60,0.55,0.09);
}
function _fanGoat(){     // 🐐 himno: build + acorde mayor enorme en tres octavas
  _brass(523.25,0.00,0.14,0.12,2400);
  _brass(523.25,0.16,0.14,0.12,2400);
  _brass(659.25,0.32,0.16,0.12,2400);
  _brass(783.99,0.50,0.20,0.13,2400);
  _brass(130.81,0.74,0.95,0.10);          // octava sub = peso
  _brass(261.63,0.74,0.95,0.11);
  _brass(329.63,0.74,0.95,0.11,2400);
  _brass(392.00,0.74,0.95,0.11,2400);
  _brass(523.25,0.74,0.95,0.13,2400);     // cima
}
function _swoop(f0,f1,start,dur,vol,cut){   // glissando ascendente (efecto, no voz)
  const c=audioCtx(); if(!c) return;
  const t=c.currentTime+start;
  const filt=c.createBiquadFilter(); filt.type="lowpass"; filt.frequency.value=cut||2600; filt.Q.value=0.6;
  const g=c.createGain();
  g.gain.setValueAtTime(0.0001,t);
  g.gain.linearRampToValueAtTime(vol,t+0.04);
  g.gain.setValueAtTime(vol,t+dur*0.62);
  g.gain.exponentialRampToValueAtTime(0.0001,t+dur);
  filt.connect(g); g.connect(c.destination);
  [0,-7,7].forEach(cents=>{
    const o=c.createOscillator(); o.type="sawtooth";
    try{ o.detune.value=cents; }catch(e){}
    o.frequency.setValueAtTime(f0,t);
    o.frequency.exponentialRampToValueAtTime(f1,t+dur*0.8);
    o.connect(filt); o.start(t); o.stop(t+dur+0.03);
  });
}
function _fanSiu(){      // CR7: subida tipo "siuuu" + golpe triunfal (homenaje sintetizado, no su voz)
  _swoop(330,880,0.00,0.52,0.16,2800);
  _brass(523.25,0.56,0.42,0.14,2400);
  _brass(392.00,0.56,0.42,0.11,2400);
  _brass(261.63,0.56,0.42,0.10);
}
function _fanMessi(){    // Messi: gambeta que acelera ("encara, encara…") y resuelve (homenaje sintetizado, no el relato)
  [0.00,0.14,0.26,0.36,0.44].forEach(st=>_brass(440.00,st,0.10,0.12,2200));
  _brass(587.33,0.56,0.48,0.15,2400);   // ¡definición!
  _brass(293.66,0.56,0.48,0.10);
}
function sfxTier(tier, code){
  if(!soundOn()) return;
  if(code==="POR15"){ _fanSiu();   return; }
  if(code==="ARG17"){ _fanMessi(); return; }
  switch(tier){
    case "bronce":   _fanBronce();   break;
    case "plata":    _fanPlata();    break;
    case "diamante": _fanDiamante(); break;
    case "goat":     _fanGoat();     break;
    default:         _fanOro();      // oro o cualquier crack sin tier explícito
  }
}
function sfxTeam(){      // ✅ equipo completo: carrera ascendente alegre (distinta al heraldo)
  if(!soundOn()) return;
  _brass(392.00,0.00,0.12,0.13);
  _brass(523.25,0.12,0.12,0.13);
  _brass(659.25,0.24,0.12,0.13);
  _brass(783.99,0.36,0.42,0.14,2400);
  _brass(261.63,0.36,0.42,0.09);
}
function sfxStar(){ sfxTier("oro"); }   // compatibilidad
function playClipOrSynth(code, tier){
  if(!soundOn()) return;
  let fell=false; const fallback=()=>{ if(fell) return; fell=true; sfxTier(tier, code); };
  let a; try{ a=new Audio("audio/"+encodeURIComponent(code)+".mp3"); }catch(e){ fallback(); return; }
  a.volume=0.95; a.addEventListener("error", fallback, {once:true});
  const pr=a.play(); if(pr && pr.catch) pr.catch(fallback);
}
["pointerdown","touchstart","keydown"].forEach(ev=>window.addEventListener(ev,()=>{ try{audioCtx();}catch(e){} },{once:true,passive:true}));

function normState(r){ r=r||{}; r.version=2; r.config=Object.assign({specials:true,extras:false,extraNames:{},specialPages:{},groupPages:{},teamsStartPage:0,nick:"",name:"",age:"",city:"",celebrate:true,sound:true,packPrice:1400,packSize:7}, r.config||{}); r.counts=r.counts||{}; if(r.counts){ Object.keys(r.counts).forEach(k=>{ const m=k.match(/^FW(\d{1,2})$/); if(m){ const nk="FWC"+m[1]; if(!r.counts[nk]) r.counts[nk]=r.counts[k]; delete r.counts[k]; } }); } return r; }
function save(){ try{ localStorage.setItem(KEY, JSON.stringify(state)); }catch(e){} scheduleCloudWrite(); scheduleDriveSync(); }
function getCount(c){ return state.counts[c]||0; }
function teamHave(code){ let h=0; for(let i=1;i<=20;i++) if(getCount(code+i)>0) h++; return h; }
function openTeamInfo(code){
  const meta=CODE_TEAM[code+"1"]; const info=TEAM_INFO[code]||{};
  const acc=TEAM_COLOR[code]||"#0067B9";
  document.getElementById("tiName").innerHTML=(meta?meta.flag+" ":"")+(meta?meta.name:code);
  const have=teamHave(code);
  const row=(l,v)=>'<div style="display:flex;justify-content:space-between;gap:12px;padding:11px 2px;border-bottom:1px solid var(--line)"><span style="color:var(--mut);font-weight:700;font-size:12.5px">'+l+'</span><span style="font-weight:700;color:var(--ink);text-align:right;max-width:64%">'+v+'</span></div>';
  let html='<div style="display:flex;gap:8px;flex-wrap:wrap;margin:2px 0 14px">';
  if(info.conf) html+='<span style="background:var(--surface2);border:1px solid var(--line2);border-radius:999px;padding:5px 12px;font-size:12px;font-weight:700;color:var(--ink2)">'+info.conf+'</span>';
  html+='<span style="background:rgba(0,128,43,.12);border:1px solid rgba(0,128,43,.3);border-radius:999px;padding:5px 12px;font-size:12px;font-weight:700;color:var(--ok-d)">📒 '+have+'/20 en tu álbum</span></div>';
  if(info.tab){
    const t=info.tab, pj=t.g+t.e+t.p, hd="padding:7px 0", cd="padding:9px 0";
    html+='<div style="font-size:11px;color:var(--mut);font-weight:800;text-transform:uppercase;letter-spacing:.05em;margin:2px 0 7px">Su clasificación'+(t.pos?' · '+t.pos:'')+(t.pts!=null?' · '+t.pts+' pts':'')+'</div>';
    html+='<div style="border:1px solid var(--line2);border-radius:12px;overflow:hidden;margin-bottom:14px">';
    html+='<div style="display:grid;grid-template-columns:repeat(6,1fr);background:var(--surface2);font-size:10px;font-weight:800;color:var(--mut);text-transform:uppercase;text-align:center"><div style="'+hd+'">PJ</div><div style="'+hd+'">G</div><div style="'+hd+'">E</div><div style="'+hd+'">P</div><div style="'+hd+'">GF</div><div style="'+hd+'">GC</div></div>';
    html+='<div style="display:grid;grid-template-columns:repeat(6,1fr);font-weight:800;font-size:15px;text-align:center;color:var(--ink)"><div style="'+cd+'">'+pj+'</div><div style="'+cd+'">'+t.g+'</div><div style="'+cd+'">'+t.e+'</div><div style="'+cd+'">'+t.p+'</div><div style="'+cd+';color:var(--ok-d)">'+t.gf+'</div><div style="'+cd+';color:var(--red)">'+t.gc+'</div></div>';
    html+='</div>';
  } else if(info.clas && info.clas.indexOf("Anfitrión")>=0){
    html+='<div style="font-size:12.5px;color:var(--mut);margin:2px 0 14px">Clasificó como anfitrión — no jugó eliminatorias. ✈️</div>';
  } else if(info.clas){
    html+='<div style="font-size:12.5px;color:var(--mut2);margin:2px 0 14px">📊 Tabla de su clasificación en camino.</div>';
  }
  if(info.clas) html+=row("Cómo clasificó", info.clas);
  if(info.fig) html+=row("Figura", info.fig);
  if(info.dato) html+='<div style="margin-top:14px;background:var(--surface2);border:1px solid var(--line);border-left:4px solid '+acc+';border-radius:12px;padding:12px 14px;font-size:13.5px;color:var(--ink2);line-height:1.5"><b style="color:'+acc+'">Dato:</b> '+info.dato+'</div>';
  if(!info.clas && !info.fig && !info.dato && !info.tab) html+='<div class="tip" style="text-align:left">Ficha de este equipo en camino. 🔜</div>';
  document.getElementById("tiBody").innerHTML=html;
  document.getElementById("ovTeam").classList.add("show");
}
function setCount(c,v){
  if(cloud.viewOnly) return;
  v=Math.max(0,v|0);
  const t=CODE_TEAM[c];
  const before = t ? teamHave(t.code) : 0;
  const wasZero = getCount(c)===0;
  if(v===0) delete state.counts[c]; else state.counts[c]=v;
  save();
  if(t){ const after=teamHave(t.code); if(before<20 && after===20) celebrateTeam(t.code, t.name); }
  if(wasZero && v>=1) maybeCelebrateStar(c);
}
function reduceMotion(){ try{ return window.matchMedia && window.matchMedia("(prefers-reduced-motion: reduce)").matches; }catch(e){ return false; } }
function launchConfetti(){
  if(reduceMotion()) return;
  const host=document.getElementById("confetti"); if(!host) return;
  const colors=["#D8232A","#F58220","#F2A20C","#00802B","#0067B9","#6A1B9A","#C2186A"];
  for(let i=0;i<90;i++){
    const p=document.createElement("i"); p.className="cft";
    const w=6+Math.random()*7;
    p.style.left=(Math.random()*100)+"vw";
    p.style.width=w+"px"; p.style.height=(w*1.5)+"px";
    p.style.background=colors[i%colors.length];
    p.style.animationDelay=(Math.random()*250)+"ms";
    p.style.animationDuration=(1500+Math.random()*900)+"ms";
    p.style.opacity=String(.8+Math.random()*.2);
    host.appendChild(p);
    setTimeout(()=>{ try{ host.removeChild(p); }catch(e){} }, 2700);
  }
}
let _celebrateTimer=null, _celebrateQueue=[];
const HYPE=["¡Genial!","¡Bkn!","¡Enhorabuena!","¡De pana!"];
let _hypeI=Math.floor(Math.random()*HYPE.length);
function hype(){ return HYPE[(_hypeI++)%HYPE.length]; }
function celebrateTeam(code,name){
  _celebrateQueue.push(name);
  clearTimeout(_celebrateTimer);
  _celebrateTimer=setTimeout(()=>{
    const names=_celebrateQueue.splice(0);
    launchConfetti(); sfxTeam();
    const t=document.getElementById("toast"); if(!t) return;
    t.textContent = names.length===1 ? ("¡Completaste "+names[0]+"! 👍🗿") : ("¡Completaste "+names.length+" equipos! 👍🗿");
    t.classList.add("show","party");
    clearTimeout(toastTimer);
    toastTimer=setTimeout(()=>{ t.classList.remove("show"); setTimeout(()=>t.classList.remove("party"),260); }, 2800);
  }, 80);
}

/* ---------- ¡Felicidades! cracks ---------- */
const STAR_FACTS={
  "POR12":"3º del mundo en el Balón de Oro 2025: el cerebro del PSG en el triplete histórico (Champions, Ligue 1 y Copa de Francia). Mejor mediocampista de la temporada. 🧠",
  "POR7":"Top 10 del Balón de Oro 2025 y mejor lateral izquierdo del mundo esa temporada; campeón de la Champions con el PSG y MVP de la final de la Nations League con Portugal. 🛡️",
  "FRA17":"Figura de la final de Champions 2025 con un doblete ante el Inter; subcampeón del Trofeo Kopa al mejor joven y campeón del triplete con el PSG a los 20 años. ✨",
  "POR14":"Joya del PSG campeón de Europa: 19º en el Balón de Oro 2025 y 3º en el Trofeo Kopa al mejor joven del mundo, con apenas 21 años. 🌟",
  "FRA15":"Balón de Oro 2025 y Mejor Jugador de la Champions. Lideró el triplete histórico del PSG (su 1ª Champions, Ligue 1 y Copa de Francia) con 35 goles. ⚡🇫🇷",
  "ARG17":"8 Balones de Oro (récord histórico), campeón del Mundial 2022, 4 Champions League y máximo goleador en la historia del Barcelona (~670 goles). El GOAT. 🐐",
  "POR15":"5 Balones de Oro, máximo goleador de la historia del fútbol (+900 goles oficiales), 5 Champions League y campeón de la Eurocopa 2016. 🚀",
  "FRA20":"Campeón del mundo 2018 y hat-trick en la final de 2022; máximo goleador histórico del PSG (~256) y varias veces Bota de Oro. ⚡",
  "NOR15":"Récord de goles en una temporada de Premier League (36) y triplete (Liga, Copa y Champions) con el City en 2023. Una máquina. 🤖",
  "BRA14":"Premio The Best de la FIFA 2024, campeón de la Champions 2024 (marcó en la final) y de LaLiga con el Real Madrid. 🇧🇷",
  "ESP15":"Goleador más joven en la historia de la Eurocopa (16 años), Trofeo Kopa al mejor joven y subcampeón del Balón de Oro 2025. 🤯",
  "ESP10":"¡Balón de Oro 2024! Campeón y mejor jugador de la Eurocopa 2024 y campeón de la Champions 2023 con el City. 🏆",
  "MAR4":"6º en el Balón de Oro 2025 (récord para Marruecos), campeón de la Champions con el PSG y semifinalista del Mundial 2022. ¡11 goles siendo lateral! 🚀",
  "EGY17":"Varias veces Bota de Oro de la Premier, campeón de la Champions 2019 y de la Premier; máximo goleador africano en la historia de la liga inglesa. 👑",
  "CRO9":"Balón de Oro 2018 (cortó la racha de Messi y Cristiano), 6 Champions League con el Real Madrid y finalista del Mundial 2018. 👑",
  "BEL15":"Dos veces Jugador del Año de la Premier League, campeón de la Champions del triplete con el City y rey de las asistencias. 🎩",
  "COL14":"Bota de Oro (máximo goleador) del Mundial 2014 y mejor jugador de la Copa América 2024. El 10 mágico de Colombia. 🎩",
  "ENG18":"Máximo goleador histórico de la selección de Inglaterra y del Tottenham; Bota de Oro del Mundial 2018 y goleador de la Bundesliga con el Bayern. 🎯",
  "NED3":"Mejor defensa del mundo (2º en el Balón de Oro 2019), campeón de la Champions 2019 y de la Premier con el Liverpool. 🧱",
  "ESP11":"Dos veces Trofeo Kopa al mejor joven del mundo y campeón de LaLiga con el Barça. El cerebro del equipo. 🎯",
  "ENG16":"Jugador del Año de la Premier League 2023-24 y campeón del triplete con el City. 🏴󠁧󠁢󠁥󠁮󠁧󠁿",
  "ENG17":"Estrella del Arsenal y de Inglaterra, finalista de dos Eurocopas; imparable por la banda derecha. 🚀",
  "GER15":"Joya del Bayern, regateador letal y goleador; varias veces campeón de la Bundesliga siendo muy joven. ✨",
  "GER11":"Cerebro del Leverkusen que salió campeón INVICTO de la Bundesliga 2024; fichó por el Liverpool por +100 M€. 🧠",
  "ENG12":"8º en el Balón de Oro 2025; explotó en el Chelsea y fue figura del Mundial de Clubes (doblete en la final). ❄️",
  "URU10":"Motor del Real Madrid, campeón de Champions y LaLiga; pulmón infinito y golazos de larga distancia. 🚂",
  "ARG18":"Capocannoniere (goleador) de la Serie A con el Inter y campeón del mundo 2022 con Argentina. 🐂",
  "ARG19":"Campeón del mundo 2022 y del triplete con el City; killer del área, hoy estrella del Atlético. 🦊",
  "NOR10":"Capitán del Arsenal; crack creativo con una zurda mágica que casi lleva a los Gunners al título. 🎩",
  "POR9":"Mago del City: múltiple campeón de la Premier y campeón de la Champions del triplete 2023. 🪄",
  "POR10":"Capitán del Manchester United y de Portugal; rey de las asistencias y goles desde el mediocampo, máximo goleador del United varias temporadas. 🅰️",
  "BRA15":"Especialista en noches mágicas de Champions; campeón de Europa 2024 y de LaLiga con el Real Madrid. 🌟",
  "GER10":"Capitán del Bayern y de Alemania; multicampeón de la Bundesliga y campeón de la Champions 2020. ⚙️",
  "ARG9":"Campeón del mundo 2022 y campeón de la Premier League con el Liverpool; mediocampista todoterreno. 🏆",
  "ARG8":"Mejor Jugador Joven del Mundial 2022, campeón del mundo y del Mundial de Clubes 2025 con el Chelsea. 🌟",
  "CRO4":"Central-crack del City: rápido, fuerte y goleador; campeón de la Premier y figura del Mundial 2022. 🧱",
  "POR20":"Extremo explosivo, campeón de la Serie A con el Milan (MVP de la liga); pura velocidad y desborde. ⚡",
  "SWE19":"Delantero letal; brilló en el Newcastle y fichó por el Liverpool en un traspaso récord. 🎯",
  "FRA4":"Muralla del Arsenal y de Francia; uno de los mejores centrales del mundo. 🧱",
  "URU11":"Mejor jugador de la Copa Libertadores con Flamengo; el 10 mágico de Uruguay. 🏆",
  "SEN16":"Extremo explosivo de Senegal; goles decisivos en Inglaterra y figura de su selección. ⚡",
  "JAP20":"Goleador de la Eredivisie (liga de Países Bajos) con +25 goles en una temporada. 👟",
  "KOR18":"Bota de Oro de la Premier 2021-22, capitán de Corea y leyenda del Tottenham con +170 goles. 🇰🇷",
  "BEL2":"Mejor portero del mundo (Premio Yashin), héroe de la final de Champions 2022 y campeón con el Real Madrid. 🧤",
  "ENG10":"Pulmón del Arsenal y de Inglaterra; fichaje récord británico, lo gana todo en el mediocampo. 💪",
  "ARG2":"El 'Dibu': héroe en los penales del Mundial 2022, dos veces mejor portero del mundo (Guante de Oro) y bicampeón de América. 🧤",
  "ENG11":"Figura del Real Madrid: campeón de la Champions y de LaLiga, decisivo en su primera temporada con goles clave. ⭐"
};
const STAR_PROB="📦 Dato: una lámina puntual sale en ~1 de cada 140 sobres (hay 980 distintas y cada sobre trae 7). ¡Por eso se celebra!";
const EXTRA_PROB="🤯 Rareza nivel dios: las Extra Stickers (foil) salen ~1 cada 100 sobres.";
const CAREER={
  "ARG17":{ accent:"#1E6FB8",
    stats:[ {i:"🏆",n:"8",l:"Balones de Oro"}, {i:"⚽",n:"+900",l:"Goles"}, {i:"🌟",n:"4",l:"Champions"}, {i:"🌍",n:"2022",l:"Mundial"} ],
    balones:8, goles:900, meta:1000, record:"8 Balones de Oro · récord absoluto" },
  "POR15":{ accent:"#D8232A",
    stats:[ {i:"🏆",n:"5",l:"Balones de Oro"}, {i:"⚽",n:"+970",l:"Goles"}, {i:"🌟",n:"5",l:"Champions"}, {i:"🏅",n:"2016",l:"Euro"} ],
    balones:5, goles:970, meta:1000, record:"Máximo goleador de la historia" }
};
function careerHTML(c){
  var stats=c.stats.map(function(s){ return '<div class="cr-stat"><div class="cr-ico">'+s.i+'</div><div class="cr-num">'+s.n+'</div><div class="cr-lab">'+s.l+'</div></div>'; }).join("");
  var balls="🏆".repeat(c.balones);
  var pct=Math.round(c.goles/c.meta*100);
  return '<div class="cr-head">📊 Su carrera · lo que el álbum no te cuenta</div>'+
    '<div class="cr-grid">'+stats+'</div>'+
    '<div class="cr-block"><div class="cr-row"><span class="cr-row-l">🏆 Balones de Oro</span><span class="cr-row-r">'+c.balones+'</span></div><div class="cr-balls">'+balls+'</div></div>'+
    '<div class="cr-block"><div class="cr-row"><span class="cr-row-l">⚽ Camino a los 1.000 goles</span><span class="cr-row-r">'+c.goles+'</span></div><div class="cr-bar"><div class="cr-bar-fill" style="width:'+pct+'%"></div></div><div class="cr-foot">'+c.goles+' de 1.000 · '+pct+'%</div></div>'+
    '<div class="cr-record">🐐 '+c.record+'</div>';
}
function renderCareerPanel(d){
  var el=document.getElementById("starCareer");
  var fc=document.getElementById("starFact");
  if(!el && fc){ el=document.createElement("div"); el.id="starCareer"; el.className="star-career"; fc.parentNode.insertBefore(el, fc); }
  if(!el) return;
  var c=(d.kind!=="extra") && CAREER[d.code];
  if(c){ el.style.setProperty("--cr-accent", c.accent); el.innerHTML=careerHTML(c); el.style.display="block"; }
  else { el.innerHTML=""; el.style.display="none"; }
}
let _starQueue=[], _starShowing=false;
function maybeCelebrateStar(code){
  if(!state.config.celebrate) return;
  let d=null;
  if(STAR_FACTS[code]) d={kind:"star",code};
  else if(code[0]==="X") d={kind:"extra",code};
  if(!d) return;
  _starQueue.push(d);
  if(!_starShowing) showNextStar();
}
function showNextStar(){
  const ov=document.getElementById("ovStar"); if(!ov){ _starShowing=false; _starQueue=[]; return; }
  if(!_starQueue.length){ _starShowing=false; return; }
  _starShowing=true;
  const d=_starQueue.shift();
  let name,sub,fact,prob;
  if(d.kind==="extra"){
    const m=d.code.match(/X(\d+)([A-Z])/);
    name=(m && state.config.extraNames["p"+m[1]]) || "Extra Sticker";
    const col=EXTRA_COLORS.find(c=>m && c[0]===m[2]);
    sub="✨ Foil de leyenda"+(col?" · "+col[1]:"");
    fact="¡Una de las láminas más difíciles de todo el álbum!"; prob=EXTRA_PROB;
  } else {
    name=playerName(d.code)||d.code;
    const t=CODE_TEAM[d.code], pos=playerPos(d.code);
    sub=(t?(t.flag+" "+t.name):"")+(pos?" · "+pos:"");
    fact=STAR_FACTS[d.code]||""; prob=STAR_PROB;
  }
  document.getElementById("starName").textContent=name;
  document.getElementById("starTeam").textContent=sub;
  const tierEl=document.getElementById("starTier");
  if(tierEl){ const tk=(d.kind!=="extra")&&CRACK_TIER[d.code]; const tier=tk&&TIERS.find(t=>t.key===tk); if(tier){ tierEl.textContent=tier.icon+" Tier "+tier.name; tierEl.style.background=tier.color; tierEl.style.display="inline-block"; } else tierEl.style.display="none"; }
  const valEl=document.getElementById("starValue");
  if(valEl){ const v=(d.kind==="extra")?"":playerValue(d.code); valEl.textContent=v; valEl.style.display=v?"block":"none"; }
  if(d.code==="POR15") fact="¡SIUUUU! 🇵🇹 — "+fact;
  else if(d.code==="ARG17") fact="¡Encara Messi, encara Messi…! 🇦🇷 — "+fact;
  document.getElementById("starFact").textContent=fact;
  document.getElementById("starProb").textContent=prob;
  renderCareerPanel(d);
  const _sc=document.getElementById("starClose"); if(_sc) _sc.textContent=hype()+" 👍🗿"; playClipOrSynth(d.code, (d.kind==="extra")?"goat":(CRACK_TIER[d.code]||"oro"));
  launchConfetti();
  ov.classList.add("show");
}
function closeStar(){ if(!_starShowing) return; _starShowing=false; const ov=document.getElementById("ovStar"); if(ov) ov.classList.remove("show"); setTimeout(showNextStar,260); }

/* ---------- code parsing ---------- */
function normalize(raw){
  let s=raw.toUpperCase().replace(/[^A-Z0-9]/g,""); if(!s) return null;
  if(s==="00") return state.config.specials?"00":null;
  let m=s.match(/^FWC?(\d{1,2})$/); if(m){ const c="FWC"+parseInt(m[1],10); return (state.config.specials&&VALID_SPECIAL.has(c))?c:null; }
  m=s.match(/^([A-Z]{3})0*(\d{1,2})$/); if(m){ const c=m[1]+parseInt(m[2],10); return VALID_TEAM.has(c)?c:null; }
  return null;
}
function findCodes(text){
  const up=text.toUpperCase(); const out=[]; const seen=new Set();
  const push=c=>{ if(c&&!seen.has(c)){ seen.add(c); out.push(c); } };
  let m; const reTeam=/([A-Z]{3})\s*[-–—]?\s*0*(\d{1,2})/g;
  while((m=reTeam.exec(up))){ const c=m[1]+parseInt(m[2],10); if(VALID_TEAM.has(c)) push(c); }
  const reFW=/F\s*W\s*C?\s*[-–—]?\s*0*(\d{1,2})/g;
  while((m=reFW.exec(up))){ const c="FWC"+parseInt(m[1],10); if(state.config.specials&&VALID_SPECIAL.has(c)) push(c); }
  return out;
}

/* ---------- rendering ---------- */
const main=document.getElementById("main");
let currentFilter="all", searchTerm="", sortMode="group", clubCountry="all";
function cellState(c){ const n=getCount(c); return n===0?0:(n===1?1:2); }
function shortName(code){ const nm=playerName(code); if(!nm) return ""; if(nm==="Foto del equipo") return "Plantel"; const p=nm.trim().split(/\s+/); return p.length>1?p[p.length-1]:nm; }
function posAbbr(code){ const po=playerPos(code); if(!po) return ""; const m={"Portero":"POR","Arquero":"POR","Defensa":"DEF","Lateral":"LAT","Mediocampista":"MED","Volante":"MED","Centrocampista":"MED","Delantero":"DEL","Extremo":"EXT"}; return m[po]||po.slice(0,3).toUpperCase(); }
function cellContent(d,code,label){
  const st=cellState(code), isTeam=!!CODE_TEAM[code]; d.innerHTML="";
  if(st>=1 && isTeam){
    d.classList.add("info");
    const nm=shortName(code), po=posAbbr(code);
    let h='<span class="cnum">'+label+'</span>';
    if(nm) h+='<span class="cname">'+escHTML(nm)+'</span>';
    if(po) h+='<span class="cpos">'+po+'</span>';
    d.innerHTML=h;
  } else { d.classList.remove("info"); d.textContent=label; }
  const nn=getCount(code);
  if(st===2){ const b=document.createElement("span"); b.className="badge"; b.textContent="+"+(nn-1); d.appendChild(b); }
}
var subMode=false;
function buildCell(code,label,opts){
  opts=opts||{};
  const n=getCount(code),st=cellState(code);
  const d=document.createElement("div"); d.className="cell"+(st===1?" have":st===2?" dup":""); d.dataset.code=code; d.dataset.label=label;
  const _pn=playerName(code); if(_pn) d.title=_pn;
  cellContent(d,code,label);
  if(opts.viewer){ d.addEventListener("click",()=>openEditor(code)); return d; }
  let lp=false, lpTimer=null;
  const quickEdit=(delta)=>{ const cur=getCount(code); setCount(code, Math.max(0,cur+delta)); refreshCellInDom(code); updateStats(); if(currentFilter!=="all") render(); const m=getCount(code); if(delta>0&&m===1) sfxPop(); toast(delta>0 ? (m>1?(displayCode(code)+" · repetida ×"+m):(hype()+" 👍🗿 "+displayCode(code)+" ✓")) : (m>0?(displayCode(code)+" −1 (×"+m+")"):(displayCode(code)+" quitada"))); };
  d.addEventListener("touchstart",()=>{ lp=false; lpTimer=setTimeout(()=>{ lp=true; openEditor(code); },450); },{passive:true});
  d.addEventListener("touchend",()=>clearTimeout(lpTimer));
  d.addEventListener("touchmove",()=>clearTimeout(lpTimer));
  // click derecho: simple = editor · doble = −1 (atajo)
  d.addEventListener("contextmenu",e=>{ e.preventDefault();
    if(d._ctxT){ clearTimeout(d._ctxT); d._ctxT=null; quickEdit(-1); return; }
    d._ctxT=setTimeout(()=>{ d._ctxT=null; openEditor(code); }, 260);
  });
  // click izquierdo: simple = marcar/desmarcar (o editor si ya es repetida) · doble = +1 (atajo)
  d.addEventListener("click",()=>{
    if(lp){ lp=false; return; }                 // fue mantener-apretado: ya abrió el editor
    if(subMode){ if(getCount(code)>0) quickEdit(-1); return; }   // modo restar: cada toque resta 1
    if(d._clkT){ clearTimeout(d._clkT); d._clkT=null;
      if(getCount(code)>=1){ quickEdit(1); }                                  // ya la tienes -> +1 (repetida)
      else { setCount(code,1); refreshCellInDom(code); updateStats(); if(currentFilter!=="all") render(); }  // no la tienes -> solo marcar, sin alerta
      return; }
    d._clkT=setTimeout(()=>{ d._clkT=null;
      const cur=getCount(code);
      if(cur>=2){ openEditor(code); return; }    // repetidas: abre el editor para no borrarlas sin querer
      setCount(code, cur===0?1:0);               // toca para marcar / desmarcar
      refreshCellInDom(code); updateStats();
      if(currentFilter!=="all") render();
    }, 260);
  });
  return d;
}
function makeTeam(code,name,flag,term){
  const dterm = term ? deburr(term) : "";
  const teamHit = !dterm || deburr(name).includes(dterm) || code.toLowerCase().includes(dterm);
  let playerHit=false;
  if(dterm && !teamHit){ for(let i=1;i<=20;i++){ const pn=PLAYER[code+i]; if(pn && deburr(pn).includes(dterm)){ playerHit=true; break; } } }
  const matchTeam = !term || teamHit || playerHit;
  const accent=TEAM_COLOR[code]||"#0067B9";
  const team=document.createElement("div"); team.className="team"; team.style.setProperty("--accent",accent);
  let have=0; for(let i=1;i<=20;i++) if(getCount(code+i)>0) have++;
  const pct=Math.round(have/20*100);
  const _tpg=teamPageRange(code);
  const th=document.createElement("div"); th.className="team-h";
  th.innerHTML=flagHTML(code, flag)+'<div class="team-name"><div class="weare">We Are</div><div class="nmrow"><div class="nm">'+name+'</div><span class="iinfo">i</span></div><div class="cd">'+code+' · 1–20'+(_tpg?' · <span class="cd-pg">📄 Págs '+_tpg+'</span>':'')+'</div></div><div class="tprog '+(have===20?'full':'')+'"><div class="fr">'+have+'/20</div><div class="bar"><i style="width:'+pct+'%"></i></div></div>';
  th.classList.add("tappable"); th.setAttribute("title","Ver ficha del equipo"); th.addEventListener("click",()=>openTeamInfo(code));
  team.appendChild(th);
  const cells=document.createElement("div"); cells.className="cells"; let vis=0;
  for(let i=1;i<=20;i++){ const c=code+i; const cell=buildCell(c,String(i));
    const isHit = dterm && !teamHit && PLAYER[c] && deburr(PLAYER[c]).includes(dterm);
    if(isHit) cell.classList.add("hit");
    const show=matchTeam&&passFilter(c); if(!show)cell.classList.add("hide"); else vis++; cells.appendChild(cell); }
  team.appendChild(cells); if(vis===0) team.classList.add("hide");
  return team;
}
/* Álbum por club: jugadores del álbum agrupados por club (plantillas 2025-26). Solo lectura. */
const CLUB_COUNTRIES=[
 {key:"ENG", name:"Inglaterra", flag:"🏴󠁧󠁢󠁥󠁮󠁧󠁿"},
 {key:"ESP", name:"España", flag:"🇪🇸"},
 {key:"ITA", name:"Italia", flag:"🇮🇹"},
 {key:"GER", name:"Alemania", flag:"🇩🇪"},
 {key:"FRA", name:"Francia", flag:"🇫🇷"},
 {key:"POR", name:"Portugal", flag:"🇵🇹"},
 {key:"NED", name:"Países Bajos", flag:"🇳🇱"},
 {key:"SUR", name:"Sudamérica", flag:"🌎"},
 {key:"SAU", name:"Arabia Saudita", flag:"🇸🇦"}
];
const CLUBS=[
 {name:"Real Madrid", country:"ESP", color:"#1D3461", codes:["FRA20","BRA14","BRA15","ENG11","FRA10","FRA9","BRA5","URU10","BEL2","GER6","ESP7","ESP5","TUR14","ENG6","ARG15"]},
 {name:"FC Barcelona", country:"ESP", color:"#A50044", codes:["ESP15","ESP11","BRA19","ESP18","ESP16","URU4","NED14","FRA5"]},
 {name:"Atlético de Madrid", country:"ESP", color:"#CB3524", codes:["ARG19","ARG20","ESP3","ARG3","NOR16"]},
 {name:"Athletic Club", country:"ESP", color:"#EE2523", codes:["ESP17","GHA15"]},
 {name:"Real Sociedad", country:"ESP", color:"#0067B1", codes:["ESP20","JAP12"]},
 {name:"Villarreal", country:"ESP", color:"#005187", codes:["GHA10","SEN11"]},
 {name:"Manchester City", country:"ENG", color:"#6CABDD", codes:["NOR15","ENG16","ESP10","POR9","POR4","BEL16","CRO4","EGY20","NED10","ALG5"]},
 {name:"Arsenal", country:"ENG", color:"#EF0107", codes:["ENG17","ENG10","NOR10","BRA6","BRA18","FRA4","NED5","GER17","SWE20","ESP9"]},
 {name:"Liverpool", country:"ENG", color:"#C8102E", codes:["EGY17","NED3","NED20","ARG9","NED11","FRA6","GER11","FRA19","SWE19","NED8","SCO6"]},
 {name:"Chelsea", country:"ENG", color:"#034694", codes:["ENG12","ARG8","ESP8","ECU9","ENG7","BRA16","POR19","BRA20"]},
 {name:"Tottenham", country:"ENG", color:"#132257", codes:["ARG4","URU12","NED4","SEN10","SWE8","SWE17","NED15","GHA14"]},
 {name:"Aston Villa", country:"ENG", color:"#670E36", codes:["ARG2","ENG5","FRA8","BEL9","BEL10","ENG14","ENG20"]},
 {name:"Brighton", country:"ENG", color:"#0057B8", codes:["NED2","NED9","BEL7","SWE11","PAR10","PAR15"]},
 {name:"Manchester United", country:"ENG", color:"#DA291C", codes:["POR10","POR6","BRA10","BRA17","CIV15"]},
 {name:"Crystal Palace", country:"ENG", color:"#1B458F", codes:["ENG4","USA3","JAP11","SEN16"]},
 {name:"West Ham", country:"ENG", color:"#7A263A", codes:["BRA9","CZE12","MEX10","COD3"]},
 {name:"Newcastle", country:"ENG", color:"#101010", codes:["BRA11","ENG15","ENG8"]},
 {name:"Inter", country:"ITA", color:"#0068A8", codes:["ARG18","TUR11","NED6","CRO15"]},
 {name:"Juventus", country:"ITA", color:"#000000", codes:["TUR20","USA10","NED12","CAN20"]},
 {name:"Napoli", country:"ITA", color:"#12A0D7", codes:["BEL15","SCO11","BEL20","SCO12"]},
 {name:"AC Milan", country:"ITA", color:"#FB090B", codes:["USA16","FRA2","POR20"]},
 {name:"Roma", country:"ITA", color:"#8E1F2F", codes:["CIV6","FRA11","BRA8"]},
 {name:"Atalanta", country:"ITA", color:"#1D71B8", codes:["BEL17","CRO14","BIH4"]},
 {name:"Bayern Múnich", country:"GER", color:"#DC052D", codes:["GER10","GER15","GER14","GER16","ENG18","FRA7","FRA14","AUT12","KOR4","COL20","GER3"]},
 {name:"RB Leipzig", country:"GER", color:"#DD0741", codes:["GER4","AUT10","AUT15","AUT18","NOR19"]},
 {name:"Borussia Dortmund", country:"GER", color:"#111111", codes:["SUI2","GER5","GER7","GER12","GER19"]},
 {name:"Eintracht Frankfurt", country:"GER", color:"#E1000F", codes:["SWE9","TUR17","JAP14"]},
 {name:"PSG", country:"FRA", color:"#004170", codes:["POR12","POR7","POR14","FRA17","FRA16","FRA15","MAR4","ESP12","POR18","BRA4","ECU6"]},
 {name:"Mónaco", country:"FRA", color:"#CE1126", codes:["SUI10","JAP16","SEN14","MAR12","USA20"]},
 {name:"Marsella", country:"FRA", color:"#0092D0", codes:["ARG7","ALG18"]},
 {name:"Benfica", country:"POR", color:"#E20E0E", codes:["ARG5","TUR12","TUR19","NOR12","COL12"]},
 {name:"Sporting CP", country:"POR", color:"#1A7A4C", codes:["POR8","CIV9","POR16","URU16","BEL5"]},
 {name:"FC Porto", country:"POR", color:"#0046A8", codes:["POR2","CAN10"]},
 {name:"PSV", country:"NED", color:"#ED1C24", codes:["MAR15","CRO16","USA18","CUW3"]},
 {name:"Feyenoord", country:"NED", color:"#C8102E", codes:["ALG17","JAP20"]},
 {name:"Flamengo", country:"SUR", color:"#C52613", codes:["URU11","URU15","URU8","BRA7"]},
 {name:"Boca Juniors", country:"SUR", color:"#0A2472", codes:["ARG12"]},
 {name:"Al-Hilal", country:"SAU", color:"#1B458F", codes:["FRA3","POR5","POR11","SEN8","MAR2","KSA16"]},
 {name:"Al-Nassr", country:"SAU", color:"#1A3A6B", codes:["POR15","POR17","FRA18","SEN15","BRA3"]}
];
/* sigla + posición por jugador (posiciones aproximadas) */
const CLUB_ABBR={"Real Madrid": "RMA", "FC Barcelona": "BAR", "Atlético de Madrid": "ATM", "Athletic Club": "ATH", "Real Sociedad": "RSO", "Villarreal": "VIL", "Manchester City": "MCI", "Arsenal": "ARS", "Liverpool": "LIV", "Chelsea": "CHE", "Tottenham": "TOT", "Aston Villa": "AVL", "Brighton": "BHA", "Manchester United": "MUN", "Crystal Palace": "CRY", "West Ham": "WHU", "Newcastle": "NEW", "Inter": "INT", "Juventus": "JUV", "Napoli": "NAP", "AC Milan": "MIL", "Roma": "ROM", "Atalanta": "ATA", "Bayern Múnich": "BAY", "RB Leipzig": "RBL", "Borussia Dortmund": "DOR", "Eintracht Frankfurt": "SGE", "PSG": "PSG", "Mónaco": "ASM", "Marsella": "OM", "Benfica": "SLB", "Sporting CP": "SCP", "FC Porto": "FCP", "PSV": "PSV", "Feyenoord": "FEY", "Flamengo": "FLA", "Boca Juniors": "BOC", "Al-Hilal": "HIL", "Al-Nassr": "NAS"};
const POS={"FRA20": "Delantero", "BRA14": "Delantero", "BRA15": "Delantero", "ENG11": "Mediocampista", "FRA10": "Mediocampista", "FRA9": "Mediocampista", "BRA5": "Defensa", "URU10": "Mediocampista", "BEL2": "Portero", "GER6": "Defensa", "ESP7": "Defensa", "ESP5": "Defensa", "TUR14": "Mediocampista", "ENG6": "Defensa", "ARG15": "Delantero", "ESP15": "Delantero", "ESP11": "Mediocampista", "BRA19": "Delantero", "ESP18": "Delantero", "ESP16": "Mediocampista", "URU4": "Defensa", "NED14": "Mediocampista", "FRA5": "Defensa", "ARG19": "Delantero", "ARG20": "Delantero", "ESP3": "Defensa", "ARG3": "Defensa", "NOR16": "Delantero", "ESP17": "Delantero", "GHA15": "Delantero", "ESP20": "Delantero", "JAP12": "Delantero", "GHA10": "Mediocampista", "SEN11": "Mediocampista", "NOR15": "Delantero", "ENG16": "Mediocampista", "ESP10": "Mediocampista", "POR9": "Mediocampista", "POR4": "Defensa", "BEL16": "Delantero", "CRO4": "Defensa", "EGY20": "Delantero", "NED10": "Mediocampista", "ALG5": "Defensa", "ENG17": "Delantero", "ENG10": "Mediocampista", "NOR10": "Mediocampista", "BRA6": "Defensa", "BRA18": "Delantero", "FRA4": "Defensa", "NED5": "Defensa", "GER17": "Delantero", "SWE20": "Delantero", "ESP9": "Mediocampista", "EGY17": "Delantero", "NED3": "Defensa", "NED20": "Delantero", "ARG9": "Mediocampista", "NED11": "Mediocampista", "FRA6": "Defensa", "GER11": "Mediocampista", "FRA19": "Delantero", "SWE19": "Delantero", "NED8": "Defensa", "SCO6": "Defensa", "ENG12": "Mediocampista", "ARG8": "Mediocampista", "ESP8": "Defensa", "ECU9": "Mediocampista", "ENG7": "Defensa", "BRA16": "Delantero", "POR19": "Delantero", "BRA20": "Delantero", "ARG4": "Defensa", "URU12": "Mediocampista", "NED4": "Defensa", "SEN10": "Mediocampista", "SWE8": "Mediocampista", "SWE17": "Mediocampista", "NED15": "Mediocampista", "GHA14": "Delantero", "ARG2": "Portero", "ENG5": "Defensa", "FRA8": "Defensa", "BEL9": "Mediocampista", "BEL10": "Mediocampista", "ENG14": "Mediocampista", "ENG20": "Delantero", "NED2": "Portero", "NED9": "Defensa", "BEL7": "Defensa", "SWE11": "Mediocampista", "PAR10": "Mediocampista", "PAR15": "Delantero", "POR10": "Mediocampista", "POR6": "Defensa", "BRA10": "Mediocampista", "BRA17": "Delantero", "CIV15": "Delantero", "ENG4": "Defensa", "USA3": "Defensa", "JAP11": "Mediocampista", "SEN16": "Delantero", "BRA9": "Mediocampista", "CZE12": "Mediocampista", "MEX10": "Mediocampista", "COD3": "Defensa", "BRA11": "Mediocampista", "ENG15": "Delantero", "ENG8": "Defensa", "ARG18": "Delantero", "TUR11": "Mediocampista", "NED6": "Defensa", "CRO15": "Mediocampista", "TUR20": "Delantero", "USA10": "Mediocampista", "NED12": "Mediocampista", "CAN20": "Delantero", "BEL15": "Mediocampista", "SCO11": "Mediocampista", "BEL20": "Delantero", "SCO12": "Mediocampista", "USA16": "Delantero", "FRA2": "Portero", "POR20": "Delantero", "CIV6": "Defensa", "FRA11": "Mediocampista", "BRA8": "Defensa", "BEL17": "Delantero", "CRO14": "Mediocampista", "BIH4": "Defensa", "GER10": "Mediocampista", "GER15": "Mediocampista", "GER14": "Mediocampista", "GER16": "Delantero", "ENG18": "Delantero", "FRA7": "Defensa", "FRA14": "Delantero", "AUT12": "Defensa", "KOR4": "Defensa", "COL20": "Delantero", "GER3": "Defensa", "GER4": "Defensa", "AUT10": "Mediocampista", "AUT15": "Mediocampista", "AUT18": "Mediocampista", "NOR19": "Delantero", "SUI2": "Portero", "GER5": "Defensa", "GER7": "Defensa", "GER12": "Mediocampista", "GER19": "Delantero", "SWE9": "Mediocampista", "TUR17": "Mediocampista", "JAP14": "Delantero", "POR12": "Mediocampista", "POR7": "Defensa", "POR14": "Mediocampista", "FRA17": "Delantero", "FRA16": "Delantero", "FRA15": "Delantero", "MAR4": "Defensa", "ESP12": "Mediocampista", "POR18": "Delantero", "BRA4": "Defensa", "ECU6": "Defensa", "SUI10": "Mediocampista", "JAP16": "Delantero", "SEN14": "Mediocampista", "MAR12": "Delantero", "USA20": "Delantero", "ARG7": "Defensa", "ALG18": "Delantero", "ARG5": "Defensa", "TUR12": "Mediocampista", "TUR19": "Delantero", "NOR12": "Delantero", "COL12": "Mediocampista", "POR8": "Defensa", "CIV9": "Defensa", "POR16": "Delantero", "URU16": "Defensa", "BEL5": "Defensa", "POR2": "Portero", "CAN10": "Mediocampista", "MAR15": "Mediocampista", "CRO16": "Delantero", "USA18": "Delantero", "CUW3": "Defensa", "ALG17": "Delantero", "JAP20": "Delantero", "URU11": "Mediocampista", "URU15": "Mediocampista", "URU8": "Defensa", "BRA7": "Defensa", "ARG12": "Mediocampista", "FRA3": "Defensa", "POR5": "Defensa", "POR11": "Mediocampista", "SEN8": "Defensa", "MAR2": "Portero", "KSA16": "Delantero", "POR15": "Delantero", "POR17": "Delantero", "FRA18": "Delantero", "SEN15": "Delantero", "BRA3": "Portero"};
const STANDING={"Manchester City": "2°", "Arsenal": "🏆 Campeón", "Liverpool": "5°", "Chelsea": "10°", "Tottenham": "17°", "Aston Villa": "4° · 🏆 Europa League", "Brighton": "8°", "Manchester United": "3°", "Crystal Palace": "15° · 🏆 Conference", "West Ham": "⬇️ Descendió", "Newcastle": "12°", "Real Madrid": "2°", "FC Barcelona": "🏆 Campeón", "Atlético de Madrid": "4°", "Athletic Club": "12°", "Real Sociedad": "10° · 🏆 Copa del Rey", "Villarreal": "3°", "Inter": "🏆 Campeón", "Juventus": "Europa League", "Napoli": "Champions League", "AC Milan": "Europa League", "Roma": "Champions League", "Atalanta": "Conference League", "Bayern Múnich": "🏆 Campeón", "RB Leipzig": "Champions League", "Borussia Dortmund": "Champions League", "Eintracht Frankfurt": "Bundesliga", "PSG": "🏆 Campeón · 🏆 Champions", "Mónaco": "Conference League", "Marsella": "5°", "Benfica": "3°", "Sporting CP": "2°", "FC Porto": "🏆 Campeón", "PSV": "🏆 Campeón", "Feyenoord": "2°", "Al-Hilal": "2° (invicto)", "Al-Nassr": "🏆 Campeón", "Flamengo": "🏆 Campeón Libertadores", "Boca Juniors": "Liga Profesional"};
function playerPos(code){ return POS[code]||''; }
const PLAYER_STATS={"NOR15": "👟 27 goles · Bota de Oro", "FRA20": "👟 25 goles · Pichichi", "ENG18": "👟 36 goles · goleador Bundesliga", "ARG18": "👟 17 goles · capocannoniere", "JAP20": "👟 25 goles · goleador Eredivisie", "POR15": "⚽ 28 goles 25-26", "POR10": "🅰️ 21 asist. · récord Premier", "SEN16": "🏆 Conference: 9 goles · mejor jugador", "URU11": "🏅 Mejor jugador · Libertadores"};
function playerStat(code){ return PLAYER_STATS[code]||''; }
const MARKET_VALUE={"ESP15":"≈ 200 M€","FRA20":"≈ 180 M€","ENG11":"≈ 180 M€","NOR15":"≈ 180 M€","BRA14":"≈ 170 M€","GER11":"≈ 140 M€","ESP11":"≈ 140 M€","ENG17":"≈ 140 M€","GER15":"≈ 130 M€","URU10":"≈ 130 M€","ESP10":"≈ 120 M€","ARG19":"≈ 120 M€","SWE19":"≈ 120 M€","ENG10":"≈ 110 M€","ENG12":"≈ 110 M€","ENG16":"≈ 100 M€","POR12":"≈ 90 M€","FRA17":"≈ 90 M€","POR14":"≈ 90 M€","FRA15":"≈ 90 M€","ENG18":"≈ 90 M€","ARG18":"≈ 90 M€","NOR10":"≈ 90 M€","BRA15":"≈ 90 M€","POR7":"≈ 80 M€","ARG8":"≈ 80 M€","FRA4":"≈ 80 M€","MAR4":"≈ 75 M€","CRO4":"≈ 75 M€","ARG9":"≈ 70 M€","POR20":"≈ 65 M€","GER10":"≈ 50 M€","POR10":"≈ 45 M€","POR9":"≈ 45 M€","EGY17":"≈ 45 M€","NED3":"≈ 35 M€","SEN16":"≈ 30 M€","BEL15":"≈ 28 M€","ARG2":"≈ 25 M€","BEL2":"≈ 25 M€","KOR18":"≈ 25 M€","JAP20":"≈ 20 M€","ARG17":"≈ 18 M€","URU11":"≈ 15 M€","POR15":"≈ 12 M€","CRO9":"≈ 4 M€","COL14":"≈ 3 M€"};
function playerValue(code){ const v=MARKET_VALUE[code]; return v?("💰 Valor aprox.: "+v):""; }

/* ---------- tiers de cracks ---------- */
const TIERS=[
  {key:"goat", name:"GOAT", icon:"🐐", color:"#16181D", sub:"Los 2 más grandes"},
  {key:"diamante", name:"Diamante", icon:"💎", color:"#2EC5E0", sub:"Fuera de serie"},
  {key:"oro", name:"Oro", icon:"🥇", color:"#F2A20C", sub:"Élite mundial"},
  {key:"plata", name:"Plata", icon:"🥈", color:"#9AA7B4", sub:"Grandes figuras"},
  {key:"bronce", name:"Bronce", icon:"🥉", color:"#C77B3B", sub:"Buenos cracks"}
];
const CRACK_TIER={
  "ARG17":"goat","POR15":"goat",
  "FRA20":"diamante","NOR15":"diamante","ESP15":"diamante","BRA14":"diamante","FRA15":"diamante","POR12":"diamante",
  "ESP10":"oro","MAR4":"oro","EGY17":"oro","CRO9":"oro","BEL15":"oro","ENG18":"oro","NED3":"oro","KOR18":"oro","ESP11":"oro","ARG18":"oro","POR7":"oro",
  "ENG11":"plata","ENG16":"plata","ENG17":"plata","GER15":"plata","GER11":"plata","ENG12":"plata","URU10":"plata","ARG19":"plata","NOR10":"plata","ENG10":"plata","BRA15":"plata","BEL2":"plata","COL14":"plata","FRA17":"plata","POR14":"plata",
  "POR9":"bronce","POR10":"bronce","GER10":"bronce","ARG9":"bronce","ARG8":"bronce","CRO4":"bronce","POR20":"bronce","SWE19":"bronce","FRA4":"bronce","URU11":"bronce","SEN16":"bronce","JAP20":"bronce","ARG2":"bronce"
};
function buildCrackCard(code, tier){
  const got=getCount(code)>0, meta=CODE_TEAM[code];
  const card=document.createElement("div"); card.className="crack-card"+(got?" got":" locked"); card.style.setProperty("--tc",tier.color); card.dataset.code=code;
  const nm=playerName(code)||code;
  card.innerHTML='<div class="crack-flag">'+(meta?flagHTML(meta.code,meta.flag,"crackflag"):'<span class="flag">⭐</span>')+'</div>'
    +'<div class="crack-nm">'+escHTML(nm)+'</div>'
    +'<div class="crack-state">'+(got?'✓ conseguido':'🔒 te falta')+'</div>';
  card.addEventListener("click",()=>openEditor(code));
  return card;
}
function openCracks(){
  const all=Object.keys(STAR_FACTS);
  const got=all.filter(c=>getCount(c)>0).length;
  document.getElementById("cracksSum").innerHTML='Llevas <b>'+got+'</b> de <b>'+all.length+'</b> cracks 🔥';
  const body=document.getElementById("cracksBody"); body.innerHTML="";
  TIERS.forEach(t=>{
    const codes=all.filter(c=>CRACK_TIER[c]===t.key);
    if(!codes.length) return;
    codes.sort((a,b)=>{ const ga=getCount(a)>0?0:1, gb=getCount(b)>0?0:1; if(ga!==gb) return ga-gb; return (playerName(a)||a).localeCompare(playerName(b)||b,"es"); });
    const gt=codes.filter(c=>getCount(c)>0).length;
    const sec=document.createElement("div"); sec.className="tier-sec";
    const stars=t.key==="goat" ? "👑" : "★".repeat({diamante:5,oro:4,plata:3,bronce:2}[t.key]||0);
    sec.innerHTML='<div class="tier-h" style="--tc:'+t.color+'"><span class="tier-ic">'+t.icon+'</span><span class="tier-nm">'+t.name+'</span><span class="tier-stars">'+stars+'</span><span class="tier-sub">'+t.sub+'</span><span class="tier-ct">'+gt+'/'+codes.length+'</span></div>';
    const grid=document.createElement("div"); grid.className="crack-grid";
    codes.forEach(c=>grid.appendChild(buildCrackCard(c,t)));
    sec.appendChild(grid); body.appendChild(sec);
  });
  document.getElementById("ovCracks").classList.add("show");
}
function makeClub(club, term){
  const dterm = term ? deburr(term) : "";
  const nameHit = !dterm || deburr(club.name).includes(dterm);
  let playerHit=false;
  if(dterm && !nameHit){ for(const c of club.codes){ const pn=PLAYER[c]; if(pn && deburr(pn).includes(dterm)){ playerHit=true; break; } } }
  const matchClub = !term || nameHit || playerHit;
  const card=document.createElement("div"); card.className="team"; card.style.setProperty("--accent", club.color||"#0067B9");
  let have=0; club.codes.forEach(c=>{ if(getCount(c)>0) have++; });
  const tot=club.codes.length; const pct=Math.round(have/tot*100);
  const th=document.createElement("div"); th.className="team-h";
  th.innerHTML='<span class="flag" style="background:'+(club.color||"#0067B9")+';color:#fff;border-radius:9px;width:38px;height:38px;display:flex;align-items:center;justify-content:center;font-weight:900;font-size:12px;letter-spacing:-.03em;font-family:var(--font-disp)">'+(CLUB_ABBR[club.name]||club.name.charAt(0))+'</span><div class="team-name"><div class="weare">Club</div><div class="nm">'+club.name+'</div><div class="cd">'+(STANDING[club.name]?('<span class="cstand'+(STANDING[club.name].indexOf("🏆")>-1||STANDING[club.name].indexOf("⬇")>-1?' gold':'')+'">'+STANDING[club.name]+'</span> · '):'')+tot+' jug.</div></div><div class="tprog '+(have===tot?'full':'')+'"><div class="fr">'+have+'/'+tot+'</div><div class="bar"><i style="width:'+pct+'%"></i></div></div>';
  card.appendChild(th);
  const cells=document.createElement("div"); cells.className="cells"; let vis=0;
  club.codes.forEach(c=>{ const meta=CODE_TEAM[c]; const num=meta?c.slice(meta.code.length):c;
    const cell=buildCell(c, num, {viewer:true});
    const isHit = dterm && !nameHit && PLAYER[c] && deburr(PLAYER[c]).includes(dterm);
    if(isHit) cell.classList.add("hit");
    const show=matchClub&&passFilter(c); if(!show)cell.classList.add("hide"); else vis++; cells.appendChild(cell); });
  card.appendChild(cells); if(vis===0) card.classList.add("hide");
  th.insertAdjacentHTML("beforeend",'<span class="rchev" aria-hidden="true">\u203a</span>');
  th.style.cursor="pointer"; th.setAttribute("role","button");
  th.addEventListener("click",()=>openSquad(club));
  return card;
}
function render(){
  main.innerHTML=""; main.classList.toggle("ov-compact", sortMode==="overview"); const term=searchTerm.trim().toLowerCase();
  if(sortMode==="clubs"){
    const present=CLUB_COUNTRIES.filter(co=>CLUBS.some(c=>c.country===co.key));
    if(clubCountry!=="all" && !present.some(co=>co.key===clubCountry)) clubCountry="all";
    const bar=document.createElement("div"); bar.className="filters clubnav";
    const mk=(key,label)=>{ const b=document.createElement("button"); b.type="button"; b.className="fbtn"+(clubCountry===key?" active":""); b.innerHTML=label; b.onclick=()=>{ clubCountry=key; render(); }; return b; };
    bar.appendChild(mk("all","🌐 Todos"));
    present.forEach(co=>{ const cnt=CLUBS.filter(c=>c.country===co.key).length; bar.appendChild(mk(co.key, co.flag+" "+co.name+' <span class="b">'+cnt+'</span>')); });
    main.appendChild(bar);
    let anyAll=false;
    present.forEach(co=>{
      if(clubCountry!=="all" && co.key!==clubCountry) return;
      const clubs=CLUBS.filter(c=>c.country===co.key).slice().sort((a,b)=>b.codes.length-a.codes.length || a.name.localeCompare(b.name,"es"));
      const gWrap=document.createElement("div"); gWrap.className="grp";
      const gh=document.createElement("div"); gh.className="grp-h";
      gh.innerHTML='<span class="tag" style="background:linear-gradient(90deg,#0067B9,#00802B)">'+co.flag+' '+co.name+'</span><span class="line"></span>';
      gWrap.appendChild(gh);
      let any=false;
      clubs.forEach(club=>{ const card=makeClub(club, term); if(!card.classList.contains("hide")) any=true; gWrap.appendChild(card); });
      if(!any) gWrap.classList.add("hide"); else anyAll=true;
      main.appendChild(gWrap);
    });
    if(!anyAll){
      const es=document.createElement("div"); es.className="empty";
      const t=term?"Ningún club coincide":"Sin clubes que mostrar";
      const s=term?"Borra el texto del buscador para ver todos los clubes.":(currentFilter==="miss"?"¡No te falta ninguna de estos clubes! 🎉":currentFilter==="dup"?"No tienes repetidas de estos clubes.":"");
      es.innerHTML='<div class="empty-ico">🏟️</div><div class="empty-t">'+t+'</div>'+(s?'<div class="empty-s">'+s+'</div>':"");
      main.appendChild(es);
    }
    updateStats(); return;
  }
  if(sortMode==="alpha"){
    const flat=[]; GROUPS.forEach(g=>g.teams.forEach(t=>flat.push(t)));
    flat.sort((a,b)=>a[1].localeCompare(b[1],"es"));
    const gWrap=document.createElement("div"); gWrap.className="grp";
    const gh=document.createElement("div"); gh.className="grp-h";
    gh.innerHTML='<span class="tag" style="background:linear-gradient(90deg,#0067B9,#6A1B9A)">Equipos A–Z</span><span class="line"></span>';
    gWrap.appendChild(gh); let anyVis=false;
    flat.forEach(t=>{ const team=makeTeam(t[0],t[1],t[2],term); if(!team.classList.contains("hide")) anyVis=true; gWrap.appendChild(team); });
    if(!anyVis) gWrap.classList.add("hide"); main.appendChild(gWrap);
  } else {
    GROUPS.forEach(g=>{
      const gWrap=document.createElement("div"); gWrap.className="grp";
      const gh=document.createElement("div"); gh.className="grp-h";
      const _gp=(state.config.groupPages&&state.config.groupPages[g.id])||(typeof GROUP_PAGES!=="undefined"&&GROUP_PAGES[g.id])||"";
      gh.innerHTML='<span class="tag" style="background:'+g.color+'">Grupo '+g.id+'</span><span class="line"></span><span class="grp-pg"><span class="pgic">📄</span><span class="gp-val" contenteditable="true" spellcheck="false" data-ph="pág.">'+escHTML(_gp)+'</span></span>';
      const _gpEl=gh.querySelector(".gp-val");
      _gpEl.addEventListener("click",e=>e.stopPropagation());
      _gpEl.addEventListener("keydown",e=>{ if(e.key==="Enter"){ e.preventDefault(); _gpEl.blur(); } });
      _gpEl.addEventListener("blur",()=>{ const v=(_gpEl.textContent||"").replace(/[^0-9–-]/g,"").slice(0,9); _gpEl.textContent=v; if(!state.config.groupPages) state.config.groupPages={}; if(v) state.config.groupPages[g.id]=v; else delete state.config.groupPages[g.id]; save(); });
      gWrap.appendChild(gh); let groupHasVisible=false;
      g.teams.forEach(t=>{ const team=makeTeam(t[0],t[1],t[2],term); if(!team.classList.contains("hide")) groupHasVisible=true; gWrap.appendChild(team); });
      if(!groupHasVisible) gWrap.classList.add("hide"); main.appendChild(gWrap);
    });
  }
  if(state.config.specials){
    const gWrap=document.createElement("div"); gWrap.className="grp";
    gWrap.innerHTML='<div class="grp-h"><span class="tag" style="background:linear-gradient(90deg,#ffb020,#ff8a00)">Especiales</span><span class="line"></span></div>';
    const team=document.createElement("div"); team.className="team"; team.style.setProperty("--accent","#F2A20C");
    let have=0; SPECIAL_CODES.forEach(c=>{ if(getCount(c)>0) have++; });
    const pct=Math.round(have/SPECIAL_CODES.length*100);
    team.innerHTML='<div class="team-h"><span class="flag">⭐</span><div class="team-name"><div class="nm">Especiales FIFA</div><div class="cd">00 · FWC1–FWC19 · repartidas por el álbum</div></div><div class="tprog '+(have===SPECIAL_CODES.length?'full':'')+'"><div class="fr">'+have+'/'+SPECIAL_CODES.length+'</div><div class="bar"><i style="width:'+pct+'%"></i></div></div></div>';
    const list=document.createElement("div"); list.className="squad-list spec-list"; let vis=0;
    SPECIAL_CODES.forEach(c=>{
      const nm=SPECIAL_NAMES[c]||c;
      const match=!term||nm.toLowerCase().includes(term)||c.toLowerCase().includes(term)||"especiales fifa fwc 00".includes(term);
      if(!(match&&passFilter(c))) return;
      vis++;
      const s=cellState(c), n=getCount(c);
      const numLabel=c==="00"?"00":c.replace("FWC","");
      const pg=(state.config.specialPages&&state.config.specialPages[c])||(typeof SPECIAL_PAGES!=="undefined"&&SPECIAL_PAGES[c])||"";
      const row=document.createElement("div"); row.className="srow"+(s===1?" have":s===2?" dup":""); row.dataset.code=c;
      row.innerHTML='<span class="snum">'+numLabel+(n>1?'<span class="sdup">+'+(n-1)+'</span>':'')+'</span>'
        +'<span class="sinfo"><span class="sname">'+escHTML(nm)+'</span><span class="scode">'+c+'</span></span>'
        +'<span class="sright"><span class="specpage" contenteditable="true" spellcheck="false">'+escHTML(pg)+'</span></span>';
      const pgEl=row.querySelector(".specpage");
      pgEl.addEventListener("click",e=>e.stopPropagation());
      pgEl.addEventListener("keydown",e=>{ if(e.key==="Enter"){ e.preventDefault(); pgEl.blur(); } });
      pgEl.addEventListener("blur",()=>{ const v=(pgEl.textContent||"").replace(/[^0-9]/g,"").slice(0,3); pgEl.textContent=v; if(!state.config.specialPages) state.config.specialPages={}; if(v) state.config.specialPages[c]=v; else delete state.config.specialPages[c]; save(); });
      row.addEventListener("click",()=>openEditor(c));
      list.appendChild(row);
    });
    team.appendChild(list); if(vis===0){ team.classList.add("hide"); if(term) gWrap.classList.add("hide"); } gWrap.appendChild(team); main.appendChild(gWrap);
  }
  if(state.config.extras){
    const matchE=!term||"extra stickers foil leyendas oro plata bronce morado".includes(term);
    const gWrap=document.createElement("div"); gWrap.className="grp";
    gWrap.innerHTML='<div class="grp-h"><span class="tag" style="background:linear-gradient(90deg,#7c5cff,#ff2d6f)">Extra</span><span class="line"></span></div>';
    for(let p=1;p<=EXTRA_COUNT;p++){
      const team=document.createElement("div"); team.className="team";
      const pname=state.config.extraNames["p"+p]||("Jugador "+p);
      let have=0; EXTRA_COLORS.forEach(a=>{ if(getCount("X"+p+a[0])>0) have++; });
      team.innerHTML='<div class="team-h"><span class="flag">✨</span><div class="team-name"><div class="nm" contenteditable="true" data-pl="'+p+'" spellcheck="false">'+escHTML(pname)+'</div><div class="cd">Extra · 4 colores</div></div><div class="tprog '+(have===4?'full':'')+'"><div class="fr">'+have+'/4</div><div class="bar"><i style="width:'+(have/4*100)+'%"></i></div></div></div>';
      const cells=document.createElement("div"); cells.className="cells"; cells.style.gridTemplateColumns="repeat(4,1fr)"; let vis=0;
      EXTRA_COLORS.forEach(a=>{ const c="X"+p+a[0]; const cell=buildCell(c,a[2]); const show=matchE&&passFilter(c); if(!show)cell.classList.add("hide"); else vis++; cells.appendChild(cell); });
      team.appendChild(cells); if(vis===0) team.classList.add("hide");
      const nmEl=team.querySelector("[data-pl]"); nmEl.addEventListener("blur",()=>{ state.config.extraNames["p"+p]=nmEl.textContent.trim()||("Jugador "+p); save(); });
      gWrap.appendChild(team);
    }
    main.appendChild(gWrap);
  }
  // Aviso si no quedó NADA visible (evita la pantalla en blanco al buscar/filtrar)
  if(!main.querySelector(".grp:not(.hide)")){
    const es=document.createElement("div"); es.className="empty";
    let t,s;
    if(term){ t="Nada coincide con tu búsqueda"; s="Borra el texto del buscador de arriba para ver todo el álbum de nuevo."; }
    else if(currentFilter==="dup"){ t="Sin repetidas todavía"; s="Cuando tengas láminas repetidas van a aparecer acá."; }
    else if(currentFilter==="have"){ t="Aún no marcas láminas"; s="Anota un código arriba (ej: ARG5) para empezar a llenar tu álbum."; }
    else if(currentFilter==="miss"){ t="¡No te falta ninguna! 🎉"; s="Completaste todo lo que se está mostrando."; }
    else { t="No hay nada que mostrar"; s=""; }
    es.innerHTML='<div class="empty-ico">🃏</div><div class="empty-t">'+t+'</div>'+(s?'<div class="empty-s">'+s+'</div>':"");
    main.appendChild(es);
  }
  updateStats();
}
function passFilter(c){ const n=getCount(c); if(currentFilter==="have")return n>=1; if(currentFilter==="miss")return n===0; if(currentFilter==="dup")return n>=2; return true; }

/* ---------- stats ---------- */
function allActiveCodes(){ const arr=[...ALL_TEAM_CODES]; if(state.config.specials) arr.push(...SPECIAL_CODES); if(state.config.extras) for(let p=1;p<=EXTRA_COUNT;p++) EXTRA_COLORS.forEach(a=>arr.push("X"+p+a[0])); return arr; }
function updateStats(){
  const codes=allActiveCodes(); const total=codes.length; let tengo=0,repe=0;
  codes.forEach(c=>{ const n=getCount(c); if(n>=1) tengo++; if(n>=2) repe+=n-1; });
  const faltan=total-tengo;
  document.getElementById("stTengo").textContent=tengo; document.getElementById("stFaltan").textContent=faltan;
  document.getElementById("stRepe").textContent=repe; document.getElementById("stTotal").textContent=total;
  document.getElementById("cAll").textContent=total; document.getElementById("cHave").textContent=tengo;
  document.getElementById("cMiss").textContent=faltan; document.getElementById("cDup").textContent=repe;
  const pct=total?tengo/total:0, circ=157.1;
  const rf=document.getElementById("ringFill"); rf.style.strokeDashoffset=(circ*(1-pct)).toFixed(1);
  document.getElementById("ringTxt").textContent=Math.round(pct*100)+"%";
  const _mp=document.getElementById("miniPct"); if(_mp) _mp.textContent=Math.round(pct*100)+"%";
  document.getElementById("ringSvg").setAttribute("aria-label","Progreso del álbum: "+Math.round(pct*100)+"%");
}

/* ---------- editor ---------- */
let editCode=null; const ovEdit=document.getElementById("ovEdit");
function openEditor(code){
  editCode=code; const meta=CODE_TEAM[code]; let who=SPECIAL_NAMES[code]?("⭐ "+SPECIAL_NAMES[code]):"Especial FIFA";
  if(meta) who=flagHTML(meta.code, meta.flag, "flaginline")+meta.name+" · Grupo "+meta.group;
  else if(code[0]==="X"){ const p=code.match(/X(\d+)/)[1]; who="✨ "+escHTML(state.config.extraNames["p"+p]||("Jugador "+p)); }
  document.getElementById("edCode").textContent=displayCode(code); document.getElementById("edWho").innerHTML=who;
  const pn=playerName(code); const ep=document.getElementById("edPlayer"); ep.textContent=pn; ep.style.display=pn?"block":"none";
  const ps=playerPos(code); const epz=document.getElementById("edPos"); epz.textContent=ps; epz.style.display=ps?"inline-block":"none";
  const sv=playerStat(code); const esr=document.getElementById("edStatRo"); esr.textContent=sv; esr.style.display=sv?"block":"none";
  const mv=playerValue(code); const evz=document.getElementById("edValue"); evz.textContent=mv; evz.style.display=mv?"block":"none";
  refreshEditor(); ovEdit.classList.add("show");
}
function displayCode(code){
  if(code[0]==="X"){ const m=code.match(/X(\d+)([A-Z])/); const col=EXTRA_COLORS.find(c=>c[0]===m[2]); return "#"+m[1]+" "+(col?col[1]:""); }
  const meta=CODE_TEAM[code]; if(meta){ return meta.code+" "+code.slice(meta.code.length); } return code;
}
function refreshEditor(){
  const n=getCount(editCode); document.getElementById("edCnt").textContent=n; const st=document.getElementById("edSt");
  if(n===0){ st.className="st s0"; st.textContent="No la tengo"; }
  else if(n===1){ st.className="st s1"; st.textContent="✓ La tengo"; }
  else { st.className="st s2"; st.textContent="Repetida ×"+n+" ("+(n-1)+" para cambio)"; }
}
document.getElementById("edPlus").onclick=()=>{ setCount(editCode,getCount(editCode)+1); refreshEditor(); refreshCellInDom(editCode); updateStats(); };
document.getElementById("edMinus").onclick=()=>{ setCount(editCode,getCount(editCode)-1); refreshEditor(); refreshCellInDom(editCode); updateStats(); };
document.getElementById("edRemove").onclick=()=>{ setCount(editCode,0); refreshEditor(); refreshCellInDom(editCode); updateStats(); };
document.getElementById("edDone").onclick=()=>{ ovEdit.classList.remove("show"); if(currentFilter!=="all") render(); };
var _sb=document.getElementById("subToggle"); if(_sb) _sb.onclick=function(){
  subMode=!subMode;
  _sb.classList.toggle("on", subMode);
  _sb.textContent = subMode ? "⊖ Restando" : "⊖ Restar";
  _sb.setAttribute("aria-pressed", subMode?"true":"false");
  document.body.classList.toggle("submode", subMode);
  var _mh=document.getElementById("modeHint"); if(_mh) _mh.textContent = subMode ? "👆 toca una lámina pa\u0027 quitarla" : "";
  toast(subMode ? "Modo restar ON — toca pa' quitar" : "Modo restar OFF");
};
function refreshCellInDom(code){
  updateSquadRow(code);
  const cell=main.querySelector('.cell[data-code="'+cssEsc(code)+'"]'); if(!cell) return;
  const st=cellState(code); cell.classList.remove("have","dup");
  if(st===1) cell.classList.add("have"); else if(st===2) cell.classList.add("dup");
  cellContent(cell, code, cell.dataset.label||cell.textContent);
  const teamEl=cell.closest(".team"); if(teamEl) refreshTeamProgress(teamEl); updateRosterRow(code);
}
function refreshTeamProgress(teamEl){
  const cells=[...teamEl.querySelectorAll(".cell")]; let have=0; cells.forEach(c=>{ if(getCount(c.dataset.code)>0) have++; });
  const tot=cells.length; const fr=teamEl.querySelector(".fr"),bar=teamEl.querySelector(".bar i"),tp=teamEl.querySelector(".tprog");
  if(fr) fr.textContent=have+"/"+tot; if(bar) bar.style.width=(have/tot*100)+"%"; if(tp) tp.classList.toggle("full",have===tot);
}
function cssEsc(s){ return s.replace(/["\\]/g,"\\$&"); }
function escHTML(s){ return String(s).replace(/[&<>"]/g,function(c){return {"&":"&amp;","<":"&lt;",">":"&gt;","\"":"&quot;"}[c];}); }
function updateRosterRow(code){ const row=main.querySelector('.rrow[data-code="'+cssEsc(code)+'"]'); if(!row) return; const s=cellState(code); row.classList.remove("have","dup"); if(s===1) row.classList.add("have"); else if(s===2) row.classList.add("dup"); }

/* ---------- nómina del club en ventana ---------- */
let _squadClub=null;
function squadSubText(club){ let have=0; club.codes.forEach(c=>{ if(getCount(c)>0) have++; }); return (STANDING[club.name]?STANDING[club.name]+" · ":"")+have+"/"+club.codes.length+" láminas"; }
function buildSquadRow(c){
  const meta=CODE_TEAM[c]; const num=meta?c.slice(meta.code.length):c; const s=cellState(c), n=getCount(c);
  const row=document.createElement("div"); row.className="srow"+(s===1?" have":s===2?" dup":""); row.dataset.code=c;
  const nm=playerName(c)||c, pos=playerPos(c), sv=playerStat(c), mv=MARKET_VALUE[c];
  let right=""; if(pos) right+='<span class="spos">'+pos+'</span>'; if(mv) right+='<span class="sval">💰 '+mv+'</span>';
  row.innerHTML='<span class="snum">'+num+(n>1?'<span class="sdup">+'+(n-1)+'</span>':'')+'</span>'
    +'<span class="sinfo"><span class="sname">'+escHTML(nm)+'</span>'+(sv?'<span class="sstat">'+escHTML(sv)+'</span>':'')+'</span>'
    +'<span class="sright">'+right+'</span>';
  row.addEventListener("click",()=>openEditor(c));
  return row;
}
function openSquad(club){
  _squadClub=club;
  document.getElementById("squadTitle").textContent=club.name;
  document.getElementById("squadSub").textContent=squadSubText(club);
  const list=document.getElementById("squadList"); list.innerHTML="";
  club.codes.forEach(c=>list.appendChild(buildSquadRow(c)));
  document.getElementById("ovSquad").classList.add("show");
}
function updateSquadRow(code){
  const row=document.querySelector('#squadList .srow[data-code="'+cssEsc(code)+'"]'); if(!row) return;
  const s=cellState(code), n=getCount(code); row.classList.remove("have","dup"); if(s===1) row.classList.add("have"); else if(s===2) row.classList.add("dup");
  const numEl=row.querySelector(".snum"); if(numEl){ const old=numEl.querySelector(".sdup"); if(old) old.remove(); if(n>1){ const b=document.createElement("span"); b.className="sdup"; b.textContent="+"+(n-1); numEl.appendChild(b); } }
  if(_squadClub){ const sub=document.getElementById("squadSub"); if(sub) sub.textContent=squadSubText(_squadClub); }
}

/* ---------- recién agregadas (sesión) ---------- */
let recent=[];
function pushRecent(code){ recent.unshift(code); if(recent.length>25) recent.pop(); renderRecent(); }
function renderRecent(){
  const tray=document.getElementById("recentTray");
  if(!recent.length){ tray.style.display="none"; tray.innerHTML=""; return; }
  tray.style.display="flex"; tray.innerHTML="";
  const lab=document.createElement("span"); lab.className="rlabel"; lab.textContent="Recién:"; tray.appendChild(lab);
  recent.slice(0,18).forEach(code=>{
    const c=document.createElement("button"); c.className="rchip"; c.title="Tocar para deshacer";
    const t=document.createElement("span"); t.textContent=displayCode(code);
    const u=document.createElement("span"); u.className="u"; u.textContent="✕";
    c.appendChild(t); c.appendChild(u);
    c.onclick=()=>{ setCount(code,getCount(code)-1); refreshCellInDom(code); updateStats(); const i=recent.indexOf(code); if(i>=0) recent.splice(i,1); renderRecent(); toast("Deshecho "+displayCode(code)); };
    tray.appendChild(c);
  });
}

/* ---------- quick add ---------- */
function addByCode(raw,silent){
  const c=normalize(raw); if(!c){ if(!silent) toast("Código no válido ✗"); return false; }
  setCount(c,getCount(c)+1); refreshCellInDom(c); updateStats(); pushRecent(c); const n=getCount(c);
  if(n===1) sfxPop(); toast((n===1?(hype()+" 👍🗿 "+displayCode(c)+" ✓"):(displayCode(c)+" · repetida ×"+n))); return true;
}
const qa=document.getElementById("quickAdd");
function doQuickAdd(){
  const v=qa.value.trim(); if(!v){ qa.focus(); return; } const codes=findCodes(v);
  if(codes.length>1){ codes.forEach(c=>{ setCount(c,getCount(c)+1); refreshCellInDom(c); pushRecent(c); }); updateStats(); toast(codes.length+" láminas sumadas ✓"); }
  else { addByCode(v,false); } qa.value=""; qa.focus();
}
document.getElementById("qaBtn").onclick=doQuickAdd;
qa.addEventListener("keydown",e=>{ if(e.key==="Enter"){ e.preventDefault(); doQuickAdd(); }});

/* ---------- filters + search ---------- */
document.getElementById("filters").addEventListener("click",e=>{ const b=e.target.closest(".fbtn"); if(!b) return;
  document.querySelectorAll(".fbtn").forEach(x=>x.classList.remove("active")); b.classList.add("active"); currentFilter=b.dataset.f; render(); });
let searchTimer; document.getElementById("search").addEventListener("input",e=>{ searchTerm=e.target.value; clearTimeout(searchTimer); searchTimer=setTimeout(render,140); });
function updateSearchPlaceholder(){ const s=document.getElementById("search"); if(!s) return; s.placeholder = sortMode==="clubs" ? "Busca club o jugador (ej. Real Madrid, Mbappé)…" : "Busca selección o jugador (ej. Argentina, Messi)…"; }
document.getElementById("sortSeg").addEventListener("click",e=>{ const b=e.target.closest("button"); if(!b) return; [...e.currentTarget.children].forEach(x=>x.classList.remove("active")); b.classList.add("active"); sortMode=b.dataset.s; updateSearchPlaceholder(); render(); });

/* ---------- settings ---------- */
const ovSet=document.getElementById("ovSet"),tgSpecials=document.getElementById("tgSpecials"),tgExtras=document.getElementById("tgExtras");
document.getElementById("btnSet").onclick=()=>{ tgSpecials.checked=state.config.specials; tgExtras.checked=state.config.extras; const tc=document.getElementById("tgCelebrate"); if(tc) tc.checked=state.config.celebrate; const tsd=document.getElementById("tgSound"); if(tsd) tsd.checked=state.config.sound!==false; if(typeof showAdminUI==="function") showAdminUI(); const _ps=document.getElementById("pgStart"); if(_ps) _ps.value=state.config.teamsStartPage||""; ovSet.classList.add("show"); };
tgSpecials.onchange=()=>{ state.config.specials=tgSpecials.checked; save(); render(); };
tgExtras.onchange=()=>{ state.config.extras=tgExtras.checked; save(); render(); };
(function(){ const tc=document.getElementById("tgCelebrate"); if(tc) tc.onchange=()=>{ state.config.celebrate=tc.checked; save(); }; })();
(function(){ const td=document.getElementById("tgSound"); if(td) td.onchange=()=>{ state.config.sound=td.checked; save(); if(td.checked) sfxPop(); }; })();
(function(){
  const calcBtn=document.getElementById("pgCalc"), startInp=document.getElementById("pgStart"), clearBtn=document.getElementById("pgClear");
  if(startInp) startInp.value=state.config.teamsStartPage||"";
  if(calcBtn) calcBtn.onclick=()=>{
    const S=parseInt(startInp.value,10);
    if(!S||S<1){ toast("Pon la página donde empieza México"); return; }
    state.config.teamsStartPage=S;
    if(!state.config.groupPages) state.config.groupPages={};
    GROUPS.forEach((g,i)=>{ const off=(i>=6?2:0); const a=S+i*8+off, b=a+7; state.config.groupPages[g.id]=a+"–"+b; });
    save(); render(); toast("Páginas calculadas ✓ (ajusta si hace falta)");
  };
  if(clearBtn) clearBtn.onclick=()=>{ state.config.groupPages={}; state.config.teamsStartPage=0; if(startInp) startInp.value=""; save(); render(); toast("Páginas borradas"); };
})();
(function(){ const sb=document.getElementById("starClose"), ov=document.getElementById("ovStar"); if(sb) sb.onclick=closeStar; if(ov) ov.addEventListener("click",e=>{ if(e.target===ov) closeStar(); }); })();
document.getElementById("btnExport").onclick=()=>{ const blob=new Blob([JSON.stringify(state,null,2)],{type:"application/json"}); const a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download="album-mundial-2026-respaldo.json"; a.click(); URL.revokeObjectURL(a.href); toast("Respaldo descargado ⬇️"); };

/* ----- Exportar a Excel (.xlsx) ----- */
function ensureXLSX(cb){
  if(typeof XLSX!=="undefined"){ cb(); return; }
  const s=document.createElement("script");
  s.src="https://cdn.jsdelivr.net/npm/xlsx-js-style@1.2.0/dist/xlsx.bundle.js";
  s.onload=cb; s.onerror=()=>toast("No se pudo cargar Excel (¿sin conexión?) ✗");
  document.head.appendChild(s);
}
function codeLabel(c){
  if(CODE_TEAM[c]){ const t=CODE_TEAM[c]; return t.name+" "+c.slice(t.code.length); }
  if(c==="00"||/^FWC\d+$/.test(c)) return "Especial: "+(SPECIAL_NAMES[c]||c)+" ("+c+")";
  const m=c.match(/^X(\d+)([OPBM])$/); if(m){ const pn=state.config.extraNames["p"+m[1]]||("Jugador "+m[1]); const col={O:"Oro",P:"Plata",B:"Bronce",M:"Morado"}[m[2]]; return "Extra: "+pn+" ("+col+")"; }
  return c;
}
function buildAlbumWorkbook(){
  const codes=allActiveCodes(); let tengo=0,repe=0;
  codes.forEach(c=>{ const n=getCount(c); if(n>=1)tengo++; if(n>=2)repe+=n-1; });
  const total=codes.length, faltan=total-tengo, pct=total?Math.round(tengo/total*100):0;
  let full=0; GROUPS.forEach(g=>g.teams.forEach(t=>{ if(teamHave(t[0])===20) full++; }));
  const now=new Date();
  const wb=XLSX.utils.book_new();

  const FONT="Arial";
  const thin=c=>{ const s={style:"thin",color:{rgb:c}}; return {top:s,bottom:s,left:s,right:s}; };
  const setS=(ws,r,c,s)=>{ const ref=XLSX.utils.encode_cell({r:r,c:c}); if(!ws[ref]) ws[ref]={t:"s",v:""}; ws[ref].s=s; };
  const hdr={ font:{name:FONT,bold:true,sz:11,color:{rgb:"FFFFFF"}}, fill:{patternType:"solid",fgColor:{rgb:"1A1A22"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("3A3A45") };

  // Resumen
  const wsR=XLSX.utils.aoa_to_sheet([
    ["MI ÁLBUM — Mundial 2026",""],
    ["Exportado", now.toLocaleString()],
    ["",""],
    ["Tengo", tengo],["Faltan", faltan],["Repetidas", repe],["Total", total],
    ["Completado", pct+"%"],["Equipos completos", full+" / 48"]
  ]);
  wsR["!cols"]=[{wch:22},{wch:26}];
  wsR["!merges"]=[{s:{r:0,c:0},e:{r:0,c:1}}];
  wsR["!rows"]=[{hpt:30}];
  setS(wsR,0,0,{ font:{name:FONT,bold:true,sz:16,color:{rgb:"FFFFFF"}}, fill:{patternType:"solid",fgColor:{rgb:"0067B9"}}, alignment:{horizontal:"center",vertical:"center"} });
  setS(wsR,0,1,{ fill:{patternType:"solid",fgColor:{rgb:"0067B9"}} });
  setS(wsR,1,0,{ font:{name:FONT,bold:true,color:{rgb:"6B6B78"}} });
  const sc=["1B7A35","1A1A22","B97700","1A1A22","0067B9","1A1A22"];
  for(let i=0;i<6;i++){ const r=3+i;
    setS(wsR,r,0,{ font:{name:FONT,bold:true,sz:11,color:{rgb:"1A1A22"}}, fill:{patternType:"solid",fgColor:{rgb:"F3F2EF"}} });
    setS(wsR,r,1,{ font:{name:FONT,bold:true,sz:12,color:{rgb:sc[i]}} });
  }
  XLSX.utils.book_append_sheet(wb, wsR, "Resumen");

  // Por equipo (matriz estilo álbum: grupo fusionado + colores)
  const head=["Grupo","Código","Equipo"]; for(let i=1;i<=20;i++) head.push(i); head.push("Tengo","Repetidas");
  const matrix=[head];
  GROUPS.forEach(g=>g.teams.forEach((t,ti)=>{
    const row=[ti===0?("Grupo "+g.id):"", t[0], t[2]+" "+t[1]]; let th=0,tr=0;
    for(let i=1;i<=20;i++){ const n=getCount(t[0]+i); row.push(n>0?n:""); if(n>=1)th++; if(n>=2)tr+=n-1; }
    row.push(th,tr); matrix.push(row);
  }));
  const wsM=XLSX.utils.aoa_to_sheet(matrix);
  wsM["!cols"]=[{wch:8},{wch:7},{wch:20}].concat(Array(20).fill({wch:4.5})).concat([{wch:7},{wch:11}]);
  for(let c=0;c<head.length;c++) setS(wsM,0,c,hdr);
  const merges=[];
  GROUPS.forEach((g,gi)=>{
    const r0=1+gi*4;
    merges.push({s:{r:r0,c:0},e:{r:r0+3,c:0}});
    setS(wsM,r0,0,{ font:{name:FONT,bold:true,sz:11,color:{rgb:"FFFFFF"}}, fill:{patternType:"solid",fgColor:{rgb:g.color.replace("#","")}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("FFFFFF") });
    for(let ti=0;ti<4;ti++){
      const r=r0+ti, code=g.teams[ti][0], th=teamHave(code);
      setS(wsM,r,1,{ font:{name:FONT,sz:9,color:{rgb:"9A9AA6"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("ECEAE6") });
      setS(wsM,r,2,{ font:{name:FONT,bold:true,sz:10,color:{rgb:"1A1A22"}}, fill:{patternType:"solid",fgColor:{rgb:"FAFAF8"}}, alignment:{horizontal:"left",vertical:"center"}, border:thin("ECEAE6") });
      for(let i=1;i<=20;i++){ const n=getCount(code+i), c=2+i; let s;
        if(n>=2) s={ font:{name:FONT,bold:true,sz:10,color:{rgb:"4A3500"}}, fill:{patternType:"solid",fgColor:{rgb:"F2A20C"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("FFFFFF") };
        else if(n>=1) s={ font:{name:FONT,bold:true,sz:10,color:{rgb:"FFFFFF"}}, fill:{patternType:"solid",fgColor:{rgb:"2E9E4A"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("FFFFFF") };
        else s={ fill:{patternType:"solid",fgColor:{rgb:"F5F5F4"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("E6E4E0") };
        setS(wsM,r,c,s);
      }
      setS(wsM,r,23,{ font:{name:FONT,bold:true,sz:10,color:{rgb: th===20?"1B7A35":"1A1A22"}}, fill:{patternType:"solid",fgColor:{rgb: th===20?"D9F2DF":"F3F2EF"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("ECEAE6") });
      setS(wsM,r,24,{ font:{name:FONT,sz:10,color:{rgb:"B97700"}}, alignment:{horizontal:"center",vertical:"center"}, border:thin("ECEAE6") });
    }
  });
  wsM["!merges"]=merges;
  XLSX.utils.book_append_sheet(wb, wsM, "Por equipo");

  // Detalle
  const det=[["Código","Equipo","Grupo","Número","Tengo","Cantidad","Repetidas"]];
  GROUPS.forEach(g=>g.teams.forEach(t=>{
    for(let i=1;i<=20;i++){ const c=t[0]+i,n=getCount(c); det.push([c,t[2]+" "+t[1],g.id,i,n>=1?"Sí":"No",n,n>=2?n-1:0]); }
  }));
  if(state.config.specials) SPECIAL_CODES.forEach(c=>{ const n=getCount(c); det.push([c,"Especiales FIFA","ESP",c,n>=1?"Sí":"No",n,n>=2?n-1:0]); });
  if(state.config.extras) for(let p=1;p<=EXTRA_COUNT;p++){ const pn=state.config.extraNames["p"+p]||("Jugador "+p); EXTRA_COLORS.forEach(a=>{ const c="X"+p+a[0],n=getCount(c); det.push([c,"Extra: "+pn,"EXTRA",a[1],n>=1?"Sí":"No",n,n>=2?n-1:0]); }); }
  const wsD=XLSX.utils.aoa_to_sheet(det);
  wsD["!cols"]=[{wch:8},{wch:22},{wch:7},{wch:8},{wch:7},{wch:9},{wch:10}];
  for(let c=0;c<7;c++) setS(wsD,0,c,hdr);
  XLSX.utils.book_append_sheet(wb, wsD, "Detalle");

  // Repetidas
  const rep=[["Código","Equipo","Repetidas"]];
  codes.forEach(c=>{ const n=getCount(c); if(n>=2) rep.push([c, codeLabel(c), n-1]); });
  if(rep.length===1) rep.push(["—","(sin repetidas aún)",""]);
  const wsRep=XLSX.utils.aoa_to_sheet(rep);
  wsRep["!cols"]=[{wch:8},{wch:26},{wch:10}];
  for(let c=0;c<3;c++) setS(wsRep,0,c,hdr);
  XLSX.utils.book_append_sheet(wb, wsRep, "Repetidas");

  return wb;
}
function exportExcel(){
  if(typeof XLSX==="undefined"){ toast("Cargando Excel… intenta otra vez ✗"); return; }
  const wb=buildAlbumWorkbook();
  XLSX.writeFile(wb, "MiAlbumMundial2026_"+new Date().toISOString().slice(0,10)+".xlsx");
  toast("Excel descargado 📊");
}
function albumExcelBlob(){ const wb=buildAlbumWorkbook(); const arr=XLSX.write(wb,{bookType:"xlsx",type:"array"}); return new Blob([arr],{type:"application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"}); }

/* ===== Sincronización automática con Google Drive (Google Sheet) ===== */
const DRIVE_KEY="albumDriveCfg_v1";
function driveCfg(){ try{ return Object.assign({clientId:"",fileId:"",auto:false}, JSON.parse(localStorage.getItem(DRIVE_KEY))||{}); }catch(e){ return {clientId:"",fileId:"",auto:false}; } }
function saveDriveCfg(c){ try{ localStorage.setItem(DRIVE_KEY, JSON.stringify(c)); }catch(e){} }
let gisReady=false, gisClient=null, driveTok=null, driveTokExp=0, drivePushing=false, driveTimer=null, driveCb=null;
const ovDrive=document.getElementById("ovDrive");
function driveStatusMsg(m){ const el=document.getElementById("driveStatus"); if(el) el.textContent=m; }
function showDriveLink(){ const c=driveCfg(), w=document.getElementById("driveLinkWrap"), a=document.getElementById("driveLink"); if(c.fileId){ w.style.display="flex"; a.href="https://docs.google.com/spreadsheets/d/"+c.fileId+"/edit"; } else { w.style.display="none"; } }
function openDrive(){ const c=driveCfg(), setup=document.getElementById("driveSetup"), conn=document.getElementById("driveConnected"); if(c.clientId){ setup.style.display="none"; conn.style.display="block"; document.getElementById("driveAuto").checked=!!c.auto; driveStatusMsg(c.fileId?"Conectado ✓":"Conectado. Aún no guardas nada."); } else { setup.style.display="block"; conn.style.display="none"; driveStatusMsg(""); } document.getElementById("driveClientId").value=c.clientId||""; showDriveLink(); ovDrive.classList.add("show"); }
function ensureGIS(cb){ if(gisReady && window.google && google.accounts && google.accounts.oauth2){ cb(); return; } const s=document.createElement("script"); s.src="https://accounts.google.com/gsi/client"; s.async=true; s.onload=()=>{ gisReady=true; cb(); }; s.onerror=()=>driveStatusMsg("No se pudo cargar Google ✗"); document.head.appendChild(s); }
function driveAuth(cb){ const c=driveCfg(); if(!c.clientId){ openDrive(); return; } if(driveTok && Date.now()<driveTokExp-60000){ cb(driveTok); return; } ensureGIS(()=>{ try{ if(!gisClient || gisClient.__cid!==c.clientId){ gisClient=google.accounts.oauth2.initTokenClient({ client_id:c.clientId, scope:"https://www.googleapis.com/auth/drive.file", callback:(resp)=>{ if(resp&&resp.access_token){ driveTok=resp.access_token; driveTokExp=Date.now()+((resp.expires_in||3600)*1000); const f=driveCb; driveCb=null; if(f) f(driveTok); } else { driveStatusMsg("No se autorizó Drive ✗"); } }, error_callback:()=>{ driveStatusMsg("No se pudo conectar (revisa el ID y el origen) ✗"); } }); gisClient.__cid=c.clientId; } driveCb=cb; gisClient.requestAccessToken(); }catch(e){ driveStatusMsg("Error al conectar ✗"); } }); }
async function drivePush(token){ if(drivePushing) return; drivePushing=true; try{ if(typeof XLSX==="undefined"){ await new Promise(r=>ensureXLSX(r)); } driveStatusMsg("Guardando en Drive…"); const blob=albumExcelBlob(); let c=driveCfg(); const xls="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"; let resp,data;
  if(c.fileId){ resp=await fetch("https://www.googleapis.com/upload/drive/v3/files/"+c.fileId+"?uploadType=media&fields=id",{method:"PATCH",headers:{Authorization:"Bearer "+token,"Content-Type":xls},body:blob}); if(resp.ok){ data=await resp.json(); } else { c.fileId=""; saveDriveCfg(c); } }
  if(!c.fileId){ const meta={name:"Mi Álbum Mundial 2026",mimeType:"application/vnd.google-apps.spreadsheet"}; const form=new FormData(); form.append("metadata", new Blob([JSON.stringify(meta)],{type:"application/json"})); form.append("file", blob); resp=await fetch("https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart&fields=id",{method:"POST",headers:{Authorization:"Bearer "+token},body:form}); if(!resp.ok) throw new Error("create"); data=await resp.json(); c.fileId=data.id; saveDriveCfg(c); }
  driveStatusMsg("Actualizado en Drive ✓ "+new Date().toLocaleTimeString()); showDriveLink(); toast("Guardado en Drive ✓");
 }catch(e){ driveStatusMsg("No se pudo guardar en Drive ✗ (revisa permisos / ID)"); } finally{ drivePushing=false; } }
function scheduleDriveSync(){ const c=driveCfg(); if(!c.clientId || !c.auto) return; clearTimeout(driveTimer); driveTimer=setTimeout(()=>{ if(driveTok && Date.now()<driveTokExp-60000) drivePush(driveTok); else driveStatusMsg("Cambios pendientes — abre Drive y toca «Guardar ahora»"); }, 4000); }
document.getElementById("btnDrive").onclick=()=>{ ovSet.classList.remove("show"); openDrive(); };
document.getElementById("driveCopyOrigin").onclick=()=>{ try{ navigator.clipboard.writeText("https://lukas-paredes.github.io"); toast("Copiado ✓"); }catch(e){ toast("Copia: https://lukas-paredes.github.io"); } };
document.getElementById("driveConnect").onclick=()=>{ const id=document.getElementById("driveClientId").value.trim(); const err=document.getElementById("driveErr"); err.textContent=""; if(!id){ err.textContent="Pega tu ID de cliente."; return; } const c=driveCfg(); c.clientId=id; c.auto=true; saveDriveCfg(c); driveAuth(t=>{ drivePush(t); openDrive(); }); };
document.getElementById("driveSaveNow").onclick=()=>{ driveAuth(t=>drivePush(t)); };
document.getElementById("driveAuto").onchange=(e)=>{ const c=driveCfg(); c.auto=e.target.checked; saveDriveCfg(c); if(c.auto && driveTok && Date.now()<driveTokExp-60000) drivePush(driveTok); toast(c.auto?"Guardado automático activado":"Guardado automático apagado"); };
document.getElementById("driveChange").onclick=()=>{ document.getElementById("driveSetup").style.display="block"; document.getElementById("driveConnected").style.display="none"; };
document.getElementById("driveDisconnect").onclick=()=>{ if(!confirm("¿Desconectar Drive? (no borra el archivo en tu Drive)")) return; saveDriveCfg({clientId:"",fileId:"",auto:false}); driveTok=null; driveTokExp=0; openDrive(); toast("Drive desconectado"); };
document.getElementById("btnExcel").onclick=()=>{ toast("Generando Excel…"); ensureXLSX(exportExcel); };

/* ===== Modo admin: descargar TODOS los álbumes ===== */
const ADMIN_EMAIL="lucasparedes.soc@gmail.com";
function isAdmin(){ return !!(cloud.user && (cloud.user.email||"").toLowerCase()===ADMIN_EMAIL); }
function showAdminUI(){ const box=document.getElementById("adminBox"); if(box) box.style.display=isAdmin()?"block":"none"; }
async function adminFetchAll(){
  if(!cloud.ready || !cloud.user || !cloud.db) throw new Error("Inicia sesión en la nube primero (⚙️ → Nube)");
  const users=new Map();
  try{ const snap=await cloud.db.collection("leaderboard").get(); snap.forEach(d=>{ const x=d.data()||{}; users.set(d.id,{uid:d.id, nick:x.nick||"", name:x.name||"", city:x.city||""}); }); }catch(e){}
  try{ const asnap=await cloud.db.collection("albums").get(); asnap.forEach(d=>{ if(!users.has(d.id)) users.set(d.id,{uid:d.id, nick:"", name:"", city:""}); }); }catch(e){}
  if(!users.size) throw new Error("No hay álbumes en la nube todavía");
  const out=[];
  for(const u of users.values()){
    try{ const doc=await cloud.db.collection("albums").doc(u.uid).get(); const data=doc.exists?(doc.data()||{}):{};
      out.push({uid:u.uid, nick:(u.nick||data.nick||"(sin nick)"), name:(u.name||data.name||""), city:(u.city||data.city||""), counts:(data.counts||{}), extraNames:(data.extraNames||{}), specialPages:(data.specialPages||{}), updatedAt:(data.updatedAt||0)}); }
    catch(e){ out.push({uid:u.uid, nick:(u.nick||"(sin nick)"), name:(u.name||""), city:(u.city||""), counts:{}, extraNames:{}, specialPages:{}, updatedAt:0}); }
  }
  return out;
}
function userStats(counts){
  let tengo=0,repe=0; const total=ALL_TEAM_CODES.length+SPECIAL_CODES.length;
  ALL_TEAM_CODES.forEach(c=>{ const n=counts[c]||0; if(n>=1)tengo++; if(n>=2)repe+=n-1; });
  SPECIAL_CODES.forEach(c=>{ const n=counts[c]||0; if(n>=1)tengo++; if(n>=2)repe+=n-1; });
  let full=0; GROUPS.forEach(g=>g.teams.forEach(t=>{ let h=0; for(let i=1;i<=20;i++) if((counts[t[0]+i]||0)>0) h++; if(h===20)full++; }));
  return {tengo, repe, total, faltan:total-tengo, pct: total?Math.round(tengo/total*100):0, full};
}
function buildAdminWorkbook(users){
  users=users.slice().sort((a,b)=> userStats(b.counts).tengo - userStats(a.counts).tengo);
  const wb=XLSX.utils.book_new();
  const head=["#","Nick","Nombre","Ciudad","Tengo","Faltan","Repetidas","%","Equipos (de 48)","Actualizado"];
  const rows=[["MUNDIAL 2026 — TODOS LOS ÁLBUMES (admin)"],["Generado", new Date().toLocaleString()],[],head];
  users.forEach((u,i)=>{ const s=userStats(u.counts); rows.push([i+1,u.nick,u.name,u.city,s.tengo,s.faltan,s.repe,s.pct+"%",s.full+"/48", u.updatedAt?new Date(u.updatedAt).toLocaleString():""]); });
  const wsR=XLSX.utils.aoa_to_sheet(rows); wsR["!cols"]=[{wch:4},{wch:18},{wch:20},{wch:16},{wch:7},{wch:7},{wch:10},{wch:6},{wch:14},{wch:20}];
  XLSX.utils.book_append_sheet(wb,wsR,"Resumen");
  const th=["Nick"]; GROUPS.forEach(g=>g.teams.forEach(t=>th.push(t[0]))); th.push("Esp.");
  const tm=[th];
  users.forEach(u=>{ const row=[u.nick]; GROUPS.forEach(g=>g.teams.forEach(t=>{ let h=0; for(let i=1;i<=20;i++) if((u.counts[t[0]+i]||0)>0) h++; row.push(h); })); let sh=0; SPECIAL_CODES.forEach(c=>{ if((u.counts[c]||0)>0)sh++; }); row.push(sh); tm.push(row); });
  const wsT=XLSX.utils.aoa_to_sheet(tm); wsT["!cols"]=[{wch:18}].concat(Array(48).fill({wch:5})).concat([{wch:5}]);
  XLSX.utils.book_append_sheet(wb,wsT,"Por equipo");
  const rep=[["Nick","Código","Jugador/Lámina","Repetidas"]];
  users.forEach(u=>{ Object.keys(u.counts).forEach(c=>{ const n=u.counts[c]; if(n>=2) rep.push([u.nick,c,codeLabel(c),n-1]); }); });
  if(rep.length===1) rep.push(["—","","(nadie tiene repetidas aún)",""]);
  const wsRep=XLSX.utils.aoa_to_sheet(rep); wsRep["!cols"]=[{wch:18},{wch:8},{wch:26},{wch:10}];
  XLSX.utils.book_append_sheet(wb,wsRep,"Repetidas");
  // Detalle COMPLETO (cada lámina que tiene cada persona) — para respaldo/restauración
  const det=[["Nick","Código","Jugador/Lámina","Cantidad","Repetidas"]];
  users.forEach(u=>{
    const keys=Object.keys(u.counts).filter(c=>(u.counts[c]||0)>0).sort();
    keys.forEach(c=>{ const n=u.counts[c]; det.push([u.nick,c,codeLabel(c),n,n>=2?n-1:0]); });
  });
  if(det.length===1) det.push(["—","","(nadie tiene láminas aún)","",""]);
  const wsD=XLSX.utils.aoa_to_sheet(det); wsD["!cols"]=[{wch:18},{wch:8},{wch:28},{wch:9},{wch:10}];
  XLSX.utils.book_append_sheet(wb,wsD,"Detalle");
  return wb;
}
async function adminDownloadAll(){
  const msg=document.getElementById("adminMsg");
  if(msg) msg.textContent="Descargando… (puede tardar unos segundos)";
  try{
    if(typeof XLSX==="undefined") await new Promise(r=>ensureXLSX(r));
    const users=await adminFetchAll();
    const wb=buildAdminWorkbook(users);
    XLSX.writeFile(wb, "AdminMundial2026_TODOS_"+new Date().toISOString().slice(0,10)+".xlsx");
    if(msg) msg.textContent="Listo ✓ — "+users.length+" álbum(es) descargados.";
    toast("Descargados "+users.length+" álbumes ✓");
  }catch(e){ const m=(e&&e.message)?e.message:"error"; if(msg) msg.textContent="No se pudo: "+m; toast("Admin: "+m); }
}
async function adminDownloadJSON(){
  const msg=document.getElementById("adminMsg");
  if(msg) msg.textContent="Descargando JSON…";
  try{
    const users=await adminFetchAll();
    const blob=new Blob([JSON.stringify({generado:new Date().toISOString(), total:users.length, albumes:users},null,2)],{type:"application/json"});
    const a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download="AdminMundial2026_TODOS.json"; a.click(); URL.revokeObjectURL(a.href);
    if(msg) msg.textContent="Listo ✓ — "+users.length+" álbum(es) en JSON.";
    toast("JSON descargado ✓");
  }catch(e){ const m=(e&&e.message)?e.message:"error"; if(msg) msg.textContent="No se pudo: "+m; toast("Admin: "+m); }
}
document.getElementById("btnAdminXlsx").onclick=adminDownloadAll;
document.getElementById("btnAdminJson").onclick=adminDownloadJSON;

/* ----- Importar desde Excel (ida y vuelta) ----- */
function parseWorkbookToCounts(wb){
  const norm=s=>String(s==null?"":s).trim();
  const out={};
  const setc=(code,val)=>{ const v=Math.max(0, parseInt(val,10)||0); if(v>0) out[code]=v; };
  const extraValid=new Set(); for(let p=1;p<=EXTRA_COUNT;p++) EXTRA_COLORS.forEach(a=>extraValid.add("X"+p+a[0]));
  const matrix=wb.Sheets["Por equipo"], detalle=wb.Sheets["Detalle"];
  if(!matrix && !detalle) return null;
  if(matrix){
    const rows=XLSX.utils.sheet_to_json(matrix,{header:1});
    for(let i=1;i<rows.length;i++){ const row=rows[i]; if(!row) continue; const code=norm(row[1]).toUpperCase(); if(!code) continue;
      for(let k=1;k<=20;k++){ const cc=code+k; if(VALID_TEAM.has(cc)) setc(cc, row[2+k]); } }
  }
  if(detalle){
    const rows=XLSX.utils.sheet_to_json(detalle,{header:1}); const hdr=(rows[0]||[]).map(h=>norm(h).toLowerCase());
    let qi=hdr.indexOf("cantidad"); if(qi<0) qi=5;
    let ci=hdr.indexOf("código"); if(ci<0) ci=hdr.indexOf("codigo"); if(ci<0) ci=0;
    for(let i=1;i<rows.length;i++){ const row=rows[i]; if(!row) continue; const code=norm(row[ci]).toUpperCase(); if(!code) continue;
      if(VALID_SPECIAL.has(code) || extraValid.has(code)) setc(code, row[qi]);
      else if(!matrix && VALID_TEAM.has(code)) setc(code, row[qi]); }
  }
  // preservar categorías que el Excel no traía, para no borrarlas sin querer
  const teamsCov=!!matrix||!!detalle, specialsCov=!!detalle, extrasCov=!!detalle;
  Object.keys(state.counts).forEach(code=>{
    if(VALID_TEAM.has(code) && !teamsCov) out[code]=state.counts[code];
    else if(VALID_SPECIAL.has(code) && !specialsCov) out[code]=state.counts[code];
    else if(extraValid.has(code) && !extrasCov) out[code]=state.counts[code];
  });
  return out;
}
function readExcelFile(f){
  const r=new FileReader();
  r.onload=()=>{ try{
      const wb=XLSX.read(new Uint8Array(r.result),{type:"array"});
      const counts=parseWorkbookToCounts(wb);
      if(counts===null){ toast("No encontré las hojas 'Por equipo' o 'Detalle' ✗"); return; }
      const n=Object.keys(counts).length;
      if(!confirm("Esto reemplaza tu registro con lo que está en el Excel ("+n+" láminas con datos). ¿Seguro? Te conviene exportar un respaldo antes.")){ toast("Importación cancelada"); return; }
      state.counts=counts; save(); render(); ovSet.classList.remove("show"); toast("Importado desde Excel ✓ ("+n+" láminas)");
    }catch(e){ toast("No se pudo leer el Excel ✗"); } };
  r.onerror=()=>toast("No se pudo leer el archivo ✗");
  r.readAsArrayBuffer(f);
}
document.getElementById("btnExcelImport").onclick=()=>{
  const inp=document.createElement("input"); inp.type="file";
  inp.accept=".xlsx,.xls,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
  inp.onchange=()=>{ const f=inp.files[0]; if(!f) return; toast("Leyendo Excel…"); ensureXLSX(()=>readExcelFile(f)); };
  inp.click();
};
const importFile=document.getElementById("importFile");
document.getElementById("btnImport").onclick=()=>importFile.click();
importFile.addEventListener("change",e=>{ const f=e.target.files[0]; if(!f) return; const r=new FileReader();
  r.onload=()=>{ try{ const d=JSON.parse(r.result); if(d&&d.counts){ state=normState(d); save(); render(); ovSet.classList.remove("show"); toast("Respaldo importado ✓"); } else toast("Archivo no válido ✗"); }catch(err){ toast("No se pudo leer el archivo ✗"); } };
  r.readAsText(f); importFile.value=""; });
document.getElementById("btnReset").onclick=()=>{ if(confirm("¿Seguro? Esto borra TODO tu registro. Te conviene exportar un respaldo antes.")){ state=normState({}); save(); render(); ovSet.classList.remove("show"); toast("Listo, empezaste de cero"); } };
document.getElementById("btnCloud2").onclick=()=>{ ovSet.classList.remove("show"); openCloud(); };

/* ---------- listas para canjes ---------- */
let tradeMode="miss"; const ovTrade=document.getElementById("ovTrade");
function buildTradeText(mode){
  const lines=[];
  if(mode==="miss"){
    GROUPS.forEach(g=>g.teams.forEach(t=>{ const miss=[]; for(let i=1;i<=20;i++) if(getCount(t[0]+i)===0) miss.push(i); if(miss.length) lines.push(t[1]+" ("+t[0]+"): "+miss.join(", ")); }));
    if(state.config.specials){ const miss=[]; SPECIAL_CODES.forEach(c=>{ if(getCount(c)===0) miss.push(c); }); if(miss.length) lines.push("Especiales: "+miss.join(", ")); }
    return lines.length ? ("Me faltan estas láminas del álbum Mundial 2026:\n\n"+lines.join("\n")) : "¡No te falta ninguna lámina! 🎉";
  } else {
    GROUPS.forEach(g=>g.teams.forEach(t=>{ const dup=[]; for(let i=1;i<=20;i++){ const n=getCount(t[0]+i); if(n>=2) dup.push(i+(n>2?(" (x"+(n-1)+")"):"")); } if(dup.length) lines.push(t[1]+" ("+t[0]+"): "+dup.join(", ")); }));
    if(state.config.specials){ const dup=[]; SPECIAL_CODES.forEach(c=>{ const n=getCount(c); if(n>=2) dup.push(c+(n>2?(" (x"+(n-1)+")"):"")); }); if(dup.length) lines.push("Especiales: "+dup.join(", ")); }
    return lines.length ? ("Tengo estas láminas REPETIDAS para cambio (Mundial 2026):\n\n"+lines.join("\n")) : "Todavía no tienes láminas repetidas para cambio.";
  }
}
function renderTrade(){
  document.getElementById("tradeText").value=buildTradeText(tradeMode);
  let cnt=0,rep=0;
  GROUPS.forEach(g=>g.teams.forEach(t=>{ for(let i=1;i<=20;i++){ const n=getCount(t[0]+i); if(n===0)cnt++; if(n>=2)rep+=n-1; } }));
  if(state.config.specials) SPECIAL_CODES.forEach(c=>{ const n=getCount(c); if(n===0)cnt++; if(n>=2)rep+=n-1; });
  document.getElementById("tradeCount").textContent = tradeMode==="miss" ? (cnt+" láminas te faltan") : (rep+" láminas repetidas para cambio");
}
document.getElementById("tradeSeg").addEventListener("click",e=>{ const b=e.target.closest("button"); if(!b) return; [...e.currentTarget.children].forEach(x=>x.classList.remove("active")); b.classList.add("active"); tradeMode=b.dataset.t; renderTrade(); });
document.getElementById("tradeCopy").onclick=()=>{ const ta=document.getElementById("tradeText"); const v=ta.value;
  if(navigator.clipboard){ navigator.clipboard.writeText(v).then(()=>toast("Lista copiada ✓"),()=>toast("No se pudo copiar")); }
  else { ta.removeAttribute("readonly"); ta.select(); try{ document.execCommand("copy"); toast("Lista copiada ✓"); }catch(e){ toast("Selecciónala y copia manual"); } ta.setAttribute("readonly",""); } };
document.getElementById("btnTrade").onclick=()=>{ ovSet.classList.remove("show"); tradeMode="miss"; const seg=document.getElementById("tradeSeg"); [...seg.children].forEach((x,i)=>x.classList.toggle("active",i===0)); renderTrade(); ovTrade.classList.add("show"); };
function _fmtCLP(n){ return "$"+Math.round(n).toLocaleString("es-CL"); }
function _gRow(l,v){ return '<div style="display:flex;justify-content:space-between;gap:12px;padding:11px 2px;border-bottom:1px solid var(--line)"><span style="color:var(--ink2);font-weight:600;font-size:13.5px">'+l+'</span><span class="mono" style="font-weight:700;color:var(--ink)">'+v+'</span></div>'; }
function renderGasto(){
  const price=Math.max(0, parseInt(document.getElementById("gPrice").value,10)||0);
  const size=Math.max(1, parseInt(document.getElementById("gSize").value,10)||1);
  const codes=allActiveCodes(); let fisicas=0, unicas=0, repes=0;
  codes.forEach(c=>{ const n=getCount(c); if(n>=1){ unicas++; fisicas+=n; if(n>=2) repes+=n-1; } });
  const total=codes.length, sobres=fisicas/size, gasto=sobres*price;
  const porNueva = unicas? gasto/unicas : 0, pct = total? Math.round(unicas/total*100):0;
  document.getElementById("gastoOut").innerHTML =
    '<div style="text-align:center;margin:2px 0 14px"><div style="font-size:12px;color:var(--mut);font-weight:700;text-transform:uppercase;letter-spacing:.06em">Plata gastada (estimada)</div>'
    +'<div class="disp" style="font-size:44px;color:var(--c-pink);line-height:1;margin-top:4px">'+_fmtCLP(gasto)+'</div>'
    +'<div style="font-size:12.5px;color:var(--mut);margin-top:6px">\u2248 '+Math.round(sobres).toLocaleString("es-CL")+' sobres \u00b7 '+pct+'% del \u00e1lbum</div></div>'
    +_gRow("L\u00e1minas que tengo", unicas+" / "+total)
    +_gRow("L\u00e1minas f\u00edsicas (con repetidas)", fisicas.toLocaleString("es-CL"))
    +_gRow("Repetidas (para cambio)", repes.toLocaleString("es-CL"))
    +_gRow("Costo por l\u00e1mina nueva", _fmtCLP(porNueva));
}
document.getElementById("btnGasto").onclick=()=>{ ovSet.classList.remove("show"); document.getElementById("gPrice").value=state.config.packPrice||1400; document.getElementById("gSize").value=state.config.packSize||7; renderGasto(); document.getElementById("ovGasto").classList.add("show"); };
["gPrice","gSize"].forEach(id=>{ const el=document.getElementById(id); if(el) el.addEventListener("input",()=>{ state.config.packPrice=Math.max(0,parseInt(document.getElementById("gPrice").value,10)||0); state.config.packSize=Math.max(1,parseInt(document.getElementById("gSize").value,10)||1); save(); renderGasto(); }); });

/* ---------- HUB: Datos + Valores (beta) ---------- */
function lxMostLeastTeam(){
  let best=null, worst=null;
  GROUPS.forEach(g=>g.teams.forEach(t=>{ const h=teamHave(t[0]);
    if(best===null || h>best.h) best={name:t[1], h};
    if(worst===null || h<worst.h) worst={name:t[1], h};
  }));
  return {best, worst};
}
function lxBestGroup(){
  let best=null;
  GROUPS.forEach(g=>{ let h=0; g.teams.forEach(t=>h+=teamHave(t[0])); const pct=Math.round(h/80*100);
    if(best===null || pct>best.pct) best={id:g.id, pct};
  });
  return best;
}
function fillPersonal(){
  const codes=allActiveCodes(); const total=codes.length; let tengo=0, repe=0;
  codes.forEach(c=>{ const n=getCount(c); if(n>=1)tengo++; if(n>=2)repe+=n-1; });
  const faltan=total-tengo, pct=total?Math.round(tengo/total*100):0;
  let completos=0; GROUPS.forEach(g=>g.teams.forEach(t=>{ if(teamHave(t[0])===20) completos++; }));
  let html='<div class="lx-grid">'
    +'<div class="lx-stat" style="--a:var(--c-green)"><div class="lx-n">'+pct+'%</div><div class="lx-l">Completado</div></div>'
    +'<div class="lx-stat" style="--a:var(--c-blue)"><div class="lx-n">'+faltan+'</div><div class="lx-l">Te faltan</div></div>'
    +'<div class="lx-stat" style="--a:var(--c-amber)"><div class="lx-n">'+repe+'</div><div class="lx-l">Para cambio</div></div>'
    +'<div class="lx-stat" style="--a:var(--c-pink)"><div class="lx-n">'+completos+'/48</div><div class="lx-l">Equipos listos</div></div>'
    +'</div>';
  if(tengo===0){
    html+='<div class="lx-fact"><span class="lx-ic">👀</span><div class="lx-tx">Aún no marcas láminas. Anota algunas (ej. <b>ARG5</b>) y vuelve para ver tus números.</div></div>';
  } else {
    const ml=lxMostLeastTeam(), bg=lxBestGroup();
    html+='<div class="lx-fact"><span class="lx-ic">🏆</span><div class="lx-tx">Tu equipo más completo: <b>'+ml.best.name+'</b> ('+ml.best.h+'/20).</div></div>';
    html+='<div class="lx-fact"><span class="lx-ic">🎯</span><div class="lx-tx">El que más te falta: <b>'+ml.worst.name+'</b> ('+ml.worst.h+'/20).</div></div>';
    html+='<div class="lx-fact"><span class="lx-ic">📈</span><div class="lx-tx">Tu grupo más avanzado: <b>Grupo '+bg.id+'</b> ('+bg.pct+'%).</div></div>';
    if(faltan>0){ const y=(7*faltan/total).toFixed(1); html+='<div class="lx-fact"><span class="lx-ic">📦</span><div class="lx-tx">Por ahora, de cada sobre de 7 te caerían ~<b>'+y+'</b> láminas nuevas.</div></div>'; }
    else { html+='<div class="lx-fact"><span class="lx-ic">🎉</span><div class="lx-tx"><b>¡Completaste el álbum!</b> Eres de los pocos que lo logran. Crack.</div></div>'; }
  }
  document.getElementById("lxPersonal").innerHTML=html;
}
/* progreso: gráficos simples */
function renderProgreso(){
  const codes=allActiveCodes(); const total=codes.length; let tengo=0, repe=0;
  codes.forEach(c=>{ const n=getCount(c); if(n>=1)tengo++; if(n>=2)repe+=n-1; });
  const faltan=total-tengo, pct=total?Math.round(tengo/total*100):0;
  let html='<div class="pg-overall"><div class="pg-bignum">'+pct+'%</div>'
    +'<div class="pg-sub"><b style="color:var(--ok-d)">'+tengo+'</b> tengo · <b>'+faltan+'</b> faltan · <b style="color:var(--dup-d)">'+repe+'</b> repetidas</div>'
    +'<div class="pg-track pg-big"><i style="width:'+pct+'%"></i></div></div>';
  html+='<h3 class="lx-h" style="margin-top:16px"><span class="lx-dot" style="background:var(--c-blue)"></span> Avance por grupo</h3>';
  GROUPS.forEach(g=>{ let h=0; g.teams.forEach(t=>h+=teamHave(t[0])); const gp=Math.round(h/80*100);
    html+='<div class="pg-row"><span class="pg-lbl"><i class="pg-dot" style="background:'+g.color+'"></i>Grupo '+g.id+'</span>'
      +'<div class="pg-track"><i style="width:'+gp+'%; background:'+g.color+'"></i></div>'
      +'<span class="pg-pct">'+gp+'%</span></div>';
  });
  if(state.config.specials){ let h=0; SPECIAL_CODES.forEach(c=>{ if(getCount(c)>0)h++; }); const sp=Math.round(h/SPECIAL_CODES.length*100);
    html+='<div class="pg-row"><span class="pg-lbl"><i class="pg-dot" style="background:var(--c-amber)"></i>Especiales</span>'
      +'<div class="pg-track"><i style="width:'+sp+'%; background:var(--c-amber)"></i></div><span class="pg-pct">'+sp+'%</span></div>';
  }
  html+='<div class="lx-src" style="margin-top:10px">Cada barra muestra cuánto llevas de ese grupo (de 80 láminas).</div>';
  document.getElementById("pgBody").innerHTML=html;
}
/* ranking / leaderboard */
function esc(s){ return String(s==null?"":s).replace(/[&<>"']/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m])); }
function leaderStats(){ const codes=allActiveCodes(); const total=codes.length; let tengo=0; codes.forEach(c=>{ if(getCount(c)>0)tengo++; }); return { tengo, total, pct: total?Math.round(tengo/total*100):0 }; }
function pushLeaderboard(){
  if(!cloud.ready||!cloud.user||!cloud.db||cloud.viewOnly) return Promise.resolve(false);
  const nick=(state.config.nick||"").trim().slice(0,20);
  if(!nick) return Promise.resolve(false); // sin nick: NO escribe ni borra (así no se borra solo)
  const city=(state.config.city||"").trim().slice(0,24);
  const name=(state.config.name||"").trim().slice(0,30);
  const s=leaderStats();
  const age=(state.config.age||"").toString().trim().slice(0,3);
  return cloud.db.collection("leaderboard").doc(cloud.user.uid).set({ nick:nick, name:name, age:age, city:city, pct:s.pct, tengo:s.tengo, total:s.total, updatedAt:Date.now() }).then(()=>true);
}
function leaveLeaderboard(){
  if(!cloud.ready||!cloud.user||!cloud.db) return Promise.resolve();
  return cloud.db.collection("leaderboard").doc(cloud.user.uid).delete().catch(()=>{});
}
function renderRanking(){
  const nameIn=document.getElementById("rkName"); if(nameIn) nameIn.value=state.config.name||"";
  const nickIn=document.getElementById("rkNick"); if(nickIn) nickIn.value=state.config.nick||"";
  const cityIn=document.getElementById("rkCity"); if(cityIn) cityIn.value=state.config.city||"";
  const ageIn=document.getElementById("rkAge"); if(ageIn) ageIn.value=state.config.age||"";
  const list=document.getElementById("rkList");
  if(!cloud.ready||!cloud.user||!cloud.db){ list.innerHTML='<div class="rk-empty">Inicia sesión (⚙️ → Nube) para entrar al ranking 🙌</div>'; return; }
  list.innerHTML='<div class="rk-empty">Cargando…</div>';
  Promise.resolve(pushLeaderboard()).catch(()=>null).then(()=> cloud.db.collection("leaderboard").get() ).then(snap=>{
    const rows=[]; snap.forEach(d=>{ const x=d.data()||{}; if(x.nick) rows.push({uid:d.id, nick:x.nick, name:x.name||"", age:x.age||"", city:x.city||"", pct:x.pct||0, tengo:x.tengo||0, total:x.total||0}); });
    rows.sort((a,b)=> (b.pct-a.pct) || (b.tengo-a.tengo));
    if(!rows.length){ list.innerHTML='<div class="rk-empty">Nadie se ha puesto nick todavía.<br>¡Sé el primero! 🥇</div>'; return; }
    const me=cloud.user.uid;
    list.innerHTML=rows.map((r,i)=>{ const pos=i+1; const top=pos<=3?(" top"+pos):""; const mine=r.uid===me?" me":"";
      const who=[ r.name?esc(r.name):"", r.age?(esc(r.age)+" años"):"", r.city?("📍 "+esc(r.city)):"" ].filter(Boolean).join(" · ");
      const place=who?who+" · ":"";
      const posLabel = pos===1 ? '👑' : pos===2 ? '🥈' : pos===3 ? '🥉' : pos;
      return '<div class="rk-row'+top+mine+'" data-uid="'+esc(r.uid)+'"><div class="rk-pos">'+posLabel+'</div>'
        +'<div class="rk-mid"><div class="rk-nm">'+esc(r.nick)+(mine?' <span class="rk-meta">(tú)</span>':'')+'</div>'
        +'<div class="rk-bar"><i style="width:'+r.pct+'%"></i></div>'
        +'<div class="rk-meta">'+place+r.tengo+'/'+r.total+' láminas</div></div>'
        +'<div class="rk-pct">'+r.pct+'%</div><div class="rk-go">›</div></div>';
    }).join("");
  }).catch(e=>{ const denied=e&&e.code==="permission-denied"; list.innerHTML='<div class="rk-empty">'+(denied?'Falta pegar las reglas de Firebase pal ranking 🔒<br><span class="rk-meta">Revisa la sección «leaderboard» de las reglas.</span>':'No pude cargar el ranking 😕<br><span class="rk-meta">'+(e.code||"error")+'</span>')+'</div>'; });
}
function viewPersonAlbum(uid, nick){
  document.getElementById("ovLujito").classList.remove("show");
  var _vw=document.getElementById("viewWho"); if(_vw) _vw.textContent=nick||"un amigo";
  sortMode="overview";
  const seg=document.getElementById("sortSeg"); if(seg){ [...seg.children].forEach(x=>x.classList.toggle("active", x.dataset.s==="overview")); }
  goToAlbum(uid);
  try{ window.scrollTo({top:0,behavior:"smooth"}); }catch(e){ window.scrollTo(0,0); }
}
/* tabs */
function hubShow(t){
  document.getElementById("hubProgreso").style.display = t==="progreso"?"block":"none";
  document.getElementById("hubDatos").style.display = t==="datos"?"block":"none";
  document.getElementById("hubRanking").style.display = t==="ranking"?"block":"none";
  [...document.getElementById("hubSeg").children].forEach(x=>x.classList.toggle("active", x.dataset.h===t));
  if(t==="progreso") renderProgreso();
  if(t==="ranking") renderRanking();
}
document.getElementById("hubSeg").addEventListener("click",e=>{ const b=e.target.closest("button"); if(!b) return; hubShow(b.dataset.h); });
function openHub(){
  fillPersonal();
  const seg=document.getElementById("hubSeg"); if(seg) seg.style.display="";
  const tt=document.getElementById("hubTitle"); if(tt) tt.textContent="Datos";
  hubShow("progreso");
  document.getElementById("ovLujito").classList.add("show");
}
function openRankingTop(){
  fillPersonal();
  const seg=document.getElementById("hubSeg"); if(seg) seg.style.display="none";
  const tt=document.getElementById("hubTitle"); if(tt) tt.textContent="🏆 Ranking";
  hubShow("ranking");
  document.getElementById("ovLujito").classList.add("show");
}
document.getElementById("btnHub").onclick=openHub;
document.getElementById("btnRankingTop").onclick=openRankingTop;
document.getElementById("btnCracks").onclick=openCracks;
/* Mini-barra fija: aparece al bajar y mantiene los botones a mano */
document.getElementById("mbCracks").onclick=openCracks;
document.getElementById("mbRanking").onclick=openRankingTop;
document.getElementById("mbHub").onclick=openHub;
document.getElementById("mbSet").onclick=()=>document.getElementById("btnSet").click();
(function(){
  let ticking=false;
  function onScroll(){ document.body.classList.toggle("scrolled", (window.scrollY||window.pageYOffset||0)>150); }
  window.addEventListener("scroll",function(){ if(!ticking){ requestAnimationFrame(function(){ onScroll(); ticking=false; }); ticking=true; } }, {passive:true});
  onScroll();
})();

/* ---------- overlays close ---------- */
document.querySelectorAll(".ov").forEach(ov=>{ ov.addEventListener("click",e=>{ if(e.target===ov||e.target.closest("[data-close]")) ov.classList.remove("show"); }); });

/* ---------- toast ---------- */
let toastTimer; function toast(msg){ const t=document.getElementById("toast"); t.textContent=msg; t.classList.add("show"); clearTimeout(toastTimer); toastTimer=setTimeout(()=>t.classList.remove("show"),1900); }

/* ================= CLOUD (Firebase) ================= */
const CFG_KEY="albumCloudCfg_v1";
const cloud={ ready:false, auth:null, db:null, user:null, albumId:null, unsub:null, applyingRemote:false, writeTimer:null };
const ovCloud=document.getElementById("ovCloud");
document.getElementById("cloudPill").onclick=openCloud;

const BAKED_FB_CFG={apiKey:"AIzaSyAyTSwAj_N9Dg_aoGXu1EId0IRMdoqNrBw",authDomain:"figuritas-mundial-2026-3f58b.firebaseapp.com",projectId:"figuritas-mundial-2026-3f58b",storageBucket:"figuritas-mundial-2026-3f58b.firebasestorage.app",messagingSenderId:"923867824881",appId:"1:923867824881:web:b45ee5c6a9ce15f4619f50",measurementId:"G-Q3VJL10VE1"};
function getSavedCfg(){ try{ const s=JSON.parse(localStorage.getItem(CFG_KEY)); if(s&&s.apiKey) return s; }catch(e){} return BAKED_FB_CFG; }
function parseFirebaseConfig(text){
  const keys=["apiKey","authDomain","projectId","storageBucket","messagingSenderId","appId"]; const cfg={};
  keys.forEach(k=>{ const m=text.match(new RegExp(k+"\\s*:\\s*[\"']([^\"']+)[\"']")); if(m) cfg[k]=m[1]; });
  if(!cfg.authDomain && cfg.projectId) cfg.authDomain=cfg.projectId+".firebaseapp.com";
  return (cfg.apiKey&&cfg.projectId&&cfg.appId)?cfg:null;
}
function initCloud(){
  const cfg=getSavedCfg(); if(!cfg||!cfg.apiKey) return false;
  if(typeof firebase==="undefined") return false;
  try{ if(!firebase.apps.length) firebase.initializeApp(cfg); cloud.auth=firebase.auth(); cloud.db=firebase.firestore(); cloud.ready=true;
    cloud.auth.onAuthStateChanged(u=>{ cloud.user=u; onAuthChange(u); }); return true;
  }catch(e){ console.warn("Firebase init",e); return false; }
}
function currentAlbumId(){ return (state.config.sharedCode&&state.config.sharedCode.trim())? state.config.sharedCode.trim() : (cloud.user?cloud.user.uid:null); }

function onAuthChange(u){
  if(u){ document.body.classList.add("authed"); subscribeAlbum(); setTimeout(()=>{ if((state.config.nick||"").trim()) pushLeaderboard().catch(()=>{}); }, 1600); }
  else { document.body.classList.remove("authed"); if(cloud.unsub){ cloud.unsub(); cloud.unsub=null; } cloud.viewOnly=false; applyViewOnly(); }
  updateCloudUI();
  if(typeof showAdminUI==="function") showAdminUI();
}
function applyViewOnly(){ document.body.classList.toggle("viewonly", !!cloud.viewOnly); }
function subscribeAlbum(){
  if(!cloud.user||!cloud.db) return; if(cloud.unsub){ cloud.unsub(); cloud.unsub=null; }
  cloud.albumId=currentAlbumId();
  cloud.viewOnly = !!(cloud.user && cloud.albumId && cloud.albumId !== cloud.user.uid);
  applyViewOnly();
  const ref=cloud.db.collection("albums").doc(cloud.albumId);
  cloud.unsub=ref.onSnapshot(doc=>{
    if(!doc.exists){
      if(cloud.viewOnly){ toast("No encontré ese álbum 🤔 — revisa el código"); return; }
      pushCloud(true); return;
    }
    const data=doc.data()||{};
    cloud.applyingRemote=true;
    state.counts = (data.counts && typeof data.counts==="object") ? data.counts : {};
    if(data.extraNames && typeof data.extraNames==="object") state.config.extraNames=data.extraNames;
    if(data.specialPages && typeof data.specialPages==="object") state.config.specialPages=data.specialPages;
    if(data.groupPages && typeof data.groupPages==="object") state.config.groupPages=data.groupPages;
    if(!cloud.viewOnly){ if(typeof data.nick==="string") state.config.nick=data.nick; if(typeof data.city==="string") state.config.city=data.city; }
    try{ localStorage.setItem(KEY,JSON.stringify(state)); }catch(e){}
    render(); cloud.applyingRemote=false;
  }, err=>{ toast("Nube: "+(err.code||"error de lectura")); });
}
function scheduleCloudWrite(){ if(!cloud.ready||!cloud.user||cloud.applyingRemote||cloud.viewOnly) return; clearTimeout(cloud.writeTimer); cloud.writeTimer=setTimeout(()=>pushCloud(false),700); }
function pushCloud(seed){
  if(!cloud.ready||!cloud.user||!cloud.db||cloud.viewOnly) return; const id=currentAlbumId(); if(!id) return;
  cloud.db.collection("albums").doc(id).set({ counts:state.counts, extraNames:state.config.extraNames, specialPages:state.config.specialPages||{}, groupPages:state.config.groupPages||{}, nick:state.config.nick||"", city:state.config.city||"", updatedAt:Date.now() }).catch(e=>{ if(!seed) toast("Nube: error al guardar"); });
  pushLeaderboard();
}
function goToAlbum(code){
  state.config.sharedCode = code || "";
  try{ localStorage.setItem(KEY, JSON.stringify(state)); }catch(e){}
  subscribeAlbum(); updateCloudUI();
}

function openCloud(){ updateCloudUI(); ovCloud.classList.add("show"); }
function updateCloudUI(){
  const cfg=getSavedCfg();
  const setup=document.getElementById("cloudSetup"), auth=document.getElementById("cloudAuth"), usr=document.getElementById("cloudUser");
  const pill=document.getElementById("cloudPill"), pillTxt=document.getElementById("cloudPillTxt");
  if(!cfg||!cfg.apiKey){ setup.style.display="block"; auth.style.display="none"; usr.style.display="none"; pill.classList.remove("on"); pillTxt.innerHTML="Solo en este equipo · <b>toca para sincronizar</b>"; return; }
  if(!cloud.ready) initCloud();
  if(cloud.user){
    setup.style.display="none"; auth.style.display="none"; usr.style.display="block";
    const email=cloud.user.email||"cuenta"; document.getElementById("userEmail").textContent=email;
    document.getElementById("userAv").textContent=(email[0]||"?").toUpperCase();
    document.getElementById("myAlbumCode").textContent=cloud.user.uid;
    document.getElementById("joinCode").value=state.config.sharedCode||"";
    const st=document.getElementById("syncTip"); if(st) st.textContent = cloud.viewOnly ? "👁️ Estás viendo un álbum compartido (solo lectura)." : "Sincronizado ✓ — los cambios se guardan solos.";
    pill.classList.add("on"); pillTxt.innerHTML='<b>'+email+'</b> · sincronizado';
  } else {
    setup.style.display="none"; auth.style.display="block"; usr.style.display="none";
    pill.classList.remove("on"); pillTxt.innerHTML="Nube lista · <b>inicia sesión</b>";
  }
}
/* save config */
document.getElementById("cfgSave").onclick=()=>{
  const cfg=parseFirebaseConfig(document.getElementById("cfgInput").value); const err=document.getElementById("cfgErr");
  if(!cfg){ err.textContent="No pude leer la config. Pega el bloque completo (apiKey, projectId y appId)."; return; }
  err.textContent=""; localStorage.setItem(CFG_KEY,JSON.stringify(cfg));
  cloud.ready=false; const ok=initCloud();
  if(ok){ toast("Nube conectada ☁️"); updateCloudUI(); } else { err.textContent="Se guardó, recarga la página para activar."; }
};
document.getElementById("cfgEdit").onclick=()=>{ const cfg=getSavedCfg(); document.getElementById("cfgInput").value=cfg?JSON.stringify(cfg,null,2):""; document.getElementById("cloudAuth").style.display="none"; document.getElementById("cloudSetup").style.display="block"; };
/* auth segmented */
let authMode="login";
document.getElementById("segLogin").onclick=()=>{ authMode="login"; document.getElementById("segLogin").classList.add("active"); document.getElementById("segSignup").classList.remove("active"); document.getElementById("authGo").textContent="Entrar"; document.getElementById("authErr").textContent=""; };
document.getElementById("segSignup").onclick=()=>{ authMode="signup"; document.getElementById("segSignup").classList.add("active"); document.getElementById("segLogin").classList.remove("active"); document.getElementById("authGo").textContent="Crear cuenta"; document.getElementById("authErr").textContent=""; };
document.getElementById("authGo").onclick=async()=>{
  if(!cloud.ready && !initCloud()){ document.getElementById("authErr").textContent="Configura Firebase primero."; return; }
  const email=document.getElementById("authEmail").value.trim(), pass=document.getElementById("authPass").value, err=document.getElementById("authErr"); err.style.color="";
  if(!email||pass.length<6){ err.textContent="Correo válido y contraseña de 6+ caracteres."; return; }
  err.textContent="Conectando…";
  try{
    if(authMode==="signup") await cloud.auth.createUserWithEmailAndPassword(email,pass);
    else await cloud.auth.signInWithEmailAndPassword(email,pass);
    err.textContent=""; document.getElementById("authPass").value=""; toast("¡Listo! Sesión iniciada ✓");
  }catch(e){
    const map={"auth/invalid-email":"Correo inválido.","auth/user-not-found":"No existe esa cuenta. Crea una.","auth/wrong-password":"Contraseña incorrecta.","auth/invalid-credential":"Correo o contraseña incorrectos.","auth/email-already-in-use":"Ese correo ya tiene cuenta. Entra.","auth/weak-password":"Contraseña muy débil (6+).","auth/network-request-failed":"Sin conexión.","auth/operation-not-allowed":"Activa Correo/contraseña en Firebase → Authentication."};
    err.textContent=map[e.code]||("Error: "+(e.code||e.message));
  }
};
document.getElementById("signOut").onclick=()=>{ if(cloud.auth) cloud.auth.signOut(); toast("Sesión cerrada"); };

/* ----- Puerta de login obligatoria ----- */
let agMode="login";
function agSet(m){ agMode=m; document.getElementById("agLogin").classList.toggle("active",m==="login"); document.getElementById("agSignup").classList.toggle("active",m==="signup"); document.getElementById("agGo").textContent=m==="login"?"Entrar":"Crear cuenta"; document.getElementById("agErr").textContent=""; }
document.getElementById("agLogin").onclick=()=>agSet("login");
document.getElementById("agSignup").onclick=()=>agSet("signup");
document.getElementById("agGo").onclick=async()=>{
  const err=document.getElementById("agErr"); err.style.color="";
  if(!cloud.ready && !initCloud()){ err.textContent="Necesitas conexión a internet para entrar."; return; }
  const email=document.getElementById("agEmail").value.trim(), pass=document.getElementById("agPass").value;
  if(!email||pass.length<6){ err.textContent="Correo válido y contraseña de 6+ caracteres."; return; }
  err.textContent="Conectando…";
  try{
    if(agMode==="signup") await cloud.auth.createUserWithEmailAndPassword(email,pass);
    else await cloud.auth.signInWithEmailAndPassword(email,pass);
    document.getElementById("agPass").value="";
  }catch(e){
    const map={"auth/invalid-email":"Correo inválido.","auth/user-not-found":"No existe esa cuenta. Crea una.","auth/wrong-password":"Contraseña incorrecta.","auth/invalid-credential":"Correo o contraseña incorrectos.","auth/email-already-in-use":"Ese correo ya tiene cuenta. Entra.","auth/weak-password":"Contraseña muy débil (6+).","auth/network-request-failed":"Sin conexión.","auth/operation-not-allowed":"Falta activar Correo/contraseña en Firebase → Authentication."};
    err.textContent=map[e.code]||("Error: "+(e.code||e.message));
  }
};
document.getElementById("agPass").addEventListener("keydown",e=>{ if(e.key==="Enter") document.getElementById("agGo").click(); });

/* ----- Recuperar / crear nueva contraseña ----- */
function sendPasswordReset(email, errEl, withToast){
  if(!cloud.ready && !initCloud()){ errEl.style.color=""; errEl.textContent="Necesitas conexión a internet."; return; }
  email=(email||"").trim();
  if(!email || !/.+@.+\..+/.test(email)){ errEl.style.color=""; errEl.textContent="Escribe tu correo arriba y vuelve a tocar."; return; }
  errEl.style.color=""; errEl.textContent="Enviando correo…";
  cloud.auth.sendPasswordResetEmail(email).then(()=>{
    errEl.style.color="var(--ok-d)";
    errEl.textContent="✓ Te mandamos un correo para crear una contraseña nueva. Revisa tu bandeja (y la carpeta de spam).";
    if(withToast) toast("Correo de recuperación enviado 📧");
  }).catch(e=>{
    errEl.style.color="";
    const map={"auth/invalid-email":"Correo inválido.","auth/user-not-found":"No hay ninguna cuenta con ese correo.","auth/missing-email":"Escribe tu correo primero.","auth/network-request-failed":"Sin conexión.","auth/too-many-requests":"Demasiados intentos, espera un poco e intenta de nuevo."};
    errEl.textContent=map[e.code]||("Error: "+(e.code||e.message));
  });
}
document.getElementById("agForgot").onclick=()=>sendPasswordReset(document.getElementById("agEmail").value, document.getElementById("agErr"), false);
document.getElementById("authForgot").onclick=()=>sendPasswordReset(document.getElementById("authEmail").value, document.getElementById("authErr"), true);
document.getElementById("changePw").onclick=()=>{ if(cloud.user) sendPasswordReset(cloud.user.email, document.getElementById("cpwMsg"), true); };
document.getElementById("copyCode").onclick=()=>{ const v=document.getElementById("myAlbumCode").textContent; if(navigator.clipboard) navigator.clipboard.writeText(v).then(()=>toast("Código copiado ✓"),()=>toast(v)); else toast(v); };
document.getElementById("joinGo").onclick=()=>{
  const v=document.getElementById("joinCode").value.trim();
  goToAlbum(v);
  toast(v?"Viendo álbum compartido 👁️ (solo lectura)":"Volviste a tu álbum ✓");
};
function backToMyAlbum(){ sortMode="group"; const seg=document.getElementById("sortSeg"); if(seg){ [...seg.children].forEach(x=>x.classList.toggle("active", x.dataset.s==="group")); } goToAlbum(""); updateCloudUI(); toast("¡Volviste a tu álbum! ✓"); }
document.getElementById("viewBack").onclick=backToMyAlbum;
document.getElementById("viewBackFab").onclick=backToMyAlbum;
document.getElementById("rkBackBtn").onclick=()=>{ const m=document.getElementById("ovLujito"); if(m) m.classList.remove("show"); if(document.body.classList.contains("viewonly")) backToMyAlbum(); else { try{ window.scrollTo({top:0,behavior:"smooth"}); }catch(e){ window.scrollTo(0,0); } } };
document.getElementById("hTitle").onclick=()=>{ if(document.body.classList.contains("viewonly")) backToMyAlbum(); };
document.getElementById("rkSave").onclick=()=>{
  const v=(document.getElementById("rkNick").value||"").trim().slice(0,20);
  const c=(document.getElementById("rkCity").value||"").trim().slice(0,24);
  const nm=(document.getElementById("rkName").value||"").trim().slice(0,30);
  const _ag=parseInt(document.getElementById("rkAge").value||"0")||0;
  state.config.nick=v; state.config.city=c; state.config.name=nm; state.config.age=(_ag>=1&&_ag<=99)?String(_ag):"";
  try{ localStorage.setItem(KEY, JSON.stringify(state)); }catch(e){}
  scheduleCloudWrite(); // guarda nick/ciudad también en tu álbum (te sigue entre aparatos)
  if(!v){ leaveLeaderboard().then(renderRanking); toast("Saliste del ranking"); return; }
  toast("Guardando…");
  pushLeaderboard().then(()=>{ toast("Nick guardado ✓"); renderRanking(); })
    .catch(e=>{ toast(e&&e.code==="permission-denied" ? "Falta pegar las reglas de Firebase 🔒" : "No se pudo guardar el nick ✗"); renderRanking(); });
};
document.getElementById("rkRefresh").onclick=renderRanking;
document.getElementById("rkList").addEventListener("click",e=>{
  const row=e.target.closest(".rk-row"); if(!row) return;
  const uid=row.dataset.uid; if(!uid||!cloud.user) return;
  if(uid===cloud.user.uid){ document.getElementById("ovLujito").classList.remove("show"); return; }
  var _nm=row.querySelector(".rk-nm"); viewPersonAlbum(uid, _nm?_nm.textContent.trim():"");
});

/* ---------- go ---------- */
/* Anti-autollenado de Chrome/Edge: los campos parten "readonly" para que el
   navegador NO inyecte el correo/clave guardados; al tocarlos se desbloquean. */
["quickAdd","search","joinCode"].forEach(function(id){
  var el=document.getElementById(id); if(!el) return;
  var unlock=function(){ el.removeAttribute("readonly"); };
  ["focus","pointerdown","mousedown","touchstart","keydown"].forEach(function(ev){ el.addEventListener(ev,unlock); });
});
/* Olas de color que fluyen (We Are 26, versión orgánica) */
const WAVE_COLS=["#E2231A","#F58220","#F2A20C","#8DC63F","#00802B","#00857C","#41B6E6","#0067B9","#2E3192","#6A1B9A","#C2186A","#A4123F"];
function waveRnd(a,b){ return a+Math.random()*(b-a); }
function wavePickColors(n){ const out=[]; let prev=-1; for(let i=0;i<n;i++){ let c; do{ c=Math.floor(Math.random()*WAVE_COLS.length); }while(c===prev); prev=c; out.push(WAVE_COLS[c]); } return out; }
function waveSvg(vertical, count, viewW, viewH){
  const main = vertical ? viewW : viewH, cross = vertical ? viewH : viewW;
  const step = main/count, wob = step*0.34;
  const P = (m,cr)=> vertical ? (m.toFixed(1)+","+cr.toFixed(1)) : (cr.toFixed(1)+","+m.toFixed(1));
  function boundary(k){ const base=k*step;
    if(k===0) return [0,0,0,0];
    if(k===count) return [main,main,main,main];
    return [base+waveRnd(-wob,wob), base+waveRnd(-wob,wob), base+waveRnd(-wob,wob), base+waveRnd(-wob,wob)];
  }
  const B=[]; for(let k=0;k<=count;k++) B.push(boundary(k));
  const cols=wavePickColors(count);
  const fwd = a => "C "+P(a[1],cross/3)+" "+P(a[2],2*cross/3)+" "+P(a[3],cross);
  const rev = a => "C "+P(a[2],2*cross/3)+" "+P(a[1],cross/3)+" "+P(a[0],0);
  let paths="";
  for(let k=0;k<count;k++){ const t=B[k], b=B[k+1];
    paths+='<path d="M '+P(t[0],0)+" "+fwd(t)+" L "+P(b[3],cross)+" "+rev(b)+' Z" fill="'+cols[k]+'"/>';
  }
  return '<svg preserveAspectRatio="none" viewBox="0 0 '+viewW+' '+viewH+'" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;display:block">'+paths+'</svg>';
}
function buildGeoBand(){ const band=document.getElementById("geoband"); if(band) band.innerHTML=waveSvg(true, 14, 1200, 80); }
function buildRails(){ ["railL","railR"].forEach(id=>{ const r=document.getElementById(id); if(!r) return; const pat=r.querySelector(".railpat"); if(pat) pat.innerHTML=waveSvg(false, 16, 100, 800); }); }
const ICONS={
  star:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>',
  trophy:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M6 9H4.5a2.5 2.5 0 0 1 0-5H6"/><path d="M18 9h1.5a2.5 2.5 0 0 0 0-5H18"/><path d="M4 22h16"/><path d="M10 14.66V17c0 .55-.47.98-.97 1.21C7.85 18.75 7 20.24 7 22"/><path d="M14 14.66V17c0 .55.47.98.97 1.21C16.15 18.75 17 20.24 17 22"/><path d="M18 2H6v7a6 6 0 0 0 12 0V2Z"/></svg>',
  chart:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 3v18h18"/><path d="M18 17V9"/><path d="M13 17V5"/><path d="M8 17v-3"/></svg>',
  gear:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12.22 2h-.44a2 2 0 0 0-2 2v.18a2 2 0 0 1-1 1.73l-.43.25a2 2 0 0 1-2 0l-.15-.08a2 2 0 0 0-2.73.73l-.22.38a2 2 0 0 0 .73 2.73l.15.1a2 2 0 0 1 1 1.72v.51a2 2 0 0 1-1 1.74l-.15.09a2 2 0 0 0-.73 2.73l.22.38a2 2 0 0 0 2.73.73l.15-.08a2 2 0 0 1 2 0l.43.25a2 2 0 0 1 1 1.73V20a2 2 0 0 0 2 2h.44a2 2 0 0 0 2-2v-.18a2 2 0 0 1 1-1.73l.43-.25a2 2 0 0 1 2 0l.15.08a2 2 0 0 0 2.73-.73l.22-.39a2 2 0 0 0-.73-2.73l-.15-.08a2 2 0 0 1-1-1.74v-.5a2 2 0 0 1 1-1.74l.15-.09a2 2 0 0 0 .73-2.73l-.22-.38a2 2 0 0 0-2.73-.73l-.15.08a2 2 0 0 1-2 0l-.43-.25a2 2 0 0 1-1-1.73V4a2 2 0 0 0-2-2z"/><circle cx="12" cy="12" r="3"/></svg>',
  cloud:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M17.5 19H9a7 7 0 1 1 6.71-9h1.79a4.5 4.5 0 1 1 0 9Z"/></svg>'
  ,repeat:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m17 2 4 4-4 4"/><path d="M3 11v-1a4 4 0 0 1 4-4h14"/><path d="m7 22-4-4 4-4"/><path d="M21 13v1a4 4 0 0 1-4 4H3"/></svg>'
  ,sheet:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect width="18" height="18" x="3" y="3" rx="2"/><path d="M3 9h18"/><path d="M3 15h18"/><path d="M9 3v18"/><path d="M15 3v18"/></svg>'
  ,cloudUp:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 13v8"/><path d="m8 17 4-4 4 4"/><path d="M20 16.2A4.5 4.5 0 0 0 17.5 8h-1.8A7 7 0 1 0 4 14.9"/></svg>'
  ,braces:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M8 3H7a2 2 0 0 0-2 2v5a2 2 0 0 1-2 2 2 2 0 0 1 2 2v5c0 1.1.9 2 2 2h1"/><path d="M16 3h1a2 2 0 0 1 2 2v5a2 2 0 0 0 2 2 2 2 0 0 0-2 2v5a2 2 0 0 1-2 2h-1"/></svg>'
  ,trash:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 6h18"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/></svg>'
  ,key:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="7.5" cy="15.5" r="5.5"/><path d="m21 2-9.6 9.6"/><path d="m15.5 7.5 3 3L22 7l-3-3"/></svg>'
  ,logout:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" x2="9" y1="12" y2="12"/></svg>'
  ,unplug:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m19 5 3-3"/><path d="m2 22 3-3"/><path d="M6.3 20.3a2.4 2.4 0 0 0 3.4 0L12 18l-6-6-2.3 2.3a2.4 2.4 0 0 0 0 3.4Z"/><path d="M7.5 13.5 10 11"/><path d="M10.5 16.5 13 14"/><path d="m12 6 6 6 2.3-2.3a2.4 2.4 0 0 0 0-3.4l-2.6-2.6a2.4 2.4 0 0 0-3.4 0Z"/></svg>'
  ,camera:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M14.5 4h-5L7 7H4a2 2 0 0 0-2 2v9a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2h-3l-2.5-3z"/><circle cx="12" cy="13" r="3"/></svg>'
  ,image:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect width="18" height="18" x="3" y="3" rx="2" ry="2"/><circle cx="9" cy="9" r="2"/><path d="m21 15-3.086-3.086a2 2 0 0 0-2.828 0L6 21"/></svg>'
  ,search:'<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.3-4.3"/></svg>'
};
function applyIcons(){
  const set=(id,svg)=>{ const e=document.getElementById(id); if(e) e.innerHTML=svg; };
  set("btnCracks",ICONS.star); set("mbCracks",ICONS.star);
  set("btnRankingTop",ICONS.trophy); set("mbRanking",ICONS.trophy);
  set("btnHub",ICONS.chart); set("mbHub",ICONS.chart);
  set("btnSet",ICONS.gear); set("mbSet",ICONS.gear);
  const cp=document.querySelector("#cloudPill .ic"); if(cp) cp.innerHTML=ICONS.cloud;
  const setBtn=(id,svg)=>{ const e=document.getElementById(id); if(e) e.insertAdjacentHTML("afterbegin", svg); };
  setBtn("changePw",ICONS.key); setBtn("cfgEdit",ICONS.gear); setBtn("signOut",ICONS.logout);
  setBtn("driveChange",ICONS.gear); setBtn("driveDisconnect",ICONS.unplug);
  setBtn("btnCloud2",ICONS.cloud); setBtn("btnTrade",ICONS.repeat);
  setBtn("btnExcel",ICONS.sheet); setBtn("btnExcelImport",ICONS.sheet);
  setBtn("btnDrive",ICONS.cloudUp); setBtn("btnExport",ICONS.braces); setBtn("btnImport",ICONS.braces);
  setBtn("btnAdminXlsx",ICONS.sheet); setBtn("btnAdminJson",ICONS.braces);
  setBtn("btnReset",ICONS.trash);
  const si=document.getElementById("searchIco"); if(si) si.innerHTML=ICONS.search;
}
buildGeoBand();
buildRails();
applyIcons();
render();
updateSearchPlaceholder();
initCloud();
updateCloudUI();

/* Accesibilidad: etiquetas para botones de solo ícono + teclado en la pastilla de nube */
document.querySelectorAll(".x").forEach(b=>{ if(!b.getAttribute("aria-label")) b.setAttribute("aria-label","Cerrar"); });
(function(){ const cp=document.getElementById("cloudPill"); if(cp){ cp.setAttribute("role","button"); cp.setAttribute("tabindex","0"); cp.addEventListener("keydown",e=>{ if(e.key==="Enter"||e.key===" "){ e.preventDefault(); cp.click(); } }); } })();

/* Aparición escalonada solo en la primera carga */
(function(){ const m=document.getElementById("main"); if(!m) return; m.classList.add("reveal-on"); setTimeout(()=>m.classList.remove("reveal-on"),1700); })();
