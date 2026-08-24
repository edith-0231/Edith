/* ==========================================================
   LIVE PERIOD — engine
   ========================================================== */

const STORAGE_KEY = 'liveperiod_data_v1';
const DAY_MS = 24 * 60 * 60 * 1000;
const PHASE_ORDER = ['menstrual', 'follicular', 'ovulation', 'luteal'];
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
const isFinePointer = window.matchMedia('(hover:hover) and (pointer:fine)').matches;
const isTouchDevice = window.matchMedia('(hover:none), (pointer:coarse)').matches || window.innerWidth <= 640;

// ---- Phase content library ----
const PHASE_META = {
  menstrual: {
    label: 'Menstrual Phase', world: 'Menstrual Phase', mantra: 'Rest and recover',
    order: 0, colors: { c1: '#2E0A14', c2: '#160409', glow: '#FF3B5C', text: '#F6E7EA' },
    nextLabel: 'Follicular Phase',
    insight: 'Energy tends to sit at its lowest here — many people notice they need more rest in these first few days.',
    do: ['Rest when you can, and let plans flex', 'Use warmth — a heating pad or warm bath for cramps', 'Move gently — a slow walk or restorative yoga', 'Eat iron-rich food (leafy greens, lentils, meat)', 'Track flow and pain'],
    avoid: ['Pushing through intense workouts', 'Excess caffeine or alcohol', 'Tight or restrictive clothing', 'Ignoring pain that feels unusually severe'],
    need: ['Pads, tampons or a cup', 'A heating pad', 'Extra water', 'Comfortable, loose clothing', 'A spare pair of underwear on hand'],
  },
  follicular: {
    label: 'Follicular Phase', world: 'Follicular Phase', mantra: 'Build momentum',
    order: 1, colors: { c1: '#122A1A', c2: '#070F0B', glow: '#A8E6B0', text: '#EAF4EC' },
    nextLabel: 'Ovulation Phase',
    insight: 'Energy typically climbs from here as hormones rise — a good window for new starts and harder training.',
    do: ['Start new projects while motivation is rising', 'Add strength training back in', 'Try new foods or routines', 'Make plans — social energy is climbing', 'Get sunlight where possible'],
    avoid: ['Overloading the calendar before energy actually peaks', 'Skipping protein at meals', 'Assuming a low-energy day is the new normal'],
    need: ['Light layers for changing energy', 'A journal or planner', 'Regular workout gear', 'Balanced, protein-forward meals'],
  },
  ovulation: {
    label: 'Ovulation Phase', world: 'Ovulation Phase', mantra: 'Peak energy',
    order: 2, colors: { c1: '#2E1D08', c2: '#140D04', glow: '#FFCB5C', text: '#FBF1DE' },
    nextLabel: 'Luteal Phase',
    insight: 'This is usually the highest-energy point of the cycle for most people — a natural peak for effort and connection.',
    do: ['Schedule the big, high-energy things here', 'High-intensity workouts, if that is your style', 'Notice fertile signs if you are tracking them', 'Stay well hydrated'],
    avoid: ['Unprotected sex if you are avoiding pregnancy', 'Ignoring sharp one-sided pain (mittelschmerz is common but should stay mild)', 'Overbooking recovery-free days back to back'],
    need: ['Ovulation test strips, if you are tracking fertility', 'Contraception, if relevant to you', 'A water bottle you will actually refill'],
  },
  luteal: {
    label: 'Luteal Phase', world: 'Luteal Phase', mantra: 'Slow and prepare',
    order: 3, colors: { c1: '#14122E', c2: '#08070F', glow: '#8C6FE0', text: '#ECE8FA' },
    nextLabel: 'Next Period',
    insight: 'Energy commonly dips through this phase, especially in the final days — PMS symptoms are hormonal, not a character flaw.',
    do: ['Prioritise sleep as the week goes on', 'Eat magnesium-rich food (nuts, seeds, dark chocolate)', 'Keep exercise gentle and consistent', 'Prep period supplies before you need them', 'Be a little extra gentle with yourself'],
    avoid: ['Heavy sugar or salt near the end of the phase', 'Overcommitting socially in the final days', 'Harsh self-talk about mood swings you can\u2019t fully control'],
    need: ['A restocked period kit', 'Comfort items — heating pad, favourite tea', 'Comfortable, non-restrictive clothing', 'A mood note, if PMS tends to hit you hard'],
  },
};

const MOODS = ['Calm', 'Happy', 'Energized', 'Low', 'Irritable', 'Anxious'];
const SYMPTOMS = ['Cramps', 'Headache', 'Bloating', 'Fatigue', 'Tender breasts', 'Acne'];

