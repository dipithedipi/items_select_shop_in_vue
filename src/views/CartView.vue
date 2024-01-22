<template>
  <div class="about">
    <h1>Cart</h1>
    <p>Selected items:</p>
    <pre>{{ selected }}</pre>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selected: []
    }
  },
  methods: {

  },
  mounted() {
    this.selected = JSON.parse(localStorage.getItem('selectedItems'));
    console.log("selected:")
    console.log(this.selected)
  }
}
</script>
