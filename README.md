 Real-Time Polling System under Adversarial Conditions
 Problem :Design a real-time polling system where users vote and view live results under high traffic and unreliable network conditions. The system must ensure fair voting, accurate aggregation, and consistent real-time updates despite duplicates, weak identity, and distributed processing delays. 
 Key Features:
 • Real-time vote ingestion with live result streaming 
 • Idempotent vote handling to prevent duplicate counting
 • Fair voting without strong user identity verification
 • Consistency maintenance across distributed processing nodes 
 • Adversarial resilience: duplicate requests, spam, and network delay handling 
 <style>
  @import url('https://fonts.googleapis.com/css2?family=Sora:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0
  }

  :root {
    --bg: #0a0f1e;
    --surface: #111827;
    --surface2: #1a2235;
    --accent: #3b82f6;
    --accent2: #22d3ee;
    --text: #f0f4ff;
    --muted: #7c8db5;
    --border: #1e2d4a;
    --danger: #ef4444;
    --success: #22c55e;
    --warn: #f59e0b;
    --font: 'Sora', sans-serif;
    --mono: 'IBM Plex Mono', monospace;
  }

  body {
    font-family: var(--font);
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    flex-direction: column
  }

  /* NAV */
  nav {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 0 2rem;
    height: 56px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 100
  }

  .logo {
    font-weight: 600;
    font-size: 15px;
    display: flex;
    align-items: center;
    gap: 8px
  }

  .logo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--accent2);
    animation: pulse 2s infinite
  }

  @keyframes pulse {

    0%,
    100% {
      opacity: 1;
      transform: scale(1)
    }

    50% {
      opacity: .5;
      transform: scale(1.3)
    }
  }

  .nav-tag {
    font-size: 11px;
    font-family: var(--mono);
    color: var(--accent2);
    background: rgba(34, 211, 238, .1);
    padding: 3px 8px;
    border-radius: 4px;
    border: 1px solid rgba(34, 211, 238, .2)
  }

  .nav-right {
    display: flex;
    align-items: center;
    gap: 12px
  }

  .live-badge {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    font-family: var(--mono);
    color: var(--success)
  }

  .live-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--success);
    animation: pulse 1.2s infinite
  }

  /* PAGES */
  .page {
    display: none;
    flex: 1;
    animation: fadeIn .3s ease
  }

  .page.active {
    display: flex
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(8px)
    }

    to {
      opacity: 1;
      transform: translateY(0)
    }
  }

  /* LOGIN PAGE */
  #loginPage {
    align-items: center;
    justify-content: center;
    padding: 2rem
  }

  .login-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 2.5rem;
    width: 100%;
    max-width: 420px
  }

  .login-header {
    text-align: center;
    margin-bottom: 2rem
  }

  .login-icon {
    width: 56px;
    height: 56px;
    border-radius: 14px;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22px;
    margin: 0 auto 1rem
  }

  .login-title {
    font-size: 20px;
    font-weight: 600;
    margin-bottom: .35rem
  }

  .login-sub {
    font-size: 13px;
    color: var(--muted)
  }

  .field {
    margin-bottom: 1.25rem
  }

  .field label {
    display: block;
    font-size: 12px;
    font-weight: 500;
    color: var(--muted);
    margin-bottom: .5rem;
    font-family: var(--mono);
    letter-spacing: .05em;
    text-transform: uppercase
  }

  .field input {
    width: 100%;
    background: var(--bg);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: .7rem 1rem;
    font-size: 14px;
    font-family: var(--font);
    color: var(--text);
    outline: none;
    transition: border-color .2s
  }

  .field input:focus {
    border-color: var(--accent)
  }

  .field input::placeholder {
    color: var(--muted)
  }

  .btn-primary {
    width: 100%;
    background: var(--accent);
    color: #fff;
    border: none;
    border-radius: 8px;
    padding: .8rem;
    font-size: 14px;
    font-weight: 600;
    font-family: var(--font);
    cursor: pointer;
    transition: opacity .2s, transform .1s
  }

  .btn-primary:hover {
    opacity: .9
  }

  .btn-primary:active {
    transform: scale(.98)
  }

  .login-footer {
    margin-top: 1.5rem;
    display: flex;
    align-items: center;
    gap: .75rem
  }

  .shield-item {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    color: var(--muted)
  }

  .sep {
    color: var(--border)
  }

  .error-msg {
    background: rgba(239, 68, 68, .1);
    border: 1px solid rgba(239, 68, 68, .3);
    color: #fca5a5;
    border-radius: 6px;
    padding: .6rem 1rem;
    font-size: 12px;
    margin-bottom: 1rem;
    display: none
  }

  .success-msg {
    background: rgba(34, 197, 94, .08);
    border: 1px solid rgba(34, 197, 94, .3);
    color: #bbf7d0;
    border-radius: 6px;
    padding: .6rem 1rem;
    font-size: 12px;
    margin-bottom: 1rem;
    display: none
  }

  .small-link {
    font-size: 12px;
    color: var(--accent2);
    cursor: pointer;
    text-decoration: underline;
    background: none;
    border: none;
    padding: 0;
    font-family: var(--font);
  }

  .otp-row {
    display: flex;
    gap: .75rem;
    align-items: flex-end;
    flex-wrap: wrap
  }

  .otp-row .field {
    flex: 1;
    min-width: 180px;
    margin-bottom: 0
  }

  /* DASHBOARD PAGE */
  #dashPage {
    flex-direction: column;
    padding: 1.5rem 2rem;
    gap: 1.5rem
  }

  .dash-header {
    display: flex;
    align-items: center;
    justify-content: space-between
  }

  .dash-title {
    font-size: 18px;
    font-weight: 600
  }

  .dash-sub {
    font-size: 12px;
    color: var(--muted);
    margin-top: 3px;
    font-family: var(--mono)
  }

  .btn-sm {
    background: transparent;
    border: 1px solid var(--border);
    color: var(--muted);
    border-radius: 6px;
    padding: .35rem .8rem;
    font-size: 12px;
    font-family: var(--font);
    cursor: pointer;
    transition: all .2s
  }

  .btn-sm:hover {
    border-color: var(--accent);
    color: var(--text)
  }

  .btn-danger {
    border-color: rgba(239, 68, 68, .4);
    color: #fca5a5
  }

  .btn-danger:hover {
    background: rgba(239, 68, 68, .1);
    border-color: var(--danger)
  }

  /* STATS ROW */
  .stats {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px
  }

  .stat-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 1rem
  }

  .stat-label {
    font-size: 11px;
    color: var(--muted);
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .05em;
    margin-bottom: .4rem
  }

  .stat-val {
    font-size: 22px;
    font-weight: 600;
    font-family: var(--mono)
  }

  .stat-change {
    font-size: 11px;
    color: var(--success);
    margin-top: .2rem
  }

  .stat-danger {
    color: var(--danger)
  }

  /* POLL CARD */
  .poll-section {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.5rem
  }

  .poll-title {
    font-size: 15px;
    font-weight: 600;
    margin-bottom: .3rem
  }

  .poll-meta {
    font-size: 11px;
    color: var(--muted);
    font-family: var(--mono);
    margin-bottom: 1.25rem
  }

  .options-grid {
    display: flex;
    flex-direction: column;
    gap: .75rem
  }

  .opt-row {
    display: flex;
    align-items: center;
    gap: 12px
  }

  .opt-btn {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: .6rem 1rem;
    font-size: 13px;
    font-family: var(--font);
    color: var(--text);
    cursor: pointer;
    min-width: 150px;
    text-align: left;
    transition: all .2s;
    white-space: nowrap
  }

  .opt-btn:hover:not(:disabled) {
    border-color: var(--accent);
    background: rgba(59, 130, 246, .08)
  }

  .opt-btn.voted {
    border-color: var(--success);
    background: rgba(34, 197, 94, .1);
    color: var(--success);
    cursor: default
  }

  .opt-btn:disabled {
    cursor: default
  }

  .bar-wrap {
    flex: 1;
    background: var(--bg);
    border-radius: 100px;
    height: 8px;
    overflow: hidden
  }

  .bar {
    height: 100%;
    border-radius: 100px;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    transition: width .6s ease
  }

  .pct {
    font-size: 12px;
    font-family: var(--mono);
    color: var(--muted);
    min-width: 38px;
    text-align: right
  }

  .vote-count {
    font-size: 11px;
    color: var(--muted);
    font-family: var(--mono)
  }

  .voted-notice {
    font-size: 12px;
    color: var(--success);
    margin-top: 1rem;
    display: none;
    align-items: center;
    gap: 6px
  }

  /* SECURITY LOG */
  .two-col {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px
  }

  .log-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem
  }

  .log-title {
    font-size: 13px;
    font-weight: 600;
    margin-bottom: 1rem;
    display: flex;
    align-items: center;
    justify-content: space-between
  }

  .log-count {
    font-size: 11px;
    font-family: var(--mono);
    color: var(--muted)
  }

  .log-list {
    display: flex;
    flex-direction: column;
    gap: .5rem;
    max-height: 160px;
    overflow-y: auto
  }

  .log-item {
    font-size: 11px;
    font-family: var(--mono);
    padding: .4rem .6rem;
    border-radius: 5px;
    display: flex;
    align-items: center;
    gap: .5rem
  }

  .log-item.blocked {
    background: rgba(239, 68, 68, .07);
    color: #fca5a5;
    border: 1px solid rgba(239, 68, 68, .15)
  }

  .log-item.ok {
    background: rgba(34, 197, 94, .07);
    color: #86efac;
    border: 1px solid rgba(34, 197, 94, .15)
  }

  .log-item.warn {
    background: rgba(245, 158, 11, .07);
    color: #fcd34d;
    border: 1px solid rgba(245, 158, 11, .15)
  }

  .log-time {
    color: var(--muted);
    min-width: 40px
  }

  /* NODE STATUS */
  .nodes-grid {
    display: flex;
    flex-direction: column;
    gap: .5rem
  }

  .node-row {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 12px;
    font-family: var(--mono)
  }

  .node-dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    flex-shrink: 0
  }

  .n-online {
    background: var(--success)
  }

  .n-warn {
    background: var(--warn);
    animation: pulse 1.5s infinite
  }

  .n-offline {
    background: var(--danger)
  }

  .node-name {
    color: var(--text);
    flex: 1
  }

  .node-lat {
    color: var(--muted)
  }

  .node-status {
    font-size: 10px;
    padding: 1px 6px;
    border-radius: 3px
  }

  .ns-ok {
    background: rgba(34, 197, 94, .12);
    color: #86efac
  }

  .ns-warn {
    background: rgba(245, 158, 11, .12);
    color: #fcd34d
  }

  .ns-off {
    background: rgba(239, 68, 68, .12);
    color: #fca5a5
  }

  /* VOTER HEADER */
  .voter-bar {
    display: flex;
    align-items: center;
    gap: 8px
  }

  .voter-chip {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: .25rem .7rem;
    font-size: 11px;
    font-family: var(--mono);
    color: var(--muted)
  }

  .voter-chip span {
    color: var(--accent2)
  }
