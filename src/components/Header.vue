<script setup lang="ts">
import Logo from "./Logo.vue";
import { computed, onMounted, onUnmounted, ref } from "vue";
import { useHeaderTheme } from "../composables/useHeaderTheme";
import { lenis } from "../composables/useScroll";
import { projectId } from "../composables/useRouteObserver";
import ButtonRound from "./ButtonRound.vue";
import ArrowRight from "./icons/ArrowRight.vue";
import SoundsToggle from "./SoundsToggle.vue";
import { isFeatureEnabled } from "../utils/features";
import { useRouter } from "../composables/useRouter";
import { useFirstRoute } from "../composables/useFirstRoute";
import { t } from "../i18n/utils/translate";

const router = useRouter();
const { isFirstRoute } = useFirstRoute();

const scrolledPastHeroVisible = ref(false);

const { isDarkTheme } = useHeaderTheme({
  onUpdate: (
    element,
    boundingClientRect,
    hasScrolledIntoView,
  ) => {
    if (!element || !boundingClientRect) {
      scrolledPastHeroVisible.value = false;
      return;
    }

    if (hasScrolledIntoView) {
      scrolledPastHeroVisible.value = true;
    } else {
      scrolledPastHeroVisible.value = false;
    }
  },
});

/* =========================================
   BACK BUTTON
========================================= */

const handleBackClick = () => {
  if (isFirstRoute.value) {
    router.push("/");
  } else {
    router.back();
  }
};

/* =========================================
   LOGO CLICK
========================================= */

const handleLogoClick = () => {
  if (!lenis.value) return;

  lenis.value.scrollTo(0);
};

/* =========================================
   HEADER CLASSES
========================================= */

const classNames = computed(() => {
  return {
    header: true,

    "header-dark":
      isDarkTheme.value,

    "header-scrolled":
      scrolledPastHeroVisible.value,

    [`project-${projectId.value}`]:
      projectId.value !== null,
  };
});

/* =========================================
   RESUME BUTTON
========================================= */

const resumeButtonClassNames = computed(() => {
  return {
    "header-resume-button": true,

    "header-resume-button-isProjectPage":
      projectId.value !== null,
  };
});

const mobileMenuOpen = ref(false);
const mobileSections = [
  { label: "About", target: "about" },
  { label: "Education", target: "education" },
  { label: "Certifications", target: "certifications" },
  { label: "Skills", target: "skills" },
  { label: "Experience", target: "experience" },
  { label: "Projects", target: "projects" },
  { label: "Achievements", target: "achievements" },
  { label: "Contact", target: "contact" },
];
const activeMobileSection = ref("hero");
let mobileSectionObserver: IntersectionObserver | null = null;

const scrollToMobileSection = (target: string) => {
  mobileMenuOpen.value = false;
  document.getElementById(target)?.scrollIntoView({ behavior: "smooth", block: "start" });
};

onMounted(() => {
  mobileSectionObserver = new IntersectionObserver((entries) => {
    const visible = entries
      .filter((entry) => entry.isIntersecting)
      .sort((a, b) => b.intersectionRatio - a.intersectionRatio)[0];
    if (visible?.target instanceof HTMLElement) activeMobileSection.value = visible.target.id;
  }, { rootMargin: "-38% 0px -48%", threshold: [0.1, 0.35, 0.65] });

  mobileSections
    .map(({ target }) => target)
    .map((id) => document.getElementById(id))
    .filter((element): element is HTMLElement => Boolean(element))
    .forEach((element) => mobileSectionObserver?.observe(element));
});

onUnmounted(() => mobileSectionObserver?.disconnect());
</script>

