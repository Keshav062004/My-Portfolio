<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

/* =========================================================
   CERTIFICATIONS DATA
========================================================= */

const certifications = [
  {
    name: "Data Analytics Job Simulation",
    issuer: "Deloitte",
    date: "Oct 2026",
    icon: "/certificate-icons/deloitte.png",
    pdf: "/certificates/deloitte-data-analytics.pdf",
  },
  {
    name: "AWS Partner: Sales Accreditation",
    issuer: "Amazon Web Services",
    date: "Dec 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-sales.pdf",
  },
  {
    name: "AWS Partner: Generative AI Essentials",
    issuer: "Amazon Web Services",
    date: "Feb 2026",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-genai.pdf",
  },
  {
    name: "AWS Partner: Accreditation (Technical)",
    issuer: "Amazon Web Services",
    date: "Dec 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-technical.pdf",
  },
  {
    name: "Introduction to Python",
    issuer: "Coding Ninjas",
    date: "Jan 2023",
    icon: "/certificate-icons/coding-ninjas.png",
    pdf: "/certificates/python.pdf",
  },
  {
    name: "IBM Applied DevOps Engineering",
    issuer: "IBM",
    date: "Feb 2025",
    icon: "/certificate-icons/ibm.png",
    pdf: "/certificates/ibm-devops.pdf",
  },
];

const deloitteIconFailed = ref(false);

const handleCertificateIconError = (name: string) => {
  if (name === "Data Analytics Job Simulation") {
    deloitteIconFailed.value = true;
  }
};

/* =========================================================
   VIEW ALL CREDENTIALS
========================================================= */

const viewAllCredentials = () => {
  if (typeof window === "undefined") return;

  // Navigate inside the SPA so Vercel is not asked to serve a new
  // document at /certificates (which can otherwise produce a 404).
  window.history.pushState({}, "", "/certificates");
  window.dispatchEvent(new Event("route-change"));
  window.scrollTo({ top: 0, behavior: "auto" });
};

/* =========================================================
   CERTIFICATE PDF VIEWER
========================================================= */

const activeCertificate = ref<string | null>(null);
const activeCertificateName = ref("");
const certificateMaximized = ref(false);
const certificateMinimized = ref(false);

const openCertificate = (pdf: string, name: string) => {
  // Chrome mobile can block PDFs embedded in a modal. Let its native viewer
  // handle the file in a top-level tab on narrow screens instead.
  if (typeof window !== "undefined" && window.matchMedia("(max-width: 767px)").matches) {
    window.open(pdf, "_blank", "noopener,noreferrer");
    return;
  }

  // Keep Chromium's native PDF toolbar visible on desktop.
  activeCertificate.value =
    `${pdf}#toolbar=1&navpanes=0&scrollbar=1&view=Fit&page=1`;

  activeCertificateName.value = name;

  certificateMaximized.value = false;
  certificateMinimized.value = false;

  document.body.style.overflow = "hidden";
  document.body.classList.add(
    "certificate-view-open"
  );
};

const closeCertificate = () => {
  activeCertificate.value = null;
  activeCertificateName.value = "";

  certificateMaximized.value = false;
  certificateMinimized.value = false;

  document.body.style.overflow = "";
  document.body.classList.remove(
    "certificate-view-open"
  );
};

const toggleCertificateMaximize = () => {
  certificateMaximized.value =
    !certificateMaximized.value;

  certificateMinimized.value = false;
};

const toggleCertificateMinimize = () => {
  certificateMinimized.value =
    !certificateMinimized.value;

  if (certificateMinimized.value) {
    certificateMaximized.value = false;
  }
};

const handleCertificateKeydown = (
  event: KeyboardEvent
) => {
  if (
    event.key === "Escape" &&
    activeCertificate.value
  ) {
    closeCertificate();
  }
};

onMounted(() => {
  window.addEventListener(
    "keydown",
    handleCertificateKeydown
  );
});

onUnmounted(() => {
  window.removeEventListener(
    "keydown",
    handleCertificateKeydown
  );

  document.body.style.overflow = "";

  document.body.classList.remove(
    "certificate-view-open"
  );
});


/* =========================================================
   GSAP
========================================================= */

let ctx: gsap.Context | null = null;

