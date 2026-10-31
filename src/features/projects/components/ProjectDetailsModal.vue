<script setup lang="ts">
import { onBeforeUnmount, watch } from "vue";
import { lenis } from "../../../composables/useScroll";

interface ProjectScreenshot {
  src: string;
  label: string;
  caption: string;
}

interface ProjectArchitecture {
  nodes: { title: string; subtitle: string; icon: string }[];
  secondary: { label: string; icon: string }[];
}

interface ProjectDetail {
  id: number;
  visual: "compiler" | "health" | "security" | "finance";
  title: string;
  subtitle: string;
  type: string;
  description: string;
  url: string;
  github?: string;
  stack: string[];
  overview: string;
  features: string[];
  highlights: { title: string; text: string }[];
  next: string[];
  whatsNext?: { title: string; description: string }[];
  screenshots: ProjectScreenshot[];
  accent: "orange" | "blue" | "red";
  architecture: ProjectArchitecture;
}

const props = defineProps<{
  project: ProjectDetail | null;
  open: boolean;
}>();

const emit = defineEmits<{
  close: [];
}>();

/* =========================================================
   REAL PROJECT SCREENSHOTS
========================================================= */

const resolveScreenshotSrc = (shot: ProjectScreenshot): string => shot.src;

/* =========================================================
   ICON SYSTEM
   All icons are inline SVGs so no icon package is required.
========================================================= */

const ICONS: Record<string, string> = {
  code: "M9 6 4 12l5 6M15 6l5 6-5 6M13 4l-2 16",
  terminal: "M5 7l5 5-5 5M12 17h7",
  play: "M8 5v14l11-7L8 5Z",
  download: "M12 4v10m0 0 4-4m-4 4-4-4M5 20h14",
  copy: "M8 8h10v10H8zM6 16H4V4h12v2",
  input: "M4 6h16M4 12h10M4 18h16M16 10l3 2-3 2",
  shield: "M12 3 20 7v5c0 5-3.5 8-7 9-3.5-1-7-4-7-9V7l7-4Z",
  scan: "M5 8V5h3M19 8V5h-3M5 16v3h3M19 16v3h-3M9 12h6M12 9v6",
  lock: "M7 10V8a5 5 0 0 1 10 0v2M6 10h12v10H6z",
  database: "M5 6c0-2 14-2 14 0v12c0 2-14 2-14 0V6ZM5 6c0 2 14 2 14 0M5 12c0 2 14 2 14 0",
  server: "M4 5h16v5H4zM4 14h16v5H4zM7 7h.01M7 16h.01",
  cloud: "M7 18h10a4 4 0 0 0 .5-8A6 6 0 0 0 6 8a4 4 0 0 0 1 10Z",
  react: "M12 8.2c4.8 0 8.7 1.7 8.7 3.8s-3.9 3.8-8.7 3.8S3.3 14.1 3.3 12 7.2 8.2 12 8.2Zm0 0c2.4-4.2 5.4-6.8 7.2-5.8s.8 5.1-1.6 9.3-5.4 6.8-7.2 5.8-.8-5.1 1.6-9.3Zm0 0c-2.4-4.2-1.6-8.3.3-9.3s4.8 1.6 7.2 5.8 3.2 8.3 1.6 9.3-4.8-1.6-7.2-5.8Z",
  node: "M12 3 4.2 7.5v9L12 21l7.8-4.5v-9L12 3Zm0 4 4.3 2.5v5L12 17l-4.3-2.5v-5L12 7Z",
  express: "M4 17V7l8-4 8 4v10l-8 4-8-4Zm4-2 4 2 4-2V9l-4-2-4 2v6Z",
  mongo: "M12 3c-1 4-1 6 0 8 1-2 1-4 0-8Zm0 8c-2 3-2 6 0 10 2-4 2-7 0-10Z",
  websocket: "M5 7h14M5 12h14M5 17h14M8 4l-3 3 3 3M16 14l3 3-3 3",
  docker: "M4 12h16M6 9h2v3H6zM9 9h2v3H9zM12 9h2v3h-2zM15 9h2v3h-2z",
  github: "M12 3a9 9 0 0 0-2.8 17.6c.4.1.5-.2.5-.4v-1.6c-2.2.5-2.7-1-2.7-1-.4-.9-.9-1.1-.9-1.1-.7-.5.1-.5.1-.5.8.1 1.2.8 1.2.8.7 1.2 1.8.9 2.2.7.1-.5.3-.9.5-1.1-1.8-.2-3.7-.9-3.7-4a3 3 0 0 1 .8-2.1c-.1-.2-.4-1 .1-2.1 0 0 .7-.2 2.2.8a7.5 7.5 0 0 1 4 0c1.5-1 2.2-.8 2.2-.8.5 1.1.2 1.9.1 2.1.5.6.8 1.3.8 2.1 0 3.1-1.9 3.8-3.7 4 .3.3.5.8.5 1.5v2.2c0 .2.1.5.5.4A9 9 0 0 0 12 3Z",
  arrow: "M5 12h13M13 6l6 6-6 6",
  star: "M12 3l2.8 5.7 6.2.9-4.5 4.4 1.1 6.2-5.6-3-5.6 3 1.1-6.2L3 9.6l6.2-.9L12 3Z",
  layers: "M12 4 4 8l8 4 8-4-8-4Zm-8 8 8 4 8-4M4 16l8 4 8-4",
  api: "M5 5h14v14H5zM8 9h8M8 13h5M8 17h8",
  image: "M4 5h16v14H4zM8 10a2 2 0 1 0 0 .1M5 17l4-4 3 3 2-2 5 5",
  eye: "M2.5 12s3.5-6 9.5-6 9.5 6 9.5 6-3.5 6-9.5 6-9.5-6-9.5-6Zm9.5 3a3 3 0 1 0 0-6 3 3 0 0 0 0 6Z",
};

