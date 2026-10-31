<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

/* =========================================================
   ALL 33 CERTIFICATES
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
    name: "AI for Scientific Research",
    issuer: "Coursera",
    date: "Feb 2026",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/ai-for-scientific-research.pdf",
  },
  {
    name: "AI Foundations for Everyone",
    issuer: "Coursera",
    date: "Oct 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/ai-foundations-for-everyone.pdf",
  },
  {
    name: "AWS Partner: Generative AI Essentials",
    issuer: "Amazon Web Services",
    date: "Feb 2026",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-genai.pdf",
  },
  {
    name: "AWS Partner: Sales Accreditation",
    issuer: "Amazon Web Services",
    date: "Dec 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-sales.pdf",
  },
  {
    name: "AWS Partner: Accreditation (Technical)",
    issuer: "Amazon Web Services",
    date: "Dec 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-technical.pdf",
  },
  {
    name: "Becoming an SAP Professional",
    issuer: "SAP",
    date: "Oct 2025",
    icon: "/certificate-icons/sap.png",
    pdf: "/certificates/becoming-an-sap-professional.pdf",
  },
  {
    name: "Cloud Computing Foundations",
    issuer: "Coursera",
    date: "Mar 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/cloud-computing-foundations.pdf",
  },
  {
    name: "Cloud Data Engineering",
    issuer: "Coursera",
    date: "Feb 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/cloud-data-engineering.pdf",
  },
  {
    name: "Cloud Virtualization, Containers and APIs",
    issuer: "Coursera",
    date: "Mar 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/cloud-virtualization-containers-apis.pdf",
  },
  {
    name: "Cybersecurity Essentials",
    issuer: "Cisco",
    date: "Jul 2024",
    icon: "/certificate-icons/cisco.png",
    pdf: "/certificates/cybersecurity-essentials.pdf",
  },
  {
    name: "Generative AI: Introduction and Applications",
    issuer: "Coursera",
    date: "Aug 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/generative-ai-introduction-and-applications.pdf",
  },
  {
    name: "Generative AI Prompt Engineering Basics",
    issuer: "Coursera",
    date: "Aug 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/generative-ai-prompt-engineering-basics.pdf",
  },
  {
    name: "Good with Words: Speaking and Presenting",
    issuer: "Coursera",
    date: "Nov 2023",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/good-with-words-speaking-and-presenting.pdf",
  },
  {
    name: "IBM AI Enterprise Workflow",
    issuer: "IBM",
    date: "Aug 2025",
    icon: "/certificate-icons/ibm.png",
    pdf: "/certificates/ibm-ai-enterprise-workflow.pdf",
  },
  {
    name: "IBM Deep Learning with PyTorch, Keras and TensorFlow",
    issuer: "IBM",
    date: "Aug 2025",
    icon: "/certificate-icons/ibm.png",
    pdf: "/certificates/ibm-deep-learning-pytorch-keras-tensorflow.pdf",
  },
  {
    name: "IBM DevOps",
    issuer: "IBM",
    date: "Feb 2025",
    icon: "/certificate-icons/ibm.png",
    pdf: "/certificates/ibm-devops.pdf",
  },
  {
    name: "Introduction to Cloud Computing",
    issuer: "Coursera",
    date: "Mar 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/introduction-to-cloud-computing.pdf",
  },
  {
    name: "Introduction to Cybersecurity",
    issuer: "Cisco",
    date: "Feb 2024",
    icon: "/certificate-icons/cisco.png",
    pdf: "/certificates/introduction-to-cybersecurity.pdf",
  },
  {
    name: "Introduction to Packet Tracer",
    issuer: "Cisco",
    date: "Jul 2024",
    icon: "/certificate-icons/cisco.png",
    pdf: "/certificates/introduction-to-packet-tracer.pdf",
  },
  {
    name: "Network Technician Career Path",
    issuer: "Cisco",
    date: "Jan 2025",
    icon: "/certificate-icons/cisco.png",
    pdf: "/certificates/network-technician-career-path.pdf",
  },
  {
    name: "Networking Essentials",
    issuer: "Cisco",
    date: "Jan 2025",
    icon: "/certificate-icons/cisco.png",
    pdf: "/certificates/networking-essentials.pdf",
  },
  {
    name: "Introduction to Python",
    issuer: "Coding Ninjas",
    date: "Jan 2023",
    icon: "/certificate-icons/coding-ninjas.png",
    pdf: "/certificates/python.pdf",
  },
  {
    name: "SAP S4HANA From ABAP to Cloud Ready Applications",
    issuer: "SAP",
    date: "Oct 2025",
    icon: "/certificate-icons/sap.png",
    pdf: "/certificates/sap-s4hana-from-abap-to-cloud-ready-applications.pdf",
  },
  {
    name: "SAP Professional",
    issuer: "SAP",
    date: "Oct 2025",
    icon: "/certificate-icons/sap.png",
    pdf: "/certificates/sap.pdf",
  },
  {
    name: "Software Product Management",
    issuer: "Coursera",
    date: "Feb 2026",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/software-product-management.pdf",
  },
  {
    name: "The Global Financial Crisis",
    issuer: "Coursera",
    date: "Apr 2024",
    icon: "/certificate-icons/coursera.png",
    pdf: "/certificates/the-global-financial-crisis.pdf",
  },
  {
    name: "Introduction to Amazon Quick",
    issuer: "Amazon Web Services",
    date: "Jun 2026",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/introduction-to-amazon-quick.pdf",
  },
  {
    name: "AWS Cloud Quest: Cloud Practitioner",
    issuer: "Amazon Web Services",
    date: "Dec 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-cloud-quest-cloud-practitioner.pdf",
  },
  {
    name: "AWS Cloud Practitioner Essentials",
    issuer: "Amazon Web Services",
    date: "Dec 14, 2025",
    icon: "/certificate-icons/aws.png",
    pdf: "/certificates/aws-cloud-practitioner-essentials.pdf",
  },
  {
    name: "Google Analytics for Beginners",
    issuer: "Google",
    date: "May 2024",
    icon: "/certificate-icons/google.png",
    pdf: "/certificates/google-analytics-for-beginners.pdf",
  },
  {
    name: "Google Certificate – Sales",
    issuer: "Google",
    date: "Jun 2026",
    icon: "/certificate-icons/google.png",
    pdf: "/certificates/google-sales.pdf",
  },
  {
    name: "Generative AI by Google Cloud",
    issuer: "Google",
    date: "Oct 2024",
    icon: "/certificate-icons/google.png",
    pdf: "/certificates/generative-ai-by-google-cloud.pdf",
  },
];

/* =========================================================
   PDF VIEWER
========================================================= */

