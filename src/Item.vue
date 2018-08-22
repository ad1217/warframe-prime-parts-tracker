<template>
  <div class="item" v-show="filtered && !(hideOwned && owned)">
    <span class="name">
      <input type="checkbox" v-model="owned" />
      {{ name }}
    </span>
    <div class="component"
         v-for="component in components"
         v-if="'ducats' in component && !(hideOwned && component.owned)">
      <input v-for="index in component.itemCount" type="checkbox"
             v-model="component.owned" />
      {{ component.name }}
    </div>
  </div>
</template>

<script>
 export default {
   name: "Item",
   props: ['initialItem', 'hideOwned', 'filter'],
   data() {
     return {
       owned: false,
       name: this.initialItem.name,
       components: this.initialItem.components,
     }
   },
   computed: {
     filtered() {
       return this.name.toLowerCase().includes(this.filter.toLowerCase());
     }
   }
 }
</script>

<style scoped>
 .item {
   margin: .25em;
   padding: .2em;
   /* border: 1px solid black; */
   background-color: #444;
   border-radius: 5px;
 }

 .name {
   font-weight: bold;
 }
</style>
