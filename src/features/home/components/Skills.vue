<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import * as SI from "simple-icons";

gsap.registerPlugin(ScrollTrigger);

type SkillGroup = {
  name: string;
  skills: string[];
};

type SkillLogo = {
  name: string;
  icon?: SI.SimpleIcon;
  powerBi?: boolean;
};

type SphereNode = {
  index: number;
  name: string;
  icon?: SI.SimpleIcon;
  powerBi?: boolean;
  x: number;
  y: number;
  z: number;
};

type ProjectedPoint = {
  node: SphereNode;
  x: number;
  y: number;
  depth: number;
};

type LabelBounds = {
  left: number;
  right: number;
  top: number;
  bottom: number;
};

const groups: SkillGroup[] = [
  {
    name: "Programming Languages",
    skills: ["Java", "Python", "JavaScript", "TypeScript", "C++", "SQL"],
  },
  {
    name: "Backend and APIs",
    skills: ["FastAPI", "Flask", "Node.js", "Express.js", "REST APIs"],
  },
  {
    name: "Databases",
    skills: ["SQLite", "PostgreSQL", "MySQL", "MongoDB"],
  },
  {
    name: "Software Engineering",
    skills: ["Data Structures and Algorithms", "OOP", "System Design", "Agile", "Unit Testing"],
  },
  {
    name: "Frontend",
    skills: ["React", "Vite", "Tailwind CSS", "HTML5", "CSS3"],
  },
  {
    name: "Data Analysis",
    skills: ["Pandas", "NumPy", "scikit-learn", "Power BI", "Matplotlib", "Jupyter"],
  },
  {
    name: "Cloud and DevOps",
    skills: ["Docker", "Git", "GitHub", "GitHub Actions", "Vercel", "Render", "AWS", "Linux", "CI/CD", "pytest"],
  },
  {
    name: "AI and Tools",
    skills: ["Google Gemini", "Firebase Auth", "Postman", "Jira"],
  },
];

const sphereLogos: SkillLogo[] = [
  { name: "Java", icon: SI.siOpenjdk },
  { name: "Python", icon: SI.siPython },
  { name: "JavaScript", icon: SI.siJavascript },
  { name: "TypeScript", icon: SI.siTypescript },
  { name: "React", icon: SI.siReact },
  { name: "Vite", icon: SI.siVite },
  { name: "Tailwind CSS", icon: SI.siTailwindcss },
  { name: "FastAPI", icon: SI.siFastapi },
  { name: "Flask", icon: SI.siFlask },
  { name: "Node.js", icon: SI.siNodedotjs },
  { name: "Pandas", icon: SI.siPandas },
  { name: "NumPy", icon: SI.siNumpy },
  { name: "scikit-learn", icon: SI.siScikitlearn },
  { name: "Jupyter", icon: SI.siJupyter },
  { name: "Power BI", powerBi: true },
  { name: "SQLite", icon: SI.siSqlite },
  { name: "PostgreSQL", icon: SI.siPostgresql },
  { name: "MongoDB", icon: SI.siMongodb },
  { name: "Docker", icon: SI.siDocker },
  { name: "Git", icon: SI.siGit },
  { name: "GitHub Actions", icon: SI.siGithubactions },
  { name: "Linux", icon: SI.siLinux },
  { name: "Google Gemini", icon: SI.siGooglegemini },
  { name: "Firebase Auth", icon: SI.siFirebase },
];

const sectionRef = ref<HTMLElement | null>(null);
const canvasRef = ref<HTMLCanvasElement | null>(null);

