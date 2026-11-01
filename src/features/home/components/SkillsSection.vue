<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const sectionRef = ref<HTMLElement | null>(null);
const failedLogos = ref<Set<string>>(new Set());
let ctx: gsap.Context | null = null;

const groups = [
  {
    title: "Languages",
    icon: "</>",
    skills: ["Python", "JavaScript", "TypeScript", "SQL", "Java"],
  },
  {
    title: "Frontend",
    icon: "UI",
    skills: ["React.js", "Redux Toolkit", "HTML5", "CSS3", "Tailwind CSS", "Responsive Design"],
  },
  {
    title: "Backend & APIs",
    icon: "API",
    skills: ["FastAPI", "Node.js", "Express.js", "RESTful APIs", "JWT", "OAuth 2.0"],
  },
  {
    title: "Data Analytics",
    icon: "01",
    skills: ["pandas", "NumPy", "SQLite", "Data Cleaning & Validation", "Power BI", "Excel", "pytest"],
  },
  {
    title: "DevOps & Cloud",
    icon: "++",
    skills: ["Docker", "GitHub Actions", "CI/CD", "Vercel", "Render", "AWS (Cloud Practitioner)"],
  },
];

const skillLogos: Record<string, string> = {
  Python: "/skill-logos/python.svg",
  JavaScript: "/skill-logos/javascript.svg",
  TypeScript: "/skill-logos/typescript.svg",
  "React.js": "/skill-logos/react.svg",
  "Redux Toolkit": "/skill-logos/redux-toolkit.svg",
  HTML5: "/skill-logos/html5.svg",
  CSS3: "/skill-logos/css3.svg",
  "Tailwind CSS": "/skill-logos/tailwind.svg",
  FastAPI: "/skill-logos/fastapi.svg",
  "Node.js": "/skill-logos/nodejs.svg",
  "Express.js": "/skill-logos/express.svg",
  pandas: "/skill-logos/pandas.svg",
  NumPy: "/skill-logos/numpy.svg",
  SQLite: "/skill-logos/sqlite.svg",
  "Power BI": "/skill-logos/power-bi.svg",
  Excel: "/skill-logos/excel.svg",
  pytest: "/skill-logos/pytest.svg",
  Docker: "/skill-logos/docker.svg",
  "GitHub Actions": "/skill-logos/github-actions.svg",
  Vercel: "/skill-logos/vercel.svg",
  Render: "/skill-logos/render.svg",
  "AWS (Cloud Practitioner)": "/skill-logos/aws.svg",
};

onMounted(() => {
  const section = sectionRef.value;
  if (!section) return;

  ctx = gsap.context(() => {
    const reducedMotion = window.matchMedia(
      "(prefers-reduced-motion: reduce)",
    ).matches;
    const heading = section.querySelector(".skills-heading");
    const cards = Array.from(section.querySelectorAll<HTMLElement>(".skills-card"));

    if (reducedMotion) {
      gsap.set([heading, ...cards], { clearProps: "all" });
      gsap.set(section.querySelectorAll(".skill-chip"), { clearProps: "all" });
      return;
    }

    const timeline = gsap.timeline({
      scrollTrigger: {
        trigger: section,
        start: "top 78%",
        end: "bottom 62%",
        scrub: 0.7,
      },
    });

    if (heading) {
      timeline.fromTo(heading, { opacity: 0, y: 30 }, {
        opacity: 1, y: 0, duration: 0.45, ease: "power3.out",
      });
    }

    cards.forEach((card, index) => {
      const chips = card.querySelectorAll<HTMLElement>(".skill-chip");
      const position = 0.18 + index * 0.15;
      timeline.fromTo(card, { opacity: 0, y: 38 }, {
        opacity: 1, y: 0, duration: 0.48, ease: "power3.out",
      }, position);
      timeline.fromTo(chips, { opacity: 0, scale: 0.9 }, {
        opacity: 1, scale: 1, duration: 0.28, stagger: 0.025, ease: "back.out(1.5)",
      }, position + 0.13);
    });
  }, section);
});

