# ICP Brief HTML Template

This file contains the complete CSS, color system, and structural HTML patterns for generating a brief. Use these exactly — the visual consistency across briefs is the point.

---

## Full CSS

Paste this inside `<style>` tags. Replace `--brand`, `--brand-dk`, `--brand-dim`, `--brand-bd` with the company's color.

```css
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#F5F2EB;--bg2:#fff;--bg3:#FAF8F2;--bg4:#EFEBE0;
  --line:#E2DDD0;--line2:#C8C2B0;
  --txt:#0F0E0C;--txt2:#4A4641;--txt3:#8F897A;
  /* REPLACE THESE FOUR with the company's brand color */
  --brand:#5C4EE5;--brand-dk:#4438C8;--brand-dim:rgba(92,78,229,.07);--brand-bd:rgba(92,78,229,.22);
  --p1:#C2410C;--p1-bg:rgba(194,65,12,.07);--p1-bd:rgba(194,65,12,.22);
  --p2:#1D4ED8;--p2-bg:rgba(29,78,216,.07);--p2-bd:rgba(29,78,216,.22);
  --p3:#15803D;--p3-bg:rgba(21,128,61,.07);--p3-bd:rgba(21,128,61,.22);
  --red:#DC2626;--red-bg:rgba(220,38,38,.07);--red-bd:rgba(220,38,38,.18);
  --amber:#B45309;--amber-bg:rgba(180,83,9,.07);--amber-bd:rgba(180,83,9,.18);
  --blue:#1D4ED8;--blue-bg:rgba(29,78,216,.07);--blue-bd:rgba(29,78,216,.18);
}
html{font-size:14px;scroll-behavior:smooth}
body{font-family:'DM Sans',sans-serif;background:var(--bg);color:var(--txt);line-height:1.65;-webkit-font-smoothing:antialiased}
a{color:inherit;text-decoration:none}

/* TOPBAR */
.topbar{background:#fff;border-bottom:1px solid var(--line);padding:12px 32px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:200;box-shadow:0 1px 3px rgba(0,0,0,.06)}
.tb-left{display:flex;align-items:center;gap:12px}
.brand-logo{display:flex;align-items:center;gap:8px}
.brand-mark{width:20px;height:20px;border-radius:5px;background:var(--brand);display:flex;align-items:center;justify-content:center;font-family:'DM Mono',monospace;font-size:10px;font-weight:500;color:#fff}
.brand-name{font-family:'DM Mono',monospace;font-weight:500;font-size:13px;letter-spacing:.02em;color:#1A1A1A}
.tb-sep{width:1px;height:16px;background:var(--line2)}
.tb-title{font-size:11px;color:var(--txt3);letter-spacing:.05em;font-family:'DM Mono',monospace;text-transform:uppercase}
.tb-powered{font-size:10px;color:var(--txt3);font-family:'DM Mono',monospace;display:flex;align-items:center;gap:6px}
.tb-powered-dot{width:5px;height:5px;border-radius:50%;background:var(--p3)}

/* HERO — use a dark version of --brand for background, e.g. darken by 30% or use #1E1B4B for indigo */
.hero{background:#1E1B4B;padding:48px 48px 40px;position:relative;overflow:hidden}
.hero::before{content:'';position:absolute;right:-80px;top:-80px;width:340px;height:340px;border-radius:50%;background:rgba(92,78,229,.25);pointer-events:none}
.hero::after{content:'';position:absolute;right:100px;bottom:-80px;width:200px;height:200px;border-radius:50%;background:rgba(92,78,229,.12);pointer-events:none}
.hero-inner{max-width:900px;margin:0 auto;position:relative;z-index:1}
.hero-eye{font-family:'DM Mono',monospace;font-size:11px;letter-spacing:.14em;text-transform:uppercase;color:rgba(255,255,255,.45);margin-bottom:14px}
.hero-h{font-size:34px;font-weight:600;color:#fff;line-height:1.1;letter-spacing:-.025em;max-width:680px;margin-bottom:12px}
.hero-h span{color:#A5B4FC}  /* light version of brand color for the accent word */
.hero-sub{font-size:14px;color:rgba(255,255,255,.6);max-width:660px;margin-bottom:28px;line-height:1.75}
.hero-pills{display:flex;gap:10px;flex-wrap:wrap}
.hero-pill{background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.14);border-radius:6px;padding:6px 14px;font-size:12px;font-weight:500;color:rgba(255,255,255,.8);display:flex;align-items:center;gap:7px;font-family:'DM Mono',monospace}
.hp-dot-01{width:6px;height:6px;border-radius:50%;background:#FB923C;flex-shrink:0}
.hp-dot-02{width:6px;height:6px;border-radius:50%;background:#60A5FA;flex-shrink:0}
.hp-dot-03{width:6px;height:6px;border-radius:50%;background:#4ADE80;flex-shrink:0}

/* MAIN LAYOUT */
.main{max-width:900px;margin:0 auto;padding:36px 24px 80px}
.sec-label{font-family:'DM Mono',monospace;font-size:10px;text-transform:uppercase;letter-spacing:.12em;color:var(--txt3);margin:0 0 14px;display:flex;align-items:center;gap:10px}
.sec-label::after{content:'';flex:1;height:1px;background:var(--line)}

/* THREE PLAYS GRID */
.motions-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:0;border:1px solid var(--line);border-radius:10px;overflow:hidden;margin-bottom:32px}
.motion-card{padding:16px 18px 14px;background:var(--bg2);border-right:1px solid var(--line)}
.motion-card:last-child{border-right:none}
.mc-num{font-family:'DM Mono',monospace;font-size:22px;font-weight:500;line-height:1;margin-bottom:4px}
.mc-01 .mc-num{color:var(--p1)} .mc-02 .mc-num{color:var(--p2)} .mc-03 .mc-num{color:var(--p3)}
.mc-name{font-size:13px;font-weight:600;margin-bottom:8px}
.mc-01 .mc-name{color:var(--p1)} .mc-02 .mc-name{color:var(--p2)} .mc-03 .mc-name{color:var(--p3)}
.mc-row{margin-bottom:6px}
.mc-key{font-family:'DM Mono',monospace;font-size:9px;text-transform:uppercase;letter-spacing:.1em;color:var(--txt3);margin-bottom:2px}
.mc-val{font-size:11px;color:var(--txt2);line-height:1.5}
.mc-val strong{color:var(--txt);font-weight:500}
.mc-acv{font-size:14px;font-weight:600;margin-top:8px}
.mc-01 .mc-acv{color:var(--p1)} .mc-02 .mc-acv{color:var(--p2)} .mc-03 .mc-acv{color:var(--p3)}

/* METRICS ROW */
.metrics-row{display:grid;grid-template-columns:repeat(4,1fr);gap:0;border:1px solid var(--line);border-radius:10px;overflow:hidden;margin-bottom:32px;background:var(--bg2)}
.met{padding:16px 18px;border-right:1px solid var(--line)}
.met:last-child{border-right:none}
.met-n{font-size:22px;font-weight:600;letter-spacing:-.02em;line-height:1.1}
.n-brand{color:var(--brand)} .n-p1{color:var(--p1)} .n-p2{color:var(--p2)} .n-p3{color:var(--p3)}
.met-l{font-family:'DM Mono',monospace;font-size:10px;text-transform:uppercase;letter-spacing:.07em;color:var(--txt3);margin-top:4px}

/* ACCOUNT BLOCKS — collapsible */
.account-block{margin-bottom:10px}
.account-header{background:var(--bg2);border:1px solid var(--line);border-radius:10px;padding:14px 18px;cursor:pointer;display:grid;grid-template-columns:42px 1fr auto;gap:14px;align-items:center;transition:all .2s}
.account-header:hover{border-color:var(--brand-bd);background:var(--bg3)}
.account-block.open .account-header{background:var(--bg3);border-color:var(--brand-bd);border-bottom-left-radius:0;border-bottom-right-radius:0}
.acc-rank{font-family:'DM Mono',monospace;font-size:13px;font-weight:500;color:var(--txt3);width:42px;height:42px;border-radius:50%;background:var(--bg);display:flex;align-items:center;justify-content:center;border:1px solid var(--line)}
.acc-rank.top3{background:#1E1B4B;color:#A5B4FC;border-color:#3730A3}
.acc-name{font-size:16px;font-weight:600;color:var(--txt);letter-spacing:-.01em}
.acc-meta{font-family:'DM Mono',monospace;font-size:10.5px;color:var(--txt3);text-transform:uppercase;letter-spacing:.06em;margin-top:3px;display:flex;flex-wrap:wrap;gap:6px;align-items:center}
.play-badge{padding:2px 7px;border-radius:3px;font-size:10px;font-weight:500;letter-spacing:.04em}
.pb-01{background:var(--p1-bg);color:var(--p1);border:1px solid var(--p1-bd)}
.pb-02{background:var(--p2-bg);color:var(--p2);border:1px solid var(--p2-bd)}
.pb-03{background:var(--p3-bg);color:var(--p3);border:1px solid var(--p3-bd)}
.acc-chevron{font-size:11px;color:var(--txt3);font-family:'DM Mono',monospace;transition:transform .2s}
.account-block.open .acc-chevron{transform:rotate(180deg);color:var(--brand)}
.account-body{display:none;background:var(--bg2);border:1px solid var(--brand-bd);border-top:none;border-radius:0 0 10px 10px}
.account-block.open .account-body{display:block}

/* SIGNAL PILLS */
.signal-section{padding:18px 22px 14px;border-bottom:1px solid var(--line)}
.sig-row{display:flex;flex-wrap:wrap;gap:6px}
.sig-pill{font-family:'DM Mono',monospace;font-size:10.5px;padding:5px 9px;border-radius:4px;letter-spacing:.02em;display:inline-flex;align-items:center;gap:6px;line-height:1.4}
.sp-p1{background:var(--p1-bg);color:var(--p1);border:1px solid var(--p1-bd)}
.sp-p2{background:var(--p2-bg);color:var(--p2);border:1px solid var(--p2-bd)}
.sp-p3{background:var(--p3-bg);color:var(--p3);border:1px solid var(--p3-bd)}
.sp-none{background:var(--bg);color:var(--txt2);border:1px solid var(--line2)}

/* JOBS SECTION */
.jobs-section{padding:14px 22px 16px;border-bottom:1px solid var(--line);background:var(--bg3)}
.jobs-label{font-family:'DM Mono',monospace;font-size:10px;text-transform:uppercase;letter-spacing:.07em;color:var(--txt3);margin-bottom:8px;display:flex;flex-wrap:wrap;gap:6px;align-items:center}
.jobs-badge{padding:2px 6px;border-radius:3px;font-size:9px;font-weight:500;letter-spacing:.04em}
.jb-01{background:var(--p1-bg);color:var(--p1);border:1px solid var(--p1-bd)}
.jb-02{background:var(--p2-bg);color:var(--p2);border:1px solid var(--p2-bd)}
.jb-03{background:var(--p3-bg);color:var(--p3);border:1px solid var(--p3-bd)}
.jb-sumble{background:#1A1A1A;color:#fff;border:1px solid #333}
.jobs-grid{display:grid;grid-template-columns:1fr;gap:5px}
.job-row{display:grid;grid-template-columns:8px 1fr auto auto auto;gap:10px;align-items:center;padding:7px 11px;background:var(--bg2);border-radius:5px;border:1px solid var(--line);font-size:12px}
.job-play-dot{width:7px;height:7px;border-radius:50%}
.dot-01{background:var(--p1)} .dot-02{background:var(--p2)} .dot-03{background:var(--p3)}
.job-title-text{font-weight:500;color:var(--txt);line-height:1.4}
.job-tech{font-family:'DM Mono',monospace;font-size:10px;color:var(--txt3);text-transform:uppercase;letter-spacing:.05em}
.job-date{font-family:'DM Mono',monospace;font-size:10px;color:var(--txt3);text-transform:uppercase;letter-spacing:.05em}
.job-link{font-family:'DM Mono',monospace;font-size:10px;color:var(--brand);background:var(--brand-dim);padding:3px 8px;border-radius:3px;border:1px solid var(--brand-bd);white-space:nowrap}
.job-link:hover{background:var(--brand);color:#fff}
.jobs-more{display:inline-block;margin-top:7px;font-family:'DM Mono',monospace;font-size:10.5px;color:var(--brand);background:var(--bg2);padding:5px 10px;border-radius:4px;border:1px dashed var(--brand-bd)}

/* TRIGGER / WEDGE SECTIONS */
.trigger-section{padding:14px 22px;border-bottom:1px solid var(--line)}
.trig-label{font-family:'DM Mono',monospace;font-size:9.5px;text-transform:uppercase;letter-spacing:.1em;color:var(--txt3);margin-bottom:7px}
.trig-box-01{border-left:3px solid var(--p1);padding-left:14px}
.trig-box-02{border-left:3px solid var(--p2);padding-left:14px}
.trig-box-03{border-left:3px solid var(--p3);padding-left:14px}
.wedge-text{font-size:12px;color:var(--txt2);line-height:1.7;margin-bottom:10px}
.wedge-text strong{color:var(--txt);font-weight:500}
.opener-p1{font-style:italic;color:var(--p1);font-size:12.5px;line-height:1.75;font-weight:500}
.opener-p2{font-style:italic;color:var(--p2);font-size:12.5px;line-height:1.75;font-weight:500}
.opener-p3{font-style:italic;color:var(--p3);font-size:12.5px;line-height:1.75;font-weight:500}

/* THREE-COLUMN CONTEXT SECTION */
.three-col{display:grid;grid-template-columns:1fr 1fr 1fr;gap:0;border-bottom:1px solid var(--line)}
.tcol{padding:16px 20px;border-right:1px solid var(--line)}
.tcol:last-child{border-right:none}
.col-hd{font-family:'DM Mono',monospace;font-size:9.5px;text-transform:uppercase;letter-spacing:.1em;color:var(--txt3);margin-bottom:8px}
.col-body{font-size:11.5px;color:var(--txt2);line-height:1.7}
.col-body strong{color:var(--txt);font-weight:500}

/* BUYING GROUP */
.buying-group{padding:18px 22px 22px}
.bg-title{font-family:'DM Mono',monospace;font-size:10.5px;text-transform:uppercase;letter-spacing:.1em;color:var(--txt3);margin-bottom:12px;display:flex;align-items:center;gap:8px}
.bg-verified{background:#1A1A1A;color:#fff;padding:2px 7px;border-radius:3px;font-size:9.5px;font-weight:500;border:1px solid #333}
.bg-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.bg-card{background:var(--bg3);border:1px solid var(--line);border-radius:6px;padding:11px 14px;transition:border-color .15s}
.bg-card:hover{border-color:var(--brand-bd)}
.bg-card-head{display:flex;align-items:center;gap:9px;margin-bottom:6px}
.bg-av{width:30px;height:30px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-family:'DM Mono',monospace;font-weight:500;font-size:11px;flex-shrink:0}
.av-red{background:rgba(220,38,38,.07);color:#DC2626;border:1px solid rgba(220,38,38,.18)}
.av-amber{background:rgba(180,83,9,.07);color:#B45309;border:1px solid rgba(180,83,9,.18)}
.av-blue{background:rgba(29,78,216,.07);color:#1D4ED8;border:1px solid rgba(29,78,216,.18)}
.av-purple{background:rgba(29,78,216,.07);color:#1D4ED8;border:1px solid rgba(29,78,216,.22)}
.av-teal{background:rgba(21,128,61,.07);color:#15803D;border:1px solid rgba(21,128,61,.22)}
.av-brand{background:var(--brand-dim);color:var(--brand);border:1px solid var(--brand-bd)}
.bg-name{font-size:12px;font-weight:600;line-height:1.2}
.bg-role-title{font-size:10.5px;color:var(--txt3);font-family:'DM Mono',monospace;margin-top:1px}
.bg-role{font-size:10.5px;font-weight:500;margin-bottom:3px;display:inline-block}
.role-exec{color:var(--p1)} .role-eval{color:var(--p2)} .role-ec{color:var(--amber)} .role-user{color:var(--p3)}
.bg-loc{font-size:10.5px;color:var(--txt2);line-height:1.4;margin-bottom:6px;display:block}
.bg-links{display:flex;gap:5px;flex-wrap:wrap}
.bg-link{font-family:'DM Mono',monospace;font-size:9.5px;color:var(--brand);background:var(--brand-dim);padding:3px 7px;border-radius:3px;border:1px solid var(--brand-bd)}

/* FOOTER */
.footer{background:#1E1B4B;color:rgba(255,255,255,.7);padding:36px 24px;font-family:'DM Mono',monospace;font-size:11px;text-align:center;line-height:1.8;letter-spacing:.04em;border-top:3px solid #818CF8}
.footer strong{color:#A5B4FC;font-weight:500}

/* RESPONSIVE */
@media (max-width:780px){
  .motions-grid,.metrics-row,.bg-grid,.three-col{grid-template-columns:1fr}
  .motion-card,.met,.tcol{border-right:none;border-bottom:1px solid var(--line)}
  .motion-card:last-child,.met:last-child,.tcol:last-child{border-bottom:none}
  .hero{padding:32px 24px 28px} .hero-h{font-size:26px}
  .main{padding:24px 16px 60px}
  .job-row{grid-template-columns:8px 1fr;gap:6px}
  .job-tech,.job-date,.job-link{grid-column:2}
}
```