let ctx: gsap.Context | null = null;
let resizeObserver: ResizeObserver | null = null;
let visibilityObserver: IntersectionObserver | null = null;
let frameId = 0;
let visible = false;
let hovered = false;
let dragging = false;
let reducedMotion = false;
let rotation = 0;
let rotationSpeed = 0.0045;
let tilt = 0.3;
let inertiaRotationSpeed = 0;
let inertiaTiltSpeed = 0;
let inertiaActive = false;
let lastInteractionTime = 0;
let lastPointerX = 0;
let lastPointerY = 0;
let lastPointerTime = 0;
let activePointerId: number | null = null;
let pointerPosition: { x: number; y: number } | null = null;
let hoveredNodeIndex: number | null = null;
let previousFrameTime = 0;
let canvasContext: CanvasRenderingContext2D | null = null;
let canvasWidth = 0;
let canvasHeight = 0;
let pixelRatio = 1;
let refreshFrame = 0;
let nodes: SphereNode[] = [];
let mediaQuery: MediaQueryList | null = null;

const radiansPerFrameAt60Fps = 0.0045;
const hoverRadiansPerFrameAt60Fps = 0.0012;
const frameDuration = 1000 / 60;
const radiansPerPixel = 0.005;
const inertiaDecayPerFrame = 0.94;
const autoRotationDelay = 2000;

function createSphereNodes(): SphereNode[] {
  const count = sphereLogos.length;
  const goldenAngle = Math.PI * (3 - Math.sqrt(5));

  const result: SphereNode[] = sphereLogos.map((logo, index) => {
    const y = 1 - (index / Math.max(1, count - 1)) * 2;
    const ringRadius = Math.sqrt(Math.max(0, 1 - y * y));
    const theta = goldenAngle * index;

    return {
      index,
      name: logo.name,
      ...(logo.icon ? { icon: logo.icon } : {}),
      ...(logo.powerBi ? { powerBi: true } : {}),
      x: Math.cos(theta) * ringRadius,
      y,
      z: Math.sin(theta) * ringRadius,
    };
  });

  return result;
}

function rotateNode(node: SphereNode, angle: number) {
  const cosX = Math.cos(tilt);
  const sinX = Math.sin(tilt);
  const tiltedY = node.y * cosX - node.z * sinX;
  const tiltedZ = node.y * sinX + node.z * cosX;
  const cosY = Math.cos(angle);
  const sinY = Math.sin(angle);
  const x = node.x * cosY + tiltedZ * sinY;
  const z = -node.x * sinY + tiltedZ * cosY;

  return { x, y: tiltedY, depth: z };
}

function drawPowerBi(ctx2d: CanvasRenderingContext2D, size: number) {
  ctx2d.save();
  ctx2d.scale(size / 24, size / 24);
  ctx2d.translate(-12, -12);
  ctx2d.fillStyle = "#F2C811";
  ctx2d.fillRect(3, 12, 4, 8);
  ctx2d.fillRect(10, 7, 4, 13);
  ctx2d.fillRect(17, 3, 4, 17);
  ctx2d.restore();
}

function drawIcon(
  ctx2d: CanvasRenderingContext2D,
  node: SphereNode,
  size: number,
) {
  if (node.powerBi) {
    drawPowerBi(ctx2d, size);
  } else if (node.icon) {
    ctx2d.save();
    ctx2d.scale(size / 24, size / 24);
    ctx2d.translate(-12, -12);
    ctx2d.fillStyle = `#${node.icon.hex}`;
    ctx2d.fill(new Path2D(node.icon.path));
    ctx2d.restore();
  }
}

