 <html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
<meta name="theme-color" content="#0d0d14">
<title>WordShuffle — Unscramble. Compete. Dominate.</title>
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700;800;900&family=Space+Grotesk:wght@400;500;600;700&family=JetBrains+Mono:wght@400;600;700&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0d0d14;
  --bg2: #12121f;
  --bg3: #1a1a2e;
  --card: rgba(255,255,255,.04);
  --card-border: rgba(255,255,255,.08);
  --card-hover: rgba(255,255,255,.07);
  --violet: #7c3aed;
  --violet-light: #a78bfa;
  --violet-glow: rgba(124,58,237,.35);
  --pink: #ec4899;
  --pink-light: #f472b6;
  --cyan: #06b6d4;
  --cyan-light: #67e8f9;
  --lime: #84cc16;
  --lime-light: #bef264;
  --orange: #f97316;
  --red: #ef4444;
  --green: #22c55e;
  --gold: #eab308;
  --text: #f1f0ff;
  --text2: #a09db8;
  --text3: #5a5878;
  --success: #22c55e;
  --error: #ef4444;
  --radius: 12px;
  --radius-lg: 18px;
  --radius-xl: 24px;
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body { font-family: 'Outfit', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; min-height: 100vh; }
::selection { background: var(--violet); color: #fff; }
::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-track { background: var(--bg2); } ::-webkit-scrollbar-thumb { background: var(--violet); border-radius: 3px; }
a { color: inherit; text-decoration: none; }
button { font-family: 'Outfit', sans-serif; cursor: pointer; border: none; outline: none; }
input { font-family: 'Outfit', sans-serif; outline: none; border: none; }

/* ── ANIMATED BG ── */
.bg-canvas { position: fixed; inset: 0; z-index: 0; overflow: hidden; pointer-events: none; }
.bg-mesh {
  position: absolute; inset: -50%; width: 200%; height: 200%;
  background:
    radial-gradient(ellipse 60% 50% at 20% 20%, rgba(124,58,237,.12) 0%, transparent 60%),
    radial-gradient(ellipse 50% 40% at 80% 10%, rgba(236,72,153,.09) 0%, transparent 55%),
    radial-gradient(ellipse 45% 50% at 50% 80%, rgba(6,182,212,.08) 0%, transparent 60%),
    radial-gradient(ellipse 30% 30% at 85% 65%, rgba(132,204,22,.06) 0%, transparent 50%);
  animation: meshDrift 20s ease-in-out infinite alternate;
}
@keyframes meshDrift {
  0% { transform: translate(0,0) rotate(0deg); }
  33% { transform: translate(-2%,3%) rotate(.5deg); }
  66% { transform: translate(2%,-1%) rotate(-.3deg); }
  100% { transform: translate(-1%,2%) rotate(.2deg); }
}
.bg-grid {
  position: absolute; inset: 0;
  background-image: repeating-linear-gradient(0deg,transparent,transparent 47px,rgba(255,255,255,.018) 48px),
                    repeating-linear-gradient(90deg,transparent,transparent 47px,rgba(255,255,255,.018) 48px);
}
.noise { position: absolute; inset: 0; opacity: .025; background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='1'/%3E%3C/svg%3E"); }
.floating-letter {
  position: absolute; font-family: 'Outfit', sans-serif; font-weight: 900; font-size: clamp(40px,8vw,100px);
  opacity: 0; pointer-events: none; animation: floatLetter linear infinite;
}
@keyframes floatLetter {
  0% { transform: translateY(110vh) rotate(0deg); opacity: 0; }
  5% { opacity: .06; }
  90% { opacity: .04; }
  100% { transform: translateY(-10vh) rotate(360deg); opacity: 0; }
}
.layer { position: relative; z-index: 1; }

/* ── NAV ── */
nav {
  position: sticky; top: 0; z-index: 100;
  background: rgba(13,13,20,.85); backdrop-filter: blur(24px) saturate(1.5);
  border-bottom: 1px solid var(--card-border);
  padding: 0 clamp(16px, 4vw, 48px);
  display: flex; align-items: center; justify-content: space-between; height: 64px;
  transition: background .3s;
}
.nav-logo { display: flex; align-items: center; gap: 10px; cursor: pointer; }
.logo-icon {
  width: 36px; height: 36px; border-radius: 10px;
  background: linear-gradient(135deg, var(--violet), var(--pink));
  display: flex; align-items: center; justify-content: center;
  font-weight: 900; font-size: 18px; color: #fff; letter-spacing: -1px;
  flex-shrink: 0;
}
.logo-text { font-weight: 800; font-size: 20px; letter-spacing: -.5px; }
.logo-text span { background: linear-gradient(90deg, var(--violet-light), var(--pink-light)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.nav-links { display: flex; gap: 4px; align-items: center; }
.nav-links a {
  font-size: 13px; font-weight: 600; color: var(--text2); padding: 6px 14px;
  border-radius: 8px; transition: all .2s; cursor: pointer; white-space: nowrap;
}
.nav-links a:hover, .nav-links a.active { color: var(--text); background: rgba(255,255,255,.06); }
.nav-actions { display: flex; gap: 10px; align-items: center; }
.btn-ghost { background: transparent; border: 1px solid var(--card-border); color: var(--text2); font-size: 13px; font-weight: 600; padding: 7px 16px; border-radius: 10px; transition: all .2s; }
.btn-ghost:hover { border-color: var(--violet-light); color: var(--violet-light); background: var(--violet-glow); }
.btn-primary {
  background: linear-gradient(135deg, var(--violet), var(--pink)); color: #fff;
  font-size: 13px; font-weight: 700; padding: 7px 18px; border-radius: 10px;
  transition: all .2s; position: relative; overflow: hidden;
}
.btn-primary::after { content:''; position:absolute; inset:0; background: linear-gradient(135deg,rgba(255,255,255,.15),transparent); }
.btn-primary:hover { transform: translateY(-1px); box-shadow: 0 8px 24px rgba(124,58,237,.4); }
.btn-primary:active { transform: translateY(0); }
.hamburger { display: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 6px; }
.hamburger span { display: block; width: 22px; height: 2px; background: var(--text); border-radius: 2px; transition: all .3s; }
.hamburger.open span:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.hamburger.open span:nth-child(2) { opacity: 0; }
.hamburger.open span:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }
.mob-menu {
  position: fixed; top: 64px; left: 0; right: 0; background: rgba(13,13,20,.98);
  backdrop-filter: blur(24px); border-bottom: 1px solid var(--card-border);
  z-index: 99; padding: 16px; display: flex; flex-direction: column; gap: 4px;
  transform: translateY(-120%); opacity: 0; transition: all .3s cubic-bezier(.4,0,.2,1);
}
.mob-menu.open { transform: translateY(0); opacity: 1; }
.mob-menu a { font-size: 16px; font-weight: 600; color: var(--text2); padding: 12px 16px; border-radius: 10px; cursor: pointer; }
.mob-menu a:hover, .mob-menu a.active { color: var(--text); background: var(--card); }

/* ── PAGES ── */
.page { display: none; }
.page.active { display: block; }

/* ── HERO ── */
.hero {
  min-height: calc(100vh - 64px); display: flex; flex-direction: column;
  align-items: center; justify-content: center; text-align: center;
  padding: clamp(40px,8vh,80px) clamp(16px,4vw,48px) clamp(60px,10vh,100px);
  position: relative;
}
.hero-eyebrow {
  display: inline-flex; align-items: center; gap: 8px;
  background: rgba(124,58,237,.15); border: 1px solid rgba(124,58,237,.3);
  border-radius: 100px; padding: 6px 16px; margin-bottom: 28px;
  font-size: 12px; font-weight: 600; color: var(--violet-light); letter-spacing: 1.5px; text-transform: uppercase;
  animation: fadeSlide .6s ease both;
}
.live-dot { width: 7px; height: 7px; background: var(--lime); border-radius: 50%; animation: pulse 1.5s infinite; flex-shrink: 0; }
@keyframes pulse { 0%,100% { opacity:1; box-shadow:0 0 0 0 rgba(132,204,22,.5); } 50% { opacity:.8; box-shadow:0 0 0 5px rgba(132,204,22,0); } }
.hero-title {
  font-size: clamp(44px,8vw,100px); font-weight: 900; line-height: 1;
  letter-spacing: -3px; margin-bottom: 24px;
  animation: fadeSlide .6s .1s ease both;
}
.hero-title .word-grad {
  background: linear-gradient(135deg, var(--violet-light) 0%, var(--pink-light) 40%, var(--cyan-light) 100%);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}
.hero-title .shimmer-text { position: relative; }
.hero-subtitle {
  font-size: clamp(16px,2.5vw,22px); color: var(--text2); font-weight: 400;
  max-width: 540px; line-height: 1.65; margin-bottom: 40px;
  animation: fadeSlide .6s .2s ease both;
}
.hero-cta { display: flex; gap: 14px; justify-content: center; flex-wrap: wrap; margin-bottom: 56px; animation: fadeSlide .6s .3s ease both; }
.cta-primary {
  background: linear-gradient(135deg, var(--violet) 0%, var(--pink) 100%);
  color: #fff; font-size: 16px; font-weight: 700; padding: 14px 32px;
  border-radius: var(--radius); transition: all .25s; position: relative; overflow: hidden;
  box-shadow: 0 0 40px rgba(124,58,237,.4);
}
.cta-primary::before { content:''; position:absolute; inset:0; background:linear-gradient(135deg,rgba(255,255,255,.2),transparent); }
.cta-primary:hover { transform: translateY(-2px) scale(1.02); box-shadow: 0 12px 40px rgba(124,58,237,.6); }
.cta-secondary {
  background: var(--card); border: 1px solid var(--card-border); color: var(--text);
  font-size: 16px; font-weight: 600; padding: 14px 32px;
  border-radius: var(--radius); transition: all .25s; backdrop-filter: blur(10px);
}
.cta-secondary:hover { background: var(--card-hover); border-color: rgba(255,255,255,.15); transform: translateY(-2px); }
.hero-scramble-demo {
  display: flex; align-items: center; gap: 20px; flex-wrap: wrap; justify-content: center;
  animation: fadeSlide .6s .4s ease both;
}
.demo-word { display: flex; gap: 8px; }
.demo-letter {
  width: clamp(36px,6vw,52px); height: clamp(40px,7vw,58px);
  background: rgba(124,58,237,.12); border: 1.5px solid rgba(124,58,237,.3);
  border-radius: 10px; display: flex; align-items: center; justify-content: center;
  font-family: 'JetBrains Mono', monospace; font-weight: 700;
  font-size: clamp(18px,3vw,26px); color: var(--violet-light);
  transition: all .4s cubic-bezier(.34,1.56,.64,1);
  position: relative;
}
.demo-letter.correct { background: rgba(34,197,94,.12); border-color: rgba(34,197,94,.4); color: var(--green); }
.demo-letter.active { transform: scale(1.1); box-shadow: 0 0 20px rgba(124,58,237,.5); }
.demo-arrow { font-size: 24px; color: var(--text3); }
.hero-stats { display: flex; gap: 32px; flex-wrap: wrap; justify-content: center; margin-top: 56px; animation: fadeSlide .6s .5s ease both; }
.hero-stat { text-align: center; }
.hero-stat-num { font-size: 28px; font-weight: 800; background: linear-gradient(135deg,var(--violet-light),var(--pink-light)); -webkit-background-clip:text; -webkit-text-fill-color:transparent; display:block; }
.hero-stat-label { font-size: 13px; color: var(--text3); margin-top: 2px; }
@keyframes fadeSlide { from { opacity:0; transform:translateY(20px); } to { opacity:1; transform:translateY(0); } }

/* ── SECTION HEADER ── */
.sec-hdr { text-align: center; padding: 0 16px; margin-bottom: 48px; }
.sec-eyebrow { font-size: 11px; font-weight: 700; letter-spacing: 2.5px; text-transform: uppercase; color: var(--violet-light); margin-bottom: 12px; }
.sec-title { font-size: clamp(28px,4vw,44px); font-weight: 800; letter-spacing: -1.5px; margin-bottom: 14px; }
.sec-title span { background: linear-gradient(135deg,var(--violet-light),var(--pink-light)); -webkit-background-clip:text; -webkit-text-fill-color:transparent; }
.sec-sub { font-size: 16px; color: var(--text2); max-width: 480px; margin: 0 auto; line-height: 1.65; }

/* ── GLASS CARD ── */
.glass {
  background: var(--card); backdrop-filter: blur(20px) saturate(1.3);
  border: 1px solid var(--card-border); border-radius: var(--radius-lg);
  transition: all .25s;
}
.glass:hover { background: var(--card-hover); border-color: rgba(124,58,237,.25); transform: translateY(-2px); }

/* ── GAME SECTION ── */
.game-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 900px; margin: 0 auto; }
.diff-tabs { display: flex; gap: 6px; background: rgba(255,255,255,.04); border: 1px solid var(--card-border); border-radius: var(--radius); padding: 4px; margin-bottom: 28px; flex-wrap: wrap; }
.diff-tab {
  flex: 1; min-width: 70px; font-size: 13px; font-weight: 600; padding: 8px 16px;
  border-radius: 8px; background: transparent; color: var(--text2);
  transition: all .2s; cursor: pointer;
}
.diff-tab.active { background: linear-gradient(135deg,var(--violet),var(--pink)); color: #fff; box-shadow: 0 4px 12px rgba(124,58,237,.35); }
.diff-tab:hover:not(.active) { background: rgba(255,255,255,.05); color: var(--text); }
.game-card {
  background: var(--bg2); border: 1px solid var(--card-border);
  border-radius: var(--radius-xl); overflow: hidden;
  box-shadow: 0 0 80px rgba(124,58,237,.08);
}
.game-top {
  padding: 24px 28px; display: flex; align-items: center; justify-content: space-between;
  border-bottom: 1px solid var(--card-border); flex-wrap: wrap; gap: 12px;
  background: linear-gradient(135deg,rgba(124,58,237,.06),rgba(236,72,153,.04));
}
.game-info { display: flex; align-items: center; gap: 20px; flex-wrap: wrap; }
.info-chip {
  display: flex; align-items: center; gap: 7px; padding: 6px 14px;
  background: rgba(255,255,255,.05); border: 1px solid var(--card-border);
  border-radius: 100px; font-size: 13px; font-weight: 600; color: var(--text2);
}
.info-chip.highlight { background: rgba(124,58,237,.12); border-color: rgba(124,58,237,.3); color: var(--violet-light); }
.info-chip .icon { font-size: 15px; }
.timer-ring { position: relative; width: 52px; height: 52px; flex-shrink: 0; }
.timer-ring svg { transform: rotate(-90deg); }
.timer-ring circle { fill: none; stroke-width: 3; }
.timer-bg { stroke: rgba(255,255,255,.08); }
.timer-arc { stroke: var(--violet-light); stroke-linecap: round; transition: stroke-dashoffset .1s linear; }
.timer-text {
  position: absolute; inset: 0; display: flex; align-items: center; justify-content: center;
  font-family: 'JetBrains Mono', monospace; font-size: 16px; font-weight: 700; color: var(--text);
}
.game-body { padding: clamp(24px,4vw,40px); }
.category-badge {
  display: inline-flex; align-items: center; gap: 6px; font-size: 11px; font-weight: 700;
  letter-spacing: 1.5px; text-transform: uppercase; color: var(--cyan-light);
  background: rgba(6,182,212,.1); border: 1px solid rgba(6,182,212,.2);
  border-radius: 100px; padding: 4px 12px; margin-bottom: 20px;
}
.scrambled-display { text-align: center; margin-bottom: 28px; }
.scrambled-label { font-size: 12px; font-weight: 600; color: var(--text3); letter-spacing: 2px; text-transform: uppercase; margin-bottom: 14px; }
.scrambled-letters { display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; }
.scrambled-letter {
  width: clamp(40px,7vw,56px); height: clamp(44px,8vw,62px);
  background: rgba(124,58,237,.1); border: 1.5px solid rgba(124,58,237,.25);
  border-radius: 10px; display: flex; align-items: center; justify-content: center;
  font-family: 'JetBrains Mono', monospace; font-weight: 700;
  font-size: clamp(20px,3.5vw,28px); color: var(--violet-light);
  cursor: pointer; transition: all .2s; user-select: none;
  position: relative; overflow: hidden;
}
.scrambled-letter::before { content:''; position:absolute; inset:0; background:linear-gradient(135deg,rgba(255,255,255,.08),transparent); }
.scrambled-letter:hover { transform: translateY(-3px) scale(1.05); box-shadow: 0 8px 20px rgba(124,58,237,.3); border-color: rgba(124,58,237,.5); }
.scrambled-letter.used { opacity: .25; transform: scale(.9); cursor: default; }
.scrambled-letter:active:not(.used) { transform: scale(.95); }
.answer-row { margin-bottom: 24px; }
.answer-label { font-size: 12px; font-weight: 600; color: var(--text3); letter-spacing: 2px; text-transform: uppercase; margin-bottom: 12px; }
.answer-slots { display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; min-height: 62px; }
.answer-slot {
  width: clamp(40px,7vw,56px); height: clamp(44px,8vw,62px);
  background: rgba(255,255,255,.03); border: 1.5px dashed rgba(255,255,255,.12);
  border-radius: 10px; display: flex; align-items: center; justify-content: center;
  font-family: 'JetBrains Mono', monospace; font-weight: 700;
  font-size: clamp(20px,3.5vw,28px); color: var(--text);
  transition: all .25s cubic-bezier(.34,1.56,.64,1); cursor: pointer;
  position: relative; overflow: hidden;
}
.answer-slot.filled { background: rgba(124,58,237,.12); border-color: rgba(124,58,237,.4); border-style: solid; color: var(--violet-light); transform: scale(1.02); }
.answer-slot.correct { background: rgba(34,197,94,.12); border-color: rgba(34,197,94,.4); border-style: solid; color: var(--green); }
.answer-slot.wrong { background: rgba(239,68,68,.12); border-color: rgba(239,68,68,.4); border-style: solid; color: var(--red); animation: shake .4s ease; }
@keyframes shake { 0%,100%{transform:translateX(0)} 20%{transform:translateX(-8px)} 40%{transform:translateX(8px)} 60%{transform:translateX(-6px)} 80%{transform:translateX(6px)} }
.game-input-row {
  display: flex; gap: 10px; margin-bottom: 20px;
}
.game-input {
  flex: 1; background: rgba(255,255,255,.04); border: 1.5px solid var(--card-border);
  border-radius: var(--radius); padding: 14px 20px; font-size: 18px; font-weight: 600;
  color: var(--text); font-family: 'JetBrains Mono', monospace; letter-spacing: 4px; text-transform: uppercase;
  text-align: center; transition: all .2s;
}
.game-input:focus { border-color: var(--violet); background: rgba(124,58,237,.06); box-shadow: 0 0 0 3px rgba(124,58,237,.15); }
.game-input.success { border-color: var(--green); background: rgba(34,197,94,.08); animation: bounceIn .4s ease; }
.game-input.error { border-color: var(--red); background: rgba(239,68,68,.08); animation: shake .4s ease; }
@keyframes bounceIn { 0%{transform:scale(.95)} 50%{transform:scale(1.03)} 100%{transform:scale(1)} }
.game-actions { display: flex; gap: 10px; flex-wrap: wrap; }
.btn-game {
  flex: 1; min-width: 100px; font-size: 14px; font-weight: 700; padding: 12px 20px;
  border-radius: var(--radius); transition: all .2s;
}
.btn-submit { background: linear-gradient(135deg,var(--violet),var(--pink)); color:#fff; box-shadow:0 4px 16px rgba(124,58,237,.3); }
.btn-submit:hover { transform:translateY(-1px); box-shadow:0 8px 24px rgba(124,58,237,.5); }
.btn-skip { background:rgba(255,255,255,.05); border:1px solid var(--card-border); color:var(--text2); }
.btn-skip:hover { background:rgba(255,255,255,.08); color:var(--text); }
.btn-hint { background:rgba(234,179,8,.08); border:1px solid rgba(234,179,8,.2); color:var(--gold); }
.btn-hint:hover { background:rgba(234,179,8,.15); }
.hint-text { font-size: 13px; color: var(--text2); background: rgba(234,179,8,.06); border: 1px solid rgba(234,179,8,.15); border-radius: 8px; padding: 10px 14px; margin-bottom: 16px; display: none; }
.hint-text.visible { display: block; animation: fadeSlide .3s ease; }

/* ── XP & SCORE BAR ── */
.xp-bar-wrap { padding: 16px 28px; border-top: 1px solid var(--card-border); background: rgba(255,255,255,.02); }
.xp-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }
.xp-label { font-size: 12px; font-weight: 600; color: var(--text3); }
.xp-val { font-size: 12px; font-weight: 700; color: var(--violet-light); }
.xp-bar-bg { height: 6px; background: rgba(255,255,255,.06); border-radius: 3px; overflow: hidden; }
.xp-bar-fill { height: 100%; background: linear-gradient(90deg,var(--violet),var(--pink)); border-radius: 3px; transition: width .5s cubic-bezier(.4,0,.2,1); }

/* ── COMBO POPUP ── */
.combo-popup {
  position: fixed; top: 50%; left: 50%; transform: translate(-50%,-50%) scale(0);
  background: linear-gradient(135deg,var(--violet),var(--pink));
  color: #fff; font-size: clamp(28px,5vw,48px); font-weight: 900;
  padding: 20px 40px; border-radius: var(--radius-xl);
  z-index: 1000; pointer-events: none;
  box-shadow: 0 20px 60px rgba(124,58,237,.6);
  text-align: center; line-height: 1;
}
.combo-popup.show { animation: comboAnim .8s cubic-bezier(.34,1.56,.64,1) forwards; }
@keyframes comboAnim { 0%{transform:translate(-50%,-50%) scale(0);opacity:0} 30%{transform:translate(-50%,-50%) scale(1.15);opacity:1} 70%{transform:translate(-50%,-50%) scale(1);opacity:1} 100%{transform:translate(-50%,-70%) scale(.9);opacity:0} }

/* ── CONFETTI ── */
.confetti-piece {
  position: fixed; pointer-events: none; z-index: 999;
  border-radius: 3px; animation: confettiFall linear forwards;
}
@keyframes confettiFall { to { transform: translateY(110vh) rotate(1080deg); opacity: 0; } }

/* ── LEADERBOARD ── */
.lb-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 900px; margin: 0 auto; }
.lb-tabs { display: flex; gap: 6px; background: rgba(255,255,255,.04); border: 1px solid var(--card-border); border-radius: var(--radius); padding: 4px; margin-bottom: 28px; }
.lb-tab { flex: 1; font-size: 13px; font-weight: 600; padding: 8px 12px; border-radius: 8px; background: transparent; color: var(--text2); transition: all .2s; cursor: pointer; text-align: center; }
.lb-tab.active { background: linear-gradient(135deg,var(--violet),var(--pink)); color: #fff; box-shadow: 0 4px 12px rgba(124,58,237,.35); }
.lb-card { background: var(--bg2); border: 1px solid var(--card-border); border-radius: var(--radius-xl); overflow: hidden; }
.lb-header { padding: 16px 24px; border-bottom: 1px solid var(--card-border); background: linear-gradient(135deg,rgba(124,58,237,.06),rgba(236,72,153,.04)); font-size: 13px; font-weight: 600; color: var(--text2); display: grid; grid-template-columns: 48px 1fr 80px 80px; gap: 12px; align-items: center; }
.lb-row {
  padding: 14px 24px; border-bottom: 1px solid rgba(255,255,255,.04);
  display: grid; grid-template-columns: 48px 1fr 80px 80px; gap: 12px; align-items: center;
  transition: background .2s; cursor: pointer;
}
.lb-row:hover { background: rgba(255,255,255,.03); }
.lb-row:last-child { border-bottom: none; }
.lb-row.me { background: rgba(124,58,237,.08); border-left: 3px solid var(--violet); padding-left: 21px; }
.lb-rank { font-family: 'JetBrains Mono', monospace; font-size: 16px; font-weight: 700; color: var(--text3); text-align: center; }
.lb-rank.gold { color: #ffd700; }
.lb-rank.silver { color: #c0c0c0; }
.lb-rank.bronze { color: #cd7f32; }
.lb-medal { font-size: 20px; text-align: center; }
.lb-player { display: flex; align-items: center; gap: 12px; }
.lb-avatar { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 15px; font-weight: 800; flex-shrink: 0; }
.lb-name { font-size: 14px; font-weight: 600; }
.lb-badge { font-size: 10px; font-weight: 700; letter-spacing: 1px; text-transform: uppercase; padding: 2px 8px; border-radius: 100px; }
.lb-badge.pro { background: rgba(234,179,8,.15); color: var(--gold); border: 1px solid rgba(234,179,8,.3); }
.lb-badge.streak { background: rgba(239,68,68,.12); color: #ff6b6b; border: 1px solid rgba(239,68,68,.25); }
.lb-score { font-family: 'JetBrains Mono', monospace; font-size: 15px; font-weight: 700; color: var(--violet-light); text-align: right; }
.lb-streak { font-size: 13px; color: var(--orange); font-weight: 600; text-align: right; display: flex; align-items: center; justify-content: flex-end; gap: 4px; }

/* ── DAILY CHALLENGE ── */
.dc-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 900px; margin: 0 auto; }
.dc-header-card {
  background: linear-gradient(135deg,rgba(124,58,237,.2) 0%,rgba(236,72,153,.15) 50%,rgba(6,182,212,.12) 100%);
  border: 1px solid rgba(124,58,237,.25); border-radius: var(--radius-xl);
  padding: clamp(24px,4vw,40px); margin-bottom: 28px; position: relative; overflow: hidden;
}
.dc-header-card::before { content:''; position:absolute; top:-50%; right:-20%; width:300px; height:300px; border-radius:50%; background:radial-gradient(circle,rgba(124,58,237,.15),transparent); }
.dc-theme { font-size: 11px; font-weight: 700; letter-spacing: 2px; text-transform: uppercase; color: var(--cyan-light); margin-bottom: 10px; }
.dc-title { font-size: clamp(22px,4vw,32px); font-weight: 800; letter-spacing: -.5px; margin-bottom: 8px; }
.dc-sub { font-size: 14px; color: var(--text2); margin-bottom: 20px; }
.dc-meta { display: flex; gap: 16px; flex-wrap: wrap; }
.dc-meta-item { display: flex; align-items: center; gap: 6px; font-size: 13px; color: var(--text2); }
.dc-meta-item .val { font-weight: 700; color: var(--text); }
.countdown-badge {
  position: absolute; top: 20px; right: 20px; text-align: center;
  background: rgba(0,0,0,.3); border: 1px solid rgba(255,255,255,.1);
  border-radius: var(--radius); padding: 10px 16px;
}
.countdown-label { font-size: 10px; font-weight: 600; letter-spacing: 1.5px; color: var(--text3); text-transform: uppercase; margin-bottom: 4px; }
.countdown-time { font-family: 'JetBrains Mono', monospace; font-size: 18px; font-weight: 700; color: var(--violet-light); }
.dc-puzzles { display: grid; gap: 12px; }
.dc-puzzle {
  background: var(--bg2); border: 1px solid var(--card-border);
  border-radius: var(--radius-lg); padding: 18px 22px;
  display: flex; align-items: center; justify-content: space-between; gap: 14px;
  transition: all .2s; cursor: pointer; flex-wrap: wrap;
}
.dc-puzzle:hover { background: rgba(124,58,237,.06); border-color: rgba(124,58,237,.25); }
.dc-puzzle.solved { border-color: rgba(34,197,94,.3); background: rgba(34,197,94,.05); }
.dc-puzzle-left { display: flex; align-items: center; gap: 14px; }
.dc-puzzle-num { font-family: 'JetBrains Mono', monospace; font-size: 22px; font-weight: 700; color: var(--text3); width: 32px; }
.dc-puzzle-word { font-family: 'JetBrains Mono', monospace; font-size: 18px; font-weight: 700; color: var(--text); letter-spacing: 3px; }
.dc-puzzle-diff { font-size: 11px; font-weight: 700; padding: 3px 10px; border-radius: 100px; }
.diff-easy { background: rgba(34,197,94,.12); color: var(--green); }
.diff-med { background: rgba(234,179,8,.12); color: var(--gold); }
.diff-hard { background: rgba(239,68,68,.12); color: var(--red); }
.diff-imp { background: rgba(124,58,237,.15); color: var(--violet-light); }
.dc-puzzle-right { display: flex; align-items: center; gap: 12px; }
.dc-solve-btn {
  font-size: 13px; font-weight: 700; padding: 8px 18px; border-radius: 10px;
  background: linear-gradient(135deg,var(--violet),var(--pink)); color: #fff;
  transition: all .2s; white-space: nowrap;
}
.dc-solve-btn:hover { transform: scale(1.03); box-shadow: 0 4px 14px rgba(124,58,237,.4); }
.solved-check { width: 32px; height: 32px; border-radius: 50%; background: rgba(34,197,94,.15); display: flex; align-items: center; justify-content: center; color: var(--green); font-size: 16px; }

/* ── FEATURES SECTION ── */
.features-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); }
.features-grid { display: grid; grid-template-columns: repeat(auto-fit,minmax(260px,1fr)); gap: 16px; max-width: 1100px; margin: 0 auto; }
.feature-card {
  background: var(--bg2); border: 1px solid var(--card-border);
  border-radius: var(--radius-xl); padding: 28px; transition: all .3s;
  position: relative; overflow: hidden;
}
.feature-card::before { content:''; position:absolute; top:0; left:0; right:0; height:2px; border-radius:var(--radius-xl) var(--radius-xl) 0 0; }
.feature-card.v::before { background:linear-gradient(90deg,var(--violet),var(--pink)); }
.feature-card.c::before { background:linear-gradient(90deg,var(--cyan),var(--lime)); }
.feature-card.o::before { background:linear-gradient(90deg,var(--orange),var(--pink)); }
.feature-card.g::before { background:linear-gradient(90deg,var(--lime),var(--cyan)); }
.feature-card:hover { transform:translateY(-4px); border-color:rgba(255,255,255,.12); box-shadow:0 20px 60px rgba(0,0,0,.4); }
.feature-icon { font-size: 36px; margin-bottom: 16px; }
.feature-title { font-size: 18px; font-weight: 700; margin-bottom: 10px; letter-spacing: -.3px; }
.feature-desc { font-size: 14px; color: var(--text2); line-height: 1.65; }
.feature-tags { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 14px; }
.ftag { font-size: 11px; font-weight: 600; padding: 3px 10px; border-radius: 100px; background: rgba(255,255,255,.06); color: var(--text2); }

/* ── PROGRESS ── */
.progress-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 900px; margin: 0 auto; }
.xp-card {
  background: var(--bg2); border: 1px solid var(--card-border);
  border-radius: var(--radius-xl); padding: clamp(24px,4vw,36px); margin-bottom: 28px;
}
.xp-card-top { display: flex; align-items: center; gap: 20px; margin-bottom: 24px; flex-wrap: wrap; }
.level-circle {
  width: 72px; height: 72px; border-radius: 50%; flex-shrink: 0;
  background: linear-gradient(135deg,var(--violet),var(--pink));
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  box-shadow: 0 0 30px rgba(124,58,237,.4);
}
.level-num { font-size: 20px; font-weight: 900; color: #fff; line-height: 1; }
.level-word { font-size: 8px; font-weight: 700; color: rgba(255,255,255,.7); letter-spacing: 1.5px; text-transform: uppercase; }
.xp-info { flex: 1; }
.xp-title { font-size: 20px; font-weight: 800; margin-bottom: 4px; }
.xp-sub { font-size: 13px; color: var(--text2); margin-bottom: 12px; }
.xp-bigbar-bg { height: 10px; background: rgba(255,255,255,.06); border-radius: 5px; overflow: hidden; }
.xp-bigbar-fill { height: 100%; background: linear-gradient(90deg,var(--violet),var(--pink)); border-radius: 5px; transition: width .6s cubic-bezier(.4,0,.2,1); width: 65%; }
.xp-bigbar-label { display: flex; justify-content: space-between; font-size: 12px; color: var(--text3); margin-top: 6px; }
.badges-grid { display: grid; grid-template-columns: repeat(auto-fill,minmax(80px,1fr)); gap: 12px; }
.badge-item { text-align: center; cursor: pointer; transition: transform .2s; }
.badge-item:hover { transform: translateY(-3px) scale(1.05); }
.badge-icon { width: 56px; height: 56px; border-radius: 14px; display: flex; align-items: center; justify-content: center; font-size: 28px; margin: 0 auto 6px; border: 1.5px solid rgba(255,255,255,.08); }
.badge-icon.unlocked { background: rgba(124,58,237,.15); border-color: rgba(124,58,237,.3); box-shadow: 0 4px 12px rgba(124,58,237,.2); }
.badge-icon.locked { background: rgba(255,255,255,.03); opacity: .4; filter: grayscale(1); }
.badge-name { font-size: 11px; font-weight: 600; color: var(--text2); }
.stats-row { display: grid; grid-template-columns: repeat(auto-fit,minmax(130px,1fr)); gap: 12px; margin-top: 24px; }
.stat-card { background: rgba(255,255,255,.04); border: 1px solid var(--card-border); border-radius: var(--radius); padding: 16px; text-align: center; }
.stat-card-num { font-size: 28px; font-weight: 800; color: var(--text); margin-bottom: 4px; }
.stat-card-label { font-size: 12px; color: var(--text3); font-weight: 500; }

/* ── AI GENERATOR ── */
.ai-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 900px; margin: 0 auto; }
.ai-card { background: var(--bg2); border: 1px solid var(--card-border); border-radius: var(--radius-xl); overflow: hidden; }
.ai-top {
  padding: 24px 28px; border-bottom: 1px solid var(--card-border);
  background: linear-gradient(135deg,rgba(6,182,212,.08),rgba(132,204,22,.05));
  display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 12px;
}
.ai-badge { display: inline-flex; align-items: center; gap: 6px; background: rgba(6,182,212,.12); border: 1px solid rgba(6,182,212,.25); border-radius: 100px; padding: 4px 12px; font-size: 11px; font-weight: 700; letter-spacing: 1.5px; text-transform: uppercase; color: var(--cyan-light); }
.ai-dot { width: 7px; height: 7px; background: var(--cyan); border-radius: 50%; animation: pulse 1.5s infinite; }
.ai-body { padding: 28px; }
.mode-grid { display: grid; grid-template-columns: repeat(auto-fill,minmax(120px,1fr)); gap: 10px; margin-bottom: 24px; }
.mode-btn {
  background: rgba(255,255,255,.04); border: 1.5px solid var(--card-border);
  border-radius: var(--radius); padding: 14px 10px; text-align: center; cursor: pointer;
  transition: all .2s;
}
.mode-btn:hover { border-color: rgba(124,58,237,.3); background: rgba(124,58,237,.06); transform: translateY(-2px); }
.mode-btn.active { background: rgba(124,58,237,.12); border-color: rgba(124,58,237,.4); box-shadow: 0 4px 16px rgba(124,58,237,.2); }
.mode-btn .mode-emoji { font-size: 28px; margin-bottom: 6px; display: block; }
.mode-btn .mode-name { font-size: 12px; font-weight: 600; color: var(--text); }
.ai-generate-btn {
  width: 100%; padding: 16px; font-size: 16px; font-weight: 700;
  background: linear-gradient(135deg,var(--cyan) 0%,var(--violet) 100%);
  color: #fff; border-radius: var(--radius); transition: all .25s;
  box-shadow: 0 4px 20px rgba(6,182,212,.25); position: relative; overflow: hidden;
}
.ai-generate-btn::before { content:''; position:absolute; inset:0; background:linear-gradient(135deg,rgba(255,255,255,.15),transparent); }
.ai-generate-btn:hover { transform: translateY(-2px); box-shadow: 0 10px 30px rgba(6,182,212,.4); }
.ai-generate-btn:disabled { opacity: .6; cursor: not-allowed; transform: none; }
.ai-output { margin-top: 20px; display: none; }
.ai-output.visible { display: block; animation: fadeSlide .4s ease; }
.ai-words { display: flex; flex-wrap: wrap; gap: 10px; margin-bottom: 16px; }
.ai-word {
  display: flex; gap: 0; background: rgba(255,255,255,.05); border: 1px solid var(--card-border);
  border-radius: 10px; overflow: hidden; cursor: pointer; transition: all .2s;
}
.ai-word:hover { border-color: rgba(124,58,237,.3); background: rgba(124,58,237,.06); transform: scale(1.02); }
.ai-word-scrambled { padding: 8px 12px; font-family: 'JetBrains Mono', monospace; font-size: 14px; font-weight: 700; color: var(--violet-light); background: rgba(124,58,237,.08); }
.ai-word-answer { padding: 8px 12px; font-size: 14px; font-weight: 600; color: var(--text2); }
.ai-loading { display: flex; align-items: center; gap: 12px; padding: 20px; color: var(--text2); font-size: 14px; }
.ai-spinner { width: 20px; height: 20px; border: 2px solid rgba(6,182,212,.2); border-top-color: var(--cyan); border-radius: 50%; animation: spin .8s linear infinite; flex-shrink: 0; }
@keyframes spin { to { transform: rotate(360deg); } }

/* ── SHARE CARD ── */
.share-section { padding: clamp(60px,8vh,100px) clamp(16px,4vw,48px); max-width: 600px; margin: 0 auto; }
.share-card {
  background: linear-gradient(135deg,var(--bg3) 0%,rgba(124,58,237,.12) 50%,rgba(236,72,153,.08) 100%);
  border: 1px solid rgba(124,58,237,.2); border-radius: var(--radius-xl);
  padding: 32px; text-align: center; position: relative; overflow: hidden;
}
.share-card::before { content:''; position:absolute; inset:0; background:radial-gradient(ellipse 80% 60% at 50% 0%,rgba(124,58,237,.15),transparent); }
.share-logo { font-size: 14px; font-weight: 800; letter-spacing: -.3px; color: var(--text3); margin-bottom: 20px; position: relative; }
.share-score-num { font-size: clamp(64px,12vw,96px); font-weight: 900; letter-spacing: -4px; background: linear-gradient(135deg,var(--violet-light),var(--pink-light)); -webkit-background-clip:text; -webkit-text-fill-color:transparent; line-height: 1; position: relative; }
.share-score-sub { font-size: 16px; color: var(--text2); margin: 8px 0 20px; position: relative; }
.share-tiles { display: flex; gap: 6px; justify-content: center; margin-bottom: 24px; flex-wrap: wrap; position: relative; }
.share-tile { width: 36px; height: 36px; border-radius: 8px; display: flex; align-items: center; justify-content: center; font-family: 'JetBrains Mono', monospace; font-size: 16px; font-weight: 700; }
.st-correct { background: rgba(34,197,94,.2); border: 1.5px solid rgba(34,197,94,.4); color: var(--green); }
.st-wrong { background: rgba(239,68,68,.12); border: 1.5px solid rgba(239,68,68,.25); color: var(--red); }
.share-btns { display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; position: relative; }
.share-btn {
  padding: 11px 24px; border-radius: var(--radius); font-size: 14px; font-weight: 700;
  display: flex; align-items: center; gap: 8px; transition: all .2s;
}
.share-btn.twitter { background: #000; color: #fff; border: 1px solid rgba(255,255,255,.1); }
.share-btn.twitter:hover { background: #111; transform: translateY(-1px); }
.share-btn.tiktok { background: linear-gradient(135deg,#ff0050,#000); color: #fff; border: none; }
.share-btn.tiktok:hover { transform: translateY(-1px); box-shadow: 0 8px 20px rgba(255,0,80,.3); }
.share-btn.copy { background: rgba(255,255,255,.06); color: var(--text); border: 1px solid var(--card-border); }
.share-btn.copy:hover { background: rgba(255,255,255,.1); }



/* ── FOOTER ── */
footer { background: var(--bg2); border-top: 1px solid var(--card-border); padding: clamp(40px,6vw,80px) clamp(16px,4vw,48px) clamp(24px,4vw,40px); }
.footer-inner { max-width: 1100px; margin: 0 auto; display: grid; grid-template-columns: 2fr 1fr 1fr 1fr; gap: 40px; padding-bottom: 32px; border-bottom: 1px solid var(--card-border); margin-bottom: 24px; }
.footer-brand-desc { font-size: 14px; color: var(--text2); line-height: 1.7; margin-top: 12px; max-width: 280px; }
.footer-col h4 { font-size: 12px; font-weight: 700; letter-spacing: 2px; text-transform: uppercase; color: var(--text3); margin-bottom: 14px; }
.footer-col a { display: block; font-size: 13px; color: var(--text2); margin-bottom: 8px; transition: color .2s; cursor: pointer; }
.footer-col a:hover { color: var(--text); }
.footer-bottom { max-width: 1100px; margin: 0 auto; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px; font-size: 13px; color: var(--text3); }
.footer-socials { display: flex; gap: 8px; }
.soc-btn { width: 34px; height: 34px; border-radius: 8px; border: 1px solid var(--card-border); display: flex; align-items: center; justify-content: center; font-size: 14px; color: var(--text2); transition: all .2s; cursor: pointer; }
.soc-btn:hover { border-color: var(--violet-light); color: var(--violet-light); background: var(--violet-glow); }

/* ── NOTIFICATION TOAST ── */
.toast {
  position: fixed; bottom: 24px; right: 24px; z-index: 2000;
  background: var(--bg3); border: 1px solid var(--card-border);
  border-radius: var(--radius); padding: 14px 20px;
  font-size: 14px; font-weight: 600;
  transform: translateX(120%); opacity: 0;
  transition: all .35s cubic-bezier(.34,1.56,.64,1);
  display: flex; align-items: center; gap: 10px;
  max-width: 300px; box-shadow: 0 12px 40px rgba(0,0,0,.5);
}
.toast.show { transform: translateX(0); opacity: 1; }
.toast.success { border-color: rgba(34,197,94,.3); }
.toast.error { border-color: rgba(239,68,68,.3); }
.toast-icon { font-size: 18px; }

/* ── RESPONSIVE ── */
@media(max-width:900px) {
  .nav-links { display: none; }
  .nav-actions .btn-ghost { display: none; }
  .hamburger { display: flex; }
  .footer-inner { grid-template-columns: 1fr 1fr; }

}
@media(max-width:600px) {
  .hero-stats { gap: 20px; }
  .game-top { flex-direction: column; align-items: flex-start; }
  .footer-inner { grid-template-columns: 1fr; gap: 24px; }
  .footer-col:nth-child(3),.footer-col:nth-child(4) { display: none; }
.lb-header, .lb-row { grid-template-columns: 36px 1fr 70px; }
  .lb-streak { display: none; }
}
</style>
</head>
<body>

<!-- BACKGROUND -->
<div class="bg-canvas">
  <div class="bg-mesh"></div>
  <div class="bg-grid"></div>
  <div class="noise"></div>
</div>

<!-- FLOATING LETTERS (JS generated) -->
<div id="floating-letters"></div>

<!-- MOBILE MENU -->
<div class="mob-menu" id="mobMenu">
  <a onclick="showPage('home');closeMob()" id="mml-home" class="active">🏠 Home</a>
  <a onclick="showPage('game');closeMob()" id="mml-game">🎮 Play</a>
  <a onclick="showPage('daily');closeMob()" id="mml-daily">📅 Daily Challenge</a>
  <a onclick="showPage('leaderboard');closeMob()" id="mml-leaderboard">🏆 Leaderboard</a>
  <a onclick="showPage('ai');closeMob()" id="mml-ai">🤖 AI Generator</a>
</div>

<div class="layer">
<!-- NAV -->
<nav>
  <div class="nav-logo" onclick="showPage('home')">
    <div class="logo-icon">WS</div>
    <div class="logo-text">Word<span>Shuffle</span></div>
  </div>
  <div class="nav-links">
    <a onclick="showPage('home')" id="nl-home" class="active">Home</a>
    <a onclick="showPage('game')" id="nl-game">Play</a>
    <a onclick="showPage('daily')" id="nl-daily">Daily</a>
    <a onclick="showPage('leaderboard')" id="nl-leaderboard">Leaderboard</a>
    <a onclick="showPage('ai')" id="nl-ai">AI</a>
  </div>
  <div class="nav-actions">
    <button class="btn-ghost" onclick="showPage('leaderboard')">👤 @WordMaster</button>
    <button class="btn-primary" onclick="showPage('game')">Play Now</button>
    <div class="hamburger" id="hamburger" onclick="toggleMob()">
      <span></span><span></span><span></span>
    </div>
  </div>
</nav>

<!-- ══ HOME PAGE ══ -->
<div id="page-home" class="page active">
  <section class="hero layer">
    <div class="hero-eyebrow">
      <span class="live-dot"></span>
      12,847 players online now
    </div>
    <h1 class="hero-title">
      <span class="word-grad">Unscramble.</span><br>
      Compete.<br>
      <span class="word-grad">Dominate.</span>
    </h1>
    <p class="hero-subtitle">
      The addictive word game that combines lightning-fast puzzle solving with social competition, XP rewards, and daily challenges that keep you coming back for more.
    </p>
    <div class="hero-cta">
      <button class="cta-primary" onclick="showPage('game')">⚡ Play Now — It's Free</button>
      <button class="cta-secondary" onclick="showPage('daily')">📅 Daily Challenge</button>
    </div>

    <!-- DEMO ANIMATION -->
    <div class="hero-scramble-demo" id="heroDemo">
      <div class="demo-word" id="demoScrambled"></div>
      <div class="demo-arrow">→</div>
      <div class="demo-word" id="demoAnswer"></div>
    </div>

    <div class="hero-stats">
      <div class="hero-stat"><span class="hero-stat-num" id="cntPlayers">2.4M+</span><span class="hero-stat-label">Players Worldwide</span></div>
      <div class="hero-stat"><span class="hero-stat-num" id="cntGames">847M+</span><span class="hero-stat-label">Games Played</span></div>
      <div class="hero-stat"><span class="hero-stat-num">4.9 ★</span><span class="hero-stat-label">App Store Rating</span></div>
      <div class="hero-stat"><span class="hero-stat-num">#1</span><span class="hero-stat-label">Word Game App</span></div>
    </div>
  </section>

  <!-- FEATURES -->
  <section class="features-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">Why Players Love It</div>
      <h2 class="sec-title">Everything a word game <span>should be</span></h2>
      <p class="sec-sub">Fast, addictive, competitive, and packed with features to keep you coming back every single day.</p>
    </div>
    <div class="features-grid">
      <div class="feature-card v">
        <div class="feature-icon">⚡</div>
        <div class="feature-title">Lightning Fast Gameplay</div>
        <div class="feature-desc">Race against the clock with instant feedback, satisfying animations, and combo multipliers that reward speed AND accuracy.</div>
        <div class="feature-tags"><span class="ftag">⏱ Timer Mode</span><span class="ftag">🔥 Combos</span><span class="ftag">✨ XP Rewards</span></div>
      </div>
      <div class="feature-card c">
        <div class="feature-icon">🤖</div>
        <div class="feature-title">AI-Powered Word Packs</div>
        <div class="feature-desc">Claude AI generates fresh themed packs daily — gaming slang, movie titles, internet memes, and impossible mode for true word nerds.</div>
        <div class="feature-tags"><span class="ftag">🎮 Gaming Mode</span><span class="ftag">🎬 Movie Mode</span><span class="ftag">🔥 Meme Mode</span></div>
      </div>
      <div class="feature-card g">
        <div class="feature-icon">🏆</div>
        <div class="feature-title">Daily Global Competition</div>
        <div class="feature-desc">Every day brings a new themed challenge. Race against 2.4M players worldwide for the top spot on the global leaderboard.</div>
        <div class="feature-tags"><span class="ftag">🌍 Global LB</span><span class="ftag">🔥 Streaks</span><span class="ftag">🎖️ Badges</span></div>
      </div>
      <div class="feature-card v">
        <div class="feature-icon">📈</div>
        <div class="feature-title">Deep Progression System</div>
        <div class="feature-desc">Level up through 100 tiers, unlock rare badges, collect themed skins, and earn seasonal rewards. Progression that actually feels meaningful.</div>
        <div class="feature-tags"><span class="ftag">💎 100 Levels</span><span class="ftag">🎨 Themes</span><span class="ftag">🌟 Seasons</span></div>
      </div>
      <div class="feature-card c">
        <div class="feature-icon">📲</div>
        <div class="feature-title">Share & Go Viral</div>
        <div class="feature-desc">One-tap TikTok-style score cards. Show off your 30-word streak or your 0.4s record solve. Make your followers jealous.</div>
        <div class="feature-tags"><span class="ftag">🎵 TikTok Cards</span><span class="ftag">𝕏 X.com</span><span class="ftag">🔗 Link Share</span></div>
      </div>
    </div>
  </section>

  <footer>
    <div class="footer-inner">
      <div>
        <div class="nav-logo" style="margin-bottom:0">
          <div class="logo-icon">WS</div>
          <div class="logo-text">Word<span>Shuffle</span></div>
        </div>
        <p class="footer-brand-desc">The world's most addictive word scramble game. Unscramble. Compete. Dominate.</p>
        <div class="footer-socials" style="margin-top:16px;">
          <div class="soc-btn">𝕏</div><div class="soc-btn">📱</div><div class="soc-btn">▶</div><div class="soc-btn">in</div>
        </div>
      </div>
      <div class="footer-col"><h4>Game</h4><a onclick="showPage('game')">Play Now</a><a onclick="showPage('daily')">Daily Challenge</a><a onclick="showPage('leaderboard')">Leaderboard</a></div>
      <div class="footer-col"><h4>Features</h4><a onclick="showPage('ai')">AI Generator</a><a>Word Packs</a><a>Custom Puzzles</a><a>Seasonal Events</a></div>
      <div class="footer-col"><h4>Company</h4><a>About</a><a>Blog</a><a>Careers</a><a>Contact</a></div>
    </div>
    <div class="footer-bottom">
      <span>© 2026 WordShuffle Inc. All rights reserved.</span>
      <span>🇿🇦 Made with love for word nerds everywhere</span>
    </div>
  </footer>
</div>

<!-- ══ GAME PAGE ══ -->
<div id="page-game" class="page">
  <div class="game-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">Main Game</div>
      <h2 class="sec-title">Unscramble the <span>Word</span></h2>
    </div>
    <div class="diff-tabs" id="diffTabs">
      <button class="diff-tab active" onclick="setDiff('easy',this)">🟢 Easy</button>
      <button class="diff-tab" onclick="setDiff('medium',this)">🟡 Medium</button>
      <button class="diff-tab" onclick="setDiff('hard',this)">🔴 Hard</button>
      <button class="diff-tab" onclick="setDiff('impossible',this)">💀 Impossible</button>
    </div>

    <div class="game-card">
      <div class="game-top">
        <div class="game-info">
          <div class="info-chip highlight">
            <span class="icon">⚡</span>
            <span>Score: <strong id="scoreDisplay">0</strong></span>
          </div>
          <div class="info-chip">
            <span class="icon">🔥</span>
            <span>Streak: <strong id="streakDisplay">0</strong></span>
          </div>
          <div class="info-chip">
            <span class="icon">⭐</span>
            <span>Combo: <strong id="comboDisplay">x1</strong></span>
          </div>
          <div class="info-chip">
            <span class="icon">❓</span>
            <span>Hints: <strong id="hintsDisplay">3</strong></span>
          </div>
        </div>
        <div class="timer-ring">
          <svg width="52" height="52" viewBox="0 0 52 52">
            <circle class="timer-bg" cx="26" cy="26" r="22" />
            <circle class="timer-arc" id="timerArc" cx="26" cy="26" r="22" stroke-dasharray="138" stroke-dashoffset="0" />
          </svg>
          <div class="timer-text" id="timerText">30</div>
        </div>
      </div>

      <div class="game-body">
        <div class="category-badge" id="categoryBadge">🌍 General Knowledge</div>

        <div class="scrambled-display">
          <div class="scrambled-label">Scrambled Word — click letters to solve</div>
          <div class="scrambled-letters" id="scrambledLetters"></div>
        </div>

        <div class="hint-text" id="hintText"></div>

        <div class="answer-row">
          <div class="answer-label">Your Answer</div>
          <div class="answer-slots" id="answerSlots"></div>
        </div>

        <div class="game-input-row">
          <input type="text" class="game-input" id="gameInput" placeholder="TYPE YOUR ANSWER..." maxlength="20" autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false">
        </div>

        <div class="game-actions">
          <button class="btn-game btn-submit" onclick="submitAnswer()">✓ Submit</button>
          <button class="btn-game btn-hint" onclick="useHint()">💡 Hint</button>
          <button class="btn-game btn-skip" onclick="skipWord()">⟩ Skip</button>
        </div>
      </div>

      <div class="xp-bar-wrap">
        <div class="xp-row">
          <span class="xp-label">⭐ Level 7 — Word Hunter</span>
          <span class="xp-val" id="xpVal">3,240 / 5,000 XP</span>
        </div>
        <div class="xp-bar-bg"><div class="xp-bar-fill" id="xpBarFill" style="width:65%"></div></div>
      </div>
    </div>
  </div>
</div>

<!-- ══ DAILY CHALLENGE ══ -->
<div id="page-daily" class="page">
  <div class="dc-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">Daily Challenge</div>
      <h2 class="sec-title">Today's <span>Puzzle Set</span></h2>
      <p class="sec-sub">A new theme every day. Compete globally. Your streak resets at midnight.</p>
    </div>

    <div class="dc-header-card">
      <div class="dc-theme">🎬 TODAY'S THEME</div>
      <div class="dc-title">Classic Hollywood Cinema</div>
      <div class="dc-sub">Famous movie titles, directors, and iconic characters — scrambled just for you.</div>
      <div class="dc-meta">
        <div class="dc-meta-item">🌍 <span class="val">2,847,391</span> players attempted</div>
        <div class="dc-meta-item">✅ <span class="val">68.2%</span> completion rate</div>
        <div class="dc-meta-item">⚡ <span class="val">14.3s</span> fastest solve</div>
        <div class="dc-meta-item">🔥 <span class="val">Day 47</span> global streak record</div>
      </div>
      <div class="countdown-badge">
        <div class="countdown-label">Resets in</div>
        <div class="countdown-time" id="countdownTimer">08:42:17</div>
      </div>
    </div>

    <div class="dc-puzzles" id="dcPuzzles">
      <div class="dc-puzzle" id="dcp-0">
        <div class="dc-puzzle-left">
          <div class="dc-puzzle-num">01</div>
          <div>
            <div class="dc-puzzle-word" id="dcp0word">TNEMTRAPED</div>
            <div style="font-size:12px;color:var(--text3);margin-top:3px;">8 letters · 🎬 Movie Title</div>
          </div>
        </div>
        <div class="dc-puzzle-right">
          <span class="dc-puzzle-diff diff-easy">Easy</span>
          <button class="dc-solve-btn" onclick="playDCPuzzle(0)">Solve →</button>
        </div>
      </div>
      <div class="dc-puzzle" id="dcp-1">
        <div class="dc-puzzle-left">
          <div class="dc-puzzle-num">02</div>
          <div>
            <div class="dc-puzzle-word" id="dcp1word">DNOIRPHAC</div>
            <div style="font-size:12px;color:var(--text3);margin-top:3px;">9 letters · 🎬 Director</div>
          </div>
        </div>
        <div class="dc-puzzle-right">
          <span class="dc-puzzle-diff diff-med">Medium</span>
          <button class="dc-solve-btn" onclick="playDCPuzzle(1)">Solve →</button>
        </div>
      </div>
      <div class="dc-puzzle solved" id="dcp-2">
        <div class="dc-puzzle-left">
          <div class="dc-puzzle-num">03</div>
          <div>
            <div class="dc-puzzle-word" id="dcp2word">SEHSTNARG</div>
            <div style="font-size:12px;color:var(--text3);margin-top:3px;">9 letters · 🎬 Character</div>
          </div>
        </div>
        <div class="dc-puzzle-right">
          <span class="dc-puzzle-diff diff-hard">Hard</span>
          <div class="solved-check">✓</div>
        </div>
      </div>
      <div class="dc-puzzle" id="dcp-3">
        <div class="dc-puzzle-left">
          <div class="dc-puzzle-num">04</div>
          <div>
            <div class="dc-puzzle-word" id="dcp3word">NOINCITEP</div>
            <div style="font-size:12px;color:var(--text3);margin-top:3px;">9 letters · 🎬 Movie Title</div>
          </div>
        </div>
        <div class="dc-puzzle-right">
          <span class="dc-puzzle-diff diff-imp">Impossible</span>
          <button class="dc-solve-btn" onclick="playDCPuzzle(3)">Solve →</button>
        </div>
      </div>
    </div>

    <div style="margin-top:28px;background:var(--bg2);border:1px solid var(--card-border);border-radius:var(--radius-xl);padding:24px;">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:20px;flex-wrap:wrap;gap:12px;">
        <div style="font-size:18px;font-weight:800;">Today's Leaders</div>
        <div style="font-size:13px;color:var(--text2);">Updated every 60s</div>
      </div>
      <div class="lb-header">
        <div style="text-align:center;">Rank</div>
        <div>Player</div>
        <div style="text-align:right;">Score</div>
        <div style="text-align:right;">Time</div>
      </div>
      <div class="lb-row">
        <div class="lb-medal">🥇</div>
        <div class="lb-player">
          <div class="lb-avatar" style="background:linear-gradient(135deg,#ffd700,#ff8c00);color:#fff;">SK</div>
          <div><div class="lb-name">SpeedKing_ZA</div><div class="lb-badge streak">47 🔥</div></div>
        </div>
        <div class="lb-score">12,450</div>
        <div class="lb-streak">14.3s ⚡</div>
      </div>
      <div class="lb-row">
        <div class="lb-medal">🥈</div>
        <div class="lb-player">
          <div class="lb-avatar" style="background:linear-gradient(135deg,#c0c0c0,#808080);color:#fff;">LW</div>
          <div><div class="lb-name">LexiWizard</div><div class="lb-badge pro">PRO</div></div>
        </div>
        <div class="lb-score">11,820</div>
        <div class="lb-streak">15.7s ⚡</div>
      </div>
      <div class="lb-row me">
        <div class="lb-rank" style="color:var(--violet-light);">247</div>
        <div class="lb-player">
          <div class="lb-avatar" style="background:linear-gradient(135deg,var(--violet),var(--pink));color:#fff;">WM</div>
          <div><div class="lb-name">WordMaster (You)</div></div>
        </div>
        <div class="lb-score">4,200</div>
        <div class="lb-streak">34.1s</div>
      </div>
    </div>
  </div>
</div>


<!-- ══ LEADERBOARD ══ -->
<div id="page-leaderboard" class="page">
  <div class="lb-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">Global Rankings</div>
      <h2 class="sec-title">Who's <span>Dominating</span></h2>
    </div>
    <div class="lb-tabs">
      <button class="lb-tab active" onclick="switchLB('global',this)">🌍 Global</button>
      <button class="lb-tab" onclick="switchLB('daily',this)">📅 Daily</button>
      <button class="lb-tab" onclick="switchLB('weekly',this)">📆 Weekly</button>
      <button class="lb-tab" onclick="switchLB('friends',this)">👥 Friends</button>
    </div>
    <div class="lb-card">
      <div class="lb-header">
        <div style="text-align:center;">Rank</div>
        <div>Player</div>
        <div style="text-align:right;">Score</div>
        <div style="text-align:right;">Streak</div>
      </div>
      <div id="lbRows"></div>
    </div>

    <!-- PROGRESS -->
    <div style="margin-top:32px;">
      <div class="sec-hdr" style="margin-bottom:20px;text-align:left;padding:0;">
        <div class="sec-eyebrow">Your Profile</div>
        <h3 style="font-size:22px;font-weight:800;letter-spacing:-0.5px;">WordMaster — Level 7</h3>
      </div>
      <div class="xp-card">
        <div class="xp-card-top">
          <div class="level-circle">
            <div class="level-num">7</div>
            <div class="level-word">Level</div>
          </div>
          <div class="xp-info">
            <div class="xp-title">Word Hunter</div>
            <div class="xp-sub">1,760 XP until Level 8 — <em>Word Wizard</em></div>
            <div class="xp-bigbar-bg"><div class="xp-bigbar-fill"></div></div>
            <div class="xp-bigbar-label"><span>3,240 XP</span><span>5,000 XP</span></div>
          </div>
        </div>
        <div class="stats-row">
          <div class="stat-card"><div class="stat-card-num" style="color:var(--violet-light)">1,247</div><div class="stat-card-label">Words Solved</div></div>
          <div class="stat-card"><div class="stat-card-num" style="color:var(--orange)">23 🔥</div><div class="stat-card-label">Day Streak</div></div>
          <div class="stat-card"><div class="stat-card-num" style="color:var(--green)">94%</div><div class="stat-card-label">Accuracy</div></div>
          <div class="stat-card"><div class="stat-card-num" style="color:var(--cyan)">4.2s</div><div class="stat-card-label">Avg. Solve Time</div></div>
          <div class="stat-card"><div class="stat-card-num" style="color:var(--gold)">1,247</div><div class="stat-card-label">ELO Rating</div></div>
          <div class="stat-card"><div class="stat-card-num" style="color:var(--pink)">12</div><div class="stat-card-label">Badges Earned</div></div>
        </div>
      </div>

      <div style="margin-top:24px;">
        <div style="font-size:16px;font-weight:700;margin-bottom:16px;">Achievement Badges</div>
        <div class="badges-grid">
          <div class="badge-item"><div class="badge-icon unlocked">🔥</div><div class="badge-name">7-Day Streak</div></div>
          <div class="badge-item"><div class="badge-icon unlocked">⚡</div><div class="badge-name">Speed Solver</div></div>
          <div class="badge-item"><div class="badge-icon unlocked">🎯</div><div class="badge-name">Sharpshooter</div></div>
          <div class="badge-item"><div class="badge-icon unlocked">🧠</div><div class="badge-name">Big Brain</div></div>
          <div class="badge-item"><div class="badge-icon unlocked">🏆</div><div class="badge-name">Top 1000</div></div>
          <div class="badge-item"><div class="badge-icon unlocked">💎</div><div class="badge-name">50 Dailies</div></div>
          <div class="badge-item"><div class="badge-icon locked">👑</div><div class="badge-name">Champion</div></div>
          <div class="badge-item"><div class="badge-icon locked">🌟</div><div class="badge-name">Legend</div></div>
          <div class="badge-item"><div class="badge-icon locked">💀</div><div class="badge-name">Impossible</div></div>
          <div class="badge-item"><div class="badge-icon locked">🌈</div><div class="badge-name">All Modes</div></div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ══ AI GENERATOR ══ -->
<div id="page-ai" class="page">
  <div class="ai-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">AI-Powered</div>
      <h2 class="sec-title">Word Pack <span>Generator</span></h2>
      <p class="sec-sub">Claude AI dynamically creates themed word packs on demand. Pick a mode, generate, and play.</p>
    </div>
    <div class="ai-card">
      <div class="ai-top">
        <div>
          <div style="font-size:18px;font-weight:800;margin-bottom:4px;">Generate Custom Word Pack</div>
          <div style="font-size:13px;color:var(--text2);">AI creates scrambled challenges based on your chosen theme</div>
        </div>
        <div class="ai-badge"><span class="ai-dot"></span>Claude AI Active</div>
      </div>
      <div class="ai-body">
        <div style="font-size:13px;font-weight:600;color:var(--text2);letter-spacing:1px;text-transform:uppercase;margin-bottom:12px;">Choose Theme</div>
        <div class="mode-grid" id="modeGrid">
          <div class="mode-btn active" onclick="selectMode(this,'🎮 Gaming')"><span class="mode-emoji">🎮</span><span class="mode-name">Gaming</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🎬 Movies')"><span class="mode-emoji">🎬</span><span class="mode-name">Movies</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🐦 Memes & Internet')"><span class="mode-emoji">🐦</span><span class="mode-name">Memes</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🌍 Geography')"><span class="mode-emoji">🌍</span><span class="mode-name">Geography</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🔬 Science')"><span class="mode-emoji">🔬</span><span class="mode-name">Science</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🍕 Food & Cooking')"><span class="mode-emoji">🍕</span><span class="mode-name">Food</span></div>
          <div class="mode-btn" onclick="selectMode(this,'🏆 Sports')"><span class="mode-emoji">🏆</span><span class="mode-name">Sports</span></div>
          <div class="mode-btn" onclick="selectMode(this,'💀 Impossible Mode')"><span class="mode-emoji">💀</span><span class="mode-name">Impossible</span></div>
        </div>
        <div style="margin-bottom:16px;">
          <div style="font-size:13px;font-weight:600;color:var(--text2);letter-spacing:1px;text-transform:uppercase;margin-bottom:10px;">Difficulty</div>
          <div class="diff-tabs" style="max-width:400px;">
            <button class="diff-tab active" onclick="setAIDiff('Mixed',this)">🎲 Mixed</button>
            <button class="diff-tab" onclick="setAIDiff('Easy',this)">🟢 Easy</button>
            <button class="diff-tab" onclick="setAIDiff('Hard',this)">🔴 Hard</button>
          </div>
        </div>
        <button class="ai-generate-btn" id="aiGenBtn" onclick="generateAIWords()">✦ Generate AI Word Pack</button>
        <div class="ai-output" id="aiOutput"></div>
      </div>
    </div>
  </div>
</div>

<!-- ══ SHARE CARD ══ -->
<div id="page-share" class="page">
  <div class="share-section">
    <div class="sec-hdr">
      <div class="sec-eyebrow">Share Your Score</div>
      <h2 class="sec-title">Go <span>Viral</span></h2>
    </div>
    <div class="share-card" id="shareCard">
      <div class="share-logo">WordShuffle</div>
      <div class="share-score-num" id="finalScore">4,850</div>
      <div class="share-score-sub">🔥 23-word streak · 94% accuracy · Level 7</div>
      <div class="share-tiles">
        <div class="share-tile st-correct">A</div>
        <div class="share-tile st-correct">P</div>
        <div class="share-tile st-wrong">X</div>
        <div class="share-tile st-correct">P</div>
        <div class="share-tile st-correct">L</div>
        <div class="share-tile st-correct">E</div>
      </div>
      <div style="font-size:13px;color:var(--text2);margin-bottom:16px;position:relative;">Can you beat my score? wordshuffle.io</div>
      <div class="share-btns">
        <button class="share-btn twitter" onclick="showToast('📤 Shared to X!','success')">𝕏 Share</button>
        <button class="share-btn tiktok" onclick="showToast('🎵 TikTok card copied!','success')">🎵 TikTok</button>
        <button class="share-btn copy" onclick="copyScore()">📋 Copy</button>
      </div>
    </div>
  </div>
</div>


</div><!-- end .layer -->

<!-- COMBO POPUP -->
<div class="combo-popup" id="comboPopup">🔥 COMBO!</div>

<!-- TOAST -->
<div class="toast" id="toast"><span class="toast-icon" id="toastIcon"></span><span id="toastMsg"></span></div>

<script>
// ════════════════════════════════════════
// FLOATING LETTERS
// ════════════════════════════════════════
const LETTERS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
const COLORS = ['rgba(124,58,237,.7)','rgba(236,72,153,.7)','rgba(6,182,212,.6)','rgba(132,204,22,.5)','rgba(249,115,22,.6)'];
const container = document.getElementById('floating-letters');
for(let i=0;i<20;i++){
  const el = document.createElement('div');
  el.className = 'floating-letter';
  el.textContent = LETTERS[Math.floor(Math.random()*26)];
  el.style.cssText = `left:${Math.random()*100}%;color:${COLORS[Math.floor(Math.random()*COLORS.length)]};animation-duration:${8+Math.random()*12}s;animation-delay:${-Math.random()*15}s;font-size:${40+Math.random()*80}px;`;
  container.appendChild(el);
}

// ════════════════════════════════════════
// NAVIGATION
// ════════════════════════════════════════
const PAGES = ['home','game','daily','leaderboard','ai','share'];
function showPage(n){
  PAGES.forEach(p=>{
    const pg=document.getElementById('page-'+p); if(pg) pg.classList.remove('active');
    const nl=document.getElementById('nl-'+p); if(nl) nl.classList.remove('active');
    const ml=document.getElementById('mml-'+p); if(ml) ml.classList.remove('active');
  });
  const pg=document.getElementById('page-'+n); if(pg) pg.classList.add('active');
  const nl=document.getElementById('nl-'+n); if(nl) nl.classList.add('active');
  const ml=document.getElementById('mml-'+n); if(ml) ml.classList.add('active');
  window.scrollTo(0,0);
  if(n==='game') initGame();
  if(n==='leaderboard') renderLB('global');
}
function toggleMob(){ document.getElementById('mobMenu').classList.toggle('open'); document.getElementById('hamburger').classList.toggle('open'); }
function closeMob(){ document.getElementById('mobMenu').classList.remove('open'); document.getElementById('hamburger').classList.remove('open'); }

// ════════════════════════════════════════
// HERO DEMO ANIMATION
// ════════════════════════════════════════
const demoWords=[{scr:'LPAEP',ans:'APPLE'},{scr:'RETUPMOC',ans:'COMPUTER'},{scr:'NRETTENI',ans:'INTERNET'},{scr:'LDRLAEOB',ans:'LABRADOR'},{scr:'ENODRTHU',ans:'THUNDERO'}];
let demoIdx=0;
function buildDemoLetters(word, container, isAnswer){
  container.innerHTML='';
  [...word].forEach((ch,i)=>{
    const el=document.createElement('div');
    el.className='demo-letter'+(isAnswer?' correct':'');
    el.textContent=ch;
    el.style.animationDelay=i*.05+'s';
    container.appendChild(el);
  });
}
function runDemo(){
  const w=demoWords[demoIdx%demoWords.length]; demoIdx++;
  buildDemoLetters(w.scr, document.getElementById('demoScrambled'),false);
  const ansEl=document.getElementById('demoAnswer');
  ansEl.innerHTML='';
  // Reveal answer letter by letter
  let i=0;
  const iv=setInterval(()=>{
    if(i>=w.ans.length){clearInterval(iv);setTimeout(runDemo,2000);return;}
    const el=document.createElement('div');
    el.className='demo-letter correct active';
    el.textContent=w.ans[i];
    ansEl.appendChild(el);
    setTimeout(()=>el.classList.remove('active'),300);
    i++;
  },200);
}
setTimeout(runDemo,800);

// ════════════════════════════════════════
// GAME ENGINE
// ════════════════════════════════════════
const WORD_PACKS = {
  easy:   [{w:'APPLE',cat:'🍎 Food',hint:'A fruit a day...'},{w:'HOUSE',cat:'🏠 Places',hint:'A place to live'},{w:'MONEY',cat:'💰 Finance',hint:'Makes the world go round'},{w:'MUSIC',cat:'🎵 Arts',hint:'Sounds that move you'},{w:'WATER',cat:'💧 Nature',hint:'H2O'},{w:'BREAD',cat:'🍞 Food',hint:'Baked grain loaf'},{w:'LIGHT',cat:'💡 Science',hint:'What a bulb produces'},{w:'EARTH',cat:'🌍 Nature',hint:'Our home planet'},{w:'CHESS',cat:'♟️ Games',hint:'Ancient strategy game'},{w:'CLOUD',cat:'☁️ Nature',hint:'White fluffy thing in sky'}],
  medium: [{w:'PLANET',cat:'🌍 Science',hint:'Orbits a star'},{w:'BRIDGE',cat:'🌉 Places',hint:'Crosses a river'},{w:'GUITAR',cat:'🎸 Music',hint:'Stringed instrument'},{w:'MARKET',cat:'💼 Business',hint:'Where trades happen'},{w:'POCKET',cat:'👖 Clothing',hint:'Part of trousers'},{w:'ROCKET',cat:'🚀 Space',hint:'Launches into orbit'},{w:'MIRROR',cat:'🪞 Objects',hint:'Reflects your image'},{w:'COFFEE',cat:'☕ Food',hint:'Morning energy boost'},{w:'JUNGLE',cat:'🌴 Nature',hint:'Dense tropical forest'},{w:'CASTLE',cat:'🏰 History',hint:'Medieval fortress'}],
  hard:   [{w:'CRYSTAL',cat:'💎 Science',hint:'Structured mineral'},{w:'ANCIENT',cat:'🏛️ History',hint:'Very, very old'},{w:'GRAVITY',cat:'🌍 Physics',hint:'Keeps you on Earth'},{w:'PHANTOM',cat:'👻 Mythology',hint:'A ghostly apparition'},{w:'QUANTUM',cat:'⚛️ Physics',hint:'Subatomic realm'},{w:'MONARCH',cat:'👑 Politics',hint:'A king or queen'},{w:'VOLCANO',cat:'🌋 Nature',hint:'Erupts with lava'},{w:'SYMPTOM',cat:'🏥 Medicine',hint:'Sign of illness'},{w:'TEXTURE',cat:'🎨 Art',hint:'Surface feel or look'},{w:'WHISPER',cat:'💬 Language',hint:'Very soft speech'}],
  impossible:[{w:'ONOMATOPOEIA',cat:'📚 Language',hint:'A word that sounds like what it describes'},{w:'PHENOMENON',cat:'🔬 Science',hint:'An observable occurrence'},{w:'ARCHIPELAGO',cat:'🌏 Geography',hint:'A chain of islands'},{w:'METAMORPHIC',cat:'🪨 Geology',hint:'Type of rock formed by heat/pressure'},{w:'SERENDIPITY',cat:'✨ Concepts',hint:'Happy accident'},{w:'CLANDESTINE',cat:'🕵️ Language',hint:'Kept secret or hidden'},{w:'BELLIGERENT',cat:'⚔️ Language',hint:'Aggressively hostile'},{w:'HALCYON',cat:'🌅 Language',hint:'Idyllically happy and peaceful'}]
};
let gameState = { diff:'easy', word:'', scrambled:'', score:0, streak:0, combo:1, xp:3240, hints:3, timer:30, timerMax:30, timerInterval:null, selectedLetters:[], usedIndices:[] };

function setDiff(d, el){
  document.querySelectorAll('.diff-tab').forEach(t=>t.classList.remove('active'));
  el.classList.add('active');
  gameState.diff = d;
  gameState.timerMax = d==='easy'?45:d==='medium'?30:d==='hard'?20:15;
  loadNewWord();
}

function scrambleWord(w){
  let arr=[...w]; let sc;
  do { sc=[...arr].sort(()=>Math.random()-.5).join(''); } while(sc===w && w.length>2);
  return sc;
}

function loadNewWord(){
  const pack = WORD_PACKS[gameState.diff];
  const entry = pack[Math.floor(Math.random()*pack.length)];
  gameState.word = entry.w;
  gameState.scrambled = scrambleWord(entry.w);
  gameState.selectedLetters = [];
  gameState.usedIndices = [];
  document.getElementById('categoryBadge').textContent = entry.cat;
  document.getElementById('hintText').textContent = '💡 ' + entry.hint;
  document.getElementById('hintText').classList.remove('visible');
  document.getElementById('gameInput').value = '';
  document.getElementById('gameInput').className = 'game-input';
  renderScrambledLetters();
  renderAnswerSlots();
  resetTimer();
}

function renderScrambledLetters(){
  const c = document.getElementById('scrambledLetters'); c.innerHTML = '';
  [...gameState.scrambled].forEach((ch,i)=>{
    const el = document.createElement('div');
    el.className = 'scrambled-letter' + (gameState.usedIndices.includes(i)?' used':'');
    el.textContent = ch;
    el.dataset.idx = i;
    el.onclick = () => clickLetter(i, ch);
    c.appendChild(el);
  });
}

function renderAnswerSlots(){
  const c = document.getElementById('answerSlots'); c.innerHTML = '';
  const len = gameState.word.length;
  gameState.selectedLetters.forEach((ch,i)=>{
    const el = document.createElement('div');
    el.className = 'answer-slot filled';
    el.textContent = ch;
    el.dataset.slotIdx = i;
    el.onclick = () => removeFromSlot(i);
    c.appendChild(el);
  });
  for(let i=gameState.selectedLetters.length;i<len;i++){
    const el = document.createElement('div');
    el.className = 'answer-slot';
    c.appendChild(el);
  }
}

function clickLetter(idx, ch){
  if(gameState.usedIndices.includes(idx)) return;
  gameState.selectedLetters.push(ch);
  gameState.usedIndices.push(idx);
  renderScrambledLetters();
  renderAnswerSlots();
  document.getElementById('gameInput').value = gameState.selectedLetters.join('');
  if(gameState.selectedLetters.length === gameState.word.length){ submitAnswer(); }
}

function removeFromSlot(slotIdx){
  const originalIdx = gameState.usedIndices[slotIdx];
  gameState.selectedLetters.splice(slotIdx,1);
  gameState.usedIndices.splice(slotIdx,1);
  renderScrambledLetters();
  renderAnswerSlots();
  document.getElementById('gameInput').value = gameState.selectedLetters.join('');
}

function submitAnswer(){
  const inp = document.getElementById('gameInput').value.toUpperCase().trim();
  if(!inp) return;
  if(inp === gameState.word){
    correctAnswer();
  } else {
    wrongAnswer();
  }
}

function correctAnswer(){
  clearInterval(gameState.timerInterval);
  gameState.streak++;
  gameState.combo = Math.min(Math.floor(gameState.streak/3)+1, 5);
  const pts = (100 + (gameState.timerMax - gameState.timer)*2) * gameState.combo;
  gameState.score += pts;
  gameState.xp += pts;
  document.getElementById('scoreDisplay').textContent = gameState.score.toLocaleString();
  document.getElementById('streakDisplay').textContent = gameState.streak;
  document.getElementById('comboDisplay').textContent = 'x'+gameState.combo;
  // XP bar
  const xpPct = Math.min((gameState.xp / 5000)*100, 100);
  document.getElementById('xpBarFill').style.width = xpPct+'%';
  document.getElementById('xpVal').textContent = gameState.xp.toLocaleString()+' / 5,000 XP';
  // Animate answer slots correct
  document.querySelectorAll('.answer-slot').forEach(s=>s.classList.add('correct'));
  document.getElementById('gameInput').className = 'game-input success';
  if(gameState.combo>=2) showCombo('🔥 x'+gameState.combo+' COMBO!');
  if(gameState.streak===5) { showCombo('⚡ 5 STREAK!'); spawnConfetti(); }
  if(gameState.streak===10) { showCombo('💥 10 STREAK!'); spawnConfetti(); showToast('🏆 10-word streak! +500 bonus XP','success'); }
  setTimeout(()=>{ loadNewWord(); }, 700);
}

function wrongAnswer(){
  gameState.streak = 0; gameState.combo = 1;
  document.getElementById('streakDisplay').textContent = 0;
  document.getElementById('comboDisplay').textContent = 'x1';
  document.getElementById('gameInput').className = 'game-input error';
  document.querySelectorAll('.answer-slot.filled').forEach(s=>{ s.classList.add('wrong'); setTimeout(()=>s.classList.remove('wrong'),500); });
  // Clear selection
  gameState.selectedLetters = []; gameState.usedIndices = [];
  renderScrambledLetters(); renderAnswerSlots();
  document.getElementById('gameInput').value = '';
  setTimeout(()=>{ document.getElementById('gameInput').className='game-input'; },600);
}

function skipWord(){ gameState.streak=0; gameState.combo=1; document.getElementById('streakDisplay').textContent=0; document.getElementById('comboDisplay').textContent='x1'; loadNewWord(); showToast('⟩ Word skipped',''); }
function useHint(){
  if(gameState.hints<=0){showToast('❌ No hints left!','error');return;}
  gameState.hints--;
  document.getElementById('hintsDisplay').textContent=gameState.hints;
  document.getElementById('hintText').classList.add('visible');
  showToast('💡 Hint revealed! -1 hint','');
}

// TIMER
function resetTimer(){
  clearInterval(gameState.timerInterval);
  gameState.timer = gameState.timerMax;
  updateTimerUI();
  gameState.timerInterval = setInterval(()=>{
    gameState.timer--;
    updateTimerUI();
    if(gameState.timer<=0){ clearInterval(gameState.timerInterval); timeUp(); }
  },1000);
}
function updateTimerUI(){
  const t = gameState.timer; const max = gameState.timerMax;
  document.getElementById('timerText').textContent = t;
  const circ = 2*Math.PI*22;
  const offset = circ - (t/max)*circ;
  document.getElementById('timerArc').style.strokeDashoffset = offset;
  const arc = document.getElementById('timerArc');
  arc.style.stroke = t>10?'var(--violet-light)':t>5?'var(--gold)':'var(--red)';
}
function timeUp(){ wrongAnswer(); showToast("⏰ Time's up! Word was: "+gameState.word,'error'); setTimeout(loadNewWord,800); }

function initGame(){ if(!gameState.word) loadNewWord(); }

// ════════════════════════════════════════
// COMBO + CONFETTI
// ════════════════════════════════════════
function showCombo(text){
  const el=document.getElementById('comboPopup');
  el.textContent=text; el.className='combo-popup show';
  setTimeout(()=>el.className='combo-popup',900);
}
function spawnConfetti(){
  const cols=['#7c3aed','#ec4899','#06b6d4','#84cc16','#f97316','#eab308'];
  for(let i=0;i<60;i++){
    const el=document.createElement('div');
    el.className='confetti-piece';
    const size=8+Math.random()*8;
    el.style.cssText=`left:${20+Math.random()*60}%;top:-10px;width:${size}px;height:${size}px;background:${cols[Math.floor(Math.random()*cols.length)]};transform:rotate(${Math.random()*360}deg);animation-duration:${1.5+Math.random()*1.5}s;animation-delay:${Math.random()*.5}s;`;
    document.body.appendChild(el);
    setTimeout(()=>el.remove(),3500);
  }
}

// ════════════════════════════════════════
// LEADERBOARD
// ════════════════════════════════════════
const LB_DATA = [
  {rank:1,medal:'🥇',name:'SpeedKing_ZA',elo:1891,score:284750,streak:'47 🔥',badge:'streak',av:'SK',c:'linear-gradient(135deg,#ffd700,#ff8c00)'},
  {rank:2,medal:'🥈',name:'LexiWizard',elo:1845,score:271200,streak:'31 🔥',badge:'pro',av:'LW',c:'linear-gradient(135deg,#c0c0c0,#808080)'},
  {rank:3,medal:'🥉',name:'WordBeast99',elo:1790,score:258900,streak:'22 🔥',badge:'pro',av:'WB',c:'linear-gradient(135deg,#cd7f32,#8b4513)'},
  {rank:4,medal:'',name:'Quizzical_K',elo:1741,score:245100,streak:'18 🔥',badge:'',av:'QK',c:'linear-gradient(135deg,var(--violet),var(--pink))'},
  {rank:5,medal:'',name:'AlphabetSoup',elo:1698,score:231500,streak:'15 🔥',badge:'pro',av:'AS',c:'linear-gradient(135deg,var(--cyan),var(--lime))'},
  {rank:6,medal:'',name:'VocabViper',elo:1654,score:218700,streak:'9 🔥',badge:'',av:'VV',c:'linear-gradient(135deg,var(--orange),var(--red))'},
  {rank:247,medal:'',name:'WordMaster (You)',elo:1247,score:42000,streak:'23 🔥',badge:'',av:'WM',c:'linear-gradient(135deg,var(--violet),var(--pink))',me:true},
];
function renderLB(type){
  const c=document.getElementById('lbRows'); if(!c) return;
  c.innerHTML='';
  const data = type==='friends'?LB_DATA.slice(0,4):LB_DATA;
  data.forEach(p=>{
    const row=document.createElement('div');
    row.className='lb-row'+(p.me?' me':'');
    const rankClass = p.rank===1?'gold':p.rank===2?'silver':p.rank===3?'bronze':'';
    const badge = p.badge?`<span class="lb-badge ${p.badge}">${p.badge==='pro'?'PRO':'🔥 STREAK'}</span>`:'';
    row.innerHTML=`
      <div class="lb-rank ${rankClass}">${p.medal||'#'+p.rank}</div>
      <div class="lb-player">
        <div class="lb-avatar" style="background:${p.c};color:#fff;">${p.av}</div>
        <div><div class="lb-name">${p.name}</div>${badge}</div>
      </div>
      <div class="lb-score">${p.score.toLocaleString()}</div>
      <div class="lb-streak">${p.streak}</div>`;
    c.appendChild(row);
  });
}
function switchLB(type,el){
  document.querySelectorAll('.lb-tab').forEach(t=>t.classList.remove('active'));
  el.classList.add('active');
  renderLB(type);
}

// ════════════════════════════════════════
// DAILY CHALLENGE
// ════════════════════════════════════════
function playDCPuzzle(idx){
  showPage('game');
  initGame();
  showToast('📅 Daily puzzle '+( idx+1)+' loaded!','success');
}
function startCountdown(){
  function update(){
    const now=new Date(), midnight=new Date(); midnight.setHours(24,0,0,0);
    const diff=midnight-now;
    const h=Math.floor(diff/3600000), m=Math.floor((diff%3600000)/60000), s=Math.floor((diff%60000)/1000);
    const el=document.getElementById('countdownTimer');
    if(el) el.textContent=`${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
  }
  update(); setInterval(update,1000);
}
startCountdown();

// ════════════════════════════════════════
// AI WORD GENERATOR
// ════════════════════════════════════════
let selectedAIMode = '🎮 Gaming';
let selectedAIDiff = 'Mixed';
function selectMode(el, mode){
  document.querySelectorAll('.mode-btn').forEach(b=>b.classList.remove('active'));
  el.classList.add('active');
  selectedAIMode = mode;
}
function setAIDiff(d, el){
  el.closest('.diff-tabs').querySelectorAll('.diff-tab').forEach(t=>t.classList.remove('active'));
  el.classList.add('active');
  selectedAIDiff = d;
}
async function generateAIWords(){
  const btn=document.getElementById('aiGenBtn');
  const out=document.getElementById('aiOutput');
  btn.disabled=true; btn.textContent='✦ Generating...';
  out.className='ai-output visible';
  out.innerHTML='<div class="ai-loading"><div class="ai-spinner"></div>Claude AI is crafting your custom word pack...</div>';
  const prompt=`You are a word game designer. Generate 8 words for a WordShuffle game with theme: "${selectedAIMode}", difficulty: "${selectedAIDiff}".

Rules:
- Words must match the theme
- Easy: 4-5 letters, Medium: 6-7 letters, Hard: 8+ letters, Mixed: variety
- Each word must be scrambled (shuffle the letters, ensure scrambled ≠ original for words >2 letters)
- Provide a short clue (max 6 words)

Respond ONLY with valid JSON array, no markdown, no preamble:
[{"word":"EXAMPLE","scrambled":"AXEMPLE","clue":"This is a sample","difficulty":"easy"}]`;

  try{
    const res=await fetch('https://api.anthropic.com/v1/messages',{
      method:'POST',headers:{'Content-Type':'application/json'},
      body:JSON.stringify({model:'claude-sonnet-4-20250514',max_tokens:1200,messages:[{role:'user',content:prompt}]})
    });
    const data=await res.json();
    const raw=data.content.map(i=>i.text||'').join('');
    const clean=raw.replace(/```json|```/g,'').trim();
    const words=JSON.parse(clean);
    out.innerHTML=`
      <div style="font-size:13px;color:var(--text2);margin-bottom:12px;display:flex;align-items:center;gap:8px;">
        <span style="color:var(--cyan)">✦</span> ${words.length} words generated for <strong style="color:var(--text)">${selectedAIMode}</strong>
        <button onclick="useAIWordPack()" style="background:linear-gradient(135deg,var(--violet),var(--pink));color:#fff;font-size:12px;font-weight:700;padding:5px 14px;border-radius:8px;margin-left:auto;">▶ Play These Words</button>
      </div>
      <div class="ai-words">${words.map(w=>`
        <div class="ai-word" onclick="showToast('▶ Playing: '+this.dataset.word,'success')" data-word="${w.word}">
          <div class="ai-word-scrambled">${w.scrambled}</div>
          <div class="ai-word-answer">
            <div style="font-size:12px;color:var(--text3);margin-bottom:2px;">${w.difficulty.toUpperCase()}</div>
            <div style="font-weight:700;">${w.word}</div>
            <div style="font-size:12px;color:var(--text3);margin-top:2px;">${w.clue}</div>
          </div>
        </div>`).join('')}
      </div>`;
    showToast('✦ AI word pack ready! Click words to play.','success');
  }catch(err){
    out.innerHTML=`<div style="color:var(--red);padding:12px;">⚠ Could not generate words: ${err.message}</div>`;
  }
  btn.disabled=false; btn.textContent='✦ Generate AI Word Pack';
}

function useAIWordPack(){ showPage('game'); initGame(); showToast('🎮 AI word pack loaded!','success'); }

// ════════════════════════════════════════
// SHARE
// ════════════════════════════════════════
function copyScore(){
  const text='🎮 WordShuffle Score: '+gameState.score+'\n🔥 Streak: '+gameState.streak+'\n⚡ Level 7 — Word Hunter\nCan you beat me? wordshuffle.io';
  navigator.clipboard.writeText(text).then(()=>showToast('📋 Score copied!','success')).catch(()=>showToast('📋 Score ready to share!','success'));
}

// ════════════════════════════════════════
// TOAST
// ════════════════════════════════════════
let toastTimeout;
function showToast(msg, type){
  const t=document.getElementById('toast');
  const ic=document.getElementById('toastIcon');
  const mg=document.getElementById('toastMsg');
  ic.textContent = type==='success'?'✓':type==='error'?'✕':'ℹ';
  mg.textContent = msg;
  t.className='toast show '+(type||'');
  clearTimeout(toastTimeout);
  toastTimeout=setTimeout(()=>t.className='toast',3000);
}

// ════════════════════════════════════════
// ANIMATED COUNTER
// ════════════════════════════════════════
function animateCount(el, target, suffix=''){
  let current=0; const step=Math.ceil(target/60);
  const iv=setInterval(()=>{
    current=Math.min(current+step, target);
    const n=current>=1000000?(current/1000000).toFixed(1)+'M':current>=1000?(current/1000).toFixed(1)+'K':current;
    el.textContent=n+suffix;
    if(current>=target) clearInterval(iv);
  },16);
}
// Run on load
window.addEventListener('load',()=>{
  const p=document.getElementById('cntPlayers');
  const g=document.getElementById('cntGames');
  if(p) animateCount(p,2400,'M+');
  if(g) animateCount(g,847,'M+');
});

// Game input keyboard
document.getElementById('gameInput')?.addEventListener('keydown',e=>{
  if(e.key==='Enter') submitAnswer();
  if(e.key==='Escape'){ gameState.selectedLetters=[]; gameState.usedIndices=[]; renderScrambledLetters(); renderAnswerSlots(); document.getElementById('gameInput').value=''; }
});
document.getElementById('gameInput')?.addEventListener('input',e=>{
  const v=e.target.value.toUpperCase();
  e.target.value=v;
});
</script>
</body>
</html>
