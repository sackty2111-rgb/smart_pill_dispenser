import { useState, useEffect, useRef } from "react";
import {
  Plus, Check, Wifi, Battery, Trash2, Moon, Sun,
  AlertTriangle, Droplets, Clock, Cpu, Radio, Zap,
  Volume2, Activity,
} from "lucide-react";

/* ─── Constants ─────────────────────────────────────── */
const PINK   = "#FF2D55";
const GREEN  = "#30D158";
const BLUE   = "#007AFF";
const ORANGE = "#FF9F0A";

const HARDWARE = [
  {
    Icon: Cpu, name: "ESP32", tag: "Controller", color: BLUE,
    front: "Dual-core MCU with Wi-Fi & Bluetooth 5.0",
    back:  "Syncs with Blynk cloud over Wi-Fi. Logs every dose event in real-time and pushes instant notifications to the caregiver's mobile app whenever a pill is missed or dispensed.",
  },
  {
    Icon: Radio, name: "RTC DS3231", tag: "Timing", color: ORANGE,
    front: "±2 ppm high-accuracy real-time clock",
    back:  "Operates fully offline — maintains precise time even during Wi-Fi outages or power cuts. Schedules are stored locally, guaranteeing medication is dispensed on time regardless of connectivity.",
  },
  {
    Icon: Zap, name: "Servo Motor", tag: "Actuator", color: PINK,
    front: "MG996R — precision 180° rotation dispensing",
    back:  "Receives a PWM signal from the ESP32 to rotate the pill chamber to the exact compartment. Each dispense cycle completes in under 200 ms, with a physical stop to prevent double-dispensing.",
  },
  {
    Icon: Volume2, name: "LCD + Buzzer", tag: "Alerts", color: GREEN,
    front: "16×2 I²C display with piezo audio cues",
    back:  "Displays the medication name, scheduled time, and instructions in large text. The buzzer fires 3 short beeps before dispensing — critical accessibility feature for elderly or visually impaired patients.",
  },
];

/* ─── SVG Activity Rings ─────────────────────────────── */
function ActivityRings({ adherence, onTime }) {
  const [filled, setFilled] = useState(false);
  useEffect(() => { const t = setTimeout(() => setFilled(true), 700); return () => clearTimeout(t); }, []);
  const cx = 90, cy = 90;
  const rings = [
    { r: 70, pct: onTime,    color: BLUE,  bg: `${BLUE}28`,  sw: 13 },
    { r: 50, pct: adherence, color: PINK,  bg: `${PINK}28`,  sw: 13 },
  ];
  return (
    <svg width={180} height={180} viewBox="0 0 180 180">
      {rings.map(({ r, pct, color, bg, sw }) => {
        const c = 2 * Math.PI * r;
        const off = filled ? c - (pct / 100) * c : c;
        return (
          <g key={r}>
            <circle cx={cx} cy={cy} r={r} fill="none" stroke={bg} strokeWidth={sw} />
            <circle cx={cx} cy={cy} r={r} fill="none" stroke={color} strokeWidth={sw}
              strokeDasharray={c} strokeDashoffset={off} strokeLinecap="round"
              transform={`rotate(-90 ${cx} ${cy})`}
              style={{ transition: "stroke-dashoffset 1.3s cubic-bezier(0.34,1.56,0.64,1)" }} />
          </g>
        );
      })}
      <text x={cx} y={cy - 9}  textAnchor="middle" fill={PINK}       fontSize={24} fontWeight={800} fontFamily="-apple-system,sans-serif">{adherence}%</text>
      <text x={cx} y={cy + 13} textAnchor="middle" fill="rgba(142,142,147,1)" fontSize={11} fontFamily="-apple-system,sans-serif">Adherence</text>
    </svg>
  );
}