function drawSphere() {
  const ctx2d = canvasContext;
  if (!ctx2d || canvasWidth <= 0 || canvasHeight <= 0) return;

  ctx2d.clearRect(0, 0, canvasWidth, canvasHeight);

  const centerX = canvasWidth / 2;
  const centerY = canvasHeight / 2;
  const radius = Math.max(1, Math.min(canvasWidth, canvasHeight) * 0.37);

  const glow = ctx2d.createRadialGradient(
    centerX,
    centerY,
    radius * 0.08,
    centerX,
    centerY,
    radius * 1.18,
  );
  glow.addColorStop(0, "rgba(255, 138, 0, 0.11)");
  glow.addColorStop(0.65, "rgba(255, 138, 0, 0.045)");
  glow.addColorStop(1, "rgba(255, 138, 0, 0)");
  ctx2d.fillStyle = glow;
  ctx2d.fillRect(0, 0, canvasWidth, canvasHeight);

  ctx2d.save();
  ctx2d.beginPath();
  ctx2d.arc(centerX, centerY, radius, 0, Math.PI * 2);
  ctx2d.strokeStyle = "rgba(255, 132, 0, 0.22)";
  ctx2d.lineWidth = 1;
  ctx2d.stroke();

  const points: ProjectedPoint[] = nodes.map((node) => {
    const rotated = rotateNode(node, rotation);
    return {
      node,
      x: centerX + rotated.x * radius,
      y: centerY - rotated.y * radius,
      depth: (rotated.depth + 1) / 2,
    };
  });

  hoveredNodeIndex = null;
  if (hovered && !dragging && pointerPosition) {
    let nearestDistance = Number.POSITIVE_INFINITY;
    for (const point of points) {
      const depth = Math.max(0, Math.min(1, point.depth));
      const scale = 0.55 + 0.45 * depth;
      const bubbleRadius = 23 * scale;
      const distance = Math.hypot(
        pointerPosition.x - point.x,
        pointerPosition.y - point.y,
      );
      if (distance <= bubbleRadius && distance < nearestDistance) {
        nearestDistance = distance;
        hoveredNodeIndex = point.node.index;
      }
    }
  }

  const edges = new Set<string>();
  for (const point of points) {
    const nearest = points
      .filter((candidate) => candidate.node.index !== point.node.index)
      .map((candidate) => ({
        candidate,
        distance:
          (point.x - candidate.x) ** 2 +
          (point.y - candidate.y) ** 2,
      }))
      .sort((a, b) => a.distance - b.distance)
      .slice(0, 3);

    for (const { candidate } of nearest) {
      const first = Math.min(point.node.index, candidate.node.index);
      const second = Math.max(point.node.index, candidate.node.index);
      edges.add(`${first}:${second}`);
    }
  }

  for (const edge of edges) {
    const [first, second] = edge.split(":").map(Number);
    if (first === undefined || second === undefined) continue;
    const point = points[first];
    const other = points[second];
    if (!point || !other) continue;

    const depth = (point.depth + other.depth) / 2;
    ctx2d.beginPath();
    ctx2d.moveTo(point.x, point.y);
    ctx2d.lineTo(other.x, other.y);
    ctx2d.strokeStyle = `rgba(255, 132, 0, ${0.05 + 0.25 * depth})`;
    ctx2d.lineWidth = 0.8;
    ctx2d.stroke();
  }

  const drawOrder = [...points].sort((a, b) => a.depth - b.depth);

  for (const point of drawOrder) {
    const depth = Math.max(0, Math.min(1, point.depth));
    const scale = 0.55 + 0.45 * depth;
    const alpha = 0.25 + 0.75 * depth;
    const isHovered = point.node.index === hoveredNodeIndex;
    const bubbleRadius = 23 * scale * (isHovered ? 1.15 : 1);
    const iconSize = 25 * scale * (isHovered ? 1.15 : 1);

    ctx2d.save();
    ctx2d.globalAlpha = alpha;
    ctx2d.beginPath();
    ctx2d.arc(point.x, point.y, bubbleRadius, 0, Math.PI * 2);
    ctx2d.fillStyle = "rgba(255, 252, 246, 0.95)";
    ctx2d.shadowColor = "rgba(255, 132, 0, 0.24)";
    ctx2d.shadowBlur = 12 * scale;
    ctx2d.fill();
    ctx2d.shadowBlur = 0;
    ctx2d.strokeStyle = "rgba(255, 132, 0, 0.18)";
    ctx2d.lineWidth = 1;
    ctx2d.stroke();

    ctx2d.save();
    ctx2d.translate(point.x, point.y);
    drawIcon(ctx2d, point.node, iconSize);
    ctx2d.restore();
    ctx2d.restore();
  }

  const labelBounds: LabelBounds[] = [];
  const hoveredPoint =
    hoveredNodeIndex === null
      ? undefined
      : points.find((point) => point.node.index === hoveredNodeIndex);
  const labelOrder = [
    ...(hoveredPoint ? [hoveredPoint] : []),
    ...drawOrder.filter(
      (point) =>
        point.depth > 0.5 && point.node.index !== hoveredNodeIndex,
    ),
  ];

  for (const point of labelOrder) {
    const depth = Math.max(0, Math.min(1, point.depth));
    const isHovered = point.node.index === hoveredNodeIndex;
    const scale = 0.55 + 0.45 * depth;
    const bubbleRadius = 23 * scale * (isHovered ? 1.15 : 1);
    ctx2d.font = `${isHovered ? "700" : "600"} 11px Urbanist, system-ui, sans-serif`;
    ctx2d.textAlign = "center";
    ctx2d.textBaseline = "top";
    const labelWidth = ctx2d.measureText(point.node.name).width;
    const label: LabelBounds = {
      left: point.x - labelWidth / 2 - 3,
      right: point.x + labelWidth / 2 + 3,
      top: point.y + bubbleRadius + 2,
      bottom: point.y + bubbleRadius + 17,
    };
    const overlaps = labelBounds.some(
      (other) =>
        label.left < other.right &&
        label.right > other.left &&
        label.top < other.bottom &&
        label.bottom > other.top,
    );
    if (!overlaps) {
      ctx2d.globalAlpha = isHovered ? 1 : 0.25 + 0.75 * depth;
      ctx2d.fillStyle = isHovered ? "#ff8400" : "#2d2a24";
      ctx2d.fillText(point.node.name, point.x, point.y + bubbleRadius + 5);
      labelBounds.push(label);
    }
  }

  ctx2d.restore();
}