---

## Document Skeleton

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>[Company] · US Top 10 · Powered by Sumble</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,wght@0,300;0,400;0,500;0,600;1,400&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  /* PASTE FULL CSS HERE */
</style>
</head>
<body>
<script>
function toggle(el){
  const block=el.closest('.account-block');
  block.classList.toggle('open');
}
</script>

<!-- TOPBAR -->
<div class="topbar">
  <div class="tb-left">
    <div class="brand-logo">
      <div class="brand-mark">[First letter of company]</div>
      <span class="brand-name">[Company Name]</span>
    </div>
    <span class="tb-sep"></span>
    <span class="tb-title">US Top 10 · Target Accounts</span>
  </div>
  <span class="tb-powered"><span class="tb-powered-dot"></span>Powered by Sumble · [Month Year]</span>
</div>

<!-- HERO -->
<section class="hero">
  <div class="hero-inner">
    <div class="hero-eye">⚡ Prepared for [Full Name], [Title] · [Company] · Internal</div>
    <h1 class="hero-h">[Striking stat or hook]. <span>[Punchy contrast phrase].</span></h1>
    <p class="hero-sub">[2-3 sentences: who the target company is, why these 10 accounts are their ICP, what Sumble data surfaced]</p>
    <div class="hero-pills">
      <span class="hero-pill"><span class="hp-dot-01"></span>Play 01 · [Name]</span>
      <span class="hero-pill"><span class="hp-dot-02"></span>Play 02 · [Name]</span>
      <span class="hero-pill"><span class="hp-dot-03"></span>Play 03 · [Name]</span>
    </div>
  </div>
