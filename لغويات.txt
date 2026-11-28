(async () => {
'use strict';

// ╔══════════════════════════════════════════════════════════╗
// ║  🔤 لغويات الشات v1.0.1 🔒                               ║
// ║  • يعرض ويشغّل 5 مسابقات من مجلد لغويات فقط       ║
// ║  • يحتوي كل ملف على 200 سؤال مختلف                  ║
// ╚══════════════════════════════════════════════════════════╝

const VERSION = '1.0.0';
const BRAND = 'لغويات';
const PAGE_SIZE = 15;

const CONFIG = {
  chatSelector: null,
  inputSelector: null,
  github: { owner: 'rani079', repo: 'quiz-data', branch: 'main', basePath: '' }
};

const GAMES = [
  { key:"مرادفات", title:"مرادفات", eventGame:true, textGame:true, dataFile:"مرادفات.txt", intro:"اكتشف الكلمة التي تحمل المعنى نفسه", emoji:"📝" },
  { key:"أضداد", title:"أضداد", eventGame:true, textGame:true, dataFile:"اضداد.txt", intro:"هات عكس الكلمة بدقة", emoji:"↔️" },
  { key:"جموع", title:"جموع", eventGame:true, textGame:true, dataFile:"جموع.txt", intro:"حوّل المفرد إلى جمعه الصحيح", emoji:"📚" },
  { key:"مفرد", title:"مفرد", eventGame:true, textGame:true, dataFile:"مفرد.txt", intro:"استخرج المفرد الصحيح من الجمع", emoji:"📖" },
  { key:"تراكيب", title:"تراكيب", eventGame:true, textGame:true, dataFile:"تكوين.txt", intro:"أكمل التركيب اللغوي بالكلمة المناسبة", emoji:"🧩" }
];

const EMOJI_MAP = {
  'اسلام':'🕌','إسلام':'🕌','دين':'🕌','قران':'📖','قرآن':'📖','القران الكريم - Tanzil':'📖','تفسير':'📖',
  'حديث':'📜','فقه':'⚖️','سيرة':'📜','مسيرة':'📜','صحابة':'⭐','انبياء':'🕊️','أنبياء':'🕊️',
  'ادعية':'🤲','أدعية':'🤲','اذكار':'📿','أذكار':'📿','تجويد':'📿','رمضان':'🌙','حج':'🕋',
  'عواصم':'🌍','جغرافيا':'🗺️','دول':'🏳️','مدن':'🏙️','انهار':'🏞️','أنهار':'🏞️',
  'جبال':'⛰️','بحار':'🌊','محيطات':'🌊','صحاري':'🏜️','قارات':'🌐','خرائط':'🗺️',
  'مرادفات':'📝','اضداد':'🔄','أضداد':'🔄','جموع':'📚','مفرد':'📕','لغة':'🔤','نحو':'✍️',
  'صرف':'🔡','بلاغة':'✨','املاء':'✏️','إملاء':'✏️','ترجمة':'🗣️','ترجمة_المستويات':'🌐','ترجمة_مستويات':'🌐',
  'حروف':'🔠','كلمات الحروف':'🔤','اول حرف':'🔡','أول حرف':'🔡','اخر حرف':'🔤','آخر حرف':'🔤',
  'معاني':'📖','كلمات':'💬','امثال':'💭','أمثال':'💭','مثل':'💬','شعر':'🖋️',
  'ادب':'📖','أدب':'📖','روايات':'📚','قواعد':'📐',
  'كيمياء':'🧪','فيزياء':'⚛️','احياء':'🧬','أحياء':'🧬','رياضيات':'➗',
  'رياضيات ص':'🧮','فلك':'🔭','فضاء':'🌌','علوم':'🔬','جيولوجيا':'🪨',
  'اختراعات':'💡','مخترعون':'👨‍💻','علماء':'🔬','نباتات':'🌱','حيوانات':'🐾',
  'صحة':'🩺','صحة ص':'🩺','طب':'💊','امراض':'🏥','أمراض':'🏥','اعشاب':'🌿','أعشاب':'🌿',
  'فيتامينات':'💊','تغذية':'🍎',
  'ثقافة':'🧠','ثقافة ص':'🧠','ثقافة م':'🧠','معلومات':'💡','معارف':'💡','عام':'🌐','متنوع':'🎲',
  'احجية':'🧩','أحجية':'🧩','الغاز':'🧩','ألغاز':'🧩','ذكاء':'🧠','اختبار':'📋','اختيار':'✅',
  'فن':'🎨','رسم':'🖌️','موسيقى':'🎼','اغنية':'🎵','أغنية':'🎵','اغاني':'🎶',
  'أغاني':'🎶','افلام':'🎬','أفلام':'🎬','مسلسلات':'📺','ممثلون':'🎭',
  'شخصيات':'👤','عضو':'👤','شعارات':'🏷️','ايموجي':'😀','إيموجي':'😀','العاب':'🎮',
  'ألعاب':'🎮','تطبيقات':'📱','سنابات':'📸','ش انمي':'🎌',
  'رياضة':'⚽','كرة قدم':'⚽','كرة سلة':'🏀','ملاكمة':'🥊','اولمبياد':'🏅',
  'أولمبياد':'🏅','ابطال':'🏆','أبطال':'🏆',
  'تكنولوجيا':'💻','برمجة':'👨‍💻','انترنت':'🌐','إنترنت':'🌐','هواتف':'📱',
  'كمبيوتر':'🖥️','سيارات':'🚗','طائرات':'✈️','قطارات':'🚂','سفن':'🚢',
  'اعلام':'🎌','أعلام':'🎌','عالم':'🌍','وطن':'🏴','خليج':'🌴',
  'فاكهة':'🍎','فواكه':'🍎','خضار':'🥕','خضروات':'🥕','ازهار':'🌸','أزهار':'🌸',
  'اشجار':'🌳','أشجار':'🌳','طيور':'🦅','اسماك':'🐟','أسماك':'🐟','حشرات':'🐛',
  'اكلات':'🍽️','أكلات':'🍽️','مشروبات':'☕','حلويات':'🍰','مطبخ':'👨‍🍳',
  'تاريخ':'📜','مهن':'👷','ملابس':'👕','الوان':'🎨','ألوان':'🎨','اشكال':'🔷',
  'أشكال':'🔷','ارقام':'🔢','أرقام':'🔢','ارقام كتابة':'🔢','أرقام كتابة':'🔢',
  'عدد':'🔢','أعداد':'🔢','اعداد':'🔢','نقاط':'🔢','عدد النقاط':'🔢',
  'حرف تالي':'➡️','حرف سابق':'⬅️','عملات':'💰','عواصم عربية':'🌍',
  'استخرج':'🔍','اصوات':'🔊','أصوات':'🔊','اضافة ال':'➕','إضافة ال':'➕',
  'اعصار':'🌪️','إعصار':'🌪️','اكمل كلمة':'✍️','أكمل كلمة':'✍️','اكمل':'🧩','أكمل':'🧩',
  'الفا':'🅰️','ألفا':'🅰️','تحوير':'🔀','تحويلات':'🔄','ترتيب':'📊',
  'تسليب':'🎯','تكوين':'🏗️','رابط':'🔗','متلازمات':'🔗',
  'عجلة الحظ':'🎡','عكس الكلمة':'🔁','فراسة':'👁️','فواصل':'✂️',
  'لهجات':'🗣️','مؤسس':'🏛️','مقصود':'🎯',
  'لغز':'🧩','حزورة':'🧩','حزازير':'🧩',
  'عاصمة':'🌍','دولة':'🏳️',
  'نبات':'🌱','زهرة':'🌸','شجرة':'🌳',
  'طائر':'🦅','سمكة':'🐟','حشرة':'🐛',
  'سيارة':'🚗','طائرة':'✈️','قطار':'🚂',
  'مسلسل':'📺','فيلم':'🎬','ممثل':'🎭',
  'تطبيق':'📱','لعبة':'🎮',
  'اوصاف':'🧩','أوصاف':'🧩','انجليزي':'🇬🇧','إنجليزي':'🇬🇧','english':'🇬🇧'
};
function emojiFor(name) {
  if (!name) return '📄';
  const k = name.trim();
  if (EMOJI_MAP[k]) return EMOJI_MAP[k];
  const sorted = Object.entries(EMOJI_MAP).sort((a, b) => b[0].length - a[0].length);
  for (const [key, emoji] of sorted) {
    if (key.length >= 2 && k.includes(key)) return emoji;
  }
  if (k.length >= 3) {
    for (const [key, emoji] of sorted) {
      if (key.includes(k)) return emoji;
    }
  }
  return '📄';
}

const sleep = ms => new Promise(r => setTimeout(r, ms));
const toAr = s => String(s).replace(/[٠-٩۰-۹]/gu, c => String(c.charCodeAt(0) - (c >= '۰' ? 0x6f0 : 0x660)));

function normAns(v, stripAl = true) {
  let t = toAr(String(v ?? '')).normalize('NFKC').toLowerCase()
    .replace(/[\p{M}\p{Cf}\u0640]/gu, '').replace(/[أإآٱ]/gu, 'ا').replace(/[ةه]/gu, 'ه')
    .replace(/[^\p{L}\p{N}\p{White_Space}]/gu, '').replace(/\p{White_Space}+/gu, ' ').trim();
  if (stripAl && t.startsWith('ال')) t = t.slice(2);
  return t;
}
function ansForms(v) {
  const exact = normAns(v, false);
  if (!exact) return [];
  const out = [exact];
  if (exact.startsWith('ال') && exact.length > 2) out.push(exact.slice(2));
  return [...new Set(out)];
}

function splitGraphemes(s) {
  if (typeof Intl !== 'undefined' && Intl.Segmenter) {
    try {
      const seg = new Intl.Segmenter('ar', { granularity: 'grapheme' });
      return [...seg.segment(s)].map(x => x.segment);
    } catch {}
  }
  return Array.from(s);
}
function shuffleArray(arr) {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

// ═══════════ 🔢 نقاط الحروف العربية ═══════════
const ARABIC_DOTS = {
  'ب':1,'ت':2,'ث':3,'ج':1,'ح':0,'خ':1,'د':0,'ذ':1,'ر':0,'ز':1,
  'س':0,'ش':3,'ص':0,'ض':1,'ط':0,'ظ':1,'ع':0,'غ':1,'ف':1,'ق':2,
  'ن':1,'ي':2,'ى':0,'ه':0,'ة':0,'و':0,'ؤ':1,'ا':0,'أ':0,'إ':0,
  'آ':0,'ٱ':0,'ك':0,'ل':0,'م':0,'ء':0,'ئ':1
};
function countDots(word) {
  let count = 0;
  for (const g of splitGraphemes(String(word))) {
    const base = g.replace(/[\u064B-\u065F\u0670\u0640]/g, '').trim();
    if (base && ARABIC_DOTS[base] !== undefined) count += ARABIC_DOTS[base];
  }
  return count;
}

// ═══════════ إحصائيات ═══════════
const STATS_KEY = 'mqb_stats_v4';
function loadStats() { try { return JSON.parse(localStorage.getItem(STATS_KEY)) || {}; } catch { return {}; } }
function saveStats(s) { try { localStorage.setItem(STATS_KEY, JSON.stringify(s)); } catch {} }
function recordPoint(user, elapsedSec) {
  const s = loadStats();
  if (!s[user]) s[user] = { total:0, wins:0, games:0, bestTime:null };
  s[user].total = (s[user].total || 0) + 1;
  if (elapsedSec > 0 && (s[user].bestTime == null || elapsedSec < s[user].bestTime)) s[user].bestTime = elapsedSec;
  saveStats(s);
}
function finishRoundStats(winner) {
  const s = loadStats();
  for (const u of scores.keys()) {
    if (!s[u]) s[u] = { total:0, wins:0, games:0, bestTime:null };
    s[u].games = (s[u].games || 0) + 1;
  }
  if (winner) {
    if (!s[winner]) s[winner] = { total:0, wins:0, games:0, bestTime:null };
    s[winner].wins = (s[winner].wins || 0) + 1;
  }
  saveStats(s);
}
function getTopList(n = 10) {
  return Object.entries(loadStats())
    .map(([name, d]) => ({ name, ...d }))
    .sort((a, b) => (b.total - a.total) || (b.wins - a.wins) || (b.games - a.games))
    .slice(0, n);
}
function resetStats() { try { localStorage.removeItem(STATS_KEY); } catch {} }

// ═══════════ مشرفون ═══════════
const ADMINS_KEY = 'mqb_admins_v1';
function loadAdmins() { try { return new Set(JSON.parse(localStorage.getItem(ADMINS_KEY)) || []); } catch { return new Set(); } }
function saveAdmins(s) { try { localStorage.setItem(ADMINS_KEY, JSON.stringify([...s])); } catch {} }
function isAdmin(name) { const a = loadAdmins(); if (!a.size) return true; return a.has(normAns(name)); }
function addAdmin(name) { const a = loadAdmins(); a.add(normAns(name)); saveAdmins(a); }
function removeAdmin(name) { const a = loadAdmins(); a.delete(normAns(name)); saveAdmins(a); }

// ═══════════ 🔒 القفل ═══════════
const LOCK_KEY = 'mqb_lock_v1';
function isLocked() { try { return localStorage.getItem(LOCK_KEY) === '1'; } catch { return false; } }
function setLocked(v) { try { localStorage.setItem(LOCK_KEY, v ? '1' : '0'); } catch {} }

// ═══════════ هوية المتحكم ═══════════
const HOST_KEY = 'mqb_host_v1';
function getHostName() { try { return localStorage.getItem(HOST_KEY) || ''; } catch { return ''; } }
function setHostName(name) { try { localStorage.setItem(HOST_KEY, normAns(name)); } catch {} }
function clearHostName() { try { localStorage.removeItem(HOST_KEY); } catch {} }
function isHostAdmin() {
  const host = getHostName();
  if (!host) return false;
  const admins = loadAdmins();
  if (!admins.size) return true;
  return admins.has(host);
}

// ═══════════ ثيم ═══════════
const THEME_KEY = 'mqb_theme_v1';
const DEFAULT_THEME = {
  bg: '#000000', bg2: '#1a1a1a', text: '#d4af37',
  border: '#d4af37', progress: '#d4af37', fontSize: 13, radius: 12
};
function loadTheme() {
  try { const t = JSON.parse(localStorage.getItem(THEME_KEY)); return { ...DEFAULT_THEME, ...(t || {}) }; }
  catch { return { ...DEFAULT_THEME }; }
}
function saveTheme(t) { try { localStorage.setItem(THEME_KEY, JSON.stringify(t)); } catch {} }
function resetTheme() { try { localStorage.removeItem(THEME_KEY); } catch {} }

function exportTheme() {
  const t = loadTheme();
  const blob = new Blob([JSON.stringify(t, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = `mqb-theme-${Date.now()}.json`;
  document.body.appendChild(a); a.click(); document.body.removeChild(a);
  URL.revokeObjectURL(url);
  setStatus('✅ تم تصدير ملف الألوان');
}
function importTheme() {
  const inp = document.createElement('input');
  inp.type = 'file'; inp.accept = '.json,application/json';
  inp.onchange = async e => {
    const file = e.target.files?.[0]; if (!file) return;
    try {
      const text = await file.text();
      const data = JSON.parse(text);
      const valid = ['bg','bg2','text','border','progress','fontSize','radius'];
      const merged = { ...DEFAULT_THEME };
      for (const k of valid) if (data[k] != null) merged[k] = data[k];
      saveTheme(merged); renderPanel();
      setStatus('✅ تم استيراد الألوان بنجاح');
    } catch (err) { setStatus('❌ ملف غير صالح'); }
  };
  inp.click();
}

const MIN_KEY = 'mqb_min_v1';
function loadMin() { try { return localStorage.getItem(MIN_KEY) === '1'; } catch { return false; } }
function saveMin(v) { try { localStorage.setItem(MIN_KEY, v ? '1' : '0'); } catch {} }

// ═══════════ 🎮 مسابقات مخصصة ═══════════
const CUSTOM_GAMES_KEY = 'mqb_custom_games_v1';
function loadCustomGames() {
  try {
    const raw = localStorage.getItem(CUSTOM_GAMES_KEY);
    if (!raw) return [];
    const data = JSON.parse(raw);
    return Array.isArray(data) ? data : [];
  } catch { return []; }
}
function saveCustomGames(games) {
  try {
    localStorage.setItem(CUSTOM_GAMES_KEY, JSON.stringify(games));
    return true;
  } catch (e) {
    console.error('[QuizBot] فشل حفظ المسابقات المخصصة:', e);
    return false;
  }
}
function addCustomGame(game) {
  const games = loadCustomGames();
  games.push(game);
  return saveCustomGames(games);
}
function removeCustomGame(key) {
  return saveCustomGames(loadCustomGames().filter(g => g.key !== key));
}

const CUSTOM_TYPE_LABELS = {
  text:      '📄 نصية (سؤال:جواب / MCQ / أوصاف)',
  wordList:  '🔤 ترتيب حروف (قائمة كلمات)',
  wordGame:  '🏗️ تكوين (حروف مبعثرة)',
  vocab:     '🇬🇧 مفردات إنجليزية (مستوى|كلمة)',
  quran:     '📖 قرآن (سورة|آية|نص)',
  song:      '🎵 أغاني (سؤال:جواب)',
  dots:      '🔢 عدد نقاط (كلمة|رقم)',
  image:     '🖼️ صور (مجلد صور)'
};

// ═══════════ اكتشاف الشات ═══════════
function detectChat() {
  if (CONFIG.chatSelector) { const el = document.querySelector(CONFIG.chatSelector); if (el) return el; }
  for (const s of ['#d2', '.chat-messages', '#messages', '.messages', '.chat-content', '[role="log"]', '.msgs']) {
    const el = document.querySelector(s); if (el && el.children.length) return el;
  }
  return null;
}
function detectInput() {
  if (CONFIG.inputSelector) { const el = document.querySelector(CONFIG.inputSelector); if (el) return el; }
  for (const s of ['#tbox', 'textarea[placeholder]', 'textarea', 'input[type="text"]']) {
    const el = document.querySelector(s); if (el && !el.closest('[data-cq-panel]')) return el;
  }
  return null;
}
const chat = detectChat();
const input = detectInput();
if (!chat || !input) { console.error(`%c[${BRAND}] ❌ لم أجد عناصر الشات`, 'color:red;font-weight:bold'); return; }
console.log(`%c[${BRAND}] ✅ الشات جاهز`, 'color:#4caf50;font-weight:bold');

// ═══════════ GitHub ═══════════
async function ghListDir(path, fromRoot = false) {
  const { owner, repo, branch, basePath, imageBasePath = '' } = CONFIG.github;
  const selectedBase = fromRoot ? imageBasePath : basePath;
  const base = selectedBase ? selectedBase.replace(/\/+$/, '') + '/' : '';
  const cleanPath = String(path || '').replace(/^\/+|\/+$/g, '');
  const url = `https://api.github.com/repos/${owner}/${repo}/contents/${base}${cleanPath}?ref=${branch}`;
  const r = await fetch(url);
  if (!r.ok) throw new Error(`فشل جلب ${cleanPath || (fromRoot ? 'جذر المستودع' : basePath)}`);
  return await r.json();
}
async function ghFetchText(path) {
  const { owner, repo, branch, basePath } = CONFIG.github;
  const base = basePath ? basePath.replace(/\/+$/, '') + '/' : '';
  const url = `https://raw.githubusercontent.com/${owner}/${repo}/${branch}/${base}${path}`;
  const r = await fetch(url);
  if (!r.ok) throw new Error(`الملف غير موجود: ${path}`);
  return await r.text();
}

async function ghFetchCustom(owner, repo, branch, path) {
  const clean = String(path || '').replace(/^\/+/, '');
  const url = `https://raw.githubusercontent.com/${owner}/${repo}/${branch}/${clean}`;
  const r = await fetch(url);
  if (!r.ok) throw new Error(`الملف غير موجود (${r.status})`);
  return await r.text();
}
async function ghListCustom(owner, repo, branch, path) {
  const clean = String(path || '').replace(/^\/+|\/+$/g, '');
  const url = `https://api.github.com/repos/${owner}/${repo}/contents/${clean}?ref=${branch}`;
  const r = await fetch(url);
  if (!r.ok) throw new Error(`فشل جلب المجلد (${r.status})`);
  return await r.json();
}

// ═══════════ محركات التحليل ═══════════
function parseTextQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const clean = line.trim();
    if (!clean || clean.startsWith('#')) continue;

    // صيغة ملفات لغويات: "نص السؤال" : "الإجابة"
    // نعالج السطر المقتبس كاملاً أولاً حتى لا تُفسَّر النقطتان داخل السؤال كفاصل للإجابة.
    const quotedPair = clean.match(/^("(?:\\.|[^"\\])*")\s*:\s*("(?:\\.|[^"\\])*")\s*,?\s*$/);
    if (quotedPair) {
      let q = '', answerText = '';
      try { q = JSON.parse(quotedPair[1]); answerText = JSON.parse(quotedPair[2]); }
      catch { q = quotedPair[1].slice(1, -1); answerText = quotedPair[2].slice(1, -1); }
      const ans = String(answerText).split('|').map(a => a.trim()).filter(Boolean);
      q = String(q).trim();
      if (!q || !ans.length) continue;
      items.push({ key: key + ':' + i, question: q, answers: ans,
        accepted: [...new Set(ans.flatMap(ansForms))], correctDisplay: ans[0] });
      continue;
    }

    // دعم الاقتباس العربي المنحني، ومنع سقوط السطر المقتبس إلى محلل النقطتين.
    const curlyPair = clean.match(/^[“](.*)[”]\s*:\s*[“](.*)[”],?\s*$/);
    if (curlyPair) {
      const q = curlyPair[1].trim();
      const ans = curlyPair[2].split('|').map(a => a.trim()).filter(Boolean);
      if (q && ans.length) items.push({ key: key + ':' + i, question: q, answers: ans,
        accepted: [...new Set(ans.flatMap(ansForms))], correctDisplay: ans[0] });
      continue;
    }
    if (/^["“”]/.test(clean)) continue;

    // دعم الصيغ القديمة غير المقتبسة.
    const colon = clean.indexOf(':');
    if (colon >= 1) {
      const q = clean.slice(0, colon).trim();
      const ans = clean.slice(colon + 1).split('|').map(a => a.trim()).filter(Boolean);
      if (!q || !ans.length) continue;
      items.push({ key: key + ':' + i, question: q, answers: ans,
        accepted: [...new Set(ans.flatMap(ansForms))], correctDisplay: ans[0] });
      continue;
    }

    const parts = clean.split('|').map(s => s.trim()).filter(Boolean);
    if (parts.length < 2) continue;
    const first = parts[0];
    if (/[؟?]\s*$/.test(first)) {
      const correct = parts[1], wrong = parts.slice(2);
      if (!first || !correct) continue;
      const shuffled = shuffleArray([correct, ...wrong]);
      const correctPosition = shuffled.findIndex(o => normAns(o) === normAns(correct));
      const numEmojis = ['1️⃣','2️⃣','3️⃣','4️⃣','5️⃣','6️⃣'];
      const displayQ = first + '\n\n' + shuffled.map((o, idx) => (numEmojis[idx] || (idx + 1) + '.') + ' ' + o).join('\n');
      items.push({ key: key + ':' + i, question: displayQ, answers: [correct],
        accepted: [...new Set([...ansForms(correct), String(correctPosition + 1)])],
        correctDisplay: correct, isMCQ: true, options: shuffled, correctPosition });
    } else {
      const word = parts[0], clues = parts.slice(1);
      if (!word || !clues.length) continue;
      const displayQ = 'خمن الكلمة من الأوصاف:\n' + clues.map((clue, idx) => (idx + 1) + '. ' + clue).join('\n');
      items.push({ key: key + ':' + i, question: displayQ, answers: [word],
        accepted: ansForms(word), correctDisplay: word, isClues: true });
    }
  }
  return items;
}

function parseDotsQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const raw = line.trim();
    if (!raw || raw.startsWith('#')) continue;
    const parts = raw.split('|').map(s => s.trim()).filter(Boolean);
    const word = parts[0];
    if (!word) continue;
    const provided = parts[1];
    const num = (provided && /^\d+$/.test(toAr(provided))) ? toAr(provided) : String(countDots(word));
    items.push({
      key: `${key}:${i}`,
      question: `كم عدد النقاط في كلمة: ${word}؟`,
      answers: [num],
      accepted: [num, toAr(num), normAns(num, false)],
      correctDisplay: num
    });
  }
  return items;
}

function parseSongQuestions(src, key) {
  const items = [];
  const seen = new Set();
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const raw = line.trim();
    if (!raw || raw.startsWith('#') || raw.startsWith('//')) continue;
    let question = '';
    let answer = '';
    const quoted = raw.match(/^[\"“](.*?)[\"”]\s*:\s*[\"“](.*?)[\"”]\s*,?\s*$/);
    if (quoted) {
      question = quoted[1].trim();
      answer = quoted[2].trim();
    } else {
      const splitAt = raw.lastIndexOf(':');
      if (splitAt > 0) {
        question = raw.slice(0, splitAt).trim().replace(/^[\"“]|[\"”]$/g, '');
        answer = raw.slice(splitAt + 1).trim().replace(/^[\"“]|[\"”]$/g, '');
      }
    }
    question = question.replace(/\\uFEFF/g, '').trim();
    answer = answer.replace(/\\uFEFF/g, '').trim();
    if (!question || !answer) continue;
    const dedupeKey = normAns(question, false);
    if (!dedupeKey || seen.has(dedupeKey)) continue;
    seen.add(dedupeKey);
    items.push({ key: `${key}:${i}`, question, answers: [answer],
      accepted: ansForms(answer), correctDisplay: answer });
  }
  return items;
}

function parseWordQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const raw = line.trim();
    if (!raw || raw.startsWith('#')) continue;
    let hint = '';
    let word = raw;
    let preScrambled = null;
    const cIdx = raw.indexOf(':');
    if (cIdx > 0 && cIdx < raw.length - 1) {
      hint = raw.slice(0, cIdx).trim();
      word = raw.slice(cIdx + 1).trim();
      const tokens = hint.split(/\s+/).filter(Boolean);
      const isPreScrambled = tokens.length >= 2 &&
        tokens.every(t => t.length === 1 && /[\u0600-\u06FFa-zA-Z]/.test(t));
      if (isPreScrambled) preScrambled = tokens.join('  ');
    }
    if (!word) continue;
    let displayLetters;
    if (preScrambled) displayLetters = preScrambled;
    else {
      const letters = splitGraphemes(word).filter(c => c.trim() !== '');
      if (letters.length < 2) continue;
      let scrambled = shuffleArray(letters);
      for (let a = 0; a < 20 && scrambled.join('') === letters.join(''); a++) scrambled = shuffleArray(letters);
      displayLetters = scrambled.join('  ');
    }
    items.push({
      key: `${key}:${i}`,
      question: hint && !preScrambled ? `${hint}\n${displayLetters}` : displayLetters,
      answers: [word], accepted: ansForms(word), correctDisplay: word
    });
  }
  return items;
}

function parseVocabQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const raw = line.trim();
    if (!raw || raw.startsWith('#')) continue;
    const parts = raw.split('|').map(s => s.trim());
    if (parts.length < 2) continue;
    const level = parts[0];
    const word = parts[1];
    if (!level || !word) continue;
    const letters = word.split('');
    if (letters.length < 2) continue;
    let scrambled = shuffleArray(letters);
    for (let a = 0; a < 20 && scrambled.join('') === letters.join(''); a++) scrambled = shuffleArray(letters);
    items.push({
      key: `${key}:${i}`,
      question: `[${level.toUpperCase()}] رتب الحروف:\n${scrambled.join('  ')}`,
      answers: [word], accepted: ansForms(word), correctDisplay: word
    });
  }
  return items;
}

function parseWordListQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const word = line.trim();
    if (!word || word.startsWith('#') || word.startsWith('//')) continue;
    const letters = splitGraphemes(word).filter(c => c.trim());
    if (letters.length < 2) continue;
    let scrambled = shuffleArray(letters);
    for (let tries = 0; tries < 20 && scrambled.join('') === letters.join(''); tries++) scrambled = shuffleArray(letters);
    items.push({
      key: `${key}:${i}`,
      question: `رتب الحروف لتكوين كلمة صحيحة:\n${scrambled.join('  ')}`,
      answers: [word], accepted: ansForms(word), correctDisplay: word
    });
  }
  return items;
}

function parseQuranQuestions(src, key) {
  const items = [];
  for (const [i, line] of String(src).split(/\r?\n/).entries()) {
    const parts = line.trim().split('|');
    if (parts.length < 3) continue;
    const surah = parts[0].trim();
    const ayah = parts[1].trim();
    const verse = parts.slice(2).join('|').trim();
    if (!/^\d+$/.test(surah) || !/^\d+$/.test(ayah) || !verse || verse.length > 350) continue;
    items.push({
      key: `${key}:${i}`,
      question: `أكمل نص الآية الكريمة:\nالسورة رقم ${surah} — الآية رقم ${ayah}`,
      answers: [verse], accepted: ansForms(verse), correctDisplay: verse
    });
  }
  return items;
}

async function loadImageQuestions(game) {
  const files = await ghListDir(game.folder, true);
  if (!Array.isArray(files)) throw new Error('فشل القراءة');
  const items = files
    .filter(f => f.type === 'file' && /\.(jpg|jpeg|png|gif|webp|bmp)$/i.test(f.name))
    .map(f => {
      const answer = f.name.replace(/\.[^.]+$/, '');
      return { key:f.name, isImage:true, url:f.download_url, answer, accepted:ansForms(answer), correctDisplay:answer };
    });
  if (!items.length) throw new Error('لا توجد صور');
  return items;
}

async function loadCustomGameData(game) {
  const { owner, repo, branch, path, type, key } = game;
  if (type === 'image') {
    const files = await ghListCustom(owner, repo, branch, path);
    if (!Array.isArray(files)) throw new Error('المسار ليس مجلد صور');
    const items = files
      .filter(f => f.type === 'file' && /\.(jpg|jpeg|png|gif|webp|bmp)$/i.test(f.name))
      .map(f => {
        const answer = f.name.replace(/\.[^.]+$/, '');
        return { key:f.name, isImage:true, url:f.download_url, answer, accepted:ansForms(answer), correctDisplay:answer };
      });
    if (!items.length) throw new Error('لا توجد صور في المجلد');
    return items;
  }
  const src = await ghFetchCustom(owner, repo, branch, path);
  switch (type) {
    case 'text':     return parseTextQuestions(src, key);
    case 'wordGame': return parseWordQuestions(src, key);
    case 'quran':    return parseQuranQuestions(src, key);
    case 'song':     return parseSongQuestions(src, key);
    case 'vocab':    return parseVocabQuestions(src, key);
    case 'dots':     return parseDotsQuestions(src, key);
    case 'wordList': return parseWordListQuestions(src, key);
    default:         return parseTextQuestions(src, key);
  }
}

function generateLocal(mode) {
  const items = [];
  if (mode === 'math' || mode === 'mathHard') {
    const hard = mode === 'mathHard';
    for (let i = 0; i < 50; i++) {
      const a = hard ? 20 + Math.floor(Math.random()*180) : 1 + Math.floor(Math.random()*50);
      const b = hard ? 2 + Math.floor(Math.random()*20)  : 1 + Math.floor(Math.random()*20);
      const op = ['+','-','×'][i % 3];
      let ans;
      if (op === '+') ans = a + b;
      else if (op === '-') ans = Math.max(a,b) - Math.min(a,b);
      else ans = a * b;
      const q = op === '-' ? `${Math.max(a,b)} - ${Math.min(a,b)}` : `${a} ${op} ${b}`;
      items.push({ key:`g${i}`, question:`${q} = ؟`, answers:[String(ans)], accepted:[String(ans)], correctDisplay:String(ans) });
    }
  } else if (mode === 'nextLetter' || mode === 'previousLetter') {
    const letters = ['ا','ب','ت','ث','ج','ح','خ','د','ذ','ر','ز','س','ش','ص','ض','ط','ظ','ع','غ','ف','ق','ك','ل','م','ن','ه','و','ي'];
    for (let i = 0; i < 50; i++) {
      const idx = Math.floor(Math.random() * letters.length);
      const nxt = mode === 'nextLetter' ? idx + 1 : idx - 1;
      if (nxt < 0 || nxt >= letters.length) continue;
      items.push({ key:`g${i}`, question:letters[idx], answers:[letters[nxt]], accepted:ansForms(letters[nxt]), correctDisplay:letters[nxt] });
    }
  }
  return items;
}

// ═══════════ تحميل أسئلة لغويات من مجلد GitHub فقط ═══════════
async function loadEventQuestions(game) {
  if (!game?.dataFile) throw new Error('ملف أسئلة الفعالية غير محدد');
  const src = await ghFetchText(`لغويات/${game.dataFile}`);
  const parsed = parseTextQuestions(src, `event:${game.key}`);
  if (parsed.length !== 200) throw new Error(`ملف ${game.title} يحتوي ${parsed.length} سؤالاً بدلاً من 200`);
  return parsed;
}

async function loadGameData(game) {
  if (game.eventGame) return loadEventQuestions(game);
  if (game.generatedTextGame) return generateLocal(game.generatedTextGame);
  if (game.custom) return loadCustomGameData(game);
  if (game.folder) return loadImageQuestions(game);
  const src = game.dataFile ? await ghFetchText(game.dataFile) : null;
  if (game.quranGame) return parseQuranQuestions(src, game.key);
  if (game.dotsGame) return parseDotsQuestions(src, game.key);
  if (game.songGame) return parseSongQuestions(src, game.key);
  if (game.wordListGame) return parseWordListQuestions(src, game.key);
  if (game.vocabGame) return parseVocabQuestions(src, game.key);
  if (game.wordGame) return parseWordQuestions(src, game.key);
  if (game.textGame) return parseTextQuestions(src, game.key);
  throw new Error('نوع غير معروف');
}

// ═══════════ إرسال آمن ومتسلسل ═══════════
let lastSendTime = 0;
let sendQueue = Promise.resolve();

function writeInputValue(el, value) {
  const proto = el.tagName === 'TEXTAREA'
    ? HTMLTextAreaElement.prototype
    : HTMLInputElement.prototype;
  const setter = Object.getOwnPropertyDescriptor(proto, 'value')?.set;
  if (setter) setter.call(el, value);
  else el.value = value;
  el.dispatchEvent(new Event('input', { bubbles:true }));
  el.dispatchEvent(new Event('change', { bubbles:true }));
}

async function sendNow(text) {
  const wait = Math.max(0, 1200 - (Date.now() - lastSendTime));
  if (wait) await sleep(wait);
  if (!input || !input.isConnected) throw new Error('حقل الكتابة غير متاح');
  input.focus();
  writeInputValue(input, String(text));
  const btn =
    document.querySelector('button[onclick*="Tsend"]') ||
    document.querySelector('[onclick*="Tsend"]') ||
    input.closest('form')?.querySelector('button[type="submit"]') ||
    input.parentElement?.querySelector('button');
  if (btn) btn.click();
  else {
    input.dispatchEvent(new KeyboardEvent('keydown', { key:'Enter', code:'Enter', keyCode:13, which:13, bubbles:true }));
    input.dispatchEvent(new KeyboardEvent('keyup', { key:'Enter', code:'Enter', keyCode:13, which:13, bubbles:true }));
  }
  lastSendTime = Date.now();
  await sleep(180);
}

function send(text) {
  const task = () => sendNow(text);
  const result = sendQueue.then(task, task);
  sendQueue = result.catch(err => { console.warn(`[${BRAND}] فشل إرسال رسالة:`, err); });
  return result;
}

// ═══════════ حالة اللعبة ═══════════
let running = false, activeQ = null, currentGame = GAMES[0];
let targetScore = 5, gapSec = 3, questions = [], qIdx = 0;
let scores = new Map(), wrongCount = 0, answerLock = false;
let availableKeys = null, dynamicGames = [], questionStartTime = 0, questionTimer = null;

const getAllGames = () => GAMES;
const getVisibleGames = () => GAMES;
// الأسماء المضافة فقط تظهر في !م ولا تظهر ضمن الألعاب القابلة للتشغيل.
const getPlayableGames = () => GAMES;
const getPageCount = () => Math.max(1, Math.ceil(getVisibleGames().length / PAGE_SIZE));
const getPage = n => getVisibleGames().slice((n-1)*PAGE_SIZE, n*PAGE_SIZE);

async function sendGamesPage(n) {
  const pages = getPageCount();
  if (n < 1 || n > pages) return send(`⚠️ الصفحة ${n} غير موجودة`);
  const list = getPage(n).map(g =>
    `${g.emoji||'🎯'} ${g.title}${g.custom ? ' ✨' : ''}`
  ).join('\n');
  await send(`📄 صفحة ${n}/${pages}\n${list}`);
}
async function showAllPages() {
  const total = getVisibleGames().length;
  await send(`🎮 مسابقات ${BRAND}\n📊 ${total} مسابقة — إليك القائمة الكاملة 👇`);
  for (let p = 1; p <= getPageCount(); p++) { await sleep(900); await sendGamesPage(p); }
}
async function showSummary() {
  const visible = getVisibleGames();
  const total = visible.length;
  const txt = visible.length;
  const img = 0;
  const cus = 0;
  await send(`📊 ملخص مسابقات ${BRAND}:\n📝 المجموع: ${total}\n📄 نصية: ${txt} • 🖼️ صور: ${img} • ✨ مخصصة: ${cus}\n\n` +
    `استخدم:\n!م — عرض المسابقات الـ5\n!قائمة — ملخص\n!قائمة1 — صفحة محددة`);
}
function makeHint(answer, n = 2) {
  const a = String(answer || '').trim();
  if (!a) return '—';
  const hide = Math.max(1, Math.min(n, a.length));
  if (a.length <= hide) return '؟'.repeat(a.length);
  return a.slice(0, -hide) + '؟'.repeat(hide);
}

// ═══════════ اللوحة ═══════════
let panel;
let _hostPrompted = false;
const PANEL_ATTR = 'data-cq-panel';
function setStatus(t) { const el = panel?.querySelector('#cq-status'); if (el) el.textContent = t; }

function updateProgress() {
  if (!panel) return;
  const fill = panel.querySelector('#cq-progress-fill');
  const text = panel.querySelector('#cq-progress-text');
  if (!fill || !text) return;
  if (!running || !questions.length) {
    fill.style.width = '0%';
    text.textContent = isLocked() ? '🔒 مقفول' : '⏸️ متوقف';
    return;
  }
  const pct = Math.min(100, (qIdx / questions.length) * 100);
  fill.style.width = pct.toFixed(1) + '%';
  const top = [...scores.values()].sort((a,b) => b.score - a.score)[0];
  text.textContent = top
    ? `📝 ${qIdx}/${questions.length} • 🥇 ${top.raw}: ${top.score}/${targetScore}`
    : `📝 ${qIdx}/${questions.length} • 🎯 الهدف: ${targetScore}`;
}

function promptHostName() {
  const current = getHostName();
  const name = prompt('👤 أدخل اسمك في الشات (كما يظهر تماماً):', current || '');
  if (name && name.trim()) {
    setHostName(name);
    if (loadAdmins().size === 0) addAdmin(name);
    renderPanel();
    return true;
  }
  return false;
}

function refreshGameSelect() {
  const sel = panel?.querySelector('#cq-game');
  if (!sel) return;
  const cur = currentGame.key;
  const visible = getPlayableGames();
  sel.innerHTML = visible.map(g =>
    `<option value="${g.key}">${g.emoji||'🎯'} ${g.title}${g.custom ? ' ✨' : ''}</option>`
  ).join('');
  if (visible.some(g => g.key === cur)) sel.value = cur;
  else if (visible[0]) { currentGame = visible[0]; sel.value = currentGame.key; }
}

function renderCustomList() {
  const list = panel?.querySelector('#cq-custom-list');
  if (!list) return;
  const customs = loadCustomGames();
  if (!customs.length) {
    list.innerHTML = '<div style="opacity:0.55;font-size:10px;text-align:center;padding:6px;">لا توجد مسابقات مخصصة بعد</div>';
    return;
  }
  list.innerHTML = customs.map(g => `
    <div style="display:flex;gap:4px;align-items:center;padding:5px 6px;background:var(--cq-bg);border:1px solid var(--cq-border)33;border-radius:5px;font-size:10px;">
      <span style="flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;" title="${g.owner}/${g.repo}/${g.branch}/${g.path}">
        ${g.emoji||'🎮'} <b>${g.title}</b>
        <span style="opacity:0.55;">[${g.type}]</span>
      </span>
      <button class="cq-del-custom" data-key="${g.key}" style="background:#8b1a1a;color:#fff;border:none;padding:3px 7px;border-radius:4px;cursor:pointer;font-size:10px;">🗑️</button>
    </div>
  `).join('');
  list.querySelectorAll('.cq-del-custom').forEach(btn => {
    btn.onclick = () => {
      if (!confirm(`حذف المسابقة المخصصة؟`)) return;
      removeCustomGame(btn.dataset.key);
      renderCustomList();
      refreshGameSelect();
      setStatus('🗑️ تم الحذف');
    };
  });
}

function renderPanel() {
  document.querySelector(`[${PANEL_ATTR}]`)?.remove();
  const theme = loadTheme();
  const minimized = loadMin();
  const hostName = getHostName();
  const hostAdmin = isHostAdmin();
  const adminsCount = loadAdmins().size;
  const locked = isLocked();

  panel = document.createElement('div');
  panel.setAttribute(PANEL_ATTR, '');
  panel.style.cssText = `
    position:fixed;top:20px;left:20px;z-index:999999;
    --cq-bg:${theme.bg};--cq-bg2:${theme.bg2};--cq-text:${theme.text};
    --cq-border:${theme.border};--cq-progress:${theme.progress};
    --cq-radius:${theme.radius}px;--cq-fs:${theme.fontSize}px;
    background:linear-gradient(160deg,var(--cq-bg),var(--cq-bg2));
    color:var(--cq-text);padding:16px;border-radius:var(--cq-radius);
    width:min(340px,calc(100vw - 40px));font-family:Tahoma,Arial,sans-serif;
    direction:rtl;font-size:var(--cq-fs);border:2px solid ${locked ? '#c1272d' : 'var(--cq-border)'};
    box-shadow:0 10px 40px ${locked ? 'rgba(193,39,45,0.5)' : 'rgba(0,0,0,0.55)'};cursor:move;user-select:none;
    max-height:calc(100vh - 40px);overflow-y:auto;`;

  const inputStyle = `width:100%;padding:6px;margin:3px 0;background:var(--cq-bg);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:5px;font-family:inherit;font-size:11px;box-sizing:border-box;`;
  const btnStyle   = `width:100%;padding:7px;background:linear-gradient(#2e8b2e,#1a5c1a);color:#fff;border:none;border-radius:5px;cursor:pointer;font-weight:bold;font-size:11px;margin-top:4px;`;

  panel.innerHTML = `
    <div id="cq-header" style="display:flex;justify-content:space-between;align-items:center;
      ${minimized ? '' : 'margin-bottom:12px;padding-bottom:8px;border-bottom:1px solid var(--cq-border)55;'}">
      <div style="font-size:15px;font-weight:bold;">🎮 ${BRAND} v${VERSION} 🔒</div>
      <div style="display:flex;gap:4px;">
        <button id="cq-min" title="${minimized?'تكبير':'تصغير'}" style="background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;width:24px;height:24px;border-radius:6px;cursor:pointer;font-size:12px;">${minimized?'🔽':'🔼'}</button>
        <button id="cq-close" style="background:#8b1a1a;color:#fff;border:none;width:24px;height:24px;border-radius:50%;cursor:pointer;">✕</button>
      </div>
    </div>
    <div id="cq-body" style="${minimized ? 'display:none;' : ''}">
      ${locked ? `
        <div style="background:#8b1a1a;color:#fff;padding:8px;border-radius:6px;margin-bottom:8px;font-size:12px;text-align:center;font-weight:bold;">
          🔒 مقفول — لا يمكن بدء جولة
        </div>` : ''}

      <div style="display:flex;gap:4px;margin-bottom:8px;padding:6px;background:var(--cq-bg);border-radius:6px;font-size:11px;align-items:center;border:1px solid ${hostAdmin ? '#2e8b2e' : '#c1272d'}66;">
        <span style="flex:1;">${hostAdmin ? '👑' : '⛔'} <b>${hostName || 'غير محدد'}</b>${hostAdmin ? ' (مشرف)' : (hostName ? ' (ليس مشرفاً)' : '')}</span>
        <button id="cq-host-btn" style="padding:2px 8px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:4px;cursor:pointer;font-size:10px;">${hostName ? 'تعديل' : 'تحديد'}</button>
      </div>
      ${!hostAdmin ? `
        <div style="background:#8b1a1a;color:#fff;padding:8px;border-radius:6px;margin-bottom:8px;font-size:11px;text-align:center;line-height:1.5;">
          ${hostName
            ? `⛔ أنت (${hostName}) لست مشرفاً<br><span style="font-size:10px;opacity:0.85;">اطلب من مشرف إضافتك عبر: <b>!!${hostName}</b></span>`
            : `⚠️ لم تحدد هويتك<br><span style="font-size:10px;opacity:0.85;">اضغط "تحديد" لتسجيل اسمك</span>`}
        </div>` : ''}
      ${hostName && hostAdmin && adminsCount === 0 ? `
        <div style="background:#2e8b2e;color:#fff;padding:6px;border-radius:6px;margin-bottom:8px;font-size:10px;text-align:center;">
          ✅ أنت أول مشرف — تم تفعيل التحكم
        </div>` : ''}

      <label style="font-size:12px;">🎲 اللعبة:</label>
      <select id="cq-game" style="width:100%;padding:7px;margin:4px 0 10px;background:var(--cq-bg);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;font-family:inherit;">
        ${getPlayableGames().map(g => `<option value="${g.key}">${g.emoji||'🎯'} ${g.title}${g.custom ? ' ✨' : ''}</option>`).join('')}
      </select>
      <label style="font-size:12px;">🎯 الهدف:</label>
      <input id="cq-target" type="number" value="${targetScore}" min="1" max="50" style="width:100%;padding:7px;margin:4px 0 10px;background:var(--cq-bg);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;font-family:inherit;">
      <label style="font-size:12px;">⏱️ المدة (ث):</label>
      <input id="cq-gap" type="number" value="${gapSec}" min="1" max="30" style="width:100%;padding:7px;margin:4px 0 12px;background:var(--cq-bg);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;font-family:inherit;">
      <div style="display:flex;gap:6px;margin-bottom:8px;">
        <button id="cq-start" style="flex:1;padding:10px;background:linear-gradient(#2e8b2e,#1a5c1a);color:#fff;border:none;border-radius:6px;cursor:pointer;font-weight:bold;">${locked ? '🔒 مقفول' : '▶ بدء'}</button>
        <button id="cq-stop"  style="flex:1;padding:10px;background:linear-gradient(#c1272d,#8b1a1a);color:#fff;border:none;border-radius:6px;cursor:pointer;font-weight:bold;">⏹ إنهاء</button>
        <button id="cq-skip"  style="flex:0 0 auto;padding:10px 12px;background:linear-gradient(#d4af37,#a8842a);color:#000;border:none;border-radius:6px;cursor:pointer;font-weight:bold;" title="تجاوز السؤال الحالي">⏭️</button>
      </div>
      <div style="margin:8px 0;">
        <div style="background:var(--cq-bg);border-radius:6px;overflow:hidden;height:10px;border:1px solid var(--cq-border)33;">
          <div id="cq-progress-fill" style="height:100%;background:linear-gradient(90deg,var(--cq-progress),var(--cq-progress)dd);width:0%;transition:width 0.3s;border-radius:6px;"></div>
        </div>
        <div id="cq-progress-text" style="font-size:11px;text-align:center;color:var(--cq-text);opacity:0.7;margin-top:5px;">⏸️ متوقف</div>
      </div>
      <div style="display:flex;gap:6px;margin-bottom:8px;">
        <button id="cq-list" style="flex:1;padding:8px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;cursor:pointer;font-size:12px;">📄 عرض القائمة</button>
        <button id="cq-refresh" style="flex:1;padding:8px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;cursor:pointer;font-size:12px;">🔄 تحديث</button>
      </div>


      <details style="margin-top:8px;border-top:1px solid var(--cq-border)33;padding-top:8px;">
        <summary style="cursor:pointer;font-size:12px;color:var(--cq-text);opacity:0.9;">🎨 الألوان والستايل</summary>
        <div style="padding:8px 0;font-size:11px;display:grid;gap:6px;">
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:6px;">
            <label style="display:flex;align-items:center;gap:4px;">خلفية1 <input type="color" id="cq-c-bg" value="${theme.bg}" style="width:28px;height:20px;border:none;padding:0;background:none;cursor:pointer;"></label>
            <label style="display:flex;align-items:center;gap:4px;">خلفية2 <input type="color" id="cq-c-bg2" value="${theme.bg2}" style="width:28px;height:20px;border:none;padding:0;background:none;cursor:pointer;"></label>
            <label style="display:flex;align-items:center;gap:4px;">النص <input type="color" id="cq-c-text" value="${theme.text}" style="width:28px;height:20px;border:none;padding:0;background:none;cursor:pointer;"></label>
            <label style="display:flex;align-items:center;gap:4px;">الحدود <input type="color" id="cq-c-border" value="${theme.border}" style="width:28px;height:20px;border:none;padding:0;background:none;cursor:pointer;"></label>
            <label style="display:flex;align-items:center;gap:4px;grid-column:span 2;">التقدم <input type="color" id="cq-c-progress" value="${theme.progress}" style="width:28px;height:20px;border:none;padding:0;background:none;cursor:pointer;"></label>
          </div>
          <label style="display:flex;align-items:center;gap:6px;">حجم الخط: <input type="range" id="cq-c-fs" min="10" max="18" value="${theme.fontSize}" style="flex:1;"></label>
          <label style="display:flex;align-items:center;gap:6px;">الزوايا: <input type="range" id="cq-c-radius" min="0" max="24" value="${theme.radius}" style="flex:1;"></label>
          <div style="display:flex;gap:6px;margin-top:4px;">
            <button id="cq-theme-export" style="flex:1;padding:6px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;cursor:pointer;font-size:11px;">💾 تصدير</button>
            <button id="cq-theme-import" style="flex:1;padding:6px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;cursor:pointer;font-size:11px;">📂 استيراد</button>
            <button id="cq-theme-reset"  style="flex:1;padding:6px;background:var(--cq-bg2);color:var(--cq-text);border:1px solid var(--cq-border)55;border-radius:6px;cursor:pointer;font-size:11px;">🔄 استعادة</button>
          </div>
        </div>
      </details>
      <div id="cq-status" style="margin-top:10px;font-size:12px;text-align:center;color:var(--cq-text);opacity:0.7;padding-top:8px;border-top:1px solid var(--cq-border)33;">${locked ? '🔒 مقفول' : 'متوقف'}</div>
    </div>`;
  document.body.appendChild(panel);

  const $ = id => panel.querySelector('#' + id);
  $('cq-close').onclick = dispose;
  $('cq-min').onclick   = () => { saveMin(!loadMin()); renderPanel(); };
  $('cq-host-btn').onclick = promptHostName;

  const protectedBtns = ['cq-start','cq-stop','cq-skip','cq-list','cq-refresh','cq-target','cq-gap'];
  if (!hostAdmin) {
    protectedBtns.forEach(id => {
      const el = $(id);
      if (el) { el.disabled = true; el.style.opacity = '0.4'; el.style.cursor = 'not-allowed'; el.title = '⛔ للمشرفين فقط'; }
    });
  } else {
    $('cq-game').onchange    = e => currentGame = getAllGames().find(g => g.key === e.target.value) || currentGame;
    $('cq-target').onchange  = e => { const v = +e.target.value; if (v>=1 && v<=50) { targetScore = v; updateProgress(); } };
    $('cq-gap').onchange     = e => { const v = +e.target.value; if (v>=1 && v<=30) gapSec = v; };
    $('cq-start').onclick    = () => {
      if (!isHostAdmin()) return setStatus('⛔ صلاحية مرفوضة');
      if (isLocked()) return setStatus('🔒 مقفول — لا يمكن البدء');
      startRound();
    };
    $('cq-stop').onclick     = () => { if (!isHostAdmin()) return setStatus('⛔ صلاحية مرفوضة'); stopRound('تم الإنهاء يدوياً'); };
    $('cq-skip').onclick     = () => { if (!isHostAdmin()) return setStatus('⛔ صلاحية مرفوضة'); skipQuestion(); };
    $('cq-list').onclick     = () => showAllPages().catch(e => console.warn(e));
    $('cq-refresh').onclick  = refreshFromGitHub;
    if (locked) {
      const sb = $('cq-start');
      if (sb) { sb.disabled = true; sb.style.opacity = '0.5'; sb.style.cursor = 'not-allowed'; }
    }
  }


  ['bg','bg2','text','border','progress'].forEach(k => {
    const el = $(`cq-c-${k}`);
    if (el) el.oninput = e => {
      const t = loadTheme(); t[k] = e.target.value; saveTheme(t);
      panel.style.setProperty(`--cq-${k}`, e.target.value);
    };
  });
  const fsEl = $('cq-c-fs');
  if (fsEl) fsEl.oninput = e => {
    const t = loadTheme(); t.fontSize = +e.target.value; saveTheme(t);
    panel.style.setProperty('--cq-fs', t.fontSize + 'px');
  };
  const rdEl = $('cq-c-radius');
  if (rdEl) rdEl.oninput = e => {
    const t = loadTheme(); t.radius = +e.target.value; saveTheme(t);
    panel.style.setProperty('--cq-radius', t.radius + 'px');
  };
  $('cq-theme-export').onclick = exportTheme;
  $('cq-theme-import').onclick = importTheme;
  $('cq-theme-reset').onclick  = () => { resetTheme(); renderPanel(); };

  let drag = false, dx = 0, dy = 0;
  panel.addEventListener('mousedown', e => {
    if (e.target.closest('button,input,select,summary,details,textarea')) return;
    drag = true;
    const r = panel.getBoundingClientRect();
    dx = e.clientX - r.left; dy = e.clientY - r.top;
  });
  document.addEventListener('mousemove', e => {
    if (drag) { panel.style.left = (e.clientX - dx) + 'px'; panel.style.top = (e.clientY - dy) + 'px'; }
  });
  document.addEventListener('mouseup', () => drag = false);

  updateProgress();

  if (!getHostName() && !_hostPrompted) {
    _hostPrompted = true;
    setTimeout(() => promptHostName(), 600);
  }
}

// ═══════════ فلترة + اكتشاف ═══════════
async function refreshFromGitHub() {
  setStatus('جاري تحميل مسابقات مجلد لغويات...');
  try {
    const items = await ghListDir('لغويات');
    if (!Array.isArray(items)) throw new Error('استجابة مجلد لغويات غير صالحة');
    const files = new Set(items.filter(it => it.type === 'file' && /\.txt$/i.test(it.name)).map(it => it.name));
    const expected = new Set(GAMES.map(g => g.dataFile));
    const missing = [...expected].filter(name => !files.has(name));
    if (missing.length) throw new Error('ملفات ناقصة: ' + missing.join('، '));
    availableKeys = new Set(GAMES.map(g => g.key));
    dynamicGames = [];
    setStatus(`✅ جاهز — ${GAMES.length} مسابقة من مجلد لغويات`);
    refreshGameSelect();
    updateProgress();
  } catch (e) {
    availableKeys = new Set();
    setStatus('❌ فشل تحميل مجلد لغويات: ' + e.message);
    console.warn(`[${BRAND}] تعذر تحميل مسابقات لغويات`, e);
  }
}

// مؤقت خاص بتحدي العشر ثواني
function armQuestionTimer() {
  if (questionTimer) clearTimeout(questionTimer);
  questionTimer = null;
  const seconds = Number(currentGame?.timeLimitSeconds || 0);
  if (!seconds || !running || !activeQ) return;
  questionTimer = setTimeout(async () => {
    if (!running || !activeQ || answerLock) return;
    answerLock = true;
    activeQ = null;
    await send('⌛ انتهى الوقت! الإجابة الصحيحة: ' + (questions[qIdx - 1]?.correctDisplay || '—'));
    answerLock = false;
    if (running) setTimeout(() => nextQuestion(), 700);
  }, seconds * 1000);
}

// ═══════════ منطق اللعبة ═══════════
async function startRound() {
  if (running) return;
  if (isLocked()) { setStatus('🔒 مقفول'); return; }
  running = true; qIdx = 0; questions = []; scores.clear(); wrongCount = 0; activeQ = null;
  setStatus('جاري التحميل...'); updateProgress();
  try {
    const raw = await loadGameData(currentGame);
    if (!raw.length) throw new Error('لا توجد أسئلة');
    questions = shuffleArray(raw);
    await send(`🎮 جولة [${currentGame.title}]`);
    await sleep(1500);
    activeQ = questions[qIdx++];
    if (activeQ.isImage && activeQ.url) { await changeAvatar(activeQ.url); await sleep(1500); }
    const intro = currentGame.intro || currentGame.prompt || currentGame.title;
    const body  = activeQ.isImage ? '📸 (انظر لصورة البوت)' : activeQ.question;
    await send(`${currentGame.emoji||'🎯'} ${intro}\n${body}`);
    questionStartTime = Date.now();
    armQuestionTimer();
    setStatus(`السؤال ${qIdx}/${questions.length}`); updateProgress();
  } catch (e) {
    running = false; setStatus('❌ ' + e.message); updateProgress();
    await send('🛑 خطأ: ' + e.message);
  }
}
function stopRound(reason) {
  if (!running) return;
  running = false; activeQ = null; wrongCount = 0; questionStartTime = 0;
  if (questionTimer) clearTimeout(questionTimer); questionTimer = null;
  setStatus('متوقف'); updateProgress(); restoreAvatar();
  if (reason) send(`🛑 ${reason}`).catch(() => {});
}
async function skipQuestion() {
  if (!running || !activeQ) return setStatus('⚠️ لا يوجد سؤال نشط');
  answerLock = true;
  if (questionTimer) clearTimeout(questionTimer); questionTimer = null;
  activeQ = null;
  wrongCount = 0;
  setStatus('⏭️ تجاوز');
  try { await send('⏭️ تم تجاوز السؤال'); } catch {}
  answerLock = false;
  setTimeout(() => running && nextQuestion(), 500);
}
async function nextQuestion() {
  if (!running) return;
  if (qIdx >= questions.length) return stopRound('انتهت الأسئلة 🎉');
  activeQ = questions[qIdx++]; wrongCount = 0;
  if (activeQ.isImage && activeQ.url) { await changeAvatar(activeQ.url); await sleep(1500); }
  const intro = currentGame.intro || currentGame.prompt || currentGame.title;
  const body  = activeQ.isImage ? '📸 (انظر لصورة البوت)' : activeQ.question;
  await send(`${currentGame.emoji||'🎯'} ${intro}\n${body}`);
  questionStartTime = Date.now();
  armQuestionTimer();
  setStatus(`السؤال ${qIdx}/${questions.length}`); updateProgress();
}
async function showFinalResults() {
  await sleep(1800);
  if (scores.size) {
    const s = [...scores.values()].sort((a,b) => b.score - a.score);
    await send(`📊 نتائج الجولة:\n` + s.map((e,i) => `${i===0?'🥇':i===1?'🥈':i===2?'🥉':`${i+1}.`} ${e.raw} — ${e.score}`).join('\n'));
  }
  await sleep(1800);
  const top = getTopList(5);
  if (top.length) {
    const medals = ['🥇','🥈','🥉','4️⃣','5️⃣'];
    await send(`🏆 المتصدرون (أفضل 5):\n` + top.map((e,i) => `${medals[i]} ${e.name} — ${e.total} نقطة (${e.wins} فوز)`).join('\n'));
  }
}

// ═══════════ صورة البوت ═══════════
let originalAvatar = null;
async function changeAvatar(url) {
  try {
    if (!originalAvatar) {
      const img = document.querySelector('#mypic img') || document.querySelector('.profile-pic img');
      if (img) originalAvatar = img.src;
    }
    const res  = await fetch(url);
    const blob = await res.blob();
    if (!blob.type.startsWith('image/')) throw new Error('ليس صورة');
    const file = new File([blob], 'avatar.png', { type: blob.type });
    const orig = HTMLInputElement.prototype.click;
    HTMLInputElement.prototype.click = function() {
      if (this.type === 'file') {
        const dt = new DataTransfer(); dt.items.add(file);
        this.files = dt.files;
        this.dispatchEvent(new Event('change', { bubbles:true }));
        HTMLInputElement.prototype.click = orig;
        return;
      }
      orig.apply(this, arguments);
    };
    if (typeof TH_PIC_USER_ROOM === 'function') TH_PIC_USER_ROOM('user');
    else if (typeof sendpic === 'function') sendpic();
    setTimeout(() => { HTMLInputElement.prototype.click = orig; }, 3000);
  } catch (e) { console.warn('فشل تغيير الصورة:', e); }
}
function restoreAvatar() {
  if (!originalAvatar) return;
  changeAvatar(originalAvatar).finally(() => setTimeout(() => originalAvatar = null, 2000));
}

// ═══════════ معالجة الرسائل ═══════════
function getMsgText(node) {
  let t = '';
  const walk = n => {
    for (const c of n.childNodes || []) {
      if (c.nodeType === Node.TEXT_NODE) t += c.nodeValue || '';
      else if (c.nodeType === Node.ELEMENT_NODE && !c.matches('button,img,script,style')) {
        if (c.tagName === 'BR') t += ' '; else walk(c);
      }
    }
  };
  walk(node);
  return t;
}
function getUserName(row) { return row?.querySelector?.('.u-topic')?.textContent?.trim() || 'مجهول'; }

function handleMessage(node) {
  if (!node?.classList) return;
  const row = node.closest?.('.mm') || node;
  const msg = node.querySelector?.('.u-msg') || (node.classList?.contains('u-msg') ? node : null);
  if (!msg) return;
  const text = getMsgText(msg).trim();
  if (!text) return;
  const user = getUserName(row);

  if (text.startsWith('!') || (text.startsWith('**') && text.length >= 3)) {
    handleCommand(text, user).catch(e => console.warn(e));
    return;
  }

  if (!running || !activeQ || answerLock) return;
  if (text.length > 500) return;
  const norm = normAns(text);
  if (!norm) return;

  if (activeQ.accepted.includes(norm)) {
    answerLock = true;
    if (questionTimer) clearTimeout(questionTimer); questionTimer = null;
    const elapsed = questionStartTime ? (Date.now() - questionStartTime) / 1000 : 0;
    const next = (scores.get(user)?.score || 0) + 1;
    if (!scores.has(user)) scores.set(user, { raw:user, score:0 });
    scores.get(user).score = next;
    recordPoint(user, elapsed);
    activeQ = null; wrongCount = 0; updateProgress();
    if (next >= targetScore) {
      finishRoundStats(user);
      send(`🏆 كہٰفہٰوو يا ${user}!\nفزت بالجولة بـ ${next} نقطة 🎉🔥`).finally(() => {
        answerLock = false; stopRound(null); showFinalResults();
      });
    } else {
      send(`✅ كہٰفہٰوو يا ${user} 🎉🔥\n${next}/${targetScore}`).finally(() => {
        answerLock = false;
        setTimeout(() => running && nextQuestion(), gapSec * 1000);
      });
    }
  } else {
    wrongCount++;
    if (wrongCount >= 5) {
      wrongCount = 0;
      if (activeQ.isImage && activeQ.url) changeAvatar(activeQ.url);
      const intro = currentGame.intro || currentGame.prompt || currentGame.title;
      const body  = activeQ.isImage ? '📸 (انظر لصورة البوت)' : activeQ.question;
      send(`🔁 إعادة السؤال (5 أخطاء):\n${intro}\n${body}`);
    }
  }
}

// ═══════════ 🔒 الأوامر ═══════════
async function handleCommand(text, user) {
  const t = text.trim();
  const deny = () => send(`⛔ ${user} — هذا الأمر للمشرفين فقط 🔒`);

  const mAdd = t.match(/^!!\s*(.+)$/);
  if (mAdd) {
    if (!isAdmin(user)) return deny();
    const name = mAdd[1].trim();
    if (!name) return send(`⚠️ اكتب: !!اسم المشرف`);
    if (loadAdmins().has(normAns(name))) return send(`⚠️ ${name} مشرف مسبقاً`);
    addAdmin(name);
    return send(`✅ تمت إضافة ${name} كمشرف`);
  }
  const mRem = t.match(/^\*\*\s*(.+)$/);
  if (mRem) {
    if (!isAdmin(user)) return deny();
    const name = mRem[1].trim();
    if (!name) return send(`⚠️ اكتب: **اسم المشرف`);
    if (!loadAdmins().has(normAns(name))) return send(`⚠️ ${name} ليس مشرفاً`);
    removeAdmin(name);
    return send(`✅ تمت إزالة ${name} من المشرفين`);
  }

  if (['!مساعدة','!أوامر','!اوامر','!help'].includes(t)) {
    send(`📖 أوامر ${BRAND}:\n` +
      `━━━ 📋 للجميع ━━━\n` +
      `!م — عرض المسابقات الـ5 من مجلد لغويات\n!قائمة — ملخص\n!قائمةN — صفحة N\n` +
      `!نقاط — الجولة + كل المتصدرين (6/قائمة)\n!توب — أفضل 10\n!مشرفين — قائمة المشرفين\n` +
      `━━━ 🔒 للمشرفين ━━━\n` +
      `!بدء / !انهاء / !تجاوز\n` +
      `!تلميح / !تلميح N\n!هدف N / !مدة N\n` +
      `!<اسم اللعبة> — بدء مباشر\n!تصفير — حذف الإحصائيات\n` +
      `!قفل — قفل البدء (كل المشرفين) 🔒\n!فتح — فتح البدء 🔓\n` +
      `!!اسم — إضافة مشرف\n**اسم — إزالة مشرف\n\n` +
      `📂 مصدر الأسئلة: GitHub / لغويات`);
    return;
  }

  if (['!قفل','!lock'].includes(t)) {
    if (!isAdmin(user)) return deny();
    if (isLocked()) return send('⚠️ مقفول مسبقاً');
    setLocked(true); renderPanel();
    return send('🔒 تم قفل المسابقات');
  }
  if (['!فتح','!unlock'].includes(t)) {
    if (!isAdmin(user)) return deny();
    if (!isLocked()) return send('⚠️ مفتوح مسبقاً');
    setLocked(false); renderPanel();
    return send('🔓 تم فتح المسابقات');
  }

  if (['!مشرفين','!مشرفون'].includes(t)) {
    const a = loadAdmins();
    if (!a.size) return send(`👑 لا يوجد مشرفون بعد`);
    return send(`👑 المشرفون (${a.size}):\n` + [...a].join('\n'));
  }

  const mHint = t.match(/^!تلميح(?:\s+(\d+))?$/);
  if (mHint) {
    if (!isAdmin(user)) return deny();
    if (!running || !activeQ) return send('⚠️ لا يوجد سؤال نشط حالياً');
    const n = mHint[1] ? Math.max(1, Math.min(+mHint[1], 10)) : 2;
    const ans = activeQ.correctDisplay || activeQ.answer || '';
    return send(`💡 تلميح (آخر ${n} مخفية):\n${makeHint(ans, n)}`);
  }

  if (/^!قائم[ةه]\s*$/.test(t)) return showSummary();
  const mP = t.match(/^!قائم[ةه]\s*(\d+)$/);
  if (mP) return sendGamesPage(+mP[1]);

  if (['!مسابقات','!م'].includes(t)) return showAllPages();

  if (['!نقاط','!نتائج'].includes(t)) {
    const perPage = 6;
    if (scores.size) {
      const s = [...scores.values()].sort((a,b) => b.score - a.score);
      const pages = Math.ceil(s.length / perPage);
      for (let p = 0; p < pages; p++) {
        const slice = s.slice(p * perPage, (p+1) * perPage);
        const lines = slice.map((e, i) => {
          const idx = p * perPage + i;
          const medal = idx===0 ? '🥇' : idx===1 ? '🥈' : idx===2 ? '🥉' : `${idx+1}.`;
          return `${medal} ${e.raw} — ${e.score}`;
        });
        const header = p === 0 ? `📊 نقاط الجولة (${currentGame.title}) — ${s.length} مشارك:` : `📄 صفحة ${p+1}/${pages}:`;
        await send(header + '\n' + lines.join('\n'));
        if (p < pages - 1) await sleep(900);
      }
    } else {
      await send('📊 لا توجد نقاط في الجولة الحالية');
    }
    const top = getTopList(9999);
    if (top.length) {
      const pages = Math.ceil(top.length / perPage);
      for (let p = 0; p < pages; p++) {
        const slice = top.slice(p * perPage, (p+1) * perPage);
        const lines = slice.map((e, i) => {
          const idx = p * perPage + i;
          const medal = idx===0 ? '🥇' : idx===1 ? '🥈' : idx===2 ? '🥉' : `${idx+1}.`;
          return `${medal} ${e.name} — ${e.total} نقطة (${e.wins} فوز)`;
        });
        const header = p === 0 ? `🏆 المتصدرون المحفوظون — ${top.length} مشارك:` : `📄 صفحة ${p+1}/${pages}:`;
        await send(header + '\n' + lines.join('\n'));
        if (p < pages - 1) await sleep(900);
      }
    }
    return;
  }

  if (['!توب','!متصدرين','!top','!الأفضل'].includes(t)) {
    const top = getTopList(10);
    if (!top.length) return send('⚠️ لا توجد إحصائيات محفوظة بعد');
    const medals = ['🥇','🥈','🥉'];
    return send(`🏆 المتصدرون (أفضل ${top.length}):\n` + top.map((e,i) => {
      const medal = medals[i] || `${i+1}.`;
      return `${medal} ${e.name} — ${e.total} نقطة (${e.wins} فوز)`;
    }).join('\n'));
  }

  if (['!تصفير','!حذف','!reset'].includes(t)) {
    if (!isAdmin(user)) return deny();
    resetStats();
    return send('🗑️ تم حذف جميع الإحصائيات المحفوظة');
  }

  if (['!بدء','!ابدأ'].includes(t)) {
    if (!isAdmin(user)) return deny();
    if (running) return send('⚠️ هناك جولة جارية');
    if (isLocked()) return send('🔒 المسابقات مقفولة');
    startRound();
    return;
  }
  if (['!انهاء','!إنهاء','!انهي'].includes(t)) {
    if (!isAdmin(user)) return deny();
    stopRound('تم الإنهاء');
    return;
  }
  if (['!تجاوز','!تجاوزي'].includes(t) && running) {
    if (!isAdmin(user)) return deny();
    skipQuestion();
    return;
  }

  const mT = t.match(/^!هدف\s+(\d+)/);
  if (mT) {
    if (!isAdmin(user)) return deny();
    const v = +mT[1];
    if (v>=1 && v<=50) { targetScore = v; updateProgress(); send(`🎯 الهدف: ${v}`); }
    return;
  }
  const mG = t.match(/^!مدة\s+(\d+)/);
  if (mG) {
    if (!isAdmin(user)) return deny();
    const v = +mG[1];
    if (v>=1 && v<=30) { gapSec = v; send(`⏱️ المدة: ${v}ث`); }
    return;
  }

  if (t.length > 1) {
    const name = t.slice(1).trim();
    const game = getVisibleGames().find(g => g.key === name || g.title === name);
    if (game && game.displayOnly) {
      return send(`ℹ️ «${game.title}» مضافة إلى قائمة !م كاسم فقط، ولم يتم ربط أسئلتها بعد`);
    }
    if (game && !running) {
      if (!isAdmin(user)) return deny();
      if (isLocked()) return send('🔒 المسابقات مقفولة');
      currentGame = game;
      const sel = panel?.querySelector('#cq-game');
      if (sel) sel.value = game.key;
      startRound();
    }
  }
}

// ═══════════ Observer ═══════════
let observer = null;
function initObserver() {
  if (observer) observer.disconnect();
  observer = new MutationObserver(changes => {
    for (const c of changes)
      for (const n of c.addedNodes)
        if (n.nodeType === Node.ELEMENT_NODE) handleMessage(n);
  });
  observer.observe(chat, { childList:true, subtree:true });
}
function dispose() {
  running = false;
  observer?.disconnect(); observer = null;
  panel?.remove(); panel = null;
  restoreAvatar();
  console.log(`[${BRAND}] تم الإيقاف`);
}

// ═══════════ التشغيل ═══════════
renderPanel();
initObserver();
refreshFromGitHub();
console.log(`%c🎮 ${BRAND} v${VERSION} 🔒 جاهز!`, 'color:#d4af37;font-weight:bold;font-size:14px;');
console.log('اكتب !م لعرض المسابقات، و !مساعدة لعرض الأوامر');
window.QuizBot = {
  dispose, VERSION, BRAND,
  stats:   { get: getTopList, reset: resetStats },
  admins:  { list: () => [...loadAdmins()], add: addAdmin, remove: removeAdmin },
  theme:   { get: loadTheme, reset: resetTheme, export: exportTheme, import: importTheme },
  host:    { get: getHostName, set: setHostName, clear: clearHostName, isAdmin: isHostAdmin },
  lock:    { isLocked, set: setLocked },
  custom:  { list: loadCustomGames, add: addCustomGame, remove: removeCustomGame }
};

})();