const activeCertificate = ref<string | null>(null);
const activeCertificateName = ref("");
const certificateMaximized = ref(false);
const certificateMinimized = ref(false);

const openCertificate = (pdf: string, name: string) => {
  // Use the browser's native PDF viewer on phones. Chromium/Safari can
  // refuse to render a PDF inside an iframe on mobile.
  if (
    typeof window !== "undefined" &&
    window.matchMedia("(max-width: 767px)").matches
  ) {
    window.open(pdf, "_blank", "noopener,noreferrer");
    return;
  }

  activeCertificate.value = pdf;

  activeCertificateName.value = name;

  certificateMaximized.value = false;
  certificateMinimized.value = false;

  document.body.style.overflow = "hidden";
  document.body.classList.add("certificate-view-open");
};

const closeCertificate = () => {
  activeCertificate.value = null;
  activeCertificateName.value = "";

  certificateMaximized.value = false;
  certificateMinimized.value = false;

  document.body.style.overflow = "";
  document.body.classList.remove("certificate-view-open");
};

const toggleCertificateMaximize = () => {
  certificateMaximized.value = !certificateMaximized.value;
  certificateMinimized.value = false;
};

const toggleCertificateMinimize = () => {
  certificateMinimized.value = !certificateMinimized.value;

  if (certificateMinimized.value) {
    certificateMaximized.value = false;
  }
};

const handleCertificateKeydown = (event: KeyboardEvent) => {
  if (event.key === "Escape" && activeCertificate.value) {
    closeCertificate();
  }
};

/* =========================================================
   PAGE BACK BUTTON
========================================================= */

const goBack = () => {
  if (typeof window === "undefined") return;

  if (window.history.length > 1) {
    window.history.back();
  } else {
    window.location.href = "/";
  }
};

/* =========================================================
   SEARCH / FILTER
========================================================= */

