<template>
  <div>
    <p>count: {{ count }}</p>
    <p>double: {{ double }}</p>
    <ul>
      <li v-for="item in numbers" :key="item">{{ item }}</li>
    </ul>
    <h3>{{ person.name }}</h3>
    <button @click="add">add</button>

    <h1>x: {{ x }}----y: {{ y }}</h1>

    <hr />

    <h1 v-if="loading">Loading...</h1>
    <img v-if="loaded" :src="result[0].url" height="200" />
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  computed,
  reactive,
  toRefs,
  onMounted,
  onUnmounted,
  onUpdated,
  watch,
  ref,
} from "vue";

import useMousePosition from "./hooks/useMousePosition";
import useURLLoader from "./hooks/useURLLoader";

interface DataProps {
  count: number;
  double: number;
  add: () => void;
  numbers: number[];
  person: { name?: string };
}

interface DogResult {
  message: string;
  status: string;
}

interface CatResult {
  id: string;
  url: string;
  width: number;
  height: number;
}

export default defineComponent({
  name: "App",
  setup() {
    // const count = ref(1)
    // const double = computed(() => {
    //   return count.value * 2
    // })
    // const add = () => {
    //   count.value++
    // }

    const data: DataProps = reactive({
      count: 0,
      add: () => {
        data.count++;
      },
      double: computed(() => data.count * 2),
      numbers: [1, 2, 3, 4],
      person: {},
    });

    const { x, y } = useMousePosition();

    // const { result, loading, loaded } = useURLLoader<DogResult>(
    //   "https://dog.ceo/api/breeds/image/random"
    // );

    const { result, loading, loaded } = useURLLoader<CatResult[]>(
      "https://api.thecatapi.com/v1/images/search?limit=1"
    );

    watch(result, () => {
      if (result.value) {
        console.log("value", result.value[0].url);
      }
    });

    data.numbers[0] = 5;
    data.person.name = "123";
    watch(
      () => data.count,
      (newVal, oldVaL) => {
        console.log("newVal", newVal);
        console.log("oldVaL", oldVaL);
      }
    );
    onUpdated(() => {
      console.log("updated");
    });

    return {
      ...toRefs(data),
      x,
      y,
      result,
      loading,
      loaded,
      // count,
      // double,
      // add
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
