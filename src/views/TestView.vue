<script lang="ts" setup>
import { basePokemonApi } from "@/constants";
import axios from "axios";
import { ref } from "vue";
import { useInfiniteQuery } from "vue-query";
import Container from "@/components/viewContainer/Container.vue";
import { RefScroll } from "@/types/RefScroll";
import TinyCard from "@/components/card/TinyCard.vue";
import { usePokemonItemStore } from "@/hooks/usePokemonItemStore";
import { getUrlId } from "@/utils/function";
import { useLayoutStore } from "@/hooks/useLayoutStore";
import TinyCardSkeleton from "@/components/loading/TinyCardSkeleton.vue";
import Card from "@/components/card/Card.vue";
const peopleFetcher = async ({ pageParam = 1 }) => {
  const num = 50;
  const response = await axios.get(basePokemonApi + "pokemon", {
    params: {
      offset: (pageParam - 1) * num,
      limit: num,
    },
  });

  return {
    pageData: response.data || [],
    cursor: pageParam + 1,
  };
};

const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetching,
  isLoading,
  isFetchingNextPage,
} = useInfiniteQuery({
  queryKey: ["getPokmonSpecies"],
  queryFn: peopleFetcher,
  getNextPageParam: (lastPage) => {
    return lastPage.cursor;
  },
  refetchOnWindowFocus: false,
});

const nextPage = () => {
  fetchNextPage.value();
};

const scrollContainer = ref<RefScroll | null>(null);

const scrolling = (e: any) => {
  const clientHeight = e.target.clientHeight;
  const scrollHeight = e.target.scrollHeight;
  const scrollTop = e.target.scrollTop;
  if (
    scrollTop + clientHeight + 500 >= scrollHeight &&
    (!hasNextPage || isFetchingNextPage)
  ) {
    fetchNextPage.value();
  }
};

const pokemonItem = usePokemonItemStore();
const layout = useLayoutStore();
const handleToggle = ({ name, url }: { name: string; url: string }) => {
  pokemonItem.name = name;
  pokemonItem.id = getUrlId(url);
  pokemonItem.isPokemonItemOpen = !pokemonItem.isPokemonItemOpen;
};
</script>
<template>
  <Container ref="scrollContainer" @scroll="scrolling">
    <TinyCardSkeleton v-if="isLoading" />
    <v-row v-if="data" class="mt-2" v-for="(page, index) in data?.pages" :key="index">
      <v-col class="d-flex justify-center" :cols="6" sm="6" md="6" v-for="item in page.pageData.results" :key="item.name">
        <div @click="handleToggle({ ...item })">
          <TinyCard :title="item.name" :id="getUrlId(item.url)" :isLoading="isLoading" />
          <!-- <Card :title="item.name" :id="getUrlId(item.url)" :isLoading="isLoading" /> -->
        </div>
      </v-col>
    </v-row>
    <div class="d-flex justify-center mt-4" v-if="isFetching">
      <v-progress-circular indeterminate color="purple"></v-progress-circular>
    </div>
    <v-btn @click="nextPage" v-if="hasNextPage && !isFetching" class="mt-4 align-center" style="width: 100vw">
      Load More Data
    </v-btn>
  </Container>
</template>
