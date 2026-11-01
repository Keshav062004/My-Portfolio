<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { siGithub } from "simple-icons";

gsap.registerPlugin(ScrollTrigger);

const sectionRef = ref<HTMLElement | null>(null);
const solvedCount = ref(0);
let ctx: gsap.Context | null = null;
let countFrame = 0;
let countStarted = false;
const hoverCleanup: (() => void)[] = [];

const achievements = [
  {
    value: "100+",
    label: "Problems solved on LeetCode",
    description:
      "Sharpening problem-solving through consistent DSA practice, from arrays and graphs to dynamic programming.",
    countUp: true,
    icon: "code",
    position: "upper-right",
  },
  {
    value: "Open Source",
    label: "Contributor",
    description:
      "Contributed to open-source projects through Hacktoberfest and Social Winter of Code, learning real collaboration.",
    icon: "github",
    position: "right",
  },
  {
    value: "Team Captain",
    label: "Hostel cricket",
    description:
      "Led my university hostel cricket team to tournament victory, building teamwork, strategy, and calm under pressure.",
    icon: "trophy",
    position: "lower-right",
  },
];

const countToHundred = () => {
  cancelAnimationFrame(countFrame);
  solvedCount.value = 0;
  const start = performance.now();
  const duration = 1500;

  const update = (now: number) => {
    const progress = Math.min((now - start) / duration, 1);
    solvedCount.value = Math.round(progress * 100);
    if (progress < 1) countFrame = requestAnimationFrame(update);
  };

  countFrame = requestAnimationFrame(update);
};

onMounted(() => {
  const section = sectionRef.value;
  if (!section) return;

  ctx = gsap.context(() => {
    const reducedMotion = window.matchMedia(
      "(prefers-reduced-motion: reduce)",
    ).matches;
    const heading = section.querySelector(".achievements-heading");
    const orbitCenter = section.querySelector(".orbit-center");
    const nodeGroup = section.querySelector(".orbit-node-group");
    const labels = Array.from(
      section.querySelectorAll<HTMLElement>(".achievement-node-copy"),
    );

    if (reducedMotion) {
      solvedCount.value = 100;
      if (heading) gsap.set(heading, { clearProps: "all" });
    } else {
      const timeline = gsap.timeline({
        scrollTrigger: {
          trigger: section,
          start: "top 78%",
          end: "bottom 62%",
          scrub: 0.7,
        },
      });

      if (heading) {
        timeline.fromTo(
          heading,
          { opacity: 0, y: 30 },
          { opacity: 1, y: 0, duration: 0.45, ease: "power3.out" },
        );
      }

      if (orbitCenter) {
        timeline.fromTo(
          orbitCenter,
          { opacity: 0, y: 32, scale: 0.97 },
          { opacity: 1, y: 0, scale: 1, duration: 0.5, ease: "power3.out" },
          0.18,
        );
      }

      if (nodeGroup) {
        timeline.fromTo(
          nodeGroup,
          { opacity: 0, y: 32, scale: 0.97 },
          { opacity: 1, y: 0, scale: 1, duration: 0.5, ease: "power3.out" },
          0.18,
        );
      }
    }

    const countNode = section.querySelector<HTMLElement>(
      '.achievement-node[data-count-up="true"]',
    );
    if (countNode && !reducedMotion) {
      ScrollTrigger.create({
        trigger: countNode,
        start: "top 84%",
        onEnter: () => {
          if (!countStarted) {
            countStarted = true;
            countToHundred();
          }
        },
        onEnterBack: () => {
          countStarted = true;
          countToHundred();
        },
        onLeaveBack: () => {
          cancelAnimationFrame(countFrame);
          solvedCount.value = 0;
          countStarted = false;
        },
      });
    }

    if (!reducedMotion) {
      labels.forEach((label) => {
        const handleMouseMove = (event: MouseEvent) => {
          const rect = label.getBoundingClientRect();
          const rotateY = ((event.clientX - rect.left) / rect.width) * 6 - 3;
          const rotateX = ((event.clientY - rect.top) / rect.height - 0.5) * -6;
          gsap.to(label, {
            rotateX,
            rotateY,
            transformPerspective: 1000,
            duration: 0.3,
            ease: "power2.out",
            overwrite: true,
          });
        };
        const handleMouseLeave = () => {
          gsap.to(label, {
            rotateX: 0,
            rotateY: 0,
            duration: 0.5,
            ease: "power3.out",
          });
        };

        label.addEventListener("mousemove", handleMouseMove);
        label.addEventListener("mouseleave", handleMouseLeave);
        hoverCleanup.push(() => {
          label.removeEventListener("mousemove", handleMouseMove);
          label.removeEventListener("mouseleave", handleMouseLeave);
        });
      });
    }
  }, section);
});