const LEARN_CONTENT = [
  {
    title: 'Why the cycle happens at all',
    body: `Every month, the uterus builds a soft, blood-rich lining in case of pregnancy. When pregnancy doesn't happen, hormone levels drop, that lining sheds, and it leaves the body as a period. It's not a malfunction — it's the same four-step process, on repeat, roughly every 21–35 days.`,
  },
  {
    title: 'Sanitary pads',
    body: `A padded strip that sticks inside your underwear and absorbs flow externally. Nothing is inserted, so there's no learning curve, and it's easy to check at a glance.
    <br><br><strong>How to use one:</strong>
    <ol><li>Peel off the paper backing strip</li><li>Press the sticky side onto clean, dry underwear</li><li>Fold side wings around the fabric edges if present</li><li>Change every 4–6 hours, sooner on heavy days</li><li>Wrap the used pad before binning it — never flush it</li></ol>`,
  },
  {
    title: 'Tampons',
    body: `A small absorbent core inserted into the vaginal canal, worn with a string left outside for removal. Invisible under any clothing, including swimwear.
    <br><br><strong>How to use one:</strong>
    <ol><li>Wash hands before opening</li><li>Insert at a slight backward angle using the applicator or your finger</li><li>Leave the string hanging outside</li><li>Change every 4–8 hours — never longer than 8</li><li>Choose the lowest absorbency that manages your flow</li></ol>`,
  },
  {
    title: 'Menstrual cups',
    body: `A soft silicone cup that sits low in the vaginal canal and collects, rather than absorbs, flow. One cup can last for years and holds more than a pad or tampon.
    <br><br><strong>How to use one:</strong>
    <ol><li>Fold the cup (C-fold or punch-down fold)</li><li>Insert and let it pop open to form a seal</li><li>Empty every 8–12 hours, rinse, and reinsert</li><li>Sterilise in boiling water between cycles</li></ol>`,
  },
  {
    title: 'Period underwear',
    body: `Underwear with built-in absorbent layers, worn alone or as backup with a cup or tampon. Feels like ordinary underwear and is reusable.
    <br><br><strong>How to use it:</strong>
    <ol><li>Wear like regular underwear, absorbent panel facing in</li><li>Rinse in cold water after use, then machine wash</li><li>Air dry — heat wears down the absorbent layers</li></ol>`,
  },
  {
    title: 'Habits that matter regardless of what you use',
    body: `<ul><li>Wash hands before and after changing any product</li><li>Change on schedule even on lighter days</li><li>Plain water is enough for external cleaning — scented washes can irritate</li><li>Keep a spare product and a change of underwear in your bag during period week</li></ul>`,
  },
];

// Add local images to the images folder and put their relative paths here.
// Example: './images/menstrual.jpg'
const LOCAL_PHASE_IMAGES = {
  menstrual: './period self care ❤️.jpg',
  follicular: './download.jpg',
  ovulation: './cuteness overload here 💅🔥.jpg',
  luteal: './download (1).jpg',
};

const PHASE_IMAGES = {
  menstrual: 'https://images.unsplash.com/photo-1541701494587-cb58502866ab?auto=format&fit=crop&w=900&q=80',
  follicular: 'https://images.unsplash.com/photo-1490750967868-88aa4486c946?auto=format&fit=crop&w=900&q=80',
  ovulation: 'https://unsplash.com/photos/9sXLmVxj2z0/download?force=true&w=900',
  luteal: 'https://unsplash.com/photos/GaxgmVGCHzc/download?force=true&w=900',
};

const PHASE_IMAGE_FALLBACKS = {
  ovulation: 'https://images.unsplash.com/photo-1515886657613-9f3515b0c78f?auto=format&fit=crop&w=900&q=80',
  luteal: 'https://images.unsplash.com/photo-1500534623283-312aade485b7?auto=format&fit=crop&w=900&q=80',
};

const PHASE_IMAGE_ALTS = {
  menstrual: 'Rich red abstract texture',
  follicular: 'Fresh flowers in bloom',
  ovulation: 'Bold fashion portrait with a romantic mood',
  luteal: 'Woman resting quietly on a bed',
};

// ---- Storage ----
function loadData() {
  try { const raw = localStorage.getItem(STORAGE_KEY); return raw ? JSON.parse(raw) : null; }
  catch (e) { return null; }
}
function saveData(data) { localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); }
function getPhaseImage(data, key) { return LOCAL_PHASE_IMAGES[key] || data?.customImages?.[key] || PHASE_IMAGES[key]; }
function todayKey() { return dateOnlyString(new Date()); }
function isFutureDate(value) { return value > todayKey(); }
function getTodayLog(data) {
  if (!data.logs) data.logs = {};
  if (!data.logs[todayKey()]) data.logs[todayKey()] = { mood: null, symptoms: [] };
  return data.logs[todayKey()];
}

