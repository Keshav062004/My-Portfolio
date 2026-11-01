<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const experienceRef = ref<HTMLElement | null>(null);
const failedLogos = ref<Set<string>>(new Set());

type Experience = {
  role: string;
  company: string;
  location: string;
  dates: string;
  logo: string;
  logoAlt: string;
  description: string;
  points: string[];
};

const experiences: Experience[] = [
  {
    role: "Cloud Consultant",
    company: "Mist Avinya Technologies LLP",
    location: "Noida, India",
    dates: "DEC 2025 — JUNE 2026",
    logo: "/images/mist-avinya-logo.png",
    logoAlt: "Mist Avinya Technologies logo",
    description:
      "Worked with businesses to understand their cloud requirements and identify suitable AWS solutions, supporting client communication, requirement analysis, cloud POCs, and AWS-related opportunities.",
    points: [
      "Client requirement research and analysis",
      "AWS cloud solution identification",
      "AWS-funded POCs and cloud credits",
      "Cloud cost optimization opportunities",
      "Lead qualification and client communication",
      "Coordination with senior AWS consultants",
    ],
  },
  {
    role: "Front End Intern",
    company: "CertifyIX",
    location: "Delhi, India (Remote)",
    dates: "JULY 2026 — Ongoing",
    logo: "/images/certifyix-logo.png",
    logoAlt: "CertifyIX logo",
    description:
      "Built and maintained responsive front-end interfaces for web applications, working with a team to deliver reusable React components and clean, user-friendly pages.",
    points: [
      "Developed and maintained responsive web interfaces using HTML, CSS, and JavaScript",
      "Built reusable UI components and dynamic web pages using React.js",
      "Collaborated with the team to implement new features and enhance existing functionality",
      "Debugged and tested web applications for clean, responsive, user-friendly interfaces",
    ],
  },
];

let ctx: gsap.Context | null = null;
const hoverCleanup: (() => void)[] = [];

