<template>
  <div>
    <div class="label-wrapper">
      <span class="label-item">
        <label for="title">
          <b>Title</b>
        </label>
        <input label="title" v-model="model.title"/>
      </span>
      <span class="label-item">
        <label for="subtitle">
          <b>Subtitle</b>
        </label>
        <textarea label="subtitle" v-model="model.subtitle"/>
      </span>
    </div>
    <table class="table">
      <tr>
        <th>
         <label><b>Label</b></label>
        </th>
        <th>
          <label><b>Rating</b></label>
        </th>
      </tr>
      <tr v-for="(item,index) in model.items" :key="index">
        <td>
          <input class="input-text" :class="{error: model.items[index].label === ''}" type="text" v-model="model.items[index].label" />
        </td>
        <td class="slider-wrapper">
          <input class="slider" min="0" max="100"  type="range" v-model="model.items[index].rating" />
          <input type="text" v-model.number="model.items[index].rating" class="input-number" :class="{error:model.items[index].rating < 0 || model.items[index].rating > 100}" />
        </td>
      </tr>
    </table>
    <button class="button add-button" @click="addItem">
      Add Item
    </button>
    <button class="button remove-button" @click="removeItem" v-show="model.items.length >= 2">
      Remove Item
    </button>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "scoring-slider",
        title: "",
        subtitle: "",
        items: [
          { label: "Growth", rating: 50 },
          { label: "Valuation", rating: 50 },
          { label: "Quality", rating: 50 }
        ]
      };
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log(
        "View source and customize: https://github.com/storyblok/storyblok-fieldtype"
      );
    },
    addItem() {
      this.model.items.push({ label: "", rating: 50 });
    },
    removeItem() {
      this.model.items.pop();
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
.label-wrapper {
  display: flex;
  flex-direction: column;
  margin-bottom: 5px;
}

.label-item {
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;
}

.table {
  max-width: 100%;
  text-align: left;
}

.input-text {
  width: 100%;
}

.slider-wrapper {
  display: flex;
}

.slider {
  padding-right: 15px;
}

.input-number {
  width: 50px;
}

.error {
  border: 1px #f2525f solid !important;
}

.button {
  outline: none;
  padding: 8px 10px;
  margin-right: 10px;
  border: none;
  color: #212121;
  font-weight: 600;
  border: 1px solid black;
  white-space: nowrap;
}

.add-button {
  background-color: #54e0ae;
}

.remove-button {
  background-color: #f2525f;
}
</style>
