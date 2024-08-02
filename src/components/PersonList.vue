<template>
  <ul class="person-list">
    <slot />
  </ul>
</template>

<script setup></script>

<style scoped lang="scss">
.person-list {
  display: flex;
  width: 720px;
  overflow: hidden;
  padding: 0;
  margin: 0;
  gap: 40px;
  overflow-x: auto;
  &::-webkit-scrollbar {
    display: none;
  }
}
</style>