function iconPath(key: string): string {
  return ICONS[key] || ICONS.star || "";
}

function techIcon(tech: string): string {
  const t = tech.toLowerCase();
  if (t.includes("react")) return "react";
  if (t.includes("node")) return "node";
  if (t.includes("express")) return "express";
  if (t.includes("mongo")) return "mongo";
  if (t.includes("websocket")) return "websocket";
  if (t.includes("docker")) return "docker";
  if (t.includes("github")) return "github";
  if (t.includes("flask")) return "server";
  if (t.includes("fastapi")) return "api";
  if (t.includes("python")) return "code";
  if (t.includes("power bi")) return "layers";
  if (t.includes("pandas")) return "database";
  if (t.includes("mysql") || t.includes("postgres") || t.includes("database")) return "database";
  if (t.includes("sql")) return "database";
  if (t.includes("cloud")) return "cloud";
  if (t.includes("jwt") || t.includes("auth")) return "lock";
  if (t.includes("api")) return "api";
  if (t.includes("prisma")) return "database";
  return "layers";
}

function featureIcon(index: number): string {
  if (!props.project) return "star";
  if (props.project.visual === "compiler") {
    return ["code", "play", "terminal", "copy", "input"][index] || "code";
  }
  if (props.project.visual === "health") {
    return ["lock", "api", "cloud", "database", "eye"][index] || "layers";
  }
  if (props.project.visual === "finance") {
    return ["database", "layers", "api", "eye"][index] || "database";
  }
  return ["shield", "scan", "code", "database", "eye"][index] || "shield";
}

function highlightIcon(title: string, index: number): string {
  const t = title.toLowerCase();
  if (t.includes("editor") || t.includes("code")) return "code";
  if (t.includes("execution") || t.includes("run")) return "play";
  if (t.includes("security") || t.includes("detect")) return "shield";
  if (t.includes("auth") || t.includes("secure")) return "lock";
  if (t.includes("database") || t.includes("data")) return "database";
  if (t.includes("api")) return "api";
  if (t.includes("cloud")) return "cloud";
  return ["layers", "server", "scan"][index % 3] || "layers";
}

/* =========================================================
   MODAL / SCROLL CONTROL
========================================================= */

const closeOnEscape = (event: KeyboardEvent) => {
  if (event.key === "Escape" && props.open) emit("close");
};

watch(
  () => props.open,
  (open) => {
    if (open) {
      lenis.value?.stop();
    } else {
      lenis.value?.start();
    }

    if (typeof document !== "undefined") {
      document.body.style.overflow = open ? "hidden" : "";
    }
  },
  { immediate: true },
);

if (typeof window !== "undefined") {
  window.addEventListener("keydown", closeOnEscape);
}

onBeforeUnmount(() => {
  lenis.value?.start();
  if (typeof document !== "undefined") document.body.style.overflow = "";

  if (typeof window !== "undefined") {
    window.removeEventListener("keydown", closeOnEscape);
  }
});
</script>

