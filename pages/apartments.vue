<script setup>
import { ref } from "vue";
import { APARTMENT_LIST, GO_TO_INVENTORY_LIST } from "~/assets/util/constants";

const apartmentList = ref([]);
const route = useRoute();
</script>

<template>
  <section
    v-if="!!apartmentList"
    class="max-full p-10 flex flex-col items-center"
  >
    <h1 class="sr-only">{{ APARTMENT_LIST }}</h1>
    <NuxtPage v-if="route.params.id" />
    <BaseDisplay
      v-else
      :title="APARTMENT_LIST"
      v-model:itemList="apartmentList"
    >
      <template v-slot:items>
        <li
          v-for="apartment in apartmentList"
          :key="`apartment-id-${apartment.id}`"
        >
          <!-- TODO: paddstrt -->
          <div class="card w-96 bg-base-100 shadow-xl mb-5">
            <div class="card-body">
              <h2 class="card-title mb-6">
                {{ apartment.address }} #{{ apartment.floor }}-{{
                  apartment.doorNumber
                }}
              </h2>
              <div class="card-actions justify-center">
                <NuxtLink
                  class="btn btn-accent text-white"
                  :to="`/apartments/${apartment.id}`"
                >
                  {{ GO_TO_INVENTORY_LIST }}</NuxtLink
                >
              </div>
            </div>
          </div>
        </li>
      </template>
    </BaseDisplay>
  </section>
</template>

<style lang="scss"></style>
