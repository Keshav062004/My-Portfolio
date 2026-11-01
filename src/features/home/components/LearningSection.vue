<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const sectionRef = ref<HTMLElement | null>(null);
let ctx: gsap.Context | null = null;

const topics = [
  {
    title: "Data Analytics & Power BI",
    description: "Building dashboards and reporting on the SQLite star schema from my FundScope project.",
    progress: 70,
  },
  {
    title: "DevOps & Containers",
    description: "Docker, CI/CD pipelines with GitHub Actions, and deploying services reliably.",
    progress: 45,
  },
  {
    title: "Cloud Architecture (AWS)",
    description: "Designing cloud solutions beyond the Cloud Practitioner level, with EC2, S3, and IAM.",
    progress: 35,
  },
];

onMounted(() => {
  const section = sectionRef.value;
  if (!section) return;

  ctx = gsap.context(() => {
    const reducedMotion = window.matchMedia(
      "(prefers-reduced-motion: reduce)",
    ).matches;
    const heading = section.querySelector(".learning-heading");
    const cards = Array.from(section.querySelectorAll<HTMLElement>(".learning-card"));

    if (reducedMotion) {
      gsap.set([heading, ...cards], { clearProps: "all" });
      gsap.set(section.querySelectorAll(".learning-progress-fill"), { width: "100%" });
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
      const fill = card.querySelector<HTMLElement>(".learning-progress-fill");
      const width = card.dataset.progress;
      if (!fill || !width) return;
      const position = 0.18 + index * 0.22;
      timeline.fromTo(card, { opacity: 0, y: 34 }, {
        opacity: 1, y: 0, duration: 0.45, ease: "power3.out",
      }, position);
      timeline.fromTo(fill, { width: "0%" }, {
        width: `${width}%`, duration: 0.6, ease: "power2.out",
      }, position + 0.15);
    });
  }, section);
});

onUnmounted(() => {
  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section id="learning" ref="sectionRef" class="learning-section">
    <header class="learning-heading section-heading">
      <p class="section-eyebrow">CURRENTLY EXPLORING</p>
      <h2>Learning<span>.</span></h2>
    </header>

    <div class="learning-list">
      <article
        v-for="topic in topics"
        :key="topic.title"
        class="learning-card"
        :data-progress="topic.progress"
      >
        <div class="learning-copy">
          <h3>{{ topic.title }}</h3>
          <p>{{ topic.description }}</p>
        </div>
        <div
          class="learning-progress"
          role="img"
          :aria-label="`${topic.title} visual progress: ${topic.progress}%`"
        >
          <span class="learning-progress-fill"></span>
        </div>
      </article>
    </div>
  </section>
</template>

<style scoped lang="scss">
.learning-section {
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

.learning-list {
  display: grid;
  gap: 16px;
  width: min(1050px, 100%);
  margin: 0 auto;
}

.learning-card {
  padding: 26px 30px;
  border: 1px solid rgba(88, 70, 45, 0.14);
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.58);
  box-shadow: 0 14px 36px rgba(55, 42, 25, 0.07), inset 0 1px 0 rgba(255, 255, 255, 0.8);
}

.learning-copy h3 {
  margin: 0 0 8px;
  color: #292622;
  font-size: clamp(1.1rem, 2vw, 1.35rem);
  font-weight: 800;
}

.learning-copy p {
  margin: 0;
  color: #716b62;
  font-size: 0.98rem;
  line-height: 1.65;
}

.learning-progress {
  height: 8px;
  margin-top: 20px;
  overflow: hidden;
  border-radius: 99px;
  background: rgba(88, 70, 45, 0.1);
}

.learning-progress-fill {
  display: block;
  width: 0;
  height: 100%;
  border-radius: inherit;
  background: linear-gradient(90deg, #ff8a00, #ffad45);
  box-shadow: 0 0 15px rgba(255, 138, 0, 0.32);
}

@media (max-width: 600px) {
  .learning-section {
    padding-block: 88px 100px;
  }

  .section-heading {
    margin-bottom: 42px;
  }

  .learning-card {
    padding: 22px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .learning-card,
  .learning-progress-fill {
    transform: none !important;
    transition: none !important;
  }
}
</style>