const searchQuery = ref("");

const selectedIssuer = ref("All");

const issuers = computed(() => {
  const values = certifications.map((cert) => cert.issuer);
  return ["All", ...Array.from(new Set(values))];
});

const filteredCertificates = computed(() => {
  const query = searchQuery.value.trim().toLowerCase();

  return certifications.filter((cert) => {
    const matchesSearch =
      !query ||
      cert.name.toLowerCase().includes(query) ||
      cert.issuer.toLowerCase().includes(query);

    const matchesIssuer =
      selectedIssuer.value === "All" ||
      cert.issuer === selectedIssuer.value;

    return matchesSearch && matchesIssuer;
  });
});

/* =========================================================
   GSAP
========================================================= */

let ctx: gsap.Context | null = null;

onMounted(() => {
  window.addEventListener("keydown", handleCertificateKeydown);

  const section = document.querySelector(".certificates-page");

  if (!section) return;

  ctx = gsap.context(() => {
    const eyebrow = section.querySelector(".certificates-eyebrow");
    const title = section.querySelector(".certificates-title");
    const description = section.querySelector(".certificates-description");
    const controls = section.querySelector(".certificates-controls");

    const cards = gsap.utils.toArray<HTMLElement>(
      ".all-certificate-card"
    );

    gsap.set(eyebrow, {
      opacity: 0,
      y: 30,
    });

    gsap.set(title, {
      opacity: 0,
      y: 50,
    });

    gsap.set(description, {
      opacity: 0,
      y: 30,
    });

    gsap.set(controls, {
      opacity: 0,
      y: 25,
    });

    gsap.set(cards, {
      opacity: 0,
      y: 45,
      scale: 0.96,
    });

    gsap.to(eyebrow, {
      opacity: 1,
      y: 0,
      duration: 0.7,
      ease: "power3.out",
    });

    gsap.to(title, {
      opacity: 1,
      y: 0,
      duration: 0.9,
      delay: 0.1,
      ease: "power4.out",
    });

    gsap.to(description, {
      opacity: 1,
      y: 0,
      duration: 0.7,
      delay: 0.25,
      ease: "power3.out",
    });

    gsap.to(controls, {
      opacity: 1,
      y: 0,
      duration: 0.7,
      delay: 0.35,
      ease: "power3.out",
    });

    gsap.to(cards, {
      opacity: 1,
      y: 0,
      scale: 1,
      duration: 0.65,
      stagger: 0.055,
      delay: 0.45,
      ease: "power3.out",
      scrollTrigger: {
        trigger: ".certificates-grid",
        start: "top 90%",
        once: true,
      },
    });

    gsap.to(".certificates-background-orb-one", {
      x: 40,
      y: -30,
      duration: 6,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
    });

    gsap.to(".certificates-background-orb-two", {
      x: -35,
      y: 35,
      duration: 7,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
    });

    ScrollTrigger.refresh();
  }, section);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleCertificateKeydown);

  document.body.style.overflow = "";
  document.body.classList.remove("certificate-view-open");

  ctx?.revert();
  ctx = null;
});
</script>