</style>

<nav>
  <div class="logo">
    <div class="logo-dot"></div>
    FS-08 &nbsp;<span style="color:var(--muted);font-weight:400">PollStream</span>
  </div>
  <div class="nav-right">
    <div class="live-badge" id="liveBadge" style="display:none">
      <div class="live-dot"></div>LIVE
    </div>
    <div class="nav-tag">v2.4.1</div>
  </div>
</nav>

<!-- LOGIN PAGE -->
<div class="page active" id="loginPage">
  <div class="login-card">
    <div class="login-header">
      <div class="login-icon">🗳</div>
      <div class="login-title">Voter Authentication</div>
      <div class="login-sub" id="loginSubtitle">Enter your Voter ID and password to access the secure polling system
      </div>
    </div>
    <div class="error-msg" id="errMsg">Invalid Voter ID or password. Please try again.</div>
    <div class="success-msg" id="successMsg">Registration completed. You can now login.</div>

    <div id="loginSection">
      <div class="field">
        <label>Voter ID</label>
        <input type="text" id="voterIdInput" placeholder="e.g. VTR-2024-001234" maxlength="20" />
      </div>
      <div class="field">
        <label>Password</label>
        <input type="password" id="passInput" placeholder="Enter your password" />
      </div>
      <button class="btn-primary" id="loginBtn" onclick="doLogin()">Authenticate &amp; Enter</button>
      <div class="login-footer">
        <div class="shield-item">🔒 Encrypted</div>
        <span class="sep">·</span>
        <div class="shield-item">🛡 Idempotent</div>
        <span class="sep">·</span>
        <div class="shield-item">⚡ Real-Time</div>
      </div>
      <div style="margin-top:1rem;text-align:center;">
        <button class="btn-sm" onclick="showRegisterMode()">New user? Register with OTP</button>
      </div>
    </div>

    <div id="registerSection" style="display:none">
      <div class="field">
        <label>Voter ID</label>
        <input type="text" id="regVoterIdInput" placeholder="e.g. VTR-2024-007777" maxlength="20" />
      </div>
      <div class="field">
        <label>Password</label>
        <input type="password" id="regPassInput" placeholder="Choose a password" />
      </div>
      <div class="field">
        <label>Confirm Password</label>
        <input type="password" id="regPassConfirmInput" placeholder="Re-enter password" />
      </div>
      <div class="field">
        <label>Mobile / Email for OTP</label>
        <input type="text" id="regMobileInput" placeholder="Enter phone or email" />
      </div>
      <div class="field" id="otpField" style="display:none">
        <label>OTP Code</label>
        <input type="text" id="otpInput" placeholder="Enter the 6-digit OTP" maxlength="6" />
      </div>
      <div style="display:flex;gap:.75rem;flex-wrap:wrap;margin-top:1rem;">
        <button class="btn-primary" id="sendOtpBtn" onclick="sendOTP()">Send OTP</button>
        <button class="btn-primary" id="registerBtn" onclick="doRegister()" disabled>Verify &amp; Register</button>
      </div>
      <div class="login-footer" style="margin-top:1rem;justify-content:center;">
        <button class="btn-sm" onclick="showLoginMode()">Back to login</button>
      </div>
    </div>
  </div>