<template>
  <Teleport to="body">
    <Transition name="project-details">
      <div
        v-if="open && project"
        class="project-details-overlay"
        @click.self="emit('close')"
      >
        <section
          class="project-details"
          :class="`project-details-${project.accent}`"
          role="dialog"
          aria-modal="true"
          :aria-label="`${project.title} project details`"
        >
          <button
            class="project-details-close"
            type="button"
            aria-label="Close project details"
            @click="emit('close')"
          >
            <span>×</span>
          </button>

          <div
            class="project-details-scroll"
            data-lenis-prevent-wheel
            data-lenis-prevent-touch
          >
            <div class="project-details-inner">

              <!-- =================================================
                   HEADER
              ================================================== -->

              <header class="details-header">
                <div class="details-header-copy">
                  <div class="details-eyebrow">
                    {{ project.type }} PROJECT
                  </div>

                  <h2>{{ project.title }}<span>.</span></h2>

                  <p class="details-subtitle">
                    {{ project.subtitle }}
                  </p>

                  <p class="details-lead">
                    {{ project.description }}
                  </p>

                  <div class="details-actions">
                    <a
                      class="details-action details-action-primary"
                      :href="project.url"
                      target="_blank"
                      rel="noopener noreferrer"
                    >
                      <span>Live Demo</span>
                      <b>↗</b>
                    </a>

                    <a
                      v-if="project.github"
                      class="details-action details-action-secondary"
                      :href="project.github"
                      target="_blank"
                      rel="noopener noreferrer"
                    >
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path :d="iconPath('github')" />
                      </svg>
                      <span>GitHub</span>
                      <b>↗</b>
                    </a>
                  </div>
                </div>

                <div class="details-hero-art" aria-hidden="true">
                  <div class="hero-art-window">
                    <div class="hero-art-bar">
                      <i></i><i></i><i></i>
                      <span>{{ project.title }}</span>
                    </div>

                    <div v-if="project.visual === 'compiler'" class="hero-code">
                      <span>function <b>compile</b>(code) {</span>
                      <span>&nbsp;&nbsp;return execute(code);</span>
                      <span>}</span>
                      <em>✓ Build successful</em>
                    </div>

                    <div v-else-if="project.visual === 'health'" class="hero-health">
                      <div class="health-nav">
                        <b>♥</b><i></i><i></i><i></i>
                      </div>
                      <div>
                        <strong>Healthcare Dashboard</strong>
                        <div class="health-cards">
                          <span>Patients <b>1,248</b></span>
                          <span>Appointments <b>328</b></span>
                          <span>Doctors <b>82</b></span>
                        </div>
                      </div>
                    </div>

                    <div v-else-if="project.visual === 'finance'" class="hero-security hero-finance">
                      <div class="security-label">NAV ANALYSIS</div>
                      <strong>CAGR · Sharpe · Drawdown</strong>
                      <div class="finance-hero-meta">16 schemes · 42K+ NAV rows · 2013–2026</div>
                      <em>Educational use only</em>
                    </div>

                    <div v-else class="hero-security">
                      <div class="security-label">THREAT SCAN</div>
                      <strong>URL ANALYSIS</strong>
                      <div class="security-url">
                        https://example-site.com
                      </div>
                      <em>✓ Website is safe</em>
                    </div>
                  </div>
                </div>
              </header>

              <!-- =================================================
                   01 TECH STACK
              ================================================== -->

              <section class="details-section details-stack">
                <div class="details-section-heading">
                  <span>01</span>
                  <h3>Tech Stack</h3>
                </div>

                <div class="details-stack-list">
                  <div
                    v-for="tech in project.stack"
                    :key="tech"
                    class="details-stack-item"
                  >
                    <span class="stack-icon">
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path :d="iconPath(techIcon(tech))" />
                      </svg>
                    </span>
                    <span>{{ tech }}</span>
                  </div>
                </div>
              </section>

              <!-- =================================================
                   02 OVERVIEW
              ================================================== -->

              <section class="details-section">
                <div class="details-section-heading">
                  <span>02</span>
                  <h3>Overview</h3>
                </div>

                <div class="overview-card">
                  <div class="overview-icon">
                    <svg viewBox="0 0 24 24" aria-hidden="true">
                      <path :d="iconPath('layers')" />
                    </svg>
                  </div>
                  <p class="details-copy">{{ project.overview }}</p>
                </div>
              </section>

              <!-- =================================================
                   03 KEY FEATURES
              ================================================== -->

              <section class="details-section">
                <div class="details-section-heading">
                  <span>03</span>
                  <h3>Key Features</h3>
                </div>

                <div class="details-feature-grid">
                  <article
                    v-for="(feature, index) in project.features"
                    :key="feature"
                    class="details-feature"
                  >
                    <div class="details-feature-icon">
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path :d="iconPath(featureIcon(index))" />
                      </svg>
                    </div>

                    <div class="details-feature-content">
                      <span class="details-feature-number">
                        0{{ index + 1 }}
                      </span>
                      <p>{{ feature }}</p>
                    </div>
                  </article>
                </div>
              </section>

              <!-- =================================================
                   04 SCREENSHOTS
              ================================================== -->

              <section class="details-section">
                <div class="details-section-heading">
                  <span>04</span>
                  <h3>Screenshots / Demo</h3>
                </div>

                <div class="details-media-grid">
                  <article
                    v-for="(shot, index) in project.screenshots"
                    :key="`${shot.label}-${index}`"
                    class="details-media-card"
                  >
                    <div class="details-media-visual">
                      <img
                        v-if="resolveScreenshotSrc(shot)"
                        :src="resolveScreenshotSrc(shot)"
                        :alt="`${project.title} — ${shot.label}`"
                        loading="lazy"
                        decoding="async"
                      />

                      <div
                        v-else-if="project.visual === 'security'"
                        class="mock-security"
                      >
                        <div class="mock-security-top">
                          <b>PhishGuard</b>
                          <span>Detection</span>
                        </div>

                        <div class="mock-security-body">
                          <small>AI-POWERED PHISHING DETECTION</small>
                          <strong>Enter website URL</strong>
                          <div class="mock-input">
                            https://website.com
                          </div>
                          <button>Scan URL</button>
                        </div>
                      </div>

                      <div
                        v-else
                        class="details-media-placeholder"
                      >
                        <span>{{ project.title }}</span>
                        <b>{{ shot.label }}</b>
                      </div>

                      <div class="media-overlay">
                        <span>
                          <svg viewBox="0 0 24 24" aria-hidden="true">
                            <path :d="iconPath('eye')" />
                          </svg>
                          Preview
                        </span>
                      </div>
                    </div>

                    <div class="details-media-caption">
                      <div class="media-caption-title">
                        <span class="caption-icon">
                          <svg viewBox="0 0 24 24" aria-hidden="true">
                            <path :d="iconPath('image')" />
                          </svg>
                        </span>
                        <strong>{{ shot.label }}</strong>
                      </div>
                      <p>{{ shot.caption }}</p>
                    </div>
                  </article>
                </div>
              </section>

              <!-- =================================================
                   05 TECHNICAL HIGHLIGHTS
              ================================================== -->

              <section class="details-section">
                <div class="details-section-heading">
                  <span>05</span>
                  <h3>Technical Highlights</h3>
                </div>

                <div class="details-highlight-list">
                  <article
                    v-for="(item, index) in project.highlights"
                    :key="item.title"
                    class="details-highlight"
                  >
                    <div class="details-highlight-icon">
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path
                          :d="iconPath(highlightIcon(item.title, index))"
                        />
                      </svg>
                    </div>

                    <div>
                      <div class="highlight-topline">
                        <span>0{{ index + 1 }}</span>
                        <h4>{{ item.title }}</h4>
                      </div>
                      <p>{{ item.text }}</p>
                    </div>
                  </article>
                </div>
              </section>

              <!-- =================================================
                   06 ARCHITECTURE
              ================================================== -->

              <section class="details-section">
                <div class="details-section-heading">
                  <span>06</span>
                  <h3>Architecture</h3>
                </div>

                <div class="architecture">
                  <template
                    v-for="(node, index) in project.architecture.nodes"
                    :key="node.title"
                  >
                    <div
                      class="architecture-node"
                      :class="{ 'architecture-node-accent': index === 1 }"
                    >
                      <span class="architecture-icon">
                        <svg viewBox="0 0 24 24" aria-hidden="true">
                          <path :d="iconPath(node.icon)" />
                        </svg>
                      </span>
                      <strong>{{ node.title }}</strong>
                      <small>{{ node.subtitle }}</small>
                    </div>

                    <div
                      v-if="index < project.architecture.nodes.length - 1"
                      class="architecture-arrow"
                    >
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path :d="iconPath('arrow')" />
                      </svg>
                    </div>
                  </template>
                </div>

                <div
                  v-if="project.architecture.secondary.length"
                  class="architecture-secondary"
                >
                  <span
                    v-for="item in project.architecture.secondary"
                    :key="item.label"
                  >
                    <i>
                      <svg viewBox="0 0 24 24">
                        <path :d="iconPath(item.icon)" />
                      </svg>
                    </i>
                    {{ item.label }}
                  </span>
                </div>
              </section>

              <!-- =================================================
                   07 WHAT'S NEXT
              ================================================== -->

              <section class="details-section details-next">
                <div class="details-section-heading">
                  <span>07</span>
                  <h3>What's Next</h3>
                </div>

                <div
                  v-if="project.whatsNext?.length"
                  class="whats-next-grid"
                >
                  <article
                    v-for="(item, index) in project.whatsNext"
                    :key="item.title"
                    class="whats-next-card"
                  >
                    <span class="whats-next-number">
                      {{ String(index + 1).padStart(2, "0") }}
                    </span>
                    <h4>{{ item.title }}</h4>
                    <p>{{ item.description }}</p>
                  </article>
                </div>

                <ul v-else-if="project.next.length">
                  <li
                    v-for="item in project.next"
                    :key="item"
                  >
                    <span class="next-icon">
                      <svg viewBox="0 0 24 24" aria-hidden="true">
                        <path :d="iconPath('arrow')" />
                      </svg>
                    </span>
                    <span>{{ item }}</span>
                  </li>
                </ul>

                <div class="details-final-cta">
                  <div class="cta-copy">
                    <small>READY TO EXPLORE?</small>
                    <h3>Try {{ project.title }}.</h3>
                  </div>

                  <a
                    class="details-action details-action-primary"
                    :href="project.url"
                    target="_blank"
                    rel="noopener noreferrer"
                  >
                    <span>Open Live Demo</span>
                    <b>↗</b>
                  </a>
                </div>
              </section>

            </div>
          </div>
        </section>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped lang="scss">