// ---- Date helpers ----
function parseDateOnly(str) { const [y, m, d] = str.split('-').map(Number); return new Date(y, m - 1, d); }
function dateOnlyString(date) {
  const y = date.getFullYear(), m = String(date.getMonth() + 1).padStart(2, '0'), d = String(date.getDate()).padStart(2, '0');
  return `${y}-${m}-${d}`;
}
function formatPretty(date) { return date.toLocaleDateString(undefined, { day: 'numeric', month: 'short', year: 'numeric' }); }

// ---- Cycle math ----
function getPhaseBounds(cycleLength, periodLength) {
  const ovulationDay = Math.max(periodLength + 3, cycleLength - 14);
  const ovStart = Math.max(periodLength + 1, ovulationDay - 1);
  const ovEnd = Math.min(cycleLength - 1, ovulationDay + 1);
  return {
    menstrual: { start: 1, end: periodLength },
    follicular: { start: periodLength + 1, end: ovStart - 1 >= periodLength + 1 ? ovStart - 1 : periodLength + 1 },
    ovulation: { start: ovStart, end: ovEnd },
    luteal: { start: ovEnd + 1, end: cycleLength },
  };
}
function getCurrentPhase(day, bounds) {
  for (const key of PHASE_ORDER) { const b = bounds[key]; if (day >= b.start && day <= b.end) return key; }
  return 'luteal';
}
function computeCycleInfo(data, now = new Date()) {
  const { lastPeriodStart, cycleLength, periodLength } = data;
  const startDate = parseDateOnly(lastPeriodStart);
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
  const daysSince = Math.floor((today - startDate) / DAY_MS);
  const cyclesElapsed = Math.floor(daysSince / cycleLength);
  const currentDay = (daysSince - cyclesElapsed * cycleLength) + 1;
  const currentCycleStart = new Date(startDate.getTime() + cyclesElapsed * cycleLength * DAY_MS);
  const bounds = getPhaseBounds(cycleLength, periodLength);
  const phase = getCurrentPhase(currentDay, bounds);
  const phaseEndDay = bounds[phase].end;
  const nextPhaseKey = PHASE_ORDER[(PHASE_ORDER.indexOf(phase) + 1) % 4];
  let nextPhaseDate = new Date(currentCycleStart.getTime() + phaseEndDay * DAY_MS);
  if (phase === 'luteal') nextPhaseDate = new Date(currentCycleStart.getTime() + cycleLength * DAY_MS);
  const nextPeriodDate = new Date(currentCycleStart.getTime() + cycleLength * DAY_MS);
  const daysUntilNextPeriod = Math.ceil((nextPeriodDate - now) / DAY_MS);
  const dayInPhase = currentDay - bounds[phase].start + 1;
  const phaseLength = bounds[phase].end - bounds[phase].start + 1;
  return { startDate, today, daysSince, cyclesElapsed, currentDay, currentCycleStart, bounds, phase,
    nextPhaseKey, nextPhaseDate, nextPeriodDate, daysUntilNextPeriod, cycleLength, periodLength, dayInPhase, phaseLength };
}

// ==========================================================
// RING RENDERING (SVG)
// ==========================================================
function renderRing(svgEl, bounds, cycleLength, currentDay, colors) {
  const cx = 160, cy = 160, r = 130;
  const C = 2 * Math.PI * r;
  svgEl.innerHTML = '';
  const ns = 'http://www.w3.org/2000/svg';

  const track = document.createElementNS(ns, 'circle');
  track.setAttribute('cx', cx); track.setAttribute('cy', cy); track.setAttribute('r', r);
  track.setAttribute('class', 'wheel-track-base');
  svgEl.appendChild(track);

  let cumulativeDays = 0;
  PHASE_ORDER.forEach((key) => {
    const b = bounds[key];
    const phaseDays = b.end - b.start + 1;
    const arcLen = (phaseDays / cycleLength) * C;
    const gap = C - arcLen;
    const offset = -1 * (cumulativeDays / cycleLength) * C;
    const seg = document.createElementNS(ns, 'circle');
    seg.setAttribute('cx', cx); seg.setAttribute('cy', cy); seg.setAttribute('r', r);
    seg.setAttribute('class', 'wheel-seg');
    seg.setAttribute('stroke-dasharray', `${Math.max(arcLen - 4, 0)} ${gap + 4}`);
    seg.setAttribute('stroke-dashoffset', offset);
    seg.style.stroke = PHASE_META[key].colors.glow;
    seg.style.opacity = key === bounds.__current ? '1' : '.55';
    svgEl.appendChild(seg);
    cumulativeDays += phaseDays;
  });

  const angle = ((currentDay - 0.5) / cycleLength) * 2 * Math.PI - Math.PI / 2;
  const mx = cx + r * Math.cos(angle), my = cy + r * Math.sin(angle);
  const marker = document.createElementNS(ns, 'circle');
  marker.setAttribute('cx', mx); marker.setAttribute('cy', my); marker.setAttribute('r', 8);
  marker.setAttribute('class', 'wheel-marker');
  marker.style.fill = colors.glow;
  svgEl.appendChild(marker);
}