/* ─── 3D Flip Card ───────────────────────────────────── */
function FlipCard({ data, dark, isFlipped, onClick }) {
  const { Icon, name, tag, color, front, back } = data;
  const face = {
    position: "absolute", width: "100%", height: "100%",
    borderRadius: 18, padding: "16px 15px",
    backfaceVisibility: "hidden", WebkitBackfaceVisibility: "hidden",
    boxSizing: "border-box",
  };
  return (
    <div onClick={onClick} style={{ perspective: 900, cursor: "pointer", height: 158 }}>
      <div style={{
        position: "relative", width: "100%", height: "100%",
        transformStyle: "preserve-3d",
        transform: isFlipped ? "rotateY(180deg)" : "rotateY(0deg)",
        transition: "transform 0.55s cubic-bezier(0.4,0,0.2,1)",
      }}>
        {/* Front */}
        <div style={{ ...face, background: dark ? "#2C2C2E" : "#fff", display: "flex", flexDirection: "column", justifyContent: "space-between" }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
            <div style={{ width: 40, height: 40, borderRadius: 12, background: `${color}1E`, display: "flex", alignItems: "center", justifyContent: "center" }}>
              <Icon size={22} color={color} strokeWidth={1.8} />
            </div>
            <span style={{ fontSize: 9, fontWeight: 700, color: "#fff", background: color, padding: "3px 8px", borderRadius: 20, textTransform: "uppercase", letterSpacing: "0.06em" }}>{tag}</span>
          </div>
          <div>
            <p style={{ margin: 0, fontWeight: 700, fontSize: 14, color: dark ? "#fff" : "#1C1C1E" }}>{name}</p>
            <p style={{ margin: "4px 0 0", fontSize: 12, color: "#8E8E93", lineHeight: 1.45 }}>{front}</p>
          </div>
          <p style={{ margin: 0, fontSize: 11, color: color, fontWeight: 600 }}>Tap to explore →</p>
        </div>
        {/* Back */}
        <div style={{ ...face, background: `${color}16`, transform: "rotateY(180deg)", border: `1px solid ${color}44`, display: "flex", flexDirection: "column", justifyContent: "center", gap: 8 }}>
          <p style={{ margin: 0, fontSize: 12, fontWeight: 700, color }}>{name} — Technical Detail</p>
          <p style={{ margin: 0, fontSize: 12, color: dark ? "rgba(255,255,255,0.72)" : "#3A3A3C", lineHeight: 1.6 }}>{back}</p>
          <p style={{ margin: 0, fontSize: 10, color: "#8E8E93" }}>Tap again to flip back</p>
        </div>
      </div>
    </div>
  );
}

/* ─── Main App ───────────────────────────────────────── */
export default function SmartPillDispenser() {
  const [dark, setDark] = useState(false);
  const [reminders, setReminders] = useState([
    { id: 1, time: "08:00", medication: "Metformin",    dosage: "500mg", taken: true  },
    { id: 2, time: "13:00", medication: "Lisinopril",   dosage: "10mg",  taken: false },
    { id: 3, time: "20:00", medication: "Atorvastatin", dosage: "20mg",  taken: false },
  ]);
  const [newTime,   setNewTime]   = useState("");
  const [newMed,    setNewMed]    = useState("");
  const [newDosage, setNewDosage] = useState("");
  const [now,       setNow]       = useState(new Date());
  const [barsIn,    setBarsIn]    = useState(false);
  const [hydTarget, setHydTarget] = useState(null);   // hydration modal
  const [overdueT,  setOverdueT]  = useState(null);   // overdue banner
  const [flipped,   setFlipped]   = useState(null);   // flip card index
  const shownHyd = useRef(new Set());
  const shownOvd = useRef(new Set());

  /* Theme tokens */
  const T = {
    bg:    dark ? "#000"     : "#F2F2F7",
    card:  dark ? "#1C1C1E"  : "#FFFFFF",
    card2: dark ? "#2C2C2E"  : "#F2F2F7",
    text:  dark ? "#FFFFFF"  : "#1C1C1E",
    muted: "#8E8E93",
    border:dark ? "#3A3A3C"  : "#E5E5EA",
  };
  const sl = { fontSize: 12, fontWeight: 700, textTransform: "uppercase", letterSpacing: "0.08em", color: T.muted, marginBottom: 10, paddingLeft: 2 };
  const card = { background: T.card, borderRadius: 20, padding: "20px 22px", marginBottom: 10 };

  /* Live clock */
  useEffect(() => { const t = setInterval(() => setNow(new Date()), 1000); return () => clearInterval(t); }, []);
  /* Bar animation */
  useEffect(() => { const t = setTimeout(() => setBarsIn(true), 500); return () => clearTimeout(t); }, []);

  /* Notification logic — runs each second */
  useEffect(() => {
    const mStr = now.toLocaleTimeString("en-MY", { timeZone: "Asia/Kuala_Lumpur", hour12: false, hour: "2-digit", minute: "2-digit" });
    const [h, m] = mStr.split(":").map(Number);
    const nowMins = h * 60 + m;
    for (const r of reminders) {
      if (r.taken) continue;
      const [rh, rm] = r.time.split(":").map(Number);
      const diff = rh * 60 + rm - nowMins;
      if (!hydTarget && diff >= 4 && diff <= 6 && !shownHyd.current.has(r.id)) {
        setHydTarget(r); shownHyd.current.add(r.id); break;
      }
      if (!overdueT && diff <= -5 && diff >= -60 && !shownOvd.current.has(r.id)) {
        setOverdueT(r); shownOvd.current.add(r.id); break;
      }
    }
  }, [now]);  // eslint-disable-line

  /* Derived state */
  const taken   = reminders.filter(r => r.taken).length;
  const total   = reminders.length;
  const todayPct = total > 0 ? Math.round((taken / total) * 100) : 0;
  const onTimePct = total > 0 ? Math.round((taken / total) * 91) : 0;

  const timeStr = now.toLocaleTimeString("en-MY", { timeZone: "Asia/Kuala_Lumpur", hour12: false, hour: "2-digit", minute: "2-digit", second: "2-digit" });
  const dateStr = now.toLocaleDateString("en-MY",  { timeZone: "Asia/Kuala_Lumpur", weekday: "long", year: "numeric", month: "long", day: "numeric" });

  /* Actions */
  const addReminder = () => {
    if (!newTime || !newMed.trim()) return;
    setReminders(p => [...p, { id: Date.now(), time: newTime, medication: newMed.trim(), dosage: newDosage.trim() || "—", taken: false }]);
    setNewTime(""); setNewMed(""); setNewDosage("");
  };
  const toggle = (id) => setReminders(p => p.map(r => r.id === id ? { ...r, taken: !r.taken } : r));
  const remove = (id) => setReminders(p => p.filter(r => r.id !== id));
  const markTakenFromBanner = () => { if (overdueT && overdueT.id !== "demo") toggle(overdueT.id); setOverdueT(null); };
  const triggerDemo = () => {
    const pending = reminders.find(r => !r.taken);
    setOverdueT(pending ?? { id: "demo", time: "12:00", medication: "Demo Medication", dosage: "100mg" });
  };
  const triggerHydDemo = () => {
    const pending = reminders.find(r => !r.taken);
    if (pending) setHydTarget(pending);
  };

  const weekData = [
    { day: "Mon", val: 100 }, { day: "Tue", val: 75 }, { day: "Wed", val: 100 },
    { day: "Thu", val: 67  }, { day: "Fri", val: 90 }, { day: "Sat", val: 80  },
    { day: "Today", val: todayPct, isToday: true },
  ];

  /* ─── Render ─── */
  return (
    <div style={{ fontFamily: '-apple-system,BlinkMacSystemFont,"SF Pro Display","Helvetica Neue",sans-serif', background: T.bg, minHeight: "100vh", color: T.text, transition: "background 0.3s,color 0.3s" }}>

      {/* ══ OVERDUE BANNER ══ */}
      {overdueT && (
        <div style={{ position: "fixed", top: 0, left: 0, right: 0, zIndex: 1000, display: "flex", justifyContent: "center", padding: "10px 14px", pointerEvents: "none" }}>
          <div style={{ background: "#FF3B30", borderRadius: 18, padding: "13px 18px", display: "flex", alignItems: "center", gap: 13, maxWidth: 520, width: "100%", pointerEvents: "all", boxShadow: "0 10px 40px rgba(255,59,48,0.45)", animation: "slideDown 0.4s cubic-bezier(0.34,1.56,0.64,1)" }}>
            <div style={{ width: 36, height: 36, borderRadius: "50%", background: "rgba(255,255,255,0.18)", display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
              <AlertTriangle size={20} color="white" />
            </div>
            <div style={{ flex: 1 }}>
              <p style={{ margin: 0, fontWeight: 700, fontSize: 14, color: "white" }}>Missed Dose Warning</p>
              <p style={{ margin: "2px 0 0", fontSize: 12, color: "rgba(255,255,255,0.82)", lineHeight: 1.4 }}>{overdueT.medication} — Caregiver will be alerted in 3 minutes. Tap to log as taken.</p>
            </div>
            <button onClick={markTakenFromBanner} style={{ background: "white", color: "#FF3B30", border: "none", borderRadius: 12, padding: "8px 14px", fontSize: 13, fontWeight: 700, cursor: "pointer", whiteSpace: "nowrap", flexShrink: 0 }}>Log Taken</button>
            <button onClick={() => setOverdueT(null)} style={{ background: "rgba(255,255,255,0.2)", color: "white", border: "none", borderRadius: 12, padding: "8px 11px", fontSize: 13, cursor: "pointer" }}>✕</button>
          </div>
        </div>
      )}

      {/* ══ HYDRATION MODAL ══ */}
      {hydTarget && (
        <div style={{ position: "fixed", inset: 0, zIndex: 999, background: "rgba(0,0,0,0.52)", backdropFilter: "blur(10px)", WebkitBackdropFilter: "blur(10px)", display: "flex", alignItems: "center", justifyContent: "center", padding: 24, animation: "fadeIn 0.25s ease" }}>
          <div style={{ background: dark ? "rgba(28,28,30,0.96)" : "rgba(255,255,255,0.96)", backdropFilter: "blur(20px)", WebkitBackdropFilter: "blur(20px)", borderRadius: 30, padding: "36px 28px", maxWidth: 360, width: "100%", textAlign: "center", border: `1px solid ${T.border}`, animation: "scaleIn 0.35s cubic-bezier(0.34,1.56,0.64,1)" }}>
            <div style={{ width: 68, height: 68, borderRadius: "50%", background: `${BLUE}1E`, display: "flex", alignItems: "center", justifyContent: "center", margin: "0 auto 20px" }}>
              <Droplets size={34} color={BLUE} />
            </div>
            <h2 style={{ margin: "0 0 10px", fontSize: 22, fontWeight: 800, letterSpacing: "-0.02em", color: T.text }}>Preparation Time</h2>
            <p style={{ margin: "0 0 6px", fontSize: 16, color: T.muted, lineHeight: 1.6 }}>Please drink a glass of water.</p>
            <p style={{ margin: "0 0 30px", fontSize: 14, fontWeight: 600, color: BLUE }}>{hydTarget.medication} · dispensing in 5 minutes.</p>
            <button onClick={() => setHydTarget(null)} style={{ width: "100%", padding: "15px", borderRadius: 15, background: BLUE, color: "white", border: "none", cursor: "pointer", fontSize: 16, fontWeight: 700, letterSpacing: "-0.01em" }}>I'm Ready ✓</button>
          </div>
        </div>
      )}

      {/* ══ HERO ══ */}
      <div style={{ padding: "56px 24px 42px", textAlign: "center", background: dark ? "linear-gradient(180deg,#1C1C1E 0%,#000 100%)" : "linear-gradient(180deg,#fff 0%,#F2F2F7 100%)", position: "relative", overflow: "hidden" }}>
        {/* Decorative glow */}
        <div style={{ position: "absolute", top: -100, left: "50%", transform: "translateX(-50%)", width: 600, height: 400, borderRadius: "50%", background: `radial-gradient(circle, ${PINK}0C 0%, transparent 65%)`, pointerEvents: "none" }} />

        {/* Dark mode toggle */}
        <button onClick={() => setDark(d => !d)} style={{ position: "absolute", top: 18, right: 18, width: 42, height: 42, borderRadius: "50%", background: T.card2, border: `1px solid ${T.border}`, cursor: "pointer", display: "flex", alignItems: "center", justifyContent: "center", color: T.muted, transition: "background 0.3s" }}>
          {dark ? <Sun size={18} /> : <Moon size={18} />}
        </button>

        <div style={{ display: "inline-flex", alignItems: "center", gap: 6, background: dark ? "#1C2E40" : "#E8F3FF", borderRadius: 20, padding: "6px 14px", marginBottom: 22, color: BLUE, fontSize: 13, fontWeight: 700 }}>
          <Activity size={14} /> IoT Medical System
        </div>

        <h1 style={{ fontSize: "clamp(34px,7vw,62px)", fontWeight: 800, letterSpacing: "-0.04em", lineHeight: 1.07, margin: "0 0 14px" }}>
          Never Miss<br />
          <span style={{ background: `linear-gradient(135deg,${PINK} 0%,${ORANGE} 100%)`, WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent", backgroundClip: "text" }}>a Dose Again.</span>
        </h1>
        <p style={{ fontSize: 17, color: T.muted, lineHeight: 1.65, maxWidth: 380, margin: "0 auto 28px" }}>
          Automated IoT pill dispensing with real-time cloud monitoring, caregiver alerts, and precision scheduling.
        </p>

        {/* Device status pill */}
        <div style={{ display: "inline-flex", alignItems: "center", gap: 14, background: dark ? "rgba(28,28,30,0.85)" : "rgba(255,255,255,0.85)", backdropFilter: "blur(12px)", WebkitBackdropFilter: "blur(12px)", borderRadius: 50, padding: "10px 22px", border: `1px solid ${T.border}`, marginBottom: 34 }}>
          <div style={{ display: "flex", alignItems: "center", gap: 7 }}>
            <div style={{ width: 9, height: 9, borderRadius: "50%", background: GREEN, boxShadow: `0 0 10px ${GREEN}`, animation: "pulse 2s ease-in-out infinite" }} />
            <span style={{ fontSize: 13, fontWeight: 600, color: T.text }}>System Online</span>
          </div>
          <div style={{ width: 1, height: 14, background: T.border }} />
          <div style={{ display: "flex", alignItems: "center", gap: 4, color: T.muted }}>
            <Wifi size={14} /><span style={{ fontSize: 12 }}>Connected</span>
          </div>
          <div style={{ display: "flex", alignItems: "center", gap: 4, color: GREEN }}>
            <Battery size={14} /><span style={{ fontSize: 12, fontWeight: 600 }}>98%</span>
          </div>
        </div>

        {/* Product illustration card */}
        <div style={{ maxWidth: 260, margin: "0 auto", background: dark ? "#2C2C2E" : "#fff", borderRadius: 28, padding: 24, boxShadow: dark ? "0 8px 48px rgba(0,0,0,0.6)" : "0 12px 48px rgba(0,0,0,0.09)" }}>
          <div style={{ background: T.bg, borderRadius: 20, height: 158, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", gap: 8, position: "relative" }}>
            <div style={{ width: 52, height: 76, background: PINK, borderRadius: "26px 26px 14px 14px", display: "flex", alignItems: "flex-start", justifyContent: "center", paddingTop: 10, boxShadow: `0 10px 28px ${PINK}55` }}>
              <div style={{ width: 20, height: 7, background: "rgba(255,255,255,0.6)", borderRadius: 4 }} />
            </div>
            <div style={{ width: 68, height: 9, background: T.border, borderRadius: 5, marginTop: 4 }} />
            <div style={{ display: "flex", gap: 5, position: "absolute", bottom: 16 }}>
              {[PINK, GREEN, BLUE].map((c, i) => <div key={i} style={{ width: 10, height: 10, borderRadius: "50%", background: c, opacity: 0.85 }} />)}
            </div>
          </div>
          <p style={{ margin: "13px 0 0", fontSize: 12, color: T.muted, textAlign: "center" }}>Smart Pill Dispenser · Prototype v1</p>
        </div>
      </div>

      {/* ══ CONTENT ══ */}
      <div style={{ maxWidth: 680, margin: "0 auto", padding: "0 14px 70px" }}>

        {/* ── CLOCK + DEMO BUTTONS ── */}
        <div style={{ marginBottom: 28, marginTop: 22 }}>
          <p style={sl}>Live Clock · Malaysia (UTC+8)</p>
          <div style={{ background: dark ? "#1C1C1E" : "#000", borderRadius: 20, padding: "28px 24px", textAlign: "center", position: "relative", overflow: "hidden" }}>
            <div style={{ position: "absolute", top: -70, right: -70, width: 220, height: 220, borderRadius: "50%", background: "rgba(255,45,85,0.07)", pointerEvents: "none" }} />
            <div style={{ display: "flex", alignItems: "center", justifyContent: "center", gap: 7, color: "rgba(255,255,255,0.32)", fontSize: 13, marginBottom: 10 }}>
              <Clock size={13} /> Kuala Lumpur · Asia/Kuala_Lumpur
            </div>
            <div style={{ fontSize: "clamp(52px,12vw,90px)", fontWeight: 200, color: "#fff", letterSpacing: "-0.04em", fontVariantNumeric: "tabular-nums", lineHeight: 1 }}>{timeStr}</div>
            <p style={{ fontSize: 14, color: "rgba(255,255,255,0.32)", marginTop: 10, marginBottom: 0 }}>{dateStr}</p>
          </div>
          <div style={{ display: "flex", gap: 8, marginTop: 8 }}>
            <button onClick={triggerDemo} style={{ flex: 1, padding: "13px", borderRadius: 14, background: "#FF3B30", color: "white", border: "none", cursor: "pointer", fontSize: 14, fontWeight: 700, display: "flex", alignItems: "center", justifyContent: "center", gap: 8 }}>
              <AlertTriangle size={16} /> Trigger Demo Alert
            </button>
            <button onClick={triggerHydDemo} style={{ flex: 1, padding: "13px", borderRadius: 14, background: BLUE, color: "white", border: "none", cursor: "pointer", fontSize: 14, fontWeight: 700, display: "flex", alignItems: "center", justifyContent: "center", gap: 8 }}>
              <Droplets size={16} /> Hydration Demo
            </button>
          </div>
        </div>

        {/* ── MEDICATION TIMELINE ── */}
        <div style={{ marginBottom: 28 }}>
          <p style={sl}>Medication Timeline</p>
          {/* Add form */}
          <div style={card}>
            <p style={{ margin: "0 0 12px", fontSize: 13, fontWeight: 600, color: T.muted }}>Add New Reminder</p>
            <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
              <input type="time" value={newTime} onChange={e => setNewTime(e.target.value)}
                style={{ flex: "0 0 108px", padding: "10px 12px", borderRadius: 12, border: `1.5px solid ${T.border}`, fontSize: 15, outline: "none", background: T.card2, color: T.text, fontFamily: "inherit" }} />
              <input type="text" placeholder="Medication name…" value={newMed} onChange={e => setNewMed(e.target.value)} onKeyDown={e => e.key === "Enter" && addReminder()}
                style={{ flex: 1, minWidth: 120, padding: "10px 12px", borderRadius: 12, border: `1.5px solid ${T.border}`, fontSize: 15, outline: "none", background: T.card2, color: T.text, fontFamily: "inherit" }} />
              <input type="text" placeholder="Dosage" value={newDosage} onChange={e => setNewDosage(e.target.value)}
                style={{ flex: "0 0 86px", padding: "10px 12px", borderRadius: 12, border: `1.5px solid ${T.border}`, fontSize: 15, outline: "none", background: T.card2, color: T.text, fontFamily: "inherit" }} />
              <button onClick={addReminder} style={{ padding: "10px 18px", borderRadius: 12, background: PINK, color: "white", border: "none", cursor: "pointer", fontSize: 15, fontWeight: 700, display: "flex", alignItems: "center", gap: 6, whiteSpace: "nowrap" }}>
                <Plus size={16} strokeWidth={2.5} /> Add
              </button>
            </div>
          </div>

          {/* Summary chips */}
          <div style={{ display: "flex", gap: 8, marginBottom: 10, flexWrap: "wrap" }}>
            <span style={{ fontSize: 12, fontWeight: 600, padding: "5px 12px", borderRadius: 20, background: "#E8FAF0", color: GREEN }}>✓ {taken} Taken</span>
            <span style={{ fontSize: 12, fontWeight: 600, padding: "5px 12px", borderRadius: 20, background: T.card, color: T.muted }}>◌ {total - taken} Pending</span>
            <span style={{ fontSize: 12, fontWeight: 600, padding: "5px 12px", borderRadius: 20, background: "#FFE5EC", color: PINK }}>{todayPct}% Today</span>
          </div>

          {/* Reminder list */}
          {reminders.length === 0
            ? <div style={{ ...card, textAlign: "center", color: T.muted, padding: "32px 24px" }}>No reminders yet — add your first medication above.</div>
            : [...reminders].sort((a, b) => a.time.localeCompare(b.time)).map(r => (
              <div key={r.id} style={{ background: T.card, borderRadius: 18, padding: "14px 18px", marginBottom: 8, display: "flex", alignItems: "center", gap: 12, borderLeft: `3px solid ${r.taken ? GREEN : T.border}`, transition: "border-color 0.3s,opacity 0.3s", opacity: r.taken ? 0.62 : 1 }}>
                <button onClick={() => toggle(r.id)} style={{ width: 30, height: 30, borderRadius: "50%", border: `2px solid ${r.taken ? GREEN : T.border}`, background: r.taken ? GREEN : "transparent", display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer", flexShrink: 0, transition: "all 0.25s" }}>
                  {r.taken && <Check size={14} color="white" strokeWidth={3} />}
                </button>
                <div style={{ flex: 1 }}>
                  <p style={{ margin: 0, fontWeight: 600, fontSize: 15, color: r.taken ? T.muted : T.text, textDecoration: r.taken ? "line-through" : "none" }}>
                    {r.medication} <span style={{ fontWeight: 400, fontSize: 13, color: T.muted }}>· {r.dosage}</span>
                  </p>
                  <p style={{ margin: "3px 0 0", fontSize: 12, color: T.muted }}>{r.time} · MYT</p>
                </div>
                <span style={{ fontSize: 10, fontWeight: 700, padding: "4px 10px", borderRadius: 20, background: r.taken ? "#E8FAF0" : T.card2, color: r.taken ? GREEN : T.muted, textTransform: "uppercase", letterSpacing: "0.05em", whiteSpace: "nowrap" }}>{r.taken ? "Taken" : "Pending"}</span>
                <button onClick={() => remove(r.id)} style={{ background: "none", border: "none", cursor: "pointer", color: "#FF3B30", padding: 5, borderRadius: 8, display: "flex", alignItems: "center", opacity: 0.65 }}>
                  <Trash2 size={15} />
                </button>
              </div>
            ))
          }
        </div>

        {/* ── APPLE HEALTH DATA ── */}
        <div style={{ marginBottom: 28 }}>
          <p style={sl}>Apple Health Data</p>
          <div style={{ display: "grid", gridTemplateColumns: "auto 1fr", gap: 12 }}>

            {/* Activity rings */}
            <div style={{ background: T.card, borderRadius: 20, padding: "18px 16px", display: "flex", flexDirection: "column", alignItems: "center", gap: 14 }}>
              <ActivityRings adherence={todayPct} onTime={onTimePct} />
              <div style={{ display: "flex", gap: 18 }}>
                {[{ c: PINK, label: "Adherence", val: `${todayPct}%` }, { c: BLUE, label: "On-Time", val: `${onTimePct}%` }].map(({ c, label, val }) => (
                  <div key={label} style={{ textAlign: "center" }}>
                    <div style={{ display: "flex", alignItems: "center", gap: 5, marginBottom: 2, justifyContent: "center" }}>
                      <div style={{ width: 8, height: 8, borderRadius: "50%", background: c }} />
                      <span style={{ fontSize: 11, color: T.muted }}>{label}</span>
                    </div>
                    <p style={{ margin: 0, fontWeight: 800, fontSize: 17, color: c }}>{val}</p>
                  </div>
                ))}
              </div>
            </div>

            {/* Weekly bar chart */}
            <div style={{ background: T.card, borderRadius: 20, padding: "18px 18px" }}>
              <p style={{ margin: "0 0 2px", fontSize: 13, color: T.muted }}>Usage Insights</p>
              <p style={{ margin: "0 0 4px", fontSize: 28, fontWeight: 800, color: PINK, letterSpacing: "-0.03em", lineHeight: 1 }}>{taken}/{total}</p>
              <p style={{ margin: "0 0 16px", fontSize: 13, color: T.muted }}>doses taken today</p>
              <div style={{ display: "flex", alignItems: "flex-end", height: 90, gap: 6 }}>
                {weekData.map(item => (
                  <div key={item.day} style={{ flex: 1, display: "flex", flexDirection: "column", alignItems: "center", gap: 5 }}>
                    <div style={{ width: "100%", display: "flex", flexDirection: "column", justifyContent: "flex-end", height: 68 }}>
                      <div style={{ width: "100%", height: barsIn ? `${Math.round((item.val / 100) * 68)}px` : "0px", background: item.isToday ? PINK : item.val >= 90 ? "#C7F0D8" : T.border, borderRadius: "5px 5px 0 0", transition: "height 0.85s cubic-bezier(0.34,1.56,0.64,1)", transitionDelay: item.isToday ? "0.5s" : "0.1s" }} />
                    </div>
                    <span style={{ fontSize: 9, color: item.isToday ? PINK : T.muted, fontWeight: item.isToday ? 700 : 400 }}>{item.day}</span>
                  </div>
                ))}
              </div>
            </div>
          </div>
        </div>

        {/* ── HARDWARE FLIP CARDS ── */}
        <div style={{ marginBottom: 28 }}>
          <p style={sl}>Hardware Deep Dive · Tap cards to explore</p>
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(140px,1fr))", gap: 12 }}>
            {HARDWARE.map((hw, i) => (
              <FlipCard key={hw.name} data={hw} dark={dark} isFlipped={flipped === i} onClick={() => setFlipped(flipped === i ? null : i)} />
            ))}
          </div>
        </div>

        {/* ── FOOTER ── */}
        <div style={{ textAlign: "center", paddingTop: 30, borderTop: `1px solid ${T.border}` }}>
          <p style={{ fontSize: 11, fontWeight: 700, letterSpacing: "0.22em", textTransform: "uppercase", color: T.muted, margin: "0 0 8px" }}>A Project By</p>
          <p style={{ fontSize: "clamp(20px,4.5vw,30px)", fontWeight: 800, letterSpacing: "-0.025em", margin: 0, background: `linear-gradient(135deg,${PINK} 0%,${ORANGE} 100%)`, WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent", backgroundClip: "text" }}>SMK Dato Bijaya Setia</p>
          <p style={{ fontSize: 12, color: T.muted, marginTop: 16 }}>Smart IoT Medical Systems · {new Date().getFullYear()}</p>
        </div>
      </div>

      {/* Global keyframes */}
      <style>{`
        @keyframes pulse     { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.55;transform:scale(1.35)} }
        @keyframes slideDown { from{transform:translateY(-120%);opacity:0} to{transform:translateY(0);opacity:1} }
        @keyframes fadeIn    { from{opacity:0} to{opacity:1} }
        @keyframes scaleIn   { from{transform:scale(0.86);opacity:0} to{transform:scale(1);opacity:1} }
      `}</style>
    </div>
  );
}
