<template>
  <v-card :loading="loading" class="mx-auto my-12" max-width="374" v-for="item in prodotti" :key="item.id">
    <template v-slot:loader="{ isActive }">
      <v-progress-linear :active="isActive" color="deep-purple" height="4" indeterminate></v-progress-linear>
    </template>

    <v-img cover height="250" :src="item.image"></v-img>

    <v-card-item>
      <v-card-title>{{ item.title }}</v-card-title>
    </v-card-item>

    <v-card-text>
      <v-row align="center" class="mx-0">
        <v-rating :model-value="4.5" color="amber" density="compact" half-increments readonly size="small"></v-rating>

        <div class="text-grey ms-4">
          {{ item.rating.rate }} ({{ item.rating.count }})
        </div>
      </v-row>

      <div class="my-4 text-subtitle-1">
        {{ item.price }} $
      </div>

      <div>{{ item.description }}</div>
    </v-card-text>

    <v-divider class="mx-4 mb-1"></v-divider>

    <div class="px-4">
      <v-chip-group v-model="selection">
        <router-link :to="{ name: 'category', params: { categoryName: item.category } }">
          <v-chip>{{ item.category }}</v-chip>
        </router-link>
      </v-chip-group>
    </div>

    <v-card-actions>
      <v-btn color="deep-purple-lighten-2" variant="text" @click="reserve">
        View
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      prodotti: [],
      loading: false,
      selection: 1
    }
  },
  methods: {
    getProdotti() {
      fetch('https://fakestoreapi.com/products')
        .then((res) => res.json())
        .then((json) => this.prodotti = json)
    }
  },
  mounted() {
    this.getProdotti();
  },
  reserve() {
    this.loading = true
    setTimeout(() => (this.loading = false), 2000)
  }
}
</script>
