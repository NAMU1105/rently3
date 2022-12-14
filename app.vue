<script setup>
import { RENTLY } from "~/assets/util/constants";
</script>

<template>
  <Head>
    <Title>{{ RENTLY }}</Title>
    <Meta name="description" content="apartment list`" />
    <Meta name="keywords" content="rently" />
    <Meta property="og:title" content="rently" />
    <Meta property="og:site_name" content="rently" />
    <Meta property="og:description" content="" />
    <Meta property="og:keywords" content="rently" />
    <Meta property="og:url" content="https://www.rently.sg" />
    <Meta property="og:type" content="website" />
    <Link rel="canonical" href="https://www.rently.sg" />
  </Head>

  <NuxtPage />
</template>
