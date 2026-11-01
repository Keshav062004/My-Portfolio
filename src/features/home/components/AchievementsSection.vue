<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

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
    description: "DSA problems solved on LeetCode",
    countUp: true,
  },
  {
    value: "Open Source",
    description: "Contributor, Hacktoberfest and Social Winter of Code",
  },
  {
    value: "Captain",
    description: "Led my university hostel cricket team to tournament victory",
  },
  {
    value: "Cloud",
    description: "AWS Cloud Practitioner Essentials completed",
    link: "#certifications",
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
    const cards = Array.from(section.querySelectorAll<HTMLElement>(".achievement-card"));

    if (reducedMotion) {
      solvedCount.value = 100;
      gsap.set([heading, ...cards], { clearProps: "all" });
      return;
    }

    const timeline = gsap.timeline({
      scrollTrigger: { trigger: section, start: "top 78%", end: "bottom 62%", scrub: 0.7 },
    });

    if (heading) {
      timeline.fromTo(heading, { opacity: 0, y: 30 }, {
        opacity: 1, y: 0, duration: 0.45, ease: "power3.out",
      });
    }

    cards.forEach((card, index) => {
      const position = 0.18 + index * 0.16;
      timeline.fromTo(card, { opacity: 0, y: 32, rotateY: index % 2 === 0 ? -5 : 5 }, {
        opacity: 1, y: 0, rotateY: 0, duration: 0.5, ease: "power3.out",
      }, position);

      if (card.dataset.countUp === "true") {
        ScrollTrigger.create({
          trigger: card,
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

      const handleMouseMove = (event: MouseEvent) => {
        const rect = card.getBoundingClientRect();
        const rotateY = (event.clientX - rect.left) / rect.width * 6 - 3;
        const rotateX = ((event.clientY - rect.top) / rect.height - 0.5) * -6;
        gsap.to(card, {
          rotateX, rotateY, transformPerspective: 1000, duration: 0.3,
          ease: "power2.out", overwrite: true,
        });
      };
      const handleMouseLeave = () => {
        gsap.to(card, {
          rotateX: 0, rotateY: 0, duration: 0.5, ease: "power3.out",
        });
      };

      card.addEventListener("mousemove", handleMouseMove);
      card.addEventListener("mouseleave", handleMouseLeave);
      hoverCleanup.push(() => {
        card.removeEventListener("mousemove", handleMouseMove);
        card.removeEventListener("mouseleave", handleMouseLeave);
      });
    });
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
    <header class="achievements-heading section-heading">
      <p class="section-eyebrow">BEYOND THE CODE</p>
      <h2>Achievements<span>.</span></h2>
    </header>

    <div class="achievements-grid">
      <article
        v-for="item in achievements"
        :key="item.value"
        class="achievement-card"
        :data-count-up="item.countUp || undefined"
      >
        <template v-if="item.link">
          <a :href="item.link" class="achievement-link">
            <strong>{{ item.countUp ? `${solvedCount}${solvedCount === 100 ? '+' : ''}` : item.value }}</strong>
            <span>{{ item.description }}</span>
          </a>
        </template>
        <template v-else>
          <strong>{{ item.countUp ? `${solvedCount}${solvedCount === 100 ? '+' : ''}` : item.value }}</strong>
          <span>{{ item.description }}</span>
        </template>
      </article>
    </div>
  </section>
</template>

<style scoped lang="scss">
.achievements-section {
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
  font-size: clamp(3.2rem, 7vw, 7rem);
  font-weight: 800;
  letter-spacing: -0.055em;
  line-height: 0.95;
}

.section-heading h2 span {
  color: #ff8a00;
}

.achievements-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 18px;
  width: min(1050px, 100%);
  margin: 0 auto;
}

.achievement-card {
  display: flex;
  min-height: 170px;
  flex-direction: column;
  justify-content: center;
  padding: 30px;
  border: 1px solid rgba(88, 70, 45, 0.14);
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.58);
  box-shadow: 0 14px 36px rgba(55, 42, 25, 0.07), inset 0 1px 0 rgba(255, 255, 255, 0.8);
  transform-style: preserve-3d;
  transition: border-color 0.25s ease, box-shadow 0.25s ease;
}

.achievement-card:hover {
  border-color: rgba(255, 138, 0, 0.42);
  box-shadow: 0 20px 44px rgba(55, 42, 25, 0.1), 0 0 0 3px rgba(255, 138, 0, 0.07);
}

.achievement-card strong {
  display: block;
  margin-bottom: 12px;
  color: #292622;
  font-size: clamp(1.6rem, 3vw, 2.25rem);
  font-weight: 850;
  letter-spacing: -0.04em;
}

.achievement-card > span,
.achievement-link > span {
  color: #716b62;
  font-size: 1rem;
  line-height: 1.6;
}

.achievement-link {
  display: flex;
  flex-direction: column;
  color: inherit;
  text-decoration: none;
}

.achievement-link strong {
  color: #ff8a00;
}

.achievement-link:focus-visible {
  outline: 3px solid rgba(255, 138, 0, 0.55);
  outline-offset: 5px;
  border-radius: 10px;
}

@media (max-width: 600px) {
  .achievements-section {
    padding-block: 88px 100px;
  }

  .section-heading {
    margin-bottom: 42px;
  }

  .achievements-grid {
    grid-template-columns: 1fr;
    gap: 14px;
  }

  .achievement-card {
    min-height: 145px;
    padding: 24px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .achievement-card {
    transform: none !important;
    transition: none !important;
  }
}
</style>