onUnmounted(() => {
  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section id="skills" ref="sectionRef" class="skills-section">
    <header class="skills-heading section-heading">
      <p class="section-eyebrow">WHAT I WORK WITH</p>
      <h2>Skills<span>.</span></h2>
    </header>

    <div class="skills-grid">
      <article v-for="group in groups" :key="group.title" class="skills-card">
        <div class="skills-card-heading">
          <div class="skills-icon" aria-hidden="true">{{ group.icon }}</div>
          <h3>{{ group.title }}</h3>
        </div>
        <ul class="skills-list">
          <li v-for="skill in group.skills" :key="skill" class="skill-chip">
            <img
              v-if="skillLogos[skill] && !failedLogos.has(skill)"
              :src="skillLogos[skill]"
              :alt="`${skill} logo`"
              loading="lazy"
              @error="failedLogos.add(skill)"
            />
            <span>{{ skill }}</span>
          </li>
        </ul>
      </article>
    </div>
  </section>
</template>

<style scoped lang="scss">
.skills-section {
  width: 100%;
  padding: 120px var(--space-outer, 24px) 140px;
  overflow: hidden;
  background: #f5efe5;
}

.section-heading {
  margin: 0 auto 72px;
  text-align: center;
}

.section-eyebrow {
  margin: 0 0 14px;
  color: #ff8a00;
  font-size: 0.78rem;
  font-weight: 800;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.section-heading h2 {
  margin: 0;
  color: #292622;
  font-size: clamp(3.5rem, 8vw, 7rem);
  font-weight: 800;
  letter-spacing: -0.055em;
  line-height: 0.95;
}

.section-heading h2 span {
  color: #ff8a00;
}

.skills-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 20px;
  width: min(1200px, 100%);
  margin: 0 auto;
}

.skills-card {
  min-width: 0;
  padding: 26px;
  border: 1px solid rgba(88, 70, 45, 0.14);
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.58);
  box-shadow: 0 14px 36px rgba(55, 42, 25, 0.07), inset 0 1px 0 rgba(255, 255, 255, 0.8);
  transition: transform 0.3s ease, border-color 0.3s ease, box-shadow 0.3s ease;
}

.skills-card:hover {
  transform: translateY(-6px);
  border-color: rgba(255, 138, 0, 0.48);
  box-shadow: 0 20px 44px rgba(55, 42, 25, 0.1), 0 0 0 3px rgba(255, 138, 0, 0.08);
}

.skills-card-heading {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 22px;
}

.skills-icon {
  display: grid;
  flex: 0 0 48px;
  width: 48px;
  height: 48px;
  place-items: center;
  border: 1px solid rgba(255, 138, 0, 0.18);
  border-radius: 15px;
  color: #fff;
  background: linear-gradient(145deg, #ff9a1f, #f27b16);
  box-shadow: 0 8px 20px rgba(255, 138, 0, 0.2);
  font-size: 0.8rem;
  font-weight: 800;
}

.skills-card h3 {
  margin: 0;
  color: #292622;
  font-size: clamp(1.05rem, 1.5vw, 1.25rem);
  font-weight: 800;
}

.skills-list {
  display: flex;
  flex-wrap: wrap;
  gap: 9px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.skill-chip {
  display: inline-flex;
  min-height: 36px;
  align-items: center;
  gap: 7px;
  padding: 7px 11px;
  border: 1px solid rgba(88, 70, 45, 0.12);
  border-radius: 999px;
  color: #49433c;
  background: rgba(255, 255, 255, 0.72);
  font-size: 0.82rem;
  font-weight: 650;
}

.skill-chip img {
  width: 16px;
  height: 16px;
  object-fit: contain;
}

@media (max-width: 1023px) {
  .skills-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 600px) {
  .skills-section {
    padding-block: 88px 100px;
  }

  .section-heading {
    margin-bottom: 42px;
  }

  .skills-grid {
    grid-template-columns: 1fr;
    gap: 14px;
  }

  .skills-card {
    padding: 22px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .skills-card,
  .skill-chip {
    transform: none !important;
    transition: none !important;
  }
}
</style>
