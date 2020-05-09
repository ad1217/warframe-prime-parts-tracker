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
    [<span v-for="(era, name) in eras" :key="name">
      <span
        v-for="[loc, rarity] in eraRarity(era)"
        :key="loc"
        :class="rarity"
        :title="loc + ' | ' + rarity"
        >{{ name.slice(0, 1) }}</span
      > </span
    >]
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch, Ref } from 'vue-property-decorator';
import { Component as WFComponent, Drop as WFDrop } from 'warframe-items';

import { Filter, Eras } from './Item.vue';

@Component
export default class RecipeComponent extends Vue {
  @Prop({ required: true }) itemName!: string;
  @Prop({ required: true }) owned!: number;
  @Prop({ required: true }) component!: WFComponent;
  @Prop({ required: true }) filter!: Filter;

  ownedArray = Array.from(Array(this.owned).keys());

  @Watch('ownedArray')
  ownedArrayChanged(newOwned: RecipeComponent['ownedArray']) {
    this.$emit('update:owned', this.ownedArray.length);
  }

  eraRarity(era: WFDrop[]) {
    return era
      .filter((e) => e.location.includes('Intact'))
      .map((e) => [
        e.location.replace(' Intact', ''),
        e.chance === null
          ? '???'
          : e.chance < 0.1
          ? 'Rare'
          : e.chance < 0.2
          ? 'Uncommon'
          : 'Common',
      ]);
  }

  get eras() {
    return (
      this.component.drops?.reduce((acc, d) => {
        const key = d.location.split(' ')[0] as Eras;
        (acc[key] = acc[key] || []).push(d);
        return acc;
      }, {} as { [key in Eras]: WFDrop[] }) ?? {}
    );
  }

  get visible() {
    return (
      !(
        this.filter.owned && this.ownedArray.length === this.component.itemCount
      ) &&
      'ducats' in this.component &&
      (this.filter.era === 'Any' || this.filter.era in this.eras)
    );
  }
}
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
