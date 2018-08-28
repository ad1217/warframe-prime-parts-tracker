<template>
  <div class="component"
       v-if="'ducats' in component && visible" >
    <input type="checkbox" v-model="owned[index - 1]"
           v-for="index in component.itemCount" />
    <span v-if="this.filterEra !== 'Any'" >
      {{ itemName }}
    </span>
    {{ component.name }} [{{ eraLetters }}]
  </div>
</template>

<script>
 export default {
   name: "RecpieComponent",
   props: ['itemName', 'initialOwned', 'component', 'hideOwned', 'filterEra'],
   data() {
     return {
       owned: Array(this.initialOwned).fill(true, 0, this.initialOwned)
     }
   },
   watch: {
     owned(newOwned) {
       this.$emit('ownedUpdate', this.ownedCount);
     }
   },
   computed: {
     eras() {
       return new Set(this.component.drops.map(d => d.location.split(" ")[0]));
     },
     eraLetters() {
       return Array.from(this.eras).map(e => e.slice(0, 1)).join("");
     },
     ownedCount() {
       return this.owned.reduce((acc, x) => acc += (x === true), 0);
     },
     visible() {
       return (!(this.hideOwned && this.ownedCount === this.component.itemCount)
            && (this.filterEra === 'Any' || this.eras.has(this.filterEra)));
     }
   }
 }
</script>
