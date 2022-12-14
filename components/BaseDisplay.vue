<script setup>
import { defineEmits, defineProps } from "vue";
import { LOCALSTORAGE_KEY } from "~/assets/util/constants";

const props = defineProps({
  itemList: {
    type: Array,
    default: () => [],
    required: true,
  },
  title: {
    type: String,
  },
});

const emit = defineEmits(["update:itemList"]);
if (process.client) {
  const savedData = localStorage.getItem(LOCALSTORAGE_KEY);
  emit("update:itemList", JSON.parse(savedData));
}
</script>

<template>
  <main class="max-full p-10 flex flex-col items-center">
    <h1 class="uppercase mb-5 text-5xl font-bold">{{ title }}</h1>
    <ul class="">
      <slot name="items" :itemList="itemList" />
    </ul>
  </main>
</template>

<style></style>