onMounted(() => {
  const section =
    document.querySelector(".certifications");

  if (!section) return;

  ctx = gsap.context(() => {
    const eyebrow =
      section.querySelector(
        ".certifications-eyebrow"
      );

    const title =
      section.querySelector(
        ".certifications-title"
      );

    const line =
      section.querySelector(
        ".certifications-line"
      );

    const description =
      section.querySelector(
        ".certifications-description"
      );

    const cards =
      gsap.utils.toArray<HTMLElement>(
        ".certification-card"
      );

    const artwork =
      section.querySelector(
        ".certifications-artwork"
      );

    const button =
      section.querySelector(
        ".certifications-button"
      );

    /* INITIAL STATES */

    gsap.set(eyebrow, {
      opacity: 0,
      x: 50,
    });

    gsap.set(title, {
      opacity: 0,
      y: 80,
      rotateX: -20,
      transformPerspective: 1000,
    });

    gsap.set(line, {
      opacity: 0,
      scaleX: 0,
      transformOrigin: "left center",
    });

    gsap.set(description, {
      opacity: 0,
      y: 35,
    });

    gsap.set(cards, {
      opacity: 0,
      y: 70,
      scale: 0.96,
    });

    gsap.set(artwork, {
      opacity: 0,
      x: 100,
      scale: 0.8,
      rotate: -8,
    });

    gsap.set(button, {
      opacity: 0,
      y: 45,
      scale: 0.82,
    });

    /* MAIN TIMELINE */

    const timeline = gsap.timeline({
      scrollTrigger: {
        trigger: section,
        start: "top 70%",
        end: "top 20%",
        scrub: 1,
        invalidateOnRefresh: true,
      },
    });

    timeline.to(eyebrow, {
      opacity: 1,
      x: 0,
      duration: 0.7,
      ease: "power3.out",
    });

    timeline.to(
      title,
      {
        opacity: 1,
        y: 0,
        rotateX: 0,
        duration: 1,
        ease: "power4.out",
      },
      "-=0.35"
    );

    timeline.to(
      line,
      {
        opacity: 1,
        scaleX: 1,
        duration: 0.7,
        ease: "power3.out",
      },
      "-=0.5"
    );

    timeline.to(
      description,
      {
        opacity: 1,
        y: 0,
        duration: 0.7,
        ease: "power3.out",
      },
      "-=0.35"
    );

    timeline.to(
      cards,
      {
        opacity: 1,
        y: 0,
        scale: 1,
        duration: 0.3,
        stagger: {
          each: 0.6,
          from: "start",
        },
        ease: "power3.out",
      },
      "-=0.25"
    );

    timeline.to(
      artwork,
      {
        opacity: 1,
        x: 0,
        scale: 1,
        rotate: 0,
        duration: 1.1,
        ease: "power3.out",
      },
      "-=0.7"
    );

    timeline.to(
      button,
      {
        opacity: 1,
        y: 0,
        scale: 1,
        duration: 0.75,
        ease: "back.out(1.8)",
      },
      "-=0.35"
    );

    /* ORBIT */

    gsap.to(
      ".certifications-orbit",
      {
        rotation: "+=360",
        duration: 22,
        repeat: -1,
        ease: "none",
      }
    );

    /* FLOATING MEDAL */

    gsap.to(
      ".certifications-medal",
      {
        y: -12,
        rotate: 1,
        duration: 2.8,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
      }
    );

    gsap.to(
      ".medal-document",
      {
        y: -4,
        duration: 2.8,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
      }
    );

    gsap.to(
      ".certificate-particle",
      {
        y: -12,
        opacity: 0.35,
        duration: 2.5,
        stagger: 0.35,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
      }
    );

    ScrollTrigger.refresh();
  }, section);
});