.project-details-overlay {
  position: fixed;
  inset: 0;
  z-index: 99999;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 18px;
  background: rgba(29, 24, 18, 0.58);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

.project-details {
  --cream: #f8f1e5;
  --cream-2: #fffaf1;
  --ink: #181818;
  --muted: #716b62;
  --line: rgba(88, 70, 45, 0.14);
  --accent: #f27b16;

  position: relative;
  width: min(1120px, 100%);
  height: min(94vh, 920px);
  overflow: hidden;
  color: var(--ink);
  background:
    radial-gradient(
      circle at 88% 4%,
      rgba(255, 153, 55, 0.15),
      transparent 24%
    ),
    linear-gradient(135deg, var(--cream-2), var(--cream));
  border: 1px solid rgba(255, 255, 255, 0.8);
  border-radius: 26px;
  box-shadow: 0 35px 90px rgba(25, 20, 14, 0.28);
}

.project-details-blue {
  --accent: #2f76d2;
}

.project-details-red {
  --accent: #d6534c;
}

.project-details-scroll {
  height: 100%;
  overflow-y: auto;
  overscroll-behavior: contain;
  scroll-behavior: smooth;
}

.project-details-scroll::-webkit-scrollbar {
  width: 9px;
}

.project-details-scroll::-webkit-scrollbar-thumb {
  background: rgba(100, 80, 55, 0.28);
  border-radius: 99px;
}

.project-details-inner {
  width: min(980px, calc(100% - 64px));
  margin: 0 auto;
  padding: 68px 0 55px;
}

.project-details-close {
  position: absolute;
  top: 17px;
  right: 19px;
  z-index: 10;
  width: 44px;
  height: 44px;
  border: 1px solid rgba(80, 60, 35, 0.16);
  border-radius: 50%;
  background: rgba(255, 250, 241, 0.88);
  color: var(--ink);
  cursor: pointer;
  font-size: 29px;
  line-height: 1;
  transition:
    transform 0.3s ease,
    background 0.3s ease,
    box-shadow 0.3s ease;
}

.project-details-close:hover {
  transform: rotate(90deg) scale(1.05);
  background: #fff;
  box-shadow: 0 8px 22px rgba(60, 45, 25, 0.12);
}

/* =========================================================
   HEADER
========================================================= */

.details-header {
  display: grid;
  grid-template-columns: 1.05fr 0.95fr;
  gap: 45px;
  align-items: center;
  min-height: 420px;
}

.details-eyebrow {
  margin-bottom: 13px;
  color: var(--accent);
  font-size: 12px;
  font-weight: 800;
  letter-spacing: 0.2em;
}

.details-header h2 {
  margin: 0;
  font-size: clamp(54px, 7vw, 92px);
  line-height: 0.88;
  letter-spacing: -0.065em;
}

.details-header h2 span {
  color: var(--accent);
}

.details-subtitle {
  margin: 15px 0 10px;
  color: var(--accent);
  font-size: clamp(20px, 2.2vw, 28px);
  font-weight: 700;
}

.details-lead {
  max-width: 600px;
  color: var(--muted);
  font-size: 15px;
  line-height: 1.75;
}

.details-actions {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-top: 23px;
}

.details-action {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  min-width: 145px;
  min-height: 44px;
  padding: 12px 17px;
  border-radius: 11px;
  font-size: 13px;
  font-weight: 800;
  text-decoration: none;
  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.details-action svg {
  width: 16px;
  height: 16px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.details-action:hover {
  transform: translateY(-2px);
}

.details-action-primary {
  background: var(--accent);
  color: white;
  box-shadow:
    0 10px 24px color-mix(
      in srgb,
      var(--accent) 25%,
      transparent
    );
}

.details-action-secondary {
  border: 1px solid rgba(65, 50, 30, 0.22);
  background: rgba(255, 255, 255, 0.48);
  color: var(--ink);
}

.details-hero-art {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 330px;
}

.hero-art-window {
  width: min(430px, 100%);
  min-height: 270px;
  padding: 10px;
  transform: rotate(2deg);
  border: 1px solid rgba(60, 45, 25, 0.2);
  border-radius: 18px;
  background: #202020;
  box-shadow: 18px 22px 50px rgba(50, 38, 24, 0.18);
}

.hero-art-bar {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 8px 10px;
  color: #bdbdbd;
  font-size: 9px;
}

.hero-art-bar i {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #777;
}

.hero-art-bar span {
  margin-left: 7px;
}

.hero-code {
  display: grid;
  gap: 8px;
  padding: 35px 25px;
  color: #b9c7d4;
  font: 12px/1.7 monospace;
}

.hero-code b {
  color: #f9a657;
}

.hero-code em,
.hero-security em {
  display: inline-flex;
  width: fit-content;
  margin-top: 15px;
  padding: 6px 10px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.08);
  color: #7be495;
  font: 10px/1.2 sans-serif;
  font-style: normal;
}

.hero-health {
  min-height: 225px;
  padding: 22px;
  color: #fff;
  background:
    radial-gradient(
      circle at 85% 10%,
      rgba(64, 206, 226, 0.2),
      transparent 30%
    ),
    #101b20;
  border-radius: 10px;
}

.health-nav {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-bottom: 28px;
}

.health-nav b {
  color: #4bd8ef;
  margin-right: auto;
}

.health-nav i {
  width: 22px;
  height: 4px;
  border-radius: 99px;
  background: rgba(255, 255, 255, 0.15);
}

.hero-health strong {
  font-size: 18px;
}

.health-cards {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
  margin-top: 22px;
}

.health-cards span {
  padding: 10px;
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 8px;
  color: #8f9aa2;
  font-size: 8px;
}

.health-cards b {
  display: block;
  margin-top: 6px;
  color: #fff;
  font-size: 13px;
}

.hero-security {
  min-height: 225px;
  padding: 28px;
  color: #dce3ea;
  background:
    radial-gradient(
      circle at 75% 20%,
      rgba(214, 83, 76, 0.22),
      transparent 30%
    ),
    #171a1d;
  border-radius: 10px;
}

.hero-finance {
  background:
    radial-gradient(
      circle at 75% 20%,
      rgba(242, 123, 22, 0.24),
      transparent 34%
    ),
    #171a1d;
}

.hero-finance .security-label {
  color: #f27b16;
}

.finance-hero-meta {
  margin-top: 20px;
  padding: 11px;
  border-radius: 7px;
  background: rgba(255, 255, 255, 0.08);
  color: #cfd7e0;
  font: 10px monospace;
}

.hero-finance em {
  background: color-mix(in srgb, #f27b16 14%, transparent);
  color: #ffad5c;
}

.security-label {
  color: #d6534c;
  font-size: 9px;
  font-weight: 800;
  letter-spacing: 0.16em;
}

.hero-security strong {
  display: block;
  margin-top: 20px;
  font-size: 22px;
}

.security-url {
  margin-top: 20px;
  padding: 11px;
  border-radius: 7px;
  background: rgba(255, 255, 255, 0.08);
  color: #cfd7e0;
  font: 10px monospace;
}

/* =========================================================
   SECTION BASICS
========================================================= */

.details-section {
  padding: 52px 0;
  border-top: 1px solid var(--line);
}

.details-section-heading {
  display: flex;
  align-items: baseline;
  gap: 14px;
  margin-bottom: 22px;
}

.details-section-heading > span {
  color: var(--accent);
  font: 800 11px/1 monospace;
  letter-spacing: 0.08em;
}

.details-section-heading h3 {
  margin: 0;
  font-size: clamp(25px, 3vw, 34px);
  letter-spacing: -0.035em;
}

.details-copy {
  max-width: 820px;
  margin: 0;
  color: var(--muted);
  font-size: 16px;
  line-height: 1.85;
}

/* =========================================================
   TECH STACK
========================================================= */

.details-stack-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.details-stack-item {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 9px 13px 9px 9px;
  border: 1px solid var(--line);
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.42);
  font-size: 12px;
  font-weight: 800;
  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease;
}

.details-stack-item:hover {
  transform: translateY(-3px);
  border-color: color-mix(in srgb, var(--accent) 35%, var(--line));
  box-shadow: 0 10px 22px rgba(70, 50, 25, 0.07);
}

.stack-icon {
  display: grid;
  place-items: center;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  color: var(--accent);
  background: color-mix(
    in srgb,
    var(--accent) 10%,
    white
  );
}

.stack-icon svg {
  width: 15px;
  height: 15px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

/* =========================================================
   OVERVIEW
========================================================= */

.overview-card {
  display: grid;
  grid-template-columns: 52px 1fr;
  gap: 18px;
  align-items: start;
  max-width: 900px;
  padding: 22px;
  border: 1px solid var(--line);
  border-radius: 18px;
  background: rgba(255, 255, 255, 0.34);
}

.overview-icon {
  display: grid;
  place-items: center;
  width: 52px;
  height: 52px;
  border-radius: 15px;
  color: #fff;
  background: var(--accent);
  box-shadow:
    0 10px 24px color-mix(
      in srgb,
      var(--accent) 18%,
      transparent
    );
}

.overview-icon svg {
  width: 23px;
  height: 23px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

/* =========================================================
   KEY FEATURES
========================================================= */

.details-feature-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 13px;
}

.details-feature {
  display: grid;
  grid-template-columns: 48px 1fr;
  gap: 15px;
  align-items: start;
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.38);
  transition:
    transform 0.28s ease,
    box-shadow 0.28s ease,
    border-color 0.28s ease;
}

.details-feature:hover {
  transform: translateY(-5px);
  border-color: color-mix(in srgb, var(--accent) 30%, var(--line));
  box-shadow: 0 16px 32px rgba(70, 50, 25, 0.08);
}

.details-feature-icon {
  display: grid;
  place-items: center;
  width: 45px;
  height: 45px;
  border-radius: 13px;
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 9%, white);
  border: 1px solid color-mix(in srgb, var(--accent) 18%, transparent);
  transition:
    transform 0.28s ease,
    background 0.28s ease;
}

.details-feature:hover .details-feature-icon {
  transform: rotate(-5deg) scale(1.08);
  background: color-mix(in srgb, var(--accent) 15%, white);
}

.details-feature-icon svg {
  width: 21px;
  height: 21px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.65;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.details-feature-content {
  display: grid;
  gap: 8px;
}

.details-feature-number {
  color: var(--accent);
  font: 800 10px/1 monospace;
  letter-spacing: 0.08em;
}

.details-feature p {
  margin: 0;
  color: var(--muted);
  line-height: 1.65;
  font-size: 13px;
}

/* =========================================================
   SCREENSHOTS
========================================================= */

.details-media-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
}

.details-media-card {
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 17px;
  background: rgba(255, 255, 255, 0.42);
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease,
    border-color 0.3s ease;
}

.details-media-card:hover {
  transform: translateY(-5px);
  border-color: color-mix(in srgb, var(--accent) 28%, var(--line));
  box-shadow: 0 18px 34px rgba(60, 45, 25, 0.09);
}

.details-media-visual {
  position: relative;
  aspect-ratio: 16 / 9;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #1c1c1c;
}

.details-media-visual img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
  transition:
    transform 0.5s ease,
    filter 0.5s ease;
}

