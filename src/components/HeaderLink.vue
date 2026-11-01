<script setup lang="ts">
const props = defineProps<{
  isActive?: boolean;
  isDarkTheme?: boolean;
}>();
</script>

<template>
  <button
    class="header-link"
    data-cursor="circle-white"
    :class="{
      'header-link-active': props.isActive,
      'header-link-dark': props.isDarkTheme,
    }"
  >
    <span class="header-link-text">
      <slot></slot>
    </span>
  </button>
</template>

<style scoped lang="scss">

.header-link {
  position: relative;

  letter-spacing: 0.02em;

  font-weight: 700;

  border: none;

  background: transparent !important;

  transition:
    color 0.25s ease-in-out,
    transform 0.25s ease-in-out;

  font-size:
    var(--font-size-md);

  width:
    auto;

  white-space:
    nowrap;

  text-transform:
    uppercase;

  z-index:
    2;

  border-radius:
    100px;

  padding:
    var(--space-xxs) 0;

  color:
    var(--color-text-400);

  cursor:
    pointer;

  outline:
    none;

  /*
   * IMPORTANT:
   * No background pseudo-element.
   * The old black pill has been completely removed.
   */

  &::after {
    content: none !important;
    display: none !important;
  }


  /* =========================================
     ACTIVE LINK
  ========================================= */

  &-active {
    color:
      var(--color-orange-400) !important;

    background:
      transparent !important;

    box-shadow:
      none !important;
  }


  /* =========================================
     DARK THEME
  ========================================= */

  &-dark {
    color:
      var(--color-white-400);

    background:
      transparent !important;
  }


  &-dark.header-link-active {
    color:
      var(--color-orange-400) !important;

    background:
      transparent !important;
  }


  /* =========================================
     HOVER
  ========================================= */

  @include mixins.hover {

    &:hover {
      color:
        var(--color-orange-400);

      background:
        transparent !important;

      box-shadow:
        none !important;

      transform:
        translateY(-1px);
    }

  }


  /* =========================================
     FOCUS
  ========================================= */

  &:focus,
  &:focus-visible {
    outline:
      none;

    background:
      transparent !important;

    box-shadow:
      none !important;
  }
}

</style>