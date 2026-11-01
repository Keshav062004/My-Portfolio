<script setup lang="ts">
import HeaderLink from "./HeaderLink.vue";
import { onMounted, onUnmounted, ref } from "vue";
import { t } from "../i18n/utils/translate";
import { lenis } from "../composables/useScroll";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useHeaderTheme } from "../composables/useHeaderTheme";
import { projectId } from "../composables/useRouteObserver";

const handleLinkClick = (link: string) => {
  if (!lenis.value) return;
  lenis.value.scrollTo(link);
};

type ActiveLink =
  | "about"
  | "education"
  | "certifications"
  | "skills"
  | "experience"
  | "projects"
  | "achievements"
  | "contact";

const activeLink = ref<ActiveLink | null>(null);

const sections: ActiveLink[] = [
  "about",
  "education",
  "certifications",
  "skills",
  "experience",
  "projects",
  "achievements",
  "contact",
];

const ariaLabels: Record<ActiveLink, string> = {
  about: t("about"),
  education: "Education",
  certifications: "Certifications",
  experience: "Experience",
  projects: t("projects"),
  skills: "Skills",
  achievements: "Achievements",
  contact: t("contact"),
};

const isMounted = ref(false);

const barStyle = ref({
  transform: "",
  width: "0px",
});

const {
  isDarkTheme,
  hasScrolledIntoView,
} = useHeaderTheme();

const updateBarPosition = () => {
  if (!activeLink.value) return;

  const links = document.querySelectorAll(
    ".header-home-link",
  );

  const index = sections.indexOf(
    activeLink.value,
  );

  if (index === -1 || !links[index]) return;

  const link = links[index] as HTMLElement;

  /*
   * Position the underline exactly underneath
   * the active navigation text/button.
   */
  barStyle.value = {
    transform: `translateX(${link.offsetLeft}px)`,
    width: `${link.offsetWidth}px`,
  };
};

const handleResize = () => {
  requestAnimationFrame(() => {
    updateBarPosition();
  });
};

onMounted(() => {
  sections.forEach((section) => {
    const element =
      document.getElementById(section);

    if (!element) return;

    ScrollTrigger.create({
      trigger: element,

      start:
        section === "about"
          ? "top 22.5%"
          : "top 45%",

      end:
        section === "about"
          ? "bottom 45%"
          : "bottom 45%",

      onEnter: () => {
        activeLink.value = section;

        requestAnimationFrame(() => {
          updateBarPosition();
        });
      },

      onEnterBack: () => {
        activeLink.value = section;

        requestAnimationFrame(() => {
          updateBarPosition();
        });
      },

      onLeave: () => {
        // Keep current active section.
      },

      onLeaveBack: () => {
        // Keep current active section.
      },
    });
  });

  window.addEventListener(
    "resize",
    handleResize,
  );

  ScrollTrigger.refresh();

  isMounted.value = true;

  requestAnimationFrame(() => {
    updateBarPosition();
  });
});

onUnmounted(() => {
  window.removeEventListener(
    "resize",
    handleResize,
  );

  ScrollTrigger.getAll().forEach(
    (trigger) => {
      const triggerElement =
        trigger.trigger;

      if (
        triggerElement instanceof Element &&
        sections.some(
          (section) =>
            triggerElement.id === section,
        )
      ) {
        trigger.kill();
      }
    },
  );
});
</script>

<template>
  <div
    :class="[
      'header-home',
      {
        'header-home-mounted':
          isMounted,

        'header-home-isProjectPage':
          projectId !== null,
      },
    ]"
  >
    <div
      :class="[
        'header-home-links',
        {
          'header-home-links-dark':
            isDarkTheme,
        },
      ]"
    >

      <!-- ACTIVE ORANGE UNDERLINE -->

      <div
        :class="[
          'header-home-bar',
          {
            'header-home-bar-active':
              activeLink !== null &&
              hasScrolledIntoView,

            'header-home-bar-dark':
              isDarkTheme,
          },
        ]"
        :style="barStyle"
      ></div>

      <!-- NAVIGATION LINKS -->

      <HeaderLink
        v-for="section in sections"
        :key="section"

        :is-active="
          activeLink === section
        "

        :class="[
          'header-home-link',
          {
            'header-home-link-active':
              activeLink === section &&
              hasScrolledIntoView,

            'children-unclickable':
              true,
          },
        ]"

        @click="
          handleLinkClick(
            '#' + section,
          )
        "

        :is-dark-theme="isDarkTheme"

        :aria-label="
          ariaLabels[section]
        "

        data-sound="click"
        data-hoversound="hover"
      >
        {{
          section === "education" ||
          section === "certifications" ||
          section === "experience" ||
          section === "skills" ||
          section === "achievements"
            ? ariaLabels[section]
            : t(section)
        }}
      </HeaderLink>

    </div>
  </div>