<template>
  <main class="certificates-page">

    <!-- =====================================================
         PAGE BACK BUTTON
    ====================================================== -->

    <button
      type="button"
      class="certificates-back-button"
      aria-label="Go back"
      title="Go back"
      @click="goBack"
    >
      <span class="certificates-back-arrow" aria-hidden="true">←</span>
      <span>Back</span>
    </button>

    <!-- =====================================================
         BACKGROUND
    ====================================================== -->

    <div class="certificates-background">
      <div class="certificates-background-grid"></div>

      <div
        class="certificates-background-orb certificates-background-orb-one"
      ></div>

      <div
        class="certificates-background-orb certificates-background-orb-two"
      ></div>

      <div class="certificates-background-dot dot-one"></div>
      <div class="certificates-background-dot dot-two"></div>
      <div class="certificates-background-dot dot-three"></div>
      <div class="certificates-background-dot dot-four"></div>
    </div>

    <!-- =====================================================
         HEADER
    ====================================================== -->

    <section class="certificates-header">

      <div class="certificates-eyebrow">
        MY ACHIEVEMENTS
      </div>

      <h1 class="certificates-title">
        All Certifications<span>.</span>
      </h1>

      <div class="certificates-title-line"></div>

      <p class="certificates-description">
        A collection of certifications and professional
        credentials earned across cloud computing,
        artificial intelligence, cybersecurity,
        networking, software and business.
      </p>

    </section>

    <!-- =====================================================
         SEARCH / FILTER
    ====================================================== -->

    <section class="certificates-controls">

      <div class="certificate-search-wrapper">
        <span class="certificate-search-icon">
          ⌕
        </span>

        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search certificates..."
          class="certificate-search"
        />
      </div>

      <div class="certificate-filter-wrapper">

        <button
          v-for="issuer in issuers"
          :key="issuer"
          type="button"
          class="certificate-filter"
          :class="{
            'certificate-filter-active':
              selectedIssuer === issuer,
          }"
          @click="selectedIssuer = issuer"
        >
          {{ issuer }}
        </button>

      </div>

    </section>

    <!-- =====================================================
         COUNT
    ====================================================== -->

    <div class="certificate-count">
      Showing
      <strong>{{ filteredCertificates.length }}</strong>
      of
      <strong>{{ certifications.length }}</strong>
      certificates
    </div>

    <!-- =====================================================
         ALL 33 CERTIFICATES
    ====================================================== -->

    <section class="certificates-grid">

      <article
        v-for="(cert, index) in filteredCertificates"
        :key="cert.name"
        class="all-certificate-card"
      >

        <div class="all-certificate-card-inner">

          <!-- Number -->

          <div class="certificate-number">
            {{ String(index + 1).padStart(2, "0") }}
          </div>

          <!-- Icon -->

          <div class="all-certificate-icon">

            <img
              :src="cert.icon"
              :alt="`${cert.issuer} logo`"
              class="all-certificate-logo"
            />

          </div>

          <!-- Content -->

          <div class="all-certificate-content">

            <div class="certificate-issuer">
              {{ cert.issuer }}
            </div>

            <h2>
              {{ cert.name }}
            </h2>

            <div class="certificate-date">
              <span class="date-icon">▣</span>
              {{ cert.date }}
            </div>

          </div>

          <!-- Bottom -->

          <div class="all-certificate-bottom">

            <span class="certificate-type">
              CERTIFICATE
            </span>

            <button
              type="button"
              class="view-credential-button"
              @click="openCertificate(cert.pdf, cert.name)"
            >
              <span>
                View Credential
              </span>

              <span class="credential-arrow">
                ↗
              </span>
            </button>

          </div>

        </div>

      </article>

    </section>

    <!-- =====================================================
         EMPTY STATE
    ====================================================== -->

    <div
      v-if="filteredCertificates.length === 0"
      class="certificates-empty"
    >
      <div class="empty-icon">
        ×
      </div>

      <h2>
        No certificates found
      </h2>

      <p>
        Try another search or select a different category.
      </p>

      <button
        type="button"
        @click="
          searchQuery = '';
          selectedIssuer = 'All';
        "
      >
        Show All Certificates
      </button>
    </div>

    <!-- =====================================================
         PDF VIEWER
    ====================================================== -->

    <Teleport to="body">

      <Transition name="certificate-modal">

        <div
          v-if="activeCertificate"
          class="certificate-modal"
          :class="{
            'certificate-modal-minimized':
              certificateMinimized,
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

              <span class="certificate-viewer-small-icon">
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
                class="certificate-viewer-control certificate-viewer-close"
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
                certificateMaximized,
            }"
          >

            <div class="certificate-viewer-toolbar">

              <div class="certificate-viewer-left">

                <span class="certificate-viewer-small-icon">
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
                  {{ certificateMaximized ? "❐" : "□" }}
                </button>

                <button
                  class="certificate-viewer-control certificate-viewer-close"
                  type="button"
                  aria-label="Close certificate viewer"
                  title="Close"
                  @click="closeCertificate"
                >
                  ×
                </button>

              </div>

            </div>

            <iframe
              v-if="activeCertificate"
              :src="activeCertificate"
              class="certificate-pdf-frame"
              title="Certificate PDF"
              loading="eager"
              allow="fullscreen"
            ></iframe>

          </div>

        </div>

      </Transition>

    </Teleport>

  </main>
</template>

<style scoped lang="scss">

/* =========================================================
   PAGE
========================================================= */

