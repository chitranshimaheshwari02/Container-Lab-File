<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Containerization & DevOps — Lab File</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=Outfit:wght@400;600;700&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0f1117;
  --bg2: #161a24;
  --bg3: #1c2030;
  --border: rgba(255,255,255,0.07);
  --border2: rgba(255,255,255,0.12);
  --text: #c9cdd8;
  --text-muted: #6b7385;
  --text-bright: #eceef2;
  --accent: #5b7fff;
  --accent-soft: rgba(91,127,255,0.1);
  --accent-border: rgba(91,127,255,0.28);
  --tag-bg: rgba(255,255,255,0.04);
  --tag-text: #8a93a8;
  --sans: 'Inter', sans-serif;
  --display: 'Outfit', sans-serif;
}

* { margin: 0; padding: 0; box-sizing: border-box; }
html { scroll-behavior: smooth; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--sans);
  font-size: 14px;
  line-height: 1.7;
  overflow-x: hidden;
}

nav {
  position: fixed;
  top: 0; left: 0; right: 0;
  z-index: 100;
  height: 56px;
  background: rgba(15,17,23,0.92);
  backdrop-filter: blur(16px);
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 2.5rem;
}

.nav-logo {
  font-family: var(--display);
  font-weight: 700;
  font-size: 15px;
  color: var(--text-bright);
  text-decoration: none;
}

.nav-logo span { color: var(--accent); }

.nav-links { display: flex; gap: 2rem; list-style: none; }

.nav-links a {
  font-size: 13px;
  color: var(--text-muted);
  text-decoration: none;
  transition: color 0.2s;
}

.nav-links a:hover { color: var(--text-bright); }

.hero {
  min-height: 90vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  max-width: 1100px;
  margin: 0 auto;
  padding: 120px 2.5rem 5rem;
}

.hero-eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-size: 11.5px;
  font-weight: 500;
  color: var(--accent);
  letter-spacing: 0.07em;
  text-transform: uppercase;
  margin-bottom: 1.6rem;
  background: var(--accent-soft);
  border: 1px solid var(--accent-border);
  padding: 5px 12px;
  border-radius: 20px;
  width: fit-content;
}

.hero-title {
  font-family: var(--display);
  font-size: clamp(2.4rem, 5.5vw, 4.5rem);
  font-weight: 700;
  color: var(--text-bright);
  line-height: 1.1;
  letter-spacing: -0.03em;
  margin-bottom: 1.2rem;
}

.hero-title span { color: var(--accent); }

.hero-sub {
  font-size: 0.97rem;
  color: var(--text-muted);
  max-width: 500px;
  margin-bottom: 2.4rem;
  line-height: 1.75;
}

.hero-badges {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 3.5rem;
}

.badge {
  background: var(--bg3);
  border: 1px solid var(--border2);
  border-radius: 6px;
  padding: 6px 13px;
  font-size: 12px;
  color: var(--text);
}

.badge strong { color: var(--text-bright); font-weight: 600; margin-right: 4px; }

.stats-row { display: flex; gap: 2.5rem; flex-wrap: wrap; align-items: center; }

.stat-num {
  font-family: var(--display);
  font-size: 1.7rem;
  font-weight: 700;
  color: var(--text-bright);
  line-height: 1;
}

.stat-label { font-size: 12px; color: var(--text-muted); margin-top: 3px; }

.stat-div { width: 1px; background: var(--border2); height: 36px; }

.section {
  max-width: 1100px;
  margin: 0 auto;
  padding: 4.5rem 2.5rem;
}

.section-head {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 2rem;
}

.section-label {
  font-size: 11px;
  font-weight: 600;
  color: var(--accent);
  letter-spacing: 0.12em;
  text-transform: uppercase;
  opacity: 0.8;
}

.section-title {
  font-family: var(--display);
  font-size: 1.4rem;
  font-weight: 700;
  color: var(--text-bright);
  letter-spacing: -0.02em;
}

.section-rule { flex: 1; height: 1px; background: var(--border); margin-left: 6px; }

