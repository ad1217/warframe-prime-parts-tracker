<template>
  <div>
    <div>
      <input type="text" v-model="filter" />
      <label> Hide owned? <input type="checkbox" v-model="hideOwned" /> </label>
    </div>
    <div class="items">
      <item v-for="thing in stuff" :initialItem="thing"
            :hideOwned="hideOwned" :filter="filter" />
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
       mine: readFileSync(__dirname + '/../data/mine.txt', 'utf8').split('\n'),
       weapons: JSON.parse(readFileSync(__dirname + '/../data/weapons.json', 'utf8')),
       warframes: JSON.parse(readFileSync(__dirname + '/../data/warframes.json', 'utf8')),
       filter: "",
       hideOwned: false,
     }
   },
   computed: {
     stuff() {
       return this
         .weapons.concat(this.warframes)
         .filter(item => item.name.includes('Prime') &&
                       !this.mine.includes(item.name))
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

 .items {
   display: flex;
   flex-flow: row wrap;
 }
</style>
