<script setup lang="ts">
import { onBeforeMount, ref } from "vue";
import { useRoute, useRouter } from "vue-router";
import { useFetch } from "../utils/auxFunctions";

import type { returnVanDetails, VanData } from "../config/types";
import type { Ref } from "vue";

const { params } = useRoute();
const router = useRouter();
const vanData: Ref<VanData | undefined> = ref(undefined);
const fetchError = ref(false);
const errorMessage = ref("");
const fetchLoading = ref(true);

onBeforeMount(async () => {
  const { data, isLoading, isError, error } = await useFetch<returnVanDetails>(
    `/api/vans/${params.id}`
  );
  vanData.value = data.value?.vans;
  fetchError.value = isError.value;
  errorMessage.value = error.value;
  fetchLoading.value = isLoading.value;
});
</script>

<template>
  <main v-if="fetchLoading" class="fallbackContainer">
    <h3 class="loadingText">Loading details now</h3>
  </main>
  <main v-else-if="fetchError" class="fallbackContainer">
    <p class="errorText">{{ errorMessage }}</p>
  </main>
  <main v-else>
    <a class="breadcrumbLink" @click.prevent="router.back()">
      👈
      <span class="breadcrumbText">
        Back to {{ $route.query.type ? $route.query.type : "all" }} vans</span
      >
    </a>
    <img
      v-if="vanData?.imageUrl"
      :src="vanData.imageUrl"
      alt="Image of Van"
      class="vanImg"
    />
    <p v-else class="imgFallbackText">Image loading</p>
    <section class="vanDetailsContainer">
      <span :class="`${vanData?.type}Tag tag`">{{ vanData?.type }}</span>
      <h1 class="title">{{ vanData?.name }}</h1>
      <span class="priceTag">${{ vanData?.price }}<small>/day</small></span>
      <p>{{ vanData?.description }}</p>
      <button class="btn rentBtn">Rent this van</button>
    </section>
  </main>
</template>

<style scoped>
main {
  max-width: 700px;
  margin: auto;
}

.breadcrumbLink {
  display: block;
  width: 90%;
  margin: 1rem auto;
  cursor: pointer;
}

.breadcrumbText {
  text-decoration: underline;
}

.vanImg {
  display: block;
  width: 90%;
  margin: auto;
}

.imgFallbackText {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 300px;
}

.vanDetailsContainer {
  width: 90%;
  padding: 1rem 1rem 1rem 0;
  max-width: 700px;
  max-height: 700px;
  margin: 1rem auto;
}

.rentBtn {
  background-color: #e17654;
  color: #fff;
  font-weight: 500;
  width: 100%;
}

.rentBtn:hover {
  transform: translate(1px, 1px);
}
</style>