onUnmounted(() => {
  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <section
    id="certifications"
    class="certifications"
  >

    <!-- BACKGROUND PARTICLES -->

    <div class="background-particles">
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
    </div>

    <div class="certifications-container">

      <!-- LEFT -->

      <div class="certifications-left">

        <div class="certifications-grid">

          <article
            v-for="cert in certifications"
            :key="cert.name"
            class="certification-card"
          >

            <div
              class="certification-card-inner"
            >

              <div
                class="certification-icon"
              >
                <img
                  v-if="cert.name !== 'Data Analytics Job Simulation' || !deloitteIconFailed"
                  :src="cert.icon"
                  :alt="`${cert.issuer} logo`"
                  class="certification-logo"
                  @error="handleCertificateIconError(cert.name)"
                />
                <span
                  v-else
                  class="certification-logo-fallback"
                  aria-label="Deloitte"
                >
                  D
                </span>
              </div>

              <h3>
                {{ cert.name }}
              </h3>

              <p
                class="certification-issuer"
              >
                {{ cert.issuer }}
              </p>

              <div
                v-if="cert.date"
                class="certification-date"
              >
                <span>▣</span>
                {{ cert.date }}
              </div>

              <div
                class="certification-card-line"
              ></div>

              <button
                class="certification-link"
                type="button"
                @click="
                  openCertificate(cert.pdf, cert.name)
                "
              >
                <span>
                  View Credential
                </span>

                <span
                  class="link-arrow"
                >
                  ↗
                </span>
              </button>

            </div>

          </article>

        </div>

        <!-- =================================================
             VIEW ALL BUTTON
        ================================================== -->

        <button
          class="certifications-button"
          type="button"
          @click="viewAllCredentials"
        >

          <span class="button-text">
            View All Credentials
          </span>

          <span class="button-arrow">
            ↗
          </span>

        </button>

      </div>

      <!-- RIGHT SIDE -->

      <div
        class="certifications-content"
      >

        <div
          class="certifications-eyebrow"
        >
          MY ACHIEVEMENTS
        </div>

        <h2
          class="certifications-title"
        >
          Certifications<span>.</span>
        </h2>

        <div
          class="certifications-line"
        >
          <span></span>
        </div>

        <p
          class="certifications-description"
        >
          Industry-recognized certifications
          that strengthen my expertise and
          validate my skills across cloud,
          technology and emerging solutions.
        </p>

        <!-- ARTWORK -->

        <div
          class="certifications-artwork"
        >

          <span
            class="
              certificate-particle
              particle-one
            "
          ></span>

          <span
            class="
              certificate-particle
              particle-two
            "
          ></span>

          <span
            class="
              certificate-particle
              particle-three
            "
          ></span>

          <span
            class="
              certificate-particle
              particle-four
            "
          ></span>

          <div
            class="
              certifications-orbit
              orbit-one
            "
          ></div>

          <div
            class="
              certifications-orbit
              orbit-two
            "
          ></div>

          <div
            class="
              certifications-orbit
              orbit-three
            "
          ></div>

          <div
            class="certifications-medal"
          >

            <div
              class="medal-document"
            >

              <div
                class="document-header"
              >
                CERTIFICATE
              </div>

              <div
                class="
                  document-line
                  document-line-one
                "
              ></div>

              <div
                class="
                  document-line
                  document-line-two
                "
              ></div>

              <div
                class="
                  document-line
                  document-line-three
                "
              ></div>

              <div
                class="
                  document-line
                  document-line-four
                "
              ></div>

              <div
                class="medal-badge"
              >
                <span>★</span>
              </div>

            </div>

            <div
              class="
                medal-ribbon
                ribbon-left
              "
            ></div>

            <div
              class="
                medal-ribbon
                ribbon-right
              "
            ></div>

          </div>

          <div
            class="certifications-platform"
          >

            <div
              class="platform-top"
            ></div>

            <div
              class="platform-body"
            ></div>

            <div
              class="platform-bottom"
            ></div>

          </div>

        </div>

      </div>

    </div>

    <!-- =====================================================
         CERTIFICATE PDF VIEWER
    ====================================================== -->

    <Teleport to="body">

      <Transition
        name="certificate-modal"
      >

        <div
          v-if="activeCertificate"
          class="certificate-modal"
          :class="{
            'certificate-modal-minimized':
              certificateMinimized
          }"
          role="dialog"
          aria-modal="true"
          aria-label="Certificate viewer"
          @click.self="closeCertificate"
        >

          <!-- =================================================
               MINIMIZED
          ================================================== -->

          <div
            v-if="certificateMinimized"
            class="certificate-minimized-bar"
          >

            <div class="certificate-viewer-left">

              <span
                class="certificate-viewer-small-icon"
              >
                ✓
              </span>

              <span class="certificate-viewer-title">
                {{ activeCertificateName }}
              </span>

            </div>

            <div class="certificate-viewer-controls">

              <button
                class="certificate-viewer-control"
                type="button"
                aria-label="Restore certificate"
                title="Restore"
                @click="toggleCertificateMinimize"
              >
                □
              </button>

              <button
                class="
                  certificate-viewer-control
                  certificate-viewer-close
                "
                type="button"
                aria-label="Close certificate viewer"
                title="Close"
                @click="closeCertificate"
              >
                ×
              </button>

            </div>

          </div>

          <!-- =================================================
               FULL VIEWER
          ================================================== -->

          <div
            v-else
            class="certificate-modal-panel"
            :class="{
              'certificate-modal-panel-maximized':
                certificateMaximized
            }"
          >

            <div class="certificate-viewer-toolbar">

              <div class="certificate-viewer-left">

                <span
                  class="certificate-viewer-small-icon"
                >
                  ✓
                </span>

                <span class="certificate-viewer-title">
                  {{ activeCertificateName }}
                </span>

              </div>

              <div class="certificate-viewer-controls">

                <button
                  class="certificate-viewer-control"
                  type="button"
                  aria-label="Minimize certificate"
                  title="Minimize"
                  @click="toggleCertificateMinimize"
                >
                  −
                </button>

                <button
                  class="certificate-viewer-control"
                  type="button"
                  :aria-label="
                    certificateMaximized
                      ? 'Restore certificate size'
                      : 'Maximize certificate'
                  "
                  :title="
                    certificateMaximized
                      ? 'Restore'
                      : 'Maximize'
                  "
                  @click="toggleCertificateMaximize"
                >
                  {{
                    certificateMaximized
                      ? "❐"
                      : "□"
                  }}
                </button>

                <button
                  class="
                    certificate-viewer-control
                    certificate-viewer-close
                  "
                  type="button"
                  aria-label="Close certificate viewer"
                  title="Close"
                  @click="closeCertificate"
                >
                  ×
                </button>

              </div>

            </div>

            <object
              v-if="activeCertificate"
              :data="activeCertificate"
              type="application/pdf"
              class="certificate-pdf-frame"
              aria-label="Certificate PDF"
            >
              <iframe
                :src="activeCertificate"
                class="certificate-pdf-frame-fallback"
                title="Certificate"
              ></iframe>
            </object>

          </div>

        </div>

      </Transition>

    </Teleport>

  </section>
</template>

<style scoped lang="scss">

.certifications {
  position: relative;
  width: 100%;
  min-height: 100vh;
  margin-top: -90px;
  padding: 65px 5vw 90px;
  overflow: hidden;

  background:
    radial-gradient(
      circle at 80% 45%,
      rgba(255, 167, 65, 0.15),
      transparent 32%
    ),
    radial-gradient(
      circle at 15% 85%,
      rgba(255, 138, 0, 0.06),
      transparent 30%
    ),
    linear-gradient(
      135deg,
      #fbf5ea 0%,
      #f7eee1 48%,
      #fffaf2 100%
    );

  color: #171717;
}

.certifications::before {
  content: "";
  position: absolute;
  inset: 0;

  background-image:
    linear-gradient(
      rgba(100, 78, 48, 0.045) 1px,
      transparent 1px
    ),
    linear-gradient(
      90deg,
      rgba(100, 78, 48, 0.045) 1px,
      transparent 1px
    );

  background-size: 70px 70px;
  pointer-events: none;
}

.background-particles {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}

.background-particles span {
  position: absolute;
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: #ff9b26;
  opacity: 0.35;

  animation:
    background-float
    5s
    ease-in-out
    infinite
    alternate;
}

