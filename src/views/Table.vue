<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <TableComponent msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import TableComponent from '@/components/TableComponent.vue'

export default {
  name: 'Table',
  components: {
    TableComponent
  }
}
</script>
