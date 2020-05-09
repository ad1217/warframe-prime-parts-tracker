<template>
  <div>
    <div>
      <input type="text" v-model="filter.string" autofocus />
      <label>
        Hide owned? <input type="checkbox" v-model="filter.owned" />
      </label>
      <label>
        Era
        <select v-model="filter.era">
          <option value="Any"> Any </option>
          <option value="Lith"> Lith </option>
          <option value="Meso"> Meso </option>
          <option value="Neo"> Neo </option>
          <option value="Axi"> Axi </option>
        </select>
      </label>
    </div>
    <div class="wrapper">
      <div class="items">
        <Item
          v-for="item in items"
          :key="item.name"
          :item="item"
          :filter="filter"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';
import { Item as WFItem } from 'warframe-items';

import Primary from 'warframe-items/data/json/Primary.json';
import Secondary from 'warframe-items/data/json/Secondary.json';
import Melee from 'warframe-items/data/json/Melee.json';
import Archwing from 'warframe-items/data/json/Archwing.json';
import Sentinels from 'warframe-items/data/json/Sentinels.json';
import Pets from 'warframe-items/data/json/Pets.json';
import Warframes from 'warframe-items/data/json/Warframes.json';

import Item, { Filter } from './Item.vue';

@Component({ components: { Item } })
export default class App extends Vue {
  items = ([
    Primary,
    Secondary,
    Melee,
    Archwing,
    Sentinels,
    Pets,
    Warframes,
  ].flat() as WFItem[])
    .filter((item) => item.name.includes('Prime') && 'components' in item)
    .sort((a, b) => (a.name < b.name ? -1 : a.name > b.name ? 1 : 0));
  filter: Filter = {
    string: '',
    era: 'Any',
    owned: true,
  };
}
</script>

<style>
body {
  background-color: #282828;
  color: #eee;
}

.wrapper {
  position: relative;
  top: 0px;
  bottom: 0px;
  height: 95%;
}
.items {
  display: flex;
  flex-flow: column wrap;
  width: auto;
  position: absolute;
  top: 0px;
  bottom: 0px;
}
</style>