<template>
  <header :class="classNames">

    <!-- =====================================
         LEFT SIDE
    ====================================== -->

    <div class="header-left">

      <ButtonRound
        v-if="projectId !== null"
        variant="accent"
        @click="handleBackClick"
        :aria-label="t('back-to-home')"
        :class="{
          'header-back': true,
          'header-back-isProjectPage':
            projectId !== null,
        }"
        data-cursor="circle-white"
        data-sound="click"
        data-hoversound="hover"
      >
        <ArrowRight
          class="header-back-icon"
        />
      </ButtonRound>

    </div>


    <!-- =====================================
         LOGO
    ====================================== -->

    <div
      :class="{
        'header-logo': true,

        'header-logo-isProjectPage':
          projectId !== null,

        'header-logo-clickable':
          scrolledPastHeroVisible,

        'children-unclickable': true,
      }"
      @click="handleLogoClick"
      data-sound="click"
      data-hoversound="hover"
      data-cursor="circle-white"
    >
      <Logo
        class="header-logo-image"
      />
    </div>


    <!-- =====================================
         RIGHT SIDE
    ====================================== -->

    <div class="header-right">

      <!-- ===================================
           DOWNLOAD RESUME
      ==================================== -->

      <a
        href="/Keshav%20Gupta_Resume.pdf"
        download="Keshav Gupta_Resume.pdf"
        :class="resumeButtonClassNames"
        aria-label="Resume"
        data-cursor="circle-white"
        data-hoversound="hover"
      >

        <span class="resume-button-text">
          RESUME
        </span>

        <span
          class="resume-download-icon"
          aria-hidden="true"
        >
          ↓
        </span>

      </a>


      <!-- ===================================
           SOUND TOGGLE
      ==================================== -->

      <SoundsToggle
        v-if="isFeatureEnabled('sounds')"
        class="header-sounds-toggle"
        :isDarkTheme="isDarkTheme"
      />

    </div>

    <button
      class="mobile-menu-toggle"
      type="button"
      :aria-expanded="mobileMenuOpen"
      aria-controls="mobile-section-menu"
      aria-label="Toggle section menu"
      @click="mobileMenuOpen = !mobileMenuOpen"
    >
      <span></span><span></span><span></span>
    </button>

    <nav
      v-if="mobileMenuOpen"
      id="mobile-section-menu"
      class="mobile-section-menu"
      aria-label="Mobile sections"
    >
      <button
        v-for="section in mobileSections"
        :key="section.label"
        type="button"
        :class="{ 'mobile-section-active': activeMobileSection === section.target }"
        @click="scrollToMobileSection(section.target)"
      >
        {{ section.label }}
      </button>
    </nav>

  </header>
</template>


<style scoped lang="scss">

/* =========================================
   HEADER
========================================= */