.details-media-card:hover img {
  transform: scale(1.04);
  filter: brightness(1.03);
}

.media-overlay {
  position: absolute;
  inset: auto 12px 12px auto;
  opacity: 0;
  transform: translateY(6px);
  transition:
    opacity 0.25s ease,
    transform 0.25s ease;
}

.details-media-card:hover .media-overlay {
  opacity: 1;
  transform: translateY(0);
}

.media-overlay span {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 7px 9px;
  border: 1px solid rgba(255, 255, 255, 0.22);
  border-radius: 999px;
  background: rgba(20, 20, 20, 0.72);
  color: #fff;
  backdrop-filter: blur(8px);
  font-size: 10px;
  font-weight: 800;
}

.media-overlay svg {
  width: 13px;
  height: 13px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.details-media-caption {
  padding: 14px 16px 17px;
}

.media-caption-title {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-bottom: 6px;
}

.media-caption-title strong {
  font-size: 14px;
}

.caption-icon {
  display: grid;
  place-items: center;
  width: 25px;
  height: 25px;
  border-radius: 8px;
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 9%, white);
}

.caption-icon svg {
  width: 13px;
  height: 13px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.6;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.details-media-caption p {
  margin: 0;
  color: var(--muted);
  line-height: 1.65;
  font-size: 11px;
}

.details-media-placeholder {
  display: flex;
  height: 100%;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 8px;
  color: white;
  background:
    radial-gradient(
      circle at 50% 40%,
      color-mix(in srgb, var(--accent) 30%, transparent),
      transparent 42%
    ),
    #1a1a1a;
}

.details-media-placeholder span {
  color: #aaa;
  font-size: 10px;
  letter-spacing: 0.16em;
}

.details-media-placeholder b {
  font-size: 24px;
}

/* =========================================================
   PHISHGUARD FALLBACK
========================================================= */

.mock-security {
  height: 100%;
  width: 100%;
  padding: 15px;
  color: #222;
  background: #fff;
}

.mock-security-top {
  display: flex;
  justify-content: space-between;
  font-size: 9px;
}

.mock-security-top b {
  font-size: 12px;
}

.mock-security-top span {
  color: #777;
}

.mock-security-body {
  width: 75%;
  margin: 25px auto 0;
  text-align: center;
}

.mock-security-body small {
  color: #d6534c;
  font-size: 7px;
  font-weight: 800;
  letter-spacing: 0.12em;
}

.mock-security-body strong {
  display: block;
  margin: 8px 0;
  font-size: 18px;
}

.mock-input {
  padding: 9px;
  border: 1px solid #ddd;
  border-radius: 6px;
  color: #777;
  text-align: left;
  font: 8px monospace;
}

.mock-security-body button {
  margin-top: 9px;
  padding: 7px 13px;
  border: 0;
  border-radius: 6px;
  color: white;
  background: #d6534c;
  font-size: 8px;
  font-weight: 800;
}

/* =========================================================
   TECHNICAL HIGHLIGHTS
========================================================= */

.details-highlight-list {
  display: grid;
  gap: 12px;
}

.details-highlight {
  display: grid;
  grid-template-columns: 50px 1fr;
  gap: 16px;
  padding: 19px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.38);
  transition:
    transform 0.28s ease,
    box-shadow 0.28s ease,
    border-color 0.28s ease;
}