function resizeCanvas() {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const rect = canvas.getBoundingClientRect();
  const width = Math.max(1, rect.width);
  const height = Math.max(1, rect.height);
  pixelRatio = Math.max(1, window.devicePixelRatio || 1);
  canvasWidth = width;
  canvasHeight = height;
  canvas.width = Math.round(width * pixelRatio);
  canvas.height = Math.round(height * pixelRatio);

  canvasContext = canvas.getContext("2d");
  canvasContext?.setTransform(pixelRatio, 0, 0, pixelRatio, 0, 0);
  drawSphere();
}

function stopAnimation() {
  if (frameId) cancelAnimationFrame(frameId);
  frameId = 0;
  previousFrameTime = 0;
}

function animate(timestamp: number) {
  if (!visible || reducedMotion) {
    stopAnimation();
    return;
  }

  const delta = previousFrameTime
    ? Math.min(timestamp - previousFrameTime, 50)
    : frameDuration;
  previousFrameTime = timestamp;

  const frameScale = delta / frameDuration;
  if (!dragging && inertiaActive) {
    rotation += inertiaRotationSpeed * frameScale;
    tilt = Math.max(
      -0.6,
      Math.min(0.6, tilt + inertiaTiltSpeed * frameScale),
    );
    const decay = inertiaDecayPerFrame ** frameScale;
    inertiaTiltSpeed *= decay;
    if (Math.abs(inertiaRotationSpeed) > radiansPerFrameAt60Fps) {
      inertiaRotationSpeed *= decay;
      if (Math.abs(inertiaRotationSpeed) <= radiansPerFrameAt60Fps) {
        inertiaRotationSpeed =
          Math.sign(inertiaRotationSpeed) * radiansPerFrameAt60Fps;
      }
    }
    if (timestamp - lastInteractionTime >= autoRotationDelay) {
      inertiaActive = false;
      inertiaRotationSpeed = 0;
      inertiaTiltSpeed = 0;
    }
  } else if (!dragging && timestamp - lastInteractionTime >= autoRotationDelay) {
    const targetSpeed =
      hovered ? hoverRadiansPerFrameAt60Fps : radiansPerFrameAt60Fps;
    const speedBlend = 1 - Math.exp(-delta / 280);
    rotationSpeed += (targetSpeed - rotationSpeed) * speedBlend;
    rotation += rotationSpeed * frameScale;
  }
  drawSphere();
  frameId = requestAnimationFrame(animate);
}

