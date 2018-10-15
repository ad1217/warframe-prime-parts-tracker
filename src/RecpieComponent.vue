<template>
  <div class="component" v-show="visible" >
    <input type="checkbox" v-model="owned[index - 1]"
           v-for="index in component.itemCount" />
    <span v-if="this.filterEra !== 'Any'" >
      {{ itemName }}
    </span>
    {{ component.name }}
    [<span v-for="era in Object.keys(eras)"
           :title="eras[era].map(e => e.location + ' | ' + e.rarity).join('\n')"
     >{{ era.slice(0, 1) }}</span>]
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
       this.$emit('owned-update', this.ownedCount);
     }
   },
   computed: {
     eras() {
       return this.component.drops.reduce((acc, d) => {
         let key = d.location.split(" ")[0];
         (acc[key] = acc[key] || []).push(d);
         return acc;
       }, {});
     },
     ownedCount() {
       return this.owned.reduce((acc, x) => acc += (x === true), 0);
     },
     visible() {
       return (!(this.hideOwned && this.ownedCount === this.component.itemCount)
            && 'ducats' in this.component
            && (this.filterEra === 'Any' || this.filterEra in this.eras));
     }
   }
 }
</script>