function formatCountdownParts(ms) {
  if (ms < 0) ms = 0;
  const totalSec = Math.floor(ms / 1000);
  return {
    d: String(Math.floor(totalSec / 86400)).padStart(2, '0'),
    h: String(Math.floor((totalSec % 86400) / 3600)).padStart(2, '0'),
    m: String(Math.floor((totalSec % 3600) / 60)).padStart(2, '0'),
    s: String(totalSec % 60).padStart(2, '0'),
  };
}

// ==========================================================
// APP STATE
// ==========================================================
let currentData = null;
let countdownTimer = null;
const $ = (id) => document.getElementById(id);

function setBodyPhase(phaseKey) {
  document.body.setAttribute('data-phase', phaseKey);
}

// ---------------- ONBOARDING ----------------
function buildWorldGallery() {
  const grid = $('worldGrid');
  grid.innerHTML = '';
  PHASE_ORDER.forEach((key) => {
    const meta = PHASE_META[key];
    const card = document.createElement('button');
    card.className = `world-card world-card--${key}`;
    card.style.setProperty('--wc-1', meta.colors.c1);
    card.style.setProperty('--wc-2', meta.colors.c2);
    card.style.setProperty('--wc-glow', meta.colors.glow);
    card.style.setProperty('--wc-text', meta.colors.text);
    card.innerHTML = `<img class="world-image" src="${getPhaseImage(loadData(), key)}" alt="${PHASE_IMAGE_ALTS[key]}" loading="lazy" decoding="async">
      <span class="world-tag">Phase ${meta.order + 1} of 4</span>
      <span class="world-name">${meta.world}</span>
      <span class="world-mantra">${meta.mantra}</span>`;
    card.querySelector('.world-image').addEventListener('error', (event) => {
      const fallback = PHASE_IMAGE_FALLBACKS[key];
      if (fallback && event.currentTarget.src !== fallback) {
        event.currentTarget.src = fallback;
      } else {
        event.currentTarget.hidden = true;
      }
    });
    card.addEventListener('click', () => {
      setBodyPhase(key);
      setTimeout(() => setBodyPhase('void'), 3200);
    });
    grid.appendChild(card);
  });
}

function showOnboarding() {
  $('onboardingView').hidden = false;
  $('trackerView').hidden = true;
  $('appNav').hidden = true;
  $('bottomNav').hidden = true;
  setBodyPhase('void');
  if (countdownTimer) clearInterval(countdownTimer);
}

function goToHomePage() {
  showOnboarding();
  window.scrollTo({ top: 0, behavior: 'auto' });
}

function getStoredPeriodEnd(data) {
  if (data.lastPeriodEnd) return data.lastPeriodEnd;
  const endDate = parseDateOnly(data.lastPeriodStart);
  endDate.setDate(endDate.getDate() + data.periodLength - 1);
  return dateOnlyString(endDate);
}

function isValidPeriodEnd(startDate, endDate) {
  return Boolean(endDate) && endDate >= startDate && !isFutureDate(endDate);
}

// ---------------- TRACKER ----------------
function showTracker() {
  const saved = loadData();
  if (saved && isFutureDate(saved.lastPeriodStart)) {
    $('lastPeriodDate').value = saved.lastPeriodStart;
    showOnboarding();
    $('dateError').hidden = false;
    return;
  }
  $('onboardingView').hidden = true;
  $('trackerView').hidden = false;
  $('appNav').hidden = false;
  if (window.matchMedia('(max-width:640px)').matches) $('bottomNav').hidden = false;
  $('editLastPeriod').max = todayKey();
  $('editLastPeriodEnd').max = todayKey();
  $('lastPeriodEndDate').max = todayKey();
  renderAll();
  if (countdownTimer) clearInterval(countdownTimer);
  countdownTimer = setInterval(tick, 1000);
}

