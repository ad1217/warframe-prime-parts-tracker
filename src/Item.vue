<template>
  <div class="item" v-show="visible">
    <span class="name" v-if="filterEra === 'Any'">
      <input type="checkbox" v-model="owned.overall" />
      {{ item.name }}
    </span>
    <RecpieComponent @owned-update="componentUpdate(component.name, $event)"
                     :initial-owned="owned[component.name] || 0"
                     :item-name="item.name" :component="component"
                     :hide-owned="hideOwned" :filter-era="filterEra"
                     v-for="component in item.components" v-if="component.drops" />
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
       owned: {}
     }
   },

   created() {
     if (localStorage[`items/${this.item.name}`]) {
       let data = JSON.parse(localStorage[`items/${this.item.name}`]);
       this.owned = data;
     }
     else {
       this.components = {}
       this.item.components.forEach(comp => this.components[comp.name] = 0)
     }

     this.$watch('owned', () => {
       localStorage[`items/${this.item.name}`] = JSON.stringify(this.owned)
     }, {deep: true})
   },

   methods: {
     componentUpdate(name, newValue) {
       this.$set(this.owned, name, newValue);
     }
   },

   computed: {
     visible() {
       return (!(this.hideOwned && this.owned.overall)
            && this.item.name.toLowerCase().includes(this.filter.toLowerCase()));
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
