<template>
  <div class="component"
       v-if="'ducats' in component" v-show="visible">
    <input type="checkbox" v-model="owned" />
    <span v-if="this.filterEra !== 'Any'" >
      {{ itemName }}
    </span>
    {{ component.name }} [{{ eraLetters }}]
  </div>
</template>

<script>
 export default {
   name: "RecpieComponent",
   props: ['itemName', 'component', 'hideOwned', 'filterEra'],
   data() {
     return {
       owned: false
     }
   },
   computed: {
     eras() {
       return new Set(this.component.drops.map(d => d.location.split(" ")[0]));
     },
     eraLetters() {
       return Array.from(this.eras).map(e => e.slice(0, 1)).join("");
     },
     visible() {
       return !(this.hideOwned && this.owned) &&
              (this.filterEra === 'Any' || this.eras.has(this.filterEra));
     }
   }
 }
</script>
