<template>
  <div>
    <span class="uk-flex uk-flex-column uk-margin">
      <label for="title">
        <b>Title</b>
      </label>
      <input label="title" v-model="model.title"/>
    </span>
    <span class="uk-flex uk-flex-column uk-margin-small">
      <label for="subtitle">
        <b>Subtitle</b>
      </label>
      <textarea label="subtitle" v-model="model.subtitle"/>
      <span class="uk-flex uk-flex-column uk-margin">
        <label class="uk-margin-small"><b>Color</b></label>
        <select name="color" id="color" v-model="model.color">
          <option value="">Select One:</option>
          <option :key="option.text" v-for="option in options" :value="option.value">
            {{ option.text }}
          </option>
        </select>
      </span>
      <div class="stack-wrapper">
        <span>
          <input class="toggle-input" type="checkbox" id="description-switch" v-model="model.hideDescription" />
          <label class="toggle-label" for="description-switch">Toggle</label>
        </span>
        <p>Hide Description</p>
      </div>
    </span>
    <div class="stack-wrapper uk-margin">
      <span>
        <input class="toggle-input" type="checkbox" id="switch" v-model="model.stackedBars" />
        <label class="toggle-label" for="switch">Toggle</label>
      </span>
      <p>Stacked Bars</p>
    </div>
    <div class="column-button-wrapper">
      <button v-show="model.columns > 2" class="column-button remove-button" @click="removeColumn()">
        &#8722;
      </button>
      <button class="column-button add-button" @click="addColumn()">
        &#x002B;
      </button>
    </div>
    <table class="table">
      <!-- Header/Lablels -->
      <tr class="dataHeading">
        <th v-for="(column, index) in model.columns" :key="index">
          <label v-if="index === 0">Label</label>
          <label v-else>{{index === 1 ? 'Measurement' : 'Msmt'}}</label>
          <input class="input" :class="{error: model.items[0][index] === ''}"  v-model="model.items[0][index]"/>
        </th>
      </tr>
      <!-- Dates/Data -->
      <tr class="data-list" v-for="(item, itemIndex) in model.items" :key="itemIndex">
        <td class="data-list-item" v-for="(columnItem, columnIndex) in model.columns" :key="columnIndex" v-show="itemIndex !== 0">
          <span>
            <input
              v-if="columnIndex === 0"
              class="input"
              :class="{error: model.items[itemIndex][columnIndex] === ''}"
              v-model="model.items[itemIndex][columnIndex]" />
            <input
              v-else
              class="input"
              :class="{error: model.items[itemIndex][columnIndex] === 0}"
              v-model.number="model.items[itemIndex][columnIndex]" />
          </span>
        </td>
        <button class="delete-button" @click="removeField(itemIndex)" v-show="itemIndex !== 0 && model.items.length > 2">
            <span class="line"/>
            <span class="line"/>
          </button>
      </tr>
    </table>
    <div class="button-wrapper">
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
        plugin: "bar-chart",
        title: "",
        subtitle: "",
        color: "",
        items: [["", ""], ["", 0]],
        columns: 2,
        hideDescription: false,
        stackedBars: false
      };
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log(
        "View source and customize: https://github.com/storyblok/storyblok-fieldtype"
      );
    },
    addColumn() {
      this.model.columns = this.model.columns + 1;
      this.model.items.map((item, index) => {
        if (index === 0) {
          item.push("");
        } else {
          item.push(0);
        }
      });
    },
    removeColumn() {
      this.model.columns = this.model.columns - 1;
      this.model.items.map(item => {
        item.pop();
      });
    },
    addField() {
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

.stack-wrapper {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  margin-top: 20px;
}

.stack-wrapper p {
  margin: 0 0 0 10px;
}

.column-button-wrapper {
  width: 100%;
  display: flex;
  justify-content: flex-end;
  margin-top: 10px;
}

.column-button {
  outline: none;
  border: 1px solid black;
  margin-left: 5px;
}

.intro-input {
  display: flex;
  flex-direction: column;
}

.dataHeading,
.fieldset {
  column-gap: 10px;
}

.dataHeading {
  margin-bottom: 10px;
  margin-top: 25px;
  padding-bottom: 10px;
  vertical-align: baseline;
  width: 100%;
}

.table .input {
  padding: 4px !important;
  width: 100%;
}

.table th:first-child {
  padding-left: 0;
}

th label {
  font-size: 12px;
}

.input.error {
  border: 1px #f2525f solid !important;
}

.data-list {
  margin-top: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-bottom: 20px;
  padding: 0;
  list-style-type: none;
  border-bottom: none !important;
  position: relative;
}

.data-list-item {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.date-selector {
  width: 80px;
  position: relative;
}

input[type="date"]::-webkit-calendar-picker-indicator {
  width: 10px;
  position: absolute;
  top: 0;
  right: 0;
}

.list p {
  margin: 0;
}

.button-wrapper {
  display: flex;
  margin-top: 20px;
}

.button {
  outline: none;
  padding: 8px 10px;
  margin-right: 10px;
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

/* Switch */
.toggle-input[type="checkbox"] {
  height: 0;
  width: 0;
  visibility: hidden;
  position: absolute;
}

.toggle-label {
  cursor: pointer;
  text-indent: -9999px;
  width: 40px;
  height: 25px;
  background: grey;
  display: block;
  border-radius: 100px;
  position: relative;
}

.toggle-label:after {
  content: "";
  position: absolute;
  top: 5px;
  left: 5px;
  width: 15px;
  height: 15px;
  background: #fff;
  border-radius: 90px;
  transition: 0.3s;
}

.toggle-input:checked + .toggle-label {
  background: #0bd28b;
}

.toggle-input:checked + .toggle-label:after {
  left: calc(100% - 5px);
  transform: translateX(-100%);
}

.toggle-label:active:after {
  width: 30px;
}

.delete-button {
  position: absolute;
  right: 0;
  top: 12px;
  margin: auto 0;
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

</style>
