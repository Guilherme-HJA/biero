<script setup lang="ts">
//Files
import API from '../api/main.ts'
import alphabet from '../utils/alpha.ts'

//Components
import SearchBar from '../components/SearchBar.vue'
//import DrinkCard from '../components/DrinkCard.vue'

//Vue
import { ref } from 'vue'
import { defineAsyncComponent } from 'vue'

const DrinkCard = defineAsyncComponent(
  () => import('../components/DrinkCard.vue'),
)

const query = ref('')
const drinks = ref()
const title = ref()
const loaded = ref(false)

const searchDrinks = async (drink: String) => {
  loaded.value = false
  drinks.value =
    drink.length === 1
      ? await API.methods.search('f', drink)
      : await API.methods.search('s', drink)
  title.value =
    drink.length === 1 ? `Starting with '${drink}'` : drink.toUpperCase()
  query.value = ''
  loaded.value = true
}
</script>

<template>
  <div>
    <section class="hero">
      <div class="hero__image">
        <img
          src="../assets/biero_logo.svg"
          width="400"
          height="400"
          alt="biero_logo"
        />
      </div>
      <h1>A journey of unforgettable <span>tastes</span> awaits you...</h1>
    </section>
    <section>
      <div class="search_field">
        <SearchBar v-on:keyup.enter="searchDrinks(query)" v-model="query" />
        <div class="letters-navigation">
          <button
            class="letters"
            v-for="letter in alphabet"
            @click="searchDrinks(letter)"
          >
            {{ letter }}
          </button>
        </div>
      </div>
    </section>
    <Transition>
      <section class="cards-space" v-show="loaded">
        <div class="drink-title">
          <h1 v-if="drinks !== null">{{ title }}</h1>
          <h1 v-else>Nothing here but Us</h1>
        </div>
        <Suspense>
          <div class="cards-container">
            <DrinkCard
              v-for="drink in drinks"
              :key="drink.idDrink"
              :drink="drink"
              :from-list="false"
            />
          </div>
          <template #fallback> Loading... </template>
        </Suspense>
      </section>
    </Transition>
  </div>
</template>

<style lang="scss">
@import '../styles/variables';

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.hero {
  margin-bottom: 2em;
  padding: 1em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  &__image {
    transform: translateX(-20px);
    img {
      height: auto;
      max-width: 100%;
    }
  }

  h1 {
    margin-top: 2em;
    color: $white;
    font-size: 2rem;

    span {
      color: $green;
    }
  }
}

.letters-navigation {
  margin-top: 3em;

  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 10px;

  .letters {
    width: 55px;
    height: 55px;

    color: $white;
    background-color: transparent;
    border: 2px solid $white;

    transition: all 0.5s ease;

    &:hover {
      color: $primary;
      background-color: $green;
      border: 2px solid $green;

      transform: translateY(-10px);
    }
  }

  @media screen and (max-width: 35em) {
    overflow-x: scroll;
    flex-direction: row;
    flex-wrap: nowrap;
    height: 60px;
    justify-content: flex-start;

    &::-webkit-scrollbar {
      display: none;
    }

    .letters {
      display: flex;
      justify-content: center;
      align-content: center;
      width: 30px;
      height: 35px;
      text-align: center;

      font-size: 0.8rem;
    }
  }
}

.drink-title {
  margin-top: 3em;
  h1 {
    font-size: 3rem;
    color: $white;
  }
}

.cards-container {
  margin-top: 3em;

  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(425px, 1fr));
  row-gap: 2em;
  justify-items: center;
  align-content: center;
  justify-content: center;
}

@media (max-width: 35em) {
  .cards-space {
    .drink-title {
      h1 {
        font-size: 2.5rem;
      }
    }
  }
}

/* Vue Transition */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