.details-highlight:hover {
  transform: translateX(4px);
  border-color: color-mix(in srgb, var(--accent) 28%, var(--line));
  box-shadow: 0 14px 30px rgba(70, 50, 25, 0.07);
}

.details-highlight-icon {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  border-radius: 12px;
  color: white;
  background: var(--accent);
  box-shadow:
    0 8px 20px color-mix(
      in srgb,
      var(--accent) 18%,
      transparent
    );
}

.details-highlight-icon svg {
  width: 20px;
  height: 20px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.highlight-topline {
  display: flex;
  align-items: baseline;
  gap: 10px;
  margin-bottom: 6px;
}

.highlight-topline > span {
  color: var(--accent);
  font: 800 9px/1 monospace;
}

.details-highlight h4 {
  margin: 0;
  font-size: 15px;
}

.details-highlight p {
  margin: 0;
  color: var(--muted);
  line-height: 1.65;
  font-size: 13px;
}

/* =========================================================
   ARCHITECTURE
========================================================= */

.architecture {
  display: grid;
  grid-template-columns: 1fr auto 1fr auto 1fr;
  gap: 10px;
  align-items: center;
}

.architecture-node {
  min-height: 125px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 16px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.5);
  text-align: center;
  transition:
    transform 0.28s ease,
    box-shadow 0.28s ease;
}

