<script setup>
import { ref, computed } from "vue";
import {
  LOCALSTORAGE_KEY,
  MAX_INVENTORY_COUNT,
  CREATE,
  EDIT,
  PREVIEW,
  APPROVE,
  ADD,
  RENTLY,
  NO_INVENTORY,
  CREATE_LIST,
  INVENTORY_LIST_OF,
  TOTAL_MAX_COUNT_INFO,
  SELECT_INFO,
} from "~/assets/util/constants";

import { APARTMENT_LIST_URL } from "~/assets/util/urls";

const EDITING_MODE = { CREATE: 0, EDIT: 1, PREVIEW: 2, APPROVE: 3 };

const route = useRoute();
const router = useRouter();

// get it from Localstorage
if (!process.client) router.replace(APARTMENT_LIST_URL);

const allStoredData = ref(
  process.client ? JSON.parse(localStorage.getItem(LOCALSTORAGE_KEY)) : []
);

const storedData = ref(
  allStoredData.value.find((data) => data.id === route.params.id)
);
const storedDataIdx = ref(
  allStoredData.value.findIndex((data) => data.id === route.params.id)
);
const itemList = ref(storedData.value?.inventoryList);

const notChosenItemList = computed(() => {
  return itemList.value.filter((item) => item.qty === 0);
});

const initialtotalInventoryCount = itemList.value.reduce(
  (accumulator, currentValue) => accumulator + currentValue.qty,
  0
);
let currentTotalInventoryCount = ref(initialtotalInventoryCount);

let editingMode = ref(
  notChosenItemList.value.length > 0 ? EDITING_MODE.CREATE : EDITING_MODE.EDIT
);
let inventoryAddItem = ref(notChosenItemList.value[0]);
let inventoryAddQty = ref(0);

const handleChangeEditMode = (mode) => {
  // if mode is approve, save the state and move to the list
  if (mode === EDITING_MODE.APPROVE) {
    // save state
    allStoredData.value[storedDataIdx.value].inventoryList = itemList.value;
    localStorage.setItem(LOCALSTORAGE_KEY, JSON.stringify(allStoredData.value));

    // redirect to apartment list
    router.replace("/apartments");
  }
  editingMode.value = mode;
};

const handeleChangeInventoryMaxCount = (param, value, type) => {
  const originalItem = itemList.value.find((item) => item[type] === param);
  // currentTotalInventoryCount.value -= originalItem.qty;
  const originalItemIdx = itemList.value.indexOf(originalItem);
  if (type === "id") itemList.value[originalItemIdx].qty = value;
  // currentTotalInventoryCount.value += value;

  currentTotalInventoryCount.value = itemList.value.reduce(
    (accumulator, currentValue) => accumulator + currentValue.qty,
    0
  );
};

const returnPageStepShouldBeMarked = (currentStep) => {
  if (currentStep <= editingMode.value) return true;
  return false;
};

const handleAddInventoryItem = (name, qty) => {
  const originalItem = itemList.value.find((item) => item.name === name);
  const originalItemIdx = itemList.value.indexOf(originalItem);

  itemList.value[originalItemIdx].qty = qty;
  // reset input value
  inventoryAddQty.value = 0;
};
</script>

