<!DOCTYPE html>
<html lang="ms">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Detektif Garam: Analisis Kualitatif</title>

    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/framer-motion@10.12.16/dist/framer-motion.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Special+Elite&family=Crimson+Text:ital,wght@0,400;0,600;1,400&family=Courier+Prime:wght@400;700&display=swap" rel="stylesheet">

    <style>
        :root { --amber:#f59e0b; --red:#dc2626; --dark:#080604; }
        * { box-sizing:border-box; }
        body { font-family:'Crimson Text',serif; background:var(--dark); overflow-x:hidden; }
        body::after {
            content:''; position:fixed; inset:0; pointer-events:none; z-index:9999;
            background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
            opacity:0.45;
        }
        body::before {
            content:''; position:fixed; inset:0; pointer-events:none; z-index:9998;
            background:repeating-linear-gradient(0deg,transparent,transparent 3px,rgba(0,0,0,0.025) 3px,rgba(0,0,0,0.025) 4px);
        }
        .df  { font-family:'Special Elite',cursive; }
        .mf  { font-family:'Courier Prime',monospace; }
        .sf  { font-family:'Crimson Text',serif; }
        .crime-tape {
            background:repeating-linear-gradient(-45deg,#f59e0b,#f59e0b 18px,#080604 18px,#080604 36px);
            height:26px;
        }
        .dp {
            background:linear-gradient(135deg,#100e07 0%,#151209 100%);
            border:1px solid rgba(245,158,11,0.2);
            box-shadow:0 8px 32px rgba(0,0,0,0.85),inset 0 0 40px rgba(245,158,11,0.01);
        }
        .ga { text-shadow:0 0 12px rgba(245,158,11,0.9),0 0 24px rgba(245,158,11,0.4); }
        .opt-btn {
            position:relative; overflow:hidden; transition:all 0.2s ease;
            border:1px solid rgba(245,158,11,0.15); background:rgba(16,14,7,0.8);
        }
        .opt-btn::before {
            content:''; position:absolute; left:-100%; top:0; width:100%; height:100%;
            background:linear-gradient(90deg,transparent,rgba(245,158,11,0.08),transparent);
            transition:left 0.4s ease;
        }
        .opt-btn:not(:disabled):hover::before { left:100%; }
        .opt-btn:not(:disabled):hover {
            border-color:rgba(245,158,11,0.5); background:rgba(245,158,11,0.06);
            transform:translateX(5px); box-shadow:-3px 0 0 rgba(245,158,11,0.4);
        }
        /* Answer result states */
        .opt-correct {
            border:2px solid #10b981 !important; background:rgba(16,185,129,0.15) !important;
            box-shadow:0 0 22px rgba(16,185,129,0.45),inset 0 0 16px rgba(16,185,129,0.06) !important;
        }
        .opt-wrong {
            border:2px solid #ef4444 !important; background:rgba(239,68,68,0.15) !important;
            box-shadow:0 0 22px rgba(239,68,68,0.45),inset 0 0 16px rgba(239,68,68,0.06) !important;
        }
        .opt-revealed {
            border:2px solid #10b981 !important; background:rgba(16,185,129,0.12) !important;
            box-shadow:0 0 30px rgba(16,185,129,0.6),0 0 60px rgba(16,185,129,0.2) !important;
        }
        .opt-dim { opacity:0.28 !important; filter:grayscale(1); }
        /* Animations */
        @keyframes stamp-drop {
            0%{transform:scale(4) rotate(-12deg);opacity:0}
            55%{transform:scale(0.92) rotate(-12deg);opacity:1}
            75%{transform:scale(1.04) rotate(-12deg)}
            100%{transform:scale(1) rotate(-12deg);opacity:1}
        }
        .stamp-anim { animation:stamp-drop 0.55s cubic-bezier(0.175,0.885,0.32,1.275) forwards; }
        @keyframes flicker { 0%,19%,21%,23%,25%,54%,56%,100%{opacity:1} 20%,22%,24%,55%{opacity:0.7} }
        .flicker { animation:flicker 4s infinite; }
        @keyframes wrongShake { 0%,100%{transform:translateX(0)} 20%{transform:translateX(-9px)} 40%{transform:translateX(9px)} 60%{transform:translateX(-6px)} 80%{transform:translateX(6px)} }
        .wrong-shake { animation:wrongShake 0.45s ease; }
        @keyframes correctBounce { 0%{transform:scale(1)} 40%{transform:scale(1.03)} 70%{transform:scale(0.98)} 100%{transform:scale(1)} }
        .correct-bounce { animation:correctBounce 0.5s ease; }
        @keyframes revealPulse { 0%{box-shadow:0 0 0 0 rgba(16,185,129,0)} 40%{box-shadow:0 0 35px 8px rgba(16,185,129,0.65)} 100%{box-shadow:0 0 25px 3px rgba(16,185,129,0.4)} }
        .reveal-pulse { animation:revealPulse 0.7s ease 0.15s forwards; }
        @keyframes redsweep { 0%,100%{opacity:0.08} 50%{opacity:0.18} }
        .redsweep { animation:redsweep 1.5s ease-in-out infinite; }
        .prog-bar { background:linear-gradient(90deg,#b45309,#f59e0b,#dc2626); box-shadow:0 0 10px rgba(245,158,11,0.5); }
        .stamp-box { font-family:'Special Elite',cursive; border:5px double #dc2626; color:#dc2626; text-transform:uppercase; letter-spacing:5px; box-shadow:0 0 25px rgba(220,38,38,0.5),inset 0 0 25px rgba(220,38,38,0.08); }
        ::-webkit-scrollbar { width:5px; }
        ::-webkit-scrollbar-track { background:#080604; }
        ::-webkit-scrollbar-thumb { background:#f59e0b; border-radius:3px; }
        .vignette { background:radial-gradient(ellipse at center,transparent 35%,rgba(0,0,0,0.85) 100%); }
        .bracket-tl { position:absolute; top:10px; left:10px; width:20px; height:20px; border-left:2px solid rgba(245,158,11,0.4); border-top:2px solid rgba(245,158,11,0.4); }
        .bracket-br { position:absolute; bottom:10px; right:10px; width:20px; height:20px; border-right:2px solid rgba(245,158,11,0.4); border-bottom:2px solid rgba(245,158,11,0.4); }
    </style>
</head>
<body>
<div class="fixed inset-0 z-0">
    <img src="https://images.unsplash.com/photo-1509228468518-180dd4864904?q=80&w=2070&auto=format&fit=crop"
         alt="bg" class="w-full h-full object-cover opacity-25"/>
    <div class="absolute inset-0 bg-gradient-to-b from-black/80 via-black/50 to-black/90"></div>
    <div class="absolute inset-0 vignette"></div>
</div>
<div class="fixed top-0 left-0 right-0 z-50 crime-tape opacity-60"></div>
<div id="root"></div>

<script type="text/babel">
const { useState, useEffect } = React;
const { motion, AnimatePresence } = window.Motion;

/* ================================================================ AUDIO */
let _ctx = null;
const getCtx = () => {
    if (!_ctx) _ctx = new (window.AudioContext || window.webkitAudioContext)();
    if (_ctx.state === 'suspended') _ctx.resume();
    return _ctx;
};
const note = (ctx, freq, type, start, dur, vol) => {
    const o = ctx.createOscillator(), g = ctx.createGain();
    o.connect(g); g.connect(ctx.destination);
    o.type = type; o.frequency.value = freq;
    g.gain.setValueAtTime(0, start);
    g.gain.linearRampToValueAtTime(vol, start + 0.02);
    g.gain.exponentialRampToValueAtTime(0.001, start + dur);
    o.start(start); o.stop(start + dur);
};
const playTone = (type) => {
    try {
        const ctx = getCtx(); const now = ctx.currentTime;
        if (type === 'correct') {
            [[523,'sine',0,0.5,0.28],[659,'sine',0.12,0.5,0.22],[784,'sine',0.24,0.6,0.18],[1046,'sine',0.38,0.7,0.14]]
                .forEach(a => note(ctx,a[0],a[1],now+a[2],a[3],a[4]));
        } else if (type === 'wrong') {
            [[220,'sawtooth',0,0.25,0.22],[196,'sawtooth',0.1,0.25,0.18],[174,'sawtooth',0.2,0.3,0.14]]
                .forEach(a => note(ctx,a[0],a[1],now+a[2],a[3],a[4]));
        } else if (type === 'mission') {
            [[392,'triangle',0,0.6,0.3],[523,'triangle',0.15,0.6,0.28],[659,'triangle',0.3,0.7,0.24],
             [784,'triangle',0.45,0.8,0.2],[1046,'sine',0.6,1.0,0.16]]
                .forEach(a => note(ctx,a[0],a[1],now+a[2],a[3],a[4]));
        } else if (type === 'click') {
            note(ctx,900,'square',now,0.04,0.1);
        } else if (type === 'gameover') {
            [[523,'sine',0,0.9,0.35],[440,'sine',0.3,0.9,0.3],[392,'sine',0.6,1.0,0.25],[349,'sine',0.9,1.2,0.2]]
                .forEach(a => note(ctx,a[0],a[1],now+a[2],a[3],a[4]));
        } else if (type === 'stamp') {
            const buf = ctx.createBuffer(1,ctx.sampleRate*0.15,ctx.sampleRate);
            const d = buf.getChannelData(0);
            for (let i=0;i<d.length;i++) d[i]=(Math.random()*2-1)*Math.pow(1-i/d.length,2);
            const src=ctx.createBufferSource(),g=ctx.createGain(),f=ctx.createBiquadFilter();
            src.buffer=buf; src.connect(f); f.connect(g); g.connect(ctx.destination);
            f.type='lowpass'; f.frequency.value=400; g.gain.value=0.9; src.start(now);
        } else if (type === 'ticket') {
            [[700,'sine',0,0.15,0.18],[560,'sine',0.1,0.2,0.14]].forEach(a => note(ctx,a[0],a[1],now+a[2],a[3],a[4]));
        }
    } catch(e) {}
};
let _bgInterval=null, _drone=null;
const startBgMusic = () => {
    try {
        const ctx = getCtx(); stopBgMusic();
        _drone = ctx.createOscillator();
        const dG=ctx.createGain(), dF=ctx.createBiquadFilter();
        _drone.connect(dF); dF.connect(dG); dG.connect(ctx.destination);
        _drone.frequency.value=55; _drone.type='sawtooth';
        dF.type='lowpass'; dF.frequency.value=180; dG.gain.value=0.028; _drone.start();
        const scale=[196,220,233,261,293,311,349,392,440];
        const phrases=[[0,2,4,6],[1,3,5,4],[0,4,2,6],[3,1,5,0]]; let pi=0;
        const pluck = () => {
            const c=getCtx();
            phrases[pi%phrases.length].forEach((ni,i)=>{
                const o=c.createOscillator(),g=c.createGain(),dl=c.createDelay(0.6),dg=c.createGain();
                o.connect(g); g.connect(c.destination); g.connect(dl); dl.connect(dg); dg.connect(c.destination);
                dl.delayTime.value=0.35; dg.gain.value=0.25; o.frequency.value=scale[ni%scale.length]; o.type='triangle';
                const t=c.currentTime+i*0.28;
                g.gain.setValueAtTime(0,t); g.gain.linearRampToValueAtTime(0.055,t+0.06);
                g.gain.exponentialRampToValueAtTime(0.001,t+2.2); o.start(t); o.stop(t+2.2);
            });
            pi++;
        };
        pluck(); _bgInterval=setInterval(pluck,3200);
    } catch(e) {}
};
const stopBgMusic = () => {
    if (_bgInterval){clearInterval(_bgInterval);_bgInterval=null;}
    if (_drone){try{_drone.stop();}catch(e){} _drone=null;}
};

/* ================================================================ QUIZ DATA */
const missions = [
    {
        id:1, title:"Misi 1: Misteri Serbuk Hijau", caseNo:"KES-001",
        image:"https://images.unsplash.com/photo-1532094349884-543bc11b234d?q=80&w=1000&auto=format&fit=crop",
        questions:[
            { text:"Anda menjumpai serbuk garam berwarna hijau di tempat kejadian. Apabila dimasukkan ke dalam air suling, serbuk ini langsung tidak larut dan mendap di dasar bikar. Berdasarkan sifat keterlarutannya, apakah kemungkinan garam ini?",
              options:["Kuprum (II) karbonat","Kuprum (II) klorida","Ferum (II) sulfat"],
              answer:"Kuprum (II) karbonat", wrongToRemove:"Kuprum (II) klorida",
              explanation:"Tepat! Kuprum (II) karbonat adalah garam yang tidak larut dalam air." },
            { text:"Seterusnya, anda memanaskan garam ini dengan kuat. Warna pepejal bertukar daripada hijau kepada HITAM yang kekal hitam walaupun selepas sejuk. Apakah kation yang disahkan hadir?",
              options:["Ion Ferum (III), Fe³⁺","Ion Kuprum (II), Cu²⁺","Ion Zink, Zn²⁺"],
              answer:"Ion Kuprum (II), Cu²⁺", wrongToRemove:"Ion Ferum (III), Fe³⁺",
              explanation:"Betul! Baki hitam selepas pemanasan menunjukkan kehadiran ion Cu²⁺." },
            { text:"Semasa ujian pemanasan tadi, satu gas terbebas dan ia mengeruhkan air kapur. Berdasarkan gabungan ujian ini, apakah nama penuh garam misteri pertama kita?",
              options:["Kuprum (II) nitrat","Kuprum (II) sulfat","Kuprum (II) karbonat"],
              answer:"Kuprum (II) karbonat", wrongToRemove:"Kuprum (II) sulfat",
              explanation:"Syabas! Gas yang mengeruhkan air kapur ialah karbon dioksida — mengesahkan garam karbonat." }
        ]
    },
    {
        id:2, title:"Misi 2: Misteri Garam Perubah Warna", caseNo:"KES-002",
        image:"https://images.unsplash.com/photo-1596495577886-d920f1fb7238?q=80&w=1000&auto=format&fit=crop",
        questions:[
            { text:"Di bilik rahsia, suspek meninggalkan garam putih. Garam ini larut sepenuhnya di dalam air suling, menghasilkan larutan jernih dan tidak berwarna. Antara berikut, yang manakah BUKAN suspek garam ini?",
              options:["Plumbum (II) sulfat","Plumbum (II) nitrat","Kalsium nitrat"],
              answer:"Plumbum (II) sulfat", wrongToRemove:"Kalsium nitrat",
              explanation:"Betul! Plumbum (II) sulfat adalah garam yang TAK LARUT dalam air." },
            { text:"Anda memanaskan garam putih yang terlarutkan ini. Ia membebaskan gas berwarna perang, berbau sengit, dan sebatang kayu uji berbara menyala apabila dimasukkan. Apakah anion bagi garam ini?",
              options:["Ion klorida, Cl⁻","Ion nitrat, NO₃⁻","Ion karbonat, CO₃²⁻"],
              answer:"Ion nitrat, NO₃⁻", wrongToRemove:"Ion klorida, Cl⁻",
              explanation:"Tepat! Gas perang (NO₂) dan gas oksigen (O₂) membuktikan kehadiran ion nitrat." },
            { text:"Baki serbuk di dalam tabung uji berwarna PERANG semasa panas, dan bertukar KUNING apabila sejuk. Apakah kation misteri ini, dan namakan garam kita yang sebenar!",
              options:["Zink nitrat","Plumbum (II) nitrat","Kalsium nitrat"],
              answer:"Plumbum (II) nitrat", wrongToRemove:"Kalsium nitrat",
              explanation:"Hebat! Baki perang (panas) dan kuning (sejuk) menandakan ion Pb²⁺." }
        ]
    },
    {
        id:3, title:"Misi 3: Petunjuk Kation Penyamar", caseNo:"KES-003",
        image:"https://images.unsplash.com/photo-1559757148-5c350d0d3c56?q=80&w=1000&auto=format&fit=crop",
        questions:[
            { text:"Suspek ketiga menjatuhkan serbuk garam putih. Apabila dicampur ke dalam air, ia TIDAK LARUT. Mengingati 'Lagu Garam', kation manakah yang MUSTAHIL hadir dalam garam tak larut ini?",
              options:["Kation Zink, Zn²⁺","Kation Kalsium, Ca²⁺","Kation Ammonium, NH₄⁺"],
              answer:"Kation Ammonium, NH₄⁺", wrongToRemove:"Kation Kalsium, Ca²⁺",
              explanation:"Betul! Semua garam Ammonium sentiasa larut sepenuhnya." },
            { text:"Anda memanaskan garam tak larut ini. Baki pemanasan berwarna KUNING semasa panas dan bertukar PUTIH apabila sejuk. Apakah kation yang berjaya dikesan?",
              options:["Ion Plumbum (II), Pb²⁺","Ion Zink, Zn²⁺","Ion Aluminium, Al³⁺"],
              answer:"Ion Zink, Zn²⁺", wrongToRemove:"Ion Aluminium, Al³⁺",
              explanation:"Tepat! Baki kuning (panas) dan putih (sejuk) adalah ciri khas ion Zink (Zn²⁺)." },
            { text:"Akhir sekali, ujian pemanasan garam tadi turut membebaskan gas tidak berwarna yang mengeruhkan air kapur. Apakah nama penuh garam terakhir ini?",
              options:["Zink karbonat","Zink nitrat","Zink sulfat"],
              answer:"Zink karbonat", wrongToRemove:"Zink sulfat",
              explanation:"Tahniah! Ion zink dan ion karbonat membentuk Zink karbonat." }
        ]
    },
    {
        id:4, title:"Misi 4: Misteri Hablur Biru", caseNo:"KES-004",
        image:"https://images.unsplash.com/photo-1628595351029-c2bf17511435?q=80&w=1000&auto=format&fit=crop",
        questions:[
            { text:"Anda menemui serpihan hablur garam berwarna biru di lokasi suspek. Apabila ditambah air suling, ia larut sepenuhnya dan membentuk larutan berwarna biru. Berdasarkan 'Lagu Garam', antara garam berikut, yang manakah TIDAK mungkin bagi garam biru ini?",
              options:["Kuprum (II) sulfat","Kuprum (II) karbonat","Kuprum (II) nitrat"],
              answer:"Kuprum (II) karbonat", wrongToRemove:"Kuprum (II) nitrat",
              explanation:"Tepat! Kuprum (II) karbonat tidak larut dalam air dan berwarna hijau, bukannya biru." },
            { text:"Detektif memanaskan garam biru tersebut. Baki pepejal berubah menjadi warna hitam semasa masih panas, dan kekal hitam walaupun selepas sejuk. Berdasarkan perubahan warna ini, apakah kation yang disahkan hadir?",
              options:["Ion Kuprum (II), Cu²⁺","Ion Ferum (III), Fe³⁺","Ion Zink, Zn²⁺"],
              answer:"Ion Kuprum (II), Cu²⁺", wrongToRemove:"Ion Ferum (III), Fe³⁺",
              explanation:"Betul! Baki berwarna hitam selepas pemanasan menunjukkan kehadiran ion Kuprum (II), Cu²⁺." },
            { text:"Semasa pemanasan yang sama, terdapat pembebasan gas berwarna perang yang berbau sengit dan bersifat asid. Ujian kayu uji berbara turut menyalakan semula kayu uji tersebut. Gabungkan anion ini dengan kation dari soalan 2. Apakah nama penuh garam ini?",
              options:["Kuprum (II) klorida","Kuprum (II) sulfat","Kuprum (II) nitrat"],
              answer:"Kuprum (II) nitrat", wrongToRemove:"Kuprum (II) klorida",
              explanation:"Tepat! Gas perang (NO₂) dan gas oksigen membuktikan anion nitrat. Garam ini ialah Kuprum (II) nitrat." }
        ]
    },
    {
        id:5, title:"Misi 5: Misteri Bau Sengit", caseNo:"KES-005",
        image:"https://images.unsplash.com/photo-1576086213369-97a306d36557?q=80&w=1000&auto=format&fit=crop",
        questions:[
            { text:"Suspek meninggalkan serbuk garam berwarna putih. Ujian keterlarutan menunjukkan ia larut sepenuhnya dalam air suling membentuk larutan jernih tidak berwarna. Kumpulan kation manakah yang SENTIASA menghasilkan garam terlarutkan tanpa kecuali?",
              options:["Kalsium, Magnesium, Zink","Ammonium, Kalium, Natrium","Plumbum, Ferum, Kuprum"],
              answer:"Ammonium, Kalium, Natrium", wrongToRemove:"Kalsium, Magnesium, Zink",
              explanation:"Betul! Ammonium, kalium, natrium sentiasa larut dalam air tanpa pengecualian." },
            { text:"Anda memanaskan serbuk putih tersebut. Tabung uji membebaskan gas tidak berwarna yang berbau sangat sengit. Gas ini menukarkan kertas litmus merah lembap kepada biru, menunjukkan ia bersifat alkali. Apakah jenis garam ini?",
              options:["Garam Karbonat","Garam Ammonium","Garam Nitrat"],
              answer:"Garam Ammonium", wrongToRemove:"Garam Karbonat",
              explanation:"Tepat! Gas berbau sengit dan bersifat alkali adalah ciri tepat bagi Garam Ammonium — gas ammonia (NH₃)." },
            { text:"Detektif memanaskan bahagian lain garam pada suhu lebih tinggi. Kali ini ia turut membebaskan gas perang yang berasid, dan gas yang menyalakan kayu uji berbara. Kationnya ialah Ammonium dari soalan 2. Apakah nama penuh garam suspek terakhir?",
              options:["Ammonium klorida","Ammonium sulfat","Ammonium nitrat"],
              answer:"Ammonium nitrat", wrongToRemove:"Ammonium sulfat",
              explanation:"Syabas Detektif! Gas perang berasid dan gas oksigen membuktikan anion nitrat. Garam terakhir ialah Ammonium nitrat." }
        ]
    }
];

/* ================================================================ PARTICLES */
const Particles = () => {
    const pts = Array.from({length:16},(_,i)=>({id:i,x:Math.random()*100,y:Math.random()*100,s:Math.random()*2+1,dur:Math.random()*12+8}));
    return (
        <div className="fixed inset-0 pointer-events-none overflow-hidden z-0">
            {pts.map(p=>(
                <motion.div key={p.id} className="absolute rounded-full bg-amber-500/20"
                    style={{left:`${p.x}%`,top:`${p.y}%`,width:p.s,height:p.s}}
                    animate={{y:[0,-28,0],opacity:[0.15,0.5,0.15]}}
                    transition={{duration:p.dur,repeat:Infinity,ease:'easeInOut'}}/>
            ))}
        </div>
    );
};

/* ================================================================ TICKET BAR */
const TicketBar = ({ hintsLeft, onUse, canUse }) => (
    <div className="flex items-center justify-between mb-3 bg-black/30 rounded-lg px-3 py-2 border border-purple-500/15">
        <div className="flex items-center space-x-2">
            <i className="fas fa-ticket-alt text-purple-400 text-sm"></i>
            <span className="mf text-purple-400/80 text-xs uppercase tracking-wider">Tiket Bantuan</span>
        </div>
        <div className="flex items-center space-x-2">
            {Array.from({length:3}).map((_,i)=>(
                <div key={i} className={`w-6 h-6 rounded border-2 flex items-center justify-center transition-all duration-500 ${i<hintsLeft?'border-purple-400/70 bg-purple-500/20 text-purple-300':'border-gray-700/60 bg-transparent text-gray-700 opacity-40'}`}>
                    <i className="fas fa-ticket-alt text-xs"></i>
                </div>
            ))}
            {canUse && hintsLeft > 0 && (
                <button onClick={onUse}
                    className="mf text-xs bg-purple-900/40 text-purple-300 hover:bg-purple-800/60 border border-purple-500/40 py-1 px-2.5 rounded-lg transition-all hover:scale-105 active:scale-95 ml-1">
                    <i className="fas fa-magic mr-1"></i>GUNA
                </button>
            )}
            {hintsLeft === 0 && <span className="mf text-xs text-gray-600 ml-1 italic">Habis</span>}
        </div>
    </div>
);

/* ================================================================ MISSION COMPLETE */
const MissionComplete = ({ missionNumber, onContinue }) => {
    const [stampVisible, setStampVisible] = useState(false);
    const [showSub, setShowSub] = useState(false);
    useEffect(() => {
        playTone('stamp');
        setTimeout(()=>{setStampVisible(true);playTone('mission');}, 300);
        setTimeout(()=>setShowSub(true), 800);
        confetti({particleCount:120,spread:70,origin:{y:0.5},colors:['#f59e0b','#dc2626','#fef3c7','#ffffff']});
        const t = setTimeout(onContinue, 4200);
        return ()=>clearTimeout(t);
    }, []);
    return (
        <motion.div initial={{opacity:0}} animate={{opacity:1}} exit={{opacity:0,transition:{duration:0.8}}}
            className="fixed inset-0 z-50 flex items-center justify-center overflow-hidden"
            style={{background:'rgba(4,3,2,0.94)'}}>
            <div className="absolute inset-0 redsweep" style={{background:'radial-gradient(ellipse at 50% 50%,rgba(220,38,38,0.15) 0%,transparent 65%)'}}/>
            <div className="absolute top-0 left-0 right-0 crime-tape opacity-50"/>
            <div className="absolute bottom-0 left-0 right-0 crime-tape opacity-50"/>
            <div className="text-center relative z-10 px-6">
                {stampVisible && (
                    <div className="stamp-anim inline-block mb-8">
                        <div className="stamp-box text-4xl md:text-5xl font-bold px-10 py-5 rounded-lg df">KES<br/>DISELESAIKAN</div>
                    </div>
                )}
                {showSub && (
                    <motion.div initial={{opacity:0,y:16}} animate={{opacity:1,y:0}} transition={{duration:0.5}}>
                        <p className="df text-amber-400 text-xl md:text-2xl mb-1 ga">MISI {missionNumber} BERJAYA!</p>
                        <p className="mf text-amber-700/70 text-sm tracking-widest mb-6 uppercase">Memuat misi seterusnya...</p>
                        <div className="h-1 bg-amber-900/40 rounded-full max-w-xs mx-auto overflow-hidden border border-amber-700/20">
                            <motion.div className="h-full prog-bar rounded-full" initial={{width:'0%'}} animate={{width:'100%'}} transition={{duration:3.8,ease:'linear'}}/>
                        </div>
                    </motion.div>
                )}
            </div>
        </motion.div>
    );
};

/* ================================================================ ANSWER OPTION */
const AnswerOption = ({ option, index, onSelect, state, disabled }) => {
    const letter = String.fromCharCode(65+index);
    let btnClass = 'opt-btn w-full flex items-center text-left text-white py-4 px-5 rounded-xl ';
    let dotClass = 'w-7 h-7 rounded-full border-2 flex items-center justify-center mr-4 flex-shrink-0 df text-xs ';
    let icon = null;
    let extraAnim = '';

    if (state === 'correct') {
        btnClass += 'opt-correct correct-bounce ';
        dotClass += 'border-emerald-400 bg-emerald-500/30 text-emerald-300 ';
        icon = <i className="fas fa-check-circle text-emerald-400 ml-auto text-xl"></i>;
    } else if (state === 'wrong') {
        btnClass += 'opt-wrong wrong-shake ';
        dotClass += 'border-red-400 bg-red-500/25 text-red-300 ';
        icon = <i className="fas fa-times-circle text-red-400 ml-auto text-xl"></i>;
    } else if (state === 'revealed') {
        btnClass += 'opt-revealed reveal-pulse ';
        dotClass += 'border-emerald-400 bg-emerald-500/30 text-emerald-300 ';
        icon = (
            <div className="ml-auto flex items-center space-x-1">
                <span className="mf text-xs text-emerald-400 bg-emerald-900/50 px-2 py-0.5 rounded border border-emerald-500/50">JAWAPAN BETUL</span>
                <i className="fas fa-star text-emerald-400 text-base"></i>
            </div>
        );
    } else if (state === 'dim') {
        btnClass += 'opt-dim ';
        dotClass += 'border-gray-700 bg-gray-800 text-gray-600 ';
    } else {
        dotClass += 'border-amber-500/30 bg-amber-500/10 text-amber-500 ';
    }

    return (
        <motion.button
            initial={{opacity:0,x:-10}} animate={{opacity:1,x:0}} transition={{delay:index*0.07}}
            whileTap={!disabled && !state ? {scale:0.97} : {}}
            onClick={!disabled && !state ? ()=>onSelect(option) : undefined}
            className={btnClass}
            style={{cursor: disabled||state ? 'default':'crosshair'}}
        >
            <div className={dotClass}>{letter}</div>
            <span className="sf text-base flex-1">{option}</span>
            {icon}
        </motion.button>
    );
};

/* ================================================================ APP */
function App() {
    const [gameState,          setGameState]          = useState('welcome');
    const [playerName,         setPlayerName]         = useState('');
    const [currentMission,     setCurrentMission]     = useState(0);
    const [currentQuestion,    setCurrentQuestion]    = useState(0);
    const [score,              setScore]              = useState(0);
    const [showFeedback,       setShowFeedback]       = useState(false);
    const [feedbackMsg,        setFeedbackMsg]        = useState('');
    const [isCorrect,          setIsCorrect]          = useState(false);
    const [shake,              setShake]              = useState(false);
    const [removedOption,      setRemovedOption]      = useState(null);
    const [selectedAnswer,     setSelectedAnswer]     = useState(null);
    const [leaderboard,        setLeaderboard]        = useState([]);
    const [missionCompleteNum, setMissionCompleteNum] = useState(0);
    const [showMissionComplete,setShowMissionComplete]= useState(false);
    const [musicOn,            setMusicOn]            = useState(false);
    const [hintsLeft,          setHintsLeft]          = useState(3);

    useEffect(()=>{
        const saved = JSON.parse(localStorage.getItem('dgScores'))||[];
        setLeaderboard(saved);
    },[]);

    const toggleMusic = () => { if(musicOn){stopBgMusic();setMusicOn(false);}else{startBgMusic();setMusicOn(true);} };
    const triggerConfetti = () => confetti({particleCount:200,spread:80,origin:{y:0.5},colors:['#f59e0b','#dc2626','#fef3c7','#10b981','#ffffff']});
    const handleLogin = (e) => { e.preventDefault(); if(playerName.trim()){playTone('click');setGameState('instructions');} };
    const startGame = () => { setGameState('playing'); startBgMusic(); setMusicOn(true); };

    const handleAnswer = (option) => {
        if (showFeedback) return;
        const correct = option === missions[currentMission].questions[currentQuestion].answer;
        setIsCorrect(correct); setSelectedAnswer(option);
        if (correct) {
            playTone('correct'); setScore(s=>s+10);
            setFeedbackMsg('🔍 '+missions[currentMission].questions[currentQuestion].explanation);
        } else {
            playTone('wrong'); setShake(true); setTimeout(()=>setShake(false),500);
            setFeedbackMsg('❌ Jawapan anda salah. Jawapan yang betul telah ditandakan dengan jelas di atas.');
        }
        setShowFeedback(true);
    };

    const useHint = () => {
        if (hintsLeft>0 && !removedOption) {
            playTone('ticket'); setHintsLeft(h=>h-1);
            setRemovedOption(missions[currentMission].questions[currentQuestion].wrongToRemove);
        }
    };

    const saveAndEnd = (finalScore) => {
        const entry = {name:playerName,score:finalScore,date:new Date().toLocaleDateString()};
        const saved  = JSON.parse(localStorage.getItem('dgScores'))||[];
        const updated = [...saved,entry].sort((a,b)=>b.score-a.score).slice(0,5);
        setLeaderboard(updated); localStorage.setItem('dgScores',JSON.stringify(updated));
        stopBgMusic(); playTone('gameover'); setGameState('gameover');
        if (finalScore>80) triggerConfetti();
    };

    const nextQuestion = () => {
        const lastQ = currentQuestion+1>=missions[currentMission].questions.length;
        const lastM = currentMission+1>=missions.length;
        setShowFeedback(false); setRemovedOption(null); setSelectedAnswer(null);
        if (!lastQ) { setCurrentQuestion(q=>q+1); }
        else if (!lastM) { setMissionCompleteNum(currentMission+1); setShowMissionComplete(true); }
        else { saveAndEnd(score); }
    };

    const onMissionContinue = () => { setShowMissionComplete(false); setCurrentMission(m=>m+1); setCurrentQuestion(0); };

    const restartGame = () => {
        stopBgMusic();
        setCurrentMission(0);setCurrentQuestion(0);setScore(0);
        setShowFeedback(false);setRemovedOption(null);setSelectedAnswer(null);
        setShowMissionComplete(false);setMusicOn(false);setHintsLeft(3);
        setGameState('welcome');
    };

    const totalQ    = missions.reduce((a,m)=>a+m.questions.length,0);
    const answeredQ = missions.slice(0,currentMission).reduce((a,m)=>a+m.questions.length,0)+currentQuestion;
    const progress  = (answeredQ/totalQ)*100;

    // Determine each option's visual state after feedback
    const getState = (opt) => {
        if (!showFeedback) return null;
        const correctAns = missions[currentMission].questions[currentQuestion].answer;
        if (opt === correctAns) return isCorrect ? 'correct' : 'revealed';
        if (opt === selectedAnswer && !isCorrect) return 'wrong';
        return 'dim';
    };

    /* WELCOME */
    if (gameState==='welcome') return (
        <><Particles/>
        <motion.div initial={{opacity:0}} animate={{opacity:1}} className="relative z-10 flex flex-col items-center justify-center min-h-screen p-4 pt-10 text-center">
            <div className="dp rounded-2xl p-10 max-w-md w-full relative overflow-hidden">
                <div className="bracket-tl"/><div className="bracket-br"/>
                <motion.div animate={{y:[0,-6,0]}} transition={{duration:3,repeat:Infinity,ease:'easeInOut'}}>
                    <i className="fas fa-user-secret text-7xl text-amber-400 block mb-5 flicker ga"/>
                </motion.div>
                <p className="mf text-amber-700/70 text-xs tracking-widest uppercase mb-2">[ UNIT KHAS KIMIA FORENSIK ]</p>
                <h1 className="df text-5xl text-amber-300 ga">Detektif</h1>
                <h1 className="df text-4xl text-white mb-1">Garam</h1>
                <div className="w-full h-px bg-gradient-to-r from-transparent via-amber-600/50 to-transparent my-5"/>
                <p className="sf text-amber-200/60 mb-6 text-base">Daftar profil ejen anda untuk memulakan tugasan.</p>
                <form onSubmit={handleLogin} className="space-y-4">
                    <input type="text" required placeholder="NAMA EJEN ANDA..." value={playerName} onChange={e=>setPlayerName(e.target.value)}
                        className="w-full bg-black/50 text-amber-200 text-base py-4 px-5 rounded-lg border border-amber-600/30 focus:outline-none focus:border-amber-400 focus:ring-1 focus:ring-amber-500/40 transition-all text-center mf placeholder-amber-800/50 uppercase tracking-wider"/>
                    <motion.button whileHover={{scale:1.03}} whileTap={{scale:0.96}} type="submit"
                        className="w-full bg-amber-600 hover:bg-amber-500 text-black font-bold py-4 rounded-lg text-lg df tracking-wider transition-all"
                        style={{boxShadow:'0 0 22px rgba(245,158,11,0.35)'}}>
                        DAFTAR MASUK &nbsp;<i className="fas fa-id-badge"/>
                    </motion.button>
                </form>
            </div>
        </motion.div></>
    );

    /* INSTRUCTIONS */
    if (gameState==='instructions') return (
        <><Particles/>
        <motion.div initial={{opacity:0,x:40}} animate={{opacity:1,x:0}} className="relative z-10 flex flex-col items-center justify-center min-h-screen p-4 py-12">
            <div className="dp rounded-2xl max-w-2xl w-full relative overflow-hidden">
                <div className="crime-tape opacity-50"/>
                <div className="p-8 md:p-10">
                    <div className="flex items-center mb-7">
                        <div className="bg-red-600/20 border border-red-500/40 rounded-lg p-3 mr-4">
                            <i className="fas fa-folder-open text-red-400 text-2xl"/>
                        </div>
                        <div>
                            <p className="mf text-amber-600/60 text-xs tracking-widest uppercase">SULIT | DOKUMEN RASMI</p>
                            <h1 className="df text-2xl md:text-3xl text-white">Taklimat Misi</h1>
                        </div>
                    </div>
                    <div className="bg-black/40 p-6 rounded-xl border border-amber-500/15 mb-6">
                        <p className="df text-amber-400 text-lg mb-3">SELAMAT DATANG, DETEKTIF {playerName.toUpperCase()}</p>
                        <p className="sf text-gray-300 mb-5 leading-relaxed text-lg">Ketua Polis memerlukan kepakaran kimia anda untuk menyelesaikan Analisis Kualitatif bagi 5 kes jenayah. Sila patuhi arahan berikut:</p>
                        <div className="space-y-3">
                            {[
                                {ic:'fa-search',     cl:'text-amber-400',  bg:'bg-amber-500/10',  t:<span>Terdapat <b className="text-amber-300">5 Misi Garam Misteri</b>. Setiap misi mempunyai 3 soalan petunjuk — jumlah 15 soalan.</span>},
                                {ic:'fa-star',       cl:'text-yellow-400', bg:'bg-yellow-500/10', t:<span>Setiap jawapan betul memberi <b className="text-yellow-300">+10 Markah</b>. Markah penuh ialah 150.</span>},
                                {ic:'fa-ticket-alt', cl:'text-purple-400', bg:'bg-purple-500/10', t:<span>Anda mempunyai <b className="text-purple-300">3 Tiket Bantuan</b> untuk keseluruhan permainan. Setiap tiket membuang 1 jawapan salah. Gunakan dengan bijak!</span>},
                            ].map((item,i)=>(
                                <motion.div key={i} initial={{opacity:0,x:-16}} animate={{opacity:1,x:0}} transition={{delay:i*0.15}}
                                    className={`flex items-start p-3 rounded-lg ${item.bg} border border-white/5`}>
                                    <i className={`fas ${item.ic} ${item.cl} mt-1 mr-3 text-base flex-shrink-0`}/>
                                    <span className="sf text-gray-200 text-base leading-relaxed">{item.t}</span>
                                </motion.div>
                            ))}
                        </div>
                    </div>
                    <motion.button whileHover={{scale:1.02}} whileTap={{scale:0.97}} onClick={startGame}
                        className="w-full bg-gradient-to-r from-amber-600 to-red-700 hover:from-amber-500 hover:to-red-600 text-white font-bold py-4 rounded-xl df tracking-widest text-lg flex justify-center items-center transition-all"
                        style={{boxShadow:'0 0 28px rgba(245,158,11,0.3)'}}>
                        <i className="fas fa-play mr-3"/> MULA SIASATAN
                    </motion.button>
                    <p className="mf text-amber-800/50 text-xs text-center mt-3">* Muzik latar akan bermain secara automatik</p>
                </div>
            </div>
        </motion.div></>
    );

    /* GAME OVER */
    if (gameState==='gameover') {
        return (
            <><Particles/>
            <motion.div initial={{opacity:0}} animate={{opacity:1}} className="relative z-10 flex flex-col items-center justify-center min-h-screen p-4 py-12 w-full">
                <div className="grid grid-cols-1 md:grid-cols-2 gap-5 max-w-4xl w-full">
                    <div className="dp rounded-2xl overflow-hidden flex flex-col items-center text-center">
                        <div className="crime-tape opacity-30 w-full"/>
                        <div className="p-8 w-full flex flex-col items-center">
                            <motion.i initial={{scale:0,rotate:-180}} animate={{scale:1,rotate:0}} transition={{type:'spring',delay:0.3}}
                                className="fas fa-shield-alt text-6xl text-amber-400 mb-4" style={{filter:'drop-shadow(0 0 14px rgba(245,158,11,0.7))'}}/>
                            <p className="mf text-amber-700/60 text-xs tracking-widest uppercase mb-1">SIASATAN SELESAI</p>
                            <h2 className="df text-white text-xl mb-6">Detektif {playerName}</h2>
                            <div className="bg-black/50 rounded-xl p-6 mb-6 border border-amber-500/20 w-full">
                                <p className="mf text-amber-700/60 text-xs uppercase tracking-widest mb-1">MARKAH AKHIR</p>
                                <motion.p initial={{scale:0}} animate={{scale:1}} transition={{type:'spring',delay:0.5}} className="df text-6xl text-amber-400 ga">{score}</motion.p>
                                <p className="mf text-gray-600 text-sm mt-1">/ 150</p>
                            </div>
                            <motion.button whileHover={{scale:1.03}} whileTap={{scale:0.97}} onClick={restartGame}
                                className="w-full bg-gray-900 hover:bg-gray-800 text-amber-400 font-semibold py-3 rounded-lg border border-amber-500/25 df tracking-wider transition-all">
                                <i className="fas fa-sign-out-alt mr-2"/> LOG KELUAR
                            </motion.button>
                        </div>
                    </div>
                    <div className="dp rounded-2xl p-8">
                        <div className="flex items-center mb-5">
                            <i className="fas fa-trophy text-3xl text-amber-400 mr-3 ga"/>
                            <div>
                                <p className="mf text-amber-700/50 text-xs tracking-widest uppercase">REKOD TERBAIK</p>
                                <h2 className="df text-xl text-white">Carta Kedudukan</h2>
                            </div>
                        </div>
                        <div className="space-y-2">
                            {leaderboard.length===0 ? (
                                <p className="mf text-gray-600 text-sm text-center py-8 italic">[ TIADA REKOD ]</p>
                            ) : leaderboard.map((e,i)=>(
                                <motion.div key={i} initial={{opacity:0,x:20}} animate={{opacity:1,x:0}} transition={{delay:i*0.1}}
                                    className={`flex justify-between items-center p-3 rounded-lg border ${i===0?'bg-amber-500/15 border-amber-500/40':i===1?'bg-gray-400/10 border-gray-400/25':i===2?'bg-orange-700/15 border-orange-700/25':'bg-black/20 border-white/5'}`}>
                                    <div className="flex items-center">
                                        <span className={`df text-sm w-6 ${i===0?'text-amber-400':i===1?'text-gray-300':i===2?'text-orange-500':'text-gray-600'}`}>#{i+1}</span>
                                        <span className="mf text-sm ml-3 truncate max-w-[120px] text-gray-200 uppercase">{e.name}</span>
                                    </div>
                                    <div className="text-right">
                                        <span className="df text-amber-400 font-bold">{e.score}</span>
                                        <span className="mf text-gray-600 text-xs ml-2 block">{e.date}</span>
                                    </div>
                                </motion.div>
                            ))}
                        </div>
                    </div>
                </div>
            </motion.div></>
        );
    }

    /* PLAYING */
    const mission  = missions[currentMission];
    const question = mission.questions[currentQuestion];

    return (
        <><Particles/>
        <AnimatePresence>
            {showMissionComplete && <MissionComplete missionNumber={missionCompleteNum} onContinue={onMissionContinue}/>}
        </AnimatePresence>

        <div className="relative z-10 flex flex-col items-center py-8 px-4 min-h-screen">
            <AnimatePresence mode="wait">
                <motion.div key={`${currentMission}-${currentQuestion}`}
                    initial={{opacity:0,y:24}} animate={{opacity:1,y:0}} exit={{opacity:0,y:-24}} transition={{duration:0.35}}
                    className="w-full max-w-3xl">

                    {/* HUD */}
                    <div className="flex justify-between items-center mb-4 dp p-4 rounded-xl">
                        <div className="flex items-center space-x-3">
                            <div className="bg-amber-500/15 text-amber-400 p-2.5 rounded-lg border border-amber-500/25">
                                <i className="fas fa-user-secret text-lg"/>
                            </div>
                            <div>
                                <p className="mf text-amber-700/60 text-xs uppercase tracking-wider">EJEN</p>
                                <p className="df text-amber-200 truncate max-w-[130px]">{playerName.toUpperCase()}</p>
                            </div>
                        </div>
                        <button onClick={toggleMusic}
                            className={`mf text-xs px-3 py-1.5 rounded-lg border transition-colors ${musicOn?'bg-amber-500/15 border-amber-500/30 text-amber-400':'bg-gray-900/50 border-gray-700 text-gray-500'}`}>
                            <i className={`fas ${musicOn?'fa-volume-up':'fa-volume-mute'} mr-1`}/>{musicOn?'ON':'OFF'}
                        </button>
                        <div className="text-right">
                            <p className="mf text-amber-700/60 text-xs uppercase tracking-wider">MARKAH</p>
                            <motion.p key={score} initial={{scale:1.4}} animate={{scale:1}} className="df text-3xl text-amber-400 ga">{score}</motion.p>
                        </div>
                    </div>

                    {/* Progress */}
                    <div className="mb-3">
                        <div className="flex justify-between mf text-xs text-amber-700/60 mb-1 uppercase tracking-wider">
                            <span>Bukti Dikumpul</span><span>{answeredQ}/{totalQ}</span>
                        </div>
                        <div className="w-full bg-black/50 rounded-full h-2 border border-amber-600/15 overflow-hidden">
                            <motion.div animate={{width:`${progress}%`}} className="prog-bar h-2 rounded-full"/>
                        </div>
                    </div>

                    {/* Mission dots */}
                    <div className="flex space-x-1.5 mb-4">
                        {missions.map((_,i)=>(
                            <div key={i} className={`flex-1 h-1.5 rounded-full transition-all duration-500 ${i<currentMission?'bg-amber-500':i===currentMission?'bg-amber-400/60':'bg-gray-800'}`}/>
                        ))}
                    </div>

                    {/* Quiz card */}
                    <motion.div animate={shake?{x:[-7,7,-7,7,0]}:{}} transition={{duration:0.35}} className="dp rounded-2xl overflow-hidden">

                        {/* Mission image */}
                        <div className="h-44 md:h-52 w-full relative overflow-hidden">
                            <img src={mission.image} alt="Bukti" className="w-full h-full object-cover opacity-55"/>
                            <div className="absolute inset-0 bg-gradient-to-t from-black via-black/40 to-transparent"/>
                            <div className="absolute top-0 left-0 right-0 crime-tape opacity-35"/>
                            <div className="absolute bottom-4 left-5 flex items-center flex-wrap gap-2">
                                <span className="mf text-xs bg-red-700/80 text-white px-3 py-1 rounded uppercase tracking-wider">{mission.caseNo}</span>
                                <span className="df text-sm bg-black/60 text-amber-300 px-3 py-1 rounded border border-amber-500/25">{mission.title}</span>
                            </div>
                            <div className="absolute bottom-4 right-5 mf text-xs text-amber-500/70 bg-black/60 px-2 py-1 rounded">
                                PETUNJUK {currentQuestion+1}/3
                            </div>
                        </div>

                        {/* Question + options */}
                        <div className="p-6 md:p-8">
                            <div className="flex items-start mb-5">
                                <i className="fas fa-search text-amber-500 mt-1.5 mr-3 text-base flex-shrink-0"/>
                                <p className="sf text-gray-100 leading-relaxed text-lg">{question.text}</p>
                            </div>

                            <AnimatePresence mode="wait">
                                {!showFeedback ? (
                                    <motion.div key="opts" initial={{opacity:0}} animate={{opacity:1}} exit={{opacity:0}}>
                                        <TicketBar hintsLeft={hintsLeft} onUse={useHint} canUse={!removedOption}/>
                                        <div className="space-y-3">
                                            {question.options.map((opt,i)=>{
                                                if (opt===removedOption) return null;
                                                return <AnswerOption key={i} option={opt} index={i} onSelect={handleAnswer} state={null} disabled={false}/>;
                                            })}
                                        </div>
                                    </motion.div>
                                ) : (
                                    <motion.div key="fb" initial={{opacity:0,y:10}} animate={{opacity:1,y:0}}>
                                        {/* All options shown with colour states */}
                                        <div className="space-y-3 mb-5 pointer-events-none">
                                            {question.options.map((opt,i)=>(
                                                <AnswerOption key={i} option={opt} index={i} onSelect={()=>{}} state={getState(opt)} disabled={true}/>
                                            ))}
                                        </div>

                                        {/* Feedback message */}
                                        <div className={`p-4 rounded-xl mb-5 border flex items-start space-x-3 ${isCorrect?'bg-emerald-900/30 border-emerald-500/40 text-emerald-100':'bg-red-900/35 border-red-600/50 text-red-100'}`}>
                                            <div className={`text-xl flex-shrink-0 mt-0.5 ${isCorrect?'text-emerald-400':'text-red-400'}`}>
                                                <i className={`fas ${isCorrect?'fa-check-circle':'fa-times-circle'}`}/>
                                            </div>
                                            <p className="sf text-base leading-relaxed">{feedbackMsg}</p>
                                        </div>

                                        <motion.button whileHover={{scale:1.02}} whileTap={{scale:0.98}} onClick={nextQuestion}
                                            className="w-full bg-amber-600 hover:bg-amber-500 text-black font-bold py-4 rounded-xl df tracking-wider text-lg flex justify-center items-center transition-all"
                                            style={{boxShadow:'0 0 22px rgba(245,158,11,0.25)'}}>
                                            {currentMission===missions.length-1 && currentQuestion===mission.questions.length-1
                                                ? <><i className="fas fa-flag-checkered mr-2"/> TUTUP KES</>
                                                : <><i className="fas fa-arrow-right mr-2"/> TERUSKAN SIASATAN</>
                                            }
                                        </motion.button>
                                    </motion.div>
                                )}
                            </AnimatePresence>
                        </div>
                    </motion.div>
                </motion.div>
            </AnimatePresence>
        </div></>
    );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);
</script>
</body>
</html>