.header {
  position: fixed;

  top: 0;
  left: 50%;

  transform:
    translateX(-50%);

  display: flex;

  align-items: center;
  justify-content: space-between;

  width:
    var(--breakpoint-xxxl);

  max-width:
    100%;

  height:
    var(--height-header);

  padding:
    0 var(--space-outer);

  box-sizing:
    border-box;

  z-index:
    var(--z-index-header);

  pointer-events:
    none;

  --scrolled: 0;


  /* =========================================
     SCROLLED
  ========================================= */

  &-scrolled {
    --scrolled: 1;
  }


  /* =========================================
     BACK BUTTON
  ========================================= */

  &-back {
    pointer-events:
      none;

    opacity:
      0;

    transition:
      opacity 0.2s ease-in-out;

    transition-delay:
      0.1s;

    pointer-events:
      auto;


    &-icon {
      width:
        100%;

      transform:
        rotate(180deg);
    }


    &-isProjectPage {
      pointer-events:
        auto;

      opacity:
        1;
    }
  }


  /* =========================================
     LEFT SIDE
  ========================================= */

  &-left {
    position:
      absolute;

    left:
      var(--space-outer);

    top:
      50%;

    transform:
      translateY(-50%);
  }


  /* =========================================
     RIGHT SIDE
  ========================================= */

  &-right {
    position:
      absolute;

    right:
      24px;

    top:
      50%;

    transform:
      translateY(-50%);

    display:
      flex;

    align-items:
      center;

    justify-content:
      flex-end;

    gap:
      14px;

    pointer-events:
      auto;

    box-sizing:
      border-box;
  }


  /* =========================================
     RESUME BUTTON
  ========================================= */

  &-resume-button {
    display:
      none;

    align-items:
      center;

    justify-content:
      center;

    gap:
      10px;

    width:
      90px;

    min-width:
      170px;

    height:
      50px;

    padding:
      0 22px;

    box-sizing:
      border-box;

    border:
      none;

    border-radius:
      999px;

    background:
      var(--color-orange-400);

    color:
      var(--color-white-400);

    font-family:
      inherit;

    font-size:
      var(--font-size-md);

    font-weight:
      700;

    line-height:
      1;

    text-decoration:
      none;

    white-space:
      nowrap;

    cursor:
      pointer;

    position:
      relative;

    overflow:
      hidden;

    isolation:
      isolate;

    box-shadow:
      0 7px 20px
      rgba(255, 138, 0, 0.12);

    transition:
      transform 0.25s ease,
      box-shadow 0.25s ease,
      background-color 0.25s ease;


    /* =====================================
       DESKTOP
    ====================================== */

    @include mixins.mq("md") {
      display:
        inline-flex;
    }


    /* =====================================
       SHINE EFFECT
    ====================================== */

    &::before {
      content:
        "";

      position:
        absolute;

      top:
        0;

      left:
        -120%;

      width:
        55%;

      height:
        100%;

      background:
        linear-gradient(
          110deg,
          transparent 0%,
          rgba(255, 255, 255, 0.08) 35%,
          rgba(255, 255, 255, 0.4) 50%,
          rgba(255, 255, 255, 0.08) 65%,
          transparent 100%
        );

      transform:
        skewX(-18deg);

      transition:
        left 0.65s ease;

      pointer-events:
        none;

      z-index:
        0;
    }


    /* =====================================
       HOVER
    ====================================== */

    &:hover {
      transform:
        translateY(-3px);

      box-shadow:
        0 12px 30px
        rgba(255, 138, 0, 0.28);
    }


    &:hover::before {
      left:
        140%;
    }


    /* =====================================
       ACTIVE / CLICK
    ====================================== */

    &:active {
      transform:
        translateY(-1px)
        scale(0.97);

      box-shadow:
        0 5px 15px
        rgba(255, 138, 0, 0.18);
    }


    /* =====================================
       FOCUS
    ====================================== */

    &:focus-visible {
      outline:
        2px solid
        var(--color-orange-400);

      outline-offset:
        4px;
    }


    /* =====================================
       PROJECT PAGE
    ====================================== */

    &-isProjectPage {
      opacity:
        1 !important;
    }
  }


  /* =========================================
     RESUME TEXT
  ========================================= */

  .resume-button-text {
    display:
      inline-flex;

    align-items:
      center;

    justify-content:
      center;

    line-height:
      1;

    white-space:
      nowrap;

    position:
      relative;

    z-index:
      1;
  }


  /* =========================================
     DOWNLOAD ARROW
  ========================================= */

  .resume-download-icon {
    display:
      inline-flex;

    align-items:
      center;

    justify-content:
      center;

    width:
      20px;

    height:
      20px;

    flex:
      0 0 20px;

    font-size:
      23px;

    font-weight:
      800;

    line-height:
      1;

    position:
      relative;

    top:
      -1px;

    z-index:
      1;

    transition:
      transform 0.25s ease;
  }


  /* =========================================
     DOWNLOAD ARROW HOVER ANIMATION
  ========================================= */

  &-resume-button:hover
  .resume-download-icon {
    animation:
      resumeDownloadArrow
      0.65s
      cubic-bezier(
        0.22,
        1,
        0.36,
        1
      )
      infinite;
  }


  @keyframes resumeDownloadArrow {

    0% {
      transform:
        translateY(-2px);
    }

    45% {
      transform:
        translateY(4px);
    }

    70% {
      transform:
        translateY(1px);
    }

    100% {
      transform:
        translateY(-2px);
    }
  }


  /* =========================================
     SOUND TOGGLE
  ========================================= */

  &-music-toggle {
    display:
      flex;
  }


  /* =========================================
     DARK THEME
  ========================================= */

  &-dark {
    color:
      var(--color-white-400);

    --icon-color:
      var(--color-white-400);
  }


  /* =========================================
     LOGO
  ========================================= */

  &-logo {
    cursor:
      pointer;

    display:
      flex;

    gap:
      var(--space-xs);

    transition:
      color 0.2s ease-in-out;

    opacity:
      var(--scrolled);

    pointer-events:
      none;


    &-clickable {
      pointer-events:
        all;
    }


    @include mixins.mq("md") {
      gap:
        var(--space-sm);
    }


    &-isProjectPage {
      transition:
        opacity 0.2s ease-in-out;

      pointer-events:
        none;

      opacity:
        0;
    }


    &-image {
      width:
        36px;


      @include mixins.mq("md") {
        width:
          40px;
      }
    }


    &-text {
      font-weight:
        900;

      font-size:
        18px;


      @include mixins.mq("md") {
        font-size:
          20px;
      }
    }
  }
}