</section>

<main class="main">

<!-- THREE PLAYS -->
<div class="sec-label">Three plays · how [Company] wins</div>
<div class="motions-grid">
  <div class="motion-card mc-01">
    <div class="mc-num">01</div>
    <div class="mc-name">[Play Name]</div>
    <div class="mc-row"><div class="mc-key">Displace</div><div class="mc-val">[Tools being displaced]</div></div>
    <div class="mc-row"><div class="mc-key">Sell</div><div class="mc-val">[Specific product + value prop]</div></div>
    <div class="mc-row"><div class="mc-key">Buyer</div><div class="mc-val"><strong>[Primary Title]</strong> · [Secondary Title]</div></div>
    <div class="mc-acv">ACV $[X]K–$[Y]K</div>
  </div>
  <!-- repeat for mc-02 and mc-03 -->
</div>

<!-- METRICS ROW -->
<div class="sec-label">Pipeline · top 10 ranked by Sumble signal density × [Company] ICP fit</div>
<div class="metrics-row">
  <div class="met"><div class="met-n n-brand">10</div><div class="met-l">Ranked US accounts · [size range] employees</div></div>
  <div class="met"><div class="met-n n-p1">[N]</div><div class="met-l">Play 01 signals · [brief description]</div></div>
  <div class="met"><div class="met-n n-p2">[N]</div><div class="met-l">Play 02 signals · [brief description]</div></div>
  <div class="met"><div class="met-n n-p3">[N]</div><div class="met-l">Play 03 signals · [brief description]</div></div>