.background-particles span:nth-child(1) {
  top: 15%;
  left: 8%;
}

.background-particles span:nth-child(2) {
  top: 28%;
  left: 30%;
  animation-delay: 0.7s;
}

.background-particles span:nth-child(3) {
  top: 70%;
  left: 18%;
  animation-delay: 1.3s;
}

.background-particles span:nth-child(4) {
  top: 30%;
  right: 15%;
  animation-delay: 2s;
}

.background-particles span:nth-child(5) {
  bottom: 18%;
  right: 8%;
  animation-delay: 2.8s;
}

@keyframes background-float {
  from {
    transform:
      translateY(0)
      scale(0.8);
    opacity: 0.25;
  }

  to {
    transform:
      translateY(-15px)
      scale(1.2);
    opacity: 0.65;
  }
}

.certifications-container {
  position: relative;
  z-index: 2;

  max-width: 1500px;
  margin: 0 auto;

  display: grid;

  grid-template-columns:
    minmax(0, 1.35fr)
    minmax(360px, 0.85fr);

  gap: 70px;
  align-items: center;
}

.certifications-left {
  width: 100%;

  display: flex;
  flex-direction: column;
  align-items: center;
}

.certifications-grid {
  width: 100%;

  display: grid;

  grid-template-columns:
    repeat(3, minmax(0, 1fr));

  gap: 18px;

  perspective: 1200px;
}

.certification-card {
  min-height: 285px;
  padding: 1px;
  position: relative;
  border-radius: 18px;

  background:
    linear-gradient(
      135deg,
      rgba(255, 138, 0, 0.52),
      rgba(255, 255, 255, 0.90) 45%,
      rgba(255, 138, 0, 0.22)
    );

  box-shadow:
    0 12px 30px
    rgba(91, 65, 35, 0.08);

  transform:
    translateY(0);

  transition:
    transform 0.35s
    cubic-bezier(.2,.8,.2,1),
    box-shadow 0.35s ease,
    background 0.35s ease;
}

.certification-card:hover {
  transform:
    translateY(-7px);

  background:
    linear-gradient(
      135deg,
      #ff8a00,
      rgba(255, 255, 255, 0.96) 45%,
      rgba(255, 138, 0, 0.35)
    );

  box-shadow:
    0 20px 42px
    rgba(91, 65, 35, 0.14),
    0 0 25px
    rgba(255, 138, 0, 0.10);
}

.certification-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  min-height: 283px;

  padding: 23px 22px;

  display: flex;
  flex-direction: column;

  border-radius: 17px;
  overflow: hidden;

  background:
    radial-gradient(
      circle at 100% 0%,
      rgba(255, 138, 0, 0.09),
      transparent 35%
    ),
    linear-gradient(
      145deg,
      #fffdf8,
      #f7eee2
    );

  border:
    1px solid
    rgba(255, 255, 255, 0.85);

  box-shadow:
    inset 0 1px 0
    rgba(255,255,255,0.95);
}

.certification-card-inner::after {
  content: "";

  position: absolute;
  top: 0;
  left: -120%;

  width: 70%;
  height: 100%;

  pointer-events: none;

  background:
    linear-gradient(
      110deg,
      transparent,
      rgba(255,255,255,0.42),
      transparent
    );

  transform:
    skewX(-18deg);

  transition:
    left 0.65s ease;
}

.certification-card:hover
.certification-card-inner::after {
  left: 140%;
}

.certification-icon {
  width: 58px;
  height: 58px;

  display: flex;
  align-items: center;
  justify-content: center;

  margin-bottom: 19px;

  border-radius: 15px;

  background:
    linear-gradient(
      145deg,
      #fffefa,
      #f4e7d5
    );

  border:
    1px solid
    rgba(255, 138, 0, 0.20);

  box-shadow:
    0 8px 18px
    rgba(80, 58, 30, 0.06),

    inset 0 0 20px
    rgba(255, 138, 0, 0.035);

  transition:
    transform 0.35s
    cubic-bezier(.2,.8,.2,1),
    box-shadow 0.35s ease;
}

.certification-card:hover
.certification-icon {
  transform:
    translateY(-2px)
    scale(1.07)
    rotate(-3deg);

  box-shadow:
    0 10px 22px
    rgba(255, 138, 0, 0.12),

    inset 0 0 20px
    rgba(255, 138, 0, 0.05);
}

.certification-logo {
  width: 42px;
  height: 42px;
  object-fit: contain;
  display: block;
}

.certification-logo-fallback {
  width: 43px;
  height: 43px;
  display: grid;
  place-items: center;
  border-radius: 12px;
  color: #292622;
  background: rgba(255, 255, 255, 0.9);
  font-size: 24px;
  font-weight: 800;
}

.certification-card h3 {
  margin: 0;

  font-size: 17px;
  line-height: 1.35;
  font-weight: 700;

  color: #1c1b1a;

  transition:
    transform 0.35s ease,
    color 0.35s ease;
}

.certification-card:hover h3 {
  transform:
    translateX(2px);

  color: #e87500;
}

.certification-issuer {
  margin-top: 12px;

  font-size: 13px;

  color: #81796e;
}

.certification-date {
  display: flex;
  align-items: center;

  gap: 8px;

  margin-top: auto;
  padding-top: 16px;

  font-size: 13px;

  color: #665f56;
}

.certification-date span {
  color: #ff8a00;
}

.certification-card-line {
  width: 100%;
  height: 1px;

  margin-top: 17px;

  background:
    linear-gradient(
      90deg,
      rgba(255, 138, 0, 0.42),
      rgba(100, 80, 55, 0.10)
    );
}

