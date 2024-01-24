<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <div class="wrapper">
      <nav>
        <RouterLink to="/" class="logo">
          <img src="../src/components/icons/pokebola.png" alt="pokeball">
          Pokedex
        </RouterLink>
      </nav>
    </div>
    <RouterView />
  </header>

</template>

<style scoped>

* {
  font-family: 'PixelMplus', sans-serif;
}

.logo{
  text-decoration: none;
  color: #fffa33;
  transition: 0.4s;
}

nav {
  width: 100vw;
  background-color: #ef5350;
  height: 4rem;
  font-size: 12px;
  text-align: center;
}

a img {
  height: 50%;
}

nav a {
  display: inline-block;
  padding: 0 2rem;
  font-size: 17px;
  font-weight: 600;
  border: 0;
  display: flex;
  height: 100%;
  width: max-content;
  align-items: center;
}


@media (min-width: 1024px) {

  .logo {
    margin: 0 2rem 0 0;
  }

  .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    margin-left: -1rem;
    font-size: 1rem;
  }
}
</style>