</div>

<div class="sec-label">Top 10 US accounts · click to expand</div>

<!-- ACCOUNT BLOCKS (repeat 10×) -->
<div class="account-block">
  <div class="account-header" onclick="toggle(this)">
    <div class="acc-rank top3">[1–3] or acc-rank [4–10]</div>
    <div>
      <div class="acc-name">[Company Name]</div>
      <div class="acc-meta">
        <span class="play-badge pb-01">01 · [Play Name]</span>
        <!-- add pb-02 or pb-03 for secondary play if applicable -->
        <span>Sumble [score] · [Industry] · [City, State] · [N,NNN] employees</span>
      </div>
    </div>
    <span class="acc-chevron">▼</span>
  </div>
  <div class="account-body">

    <!-- Signal pills -->
    <div class="signal-section">
      <div class="sig-row">
        <span class="sig-pill sp-p1">● Play 01 · [specific Sumble data point]</span>
        <span class="sig-pill sp-p2">● Play 02 · [specific Sumble data point]</span>
        <span class="sig-pill sp-none">● [Context signal]</span>
      </div>
    </div>

    <!-- Job triggers (top 5 accounts only) -->
    <div class="jobs-section">
      <div class="jobs-label">Sumble job triggers <span class="jobs-badge jb-01">Play 01</span> <span class="jobs-badge jb-sumble">sumble verified</span></div>
      <div class="jobs-grid">
        <div class="job-row">
          <div class="job-play-dot dot-01"></div>
          <div class="job-title-text">[Job Title]</div>
          <span class="job-tech">[Tech Tag]</span>
          <span class="job-date">[Mon DD]</span>
          <a class="job-link" href="[sumble job url]" target="_blank">↗ sumble</a>
        </div>
      </div>
      <a class="jobs-more" href="[sumble org jobs url]" target="_blank">↗ All [Company] jobs on Sumble</a>
    </div>

    <!-- Wedge + opener (use trig-box-01, -02, or -03 based on primary play) -->
    <div class="trigger-section">
      <div class="trig-label">[Play name] wedge · [one-sentence summary of the angle]</div>
      <div class="trig-box-01">
        <div class="wedge-text"><strong>[Lead with the most specific Sumble data point in bold.]</strong> [2-3 sentences explaining what the signal means commercially and why it opens a buying window.]</div>
        <div class="opener-p1">"[Personalized cold opener referencing the data, citing a reference customer with a metric, ending with a CTA.]"</div>
      </div>
    </div>

    <!-- Three-column context -->
    <div class="three-col">
      <div class="tcol"><div class="col-hd">Why [Account] fits</div><div class="col-body">[Company profile + Sumble fit score]</div></div>
      <div class="tcol"><div class="col-hd">Stack signal</div><div class="col-body">[Real tech stack data: tool, teams, job count]</div></div>
      <div class="tcol"><div class="col-hd">Reference</div><div class="col-body"><strong>[Reference Customer]</strong> — [specific metric from case study]</div></div>
    </div>

    <!-- Buying group -->
    <div class="buying-group">
      <div class="bg-title">Buying group · [Account] <span class="bg-verified">sumble verified</span></div>
      <div class="bg-grid">
        <div class="bg-card">
          <div class="bg-card-head">
            <div class="bg-av av-red">[Initials]</div>
            <div><div class="bg-name">[Full Name]</div><div class="bg-role-title">[Job Title]</div></div>
          </div>
          <span class="bg-role role-exec">Executive Sponsor</span>
          <span class="bg-loc">[City, State] · [Title] since [Mon YYYY]</span>
          <div class="bg-links">
            <a class="bg-link" href="[sumble person url]" target="_blank">↗ sumble</a>
            <a class="bg-link" href="[linkedin url]" target="_blank">↗ linkedin</a>
          </div>
        </div>
        <!-- repeat bg-card for additional contacts -->
      </div>
    </div>

  </div>
