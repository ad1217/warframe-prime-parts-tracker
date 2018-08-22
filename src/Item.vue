<template>
  <div class="item" v-show="filtered && !(hideOwned && owned)">
    <span class="item.name" v-if="filterEra === 'Any'">
      <input type="checkbox" v-model="owned" />
      {{ item.name }}
    </span>
    <div v-for="component in item.components">
      <RecpieComponent :itemName="item.name" :component="component"
                       :hideOwned="hideOwned" :filter-era="filterEra"
                       v-for="index in component.itemCount" />
    </div>
  </div>
</template>

<script>
 import RecpieComponent from './RecpieComponent';

 export default {
   name: "Item",
   props: ['item', 'hideOwned', 'filter', 'filterEra'],
   components: { RecpieComponent },
   data() {
     return {
       owned: false
     }
   },
   computed: {
     filtered() {
       return this.item.name.toLowerCase().includes(this.filter.toLowerCase());
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