function updateAnimation() {
  if (visible && !reducedMotion && !frameId) {
    frameId = requestAnimationFrame(animate);
  } else if ((!visible || reducedMotion) && frameId) {
    stopAnimation();
    drawSphere();
  }
}

function updatePointerPosition(event: PointerEvent) {
  const canvas = canvasRef.value;
  if (!canvas) return;
  const rect = canvas.getBoundingClientRect();
  pointerPosition = {
    x: event.clientX - rect.left,
    y: event.clientY - rect.top,
  };
}

function handlePointerDown(event: PointerEvent) {
  if (event.pointerType === "mouse" && event.button !== 0) return;
  const canvas = canvasRef.value;
  if (!canvas) return;

  dragging = true;
  canvas.classList.add("is-dragging");
  activePointerId = event.pointerId;
  lastPointerX = event.clientX;
  lastPointerY = event.clientY;
  lastPointerTime = event.timeStamp;
  lastInteractionTime = performance.now();
  inertiaRotationSpeed = 0;
  inertiaTiltSpeed = 0;
  inertiaActive = false;
  hoveredNodeIndex = null;
  updatePointerPosition(event);
  canvas.setPointerCapture(event.pointerId);
  drawSphere();
}

function handlePointerMove(event: PointerEvent) {
  if (dragging && event.pointerId === activePointerId) {
    const elapsed = Math.max(1, event.timeStamp - lastPointerTime);
    const deltaX = event.clientX - lastPointerX;
    const deltaY = event.clientY - lastPointerY;
    const rotationDelta = deltaX * radiansPerPixel;
    const tiltDelta = deltaY * radiansPerPixel;
    rotation += rotationDelta;
    tilt = Math.max(-0.6, Math.min(0.6, tilt + tiltDelta));
    inertiaRotationSpeed =
      (rotationDelta * frameDuration) / elapsed;
    inertiaTiltSpeed = (tiltDelta * frameDuration) / elapsed;
    lastPointerX = event.clientX;
    lastPointerY = event.clientY;
    lastPointerTime = event.timeStamp;
    lastInteractionTime = performance.now();
    updatePointerPosition(event);
    drawSphere();
    return;
  }

  updatePointerPosition(event);
  hovered = true;
  drawSphere();
}

function endDrag(event?: PointerEvent) {
  if (!dragging || (event && event.pointerId !== activePointerId)) return;
  const canvas = canvasRef.value;
  const pointerId = activePointerId;
  dragging = false;
  canvas?.classList.remove("is-dragging");
  activePointerId = null;
  lastInteractionTime = performance.now();
  inertiaActive = !reducedMotion;
  if (reducedMotion) {
    inertiaRotationSpeed = 0;
    inertiaTiltSpeed = 0;
  }
  if (pointerId !== null && canvas?.hasPointerCapture(pointerId)) {
    canvas.releasePointerCapture(pointerId);
  }
  drawSphere();
}

function handlePointerEnter(event: PointerEvent) {
  updatePointerPosition(event);
  hovered = true;
  drawSphere();
}

function handlePointerLeave() {
  hovered = false;
  pointerPosition = null;
  hoveredNodeIndex = null;
  drawSphere();
}

function handlePointerCancel(event: PointerEvent) {
  endDrag(event);
}

function handleWindowPointerUp(event: PointerEvent) {
  endDrag(event);
}

function handleWindowBlur() {
  endDrag();
}

function handleMotionPreferenceChange(event: MediaQueryListEvent) {
  reducedMotion = event.matches;
  updateAnimation();
}