</div>
<!-- end account-block; repeat × 10 -->

</main>

<!-- FOOTER -->
<div class="footer">
  [Company] GTM Intelligence Brief · Powered by <strong>Sumble</strong> · Account intelligence built on live hiring signals, verified tech stacks, and real-time org data.<br>
  Prepared for <strong>[Full Name], [Title]</strong> · Sourced [Month Year]<br>
  <a href="https://sumble.com" style="color:#A5B4FC;text-decoration:underline" target="_blank">Explore all signals on Sumble →</a>
</div>

</body>
</html>
```

---

## Avatar Color Guide

Use these `.bg-av` color classes to visually differentiate buying group contacts:

| Class | Color | Use for |
|-------|-------|---------|
| `av-red` | Red | Executive Sponsors (CRO, CEO, President) |
| `av-amber` | Amber | Economic Champions (CFO, VP Finance, Dir RevOps) |
| `av-blue` | Blue | Key Evaluators (VP Sales Ops, Dir Sales) |
| `av-purple` | Purple | Secondary Evaluators (VP Sales, Dir AE) |
| `av-teal` | Teal | End Users (VP AE, SDR leadership) |
| `av-brand` | Brand color | Any contact that's a close fit but doesn't match above |

---

## Signal Pill Color Guide

| Class | Color | When to use |
|-------|-------|-------------|
| `sp-p1` | Orange/red | Play 01 signals (consolidation, displacement) |
| `sp-p2` | Blue | Play 02 signals (RevOps, data, CRM) |
| `sp-p3` | Green | Play 03 signals (AI, scale, pipeline growth) |
| `sp-none` | Neutral gray | Context signals (tenure, market position, etc.) |

---

## Brand Color Presets

If you don't know the company's exact brand color, use one of these:

| Company type | `--brand` | `--brand-dk` | `--brand-dim` | `--brand-bd` |
|-------------|-----------|--------------|----------------|--------------|
| Indigo/purple (default) | `#5C4EE5` | `#4438C8` | `rgba(92,78,229,.07)` | `rgba(92,78,229,.22)` |
| Slate/dark | `#334155` | `#1E293B` | `rgba(51,65,85,.07)` | `rgba(51,65,85,.22)` |
| Green | `#166534` | `#14532D` | `rgba(22,101,52,.07)` | `rgba(22,101,52,.22)` |
| Blue | `#1D4ED8` | `#1E40AF` | `rgba(29,78,216,.07)` | `rgba(29,78,216,.22)` |
| Red/orange | `#B91C1C` | `#991B1B` | `rgba(185,28,28,.07)` | `rgba(185,28,28,.22)` |
