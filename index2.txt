import React, { useEffect, useState } from "react";

// Full-page responsive React component with a waving Swedish flag background
// Tailwind CSS utility classes are used for styling (no imports required).
// Exports a default component ready to drop into a React app.

export default function CountdownFlagPage() {
  const target = new Date(2025, 11, 22, 0, 0, 0); // December is month 11 (0-indexed)

  const calcRemaining = () => {
    const now = new Date();
    const diff = Math.max(0, target.getTime() - now.getTime());

    const days = Math.floor(diff / (1000 * 60 * 60 * 24));
    const hours = Math.floor((diff / (1000 * 60 * 60)) % 24);
    const minutes = Math.floor((diff / (1000 * 60)) % 60);
    const seconds = Math.floor((diff / 1000) % 60);

    return { diff, days, hours, minutes, seconds };
  };

  const [timeLeft, setTimeLeft] = useState(calcRemaining());

  useEffect(() => {
    if (timeLeft.diff <= 0) return;
    const t = setInterval(() => {
      setTimeLeft(calcRemaining());
    }, 1000);
    return () => clearInterval(t);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  // Respect reduced motion preference: stop animation if user prefers reduced motion.
  const prefersReducedMotion = typeof window !== "undefined" && window.matchMedia && window.matchMedia("(prefers-reduced-motion: reduce)").matches;

  return (
    <div className="min-h-screen w-screen relative overflow-hidden bg-gray-900 text-white flex items-center justify-center">
      {/* Waving Swedish flag SVG as a fullscreen background */}
      <svg
        aria-hidden="true"
        className="pointer-events-none absolute inset-0 w-full h-full -z-10"
        preserveAspectRatio="none"
        viewBox="0 0 1600 900"
        xmlns="http://www.w3.org/2000/svg"
      >
        <defs>
          {/* Turbulence + displacement filter to create a subtle waving effect */}
          <filter id="waveFilter">
            <feTurbulence
              type="fractalNoise"
              baseFrequency="0.002 0.009"
              numOctaves="3"
              seed="2"
              result="noise"
            >
              {!prefersReducedMotion ? (
                <animate attributeName="baseFrequency" dur="6s" values="0.002 0.009;0.003 0.01;0.002 0.009" repeatCount="indefinite" />
              ) : null}
            </feTurbulence>
            <feDisplacementMap in="SourceGraphic" in2="noise" scale="18" xChannelSelector="R" yChannelSelector="G" />
          </filter>
        </defs>

        {/* The flag is drawn using rectangles: blue background + yellow Nordic cross */}
        <g filter={prefersReducedMotion ? undefined : "url(#waveFilter)"}>
          {/* Blue background */}
          <rect x="0" y="0" width="1600" height="900" fill="#006aa7" />

          {/* Yellow cross - vertical */}
          <rect x="480" y="0" width="160" height="900" fill="#fecc00" />

          {/* Yellow cross - horizontal */}
          <rect x="0" y="360" width="1600" height="160" fill="#fecc00" />
        </g>

        {/* A subtle overlay to darken the background so the countdown pops */}
        <rect x="0" y="0" width="1600" height="900" fill="black" opacity="0.16" />
      </svg>

      {/* Main content container */}
      <main className="relative z-10 max-w-4xl w-full px-6 sm:px-10 py-12 flex flex-col items-center text-center">
        <h1 className="text-3xl sm:text-5xl lg:text-6xl font-extrabold tracking-tight drop-shadow-lg">
          Countdown tot 22 december 2025
        </h1>

        <p className="mt-3 text-sm sm:text-base text-white/90 max-w-2xl">
          Een live countdown — volledig responsive en toegankelijk. De achtergrond toont een subtiel
          bewegende Zweedse vlag (respects reduced-motion instellingen).
        </p>

        <section
          role="timer"
          aria-live="polite"
          aria-atomic="true"
          className="mt-10 bg-white/10 backdrop-blur-sm border border-white/10 rounded-2xl px-6 py-8 w-full max-w-3xl shadow-xl"
        >
          <div className="flex flex-col sm:flex-row items-center justify-center gap-6">
            <TimeCard label="Dagen" value={timeLeft.days} />
            <TimeCard label="Uren" value={timeLeft.hours} />
            <TimeCard label="Minuten" value={timeLeft.minutes} />
            <TimeCard label="Seconden" value={timeLeft.seconds} />
          </div>

          {/* If the timer reached zero show celebration message */}
          {timeLeft.diff <= 0 && (
            <div className="mt-6 text-green-200 font-semibold text-lg">Het is 22 december 2025! 🎉</div>
          )}

          <div className="mt-6 text-xs text-white/60">Target date (lokale tijd): 22 dec 2025 00:00:00</div>
        </section>

        {/* Small footer */}
        <footer className="mt-8 text-xs text-white/60">
          Gemaakt met ❤️ — responsive en toegankelijk.
        </footer>
      </main>

      {/* Decorative bottom-left accent for small screens */}
      <div className="pointer-events-none absolute left-3 bottom-3 text-white/20 text-xs sm:text-sm">
        Svenska flaggan • Countdown
      </div>
    </div>
  );
}

function TimeCard({ label, value }) {
  return (
    <div className="min-w-[90px] sm:min-w-[120px] bg-white/6 border border-white/6 rounded-xl px-4 py-3 flex flex-col items-center">
      <div className="text-3xl sm:text-4xl font-bold tabular-nums">{String(value).padStart(2, "0")}</div>
      <div className="mt-1 text-xs sm:text-sm text-white/70 uppercase tracking-wider">{label}</div>
    </div>
  );
}
