<script setup lang="ts">
import TopNavBar from "@/layout/topNavBar/TopNavBar.vue";
import Footer2 from "./Footer2.vue";

import { useLayoutStore } from "@/hooks/useLayoutStore";
import ActionSheet from "@/views/ActionSheet.vue";
import Drawer3 from "./drawer/Drawer3.vue";
const layout = useLayoutStore();



</script>

<template>
  <v-layout class="rounded rounded-md">
    <Drawer3 v-if="!layout.isPwa" />

    <!-- ActionSheet -->
    <ActionSheet />

    <TopNavBar />
    <!-- <TopNavBar v-if="layout.isPwa || isMobile" /> -->

    <v-main>
      <router-view></router-view>
    </v-main>
    <!-- <v-main :style="layout.isPwa ||
      layout.isOpenSearchDrawer
      ? `height: 100vh; overflow: hidden;`
      : ''
      ">
      <router-view></router-view>
    </v-main> -->

    <!-- <Footer /> -->
    <!-- <SpeedDial /> -->
    <Footer2 v-if="layout.isPwa" />
  </v-layout>
</template>