.architecture-node:hover {
  transform: translateY(-4px);
  box-shadow: 0 15px 30px rgba(70, 50, 25, 0.08);
}

.architecture-node strong {
  margin-top: 11px;
  font-size: 14px;
}

.architecture-node small {
  margin-top: 5px;
  color: var(--muted);
  font-size: 10px;
}

.architecture-icon {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  border-radius: 13px;
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 9%, white);
  border: 1px solid color-mix(in srgb, var(--accent) 16%, transparent);
}

.architecture-icon svg {
  width: 20px;
  height: 20px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.6;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.architecture-node-accent {
  border-color: color-mix(in srgb, var(--accent) 40%, transparent);
  box-shadow:
    inset 0 0 0 1px color-mix(
      in srgb,
      var(--accent) 8%,
      transparent
    );
}

.architecture-arrow {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  color: var(--accent);
}

.architecture-arrow svg {
  width: 22px;
  height: 22px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.architecture-secondary {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 14px;
}

.architecture-secondary span {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 7px 10px;
  border-radius: 999px;
  background: rgba(80, 60, 35, 0.07);
  color: var(--muted);
  font-size: 10px;
  font-weight: 700;
}

.architecture-secondary i {
  display: grid;
  place-items: center;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 9%, white);
  font-style: normal;
}

.architecture-secondary svg {
  width: 11px;
  height: 11px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

/* =========================================================
   WHAT'S NEXT
========================================================= */

.details-next {
  padding-bottom: 10px;
}

.details-next ul {
  display: grid;
  gap: 11px;
  padding: 0;
  margin: 0 0 30px;
  list-style: none;
}

.whats-next-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 13px;
  margin-bottom: 30px;
}

.whats-next-card {
  min-width: 0;
  padding: 18px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.38);
  transition:
    transform 0.28s ease,
    box-shadow 0.28s ease,
    border-color 0.28s ease;
}

