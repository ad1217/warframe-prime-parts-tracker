<template>
  <div class="component" v-show="visible">
    <input
      type="checkbox"
      :value="index - 1"
      v-model="ownedArray"
      v-for="index in component.itemCount"
    />
    <span v-if="this.filter.era !== 'Any'">
      {{ itemName }}
    </span>
    {{ component.name }}
    [<span v-for="(era, name) in eras">
      <span
        v-for="[loc, rarity] in eraRarity(era)"
        :class="rarity"
        :title="loc + ' | ' + rarity"
        >{{ name.slice(0, 1) }}</span
      > </span
    >]
  </div>
</template>

<script>
export default {
  name: 'RecpieComponent',
  props: ['itemName', 'owned', 'component', 'filter'],
  data() {
    return {
      ownedArray: [...Array(this.owned).keys()],
    };
  },

  watch: {
    ownedArray(newOwned) {
      this.$emit('update:owned', this.ownedArray.length);
    },
  },

  methods: {
    eraRarity(era) {
      console.log(new Set(era.map(e => e.rarity)));
      return era
        .filter(e => e.location.includes('Intact'))
        .map(e => [
          e.location.replace(' Intact', ''),
          e.chance < 0.1 ? 'Rare' : e.chance < 0.2 ? 'Uncommon' : 'Common',
        ]);
    },
  },

  computed: {
    eras() {
      return this.component.drops.reduce((acc, d) => {
        let key = d.location.split(' ')[0];
        (acc[key] = acc[key] || []).push(d);
        return acc;
      }, {});
    },

    visible() {
      return (
        !(
          this.filter.owned &&
          this.ownedArray.length === this.component.itemCount
        ) &&
        'ducats' in this.component &&
        (this.filter.era === 'Any' || this.filter.era in this.eras)
      );
    },
  },
};
</script>

<style scoped>
.Common {
  color: #b06500;
}

.Uncommon {
  color: silver;
}

.Rare {
  color: gold;
}
</style>