onUnmounted(() => {
  if (countFrame) cancelAnimationFrame(countFrame);
  hoverCleanup.forEach((cleanup) => cleanup());
  hoverCleanup.length = 0;
  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section id="achievements" ref="sectionRef" class="achievements-section">
    <div class="achievements-layout">
      <header class="achievements-heading">
        <p class="achievements-eyebrow">MILESTONES &amp; RECOGNITIONS</p>
        <h2>Achievements<span>.</span></h2>
        <p class="achievements-intro">
          Every milestone here taught me something beyond the classroom: how to
          solve problems under pressure, collaborate in open source, and lead a
          team toward a shared goal. These achievements reflect the curiosity and
          commitment I bring to every project I build.
        </p>
      </header>

      <div class="achievements-orbit" aria-label="Achievement milestones">
        <div class="orbit-layer" aria-hidden="true">
          <div class="orbit-rings">
            <div class="orbit-ring-glow"></div>
            <div class="orbit-ring orbit-ring-inner"></div>
            <div class="orbit-ring orbit-ring-middle"></div>
            <div class="orbit-ring orbit-ring-outer"></div>
          </div>
          <span class="orbit-dot-path">
            <span class="orbit-dot"></span>
          </span>
        </div>

        <div class="orbit-center" aria-hidden="true">
          <svg class="trophy-art" viewBox="0 0 180 180">
            <g class="laurel" fill="#ff8a00">
              <path d="M39 121c-13-15-18-35-14-55l5 1c-3 17 1 34 13 47z" />
              <ellipse cx="27" cy="99" rx="4" ry="8" transform="rotate(-38 27 99)" />
              <ellipse cx="25" cy="84" rx="4" ry="8" transform="rotate(-22 25 84)" />
              <ellipse cx="27" cy="69" rx="4" ry="8" transform="rotate(-7 27 69)" />
              <ellipse cx="33" cy="56" rx="4" ry="8" transform="rotate(12 33 56)" />
              <ellipse cx="42" cy="46" rx="4" ry="8" transform="rotate(32 42 46)" />
              <path d="M141 121c13-15 18-35 14-55l-5 1c3 17-1 34-13 47z" />
              <ellipse cx="153" cy="99" rx="4" ry="8" transform="rotate(38 153 99)" />
              <ellipse cx="155" cy="84" rx="4" ry="8" transform="rotate(22 155 84)" />
              <ellipse cx="153" cy="69" rx="4" ry="8" transform="rotate(7 153 69)" />
              <ellipse cx="147" cy="56" rx="4" ry="8" transform="rotate(-12 147 56)" />
              <ellipse cx="138" cy="46" rx="4" ry="8" transform="rotate(-32 138 46)" />
            </g>
            <g class="trophy-shape">
              <path d="M55 48h70v30c0 20-13 36-30 40v10h17v9H68v-9h17v-10c-17-4-30-20-30-40V48Z" />
              <path d="M55 57H39v13c0 17 10 29 25 32l3-9c-10-2-17-10-17-22v-5h5v-9Zm70 0h16v13c0 17-10 29-25 32l-3-9c10-2 17-10 17-22v-5h-5v-9Z" />
              <path class="trophy-highlight" d="M62 55h9v23c0 12 7 22 17 27-15-3-26-16-26-32V55Z" />
              <path d="M68 137h44v8H68z" />
              <path class="trophy-star" d="m90 57 3.2 7 7.6.8-5.7 5.1 1.6 7.4-6.7-3.8-6.7 3.8 1.6-7.4-5.7-5.1 7.6-.8L90 57Z" />
            </g>
          </svg>
        </div>

        <div class="orbit-node-group">
          <article
            v-for="item in achievements"
            :key="item.value"
            class="achievement-node"
            :class="`achievement-node-${item.position}`"
            :data-count-up="item.countUp || undefined"
          >
            <div class="achievement-node-inner">
              <span class="achievement-node-bubble" aria-hidden="true">
                <svg
                  v-if="item.icon === 'code'"
                  class="code-icon"
                  viewBox="0 0 32 32"
                  fill="none"
                >
                  <path d="m11 8-8 8 8 8M21 8l8 8-8 8M19 5l-6 22" />
                </svg>
                <svg
                  v-else-if="item.icon === 'github'"
                  viewBox="0 0 24 24"
                  role="presentation"
                >
                  <path :d="siGithub.path" />
                </svg>
                <svg v-else viewBox="0 0 32 32" role="presentation">
                  <path
                    d="M9 4h14v5.2c0 4.4-2.5 7.9-6 8.8v4h5v4H10v-4h5v-4c-3.5-.9-6-4.4-6-8.8V4Zm-5 2h3v3.2c0 2.8-1 4.9-3 6.1l-2-3c1.3-.8 2-1.8 2-3.1V6Zm24 0v3.2c0 1.3.7 2.3 2 3.1l-2 3c-2-1.2-3-3.3-3-6.1V6h3Z"
                  />
                  <path d="M12 7h8v2h-8z" fill="#ffb347" />
                </svg>
              </span>
              <svg
                class="achievement-connector"
                viewBox="0 0 48 16"
                aria-hidden="true"
              >
                <path d="M0 8h43m-5-5 5 5-5 5" />
              </svg>
              <span class="achievement-node-copy">
                <strong v-if="item.countUp" class="achievement-count">
                  {{ solvedCount }}{{ solvedCount === 100 ? "+" : "" }}
                </strong>
                <strong v-else>{{ item.value }}</strong>
                <span class="achievement-label">{{ item.label }}</span>
                <span class="achievement-description">{{ item.description }}</span>
              </span>
            </div>
          </article>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped lang="scss">
.achievements-section {
  width: 100%;
  max-width: 100vw;
  padding: 72px var(--space-outer, 24px);
  overflow: hidden;
  background: #f5efe5;
  box-sizing: border-box;
}

.achievements-layout {
  display: grid;
  grid-template-columns: minmax(46%, 0.9fr) minmax(0, 1.1fr);
  align-items: center;
  gap: clamp(16px, 3vw, 40px);
  width: min(1240px, 100%);
  margin: 0 auto;
}

.achievements-heading {
  min-width: 0;
  max-width: 620px;
}

.achievements-eyebrow {
  margin: 0 0 18px;
  color: #ff8a00;
  font-size: 0.78rem;
  font-weight: 800;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.achievements-heading h2 {
  margin: 0;
  color: #292622;
  font-size: clamp(2.4rem, 4.4vw, 4.6rem);
  font-weight: 800;
  letter-spacing: -0.05em;
  line-height: 0.98;
  white-space: nowrap;
}

.achievements-heading h2 span {
  color: #ff8a00;
}

.achievements-intro {
  max-width: 520px;
  margin: 24px 0 0;
  color: #716b62;
  font-size: clamp(1.1rem, 1.5vw, 1.3rem);
  line-height: 1.65;
}

.achievements-orbit {
  position: relative;
  width: min(640px, 92vw, 100%);
  aspect-ratio: 1;
  justify-self: center;
  isolation: isolate;
}

.orbit-motion {
  position: absolute;
  top: 50%;
  left: 30%;
  width: 52%;
  height: 52%;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: orbit-spin 60s linear infinite;
}

.orbit-ring {
  position: absolute;
  top: 50%;
  left: 50%;
  border: 1px solid rgba(255, 138, 0, 0.18);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.orbit-ring-inner {
  width: 50%;
  height: 50%;
  border-color: rgba(255, 138, 0, 0.16);
}

.orbit-ring-middle {
  width: 68%;
  height: 68%;
  border-color: rgba(255, 138, 0, 0.16);
}

.orbit-ring-outer {
  width: 86%;
  height: 86%;
  border-color: rgba(255, 138, 0, 0.16);
}

.orbit-ring-outermost,
.orbit-ring-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  border: 1px solid rgba(255, 138, 0, 0.16);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.orbit-ring-glow {
  z-index: -1;
  background: rgba(255, 255, 255, 0.35);
}

.orbit-center {
  position: absolute;
  top: 50%;
  left: 30%;
  z-index: 1;
  display: grid;
  width: 36%;
  aspect-ratio: 1;
  place-items: center;
  border-radius: 50%;
  background:
    radial-gradient(circle at 50% 45%, rgba(255, 138, 0, 0.12), transparent 68%),
    #fffaf3;
  box-shadow: 0 0 80px rgba(255, 138, 0, 0.28);
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.orbit-center svg {
  width: 56%;
  fill: #ff8a00;
}

.icon-highlight {
  fill: rgba(255, 255, 255, 0.55);
  stroke: rgba(255, 255, 255, 0.7);
  stroke-width: 1;
}

.orbit-dot {
  position: absolute;
  top: 0;
  left: 50%;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #ff8a00;
  box-shadow: 0 0 14px rgba(255, 138, 0, 0.75);
  transform: translateX(-50%);
}

.orbit-dot-path {
  position: absolute;
  inset: 0;
  animation: orbit-dot-spin 40s linear infinite;
}

.achievement-node {
  position: absolute;
  width: 0;
  height: 0;
}

.achievement-node-upper-right {
  top: 24%;
  left: 71.9%;
}

.achievement-node-right {
  top: 50%;
  left: 84%;
}

.achievement-node-lower-right {
  top: 76%;
  left: 71.9%;
}

.achievement-node-counter {
  position: absolute;
  top: 0;
  left: 0;
  animation: orbit-counter-spin 60s linear infinite;
}

.achievement-node-label {
  position: absolute;
  top: 0;
  left: 0;
  width: 0;
  height: 0;
  color: inherit;
  text-decoration: none;
}

.achievement-node-copy:hover {
  filter: drop-shadow(0 8px 16px rgba(55, 42, 25, 0.1));
}

.achievement-node-bubble {
  position: absolute;
  top: 0;
  left: 0;
  transform: translate(-50%, -50%);
}

.achievement-node-label::before {
  position: absolute;
  top: 0;
  left: 42px;
  width: 40px;
  height: 1px;
  background: rgba(255, 138, 0, 0.45);
  content: "";
}

.achievement-node-label::after {
  position: absolute;
  top: -3px;
  left: 78px;
  width: 6px;
  height: 6px;
  border-top: 1px solid rgba(255, 138, 0, 0.65);
  border-right: 1px solid rgba(255, 138, 0, 0.65);
  content: "";
  transform: rotate(45deg);
}

.achievement-node-copy {
  position: absolute;
  top: 0;
  left: 82px;
  width: clamp(100px, 12vw, 180px);
  transform: translateY(-50%);
  transition: filter 0.2s ease;
}

.achievement-node-bubble {
  display: grid;
  width: 72px;
  height: 72px;
  flex: 0 0 72px;
  place-items: center;
  clip-path: polygon(25% 3%, 75% 3%, 97% 25%, 97% 75%, 75% 97%, 25% 97%, 3% 75%, 3% 25%);
  background: #ffffff;
  filter: drop-shadow(0 14px 34px rgba(55, 42, 25, 0.1));
  color: #ff8a00;
}

.achievement-node-bubble svg {
  width: 30px;
  height: 30px;
  fill: #ff8a00;
}

.achievement-node-bubble .icon-highlight {
  fill: rgba(255, 255, 255, 0.55);
}

.code-icon {
  color: #ff8a00;
  font-size: 20px;
  font-weight: 850;
  letter-spacing: -0.08em;
}

.achievement-node-copy {
  display: flex;
  min-width: 0;
  flex-direction: column;
  gap: 5px;
  pointer-events: auto;
}

.achievement-node-copy strong {
  color: #292622;
  font-size: 1.2rem;
  font-weight: 800;
  line-height: 1.15;
}

.achievement-node-copy .achievement-count {
  color: #ff8a00;
  font-size: 2rem;
  font-weight: 850;
  letter-spacing: -0.04em;
}

.achievement-node-copy > span {
  color: #716b62;
  font-size: 0.98rem;
  max-width: 240px;
  line-height: 1.5;
}

@keyframes orbit-spin {
  to { transform: translate(-50%, -50%) rotate(360deg); }
}

@keyframes orbit-counter-spin {
  to { transform: rotate(-360deg); }
}

@keyframes orbit-dot-spin {
  to { transform: rotate(360deg); }
}

@media (max-width: 899px) {
  .achievements-heading {
    max-width: 100%;
  }
}

@media (max-width: 600px) {
  .achievements-layout {
    grid-template-columns: minmax(0, 1fr);
    gap: 24px;
  }

  .achievements-heading {
    text-align: left;
  }

  .achievements-intro {
    margin-inline: 0;
    font-size: clamp(1.1rem, 1.5vw, 1.3rem);
  }

  .orbit-motion {
    left: 32%;
    width: 50%;
    height: 50%;
  }

  .orbit-center {
    left: 32%;
  }

  .achievement-node-bubble {
    width: 64px;
    height: 64px;
    flex-basis: 64px;
  }

  .achievement-node-bubble svg {
    width: 25px;
    height: 25px;
  }

  .achievement-node-label::before {
    left: 32px;
    width: 24px;
  }

  .achievement-node-label::after {
    left: 52px;
  }

  .achievement-node-copy {
    left: 60px;
    width: clamp(100px, 15vw, 150px);
  }

  .achievement-node-copy strong {
    font-size: clamp(0.95rem, 4vw, 1.2rem);
  }

  .achievement-node-copy .achievement-count {
    font-size: clamp(1.5rem, 7vw, 2rem);
  }

  .achievement-node-copy > span {
    font-size: 0.95rem;
    line-height: 1.35;
  }
}

@media (prefers-reduced-motion: reduce) {
  .orbit-motion,
  .orbit-dot-path,
  .achievement-node-counter {
    animation: none !important;
  }
}

.achievements-orbit {
  width: min(600px, 92vw, 100%);
  overflow: visible;
}

.orbit-layer {
  position: absolute;
  inset: 0;
  z-index: 1;
  pointer-events: none;
}

.orbit-rings {
  position: absolute;
  top: 50%;
  left: -2%;
  width: 72%;
  height: 72%;
  border-radius: 50%;
  transform: translateY(-50%);
  animation: achievements-ring-rotation 60s linear infinite;
  transform-origin: center;
}

.orbit-ring-glow,
.orbit-ring,
.orbit-ring-outermost {
  z-index: 1;
}

.orbit-ring-glow {
  background: rgba(255, 255, 255, 0.3);
}

.orbit-ring-inner {
  width: 56%;
  height: 56%;
}

.orbit-ring-middle {
  width: 76%;
  height: 76%;
}

.orbit-ring-outer {
  width: 96%;
  height: 96%;
}

.orbit-center {
  left: 34%;
  z-index: 2;
  width: 34%;
}

.orbit-dot-path {
  pointer-events: none;
  animation: achievements-dot-rotation 40s linear infinite;
}

.orbit-node-group {
  position: absolute;
  top: 50%;
  left: 34%;
  z-index: 4;
  width: 72%;
  height: 72%;
  transform: translate(-50%, -50%);
  transform-origin: center;
  animation: none;
}

.orbit-node-group .achievement-node {
  top: 0;
  left: 0;
  z-index: 4;
}

.orbit-node-group .achievement-node-upper-right {
  top: 12%;
  left: 78%;
}

.orbit-node-group .achievement-node-right {
  top: 50%;
  left: 88%;
}

.orbit-node-group .achievement-node-lower-right {
  top: 88%;
  left: 78%;
}

.achievement-node-inner {
  position: absolute;
  top: 0;
  left: 0;
  width: 0;
  height: 0;
  animation: none;
}

.achievement-node-bubble {
  position: absolute;
  z-index: 4;
  width: 84px;
  height: 84px;
  border: 1px solid rgba(255, 138, 0, 0.2);
  border-radius: 28px;
  clip-path: polygon(25% 3%, 75% 3%, 97% 25%, 97% 75%, 75% 97%, 25% 97%, 3% 75%, 3% 25%);
  background: #fff;
  box-shadow: 0 14px 34px rgba(55, 42, 25, 0.1);
  filter: none;
}

.achievement-leader {
  position: absolute;
  top: 0;
  left: 42px;
  z-index: 3;
  width: 24px;
  height: 1px;
  background: rgba(255, 138, 0, 0.45);
}

.achievement-leader::after {
  position: absolute;
  top: -3px;
  right: 0;
  width: 6px;
  height: 6px;
  border-top: 1px solid rgba(255, 138, 0, 0.65);
  border-right: 1px solid rgba(255, 138, 0, 0.65);
  content: "";
  transform: rotate(45deg);
}

.achievement-node-copy {
  top: 0;
  left: 78px;
  width: clamp(120px, 13vw, 160px);
  white-space: normal;
  overflow-wrap: anywhere;
  transform: translateY(-50%);
}

.achievement-node-upper-right .achievement-node-copy {
  top: -12px;
  transform: translateY(-100%);
}

.achievement-node-lower-right .achievement-node-copy {
  top: 12px;
  transform: translateY(0);
}

.achievement-node-copy > span {
  max-width: none;
}

@keyframes achievements-ring-rotation {
  from {
    transform: translateY(-50%) rotate(0deg);
  }
  to {
    transform: translateY(-50%) rotate(360deg);
  }
}

@keyframes achievements-dot-rotation {
  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 899px) {
  .orbit-rings {
    left: -8%;
    width: 72%;
    height: 72%;
  }

  .orbit-node-group {
    left: 28%;
    width: 58%;
    height: 58%;
  }

  .orbit-center {
    left: 28%;
  }

  .achievement-node-bubble {
    width: 64px;
    height: 64px;
  }

  .achievement-leader {
    left: 32px;
    width: 16px;
  }

  .achievement-node-copy {
    left: 56px;
    width: clamp(96px, 14vw, 120px);
  }
}

@media (max-width: 600px) {
  .achievements-layout {
    grid-template-columns: minmax(0, 1fr);
  }

  .orbit-rings {
    left: -1%;
    width: 58%;
    height: 58%;
  }

  .orbit-node-group {
    left: 28%;
    width: 58%;
    height: 58%;
  }

  .orbit-center {
    left: 28%;
    width: 19.72%;
  }

  .achievement-node-bubble {
    width: 64px;
    height: 64px;
  }

  .achievement-leader {
    left: 32px;
    width: 16px;
  }

  .achievement-node-copy {
    left: 56px;
    width: clamp(100px, 28vw, 112px);
  }

  .achievement-node-copy > span {
    font-size: 0.95rem;
  }
}

@media (prefers-reduced-motion: reduce) {
  .orbit-rings,
  .orbit-node-group,
  .orbit-dot-path,
  .achievement-node-inner {
    animation: none !important;
  }
}

.orbit-ring-glow {
  background: rgba(255, 255, 255, 0.35);
}

.orbit-rings {
  left: -1%;
  width: 58%;
  height: 58%;
}

.orbit-ring-inner {
  width: 56%;
  height: 56%;
}

.orbit-ring-middle {
  width: 76%;
  height: 76%;
}

.orbit-ring-outer {
  width: 96%;
  height: 96%;
}

.orbit-center {
  left: 28%;
  width: 34%;
  background: radial-gradient(circle, rgba(255, 138, 0, 0.22), #fffaf3 68%);
}

.orbit-center::before {
  position: absolute;
  inset: 8px;
  border: 1px solid rgba(255, 138, 0, 0.25);
  border-radius: 50%;
  content: "";
  pointer-events: none;
}

.orbit-center .trophy-art {
  width: 100%;
  overflow: visible;
  animation: trophy-float 4s ease-in-out infinite alternate;
}

.trophy-shape {
  fill: #ff8a00;
}

.trophy-highlight {
  fill: #ffb347;
}

.trophy-star {
  fill: #fffaf3;
}

.orbit-node-group {
  top: 50%;
  left: -8%;
  width: 72%;
  height: 72%;
  z-index: 4;
  transform: translateY(-50%);
  animation: none;
}

.orbit-node-group .achievement-node-upper-right,
.orbit-node-group .achievement-node-lower-right {
  left: 74.4%;
}

.orbit-node-group .achievement-node-upper-right {
  top: 20.9%;
}

.orbit-node-group .achievement-node-right {
  top: 50%;
  left: 88%;
}

.orbit-node-group .achievement-node-lower-right {
  top: 79.1%;
}

.achievement-node-inner {
  animation: none;
}

.achievement-node-bubble {
  display: grid;
  place-items: center;
  overflow: visible;
  border: 0;
  border-radius: 0;
  clip-path: polygon(25% 3%, 75% 3%, 97% 25%, 97% 75%, 75% 97%, 25% 97%, 3% 75%, 3% 25%);
  background: linear-gradient(180deg, #fff 0%, #fff7ec 100%);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.9);
  filter: drop-shadow(0 14px 20px rgba(55, 42, 25, 0.1));
}

.achievement-node-bubble::before {
  position: absolute;
  inset: 1px;
  border: 1px solid rgba(255, 138, 0, 0.24);
  clip-path: polygon(25% 3%, 75% 3%, 97% 25%, 97% 75%, 75% 97%, 25% 97%, 3% 75%, 3% 25%);
  content: "";
  pointer-events: none;
}

.achievement-node-bubble svg {
  z-index: 1;
  width: 30px;
  height: 30px;
  fill: #ff8a00;
}

.achievement-node-bubble .code-icon {
  fill: none;
  stroke: #ff8a00;
  stroke-width: 2.6;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.achievement-connector {
  position: absolute;
  top: -8px;
  left: 42px;
  z-index: 3;
  width: 48px;
  height: 16px;
  overflow: visible;
  fill: none;
  stroke: rgba(255, 138, 0, 0.55);
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-width: 1.5;
}

.achievement-node-copy {
  z-index: 4;
  top: 0;
  left: 106px;
  width: 240px;
  gap: 2px;
  translate: 0 -50%;
  transform: none;
}

.achievement-node-upper-right .achievement-node-copy {
  top: 0;
  transform: none;
  translate: 0 -110%;
}

.achievement-node-lower-right .achievement-node-copy {
  top: 0;
  transform: none;
  translate: 0 40%;
}

.achievement-node-copy strong {
  color: #292622;
  font-size: 1.25rem;
  font-weight: 800;
}

.achievement-node-copy .achievement-count {
  color: #ff8a00;
  font-size: 2.2rem;
  font-weight: 850;
  line-height: 1;
}

.achievement-node-copy .achievement-label {
  color: #716b62;
  font-size: 1rem;
  line-height: 1.55;
}

.achievement-node-copy .achievement-description {
  color: #716b62;
  font-size: 0.95rem;
  line-height: 1.45;
}

.achievement-node-copy > span {
  max-width: none;
}

@keyframes trophy-float {
  to {
    transform: translateY(-6px);
  }
}

@media (max-width: 899px) {
  .orbit-center {
    width: 36%;
  }

  .achievement-node-copy {
    width: 180px;
  }
}

@media (min-width: 601px) and (max-width: 899px) {
  .achievements-orbit {
    height: 860px;
    aspect-ratio: auto;
  }

  .orbit-center {
    top: 120px;
    left: 21.5%;
    width: 34%;
  }

  .orbit-rings {
    top: 120px;
    left: -7.5%;
    width: 58%;
    height: auto;
    aspect-ratio: 1;
  }

  .orbit-node-group {
    top: 270px;
    left: 0;
    display: flex;
    width: 100%;
    height: auto;
    flex-direction: column;
    gap: 20px;
    transform: none;
    animation: none;
  }

  .orbit-node-group .achievement-node {
    position: relative;
    top: auto;
    left: auto;
    width: 100%;
    height: auto;
  }

  .achievement-node-inner {
    position: relative;
    top: auto;
    left: auto;
    display: grid;
    width: 100%;
    height: auto;
    grid-template-columns: 64px 48px 180px;
    align-items: center;
    transform: none;
    animation: none;
  }

  .achievement-node-bubble {
    position: relative;
    top: auto;
    left: auto;
    width: 64px;
    height: 64px;
    transform: none;
  }

  .achievement-node-bubble svg {
    width: 25px;
    height: 25px;
  }

  .achievement-connector {
    position: relative;
    top: auto;
    left: auto;
  }

  .achievement-node-copy {
    position: relative;
    top: auto;
    left: auto;
    width: 180px;
    margin-left: 16px;
    transform: none;
    translate: none;
  }

  .achievement-node-upper-right .achievement-node-copy,
  .achievement-node-right .achievement-node-copy,
  .achievement-node-lower-right .achievement-node-copy {
    transform: none;
    translate: none;
  }

  .achievement-node-copy strong {
    font-size: 1rem;
  }

  .achievement-node-copy .achievement-count {
    font-size: 1.8rem;
  }

  .achievement-node-copy .achievement-label,
  .achievement-node-copy .achievement-description {
    font-size: 0.95rem;
    line-height: 1.35;
  }

  .orbit-dot-path {
    top: 120px;
    left: -7.5%;
    width: 58%;
    height: auto;
    aspect-ratio: 1;
  }
}

@media (min-width: 900px) and (max-width: 1199px) {
  .orbit-node-group {
    left: -17%;
    width: 72%;
    height: 72%;
  }

  .orbit-rings {
    left: -17%;
    width: 72%;
    height: 72%;
  }

  .orbit-center {
    left: 19%;
    width: 30%;
  }

  .achievement-node-copy {
    width: 160px;
  }
}

@media (max-width: 600px) {
  .achievements-orbit {
    height: 910px;
    aspect-ratio: auto;
  }

  .achievements-section {
    padding-block: 48px;
  }

  .orbit-center {
    top: 135px;
    left: 21.5%;
    width: 40%;
  }

  .orbit-rings {
    top: 135px;
    left: -7.5%;
    width: 58%;
    height: auto;
    aspect-ratio: 1;
  }

  .orbit-node-group {
    top: 300px;
    left: 0;
    display: flex;
    width: 100%;
    height: auto;
    flex-direction: column;
    gap: 24px;
    transform: none;
    animation: none;
  }

  .orbit-node-group .achievement-node {
    position: relative;
    top: auto;
    left: auto;
    width: 100%;
    height: auto;
  }

  .achievement-node-inner {
    position: relative;
    top: auto;
    left: auto;
    display: grid;
    width: 100%;
    height: auto;
    grid-template-columns: 64px 48px 160px;
    align-items: center;
    transform: none;
    animation: none;
  }

  .achievement-node-bubble {
    position: relative;
    top: auto;
    left: auto;
    transform: none;
  }

  .achievement-connector {
    position: relative;
    top: auto;
    left: auto;
    width: 48px;
    height: 16px;
  }

  .achievement-node-copy {
    position: relative;
    top: auto;
    left: auto;
    width: 160px;
    margin-left: 16px;
    transform: none;
    translate: none;
  }

  .achievement-node-upper-right .achievement-node-copy,
  .achievement-node-right .achievement-node-copy,
  .achievement-node-lower-right .achievement-node-copy {
    transform: none;
    translate: none;
  }

  .achievement-node-copy strong {
    font-size: 1rem;
  }

  .achievement-node-copy .achievement-count {
    font-size: 1.8rem;
  }

  .achievement-node-copy .achievement-label,
  .achievement-node-copy .achievement-description {
    font-size: 0.95rem;
    line-height: 1.35;
  }

  .orbit-dot-path {
    top: 135px;
    left: -7.5%;
    width: 58%;
    height: auto;
    aspect-ratio: 1;
  }
}

.orbit-dot-path {
  position: absolute;
  top: 50%;
  left: -8%;
  width: 72%;
  height: 72%;
  transform: translateY(-50%);
  animation: achievements-dot-orbit 40s linear infinite;
}

.orbit-dot-path .orbit-dot {
  top: 0;
  left: 50%;
}

@keyframes achievements-dot-orbit {
  from {
    transform: translateY(-50%) rotate(0deg);
  }
  to {
    transform: translateY(-50%) rotate(360deg);
  }
}

@media (max-width: 600px) {
  .orbit-dot-path {
    top: 135px;
    left: -7.5%;
    height: auto;
    aspect-ratio: 1;
    transform: translateY(-50%);
  }
}

@media (prefers-reduced-motion: reduce) {
  .orbit-rings,
  .orbit-node-group,
  .orbit-dot-path,
  .achievement-node-inner,
  .orbit-center .trophy-art {
    animation: none !important;
  }
}
</style>
