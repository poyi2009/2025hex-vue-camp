<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div role="navigation">
    <ul class="nav nav-tabs">
      <li class="nav-item">
        <RouterLink to="/" class="nav-link">Home</RouterLink>
      </li>
      <li class="nav-item">
        <RouterLink to="/about" class="nav-link">About</RouterLink>
      </li>
      <li class="nav-item">
        <RouterLink to="/week1" class="nav-link">Week1</RouterLink>
      </li>
      <li class="nav-item">
        <RouterLink to="/review" class="nav-link">Review</RouterLink>
      </li>
      <li class="nav-item">
        <RouterLink to="/week2" class="nav-link">Week2</RouterLink>
      </li>
    </ul>
  </div>
  <RouterView />
</template>
