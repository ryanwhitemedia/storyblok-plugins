<template>
  <div class="List">
    <ol class="List__list uk-margin-bottom-remove">
      <li
        v-for="(item, index) in model.items"
        :key="index"
        class="List__list-item"
      >
        <input
          v-model="model.items[index]"
          class="uk-form-small uk-width-1-1"
          :aria-label="`List item ${index}`"
        >
        <a
          class="assets__item-trash"
          aria-label="Remove item"
          @click="removeItem(index)"
        >
          <i class="uk-icon-minus-circle"></i>
        </a>
      </li>
    </ol>
    <a
      v-if="!limitReached"
      class="blok__full-btn uk-margin-small-top"
      @click="addItem"
    >
      <i class="uk-icon-plus-circle uk-margin-small-right"/>
      Add item
    </a>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  computed: {
    limitReached() {
      return (
        this.options.limit && this.model.items.length >= this.options.limit
      );
    }
  },
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "repeater-list",
        items: []
      };
    },
    addItem() {
      this.model.items.push("");
    },
    removeItem(index) {
      this.model.items = this.model.items.filter((_, i) => i !== index);
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log(
        "View source and customize: https://github.com/storyblok/storyblok-fieldtype"
      );
    }
  },
  watch: {
    model: {
      handler: function(value) {
        this.$emit("changed-model", value);
      },
      deep: true
    }
  }
};
</script>

<style>
.List__list {
  padding-left: 0;
}

.List__list-item {
  margin-top: 5px;
  display: flex;
  align-items: center;
  flex-wrap: nowrap;
  flex-direction: row;
}
</style>
