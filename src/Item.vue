<template>
  <div class="item" :class="{ vaulted: item.vaulted }" v-show="visible">
    <span class="name" v-if="filter.era === 'Any'">
      <input type="checkbox" v-model="owned.overall" />
      {{ item.name }}
    </span>
    <RecipeComponent
      ref="components"
      :owned.sync="owned[component.name]"
      :item-name="item.name"
      :component="component"
      :filter="filter"
      v-for="(component, index) in filteredComponents"
      :key="index"
    />
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch, Ref } from 'vue-property-decorator';
import { Item as WFItem, Component as WFComponent } from 'warframe-items';

import RecipeComponent from './RecipeComponent.vue';

export type Eras = 'Any' | 'Lith' | 'Meso' | 'Neo' | 'Axi';

export interface Filter {
  string: string;
  era: Eras;
  owned: boolean;
}

@Component({ components: { RecipeComponent } })
export default class Item extends Vue {
  @Prop({ required: true }) readonly item!: WFItem &
    Required<Pick<WFItem, 'components'>>;
  @Prop({ required: true }) readonly filter!: Filter;

  @Ref() readonly RecpieComponents?: RecipeComponent[];

  owned: { [key: string]: number } = {};
  someChildVisible = true;

  created() {
    const owned = Object.fromEntries(
      this.item.components.map((comp) => [comp.name, 0])
    );

    if (localStorage[`items/${this.item.name}`]) {
      Object.assign(owned, JSON.parse(localStorage[`items/${this.item.name}`]));
    }

    this.owned = owned;
  }

  mounted() {
    this.someChildVisible = this.checkChildrenVisible();
  }

  beforeUpdate() {
    // TODO: fix terrible hack to hide this Item when all child components are hidden
    this.someChildVisible = this.checkChildrenVisible();
  }

  checkChildrenVisible() {
    return this.RecpieComponents?.some((comp) => comp.visible) ?? false;
  }

  @Watch('owned', { deep: true }) onOwnedChanged(val: Item['owned']) {
    localStorage[`items/${this.item.name}`] = JSON.stringify(this.owned);
  }

  get visible() {
    return (
      !(this.filter.owned && this.owned.overall) &&
      (this.filter.era === 'Any' || this.someChildVisible) &&
      this.item.name.toLowerCase().includes(this.filter.string.toLowerCase())
    );
  }

  get filteredComponents() {
    return this.item.components.filter((c) => c.drops);
  }
}
</script>

<style scoped>
.item {
  margin: 0.25em;
  padding: 0.2em;
  background-color: #444;
  border-radius: 5px;
}

.item.vaulted {
  border: 2px solid #777;
}

.name {
  font-weight: bold;
}
</style>
