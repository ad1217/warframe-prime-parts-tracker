<template>
  <div class="item" :class="{ vaulted: item.vaulted }" v-show="visible">
    <span class="name" v-if="filter.era === 'Any'">
      <input type="checkbox" v-model="owned.overall" />
      {{ item.name }}
    </span>
    <RecpieComponent
      ref="components"
      :owned.sync="owned[component.name]"
      :item-name="item.name"
      :component="component"
      :filter="filter"
      v-for="component in item.components"
      v-if="component.drops"
    />
  </div>
</template>

<script>
import RecpieComponent from './RecpieComponent';

export default {
  name: 'Item',
  props: ['item', 'filter'],
  components: { RecpieComponent },
  data() {
    return {
      owned: {},
      someChildVisible: true,
    };
  },

  created() {
    let owned = Object.fromEntries(
      this.item.components.map(comp => [comp.name, 0])
    );

    if (localStorage[`items/${this.item.name}`]) {
      Object.assign(owned, JSON.parse(localStorage[`items/${this.item.name}`]));
    }

    this.owned = owned;
  },

  mounted() {
    this.someChildVisible = this.checkChildrenVisible();
  },

  beforeUpdate() {
    // TODO: fix terrible hack to hide this Item when all child components are hidden
    this.someChildVisible = this.checkChildrenVisible();
  },

  methods: {
    checkChildrenVisible() {
      return Array.from(this.$refs.components).some(comp => comp.visible);
    },
  },

  watch: {
    owned: {
      handler(val) {
        localStorage[`items/${this.item.name}`] = JSON.stringify(this.owned);
      },
      deep: true,
    },
  },

  computed: {
    visible() {
      return (
        !(this.filter.owned && this.owned.overall) &&
        (this.filter.era === 'Any' || this.someChildVisible) &&
        this.item.name.toLowerCase().includes(this.filter.string.toLowerCase())
      );
    },
  },
};
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