<template>
  <div>
    <Head>
      <Title>{{ RENTLY }}</Title>
      <Meta
        name="description"
        :content="`${route.params.id} apartment's inventory list`"
      />
      <Meta name="keywords" content="rently" />
      <Meta property="og:title" content="rently" />
      <Meta property="og:site_name" content="rently" />
      <Meta property="og:description" content="" />
      <Meta property="og:keywords" content="rently" />
      <Meta property="og:url" content="https://www.rently.sg" />
      <Meta property="og:type" content="website" />
      <Link rel="canonical" href="https://www.rently.sg" />
    </Head>

    <!-- STEPS -->
    <!-- TODO: separate this UI to a component -->
    <ul class="steps steps-vertical lg:steps-horizontal w-full">
      <li class="step capitalize step-accent">
        {{ CREATE }}
      </li>
      <li
        :class="`step capitalize ${
          returnPageStepShouldBeMarked(EDITING_MODE.EDIT) ? 'step-accent' : ''
        }`"
      >
        {{ EDIT }}
      </li>
      <li
        :class="`step capitalize ${
          returnPageStepShouldBeMarked(EDITING_MODE.PREVIEW)
            ? 'step-accent'
            : ''
        }`"
      >
        {{ PREVIEW }}
      </li>
      <li class="step capitalize">
        {{ APPROVE }}
      </li>
    </ul>

    <section
      v-if="editingMode === EDITING_MODE.CREATE"
      class="max-full p-10 flex flex-col items-center"
    >
      <p class="text-3xl font-bold mb-10 capitalize">{{ NO_INVENTORY }}</p>
      <button
        class="btn btn-accent"
        @click="handleChangeEditMode(EDITING_MODE.EDIT)"
      >
        {{ CREATE_LIST }}
      </button>
    </section>

    <section v-else class="max-full p-10 flex flex-col items-center">
      <h2 class="capitalize mb-5 text-5xl font-bold text-center">
        {{ INVENTORY_LIST_OF }}
        <br />
        {{ storedData.address }} #{{ storedData.floor }}-{{
          storedData.doorNumber
        }}
      </h2>
      <p class="capitalize text-xl mb-10">
        {{ TOTAL_MAX_COUNT_INFO }}
        <em class="font-bold">
          {{ MAX_INVENTORY_COUNT }}
        </em>
      </p>
      <ul>
        <!-- Add inventory item with select -->
        <li
          v-if="
            notChosenItemList.length > 0 && editingMode < EDITING_MODE.PREVIEW
          "
          class="flex justify-between mb-10"
        >
          <select class="select select-accent mr-2" v-model="inventoryAddItem">
            <option disabled value="">{{ SELECT_INFO }}</option>
            <option
              v-for="item in notChosenItemList"
              :key="`item-add-id-${item.id}`"
            >
              {{ item.name }}
            </option>
          </select>
          <input
            class="input input-bordered mr-2"
            type="number"
            v-model="inventoryAddQty"
            @change="
              handeleChangeInventoryMaxCount(
                inventoryAddItem,
                inventoryAddQty,
                'name'
              )
            "
            min="0"
            :max="MAX_INVENTORY_COUNT - currentTotalInventoryCount"
            :disabled="editingMode >= EDITING_MODE.PREVIEW"
          />
          <button
            class="btn btn-success uppercase text-white"
            @click="handleAddInventoryItem(inventoryAddItem, inventoryAddQty)"
          >
            {{ ADD }}
          </button>
        </li>
        <!-- list -->
        <li
          class="flex items-center justify-between"
          v-for="inventory in itemList"
          :key="`inventory-id-${inventory.id}`"
        >
          <span v-if="inventory.qty > 0" class="mr-5 capitalize ml-2 text-xl">
            ✔ {{ inventory.name }}
          </span>
          <input
            v-if="inventory.qty > 0"
            class="input input-bordered"
            type="number"
            :name="inventory.id"
            :id="inventory.id"
            :value="inventory.qty"
            min="0"
            :max="
              MAX_INVENTORY_COUNT - currentTotalInventoryCount + inventory.qty
            "
            @change="
              (e) =>
                handeleChangeInventoryMaxCount(
                  e.target.id,
                  e.target.value * 1,
                  'id'
                )
            "
            :disabled="editingMode >= EDITING_MODE.PREVIEW"
          />
        </li>
      </ul>
      <button
        class="btn btn-accent btn-wide mt-10"
        @click="
          handleChangeEditMode(
            editingMode === EDITING_MODE.EDIT
              ? EDITING_MODE.PREVIEW
              : EDITING_MODE.APPROVE
          )
        "
      >
        {{ editingMode === EDITING_MODE.EDIT ? EDIT : APPROVE }}
      </button>
    </section>
  </div>
</template>