</div>

<!-- DASHBOARD PAGE -->
<div class="page" id="dashPage">
  <div class="dash-header">
    <div>
      <div class="dash-title">Live Polling Dashboard</div>
      <div class="dash-sub" id="dashSub">General Election — Constituency Poll · Session active</div>
    </div>
    <div class="voter-bar">
      <div class="voter-chip">Voter: <span id="voterDisplay">—</span></div>
      <button class="btn-sm btn-danger" onclick="doLogout()">Logout</button>
    </div>
  </div>

  <!-- STATS -->
  <div class="stats">
    <div class="stat-card">
      <div class="stat-label">Total Votes</div>
      <div class="stat-val" id="statTotal">0</div>
      <div class="stat-change" id="statRate">+0/min</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Duplicates Blocked</div>
      <div class="stat-val stat-danger" id="statDupe">0</div>
      <div class="stat-change" style="color:var(--warn)" id="statSpam">0 spam</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Active Nodes</div>
      <div class="stat-val" id="statNodes">3/3</div>
      <div class="stat-change" id="statSync">Synced</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Delay Avg (ms)</div>
      <div class="stat-val" id="statDelay">42</div>
      <div class="stat-change" id="statDelayTrend">▼ stable</div>
    </div>
  </div>

  <!-- POLL -->
  <div class="poll-section">
    <div class="poll-title" id="pollQuestion">Which infrastructure project should receive priority funding?</div>
    <div class="poll-meta" id="pollMeta">Poll ID: PLQ-2024-0047 · Closes: 18:00 IST · Idempotent voting enabled</div>
    <div class="options-grid" id="optionsGrid"></div>
    <div class="voted-notice" id="votedNotice">✅ Your vote has been recorded. You cannot vote again on this poll.</div>
  </div>

  <!-- LOGS + NODES -->
  <div class="two-col">
    <div class="log-card">
      <div class="log-title">Security Log <span class="log-count" id="logCount">0 events</span></div>
      <div class="log-list" id="logList"></div>
    </div>
    <div class="log-card">
      <div class="log-title">Distributed Nodes <span class="log-count" id="nodeTime">Last sync: now</span></div>
      <div class="nodes-grid" id="nodeGrid"></div>
    </div>
  </div>
