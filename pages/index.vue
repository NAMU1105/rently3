<script setup>
import {
  LOCALSTORAGE_KEY,
  GO_TO_APARTMENT_LIST,
} from "~/assets/util/constants";
import { DUMMY_APARTMENT_DATA } from "~/assets/data/data";

if (process.client) {
  const savedData = localStorage.getItem(LOCALSTORAGE_KEY);
  if (!savedData)
    localStorage.setItem("apartment", JSON.stringify(DUMMY_APARTMENT_DATA));
}
</script>

<template>
  <main
    class="hero min-h-screen"
    style="background-image: url(https://placeimg.com/1000/800/arch)"
  >
    <h1 class="sr-only">home page</h1>
    <div class="hero-overlay bg-opacity-60"></div>
    <div class="hero-content text-center text-neutral-content">
      <section class="max-w-md">
        <h2 class="mb-5 text-5xl font-bold">Hello there</h2>
        <p class="mb-5">Thanks for the apportunity!</p>
        <NuxtLink class="btn btn-accent" to="/apartments">{{
          GO_TO_APARTMENT_LIST
        }}</NuxtLink>
      </section>
    </div>
  </main>
</template>
