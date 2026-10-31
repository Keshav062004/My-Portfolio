<script setup lang="ts">
import Layout from "../../../components/Layout.vue";
import Hero from "./Hero.vue";
import About from "./About.vue";
import Education from "./Education.vue";
import Certifications from "./Certifications.vue";
import Experience from "./Experience.vue";
import Projects from "./Projects.vue";
import Contact from "./Contact.vue";
import Footer from "../../../components/Footer.vue";

import {
  ref,
  onMounted,
  onUnmounted,
  watchEffect,
  computed,
  watch,
  nextTick,
} from "vue";

import { three } from "../../../three";
import { animations } from "../../../animations";
import HeaderHome from "../../../components/HeaderHome.vue";
import { preloaderVisible } from "../../../composables/usePreloader";
import ScrollIcon from "../../../components/ScrollIcon.vue";
import { raycast } from "../../../three/utils/raycast";
import gsap from "gsap";
import { useAgent } from "../../../composables/useAgent";
import {
  projectId,
  projectVisible,
} from "../../../composables/useRouteObserver";
import { isTransitioning } from "../../../composables/useProjectTransition";
import { renderer } from "../../../three/core/renderer";

const introRef = ref<HTMLElement | null>(null);
const stickyObserver = ref<IntersectionObserver | null>(null);

const scrolledPastIntro = ref(false);
const projectsLoaded = ref(false);

const contactRef = ref<HTMLElement | null>(null);
const contactBottom = ref<number>(0);

const aboutSpacerRef = ref<HTMLElement | null>(null);

const isHoveringObject3D = ref<boolean>(false);

const threeCanvasRef = ref<HTMLCanvasElement | null>(null);
const threeInitialized = ref<boolean>(false);

const { isTouch } = useAgent();

/* =========================================================
   INTRO / STICKY SECTION
========================================================= */

const handleIntersection = (entries: IntersectionObserverEntry[]) => {
  scrolledPastIntro.value = entries[0]?.isIntersecting ?? false;
};

const isStickyVisible = computed(() => {
  return scrolledPastIntro.value || !projectsLoaded.value;
});

/* =========================================================
   CONTACT POSITION
========================================================= */

const updateContactBottomOffset = () => {
  if (!contactRef.value) return;

  const bounding = contactRef.value.getBoundingClientRect();
  const documentBottom = document.documentElement.scrollHeight;
  const elementBottom = bounding.bottom + window.scrollY;

  contactBottom.value = documentBottom - elementBottom;
};

watch([projectVisible, isTransitioning], () => {
  if (!projectVisible.value) {
    updateContactBottomOffset();
  }
});

/* =========================================================
   CONTACT RESIZE OBSERVER
========================================================= */

watchEffect((onInvalidate) => {
  if (!contactRef.value || preloaderVisible.value) return;

  const resizeObserver = new ResizeObserver(
    updateContactBottomOffset,
  );

  resizeObserver.observe(contactRef.value);

  onInvalidate(() => {
    resizeObserver.disconnect();
  });
});

/* =========================================================
   THREE.JS CURSOR
========================================================= */

const updateCursor = () => {
  if (isTouch.value) return;

  const hoveringBox = raycast.getHoveringBox();
  const shouldBePointer = !!hoveringBox;

  if (shouldBePointer !== isHoveringObject3D.value) {
    isHoveringObject3D.value = shouldBePointer;

    document.documentElement.style.cursor =
      shouldBePointer ? "pointer" : "";
  }
};

/* =========================================================
   MOUNT
========================================================= */

onMounted(() => {
  /* Sticky observer */
  stickyObserver.value = new IntersectionObserver(
    handleIntersection,
  );

  if (introRef.value) {
    stickyObserver.value.observe(introRef.value);
  }

  /* Three.js */
  if (
    threeCanvasRef.value &&
    !threeInitialized.value
  ) {
    three.init(threeCanvasRef.value);
    threeInitialized.value = true;
  }

  /* Cursor */
  gsap.ticker.add(updateCursor);

});

/* =========================================================
   UNMOUNT
========================================================= */

onUnmounted(() => {
  stickyObserver.value?.disconnect();
  stickyObserver.value = null;

  three.destroy();

  document.documentElement.style.cursor = "";

  gsap.ticker.remove(updateCursor);

  animations.destroy();
});

/* =========================================================
   PROJECTS
========================================================= */

const handleProjectsLoaded = () => {
  projectsLoaded.value = true;

  /*
   * At this point `v-if="projectsLoaded"` on Contact becomes true, so
   * Contact.vue mounts and creates its own ScrollTriggers on the NEXT
   * tick (child components mount after this flag update flushes). Wait
   * for that before refreshing, otherwise Contact's trigger boundaries
   * get calculated against a layout that hasn't accounted for it yet -
   * this is what caused Contact to render "stuck" after returning from
   * /certificates while every other section eventually recovered.
   */
  void nextTick(() => {
    animations.refresh();
  });
};

/* =========================================================
   THREE.JS / GSAP ANIMATIONS
========================================================= */

watchEffect((onInvalidate) => {
  if (
    projectsLoaded.value &&
    threeInitialized.value &&
    !preloaderVisible.value
  ) {
    animations.init();
  }

  onInvalidate(() => {
    animations.destroy();
  });
});

/* =========================================================
   PROJECT ROUTE VISIBILITY
========================================================= */

watch(
  projectVisible,
  (newVal) => {
    renderer.setIsActive(!newVal);
  },
  {
    immediate: true,
  },
);
</script>

