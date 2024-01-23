<template>
  <div style="position: absolute; top: 0; right: 0; padding: 20px;">
    <!-- <router-link :to="{ name: 'cart', params: { selectedItems: selected } }"> -->
      <v-btn v-if="selected.length > 0" @click="gotoCart" color="blue" dark>Go to the cart</v-btn>
    <!-- </router-link> -->
  </div>
  
  <v-card flat>
    <v-divider></v-divider>
    <v-data-table
    v-model="selected"
    :headers="headers" 
    :items="items" 
    items-per-page="5"
    :item-key="item => item.id"
    :item-selectable="item => item.stock > 0"
    return-object
    show-select
    >
      <template v-slot:[`header.stock`]>
        <div class="text-end">Stock</div>
      </template>

      <template v-slot:[`item.image`]="{ item }">
        <v-card class="my-2" elevation="2" rounded>
          <v-img :src="`${item.image}`" height="64" cover></v-img>
        </v-card>
      </template>

      <template v-slot:[`item.rating`]="{ item }">
        <v-rating :model-value="item.rating.rate" color="orange-darken-2" density="compact" size="small" readonly></v-rating>
      </template>

      <template v-slot:[`item.stock`]="{ item }">
        <div class="text-end">
          <v-chip :color="item.stock ? 'green' : 'red'" :text="item.stock ? 'In stock (' +item.stock+ ')' : 'Out of stock'"
            class="text-uppercase" label size="small"></v-chip>
        </div>
      </template>
    </v-data-table>
  </v-card>
  
  <!-- <pre>{{ selected }}</pre> -->
</template>


<script>
export default {
  data() {
    return {
      items: [],
      selected: [],
    };
  },
  methods: {
    async getProducts() {
      try {
        const apiPromise = fetch('https://fakestoreapi.com/products')
          .then(response => {
            if (!response.ok) {
              throw new Error('Failed to fetch products from API');
            }
            return response.json();
          });

        const timeoutPromise = new Promise((_, reject) => {
          setTimeout(() => reject(new Error('API request timed out')), 2000);
        });

        // Use Promise.race to wait for either the API response or the timeout
        const json = await Promise.race([apiPromise, timeoutPromise]);

        this.items = json;

        this.items.forEach(item => {
          item.stock = Math.floor(Math.random() * 100);
        });

        localStorage.setItem('items', JSON.stringify(this.items));
        console.log("Items from API");
      } catch (error) {
        // Handle error: try reading from file
        this.readFromFile();
      }
    },
    gotoCart() {
      if(JSON.parse(localStorage.getItem('selectedItems'))) {
        let oldSelected = JSON.parse(localStorage.getItem('selectedItems'));
        oldSelected.forEach(oldSelectedItem => {
          this.selected.push(oldSelectedItem);
        });
        localStorage.setItem('selectedItems', JSON.stringify(this.selected));
      } else {
        localStorage.setItem('selectedItems', JSON.stringify(this.selected));
      }

      this.$router.push({ name: 'cart' });
    },
    readFromFile() {
      fetch('./response.json')
        .then(response => response.json())
        .then(data => {
          this.items = data;
          console.log('Items read from file');
          localStorage.setItem('items', JSON.stringify(this.items));
        })
        .catch(error => console.error('Error:', error));
    },
  },
  mounted() {
    if (localStorage.getItem('items')) {
      this.items = JSON.parse(localStorage.getItem('items'));
      console.log('Items from local storage');
      // console.log("Items:")
      // console.log(this.items)
    } else {
      this.getProducts();
    }
  },
};
</script>