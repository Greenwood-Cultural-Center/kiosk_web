<script setup>
</script>

<template>
  <div class="fab">
    <input type="checkbox" name="fabToggle" class="fabToggle" aria-label="floating action button" />
    <a class="fabButton" href="#"><img src="/gcc.png" alt="Greenwood Cultural Center Icon"/></a>
    <div class="fabButtons">
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
  .fab {
    position: fixed;
    bottom: 12rem;
    left: 1rem;
    z-index: 10;
  }

  .fabButton {
    display: block;
    background-color: var(--gcc-dk-green);
    color: var(--gcc-orange);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    font-weight: bold;
    font-size: 2rem;
    width: 9rem;
    height: 9rem;
    position: relative;
    z-index: 10;
  }

  .fabButton img {
    position: absolute;
    top: 9%;
    left: 15%;
    width: 6.3rem;
    height: 6.3rem;
  }

  .fabToggle {
    -webkit-appearance: none;
    appearance: none;
    position: absolute;
    border-radius: 50%;
    top: 0; left: 0;
    margin: 0;
    width: 100%;
    height: 100%;
    cursor: pointer;
    background-color: transparent;
    border: none;
    outline: none;
    z-index: 11;
    transition: box-shadow .2s ease-in-out;
    box-shadow: 0 3px 5px 1px rgba(51, 51, 51, .3);
  }

  .fabButton:hover {
    background-color: var(--gcc-lt-green);
    color: #fff;
  }

  .fabButtons {
    position: absolute;
    width: 100%;
    bottom: 120%;
    text-align: center;
  }

  @media (max-width: 2500px) and (max-height: 1400px) {
    .fab {
      position: fixed;
      bottom: 1rem;
      left: 1rem;
      z-index: 10;
    }
  }
</style>