<template>
  <div class="about">
    <h1>Cart</h1>
    <p>Selected items:</p>

    <v-data-table v-if="selected.length > 0" :items="selected" :headers="headers" hide-default-footer>
      <template v-slot:item="{ item }">
        <tr>
          <td><img :src="item.image" alt="Product Image" height="50"></td>
          <td>{{ item.title }}</td>
          <td>{{ item.price }}€</td>
          <td>
            <v-btn icon @click="decrementQuantity(item)">
              <v-icon>mdi-minus</v-icon>
            </v-btn>
            {{ item.quantity }}
            <v-btn icon @click="incrementQuantity(item)">
              <v-icon>mdi-plus</v-icon>
            </v-btn>
          </td>
        </tr>
      </template>
    </v-data-table>

    <h2 v-else style="padding: 10px;">Cart is empty, Please
      <router-link to="/table">add new product</router-link>
    </h2>

    <h3 v-if="selected.length > 0">Total: {{ totalPrice() }}€</h3>

    <v-btn v-if="selected.length > 0" @click="buySelectedProducts" color="red" dark class="mr-2">Buy the
      products</v-btn>
    <v-btn v-if="selected.length > 0" @click="clearCart" color="blue" dark>Clear</v-btn>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selected: [],
      items: [],
      headers: [
        { text: 'Image', value: 'image' },
        { text: 'Title', value: 'title' },
        { text: 'Price', value: 'price' },
        { text: 'Quantity', value: 'quantity' }
      ]
    }
  },
  methods: {
    totalPrice() {
      return this.selected.reduce((total, item) => {
        return total + item.price * item.quantity;
      }, 0);
    },
    buySelectedProducts() {
      alert('Thank you for your purchase!');
      for (const item of this.selected) {
        const selectedItem = this.items.find((i) => i.id === item.id);
        selectedItem.stock -= item.quantity;
      }
      console.log("item after buy")
      console.log(this.items);
      this.clearCart();
      localStorage.setItem('items', JSON.stringify(this.items));
    },
    clearCart() {
      this.selected = [];
      localStorage.removeItem('selectedItems');
    },
    updateSelectedItems(itemId, newQuantity) {
      const item = this.selected.find((i) => i.id === itemId);
      item.quantity = newQuantity;
      localStorage.setItem('selectedItems', JSON.stringify(this.selected));
    },
    incrementQuantity(item) {
      if(item.quantity >= item.stock) {
        alert('You cannot add more than the stock!');
        return;
      }
      item.quantity++;
      this.updateSelectedItems(item.id, item.quantity);
    },
    decrementQuantity(item) {
      if (item.quantity > 1) {
        item.quantity--;
        this.updateSelectedItems(item.id, item.quantity);
      } else if (item.quantity === 1) {
        if (window.confirm('Are you sure to remove this product from the cart?')) {
          this.clearItemFromCart(item.id);
        }
      }
    },
    clearItemFromCart(itemId) {
      this.selected = this.selected.filter((i) => i.id !== itemId);
      localStorage.setItem('selectedItems', JSON.stringify(this.selected));
    },
    removeDuplicate() {
      const uniqueItems = [];
      const itemIds = new Set();

      for (const item of this.selected) {
        if (!itemIds.has(item.id)) {
          uniqueItems.push(item);
          itemIds.add(item.id);
        } else {
          const existingItem = uniqueItems.find((i) => i.id === item.id);
          existingItem.quantity += item.quantity;
        }
      }

      this.selected = uniqueItems;
    }
  },
  mounted() {
    if (localStorage.getItem('items')) {
      this.items = JSON.parse(localStorage.getItem('items'));
    } else {
      this.items = [];
    }

    if (localStorage.getItem('selectedItems')) {
      this.selected = JSON.parse(localStorage.getItem('selectedItems'));
      this.selected.forEach(selectedItem => {
        if (!selectedItem.quantity) {
          selectedItem.quantity = 1
        }
      });
      this.removeDuplicate();

      console.log("quantity calculated")
      console.log(this.selected)
    } else {
      this.selected = [];
    }
  }
}
</script>