.certificates-page {
  position: relative;

  min-height: 100vh;

  padding:
    120px
    5vw
    100px;

  overflow: hidden;

  background:
    radial-gradient(
      circle at 85% 15%,
      rgba(255, 170, 70, 0.14),
      transparent 28%
    ),
    radial-gradient(
      circle at 5% 85%,
      rgba(255, 138, 0, 0.08),
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

/* =========================================================
   PAGE BACK BUTTON
========================================================= */

.certificates-back-button {
  position: fixed;

  top: 24px;
  right: 28px;

  z-index: 9999;

  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;

  min-width: 92px;
  height: 44px;
  padding: 0 17px;

  border: 1px solid rgba(255, 138, 0, 0.28);
  border-radius: 999px;

  background: rgba(255, 253, 248, 0.82);
  color: #6f665b;

  font-family: inherit;
  font-size: 13px;
  font-weight: 800;
  letter-spacing: 0.02em;

  cursor: pointer;

  box-shadow: 0 8px 25px rgba(89, 63, 32, 0.08);

  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);

  transition:
    transform 0.25s ease,
    background 0.25s ease,
    color 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease;
}

.certificates-back-button:hover {
  transform: translateY(-2px);

  background: linear-gradient(120deg, #ff8a00, #ffad32);
  color: #ffffff;
  border-color: #ff9a26;

  box-shadow: 0 12px 30px rgba(255, 138, 0, 0.22);
}

.certificates-back-arrow {
  font-size: 18px;
  line-height: 1;

  transition: transform 0.25s ease;
}

.certificates-back-button:hover .certificates-back-arrow {
  transform: translateX(-3px);
}

/* =========================================================
   BACKGROUND
========================================================= */

.certificates-background {
  position: absolute;

  inset: 0;

  overflow: hidden;

  pointer-events: none;

  z-index: 0;
}

.certificates-background-grid {
  position: absolute;

  inset: 0;

  background-image:
    linear-gradient(
      rgba(100, 78, 48, 0.04) 1px,
      transparent 1px
    ),
    linear-gradient(
      90deg,
      rgba(100, 78, 48, 0.04) 1px,
      transparent 1px
    );

  background-size: 70px 70px;
}

.certificates-background-orb {
  position: absolute;

  width: 400px;
  height: 400px;

  border-radius: 50%;

  filter: blur(70px);

  opacity: 0.25;
}

.certificates-background-orb-one {
  top: -160px;
  right: -100px;

  background: #ffb04d;
}

.certificates-background-orb-two {
  bottom: -180px;
  left: -120px;

  background: #ffc982;
}

.certificates-background-dot {
  position: absolute;

  width: 5px;
  height: 5px;

  border-radius: 50%;

  background: #ff941d;

  box-shadow:
    0 0 10px
    rgba(255, 138, 0, 0.35);

  opacity: 0.5;
}

.dot-one {
  top: 14%;
  left: 8%;
}

.dot-two {
  top: 32%;
  right: 10%;
}

.dot-three {
  top: 65%;
  left: 6%;
}

.dot-four {
  bottom: 12%;
  right: 12%;
}

/* =========================================================
   HEADER
========================================================= */

.certificates-header {
  position: relative;

  z-index: 2;

  max-width: 1000px;

  margin:
    0
    auto
    55px;

  text-align: center;
}

.certificates-eyebrow {
  color: #ff8a00;

  font-size: 14px;

  font-weight: 800;

  letter-spacing: 0.2em;
}

.certificates-title {
  margin:
    15px
    0
    0;

  font-size:
    clamp(52px, 7vw, 100px);

  line-height: 0.95;

  letter-spacing: -0.055em;

  font-weight: 800;

  color: #171719;
}

.certificates-title span {
  color: #ff8a00;
}

.certificates-title-line {
  width: 180px;

  height: 2px;

  margin:
    30px
    auto
    0;

  background:
    linear-gradient(
      90deg,
      transparent,
      #ff8a00,
      transparent
    );
}

.certificates-description {
  max-width: 720px;

  margin:
    28px
    auto
    0;

  font-size: 17px;

  line-height: 1.75;

  color: #756e65;
}

/* =========================================================
   CONTROLS
========================================================= */

.certificates-controls {
  position: relative;

  z-index: 3;

  max-width: 1300px;

  margin:
    0
    auto
    25px;

  display: flex;

  align-items: center;

  justify-content: space-between;

  gap: 20px;

  flex-wrap: wrap;
}

.certificate-search-wrapper {
  position: relative;

  width: 280px;
}

.certificate-search-icon {
  position: absolute;

  left: 16px;
  top: 50%;

  transform:
    translateY(-50%);

  color: #9a8d7d;

  font-size: 23px;

  pointer-events: none;
}

.certificate-search {
  width: 100%;

  height: 48px;

  padding:
    0
    16px
    0
    45px;

  border:
    1px solid
    rgba(255, 138, 0, 0.22);

  border-radius: 100px;

  outline: none;

  background:
    rgba(255, 253, 248, 0.82);

  color: #24211d;

  font-size: 14px;

  box-shadow:
    0 8px 25px
    rgba(89, 63, 32, 0.05);

  transition:
    border-color 0.25s ease,
    box-shadow 0.25s ease;
}

.certificate-search:focus {
  border-color: #ff9a26;

  box-shadow:
    0 0 0 4px
    rgba(255, 138, 0, 0.08);
}

.certificate-filter-wrapper {
  display: flex;

  align-items: center;

  gap: 7px;

  flex-wrap: wrap;

  justify-content: flex-end;
}

.certificate-filter {
  min-width: 44px;
  min-height: 44px;
  padding:
    9px
    15px;

  border:
    1px solid
    rgba(255, 138, 0, 0.22);

  border-radius: 100px;

  background:
    rgba(255, 253, 248, 0.65);

  color: #746b60;

  font-size: 12px;

  font-weight: 700;

  cursor: pointer;

  transition:
    background 0.25s ease,
    color 0.25s ease,
    border-color 0.25s ease,
    transform 0.25s ease;
}

.certificate-filter:hover {
  color: #ed7900;

  border-color:
    rgba(255, 138, 0, 0.5);

  transform:
    translateY(-2px);
}

.certificate-filter-active {
  color: #ffffff;

  background:
    linear-gradient(
      120deg,
      #ff8a00,
      #ffad32
    );

  border-color:
    #ff9a26;
}

/* =========================================================
   COUNT
========================================================= */

.certificate-count {
  position: relative;

  z-index: 2;

  max-width: 1300px;

  margin:
    0
    auto
    20px;

  font-size: 13px;

  color: #81786c;
}

.certificate-count strong {
  color: #e87800;
}

/* =========================================================
   GRID
========================================================= */

.certificates-grid {
  position: relative;

  z-index: 2;

  max-width: 1300px;

  margin: 0 auto;

  display: grid;

  grid-template-columns:
    repeat(3, minmax(0, 1fr));

  gap: 20px;

  perspective: 1200px;
}

/* =========================================================
   CARD
========================================================= */

.all-certificate-card {
  position: relative;

  min-height: 315px;

  padding: 1px;

  border-radius: 19px;

  background:
    linear-gradient(
      135deg,
      rgba(255, 138, 0, 0.5),
      rgba(255, 255, 255, 0.9) 45%,
      rgba(255, 138, 0, 0.2)
    );

  box-shadow:
    0 12px 30px
    rgba(91, 65, 35, 0.08);

  transition:
    transform 0.4s
      cubic-bezier(.2,.8,.2,1),
    box-shadow 0.4s ease;
}

.all-certificate-card:hover {
  transform:
    translateY(-8px);

  box-shadow:
    0 22px 45px
    rgba(91, 65, 35, 0.14),
    0 0 28px
    rgba(255, 138, 0, 0.08);
}

.all-certificate-card-inner {
  position: relative;

  height: 100%;

  min-height: 313px;

  padding:
    23px;

  display: flex;

  flex-direction: column;

  overflow: hidden;

  border-radius: 18px;

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
    rgba(255, 255, 255, 0.9);

  box-shadow:
    inset 0 1px 0
    rgba(255, 255, 255, 0.95);
}

.all-certificate-card-inner::after {
  content: "";

  position: absolute;

  top: 0;
  left: -130%;

  width: 70%;
  height: 100%;

  pointer-events: none;

  background:
    linear-gradient(
      110deg,
      transparent,
      rgba(255, 255, 255, 0.45),
      transparent
    );

  transform:
    skewX(-18deg);

  transition:
    left 0.7s ease;
}

.all-certificate-card:hover
.all-certificate-card-inner::after {
  left: 145%;
}

.certificate-number {
  position: absolute;

  top: 17px;
  right: 20px;

  font-size: 12px;

  font-weight: 800;

  letter-spacing: 0.08em;

  color:
    rgba(198, 122, 32, 0.55);
}

/* =========================================================
   ICON
========================================================= */

.all-certificate-icon {
  width: 60px;
  height: 60px;

  margin-bottom: 20px;

  display: flex;

  align-items: center;
  justify-content: center;

  position: relative;

  border-radius: 15px;

  background:
    linear-gradient(
      145deg,
      #fffefa,
      #f3e5d2
    );

  border:
    1px solid
    rgba(255, 138, 0, 0.2);

  box-shadow:
    0 8px 18px
    rgba(80, 58, 30, 0.06);

  transition:
    transform 0.35s ease,
    box-shadow 0.35s ease;
}

.all-certificate-card:hover
.all-certificate-icon {
  transform:
    translateY(-2px)
    scale(1.07)
    rotate(-3deg);

  box-shadow:
    0 12px 25px
    rgba(255, 138, 0, 0.12);
}

.all-certificate-logo {
  width: 43px;
  height: 43px;

  object-fit: contain;

  display: block;
}

.fallback-icon {
  position: absolute;

  inset: 0;

  display: flex;

  align-items: center;
  justify-content: center;

  font-size: 17px;

  font-weight: 900;

  color: #171717;
}

.google-fallback {
  color: #4285f4;

  font-size: 31px;
}

.ibm-fallback {
  color: #78a9ff;

  font-size: 18px;
}

.sap-fallback {
  width: 37px;
  height: 37px;

  inset: auto;

  border-radius: 4px;

  background: #0874b9;

  color: #ffffff;

  font-size: 12px;
}

.cisco-fallback {
  color: #049bd7;

  font-size: 30px;
}

.coursera-fallback {
  color: #0056d2;

  font-size: 30px;
}

/* =========================================================
   CONTENT
========================================================= */

.all-certificate-content {
  position: relative;

  z-index: 2;
}

.certificate-issuer {
  margin-bottom: 8px;

  color: #ed7900;

  font-size: 11px;

  font-weight: 800;

  letter-spacing: 0.12em;

  text-transform: uppercase;
}

.all-certificate-content h2 {
  margin: 0;

  max-width: 90%;

  color: #1c1b1a;

  font-size: 17px;

  line-height: 1.38;

  font-weight: 700;

  transition:
    color 0.3s ease,
    transform 0.3s ease;
}

.all-certificate-card:hover
.all-certificate-content h2 {
  color: #e87500;

  transform:
    translateX(2px);
}

.certificate-date {
  display: flex;

  align-items: center;

  gap: 8px;

  margin-top: 15px;

  color: #6f675d;

  font-size: 13px;
}

.date-icon {
  color: #ff8a00;
}

/* =========================================================
   BOTTOM
========================================================= */

.all-certificate-bottom {
  position: relative;

  z-index: 2;

  display: flex;

  align-items: center;

  justify-content: space-between;

  gap: 10px;

  margin-top: auto;

  padding-top: 18px;

  border-top:
    1px solid
    rgba(120, 95, 65, 0.12);
}

.certificate-type {
  color: #a1978a;

  font-size: 10px;

  font-weight: 800;

  letter-spacing: 0.13em;
}

.view-credential-button {
  display: flex;

  align-items: center;

  gap: 7px;

  padding: 0;
  min-height: 44px;

  border: 0;

  outline: none;

  background: transparent;

  color: #ed7900;

  font-size: 12px;

  font-weight: 800;

  cursor: pointer;

  transition:
    color 0.25s ease;
}

.view-credential-button:hover {
  color: #ff8a00;
}

.credential-arrow {
  font-size: 18px;

  transition:
    transform 0.3s
    cubic-bezier(.2,.8,.2,1);
}

.view-credential-button:hover
.credential-arrow {
  transform:
    translate(3px, -3px);
}

/* =========================================================
   EMPTY
========================================================= */

.certificates-empty {
  position: relative;

  z-index: 2;

  max-width: 600px;

  margin:
    60px
    auto;

  padding: 50px;

  text-align: center;

  border-radius: 20px;

  background:
    rgba(255, 253, 248, 0.8);

  border:
    1px solid
    rgba(255, 138, 0, 0.2);
}

.empty-icon {
  width: 55px;
  height: 55px;

  margin:
    0
    auto
    15px;

  display: flex;

  align-items: center;
  justify-content: center;

  border-radius: 50%;

  background: #fff0df;

  color: #ff8a00;

  font-size: 25px;
}

.certificates-empty h2 {
  margin: 0;

  font-size: 25px;
}

.certificates-empty p {
  color: #756e65;
}

.certificates-empty button {
  margin-top: 15px;

  padding:
    12px
    20px;

  border: 0;

  border-radius: 100px;

  background: #ff8a00;

  color: #ffffff;

  cursor: pointer;
}

/* =========================================================
   BODY WHEN VIEWER OPEN
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

/* =========================================================
   PDF MODAL
========================================================= */

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
}

.certificate-viewer-control {
  width: 44px;
  min-width: 44px;
  height: 44px;

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

.certificate-pdf-frame {
  display: block;

  flex: 1;

  width: 100%;
  height: 100%;

  min-height: 0;

  border: 0;

  background: #ffffff;
}

.certificate-pdf-frame-fallback {
  display: block;
  width: 100%;
  height: 100%;
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

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 1200px) {

  .certificates-grid {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));
  }

  .certificates-controls {
    align-items: flex-start;
  }

  .certificate-filter-wrapper {
    justify-content: flex-start;
  }

}