.exp-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 1px;
  background: var(--border);
  border: 1px solid var(--border);
  border-radius: 12px;
  overflow: hidden;
}

.exp-card {
  background: var(--bg);
  padding: 1.6rem 1.8rem;
  text-decoration: none;
  color: inherit;
  display: block;
  position: relative;
  transition: background 0.2s;
}

.exp-card:hover { background: var(--bg2); }
.exp-card:hover .exp-arrow { opacity: 1; }

.exp-num {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-muted);
  letter-spacing: 0.08em;
  text-transform: uppercase;
  margin-bottom: 0.5rem;
}

.exp-title {
  font-family: var(--display);
  font-size: 0.95rem;
  font-weight: 600;
  color: var(--text-bright);
  margin-bottom: 0.55rem;
  line-height: 1.4;
  padding-right: 1.5rem;
}

.exp-desc {
  font-size: 12.5px;
  color: var(--text-muted);
  line-height: 1.65;
  margin-bottom: 1rem;
}

.exp-tags { display: flex; flex-wrap: wrap; gap: 5px; }

.etag {
  font-size: 10.5px;
  padding: 2px 8px;
  border-radius: 4px;
  background: var(--tag-bg);
  color: var(--tag-text);
  border: 1px solid var(--border);
}

.exp-arrow {
  position: absolute;
  top: 1.4rem;
  right: 1.4rem;
  color: var(--text-muted);
  font-size: 15px;
  opacity: 0;
  transition: opacity 0.2s;
}

.assign-list {
  display: flex;
  flex-direction: column;
  gap: 1px;
  background: var(--border);
  border: 1px solid var(--border);
  border-radius: 12px;
  overflow: hidden;
}

.assign-row {
  background: var(--bg);
  padding: 1.4rem 1.8rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
  text-decoration: none;
  color: inherit;
  transition: background 0.2s;
}

.assign-row:hover { background: var(--bg2); }

.assign-kind {
  font-size: 10.5px;
  font-weight: 600;
  color: var(--accent);
  text-transform: uppercase;
  letter-spacing: 0.1em;
  margin-bottom: 4px;
  opacity: 0.85;
}

.assign-title {
  font-family: var(--display);
  font-size: 0.92rem;
  font-weight: 600;
  color: var(--text-bright);
  margin-bottom: 8px;
}

.assign-btn {
  flex-shrink: 0;
  font-size: 12px;
  font-weight: 500;
  padding: 7px 16px;
  border: 1px solid var(--border2);
  color: var(--text-muted);
  border-radius: 6px;
  transition: border-color 0.2s, color 0.2s;
  white-space: nowrap;
}

.assign-row:hover .assign-btn { border-color: var(--accent-border); color: var(--accent); }

.profile-strip {
  background: var(--bg2);
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
  padding: 2.5rem 0;
}

.profile-inner {
  max-width: 1100px;
  margin: 0 auto;
  padding: 0 2.5rem;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 2rem;
}

.pi-label {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.1em;
  margin-bottom: 5px;
}

.pi-val {
  font-family: var(--display);
  font-size: 1rem;
  font-weight: 600;
  color: var(--text-bright);
}

.pi-sub { font-size: 11.5px; color: var(--text-muted); margin-top: 2px; }

footer {
  max-width: 1100px;
  margin: 0 auto;
  padding: 2.2rem 2.5rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 1rem;
  border-top: 1px solid var(--border);
}

.f-name {
  font-family: var(--display);
  font-weight: 600;
  font-size: 14px;
  color: var(--text-bright);
}

.f-sub { font-size: 12px; color: var(--text-muted); margin-top: 2px; }

.f-right { font-size: 12px; color: var(--text-muted); text-align: right; line-height: 1.7; }

.fade {
  opacity: 0;
  transform: translateY(14px);
  animation: fadeUp 0.5s ease forwards;
}

@keyframes fadeUp { to { opacity: 1; transform: none; } }

