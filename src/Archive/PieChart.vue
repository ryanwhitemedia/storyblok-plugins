<template>
  <div>
     <span class="margin-bottom-small block">
        <label><b>Title</b></label>
        <input class="input title"  v-model="model.title"/>
      </span>
      <span class="flex-container flex-column">
          <label><b>Color</b></label>
          <select name="color" id="color" v-model="model.color">
            <option value="">Select One:</option>
            <option :key="option.text" v-for="option in options" :value="option.value">
              {{ option.text }}
            </option>
          </select>
      </span>
    <div class="dataHeading">
      <span>
        <label>Label</label>
        <input class="input" :class="{error: model.items[0][0] === ''}"  v-model="model.items[0][0]"/>
      </span>
      <span>
        <label>Measurement</label>
        <input class="input" :class="{error: model.items[0][1] === ''}"  v-model="model.items[0][1]"/>
      </span>
    </div>
    <div>
      <ol class="data-list">
        <li class="data-list-item" v-for="(item, index) in model.items" :key="index">
          <fieldset class="fieldset" v-if="index !== 0">
            <input class="input" v-model="model.items[index][0]"/>
            <input
            class="input"
            :class="{error: isNaN(Number(model.items[index][1]))}"
            v-model.number="model.items[index][1]" />
            <button class="delete-button" @click="removeField(index)" v-show="index !== 0 && model.items.length > 2">
              <span class="line"/>
              <span class="line"/>
            </button>
          </fieldset>
        </li>
      </ol>
      <button class="button add-button" @click="addField()">
        ADD ITEM
      </button>
      <button v-show="model.items && model.items.length >= 3" class="button remove-button" @click="removeField()">
        REMOVE ITEM
      </button>
    </div>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  data() {
    return {
      options: [
        { text: "Multi Color", value: "multi" },
        { text: "Green", value: "green" },
        { text: "Light Blue", value: "light-blue" },
        { text: "Dark Blue", value: "dark-blue" },
        { text: "Red", value: "red" },
        { text: "Purple", value: "purple" },
        { text: "Orange", value: "orange" }
      ]
    };
  },
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "pie-chart",
        title: "",
        color: "",
        items: [["", ""], ["", 0]]
      };
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log(
        "View source and customize: https://github.com/storyblok/storyblok-fieldtype"
      );
    },
    addField() {
      if (this.model.items[0][0] === "" || this.model.items[0][1] === "") {
        alert("Pleas provide a label and measurement type.");
        return;
      }
      this.model.items.push(["", 0]);
    },
    removeField(index) {
      if (index) {
        this.model.items.splice(index, 1);
      } else {
        this.model.items.pop();
      }
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
.title {
  padding-bottom: 20px;
}

.dataHeading {
  padding-bottom: 10px;
  margin-bottom: 10px;
  margin-top: 25px;
  border-bottom: 1px solid;
  padding-right: 20px;
}

.dataHeading,
.fieldset {
  display: grid;
  grid-template-columns: 1fr 1fr;
  column-gap: 10px;
}

.input {
  width: 100%;
}

.input.error {
  border: 1px #f2525f solid !important;
}

.p-metatags__google-link {
  color: green;
}

.p-metatags__preview {
  margin: 5px 0 15px;
  padding: 10px;
  color: #000;
  background: #fff;
}

.data-list {
  margin-top: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-bottom: 20px;
  padding: 0;
  list-style-type: none;
}

.data-list-item {
  list-style-type: none;
  margin: 0;
  padding: 0;
  position: relative;
}

.data-list-item .fieldset {
  padding-right: 20px;
}

.delete-button {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  height: 15px;
  width: 15px;
  border: none;
  padding: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f2525f;
}

.delete-button .line {
  width: 10px;
  height: 2px;
  background-color: #fff;
  transform-origin: center;
  position: absolute;
}

.delete-button .line:first-child {
  transform: rotate(45deg);
}

.delete-button .line:last-child {
  transform: rotate(-45deg);
}

.list p {
  margin: 0;
}

.button {
  outline: none;
  padding: 8px 10px;
  margin-right: 10px;
  border: none;
  color: #212121;
  font-weight: 600;
}

.add-button {
  background-color: #54e0ae;
}

.remove-button {
  background-color: #f2525f;
}

/* GLOBAL */
.padding-bottom-small {
  padding-bottom: 12px;
}

.margin-bottom-small {
  padding-bottom: 12px;
}

.flex-container {
  display: flex;
}

.flex-column {
  flex-direction: column;
}

.block {
  display: block;
}
</style>
