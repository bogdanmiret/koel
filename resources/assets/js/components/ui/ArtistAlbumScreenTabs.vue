<template>
  <div class="tabs">
    <header>
      <slot name="header"/>
    </header>
    <main>
      <slot/>
    </main>
  </div>
</template>

<script lang="ts" setup>
</script>

<style lang="scss" scoped>
::v-deep(.tabs) {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  flex: 1;
  color: var(--color-text-secondary);
}

::v-deep(header) {
  display: flex;
  background: rgba(0, 0, 0, 0.05);
  overflow: hidden;
  flex-shrink: 0;
  border-bottom: 1px solid rgba(255, 255, 255, .04);

  label {
    font-size: 1rem;
    position: relative;
    padding: 1rem 1.8rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    border-radius: 0;
    opacity: .5;
    cursor: pointer;
    transition: opacity .2s ease-in-out;

    &:hover {
      opacity: .8;
    }

    &.active {
      opacity: 1;
    }

    input {
      display: none;
    }
  }
}

::v-deep(main) {
  flex: 1;
  overflow: auto;
}
</style>
