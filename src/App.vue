<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <RouterView />
</template>

<style>
/* Global styles for Vue Flow */
:root {
  --edge-color: #b1b1b7;
  --edge-path-color: #555;
  --edge-path-selected-color: #1a192b;
  --edge-label-color: #222;
  --edge-width: 2px;
}

.vue-flow__edge {
  pointer-events: all;
}

.vue-flow__edge-path {
  stroke: var(--edge-path-color);
  stroke-width: var(--edge-width);
  fill: none;
}

.vue-flow__edge-text {
  font-size: 12px;
  fill: var(--edge-label-color);
}

.vue-flow__edge.selected .vue-flow__edge-path {
  stroke: var(--edge-path-selected-color);
  stroke-width: 3px;
}

.vue-flow__edge.animated .vue-flow__edge-path {
  stroke-dasharray: 5;
  animation: dashdraw 0.5s linear infinite;
}

@keyframes dashdraw {
  from {
    stroke-dashoffset: 10;
  }
}

/* Custom edge styles for YES/NO paths */
.vue-flow__edge.yes-path .vue-flow__edge-path {
  stroke: #52c41a;
  stroke-width: 2px;
}

.vue-flow__edge.no-path .vue-flow__edge-path {
  stroke: #f5222d;
  stroke-width: 2px;
}
</style>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