function renderAll() {
  currentData = loadData();
  if (!currentData) { showOnboarding(); return; }
  const info = computeCycleInfo(currentData);
  const meta = PHASE_META[info.phase];
  setBodyPhase(info.phase);

  const bounds = { ...info.bounds, __current: info.phase };
  renderRing($('cycleRingSvg'), bounds, info.cycleLength, info.currentDay, meta.colors);
  $('ringDay').textContent = `Day ${info.currentDay}`;
  $('ringPhase').textContent = meta.label;
  $('ringMantra').textContent = meta.mantra;

  $('countdownEyebrow').textContent = `Estimated time to ${meta.nextLabel}`;
  $('nextPeriodInline').textContent = formatPretty(info.nextPeriodDate);

  fillList('listDo', meta.do);
  fillList('listAvoid', meta.avoid);
  fillList('listNeed', meta.need);

  // luteal warning (based on real data only)
  const warn = $('lutealWarning');
  if (info.phase === 'luteal' && info.daysUntilNextPeriod <= 5) {
    warn.hidden = false;
    const d = info.daysUntilNextPeriod;
    $('warningHeadline').textContent = d <= 0 ? 'Your period is estimated any time now.' : `Your period is estimated in ${d} day${d === 1 ? '' : 's'}.`;
    $('warningBody').textContent = 'Restock supplies, keep a heating pad handy, and pack a small kit if you\u2019ll be out.';
  } else { warn.hidden = true; }

  renderTimeline(info);
  renderDashboard(info, meta);
  renderHistory(currentData);
  renderSettingsForm(currentData);
  renderMoodChips();
  renderSymptomChips();

  tick(); // set initial countdown numbers immediately
}

function fillList(id, items) {
  const el = $(id); el.innerHTML = '';
  items.forEach((t) => { const li = document.createElement('li'); li.textContent = t; el.appendChild(li); });
}

function renderTimeline(info) {
  const wrap = $('timeline');
  wrap.innerHTML = '';
  PHASE_ORDER.forEach((key) => {
    const meta = PHASE_META[key];
    const b = info.bounds[key];
    const node = document.createElement('button');
    node.className = 'timeline-node' + (key === info.phase ? ' is-active' : '');
    node.style.setProperty('--tn-glow', meta.colors.glow);
    node.innerHTML = `<span class="tn-dot"></span><span class="tn-label">${meta.world}</span><span class="tn-range">Day ${b.start}–${b.end}</span>`;
    wrap.appendChild(node);
  });
}

function renderDashboard(info, meta) {
  $('aiInsight').textContent = meta.insight;

  const d = info.daysUntilNextPeriod;
  let periodLine;
  if (info.phase === 'menstrual') periodLine = `currently on day ${info.currentDay} of her period.`;
  else if (info.phase === 'luteal' && d <= 5) periodLine = `expecting her period in about ${d} day${d === 1 ? '' : 's'} — a little extra patience helps here.`;
  else periodLine = `expecting her next period around ${formatPretty(info.nextPeriodDate)}.`;
  $('briefText').textContent = `In the ${meta.label} (${meta.world}) — ${meta.mantra.toLowerCase()}. She's ${periodLine}`;
  renderCouples(info, meta);
}

function renderCouples(info, meta) {
  const name = $('partnerName').value.trim();
  const person = name || 'your partner';
  const phaseAdvice = {
    menstrual: ['Offer comfort, warmth, or practical help without pressure.', 'Rest may be the most useful plan today.', 'Ask before touching, planning, or solving.'],
    follicular: ['Make room for plans or a little extra energy if they want it.', 'Energy can rise, but every day is still individual.', 'Support their pace instead of expecting a mood.'],
    ovulation: ['Enjoy connection, movement, or social time if it feels welcome.', 'A predicted fertile window is not birth control.', 'Consent and contraception conversations matter every day.'],
    luteal: ['Help protect quiet time and check whether supplies are stocked.', 'PMS varies widely and is never a personality label.', 'Offer patience, then listen to the answer you get.'],
  }[info.phase];
  $('couplesPhaseLabel').textContent = `${meta.label} · day ${info.currentDay}`;
  $('couplesTitle').textContent = `${person} may need a little more of this`;
  $('couplesSummary').textContent = `${person} is in ${meta.world}. ${meta.mantra}. This is an estimate for context, not a diagnosis or a rule for how they should feel.`;
  $('couplesTry').textContent = phaseAdvice[0];
  $('couplesRemember').textContent = phaseAdvice[1];
  $('couplesRespect').textContent = phaseAdvice[2];
}

