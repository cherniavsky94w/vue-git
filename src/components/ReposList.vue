<template>
  <v-flex xs8 sm8 md8>
    <v-flex class="ma-3">
      <v-text-field
        flat
        prepend-inner-icon="search"
        label="Search"
        v-model="query.q"
        solo
        @keypress.enter="reloadResults"
        :hint="`Total: ${total} (Press enter for search)`"
        :loading="isLoading"
        persistent-hint
      ></v-text-field>
    </v-flex>
    <v-flex>
      <v-card v-for="item in repos" :key="item.id" class="mx-auto ma-3">
        <v-card-title>
          <a :href="item.html_url" target="_blank" class="title font-weight-light">{{ item.name }}</a>
        </v-card-title>

        <v-card-text v-text="item.description"></v-card-text>

        <v-list-tile class="grow">
          <template v-if="item.owner">
            <v-list-tile-avatar color="grey darken-3">
              <v-img class="elevation-6" :src="item.owner.avatar_url"></v-img>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <a :href="item.owner.html_url" target="_blank">
                <v-list-tile-title v-text="item.owner.login"></v-list-tile-title>
              </a>
            </v-list-tile-content>
          </template>
        </v-list-tile>

        <v-card-actions>
          <v-list-tile class="grow">
            <v-layout align-center justify-end>
              <v-icon class="ma-1">stars</v-icon>
              <span class="subheading mr-2">{{ item.stargazers_count }}</span>
              <!-- <span class="ma-1">·</span> -->
              <v-icon class="ma-1">visibility</v-icon>
              <span class="subheading mr-2">{{ item.watchers }}</span>
              <!-- <span class="ma-1">·</span> -->
              <v-icon class="ma-1">device_hub</v-icon>
              <span class="subheading mr-2">{{ item.forks_count }}</span>
              <v-icon class="ma-1">error_outline</v-icon>
              <span class="subheading">{{ item.open_issues_count }}</span>
            </v-layout>
          </v-list-tile>
        </v-card-actions>
      </v-card>
      <infinite-loading :identifier="infinite" @infinite="fetchRepos"></infinite-loading>
    </v-flex>
  </v-flex>
</template>

<script>
export default {
  components: {
    InfiniteLoading: () => import("vue-infinite-loading")
  },
  data: () => ({
    total: 0,
    repos: [],
    query: { q: "javascript", sort: "stars", order: "desc", page: 1 },
    url: "https://api.github.com/search/repositories",
    infinite: +new Date(),
    isLoading: true
  }),
  methods: {
    reloadResults() {
      this.repos = [];
      this.total = 0;
      this.infinite = +new Date();
    },
    async fetchRepos($state) {
      try {
        this.isLoading = true;
        const { data } = await this.$axios.get(this.url, {
          params: this.query
        });
        this.repos.push(...data.items);
        this.total = data.total_count;
        console.log({ data });
        data.items.length == 0 ? $state.complete() : $state.loaded();
        this.query.page += 1;
      } catch (err) {
        console.error(err);
        $state.error();
      }
      this.isLoading = false;
    }
  }
};
</script>

<style>
.v-card {
  border-radius: 20px;
}

.v-card a {
  text-decoration: none;
  color: #333;
  cursor: pointer;
}
</style>
