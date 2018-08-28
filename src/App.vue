<template>
  <div>
    <div>
      <input type="text" v-model="filter" autofocus/>
      <label> Hide owned? <input type="checkbox" v-model="hideOwned" /> </label>
      <label> Era
        <select v-model="filterEra">
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
        <item v-for="thing in stuff" :item="thing"
              :hideOwned="hideOwned" :filter="filter" :filter-era="filterEra" />
      </div>
    </div>
  </div>
</template>

<script>
 import {readFileSync} from 'fs';

 import Item from './Item';

 export default {
   name: "App",
   components: { Item },
   data () {
     return {
       weapons: JSON.parse(readFileSync(__dirname + '/../data/weapons.json', 'utf8')),
       warframes: JSON.parse(readFileSync(__dirname + '/../data/warframes.json', 'utf8')),
       filter: "",
       filterEra: "Any",
       hideOwned: false,
     }
   },
   computed: {
     stuff() {
       return this
         .weapons.concat(this.warframes)
         .filter(item => item.name.includes('Prime'))
         .filter(item => 'components' in item)
     }
   }
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
   height: 95%
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