function renderMoodChips() {
  const log = getTodayLog(currentData);
  buildChipRow($('moodChips'), MOODS, log.mood, (val) => { log.mood = log.mood === val ? null : val; saveData(currentData); renderMoodChips(); renderBrief(); });
}
function renderSymptomChips() {
  const log = getTodayLog(currentData);
  buildMultiChipRow($('symptomChips'), SYMPTOMS, log.symptoms, (val) => {
    const idx = log.symptoms.indexOf(val);
    if (idx > -1) log.symptoms.splice(idx, 1); else log.symptoms.push(val);
    saveData(currentData); renderSymptomChips();
  });
}
function buildChipRow(container, options, activeVal, onClick) {
  container.innerHTML = '';
  options.forEach((opt) => {
    const chip = document.createElement('button');
    chip.type = 'button';
    chip.className = 'chip' + (opt === activeVal ? ' is-active' : '');
    chip.textContent = opt;
    chip.addEventListener('click', () => onClick(opt));
    container.appendChild(chip);
  });
}
function buildMultiChipRow(container, options, activeArr, onClick) {
  container.innerHTML = '';
  options.forEach((opt) => {
    const chip = document.createElement('button');
    chip.type = 'button';
    chip.className = 'chip' + (activeArr.includes(opt) ? ' is-active' : '');
    chip.textContent = opt;
    chip.addEventListener('click', () => onClick(opt));
    container.appendChild(chip);
  });
}
function renderBrief() {
  const info = computeCycleInfo(currentData);
  renderDashboard(info, PHASE_META[info.phase]);
}

function renderHistory(data) {
  const history = (data.history && data.history.length ? data.history : [data.lastPeriodStart]).slice().sort((a, b) => new Date(b) - new Date(a));
  const body = $('historyBody'); const empty = $('historyEmpty');
  body.innerHTML = '';
  if (!history.length) { empty.hidden = false; return; }
  empty.hidden = true;
  history.forEach((dstr, idx) => {
    const tr = document.createElement('tr');
    const tdDate = document.createElement('td'); tdDate.textContent = formatPretty(parseDateOnly(dstr));
    const tdLen = document.createElement('td');
    tdLen.textContent = idx < history.length - 1 ? `${Math.round((parseDateOnly(dstr) - parseDateOnly(history[idx + 1])) / DAY_MS)} days` : '—';
    tr.appendChild(tdDate); tr.appendChild(tdLen); body.appendChild(tr);
  });
}

function renderSettingsForm(data) {
  $('editLastPeriod').value = data.lastPeriodStart;
  $('editLastPeriodEnd').value = getStoredPeriodEnd(data);
  $('editCycleLength').value = data.cycleLength;
  $('editPeriodLength').value = data.periodLength;
}

function setupPhaseImageUploads() {
  document.querySelectorAll('[data-phase-image]').forEach((input) => {
    input.addEventListener('change', () => {
      const file = input.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.addEventListener('load', () => {
        const data = loadData();
        if (!data) return;
        if (!data.customImages) data.customImages = {};
        data.customImages[input.dataset.phaseImage] = reader.result;
        saveData(data);
        buildWorldGallery();
      });
      reader.readAsDataURL(file);
    });
  });
}

function buildLearnAccordion() {
  const wrap = $('learnAccordion');
  wrap.innerHTML = '';
  LEARN_CONTENT.forEach((item, i) => {
    const el = document.createElement('div');
    el.className = 'accordion-item glass-panel' + (i === 0 ? ' is-open' : '');
    el.innerHTML = `<button class="accordion-trigger" aria-expanded="${i === 0}">
        <span>${item.title}</span><span class="acc-plus">+</span>
      </button>
      <div class="accordion-panel"><div class="accordion-panel-inner">${item.body}</div></div>`;
    el.querySelector('.accordion-trigger').addEventListener('click', () => {
      const wasOpen = el.classList.contains('is-open');
      el.classList.toggle('is-open');
      el.querySelector('.accordion-trigger').setAttribute('aria-expanded', String(!wasOpen));
    });
    wrap.appendChild(el);
  });
}

// ---------------- COUNTDOWN TICK ----------------
function tick() {
  if (!currentData) return;
  const now = new Date();
  const info = computeCycleInfo(currentData, now);

  if (document.body.getAttribute('data-phase') !== info.phase) { renderAll(); return; }

  const ms = info.nextPhaseDate - now;
  const parts = formatCountdownParts(ms);
  updateDigit('d', parts.d); updateDigit('h', parts.h); updateDigit('m', parts.m); updateDigit('s', parts.s);
}
function updateDigit(unit, value) {
  const el = document.querySelector(`.digit[data-unit="${unit}"]`);
  if (!el) return;
  if (el.textContent !== value) {
    if (!prefersReducedMotion) {
      clearTimeout(el._rollTimer);
      el.classList.add('is-rolling');
      el._rollTimer = setTimeout(() => { el.textContent = value; el.classList.remove('is-rolling'); }, 140);
    } else { el.textContent = value; }
  }
}

