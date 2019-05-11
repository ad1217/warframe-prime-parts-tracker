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
        <Item v-for="item in items" :item="item" :filter="filter" />
      </div>
    </div>
  </div>
</template>

<script>
import item_data from '../node_modules/warframe-items/data/json/*.json';

import Item from './Item';

export default {
  name: 'App',
  components: { Item },
  data() {
    return {
      items: [
        'Primary',
        'Secondary',
        'Melee',
        'Archwing',
        'Sentinels',
        'Pets',
        'Warframes',
      ]
        .map(category => item_data[category])
        .reduce((acc, val) => acc.concat(val), [])
        .filter(item => item.name.includes('Prime'))
        .filter(item => 'components' in item)
        .sort((a, b) => (a.name < b.name ? -1 : a.name > b.name ? 1 : 0)),
      filter: {
        string: '',
        era: 'Any',
        owned: true,
      },
    };
  },
};
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