<template>
  <div
    :class="[
      'home-wrapper',

      typeof projectId === 'string' &&
        isTransitioning &&
        'home-wrapper-out',

      typeof projectId !== 'string' &&
        isTransitioning &&
        'home-wrapper-in',
    ]"
  >
    <!-- Scroll indicator -->
    <ScrollIcon />

    <Layout>

      <!-- =================================================
           HERO + ABOUT
      ================================================= -->

      <div
        class="intro-wrapper"
        ref="introRef"
      >

        <!-- Sticky Three.js / About -->
        <div
          class="intro-sticky"
          :class="{
            'intro-sticky-visible': isStickyVisible,
          }"
          :style="{
            '--contact-bottom': `${contactBottom}px`,
          }"
        >

          <canvas
            :class="[
              'three-canvas',
              {
                'three-canvas-contact':
                  !isStickyVisible,
              },
            ]"
            ref="threeCanvasRef"
          ></canvas>

          <div
            :class="{
              'intro-about-hidden':
                !isStickyVisible,
            }"
          >
            <About
              :spacer-ref="aboutSpacerRef"
            />
          </div>

        </div>

        <!-- Hero -->
        <Hero
          class="intro-hero"
          id="hero"
        />

        <!-- Desktop spacer -->
        <div
          class="intro-wrapper-spacer"
        ></div>

        <!-- About spacer -->
        <div
          class="about-spacer"
          ref="aboutSpacerRef"
          id="about"
        ></div>

      </div>
      <!-- =================================================
     EDUCATION
================================================= -->

      <Education
         id="education"
      />
  <!-- =================================================
           CERTIFICATIONS

      ================================================= -->

      <Certifications
        id="certifications"
      />

      <!-- =================================================
           EXPERIENCE
      ================================================= -->

      <Experience
        id="experience"
      />


      <!-- =================================================
           PROJECTS
      ================================================= -->

      <Projects
        @loaded="handleProjectsLoaded"
      />


      <!-- =================================================
           CONTACT
      ================================================= -->

      <div
        ref="contactRef"
        class="home-contact"
      >
        <Contact
          id="contact"
          v-if="projectsLoaded"
        />
      </div>


      <!-- =================================================
           FOOTER
      ================================================= -->

      <Footer
        :withSocial="false"
      />

    </Layout>
  </div>


  <!-- Header -->
  <HeaderHome
    v-if="projectsLoaded"
  />
</template>


<style scoped lang="scss">

/* =========================================================
   THREE.JS CANVAS
========================================================= */

.three-canvas {
  width: calc(var(--svw) * 100);
  height: calc(var(--lvh) * 100);

  max-height: calc(var(--lvh) * 100);

  position: relative;

  overflow: hidden;

  &-contact {
    position: absolute;

    bottom: var(--contact-bottom);
    left: 0;

    width: 100%;

    height: calc(var(--lvh) * 100);
    max-height: calc(var(--lvh) * 100);
  }
}


/* =========================================================
   HOME WRAPPER
========================================================= */

.home {

  &-wrapper {
    transform-origin: center center;

    &-out {
      animation:
        home-wrapper-out
        var(--transition-route-duration)
        var(--transition-route-ease);
    }

    &-in {
      animation:
        home-wrapper-in
        var(--transition-route-duration)
        var(--transition-route-ease);
    }

    @keyframes home-wrapper-out {

      0% {
        transform: scale(1);
      }

      100% {
        transform: scale(0.95);
      }

    }

    @keyframes home-wrapper-in {

      0% {
        transform: scale(0.95);
      }

      100% {
        transform: scale(1);
      }

    }
  }


  /* =====================================================
     CONTACT
  ===================================================== */

  &-contact {
    width: 100%;

    min-height:
      calc(var(--lvh) * 100);

    max-height:
      calc(var(--lvh) * 100);
  }
}


/* =========================================================
   ABOUT SPACER
========================================================= */

.about-spacer {
  max-height:
    calc(var(--lvh) * 250);

  min-height:
    calc(var(--lvh) * 250);
}


/* =========================================================
   INTRO WRAPPER
========================================================= */

.intro-wrapper {
  width: 100%;

  display: flex;

  flex-direction: column;


  &-spacer {

    display: none;

    @include mixins.mq("md") {
      display: block;

      height: 200px;
    }

  }
}


/* =========================================================
   HERO
========================================================= */

.intro-hero {
  position: absolute;

  top: 0;
  left: 0;

  width: 100%;

  max-height:
    calc(var(--lvh) * 100);

  min-height:
    calc(var(--lvh) * 100);

  overflow: hidden;
}


/* =========================================================
   ABOUT HIDDEN
========================================================= */

.intro-about-hidden {
  visibility: hidden;
}


/* =========================================================
   STICKY INTRO
========================================================= */

.intro-sticky {
  top: 0;
  left: 0;

  width: 100%;

  max-height:
    calc(var(--lvh) * 100);

  min-height:
    calc(var(--lvh) * 100);

  overflow: hidden;

  z-index: -1;

  display: flex;

  align-items: flex-end;


  &-visible {
    position: sticky;
  }
}


/* =========================================================
   STICKY CONTENT
========================================================= */

.intro-sticky-content {

  width:
    calc(var(--svw) * 100);

  height:
    calc(var(--lvh) * 100);

  max-height:
    calc(var(--lvh) * 100);

  position: relative;

  overflow: hidden;


  &-contact {

    position: absolute;

    bottom:
      var(--contact-bottom);

    left: 0;

    width: 100%;

    height:
      calc(var(--lvh) * 100);

    max-height:
      calc(var(--lvh) * 100);
  }
}


</style>