</div>

<script>
  const VOTERS = {
    "VTR-2024-001234": "pass123",
    "VTR-2024-005678": "secure99",
    "VTR-2024-009999": "vote2024",
    "ADMIN-001": "admin",
  };
  const OPTIONS = [
    { id: "a", label: "High-Speed Rail Expansion" },
    { id: "b", label: "Renewable Energy Grid" },
    { id: "c", label: "Urban Water Infrastructure" },
    { id: "d", label: "Digital Broadband Network" },
  ];
  const NODES = [
    { name: "node-primary-blr", region: "Bengaluru", lat: "12ms", status: "online" },
    { name: "node-secondary-mum", region: "Mumbai", lat: "28ms", status: "online" },
    { name: "node-backup-del", region: "Delhi", lat: "45ms", status: "warn" },
  ];
  const LOGS_INIT = [
    { type: "blocked", msg: "Duplicate vote blocked", voter: "VTR-2024-9021" },
    { type: "warn", msg: "Rate limit triggered", voter: "VTR-2024-3355" },
    { type: "ok", msg: "Vote accepted + replicated", voter: "VTR-2024-1122" },
    { type: "blocked", msg: "Spam pattern detected", voter: "ANON-7788" },
    { type: "ok", msg: "Node sync successful", voter: "node-secondary" },
  ];

  let state = {
    loggedIn: false,
    currentVoter: null,
    voted: false,
    votedOption: null,
    counts: { a: 0, b: 0, c: 0, d: 0 },
    totalVotes: 482,
    dupesBlocked: 17,
    spam: 5,
    logs: [],
    logCounter: 0,
  };

  let otpState = {
    code: null,
    expires: null,
    target: null,
    voterId: null,
    password: null,
  };

  function showRegisterMode() {
    document.getElementById('loginSection').style.display = 'none';
    document.getElementById('registerSection').style.display = 'block';
    document.getElementById('loginSubtitle').textContent = 'Create a new voter account and verify with OTP.';
    document.getElementById('errMsg').style.display = 'none';
    document.getElementById('successMsg').style.display = 'none';
  }

  function showLoginMode() {
    document.getElementById('loginSection').style.display = 'block';
    document.getElementById('registerSection').style.display = 'none';
    document.getElementById('loginSubtitle').textContent = 'Enter your Voter ID and password to access the secure polling system';
    document.getElementById('errMsg').style.display = 'none';
    resetRegistrationForm();
  }

  function resetRegistrationForm() {
    document.getElementById('regVoterIdInput').value = '';
    document.getElementById('regPassInput').value = '';
    document.getElementById('regPassConfirmInput').value = '';
    document.getElementById('regMobileInput').value = '';
    document.getElementById('otpInput').value = '';
    document.getElementById('otpField').style.display = 'none';
    document.getElementById('registerBtn').disabled = true;
    document.getElementById('sendOtpBtn').textContent = 'Send OTP';
    otpState = { code: null, expires: null, target: null, voterId: null, password: null };
  }

  function formatVoterId(id) {
    return id.trim().toUpperCase();

  }

  function sendOTP() {
    const voterId = formatVoterId(document.getElementById('regVoterIdInput').value);
    const password = document.getElementById('regPassInput').value;
    const confirm = document.getElementById('regPassConfirmInput').value;
    const contact = document.getElementById('regMobileInput').value.trim();
    const err = document.getElementById('errMsg');
    const success = document.getElementById('successMsg');

    err.style.display = 'none';
    success.style.display = 'none';

    if (!voterId || !password || !confirm || !contact) {
      err.textContent = 'Please complete all registration fields before sending OTP.';
      err.style.display = 'block';
      return;
    }
    if (password.length < 6) {
      err.textContent = 'Password must be at least 6 characters.';
      err.style.display = 'block';
      return;
    }
    if (password !== confirm) {
      err.textContent = 'Passwords do not match.';
      err.style.display = 'block';
      return;
    }
    if (VOTERS[voterId]) {
      err.textContent = 'This Voter ID is already registered. Use a different ID.';
      err.style.display = 'block';
      return;
    }

    const code = Math.floor(100000 + Math.random() * 900000).toString();
    const now = Date.now();
    otpState = {
      code,
      expires: now + 2 * 60 * 1000,
      target: contact,
      voterId,
      password,
    };

    document.getElementById('otpField').style.display = 'block';
    document.getElementById('registerBtn').disabled = false;
    document.getElementById('sendOtpBtn').textContent = 'Resend OTP';
    success.textContent = `OTP sent to ${contact}. Use code ${code} to verify within 2 minutes.`;
    success.style.display = 'block';
  }

  function doRegister() {
    const enteredOtp = document.getElementById('otpInput').value.trim();
    const err = document.getElementById('errMsg');
    const success = document.getElementById('successMsg');

    err.style.display = 'none';
    success.style.display = 'none';

    if (!otpState.code || !otpState.voterId) {
      err.textContent = 'Please send an OTP before registering.';
      err.style.display = 'block';
      return;
    }
    if (!enteredOtp) {
      err.textContent = 'Enter the OTP code to complete registration.';
      err.style.display = 'block';
      return;
    }
    if (Date.now() > otpState.expires) {
      err.textContent = 'OTP expired. Send a new code and try again.';
      err.style.display = 'block';
      return;
    }
    if (enteredOtp !== otpState.code) {
      err.textContent = 'OTP code is invalid. Please check and retry.';
      err.style.display = 'block';
      return;
    }

    VOTERS[otpState.voterId] = otpState.password;
    success.textContent = 'Registration successful! You can now login with your new Voter ID.';
    success.style.display = 'block';
    document.getElementById('voterIdInput').value = otpState.voterId;
    document.getElementById('passInput').value = otpState.password;
    resetRegistrationForm();
    showLoginMode();
  }

  function doLogin() {
    const vid = document.getElementById('voterIdInput').value.trim().toUpperCase();
    const pwd = document.getElementById('passInput').value;
    const err = document.getElementById('errMsg');
    const btn = document.getElementById('loginBtn');

    if (!vid || !pwd) { err.textContent = "Please enter both Voter ID and password."; err.style.display = 'block'; return; }

    btn.textContent = "Authenticating...";
    btn.disabled = true;
    err.style.display = 'none';

    setTimeout(() => {
      const normalVid = vid;
      const found = Object.keys(VOTERS).find(k => k.toUpperCase() === normalVid);
      if (found && VOTERS[found] === pwd) {
        state.loggedIn = true;
        state.currentVoter = found;
        state.counts = { a: 194, b: 143, c: 97, d: 48 };
        state.totalVotes = 482;
        showDash();
      } else {
        err.textContent = "Invalid Voter ID or password. Please try again.";
        err.style.display = 'block';
        btn.textContent = "Authenticate & Enter";
        btn.disabled = false;
      }
    }, 900);
  }

  document.getElementById('passInput').addEventListener('keydown', e => { if (e.key === 'Enter') doLogin(); });
  document.getElementById('voterIdInput').addEventListener('keydown', e => { if (e.key === 'Enter') doLogin(); });

  function showDash() {
    document.getElementById('loginPage').classList.remove('active');
    document.getElementById('dashPage').classList.add('active');
    document.getElementById('liveBadge').style.display = 'flex';
    document.getElementById('voterDisplay').textContent = state.currentVoter;

    renderOptions();
    renderNodes();
    state.logs = [...LOGS_INIT];
    renderLogs();
    updateStats();

    startSimulation();
  }

  function doLogout() {
    state = { ...state, loggedIn: false, currentVoter: null, voted: false, votedOption: null };
    clearInterval(state._sim);
    document.getElementById('dashPage').classList.remove('active');
    document.getElementById('loginPage').classList.add('active');
    document.getElementById('liveBadge').style.display = 'none';
    document.getElementById('voterIdInput').value = '';
    document.getElementById('passInput').value = '';
    document.getElementById('loginBtn').textContent = 'Authenticate & Enter';
    document.getElementById('loginBtn').disabled = false;
    document.getElementById('errMsg').style.display = 'none';
  }

  function renderOptions() {
    const total = Object.values(state.counts).reduce((a, b) => a + b, 0);
    const grid = document.getElementById('optionsGrid');
    grid.innerHTML = '';
    OPTIONS.forEach(opt => {
      const votes = state.counts[opt.id];
      const pct = total > 0 ? Math.round(votes / total * 100) : 0;
      const isVoted = state.voted && state.votedOption === opt.id;
      const row = document.createElement('div');
      row.className = 'opt-row';
      row.innerHTML = `
      <button class="opt-btn${isVoted ? ' voted' : ''}" onclick="castVote('${opt.id}')" ${state.voted ? 'disabled' : ''}>${opt.label}</button>
      <div class="bar-wrap"><div class="bar" style="width:${pct}%"></div></div>
      <span class="pct">${pct}%</span>
      <span class="vote-count">${votes.toLocaleString()}</span>
    `;
      grid.appendChild(row);
    });

    const notice = document.getElementById('votedNotice');
    notice.style.display = state.voted ? 'flex' : 'none';
  }

  function castVote(optId) {
    if (state.voted) {
      addLog('blocked', 'Duplicate vote prevented', state.currentVoter);
      state.dupesBlocked++;
      updateStats();
      return;
    }
    state.voted = true;
    state.votedOption = optId;
    state.counts[optId]++;
    state.totalVotes++;
    addLog('ok', `Vote accepted [${optId.toUpperCase()}]`, state.currentVoter);
    renderOptions();
    updateStats();
  }

  function updateStats() {
    document.getElementById('statTotal').textContent = state.totalVotes.toLocaleString();
    document.getElementById('statDupe').textContent = state.dupesBlocked;
    document.getElementById('statSpam').textContent = state.spam + ' spam';
    const delay = 35 + Math.round(Math.random() * 20);
    document.getElementById('statDelay').textContent = delay;
    document.getElementById('statDelayTrend').textContent = delay < 50 ? '▼ stable' : '▲ elevated';
  }

  function renderNodes() {
    const grid = document.getElementById('nodeGrid');
    grid.innerHTML = '';
    NODES.forEach(n => {
      const dot = n.status === 'online' ? 'n-online' : n.status === 'warn' ? 'n-warn' : 'n-offline';
      const badge = n.status === 'online' ? 'ns-ok' : n.status === 'warn' ? 'ns-warn' : 'ns-off';
      const label = n.status === 'online' ? 'healthy' : n.status === 'warn' ? 'degraded' : 'offline';
      const row = document.createElement('div');
      row.className = 'node-row';
      row.innerHTML = `<div class="node-dot ${dot}"></div><span class="node-name">${n.name}</span><span class="node-lat">${n.lat}</span><span class="node-status ${badge}">${label}</span>`;
      grid.appendChild(row);
    });
  }

  function addLog(type, msg, voter) {
    const now = new Date();
    const t = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0') + ':' + now.getSeconds().toString().padStart(2, '0');
    state.logs.unshift({ type, msg, voter, time: t });
    if (state.logs.length > 20) state.logs.pop();
    renderLogs();
  }

  function renderLogs() {
    const list = document.getElementById('logList');
    list.innerHTML = '';
    state.logs.slice(0, 8).forEach(l => {
      const div = document.createElement('div');
      div.className = `log-item ${l.type}`;
      div.innerHTML = `<span class="log-time">${l.time || '--:--'}</span><span>${l.msg}</span><span style="margin-left:auto;opacity:.6">${l.voter}</span>`;
      list.appendChild(div);
    });
    document.getElementById('logCount').textContent = state.logs.length + ' events';
  }

  function startSimulation() {
    const bgVoters = ['VTR-2024-3312', 'VTR-2024-7743', 'VTR-2024-0099', 'VTR-2024-5566', 'ANON-9921'];
    const blockedVoters = ['VTR-2024-2222', 'SPAM-BOT-77', 'FLOOD-0011'];
    const logTypes = [
      () => { const v = bgVoters[Math.floor(Math.random() * bgVoters.length)]; const opt = ['a', 'b', 'c', 'd'][Math.floor(Math.random() * 4)]; state.counts[opt]++; state.totalVotes++; addLog('ok', 'Vote accepted [' + opt.toUpperCase() + ']', v); },
      () => { const v = blockedVoters[Math.floor(Math.random() * blockedVoters.length)]; state.dupesBlocked++; state.spam++; addLog('blocked', 'Spam/duplicate blocked', v); },
      () => { addLog('warn', 'Network jitter detected', 'node-backup-del'); },
      () => { const v = bgVoters[Math.floor(Math.random() * bgVoters.length)]; state.counts[['a', 'b', 'c', 'd'][Math.floor(Math.random() * 4)]]++; state.totalVotes++; addLog('ok', 'Vote + replication success', v); },
    ];

    state._sim = setInterval(() => {
      const fn = logTypes[Math.floor(Math.random() * logTypes.length)];
      fn();
      renderOptions();
      updateStats();
      document.getElementById('nodeTime').textContent = 'Last sync: just now';
      renderNodes();
    }, 2200);
  }
</script>