.whats-next-card:hover {
  transform: translateY(-4px);
  border-color: color-mix(in srgb, var(--accent) 30%, var(--line));
  box-shadow: 0 16px 32px rgba(70, 50, 25, 0.08);
}

.whats-next-number {
  color: var(--accent);
  font: 800 10px/1 monospace;
  letter-spacing: 0.08em;
}

.whats-next-card h4 {
  margin: 11px 0 6px;
  font-size: 14px;
  line-height: 1.4;
}

.whats-next-card p {
  margin: 0;
  color: var(--muted);
  font-size: 12px;
  line-height: 1.6;
}

.details-next li {
  display: flex;
  align-items: center;
  gap: 11px;
  color: var(--muted);
  font-size: 14px;
  line-height: 1.6;
}

.next-icon {
  display: grid;
  place-items: center;
  flex: 0 0 25px;
  width: 25px;
  height: 25px;
  border-radius: 8px;
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 9%, white);
}

.next-icon svg {
  width: 13px;
  height: 13px;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.details-final-cta {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding: 24px;
  border: 1px solid var(--line);
  border-radius: 18px;
  background: rgba(255, 255, 255, 0.46);
}

.details-final-cta small {
  color: var(--accent);
  font-size: 9px;
  font-weight: 800;
  letter-spacing: 0.14em;
}

.details-final-cta h3 {
  margin: 5px 0 0;
  font-size: 24px;
}

.project-details-enter-active,
.project-details-leave-active {
  transition: opacity 0.3s ease;
}

.project-details-enter-active .project-details,
.project-details-leave-active .project-details {
  transition:
    transform 0.4s cubic-bezier(0.22, 1, 0.36, 1),
    opacity 0.3s ease;
}

.project-details-enter-from,
.project-details-leave-to {
  opacity: 0;
}

.project-details-enter-from .project-details,
.project-details-leave-to .project-details {
  transform: translateY(25px) scale(0.985);
  opacity: 0;
}

/* =========================================================
   RESPONSIVE
========================================================= */

  @media (max-width: 800px) {
    .project-details-overlay {
      align-items: stretch;
      padding: 0;
      overflow: hidden;
      overscroll-behavior: contain;
    }

    .project-details {
      width: 100%;
      max-width: 100%;
      height: 100dvh;
      max-height: 100dvh;
      min-width: 0;
      border-radius: 0;
    }

    .project-details-scroll {
      min-width: 0;
      max-width: 100%;
      -webkit-overflow-scrolling: touch;
      overscroll-behavior: contain;
    }

    .project-details-inner {
      width: min(100% - 28px, 980px);
      min-width: 0;
      padding-bottom: 40px;
    }

    .project-details img,
    .project-details picture {
      max-width: 100%;
      min-width: 0;
    }

    .project-details button,
    .project-details a {
      max-width: 100%;
    }

    .project-details-close {
      top: 10px;
      right: 10px;
    }

  .project-details-overlay {
    padding: 0;
  }

  .project-details {
    width: 100%;
    height: 100%;
    border-radius: 0;
  }

  .project-details-inner {
    width: min(100% - 34px, 680px);
    padding-top: 65px;
  }

  .details-header {
    grid-template-columns: 1fr;
    min-height: auto;
    gap: 28px;
  }

  .details-hero-art {
    min-height: 230px;
  }

  .hero-art-window {
    max-width: 480px;
  }

  .details-feature-grid,
  .details-media-grid {
    grid-template-columns: 1fr;
  }

  .architecture {
    grid-template-columns: 1fr;
  }

  .architecture-arrow {
    transform: rotate(90deg);
    justify-self: center;
  }

  .details-final-cta {
    align-items: flex-start;
    flex-direction: column;
  }
}

@media (max-width: 560px) {
  .overview-card {
    grid-template-columns: 1fr;
  }

  .details-highlight {
    grid-template-columns: 43px 1fr;
  }

  .details-feature {
    grid-template-columns: 43px 1fr;
  }

  .health-cards {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .project-details-inner {
    width: calc(100% - 24px);
  }

  .details-header h2 {
    font-size: 52px;
  }

  .details-section {
    padding: 38px 0;
  }

  .details-action {
    min-width: 130px;
  }

  .details-actions {
    width: 100%;
  }

  .details-actions .details-action {
    flex: 1;
  }

  .details-final-cta .details-action {
    width: 100%;
  }
}
</style>