.mobile-menu-toggle,
.mobile-section-menu {
  display: none;
}

@media (max-width: 1199px) {
  .header {
    display: grid;
    grid-template-columns: 44px minmax(0, 1fr) minmax(0, auto);
    align-items: center;
    width: 100%;
    height: auto;
    min-height: var(--height-header);
    padding: 9px 12px;
    gap: 6px;
    box-sizing: border-box;
  }

  .header-left,
  .header-logo,
  .header-right {
    position: static;
    min-width: 0;
    transform: none;
  }

  .header-left {
    grid-column: 1;
    grid-row: 1;
  }

  .header-logo {
    grid-column: 2;
    grid-row: 1;
    justify-self: center;
    max-width: 100%;
    overflow: hidden;
  }

  .header-right {
    grid-column: 3;
    grid-row: 1;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    width: max-content;
    min-width: 0;
    max-width: 48vw;
    gap: 5px;
    overflow: visible;
    justify-self: end;
  }

  .mobile-menu-toggle {
    position: static;
    grid-column: 1;
    grid-row: 1;
    z-index: 3;
    display: flex;
    width: 44px;
    height: 44px;
    padding: 10px 9px;
    flex-direction: column;
    justify-content: center;
    gap: 5px;
    border: 0;
    background: transparent;
    color: #ffffff;
    cursor: pointer;
    pointer-events: auto;
  }

  .mobile-menu-toggle span {
    display: block;
    width: 24px;
    height: 2px;
    flex: 0 0 2px;
    background: #ffffff;
  }

  .mobile-section-menu {
    position: absolute;
    top: 100%;
    left: 14px;
    z-index: 4;
    display: flex;
    width: min(168px, calc(100% - 28px));
    max-width: calc(100% - 28px);
    max-height: calc(100vh - 84px);
    padding: 8px;
    flex-direction: column;
    align-items: stretch;
    justify-content: flex-start;
    gap: 2px;
    overflow-y: auto;
    overflow-x: hidden;
    background: var(--color-background-400);
    color: var(--color-text-400);
    border: 1px solid var(--color-text-300);
    pointer-events: auto;
  }

  .mobile-section-menu button {
    flex: 0 0 auto;
    min-height: 44px;
    padding: 10px 12px;
    border: 0;
    background: transparent;
    color: inherit;
    text-align: left;
    cursor: pointer;
  }

  .mobile-section-menu button.mobile-section-active {
    color: var(--color-orange-400);
  }

  .header-resume-button {
    display: inline-flex;
    width: 78px;
    min-width: 78px;
    max-width: 78px;
    height: 44px;
    min-height: 44px;
    padding: 0 9px;
    gap: 4px;
    font-size: 10px;
    flex: 0 0 74px;
  }

  .header-resume-button .resume-download-icon {
    width: 16px;
    height: 16px;
    flex-basis: 16px;
    font-size: 18px;
  }

  .header-sounds-toggle {
    flex: 0 0 auto;
  }

  .header-sounds-toggle :deep(button) {
    width: 44px;
    height: 44px;
  }

  .header-logo-text {
    max-width: 100%;
  }

  @media (max-width: 390px) {
    .header {
      grid-template-columns: 42px minmax(0, 1fr) minmax(0, auto);
      padding-left: 10px;
      padding-right: 10px;
      gap: 4px;
    }

    .header-resume-button {
      width: 82px;
      min-width: 82px;
      max-width: 82px;
      flex-basis: 82px;
      padding-inline: 8px;
      font-size: 10px;
    }

    .header-sounds-toggle :deep(button) {
      width: 44px;
      height: 44px;
    }

    .header-right {
      gap: 3px;
      max-width: 46vw;
    }
  }
}
</style>