onMounted(() => {
  const section = sectionRef.value;
  const canvas = canvasRef.value;
  const stage = section?.querySelector<HTMLElement>(".skills-sphere-stage");
  const heading = section?.querySelector<HTMLElement>(".skills-heading");
  if (!section || !canvas || !stage) return;

  nodes = createSphereNodes();
  lastInteractionTime = performance.now() - autoRotationDelay;
  mediaQuery = window.matchMedia("(prefers-reduced-motion: reduce)");
  reducedMotion = mediaQuery.matches;
  mediaQuery.addEventListener("change", handleMotionPreferenceChange);

  resizeObserver = new ResizeObserver(resizeCanvas);
  resizeObserver.observe(stage);
  resizeCanvas();

  canvas.addEventListener("pointerdown", handlePointerDown);
  canvas.addEventListener("pointermove", handlePointerMove);
  canvas.addEventListener("pointerup", endDrag);
  canvas.addEventListener("pointerleave", handlePointerLeave);
  canvas.addEventListener("pointercancel", handlePointerCancel);
  window.addEventListener("pointerup", handleWindowPointerUp);
  window.addEventListener("pointercancel", handlePointerCancel);
  window.addEventListener("blur", handleWindowBlur);
  canvas.addEventListener("pointerenter", handlePointerEnter);

  visibilityObserver = new IntersectionObserver(
    (entries) => {
      visible = entries[0]?.isIntersecting ?? false;
      updateAnimation();
    },
    { threshold: 0.1 },
  );
  visibilityObserver.observe(section);

  ctx = gsap.context(() => {
    if (reducedMotion) {
      gsap.set([heading, stage, ...section.querySelectorAll(".skills-group")], {
        clearProps: "all",
      });
      return;
    }

    const eyebrow = section.querySelector(".skills-eyebrow");
    const title = section.querySelector(".skills-heading h2");
    if (eyebrow && title) {
      gsap.fromTo(
        [eyebrow, title],
        { autoAlpha: 0, y: 40 },
        {
          autoAlpha: 1,
          y: 0,
          ease: "none",
          stagger: 0.12,
          scrollTrigger: {
            trigger: section,
            start: "top 85%",
            end: "top 48%",
            scrub: true,
          },
        },
      );
    }

    const groups = gsap.utils.toArray<HTMLElement>(
      section.querySelectorAll(".skills-group"),
    );
    gsap.fromTo(
      groups,
      { autoAlpha: 0, y: 24 },
      {
        autoAlpha: 1,
        y: 0,
        ease: "none",
        stagger: 0.06,
        scrollTrigger: {
          trigger: section.querySelector(".skills-groups"),
          start: "top 85%",
          end: "bottom 55%",
          scrub: true,
        },
      },
    );

    gsap.fromTo(
      stage,
      { autoAlpha: 0, scale: 0.92 },
      {
        autoAlpha: 1,
        scale: 1,
        ease: "none",
        scrollTrigger: {
          trigger: stage,
          start: "top 85%",
          end: "top 45%",
          scrub: true,
        },
      },
    );
  }, section);

  refreshFrame = requestAnimationFrame(() => ScrollTrigger.refresh());
});

onUnmounted(() => {
  if (refreshFrame) cancelAnimationFrame(refreshFrame);
  stopAnimation();
  const canvas = canvasRef.value;
  if (activePointerId !== null && canvas?.hasPointerCapture(activePointerId)) {
    canvas.releasePointerCapture(activePointerId);
  }
  resizeObserver?.disconnect();
  visibilityObserver?.disconnect();
  canvas?.removeEventListener("pointerdown", handlePointerDown);
  canvas?.removeEventListener("pointermove", handlePointerMove);
  canvas?.removeEventListener("pointerup", endDrag);
  canvas?.removeEventListener("pointerleave", handlePointerLeave);
  canvas?.removeEventListener("pointercancel", handlePointerCancel);
  canvas?.removeEventListener("pointerenter", handlePointerEnter);
  window.removeEventListener("pointerup", handleWindowPointerUp);
  window.removeEventListener("pointercancel", handlePointerCancel);
  window.removeEventListener("blur", handleWindowBlur);
  mediaQuery?.removeEventListener("change", handleMotionPreferenceChange);
  ctx?.revert();
  resizeObserver = null;
  visibilityObserver = null;
  mediaQuery = null;
  ctx = null;
});
</script>