onMounted(() => {
  if (!experienceRef.value) return;

  ctx = gsap.context(() => {
    const section = experienceRef.value!;

    const eyebrow =
      section.querySelector<HTMLElement>(
        ".experience-eyebrow"
      );

    const title =
      section.querySelector<HTMLElement>(
        ".experience-heading h2"
      );

    const cards = Array.from(
      section.querySelectorAll<HTMLElement>(".experience-card")
    );
    const dates = Array.from(
      section.querySelectorAll<HTMLElement>(".experience-date")
    );
    const dots = Array.from(
      section.querySelectorAll<HTMLElement>(".experience-line-dot")
    );
    const progress = section.querySelector<HTMLElement>(
      ".experience-line-progress"
    );

    if (
      !eyebrow ||
      !title ||
      !progress ||
      cards.length !== experiences.length ||
      dates.length !== experiences.length ||
      dots.length !== experiences.length
    ) {
      return;
    }

    /* =====================================================
       INITIAL STATES
    ===================================================== */

    gsap.set(eyebrow, {
      opacity: 0,
      y: 25,
    });

    gsap.set(title, {
      opacity: 0,
      y: 70,
      rotateX: -15,
      transformPerspective: 1000,
    });

    gsap.set(cards, {
      opacity: 0,
      x: -120,
      rotateY: 7,
      transformPerspective: 1200,
      transformOrigin: "center center",
    });

    gsap.set(dates, {
      opacity: 0,
      x: 100,
      rotateY: -8,
      transformPerspective: 1000,
    });

    gsap.set(progress, {
      scaleY: 0,
      transformOrigin: "top center",
    });

    gsap.set(dots, {
      scale: 0,
      opacity: 0,
    });

    /* =====================================================
       HEADING SCROLL ANIMATION
    ===================================================== */

    const headingTimeline = gsap.timeline({
      scrollTrigger: {
        trigger: section,
        start: "top 82%",
        end: "top 52%",
        scrub: 1,
      },
    });

    headingTimeline
      .to(eyebrow, {
        opacity: 1,
        y: 0,
        duration: 0.4,
        ease: "power3.out",
      })
      .to(
        title,
        {
          opacity: 1,
          y: 0,
          rotateX: 0,
          duration: 0.8,
          ease: "power4.out",
        },
        "-=0.15"
      );

    /* =====================================================
       MAIN EXPERIENCE SCROLL ANIMATION
    ===================================================== */

    const experienceTimeline = gsap.timeline({
      scrollTrigger: {
        trigger: ".experience-wrapper",
        start: "top 75%",
        end: "bottom 60%",
        scrub: 1.2,
      },
    });

    experienceTimeline.to(
      progress,
      {
        scaleY: 1,
        duration: Math.max(1, experiences.length * 0.5 + 0.45),
        ease: "none",
      },
      0
    );

    cards.forEach((card, index) => {
      const date = dates[index];
      const dot = dots[index];
      if (!date || !dot) return;
      const offset = index * 0.5;

      experienceTimeline
        .to(
          dot,
          {
            scale: 1,
            opacity: 1,
            duration: 0.3,
            ease: "back.out(2)",
          },
          0.05 + offset
        )
        .to(
          card,
          {
            opacity: 1,
            x: 0,
            rotateY: 0,
            duration: 0.75,
            ease: "power3.out",
          },
          0.12 + offset
        )
        .to(
          date,
          {
            opacity: 1,
            x: 0,
            rotateY: 0,
            duration: 0.65,
            ease: "power3.out",
          },
          0.3 + offset
        );

      ScrollTrigger.create({
        trigger: dot,
        start: "top 65%",
        end: "bottom 40%",
        onEnter: () => {
          gsap.to(dot, {
            boxShadow: `
              0 0 0 7px rgba(255,138,0,0.12),
              0 0 35px rgba(255,138,0,0.55)
            `,
            duration: 0.5,
            ease: "power2.out",
          });
        },
        onLeaveBack: () => {
          gsap.to(dot, {
            boxShadow: `
              0 0 0 7px rgba(255,138,0,0.08),
              0 0 20px rgba(255,138,0,0.25)
            `,
            duration: 0.4,
          });
        },
      });

      const cardContent = card.querySelector<HTMLElement>(
        ".experience-card-content"
      );

      if (!cardContent) return;

      const handleMouseMove = (event: MouseEvent) => {
        const rect = card.getBoundingClientRect();
        const mouseX = event.clientX - rect.left;
        const mouseY = event.clientY - rect.top;
        const rotateY = (mouseX / rect.width - 0.5) * 5;
        const rotateX = (mouseY / rect.height - 0.5) * -5;

        gsap.to(cardContent, {
          rotateX,
          rotateY,
          translateZ: 12,
          duration: 0.35,
          ease: "power2.out",
          overwrite: true,
        });
      };

      const handleMouseLeave = () => {
        gsap.to(cardContent, {
          rotateX: 0,
          rotateY: 0,
          translateZ: 0,
          duration: 0.6,
          ease: "power3.out",
        });
      };

      card.addEventListener("mousemove", handleMouseMove);
      card.addEventListener("mouseleave", handleMouseLeave);
      hoverCleanup.push(() => {
        card.removeEventListener("mousemove", handleMouseMove);
        card.removeEventListener("mouseleave", handleMouseLeave);
      });
    });

    /* =====================================================
       REFRESH SCROLLTRIGGER
    ===================================================== */

    requestAnimationFrame(() => {
      ScrollTrigger.refresh();
    });
  }, experienceRef.value);
});