</template>

<style scoped lang="scss">

.header-home {
  position: fixed;

  top: 0;
  left: 50%;

  transform:
    translateX(-50%);

  z-index:
    var(--z-index-header-home);

  height:
    var(--height-header);

  align-items: center;
  justify-content: center;

  display: none;

  opacity: 0;

  transition:
    opacity 0.3s ease-in-out,
    transform
      var(--transition-route-duration)
      var(--transition-route-ease);

  &-isProjectPage {
    transform:
      translateX(-50%)
      translateY(-100%);
  }

  &-mounted {
    opacity: 1;
  }

  @media (min-width: 1200px) {
    display: flex;
  }

  /* =========================================
     MAIN HEADER CONTAINER
  ========================================= */

  &-links {
    position: relative;

    display: flex;

    align-items: center;
    justify-content: center;
    gap: 6px;
    white-space: nowrap;

    padding: 5px;

    width: max-content;

    background:
      rgba(239, 229, 211, 0.94);

    border:
      1px solid
      rgba(255, 255, 255, 0.8);

    border-radius:
      999px;

    color:
      var(--color-text-400);

    box-shadow:
      0 8px 25px
      rgba(45, 40, 34, 0.08),

      inset 0 1px 0
      rgba(255, 255, 255, 0.85);

    backdrop-filter:
      blur(12px);

    -webkit-backdrop-filter:
      blur(12px);

    transition:
      color 0.2s ease-in-out,
      background-color 0.2s ease-in-out,
      box-shadow 0.2s ease-in-out;
  }

  .header-home-links :deep(.header-home-link) {
    width: auto;
    min-width: 0;
    flex: 0 0 auto;
    padding-inline: clamp(5px, 0.55vw, 8px);
    font-size: clamp(0.72rem, 0.8vw, 0.85rem);
    white-space: nowrap;
  }

  /* =========================================
     DARK HEADER
  ========================================= */

  &-links-dark {
    background:
      rgba(8, 35, 82, 0.94);

    color:
      var(--color-white-400);

    border-color:
      rgba(255, 255, 255, 0.08);

    box-shadow:
      0 8px 30px
      rgba(0, 0, 0, 0.2),

      inset 0 1px 0
      rgba(255, 255, 255, 0.08);
  }

  /* =========================================
     ACTIVE ORANGE UNDERLINE
  ========================================= */

  &-bar {
    position: absolute;

    /*
     * Put underline near the bottom
     * of the navigation container.
     */
    top: auto;
    bottom: 2px;

    left: 0;

    height: 3px;

    width: 0;

    background:
      var(--color-orange-400);

    border-radius:
      999px;

    z-index: 1;

    pointer-events: none;

    opacity: 0;

    /*
     * Smoothly follows active word.
     */
    transition:
      transform 0.45s var(--ease-smooth),
      width 0.4s var(--ease-smooth),
      opacity 0.2s ease-in-out,
      background-color 0.2s ease-in-out;

    box-shadow:
      0 2px 8px
      rgba(255, 138, 0, 0.35);

    &-dark {
      background:
        var(--color-orange-400);

      box-shadow:
        0 2px 8px
        rgba(255, 138, 0, 0.3);
    }

    &-active {
      opacity: 1;
    }
  }

  /* =========================================
     NAVIGATION BUTTONS
  ========================================= */

  &-link {
    position: relative;

    z-index: 2;

    min-width:
      145px;

    width:
      auto !important;

    padding:
      0 24px;

    margin: 0;

    font-size:
      var(--font-size-md);

    font-weight:
      700;

    letter-spacing:
      0.025em;

    line-height: 1;

    white-space:
      nowrap;

    text-transform:
      uppercase;

    border:
      none;

    background:
      transparent !important;

    box-shadow:
      none !important;

    outline:
      none;

    flex:
      0 0 auto;

    transition:
      color 0.25s ease-in-out,
      transform 0.25s ease-in-out;

    /* =====================================
       ACTIVE TEXT
    ===================================== */

    &-active {
      color:
        var(--color-orange-400) !important;

      background:
        transparent !important;

      box-shadow:
        none !important;
    }

    /* =====================================
       HOVER
    ===================================== */

    &:hover {
      background:
        transparent !important;

      box-shadow:
        none !important;

      transform:
        translateY(-1px);
    }
  }

  /* =========================================
     FORCE NO ACTIVE BACKGROUND
  ========================================= */

  .header-home-link,
  .header-home-link-active {
    background:
      transparent !important;

    box-shadow:
      none !important;
  }
}

</style>