.fade:nth-child(1) { animation-delay: 0.04s; }
.fade:nth-child(2) { animation-delay: 0.10s; }
.fade:nth-child(3) { animation-delay: 0.16s; }
.fade:nth-child(4) { animation-delay: 0.22s; }
.fade:nth-child(5) { animation-delay: 0.28s; }
.fade:nth-child(6) { animation-delay: 0.34s; }

@media (max-width: 640px) {
  nav { padding: 0 1.2rem; }
  .nav-links { gap: 1.2rem; }
  .hero, .section { padding-left: 1.2rem; padding-right: 1.2rem; }
  .profile-inner { padding: 0 1.2rem; }
  footer { padding: 2rem 1.2rem; }
  .assign-row { flex-direction: column; align-items: flex-start; }
}
</style>
</head>
<body>

<nav>
  <a class="nav-logo" href="#">C&amp;D <span>Lab</span></a>
  <ul class="nav-links">
    <li><a href="#experiments">Experiments</a></li>
    <li><a href="#assignments">Assignments</a></li>
    <li><a href="#profile">Profile</a></li>
  </ul>
</nav>

<div class="hero">
  <div class="hero-eyebrow">Lab File · 2024–25</div>
  <h1 class="hero-title">Containerization<br>&amp; <span>DevOps</span> Lab</h1>
  <p class="hero-sub">A complete record of experiments and assignments covering Docker, container orchestration, infrastructure automation, and cloud-native technologies.</p>
  <div class="hero-badges">
    <div class="badge"><strong>Student</strong>Chitranshi Maheshwari</div>
    <div class="badge"><strong>SAP</strong>500120287</div>
    <div class="badge"><strong>Roll</strong>R2142231114</div>
    <div class="badge"><strong>Subject</strong>Containerization &amp; DevOps</div>
  </div>
  <div class="stats-row">
    <div class="stat">
      <div class="stat-num">6</div>
      <div class="stat-label">Experiments</div>
    </div>
    <div class="stat-div"></div>
    <div class="stat">
      <div class="stat-num">2</div>
      <div class="stat-label">Assignments</div>
    </div>
    <div class="stat-div"></div>
    <div class="stat">
      <div class="stat-num">5+</div>
      <div class="stat-label">Technologies</div>
    </div>
  </div>
</div>

<div class="profile-strip" id="profile">
  <div class="profile-inner">
    <div>
      <div class="pi-label">Student Name</div>
      <div class="pi-val">Chitranshi Maheshwari</div>
    </div>
    <div>
      <div class="pi-label">SAP ID</div>
      <div class="pi-val">500120287</div>
    </div>
    <div>
      <div class="pi-label">Roll Number</div>
      <div class="pi-val">R2142231114</div>
    </div>
    <div>
      <div class="pi-label">Subject</div>
      <div class="pi-val">Containerization &amp; DevOps</div>
      <div class="pi-sub">Docker · Ansible · Kubernetes</div>
    </div>
  </div>
</div>