// ==========================================================
// PARTICLE FIELD (canvas)
// ==========================================================
function hexToRgb(hex) {
  const m = hex.replace('#', '');
  const bigint = parseInt(m.length === 3 ? m.split('').map(c => c + c).join('') : m, 16);
  return { r: (bigint >> 16) & 255, g: (bigint >> 8) & 255, b: bigint & 255 };
}
(function particleField() {
  const canvas = $('particleField');
  if (isTouchDevice || prefersReducedMotion) {
    canvas.hidden = true;
    return;
  }
  const ctx = canvas.getContext('2d');
  let particles = [];
  let w, h, dpr;

  function resize() {
    dpr = Math.min(window.devicePixelRatio || 1, 2);
    w = window.innerWidth; h = window.innerHeight;
    canvas.width = w * dpr; canvas.height = h * dpr;
    canvas.style.width = w + 'px'; canvas.style.height = h + 'px';
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  }
  function seed() {
    const isMobile = window.innerWidth < 640;
    const count = isMobile ? 18 : 46;
    particles = Array.from({ length: count }, () => ({
      x: Math.random() * w, y: Math.random() * h,
      vx: (Math.random() - 0.5) * 0.12, vy: (Math.random() - 0.5) * 0.12,
      r: Math.random() * 1.6 + 0.4, a: Math.random() * 0.5 + 0.15,
    }));
  }
  function getGlow() {
    const raw = getComputedStyle(document.body).getPropertyValue('--p-glow').trim() || '#8C6FE0';
    return hexToRgb(raw);
  }
  function draw() {
    ctx.clearRect(0, 0, w, h);
    const c = getGlow();
    particles.forEach((p) => {
      p.x += p.vx; p.y += p.vy;
      if (p.x < 0) p.x = w; if (p.x > w) p.x = 0;
      if (p.y < 0) p.y = h; if (p.y > h) p.y = 0;
      ctx.beginPath();
      ctx.fillStyle = `rgba(${c.r},${c.g},${c.b},${p.a})`;
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
      ctx.fill();
    });
    if (!prefersReducedMotion && document.visibilityState === 'visible') requestAnimationFrame(draw);
  }
  window.addEventListener('resize', () => { resize(); seed(); });
  document.addEventListener('visibilitychange', () => { if (document.visibilityState === 'visible' && !prefersReducedMotion) requestAnimationFrame(draw); });
  resize(); seed(); draw();
})();

// ==========================================================
// CURSOR GLOW + TILT CARDS
// ==========================================================
if (isFinePointer && !prefersReducedMotion) {
  const glow = $('cursorGlow');
  window.addEventListener('mousemove', (e) => {
    glow.style.opacity = '1';
    glow.style.transform = `translate(${e.clientX}px, ${e.clientY}px) translate(-50%,-50%)`;
  });
  window.addEventListener('mouseleave', () => { glow.style.opacity = '0'; });
}