@media (max-width: 800px) {

  .certificates-page {
    padding:
      90px
      25px
      80px;
  }

  .certificates-header {
    margin-bottom: 40px;
  }

  .certificates-description {
    font-size: 15px;
  }

  .certificates-controls {
    flex-direction: column;
  }

  .certificate-search-wrapper {
    width: 100%;
  }

  .certificate-filter-wrapper {
    width: 100%;
  }

}

@media (max-width: 650px) {

  .certificates-back-button {
    top: 16px;
    right: 16px;

    min-width: 44px;
    width: 44px;
    height: 44px;
    padding: 0;

    border-radius: 50%;
  }

  .certificates-back-button span:last-child {
    display: none;
  }

  .certificates-back-arrow {
    font-size: 20px;
  }

  .certificates-page {
    padding:
      75px
      18px
      60px;
  }

  .certificates-title {
    font-size: 52px;
  }

  .certificates-grid {
    grid-template-columns: 1fr;

    gap: 17px;
  }

  .all-certificate-card {
    min-height: 295px;
  }

  .all-certificate-card-inner {
    min-height: 293px;
  }

  .certificate-filter {
    min-height: 44px;
    font-size: 11px;

    padding:
      8px
      12px;
  }

  .certificate-modal-panel {
    width: 98vw;

    height: 96vh;

    border-radius: 12px;
  }

  .certificate-viewer-toolbar {
    flex-basis: 50px;

    padding-left: 14px;
  }

  .certificate-viewer-title {
    max-width: 190px;

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

    max-width: calc(100vw - 24px);
  }

}


@media (max-width: 650px) {
  .certificate-modal {
    align-items: stretch;
    justify-content: stretch;
    padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
  }

  .certificate-modal-panel {
    width: 100%;
    height: 100%;
    max-width: 100%;
    max-height: 100%;
    border-radius: 0;
    border-left: 0;
    border-right: 0;
  }

  .certificate-viewer-toolbar {
    flex: 0 0 54px;
    min-width: 0;
    padding-left: 12px;
  }

  .certificate-viewer-left {
    min-width: 0;
    flex: 1 1 auto;
  }

  .certificate-viewer-title {
    max-width: min(58vw, 230px);
  }

  .certificate-viewer-controls {
    flex: 0 0 auto;
  }

  .certificate-viewer-control {
    width: 44px;
    min-width: 44px;
    height: 44px;
  }

  .certificate-pdf-frame {
    display: block;
    width: 100%;
    max-width: 100%;
    min-width: 0;
    height: 100%;
    min-height: 0;
    flex: 1 1 auto;
    border: 0;
    background: #fff;
  }
}

</style>