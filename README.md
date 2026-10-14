<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Are We Ready for SOC Pilots?</title>
<meta name="description" content="An evaluation of the “SOC pilot” model: where AI can act alone, where humans must stay in command, and what happens when asset data is wrong.">
<meta name="author" content="Edrian Alvizo">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,600;9..144,800&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --ink: #14202e;
    --muted: #5b6b7c;
    --paper: #f6f8fa;
    --card: #ffffff;
    --navy: #0b1b2b;
    --navy-2: #12304a;
    --teal: #19b3a6;
    --amber: #e8a33d;
    --red: #d9534f;
    --green: #2e9e6b;
    --line: #dfe5ea;
  }
  * { box-sizing: border-box; }
  html { scroll-behavior: smooth; }
  body {
    margin: 0;
    background: var(--paper);
    color: var(--ink);
    font-family: "Inter", system-ui, -apple-system, "Segoe UI", sans-serif;
    font-size: 18px;
    line-height: 1.75;
  }

  /* reading progress */
  #progress {
    position: fixed; top: 0; left: 0; height: 4px; width: 0;
    background: linear-gradient(90deg, var(--teal), var(--amber));
    z-index: 50;
  }

  /* hero */
  .hero {
    position: relative;
    overflow: hidden;
    color: #fff;
    background:
      radial-gradient(1000px 400px at 85% -10%, rgba(25,179,166,.35), transparent 60%),
      linear-gradient(160deg, var(--navy) 0%, var(--navy-2) 100%);
    padding: 88px 24px 96px;
  }
  .hero::before {
    content: "";
    position: absolute; inset: 0;
    background-image:
      linear-gradient(rgba(255,255,255,.05) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,.05) 1px, transparent 1px);
    background-size: 44px 44px;
    mask-image: linear-gradient(180deg, #000 20%, transparent 100%);
    -webkit-mask-image: linear-gradient(180deg, #000 20%, transparent 100%);
  }
  .hero-inner { position: relative; max-width: 820px; margin: 0 auto; }
  .tag {
    display: inline-block;
    font-size: 12px; font-weight: 600; letter-spacing: .14em; text-transform: uppercase;
    color: var(--teal);
    border: 1px solid rgba(25,179,166,.5);
    padding: 6px 12px; border-radius: 999px;
    margin-bottom: 24px;
  }
  h1 {
    font-family: "Fraunces", Georgia, serif;
    font-weight: 800;
    font-size: clamp(2.2rem, 5.5vw, 3.6rem);
    line-height: 1.1;
    margin: 0 0 20px;
    letter-spacing: -.01em;
  }
  .subtitle {
    font-size: 1.15rem; color: #c3d2df; max-width: 640px; margin: 0 0 32px;
  }
  .byline {
    display: inline-flex; align-items: center; gap: 12px;
    font-weight: 600; letter-spacing: .08em; text-transform: uppercase; font-size: .9rem;
  }
  .byline .dot {
    width: 38px; height: 38px; border-radius: 50%;
    display: grid; place-items: center;
    background: var(--teal); color: var(--navy); font-weight: 800; letter-spacing: 0;
  }

  /* layout */
  main { max-width: 760px; margin: -40px auto 0; padding: 0 20px 40px; position: relative; }
  article {
    background: var(--card);
    border-radius: 18px;
    padding: 48px 44px;
    box-shadow: 0 18px 50px rgba(11,27,43,.10);
  }
  h2 {
    font-family: "Fraunces", Georgia, serif;
    font-size: 1.7rem; line-height: 1.25;
    margin: 2.4em 0 .6em;
    padding-left: 16px;
    border-left: 5px solid var(--teal);
  }
  h2:first-of-type { margin-top: 1.4em; }
  p { margin: 0 0 1.15em; }
  .lede { font-size: 1.2rem; color: #2a3a4b; }
  .lede::first-letter {
    font-family: "Fraunces", Georgia, serif;
    float: left; font-size: 4rem; line-height: .85; padding: 6px 12px 0 0; color: var(--teal); font-weight: 800;
  }

  blockquote {
    margin: 1.8em 0;
    padding: 22px 26px;
    background: linear-gradient(135deg, #eef9f8, #f4f8fb);
    border-left: 5px solid var(--amber);
    border-radius: 0 12px 12px 0;
    font-family: "Fraunces", Georgia, serif;
    font-size: 1.25rem; line-height: 1.5;
  }

  /* autonomy zones */
  .zones { display: grid; gap: 14px; margin: 1.6em 0 2em; }
  @media (min-width: 700px) { .zones { grid-template-columns: repeat(3, 1fr); } }
  .zone {
    border-radius: 12px; padding: 18px 18px 14px;
    border: 1px solid var(--line); border-top-width: 6px; background: #fff;
    font-size: .95rem; line-height: 1.55;
  }
  .zone h3 { margin: 0 0 8px; font-size: 1rem; font-family: "Inter", sans-serif; }
  .zone ul { margin: 0; padding-left: 1.1em; }
  .zone.green { border-top-color: var(--green); }
  .zone.amber { border-top-color: var(--amber); }
  .zone.red   { border-top-color: var(--red); }

  ul.guard { padding-left: 1.2em; }
  ul.guard li { margin-bottom: .55em; }
  ul.guard li::marker { color: var(--teal); }

  .skills { display: grid; gap: 12px; margin: 1.4em 0; }
  .skill {
    display: grid; grid-template-columns: 40px 1fr; gap: 14px; align-items: start;
    padding: 14px 16px; border: 1px solid var(--line); border-radius: 12px; background: #fbfcfd;
  }
  .skill .n {
    width: 40px; height: 40px; border-radius: 10px;
    display: grid; place-items: center;
    background: var(--navy); color: var(--teal); font-weight: 700;
  }
  .skill p { margin: 0; font-size: .98rem; line-height: 1.6; }
  .skill strong { display: block; margin-bottom: 2px; }

  /* references */
  .refs { margin-top: 3em; padding-top: 1.6em; border-top: 2px solid var(--line); }
  .refs h2 { margin-top: 0; }
  .ref { padding-left: 2em; text-indent: -2em; font-size: .98rem; line-height: 1.65; margin-bottom: 1em; word-break: break-word; }
  .ref a { color: #0f6f7a; }

  footer { text-align: center; color: var(--muted); font-size: .9rem; padding: 10px 20px 50px; }

  @media (max-width: 600px) {
    body { font-size: 17px; }
    article { padding: 32px 22px; }
    .hero { padding: 64px 20px 80px; }
  }
</style>
</head>
<body>
<div id="progress"></div>

<header class="hero">
  <div class="hero-inner">
    <span class="tag">Cybersecurity &amp; AI</span>
    <h1>Are We Ready for SOC Pilots?</h1>
    <p class="subtitle">Autopilot, asset context, and the human who still has to sit in the cockpit.</p>
    <div class="byline"><span class="dot">E</span><span>BY: Edrian Alvizo</span></div>
  </div>
</header>

<main>
<article>

<p class="lede">Picture a long-haul flight where the pilots barely touch the controls, stepping in only when something goes wrong. Most of us accept that without a second thought, because we trust machines with the routine and people with the unexpected. John Velisaris (2025), an Associate Partner at IBM Cyber Threat Management Services, wants security operations centers (SOCs) to work the same way. Machines would run standard operations, while analysts become “SOC pilots” who decide when to get involved, especially when something unfamiliar shows up, such as a zero-day attack.</p>

<p>It is a compelling picture, and it deserves a serious test. Is the SOC pilot model realistic? Is it responsible? My answer is that the direction is realistic, the destination is not here yet, and the whole thing stands or falls on one unglamorous question: is the information the AI relies on actually true?</p>

<h2>What the article actually claims</h2>

<p>Velisaris (2025) starts from familiar pain: too much noise, too little context for investigating alerts, workflow bottlenecks, and alert fatigue. He observes that automation already works well in closed systems like endpoint detection and response (EDR) tools, where the software and its console understand every variable. He also describes a specialist at a major hyperscaler whose company knows every technology and asset in its stack, so it needs very little human involvement.</p>

<p>Most organizations do not live in that tidy world. They run SIEM platforms with SOAR playbooks, and a playbook might quarantine a host only if it is not a server and is showing known malicious behavior. The catch, as the author explains, is that the playbook cannot fire until it knows what the asset is. Is it a critical server or an ordinary workstation? Today, human analysts fill that gap by hand, hopping between tools. IBM’s answer is an orchestrator called ATOM that notices missing context and sends other agents to query vulnerability management, exposure management, configuration management databases (CMDBs), and EDR or XDR systems to fill it in. Notably, Velisaris (2025) admits it is still unknown whether analysts will really end up in the pilot’s chair.</p>

<blockquote>Automation without context is not autonomy. It is a fast way to make confident mistakes.</blockquote>

<h2>Where AI can safely act on its own</h2>

<p>The most useful way I found to think about this comes from Mohsin et al. (2025), who propose that the right amount of autonomy depends on three things: how complex the task is, how risky it is, and how much trust the system has earned. Their examples line up neatly. Blocking a known malicious IP address is high-autonomy work with little human involvement. Phishing classification sits in the middle and benefits from human review. Investigating a zero-day exploit calls for low autonomy and heavy human control.</p>

<p>Their simulated cyber range study is encouraging. The AI assistant began with every output reviewed by a person and earned more freedom as it proved accurate. The authors report that false positives dropped from 70% to 35%, investigations shortened from about three hours to one, and mean time to respond fell from eight hours to roughly ninety minutes (Mohsin et al., 2025). That is a real argument for letting machines carry the routine load.</p>

<p>I would add my own rule of thumb. An AI agent can act alone when the action is reversible, the blast radius is small, the evidence is strong and comes from verified sources, and everything is logged. Under that rule, the safe zone includes enriching alerts, grouping duplicates, closing well-understood benign noise, blocking known-bad indicators, and pulling a confirmed phishing email from mailboxes.</p>

<div class="zones">
  <div class="zone green">
    <h3>AI acts alone</h3>
    <ul>
      <li>Alert enrichment and de-duplication</li>
      <li>Blocking known-bad IPs</li>
      <li>Removing confirmed phishing emails</li>
      <li>Isolating a verified workstation (with auto-release)</li>
    </ul>
  </div>
  <div class="zone amber">
    <h3>AI proposes, human approves</h3>
    <ul>
      <li>Isolating servers or privileged accounts</li>
      <li>Actions on assets with uncertain data</li>
      <li>Closing ambiguous alerts at scale</li>
    </ul>
  </div>
  <div class="zone red">
    <h3>Human leads</h3>
    <ul>
      <li>Zero-days and novel attack patterns</li>
      <li>Business-impacting shutdowns</li>
      <li>Legal, regulatory, and disclosure calls</li>
      <li>Policy and governance</li>
    </ul>
  </div>
</div>

<p>One caution: that study ran in a simulated port network, and its authors acknowledge that real deployments face messier infrastructure, workflows, and data quality (Mohsin et al., 2025). Good results in a simulation are a reason to try, not a reason to trust blindly.</p>

<h2>Where human judgment must remain</h2>

<p>Some decisions are not really technical. Taking a payment system offline during month-end close, deciding whether a breach must be reported to regulators or customers, or choosing how hard to respond to an attack that might involve a partner are all judgments about business impact, law, and ethics. An agent can supply facts for these decisions, but it cannot own them. Mohsin et al. (2025) make a similar point, noting that AI tends to lack contextual understanding and struggles to deduce an attacker’s intent, while humans bring business awareness and ethical judgment.</p>

<p>Novelty is the other big reason. Velisaris (2025) is right that the zero-day is the defining uncertainty of security. Models learn from what has already happened, and an attacker who knows an organization automates a certain response can deliberately design around it. That is a feature of this field that the airline comparison does not have: the weather does not study the autopilot, but adversaries study the SOC.</p>

<p>Finally, there is accountability. When an automated action causes damage, someone has to answer for it. Mohsin et al. (2025) argue that humans should stay in command of decisions such as threat escalation, service takedown, and enterprise-wide containment, so that accountability and ethical governance are preserved.</p>

<h2>When the asset information is incomplete or wrong</h2>

<p>This is where the SOC pilot model is most fragile. The article’s own example makes it clear: ATOM can conclude that a device is a workstation based on its hostname and network location, reasoning the way a human analyst would (Velisaris, 2025). That is clever, and it is also an inference rather than a fact. If the inference is wrong, the consequences are not symmetrical:</p>

<ul class="guard">
  <li><strong>A critical server mislabeled as a workstation.</strong> An agent isolates it automatically and the organization causes its own outage, a self-inflicted denial of service.</li>
  <li><strong>A high-value workstation mislabeled as low priority.</strong> The compromised machine of a domain administrator gets treated like routine noise while the attacker moves laterally.</li>
  <li><strong>An unknown asset.</strong> The agent has no record at all and must guess, or quietly skip it.</li>
</ul>

<p>These are not exotic cases. Mohsin et al. (2025) point out that SIEM and EDR tools have limits in real-time asset discovery, that manual processes and constantly changing systems produce incomplete inventories, and that missing logs can hide attacker movement. There is also a circular trap in their own Level 2 example: the system waits for approval before isolating critical servers or privileged accounts, but it can only do that if it already knows which assets are critical. Adding more agents that query the CMDB does not create truth. It can launder a stale record into a confident-sounding decision, and the authors warn that overtrust leads to automation bias and overlooked errors.</p>

<p>So what would a responsible design do? I would build in these guardrails:</p>

<ul class="guard">
  <li><strong>Fail cautious.</strong> If an asset’s criticality is unknown or stale, treat it as high-criticality and require human approval.</li>
  <li><strong>Cross-check sources.</strong> Require two independent sources, such as the CMDB and EDR telemetry, to agree before an autonomous containment.</li>
  <li><strong>Prefer reversible actions.</strong> Use time-limited quarantines with automatic release instead of permanent changes.</li>
  <li><strong>Cap the blast radius.</strong> Limit how many hosts an agent can isolate in an hour, so one bad record cannot become a mass outage.</li>
  <li><strong>Keep a kill switch and an audit trail.</strong> Mohsin et al. (2025) specifically call for kill switches, audit logging, and fallback protocols.</li>
  <li><strong>Treat the inventory as a product.</strong> Give it an owner, freshness checks, and regular audits, because every agent downstream depends on it.</li>
</ul>

<h2>Realistic and responsible? My verdict</h2>

<p>The SOC pilot idea is realistic for closed environments and for narrow, well-understood tasks, and the evidence suggests it will keep expanding as trust is earned. It is responsible only as <em>bounded</em> autonomy. I also think the analogy needs one adjustment. Airline pilots do not wait on the ground to be called; they sit in the cockpit, monitor the system, and keep practicing. A SOC pilot should be the same: actively supervising, sampling the machine’s decisions, and staying sharp enough to take over. Without that practice, the pilot seat becomes an empty chair, and Mohsin et al. (2025) list complacency as one of the risks of leaning too hard on automation.</p>

<h2>Becoming a SOC pilot instead of being replaced by one</h2>

<p>If routine work is going to the machines, then the value of a human analyst moves to everything the machines cannot do well. Here are the skills I would build, in the order I think they matter:</p>

<div class="skills">
  <div class="skill"><div class="n">1</div><p><strong>Solid fundamentals.</strong> Networking, operating systems, logs, and cloud basics. You cannot check an agent’s claim if you do not understand the evidence behind it.</p></div>
  <div class="skill"><div class="n">2</div><p><strong>Adversary thinking.</strong> Threat hunting and frameworks like MITRE ATT&amp;CK, because novel attacks are exactly where humans are meant to take command.</p></div>
  <div class="skill"><div class="n">3</div><p><strong>Asset and data literacy.</strong> Understanding CMDBs, asset criticality, and data quality, since bad context is the most likely way an agent fails.</p></div>
  <div class="skill"><div class="n">4</div><p><strong>Automation and scripting.</strong> Python, SOAR playbooks, and the ability to read and tune agent logic, so I can write guardrails rather than just follow them.</p></div>
  <div class="skill"><div class="n">5</div><p><strong>AI literacy and calibrated trust.</strong> Knowing how models drift and hallucinate, and when to trust, test, or override them. Mohsin et al. (2025) stress that analysts must understand the AI they supervise.</p></div>
  <div class="skill"><div class="n">6</div><p><strong>Communication under pressure.</strong> Translating technical risk into business terms so leaders can make fast, informed decisions during an incident.</p></div>
  <div class="skill"><div class="n">7</div><p><strong>Governance and ethics.</strong> Understanding policy, accountability, and frameworks such as NIST, because someone has to own the decision.</p></div>
</div>

<p>Notice that none of these is “be faster than the AI.” That race is already lost. The goal is to be the person who knows when the AI is wrong, why it is wrong, and what to do next.</p>

<h2>Final thoughts</h2>

<p>Velisaris (2025) offers an inspiring vision, and to his credit he does not claim the journey is finished. The part I would underline is the one his own article reveals: autonomy is only as good as context. Until organizations can trust their asset data, the safest SOC pilot is a human who stays in the cockpit, watches the instruments, and keeps the skills to fly by hand.</p>

<section class="refs">
  <h2>References</h2>

  <p class="ref">Mohsin, A., Janicke, H., Ibrahim, A., Sarker, I. H., &amp; Camtepe, S. (2025). <em>A unified framework for human–AI collaboration in security operations centers with trusted autonomy</em>. arXiv. <a href="https://doi.org/10.48550/arXiv.2505.23397">https://doi.org/10.48550/arXiv.2505.23397</a></p>

  <p class="ref">Velisaris, J. (2025, May 8). <em>How agentic AI enables an autonomous SOC with minimal human involvement</em>. IBM Think. <a href="https://www.ibm.com/think/insights/agentic-ai-enables-autonomous-soc">https://www.ibm.com/think/insights/agentic-ai-enables-autonomous-soc</a></p>
</section>

</article>
</main>

<script>
  (function () {
    var bar = document.getElementById('progress');
    function update() {
      var h = document.documentElement;
      var max = h.scrollHeight - h.clientHeight;
      bar.style.width = (max > 0 ? (h.scrollTop / max) * 100 : 0) + '%';
    }
    window.addEventListener('scroll', update, { passive: true });
    update();
  })();
</script>
</body>
</html>