.certification-link {
  appearance: none;
  -webkit-appearance: none;

  display: flex;

  align-items: center;
  justify-content: space-between;

  width: 100%;
  min-height: 44px;

  margin-top: 14px;
  padding: 0;

  border: 0;

  background: transparent;

  color: #ed7900;

  font-size: 13px;
  font-weight: 700;

  position: relative;

  overflow: hidden;

  cursor: pointer;

  text-align: left;

  transition:
    transform 0.25s ease,
    color 0.25s ease;
}

.certification-link:hover {
  color: #ff8a00;
}

.link-arrow {
  font-size: 19px;

  transition:
    transform 0.3s ease;
}

.certification-link:hover
.link-arrow {
  transform:
    translate(2px, -2px)
    scale(1.08);
}

.certifications-content {
  position: relative;

  min-height: 620px;

  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.certifications-eyebrow {
  margin-top: 10px;

  color: #ff8a00;

  font-size: 15px;
  font-weight: 700;

  letter-spacing: 0.18em;
}

.certifications-title {
  margin: 12px 0 0;

  font-size:
    clamp(55px, 5vw, 90px);

  line-height: 0.95;

  letter-spacing: -0.05em;

  font-weight: 800;

  color: #161719;
}

.certifications-title span {
  color: #ff8a00;
}

.certifications-line {
  width: 170px;
  height: 2px;

  margin-top: 30px;

  position: relative;

  background:
    linear-gradient(
      90deg,
      #ff8a00,
      rgba(255, 138, 0, 0.08)
    );
}

.certifications-line span {
  position: absolute;

  width: 7px;
  height: 7px;

  right: 0;
  top: -3px;

  border-radius: 50%;

  background: #ff8a00;

  box-shadow:
    0 0 15px
    rgba(255, 138, 0, 0.45);
}

.certifications-description {
  max-width: 430px;

  margin-top: 30px;

  font-size: 17px;

  line-height: 1.7;

  color: #756e65;
}

.certifications-artwork {
  position: relative;

  width: 100%;
  height: 315px;

  margin-top: 8px;

  display: flex;

  align-items: center;
  justify-content: center;
}

.certifications-orbit {
  position: absolute;

  width: 330px;
  height: 180px;

  border:
    1px dashed
    rgba(255, 151, 42, 0.42);

  border-radius: 50%;

  transform:
    rotate(-15deg);

  box-shadow:
    0 0 10px
    rgba(255, 138, 0, 0.06);

  opacity: 0.9;
}

.orbit-two {
  width: 280px;
  height: 150px;

  transform:
    rotate(20deg);

  border-color:
    rgba(255, 177, 70, 0.32);

  opacity: 0.8;
}

.orbit-three {
  width: 390px;
  height: 220px;

  transform:
    rotate(5deg);

  border-color:
    rgba(255, 153, 35, 0.25);

  opacity: 0.7;
}

.certificate-particle {
  position: absolute;

  width: 6px;
  height: 6px;

  border-radius: 50%;

  background: #ff9b26;

  box-shadow:
    0 0 8px
    rgba(255, 138, 0, 0.3);

  z-index: 5;
}

.particle-one {
  top: 16%;
  left: 12%;
}

.particle-two {
  top: 28%;
  right: 8%;
}

.particle-three {
  bottom: 28%;
  left: 8%;
}

.particle-four {
  top: 7%;
  right: 28%;

  width: 4px;
  height: 4px;
}

.certifications-medal {
  position: relative;

  width: 220px;
  height: 155px;

  z-index: 4;

  transform-style:
    preserve-3d;

  filter:
    drop-shadow(
      0 18px 25px
      rgba(87, 62, 31, 0.14)
    );
}

.medal-document {
  position: absolute;

  width: 175px;
  height: 115px;

  left: 20px;
  top: 8px;

  padding: 20px 18px;

  border:
    2px solid
    rgba(255, 174, 79, 0.55);

  border-radius: 8px;

  background:
    linear-gradient(
      145deg,
      #fffdf9,
      #f1e3d0
    );

  box-shadow:
    0 12px 25px
    rgba(75, 55, 30, 0.12),

    inset 0 1px 0
    rgba(255,255,255,0.95);

  transform:
    perspective(600px)
    rotateY(-8deg);

  transform-style:
    preserve-3d;
}

.document-header {
  margin-bottom: 12px;

  font-size: 8px;

  font-weight: 800;

  letter-spacing: 0.15em;

  color: #c47a20;
}

.document-line {
  height: 5px;

  margin-bottom: 9px;

  border-radius: 10px;

  background:
    rgba(111, 91, 67, 0.20);
}

.document-line-one {
  width: 65%;
}

.document-line-two {
  width: 45%;
}

.document-line-three {
  width: 55%;
}

.document-line-four {
  width: 40%;
}

.medal-badge {
  position: absolute;

  right: -30px;
  bottom: -23px;

  width: 74px;
  height: 74px;

  display: flex;

  align-items: center;
  justify-content: center;

  border-radius: 50%;

  border:
    6px solid
    #fff3df;

  background:
    radial-gradient(
      circle at 35% 28%,
      #ffd07c 0%,
      #ff9b25 48%,
      #ed7100 100%
    );

  color: white;

  font-size: 30px;

  box-shadow:
    0 9px 22px
    rgba(255, 138, 0, 0.28),

    inset 0 2px 5px
    rgba(255,255,255,0.4);
}

.medal-badge::before {
  content: "";

  position: absolute;

  inset: 8px;

  border:
    1px solid
    rgba(255,255,255,0.5);

  border-radius: 50%;
}

.medal-badge span {
  position: relative;
  z-index: 2;
}

.medal-ribbon {
  position: absolute;

  bottom: -27px;

  width: 29px;
  height: 63px;

  background:
    linear-gradient(
      180deg,
      #ff9a1f,
      #ed7200
    );

  z-index: -1;

  box-shadow:
    0 6px 12px
    rgba(255, 138, 0, 0.12);
}

.ribbon-left {
  left: 65px;

  transform:
    rotate(8deg);
}

.ribbon-right {
  left: 91px;

  transform:
    rotate(-8deg);
}

.certifications-platform {
  position: absolute;

  left: 50%;
  bottom: 28px;

  width: 285px;
  height: 58px;

  transform:
    translateX(-50%);

  z-index: 2;

  border-radius: 50%;

  background:
    linear-gradient(
      180deg,
      #fffefa 0%,
      #f7ead7 48%,
      #decab0 100%
    );

  border:
    1px solid
    rgba(207, 166, 108, 0.55);

  box-shadow:
    0 16px 25px
    rgba(79, 56, 29, 0.12),

    0 5px 10px
    rgba(255, 138, 0, 0.07),

    inset 0 5px 9px
    rgba(255,255,255,0.95);

  overflow: visible;
}

.platform-top {
  position: absolute;

  left: 50%;
  top: -12px;

  width: 285px;
  height: 38px;

  transform:
    translateX(-50%);

  border-radius: 50%;

  background:
    radial-gradient(
      ellipse at center 35%,
      #fffefa 0%,
      #f9eddb 58%,
      #e6d2b5 100%
    );

  border:
    1px solid
    rgba(205, 164, 108, 0.48);

  box-shadow:
    inset 0 4px 9px
    rgba(255,255,255,0.95),

    0 4px 8px
    rgba(86, 61, 32, 0.07);

  z-index: 3;
}

.platform-body {
  position: absolute;

  left: 50%;
  top: 5px;

  width: 280px;
  height: 40px;

  transform:
    translateX(-50%);

  border-radius:
    0 0 50% 50%;

  background:
    linear-gradient(
      180deg,
      #f3e4cf,
      #dfcbb0
    );

  z-index: 1;
}

.platform-bottom {
  position: absolute;

  left: 50%;
  bottom: -12px;

  width: 245px;
  height: 25px;

  transform:
    translateX(-50%);

  border-radius:
    0 0 50% 50%;

  background:
    linear-gradient(
      180deg,
      #e3d1b8,
      #cbb498
    );

  box-shadow:
    0 8px 14px
    rgba(70, 50, 27, 0.13);

  z-index: -1;
}

.certifications-artwork::after {
  content: "";

  position: absolute;

  left: 50%;
  bottom: 5px;

  width: 310px;
  height: 28px;

  transform:
    translateX(-50%);

  border-radius: 50%;

  background:
    rgba(91, 64, 31, 0.11);

  filter: blur(15px);

  z-index: 0;

  pointer-events: none;
}

/* =========================================================
   VIEW ALL BUTTON
========================================================= */

.certifications-button {
  position: relative;

  align-self: center;

  display: flex;

  align-items: center;
  justify-content: center;

  gap: 18px;

  margin-top: 28px;

  padding: 15px 28px;

  min-width: 220px;

  border:
    1px solid
    rgba(255, 138, 0, 0.75);

  border-radius: 100px;

  background:
    rgba(255, 250, 242, 0.75);

  color: #ed7900;

  font-size: 14px;

  font-weight: 700;

  letter-spacing: 0.02em;

  cursor: pointer;

  overflow: hidden;

  box-shadow:
    0 5px 18px
    rgba(89, 63, 32, 0.06),

    0 0 18px
    rgba(255, 138, 0, 0.06);

  transition:
    background 0.35s ease,
    color 0.35s ease,
    border-color 0.35s ease,
    box-shadow 0.35s ease,
    transform 0.35s ease;

  animation:
    credential-pulse
    2.8s
    ease-in-out
    infinite;
}

.certifications-button::before {
  content: "";

  position: absolute;

  top: 0;
  left: -120%;

  width: 80%;
  height: 100%;

  background:
    linear-gradient(
      100deg,
      transparent,
      rgba(255,255,255,0.35),
      transparent
    );

  transform:
    skewX(-20deg);

  animation:
    credential-shine
    4.5s
    ease-in-out
    infinite;

  pointer-events: none;
}

.certifications-button::after {
  content: "";

  position: absolute;

  left: 15%;
  right: 15%;

  bottom: -10px;

  height: 15px;

  background:
    rgba(255, 138, 0, 0.28);

  filter: blur(14px);

  opacity: 0.35;

  pointer-events: none;
}

.button-text {
  position: relative;
  z-index: 2;
}

.button-arrow {
  position: relative;

  z-index: 2;

  display: inline-flex;

  font-size: 19px;

  line-height: 1;

  transition:
    transform 0.35s
    cubic-bezier(
      0.2,
      0.8,
      0.2,
      1
    );
}

.certifications-button:hover {
  color: #fff;

  background:
    linear-gradient(
      120deg,
      #ff8a00,
      #ffad32,
      #ff8a00
    );

  border-color:
    #ffad32;

  box-shadow:
    0 8px 30px
    rgba(255, 138, 0, 0.28),

    0 0 45px
    rgba(255, 138, 0, 0.15);

  transform:
    translateY(-5px)
    scale(1.025);

  animation-play-state: paused;
}

.certifications-button:hover
.button-arrow {
  transform:
    translate(5px, -4px)
    rotate(-5deg);
}

.certifications-button:active {
  transform:
    translateY(-1px)
    scale(0.98);
}

@keyframes credential-pulse {
  0% {
    box-shadow:
      0 5px 18px
      rgba(89, 63, 32, 0.06),

      0 0 15px
      rgba(255, 138, 0, 0.04);
  }

  50% {
    box-shadow:
      0 7px 22px
      rgba(89, 63, 32, 0.08),

      0 0 28px
      rgba(255, 138, 0, 0.16);
  }

  100% {
    box-shadow:
      0 5px 18px
      rgba(89, 63, 32, 0.06),

      0 0 15px
      rgba(255, 138, 0, 0.04);
  }
}

@keyframes credential-shine {
  0% {
    left: -120%;
  }

  30% {
    left: 140%;
  }

  100% {
    left: 140%;
  }
}

/* =========================================================
   CERTIFICATE VIEWER
========================================================= */

:global(body.certificate-view-open) {
  overflow: hidden !important;
}

:global(body.certificate-view-open header),
:global(body.certificate-view-open nav),
:global(body.certificate-view-open [class*="header"]),
:global(body.certificate-view-open [class*="navbar"]),
:global(body.certificate-view-open [class*="navigation"]),
:global(body.certificate-view-open [class*="resume"]),
:global(body.certificate-view-open [class*="sound"]),
:global(body.certificate-view-open [class*="audio"]) {
  visibility: hidden !important;
  opacity: 0 !important;
  pointer-events: none !important;
  z-index: -1 !important;
}

.certificate-modal {
  position: fixed;

  inset: 0;

  z-index: 999999;

  display: flex;

  align-items: center;
  justify-content: center;

  background:
    rgba(10, 10, 10, 0.84);

  backdrop-filter:
    blur(14px);

  -webkit-backdrop-filter:
    blur(14px);
}

.certificate-modal-panel {
  position: relative;

  display: flex;

  flex-direction: column;

  width:
    min(1200px, 96vw);

  height:
    min(94vh, 920px);

  overflow: hidden;

  border:
    1px solid
    rgba(255, 138, 0, 0.45);

  border-radius: 16px;

  background: #ffffff;

  box-shadow:
    0 35px 100px
    rgba(0, 0, 0, 0.5),

    0 0 0 1px
    rgba(255, 255, 255, 0.22)
    inset;

  transition:
    width 0.35s ease,
    height 0.35s ease,
    border-radius 0.35s ease;
}

.certificate-modal-panel-maximized {
  width: 100vw;

  height: 100vh;

  border-radius: 0;
}

/* =========================================================
   CERTIFICATE VIEWER TOOLBAR
   Exact viewer structure used by CertificatesPage.
========================================================= */

.certificate-viewer-toolbar {
  position: relative;

  z-index: 2;

  flex: 0 0 54px;

  display: flex;

  align-items: center;

  justify-content: space-between;

  padding:
    0
    12px
    0
    20px;

  background:
    linear-gradient(
      135deg,
      #202020,
      #111111
    );

  color: #ffffff;

  border-bottom:
    1px solid
    rgba(255, 138, 0, 0.35);
}

.certificate-viewer-left {
  display: flex;

  align-items: center;

  min-width: 0;

  gap: 10px;
}

.certificate-viewer-small-icon {
  width: 25px;
  height: 25px;

  display: flex;

  align-items: center;
  justify-content: center;

  flex: 0 0 25px;

  border-radius: 50%;

  background:
    #ff8a00;

  color: #ffffff;

  font-size: 13px;

  font-weight: 900;
}

.certificate-viewer-title {
  max-width: 650px;

  overflow: hidden;

  text-overflow: ellipsis;

  white-space: nowrap;

  font-size: 14px;

  font-weight: 600;

  letter-spacing: 0.02em;
}

.certificate-viewer-controls {
  display: flex;

  align-items: center;

  gap: 3px;

  flex: 0 0 auto;
}

.certificate-viewer-control {
  width: 38px;
  height: 34px;

  display: flex;

  align-items: center;
  justify-content: center;

  border: 0;

  border-radius: 6px;

  background: transparent;

  color: #ffffff;

  font-size: 20px;

  line-height: 1;

  cursor: pointer;

  transition:
    background 0.2s ease,
    color 0.2s ease,
    transform 0.2s ease;
}

.certificate-viewer-control:hover {
  background:
    rgba(255, 138, 0, 0.22);

  color: #ff9b26;

  transform:
    scale(1.05);
}

.certificate-viewer-close {
  font-size: 25px;
}

.certificate-viewer-close:hover {
  background: #ff8a00;

  color: #ffffff;

  transform:
    rotate(90deg)
    scale(1.05);
}

/* =========================================================
   PDF AREA
========================================================= */

.certificate-pdf-frame {
  display: block;

  flex: 1 1 auto;

  width: 100%;
  height: 100%;

  min-width: 0;
  min-height: 0;

  border: 0;

  background: #ffffff;

  overflow: hidden;
}

.certificate-pdf-frame-fallback {
  display: block;

  width: 100%;
  height: 100%;

  min-width: 0;
  min-height: 0;

  border: 0;

  background: #ffffff;
}

/* =========================================================
   MINIMIZED
========================================================= */

.certificate-minimized-bar {
  position: fixed;

  right: 24px;

  bottom: 24px;

  z-index: 1000000;

  display: flex;

  align-items: center;

  justify-content: space-between;

  min-width: 300px;

  max-width: 500px;

  height: 55px;

  padding:
    0
    8px
    0
    16px;

  border:
    1px solid
    rgba(255, 138, 0, 0.55);

  border-radius: 10px;

  background:
    linear-gradient(
      135deg,
      #202020,
      #111111
    );

  color: #ffffff;

  box-shadow:
    0 18px 45px
    rgba(0, 0, 0, 0.35);
}

.certificate-modal-minimized {
  align-items: flex-end;

  justify-content: flex-end;

  pointer-events: none;

  background:
    rgba(10, 10, 10, 0.08);

  backdrop-filter: none;

  -webkit-backdrop-filter: none;
}

.certificate-modal-minimized
.certificate-minimized-bar {
  pointer-events: auto;
}

/* =========================================================
   TRANSITION
========================================================= */

.certificate-modal-enter-active,
.certificate-modal-leave-active {
  transition:
    opacity 0.25s ease;
}

.certificate-modal-enter-active
.certificate-modal-panel,
.certificate-modal-leave-active
.certificate-modal-panel,
.certificate-modal-enter-active
.certificate-minimized-bar,
.certificate-modal-leave-active
.certificate-minimized-bar {
  transition:
    transform 0.3s ease,
    opacity 0.3s ease;
}

.certificate-modal-enter-from,
.certificate-modal-leave-to {
  opacity: 0;
}

.certificate-modal-enter-from
.certificate-modal-panel,
.certificate-modal-leave-to
.certificate-modal-panel {
  opacity: 0;

  transform:
    translateY(25px)
    scale(0.97);
}

.certificate-modal-enter-from
.certificate-minimized-bar,
.certificate-modal-leave-to
.certificate-minimized-bar {
  opacity: 0;

  transform:
    translateY(20px)
    scale(0.96);
}

@media (max-width: 1200px) {
  .certifications-container {
    gap: 45px;
  }

  .certifications-grid {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 900px) {
  .certifications {
    margin-top: -25px;

    padding:
      80px 25px;
  }

  .certifications-container {
    grid-template-columns: 1fr;
  }

  .certifications-content {
    order: -1;

    min-height: auto;
  }

  .certifications-artwork {
    min-height: 330px;
  }

  .certifications-grid {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 600px) {
  .certifications {
    margin-top: 0;
    width: 100%;
    max-width: 100%;
    overflow-x: clip;

    padding:
      70px 18px;
  }

  .certifications-container,
  .certifications-left,
  .certifications-grid {
    width: 100%;
    min-width: 0;
    max-width: 100%;
  }

  .certifications-container {
    grid-template-columns: minmax(0, 1fr);
  }

  .certifications-grid {
    grid-template-columns: minmax(0, 1fr);
  }

  .certification-card,
  .certification-card-inner {
    width: 100%;
    min-width: 0;
    max-width: 100%;
  }

  .certification-card {
    min-height: 280px;
  }

  .certification-card-inner {
    min-height: 278px;
  }

  .certifications-title {
    font-size: 52px;
  }

  .certifications-description {
    font-size: 15px;
  }

  .certifications-artwork {
    height: 300px;
  }

  .certifications-orbit {
    width: 270px;
    height: 150px;
  }

  .orbit-two {
    width: 225px;
    height: 125px;
  }

  .orbit-three {
    width: 310px;
    height: 185px;
  }

  .certifications-platform {
    width: 235px;
    height: 50px;
  }

  .platform-top {
    width: 235px;
    height: 32px;
  }

  .platform-body {
    width: 230px;
  }

  .platform-bottom {
    width: 200px;
  }

  .certifications-medal {
    transform:
      scale(0.9);
  }

  .certifications-button {
    margin-top: 25px;

    padding:
      14px 22px;

    min-width: 205px;

    font-size: 13px;
  }

  .certificate-modal-panel {
    width: 98vw;
    height: 96vh;

    border-radius: 12px;
  }

  .certificate-viewer-toolbar {
    flex-basis: 52px;

    padding:
      0 8px;
  }

  .certificate-viewer-title {
    max-width: 180px;
    font-size: 12px;
  }

  .certificate-viewer-control {
    width: 44px;
    min-width: 44px;
    height: 44px;
  }

  .certificate-minimized-bar {
    right: 12px;
    bottom: 12px;

    min-width: 220px;
  }
}


@media (max-width: 767px) {
  .certificate-modal {
    align-items: stretch;
    padding: 0;
    overflow: hidden;
  }

  .certificate-modal-panel,
  .certificate-modal-panel-maximized {
    width: 100vw;
    max-width: 100vw;
    height: 100dvh;
    max-height: 100dvh;
    border-radius: 0;
  }

  .certificate-viewer-toolbar {
    flex-basis: 52px;
    min-width: 0;
    padding: 0 8px 0 12px;
  }

  .certificate-viewer-title {
    max-width: min(46vw, 220px);
    font-size: 12px;
  }

  .certificate-viewer-control {
    width: 44px;
    min-width: 44px;
    height: 44px;
  }

  .certificate-pdf-frame {
    min-width: 0;
    max-width: 100%;
    height: calc(100dvh - 52px);
  }
}
</style>
