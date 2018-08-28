<template>
  <div class="item" v-show="filtered && !(hideOwned && owned)">
    <span class="name" v-if="filterEra === 'Any'">
      <input type="checkbox" v-model="owned" />
      {{ item.name }}
    </span>
    <div v-for="component in item.components">
      <RecpieComponent :itemName="item.name" :component="component" :index="index"
                       :hideOwned="hideOwned" :filterEra="filterEra"
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
   mounted() {
     if (localStorage[`items/${this.item.name}`]) {
       this.owned = JSON.parse(localStorage[`items/${this.item.name}`]);
     }
   },
   watch: {
     owned(newOwned) {
       localStorage[`items/${this.item.name}`] = newOwned;
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
   background-color: #444;
   border-radius: 5px;
 }

 .name {
   font-weight: bold;
 }
</style>