// ==========================================================
// EVENT WIRING
// ==========================================================
document.addEventListener('DOMContentLoaded', () => {
  buildWorldGallery();
  buildLearnAccordion();
  setupPhaseImageUploads();
  document.querySelectorAll('[data-fallback]').forEach((image) => {
    image.addEventListener('error', () => { image.hidden = true; });
  });

  const existing = loadData();
  if (existing) showTracker(); else showOnboarding();

  $('landingNavBtn').addEventListener('click', goToHomePage);
  $('mobileHomeBtn').addEventListener('click', goToHomePage);
  $('brandHomeLink').addEventListener('click', (event) => {
    event.preventDefault();
    goToHomePage();
  });

  $('startTrackingBtn').addEventListener('click', () => {
    document.getElementById('setupSection').scrollIntoView({ behavior: 'smooth' });
  });

  $('setupForm').addEventListener('submit', (e) => {
    e.preventDefault();
    const lastPeriodStart = $('lastPeriodDate').value;
    const lastPeriodEnd = $('lastPeriodEndDate').value;
    const cycleLength = parseInt($('cycleLength').value, 10);
    const periodLength = parseInt($('periodLength').value, 10);
    $('dateError').hidden = true;
    $('endDateError').hidden = true;
    if (!lastPeriodStart) return;
    if (isFutureDate(lastPeriodStart)) {
      $('dateError').hidden = false;
      $('lastPeriodDate').focus();
      return;
    }
    if (!isValidPeriodEnd(lastPeriodStart, lastPeriodEnd)) {
      $('endDateError').hidden = false;
      $('lastPeriodEndDate').focus();
      return;
    }
    const calculatedPeriodLength = Math.round((parseDateOnly(lastPeriodEnd) - parseDateOnly(lastPeriodStart)) / DAY_MS) + 1;
    if (calculatedPeriodLength > 10) {
      $('endDateError').textContent = 'The period length cannot be longer than 10 days.';
      $('endDateError').hidden = false;
      $('lastPeriodEndDate').focus();
      return;
    }
    $('periodLength').value = calculatedPeriodLength;
    saveData({ lastPeriodStart, lastPeriodEnd, cycleLength, periodLength: calculatedPeriodLength, history: [lastPeriodStart], logs: {} });
    showTracker();
    window.scrollTo({ top: 0, behavior: 'smooth' });
  });

  $('settingsForm').addEventListener('submit', (e) => {
    e.preventDefault();
    const data = loadData() || {};
    data.lastPeriodStart = $('editLastPeriod').value;
    data.lastPeriodEnd = $('editLastPeriodEnd').value;
    if (!data.lastPeriodStart || isFutureDate(data.lastPeriodStart)) {
      $('editLastPeriod').setCustomValidity('Choose today or an earlier date.');
      $('editLastPeriod').reportValidity();
      return;
    }
    if (!isValidPeriodEnd(data.lastPeriodStart, data.lastPeriodEnd)) {
      $('editLastPeriodEnd').setCustomValidity('Choose an end date on or after the start date and no later than today.');
      $('editLastPeriodEnd').reportValidity();
      return;
    }
    const calculatedPeriodLength = Math.round((parseDateOnly(data.lastPeriodEnd) - parseDateOnly(data.lastPeriodStart)) / DAY_MS) + 1;
    if (calculatedPeriodLength > 10) {
      $('editLastPeriodEnd').setCustomValidity('The period length cannot be longer than 10 days.');
      $('editLastPeriodEnd').reportValidity();
      return;
    }
    $('editLastPeriod').setCustomValidity('');
    $('editLastPeriodEnd').setCustomValidity('');
    data.cycleLength = parseInt($('editCycleLength').value, 10);
    data.periodLength = calculatedPeriodLength;
    $('editPeriodLength').value = calculatedPeriodLength;
    if (!data.history) data.history = [data.lastPeriodStart];
    saveData(data);
    renderAll();
  });

  $('resetBtn').addEventListener('click', () => {
    if (confirm('This will erase all saved cycle data on this device. Continue?')) {
      localStorage.removeItem(STORAGE_KEY);
      if (countdownTimer) clearInterval(countdownTimer);
      showOnboarding();
      window.scrollTo({ top: 0 });
    }
  });

  function logPeriodToday() {
    const data = loadData(); if (!data) return;
    const todayStr = dateOnlyString(new Date());
    if (!data.history) data.history = [];
    if (!data.history.includes(todayStr)) data.history.unshift(todayStr);
    data.lastPeriodStart = todayStr;
    data.lastPeriodEnd = todayStr;
    data.periodLength = 1;
    saveData(data);
    renderAll();
  }
  $('logTodayBtn').addEventListener('click', logPeriodToday);

  $('copyBriefBtn').addEventListener('click', () => {
    const text = $('briefText').textContent;
    navigator.clipboard?.writeText(text).then(() => {
      const btn = $('copyBriefBtn'); const orig = btn.textContent;
      btn.textContent = 'Copied'; setTimeout(() => { btn.textContent = orig; }, 1500);
    }).catch(() => {});
  });

  $('partnerName').addEventListener('input', () => {
    if (currentData) {
      const info = computeCycleInfo(currentData);
      renderCouples(info, PHASE_META[info.phase]);
    }
  });
  $('copyCouplesBtn').addEventListener('click', async () => {
    const text = `${$('couplesTitle').textContent}. ${$('couplesSummary').textContent} ${$('couplesTry').textContent}`;
    try { await navigator.clipboard.writeText(text); } catch (e) { return; }
    const button = $('copyCouplesBtn'); const original = button.textContent; button.textContent = 'Copied'; setTimeout(() => { button.textContent = original; }, 1500);
  });

  // nav + bottom nav scroll
  document.querySelectorAll('[data-scroll]').forEach((btn) => {
    btn.addEventListener('click', () => {
      const target = document.getElementById(btn.getAttribute('data-scroll'));
      if (target) target.scrollIntoView({ behavior: 'smooth', block: 'start' });
    });
  });

  window.addEventListener('resize', () => {
    const mobile = window.matchMedia('(max-width:640px)').matches;
    if (currentData) $('bottomNav').hidden = !mobile;
  });
});