onUnmounted(() => {
  hoverCleanup.forEach((cleanup) => cleanup());
  hoverCleanup.length = 0;
  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section
    ref="experienceRef"
    id="experience"
    class="experience"
  >
    <!-- =================================================
         HEADING
    ================================================== -->

    <div class="experience-heading">
      <p class="experience-eyebrow">
        WHAT I HAVE DONE SO FAR
      </p>

      <h2>
        Work Experience<span>.</span>
      </h2>
    </div>

    <!-- =================================================
         EXPERIENCE WRAPPER
    ================================================== -->

    <div class="experience-wrapper">

      <!-- =================================================
           TIMELINE
      ================================================== -->

      <div class="experience-line">

        <div class="experience-line-progress"></div>

      </div>

      <div
        v-for="experience in experiences"
        :key="experience.company"
        class="experience-item"
      >
        <div class="experience-line-dot"></div>

        <article class="experience-card">
          <div class="experience-card-glow"></div>

          <div class="experience-card-content">
            <div class="experience-card-header">
              <div class="experience-role">
                <img
                  v-if="!failedLogos.has(experience.company)"
                  class="experience-logo"
                  :src="experience.logo"
                  :alt="experience.logoAlt"
                  loading="lazy"
                  @error="failedLogos.add(experience.company)"
                />
                <div v-else class="experience-icon">
                  <svg
                    viewBox="0 0 24 24"
                    fill="none"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      d="M20 21V19C20 16.7909 18.2091 15 16 15H8C5.79086 15 4 16.7909 4 19V21"
                      stroke="currentColor"
                      stroke-width="1.8"
                      stroke-linecap="round"
                    />
                    <circle
                      cx="12"
                      cy="7"
                      r="4"
                      stroke="currentColor"
                      stroke-width="1.8"
                    />
                  </svg>
                </div>

                <div>
                  <h3>{{ experience.role }}</h3>
                  <h4>{{ experience.company }}</h4>
                  <p class="experience-location">{{ experience.location }}</p>
                </div>
              </div>
            </div>

            <div class="experience-divider"></div>

            <p class="experience-description">
              {{ experience.description }}
            </p>

            <ul class="experience-list">
              <li v-for="point in experience.points" :key="point">
                {{ point }}
              </li>
            </ul>
          </div>
        </article>

        <div class="experience-date">
          <div class="experience-date-icon">
            <svg
              viewBox="0 0 24 24"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <rect
                x="3"
                y="4"
                width="18"
                height="17"
                rx="2"
                stroke="currentColor"
                stroke-width="1.8"
              />
              <path
                d="M16 2V6"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
              />
              <path
                d="M8 2V6"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
              />
              <path
                d="M3 10H21"
                stroke="currentColor"
                stroke-width="1.8"
              />
            </svg>
          </div>

          <div class="experience-date-text">
            <strong>{{ experience.dates }}</strong>
          </div>
        </div>
      </div>

    </div>
  </section>
</template>

<style scoped lang="scss">

/* =====================================================
   EXPERIENCE SECTION
===================================================== */

.experience {
  position: relative;

  width: 100%;

  padding:
    0
    var(--space-outer)
    160px;

  overflow: hidden;

  background:
    var(
      --color-background-300,
      #f5efe5
    );
}


/* =====================================================
   HEADING
===================================================== */

.experience-heading {
  position: relative;

  text-align: center;

  margin-bottom: 100px;

  z-index: 2;
}

.experience-eyebrow {
  margin: 0 0 14px;

  font-size: 0.85rem;

  font-weight: 700;

  letter-spacing: 0.18em;

  text-transform: uppercase;

  color:
    rgba(45, 42, 38, 0.5);
}

.experience-heading h2 {
  margin: 0;

  font-size:
    clamp(
      4rem,
      8vw,
      8rem
    );

  line-height: 0.9;

  font-weight: 800;

  letter-spacing: -0.055em;

  color: #292622;

  transform-style: preserve-3d;
}

.experience-heading h2 span {
  color: #ff8a00;
}


/* =====================================================
   MAIN WRAPPER
===================================================== */

.experience-wrapper {
  position: relative;

  width:
    min(
      1350px,
      100%
    );

  margin: 0 auto;

  display: flex;
  flex-direction: column;
  gap: 90px;
}

.experience-item {
  position: relative;
  display: grid;
  grid-template-columns:
    minmax(0, 1fr)
    70px
    minmax(280px, 0.45fr);
  column-gap: 35px;
  align-items: start;
}


/* =====================================================
   TIMELINE BASE
===================================================== */

.experience-line {
  position: absolute;

  top: 0;
  bottom: 0;

  left:
    calc(
      69% + 35px
    );

  width: 3px;

  background:
    linear-gradient(
      to bottom,
      transparent 0%,
      rgba(42, 39, 35, 0.12) 5%,
      rgba(42, 39, 35, 0.22) 50%,
      rgba(42, 39, 35, 0.12) 95%,
      transparent 100%
    );

  transform:
    translateX(-50%);

  z-index: 1;
}


/* =====================================================
   ANIMATED ORANGE TIMELINE
===================================================== */

.experience-line-progress {
  position: absolute;

  top: 0;
  left: 0;

  width: 100%;
  height: 100%;

  background:
    linear-gradient(
      to bottom,
      #ff8a00,
      #ff9d24,
      rgba(255, 138, 0, 0.15)
    );

  transform:
    scaleY(0);

  transform-origin:
    top center;

  box-shadow:
    0 0 14px
    rgba(255, 138, 0, 0.35);

  z-index: 2;
}


/* =====================================================
   TIMELINE DOT
===================================================== */

.experience-line-dot {
  position: absolute;

  top: 0;

  left:
    calc(
      69% + 35px
    );

  width: 22px;
  height: 22px;

  transform:
    translate(-50%, -2px);

  border-radius: 50%;

  background:
    #ff8a00;

  border:
    6px solid
    rgba(255, 255, 255, 0.95);

  box-shadow:
    0 0 0 7px
    rgba(255, 138, 0, 0.1),

    0 0 28px
    rgba(255, 138, 0, 0.45);

  z-index: 5;
}


/* =====================================================
   EXPERIENCE CARD
===================================================== */

.experience-card {
  position: relative;

  grid-column: 1;

  width: 100%;

  padding: 42px;

  margin: 0;

  border-radius: 26px;

  background:
    linear-gradient(
      135deg,
      rgba(255, 255, 255, 0.97),
      rgba(250, 247, 242, 0.88)
    );

  border:
    1px solid
    rgba(255, 255, 255, 0.95);

  box-shadow:
    0 28px 75px
    rgba(50, 42, 32, 0.13),

    inset 0 1px 0
    rgba(255, 255, 255, 0.98);

  backdrop-filter:
    blur(18px);

  overflow: hidden;

  z-index: 3;

  will-change:
    transform,
    opacity;
}


/* =====================================================
   CARD GLOW
===================================================== */

.experience-card-glow {
  position: absolute;

  width: 350px;
  height: 350px;

  top: -190px;
  right: -130px;

  border-radius: 50%;

  background:
    rgba(255, 138, 0, 0.13);

  filter:
    blur(70px);

  pointer-events: none;
}


/* =====================================================
   CARD CONTENT
===================================================== */

.experience-card-content {
  position: relative;

  z-index: 2;

  transform-style:
    preserve-3d;

  will-change:
    transform;
}


/* =====================================================
   ROLE
===================================================== */

.experience-role {
  display: flex;

  align-items: flex-start;

  gap: 18px;
}


/* =====================================================
   ICON
===================================================== */

.experience-icon {
  flex:
    0 0 auto;

  width: 54px;
  height: 54px;

  display: flex;

  align-items: center;
  justify-content: center;

  border-radius: 15px;

  color:
    #ff8a00;

  background:
    rgba(255, 138, 0, 0.08);

  box-shadow:
    inset 0 0 0 1px
    rgba(255, 138, 0, 0.12),

    0 10px 25px
    rgba(255, 138, 0, 0.08);
}

.experience-logo {
  flex: 0 0 54px;
  width: 54px;
  height: 54px;
  padding: 6px;
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: inset 0 0 0 1px rgba(255, 138, 0, 0.12);
  object-fit: contain;
}

.experience-icon svg {
  width: 27px;
  height: 27px;
}


/* =====================================================
   JOB TITLE
===================================================== */

.experience-role h3 {
  margin: 0;

  font-size:
    clamp(
      1.5rem,
      2vw,
      2rem
    );

  font-weight: 800;

  color:
    #292622;
}

.experience-role h4 {
  margin:
    7px 0 3px;

  font-size:
    1.15rem;

  font-weight: 700;

  color:
    #393530;
}

.experience-location {
  margin: 0;

  color:
    rgba(45, 42, 38, 0.55);

  font-size:
    0.95rem;
}


/* =====================================================
   DIVIDER
===================================================== */

.experience-divider {
  width: 100%;

  height: 1px;

  margin:
    28px 0;

  background:
    linear-gradient(
      to right,
      rgba(42, 39, 35, 0.15),
      transparent
    );
}


/* =====================================================
   DESCRIPTION
===================================================== */

.experience-description {
  margin: 0;

  max-width: 760px;

  font-size:
    1.05rem;

  line-height:
    1.7;

  color:
    rgba(45, 42, 38, 0.78);
}


/* =====================================================
   RESPONSIBILITIES
===================================================== */

.experience-list {
  margin:
    25px 0 0;

  padding: 0;

  list-style: none;

  display: flex;

  flex-direction: column;

  gap: 12px;
}

.experience-list li {
  position: relative;

  padding-left: 22px;

  font-size:
    1rem;

  line-height:
    1.5;

  color:
    rgba(45, 42, 38, 0.75);
}

.experience-list li::before {
  content: "";

  position: absolute;

  left: 0;

  top: 9px;

  width: 7px;
  height: 7px;

  border-radius: 50%;

  background:
    #ff8a00;

  box-shadow:
    0 0 8px
    rgba(255, 138, 0, 0.4);
}


/* =====================================================
   DATE BADGE
===================================================== */

.experience-date {
  position: relative;
  grid-column: 3;
  margin-top: 38px;

  display: flex;

  align-items: center;

  gap: 16px;

  padding:
    18px 24px;

  border-radius:
    18px;

  background:
    linear-gradient(
      135deg,
      rgba(255, 255, 255, 0.96),
      rgba(255, 246, 235, 0.86)
    );

  border:
    1px solid
    rgba(255, 138, 0, 0.2);

  box-shadow:
    0 18px 45px
    rgba(50, 42, 32, 0.09),

    inset 0 1px 0
    rgba(255, 255, 255, 0.95);

  backdrop-filter:
    blur(14px);

  z-index: 4;

  white-space: nowrap;

  will-change:
    transform,
    opacity;
}


/* =====================================================
   DATE ACCENT
===================================================== */

.experience-date::before {
  content: "";

  position: absolute;

  left: 0;

  top: 12px;
  bottom: 12px;

  width: 4px;

  border-radius:
    4px;

  background:
    #ff8a00;

  box-shadow:
    0 0 14px
    rgba(255, 138, 0, 0.4);
}


/* =====================================================
   CALENDAR ICON
===================================================== */

.experience-date-icon {
  width: 42px;
  height: 42px;

  flex:
    0 0 auto;

  display: flex;

  align-items: center;
  justify-content: center;

  border-radius:
    12px;

  color:
    #ff8a00;

  background:
    rgba(255, 138, 0, 0.09);
}

.experience-date-icon svg {
  width: 23px;
  height: 23px;
}


/* =====================================================
   DATE TEXT
===================================================== */

.experience-date-text {
  display: flex;

  align-items: center;

  gap: 10px;

  white-space:
    nowrap;
}

.experience-date-text strong {
  font-size:
    1rem;

  font-weight:
    800;

  letter-spacing:
    0.03em;

  color:
    #292622;
}

.experience-date-text span {
  color:
    #ff8a00;

  font-weight:
    800;
}


/* =====================================================
   CARD HOVER
===================================================== */

.experience-card {
  transition:
    box-shadow 0.5s ease;
}

.experience-card:hover {
  box-shadow:
    0 38px 95px
    rgba(50, 42, 32, 0.17),

    inset 0 1px 0
    rgba(255, 255, 255, 1);
}


/* =====================================================
   TABLET
===================================================== */

@media (max-width: 1100px) {

  .experience {
    padding-left:
      40px;

    padding-right:
      40px;
  }

  .experience-wrapper {
    width: 100%;
  }

  .experience-item {
    grid-template-columns:
      minmax(0, 1fr)
      55px
      minmax(230px, 0.42fr);
    column-gap: 20px;
  }

  .experience-line {
    left:
      68%;
  }

  .experience-line-dot {
    left: 68%;
  }

  .experience-date {
    margin-top: 38px;
    padding:
      15px 18px;
  }

  .experience-date-text strong {
    font-size:
      0.88rem;
  }

  .experience-card {
    padding:
      32px;
  }
}


/* =====================================================
   MOBILE
===================================================== */

@media (max-width: 700px) {

  .experience {
    padding:
      0
      20px
      110px;
  }

  .experience-heading {
    margin-bottom:
      60px;
  }

  .experience-eyebrow {
    font-size:
      0.72rem;
  }

  .experience-heading h2 {
    font-size:
      clamp(
        3rem,
        14vw,
        5rem
      );
  }

  .experience-wrapper {
    display:
      block;

    width:
      100%;

    padding-left:
      30px;
  }

  .experience-item {
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  .experience-item + .experience-item {
    margin-top: 90px;
  }


  /* Timeline */

  .experience-line {
    left:
      10px;

    top:
      0;

    bottom:
      0;

    width:
      2px;

    transform:
      none;
  }

  .experience-line-dot {
    left: -20px;

    width:
      18px;

    height:
      18px;

    border-width:
      5px;
  }


  /* Card */

  .experience-card {
    width:
      100%;

    padding:
      26px
      22px;

    border-radius:
      20px;
  }

  .experience-role {
    gap:
      12px;
  }

  .experience-icon {
    width:
      44px;

    height:
      44px;

    border-radius:
      12px;
  }

  .experience-icon svg {
    width:
      23px;

    height:
      23px;
  }

  .experience-role h3 {
    font-size:
      1.35rem;
  }

  .experience-role h4 {
    font-size:
      1rem;
  }

  .experience-location {
    font-size:
      0.85rem;
  }

  .experience-description {
    font-size:
      0.95rem;

    line-height:
      1.65;
  }

  .experience-list {
    gap:
      10px;
  }

  .experience-list li {
    font-size:
      0.9rem;
  }


  /* Date */

  .experience-date {
    position:
      relative;

    display:
      inline-flex;

    margin-top:
      25px;

    margin-left:
      0;

    padding:
      12px
      16px;

    gap:
      10px;
  }

  .experience-date-icon {
    width:
      34px;

    height:
      34px;
  }

  .experience-date-icon svg {
    width:
      20px;

    height:
      20px;
  }

  .experience-date-text {
    gap:
      7px;
  }

  .experience-date-text strong {
    font-size:
      0.75rem;
  }
}


/* =====================================================
   SMALL MOBILE
===================================================== */

@media (max-width: 420px) {

  .experience {
    padding-left:
      15px;

    padding-right:
      15px;
  }

  .experience-wrapper {
    padding-left:
      25px;
  }

  .experience-line-dot {
    left: -14px;
  }

  .experience-card {
    padding:
      22px
      18px;
  }

  .experience-role h3 {
    font-size:
      1.2rem;
  }

  .experience-role h4 {
    font-size:
      0.9rem;
  }

  .experience-date-text strong {
    font-size:
      0.68rem;
  }
}

</style>