<div class="section" id="experiments">
  <div class="section-head">
    <span class="section-label">01</span>
    <h2 class="section-title">Lab Experiments</h2>
    <div class="section-rule"></div>
  </div>
  <div class="exp-grid">

    <a class="exp-card fade" href="./Exp-1/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 01</div>
      <div class="exp-title">Virtual Machines vs Containers</div>
      <div class="exp-desc">Comparative study of VMs and containers covering infrastructure provisioning, VM-based deployment, containerized execution, and architectural differences in isolation and performance.</div>
      <div class="exp-tags">
        <span class="etag">Ubuntu</span><span class="etag">VirtualBox</span><span class="etag">Vagrant</span><span class="etag">Docker</span><span class="etag">Nginx</span>
      </div>
    </a>

    <a class="exp-card fade" href="./Exp-2/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 02</div>
      <div class="exp-title">Docker Installation &amp; Container Lifecycle</div>
      <div class="exp-desc">Core Docker concepts including pulling images, running containers with port mapping, handling port conflicts, and managing the full container lifecycle.</div>
      <div class="exp-tags">
        <span class="etag">Docker</span><span class="etag">Port Mapping</span><span class="etag">Image Pull</span><span class="etag">Lifecycle</span>
      </div>
    </a>

    <a class="exp-card fade" href="./Exp-3/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 03</div>
      <div class="exp-title">Custom Docker Images — Ubuntu &amp; Alpine NGINX</div>
      <div class="exp-desc">Build custom Docker images using Ubuntu 22.04 and Alpine Linux with NGINX, including Dockerfiles, custom tagging, and comparing image size vs performance.</div>
      <div class="exp-tags">
        <span class="etag">Dockerfile</span><span class="etag">Alpine Linux</span><span class="etag">Ubuntu 22.04</span><span class="etag">NGINX</span>
      </div>
    </a>

    <a class="exp-card fade" href="./Exp-4/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 04</div>
      <div class="exp-title">Docker Swarm &amp; Container Orchestration</div>
      <div class="exp-desc">Initialize and manage a Docker Swarm cluster with manager/worker nodes, join tokens, service deployment, replica scaling, and high availability concepts.</div>
      <div class="exp-tags">
        <span class="etag">Docker Swarm</span><span class="etag">Overlay Networks</span><span class="etag">Load Balancing</span><span class="etag">HA</span>
      </div>
    </a>

    <a class="exp-card fade" href="./Exp-5/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 05</div>
      <div class="exp-title">Docker Volumes, Env Variables, Monitoring &amp; Networking</div>
      <div class="exp-desc">Container storage using volumes and bind mounts, environment variable configuration, performance monitoring with docker stats, and multi-network communication.</div>
      <div class="exp-tags">
        <span class="etag">Volumes</span><span class="etag">Bind Mounts</span><span class="etag">Bridge Network</span><span class="etag">Docker Stats</span>
      </div>
    </a>

    <a class="exp-card fade" href="./Exp-9/">
      <div class="exp-arrow">↗</div>
      <div class="exp-num">Experiment 09</div>
      <div class="exp-title">Ansible — Configuration Management</div>
      <div class="exp-desc">Provisioning and config management using Ansible on macOS with a Dockerized Ubuntu SSH server as managed node — a lightweight local lab for infrastructure automation.</div>
      <div class="exp-tags">
        <span class="etag">Ansible 2.20.4</span><span class="etag">Ubuntu 24.04</span><span class="etag">OpenSSH</span><span class="etag">Homebrew</span>
      </div>
    </a>

  </div>
</div>

<div class="section" id="assignments">
  <div class="section-head">
    <span class="section-label">02</span>
    <h2 class="section-title">Assignments</h2>
    <div class="section-rule"></div>
  </div>
  <div class="assign-list">

    <a class="assign-row" href="https://github.com/chitranshimaheshwari02/container-project/blob/main/README.md" target="_blank">
      <div>
        <div class="assign-kind">Assignment 01</div>
        <div class="assign-title">Containerized Web App with PostgreSQL — Docker Compose &amp; Macvlan/Ipvlan</div>
        <div class="exp-tags">
          <span class="etag">Docker Compose</span><span class="etag">PostgreSQL</span><span class="etag">Macvlan</span><span class="etag">Ipvlan</span>
        </div>
      </div>
      <div class="assign-btn">View on GitHub ↗</div>
    </a>

    <a class="assign-row" href="https://github.com/chitranshimaheshwari02/Container-Lab-File/blob/main/Class_assignments/Task" target="_blank">
      <div>
        <div class="assign-kind">Class Assignment</div>
        <div class="assign-title">Kubernetes Pod &amp; Deployment Experiment</div>
        <div class="exp-tags">
          <span class="etag">Kubernetes</span><span class="etag">Pods</span><span class="etag">Deployments</span><span class="etag">K8s YAML</span>
        </div>
      </div>
      <div class="assign-btn">View on GitHub ↗</div>
    </a>

  </div>
</div>

<footer>
  <div>
    <div class="f-name">Chitranshi Maheshwari</div>
    <div class="f-sub">Containerization &amp; DevOps — Lab File · 2024–25</div>
  </div>
  <div class="f-right">
    SAP: 500120287<br>Roll: R2142231114
  </div>
</footer>

</body>
</html>
