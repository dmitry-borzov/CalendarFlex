<template>
  <div class="wrapper">
    <div class="content">
      <div class="header">
        <button v-if="year > currentYear" @click="--year">&lt;</button>
        <div class="title">{{year}}</div>
        <button @click="++year">&gt;</button>
      </div>
      <div class="body">
        <calendar :year="year"></calendar>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      year: (new Date()).getFullYear(),
      currentYear: (new Date()).getFullYear(),
    }
  }
}
</script>

<style lang="scss">
  html {
    height: 100%;
    font-family: "Segoe UI Semibold";
  }

  body {
    height: 100%;
    margin: 0;
  }
</style>

<style lang="scss" scoped>
  .title {
    font-weight: bold;
    font-size: 1.5em;
    margin: 0.25em;
    text-align: center;
  }

  .header {
    padding: 0.25em;
    display: flex;
    flex-shrink: 0;
    justify-content: space-between;
  }

  .wrapper { // ограничивает высоту
    display: flex;
  }

  .content { // непосредственный контейнер с заголовком и содержимым
    display: flex;
    flex-direction: column;
  }

  .body { // основное тело контейнер
    flex-grow: 1; // растягивается чтобы заполнить  все пространство
    overflow-y: auto; // скролл если не влезает
    background-color: rgb(23, 55, 93);
    max-width: 767px;
    color: white;
  }
</style>
