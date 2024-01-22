<template>
  test
</template>

<script>
export default {
  name: "CategoryView",
  data() {
    return {
      category: ""
    }
  },
  mounted() {
    this.category = this.$route.params.categoryName
    console.log(this.category)
  }
}
</script>