<template>
  <section id="skills" ref="sectionRef" class="skills-section">
    <header class="skills-heading">
      <p class="skills-eyebrow">MY SKILLS</p>
      <h2>Tools I build with<span>.</span></h2>
    </header>

    <div
      class="skills-sphere-stage"
      role="img"
      aria-label="An animated sphere of programming, frontend, backend, data, database, cloud, and developer tool logos"
    >
      <canvas ref="canvasRef" aria-hidden="true"></canvas>
    </div>

    <div class="skills-groups">
      <section v-for="group in groups" :key="group.name" class="skills-group">
        <h3>{{ group.name }}</h3>
        <ul>
          <li v-for="skill in group.skills" :key="skill">{{ skill }}</li>
        </ul>
      </section>
    </div>
  </section>
</template>

<style scoped lang="scss">
.skills-section {
  width: 100%;
  padding: clamp(72px, 9vw, 128px) var(--space-outer, 24px) 96px;
  overflow: hidden;
  background: transparent;
  scroll-margin-top: 110px;
}

.skills-heading {
  margin: 0 auto 22px;
  text-align: center;
}

.skills-eyebrow {
  margin: 0 0 14px;
  color: #ff8400;
  font-size: 12px;
  font-weight: 800;
  letter-spacing: 0.22em;
  text-transform: uppercase;
}

.skills-heading h2 {
  margin: 0;
  color: #2d2a24;
  font-size: clamp(40px, 6vw, 72px);
  font-weight: 800;
  letter-spacing: -0.05em;
  line-height: 1;
}

.skills-heading h2 span {
  color: #ff8400;
}

.skills-sphere-stage {
  width: min(920px, 100%);
  height: clamp(340px, 56vw, 570px);
  margin: 0 auto 26px;
  opacity: 1;
  transform: none;
  will-change: opacity, transform;
}

.skills-sphere-stage canvas {
  display: block;
  width: 100%;
  height: 100%;
  cursor: grab;
  touch-action: pan-y;
  user-select: none;
}

.skills-sphere-stage canvas.is-dragging {
  cursor: grabbing;
}

.skills-groups {
  display: grid;
  grid-auto-flow: column;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  grid-template-rows: repeat(4, max-content);
  gap: 40px 42px;
  width: min(1000px, 100%);
  margin: 0 auto;
}

.skills-group {
  min-width: 0;
}

.skills-group h3 {
  margin: 0 0 12px;
  color: #ff8400;
  font-size: 14px;
  font-weight: 800;
  letter-spacing: 0.04em;
}

.skills-group ul {
  display: flex;
  flex-wrap: wrap;
  gap: 9px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.skills-group li {
  display: inline-flex;
  min-height: 38px;
  align-items: center;
  padding: 8px 14px;
  border: 1px solid rgba(255, 132, 0, 0.25);
  border-radius: 999px;
  color: #5f5646;
  background: rgba(255, 255, 255, 0.5);
  font-size: 14px;
  line-height: 1.25;
  transition:
    transform 0.2s ease,
    background-color 0.2s ease,
    box-shadow 0.2s ease;
}

.skills-group li:hover {
  transform: translateY(-2px);
  background: rgba(255, 255, 255, 0.78);
  box-shadow: 0 5px 14px rgba(80, 60, 35, 0.08);
}

@media (max-width: 839px) {
  .skills-groups {
    grid-auto-flow: row;
    grid-template-columns: 1fr;
    grid-template-rows: none;
  }
}

@media (max-width: 600px) {
  .skills-section {
    padding-bottom: 64px;
  }

  .skills-sphere-stage {
    height: min(80vw, 340px);
    min-height: 0;
    margin-bottom: 12px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .skills-sphere-stage {
    opacity: 1;
    transform: none;
    transition: none;
    will-change: auto;
  }

  .skills-group,
  .skills-group li {
    opacity: 1 !important;
    transform: none !important;
    transition: none !important;
  }
}
</style>
