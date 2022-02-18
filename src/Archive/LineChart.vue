<template>
  <div>
    <fieldset class="intro-input">
      <label><b>Title</b></label>
      <input class="input title"  v-model="model.title"/>
    </fieldset>
    <div class="uk-margin">
      <fieldset class="intro-input">
        <label for="subtitle">
          <b>Subtitle</b>
        </label>
        <textarea label="subtitle" v-model="model.subtitle"/>
      </fieldset>
    </div>
    <div class="uk-flex uk-flex-column uk-margin">
      <label class="uk-margin-medium-right"><b>Use Category Axis</b></label>
      <span class="uk-margin-medium-left">
        <input class="toggle-input" type="checkbox" id="switch" v-model="model.useCategoryAxis" />
        <label class="toggle-label" for="switch">Toggle</label>
      </span>
      <p style="margin-top: 0">Using Category Axis will remove all smart date X Axis logic. Items will be spaced out evenly no matter what their label is.</p>
    </div>
    <span class="uk-flex uk-flex-column uk-margin">
      <label class="uk-margin-small"><b>Color</b></label>
      <select name="color" id="color" v-model="model.color">
        <option value="">Select One:</option>
        <option :key="option.text" v-for="option in options" :value="option.value">
          {{ option.text }}
        </option>
      </select>
    </span>
    <span class="uk-flex uk-flex-column uk-margin">
      <label><b>Data Interval</b></label>
      <p style="margin-top: 0" class="uk-margin-small">For example, if we have daily data (one data item per day), we'd select "day":</p>
      <p style="margin-top: 3px">Do not set "year" unless every data point is the same date of different years.</p>
      <select name="dateInterval" id="dateInterval" v-model="model.dateInterval">
        <option value="">Select One:</option>
        <option :key="interval" v-for="interval in dateIntervals" :value="interval">
          {{ interval }}
        </option>
      </select>
    </span>
    <span class="uk-flex uk-flex-column uk-margin">
      <label><b>Grid Date Interval</b></label>
      <p style="margin-top: 0" class="uk-margin-small">Interval to show the grid lines at.</p>
      <select name="gridDateInterval" id="gridDateInterval" v-model="model.gridDateInterval">
        <option value="">Select One:</option>
        <option :key="interval" v-for="interval in dateIntervals" :value="interval">
          {{ interval }}
        </option>
      </select>
    </span>
    <span class="uk-flex uk-flex-column uk-margin">
      <label><b>Grid Line Frequency</b></label>
      <p style="margin-top: 0" class="uk-margin-small">How often to show a line for selected Date Interval. 1 means show for every "Date Interval" selected. 2 means show for every second "Date Interval".</p>
      <input name="gridInterval" id="gridInterval" v-model="model.gridInterval" class="input title"  />
    </span>
    <span class="uk-flex uk-flex-column uk-margin">
      <label><b>Date Format</b></label>
      <p style="margin-top: 0" class="uk-margin-small">Example: MM-YYY or YYY</p>
      <input name="dateFormat" id="dateFormat" v-model="model.dateFormat" class="input title"  />
    </span>
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
          <label>{{index === 0 ? 'Date' : 'Label'}}</label>
          <input v-show="index !== 0" class="input" :class="{error: model.items[0][index] === ''}"  v-model="model.items[0][index]"/>
        </th>
      </tr>
      <!-- Dates/Data -->
      <tr class="data-list" :class="{hideItem: itemIndex === 0}" v-for="(item, itemIndex) in model.items" :key="itemIndex">
        <td class="data-list-item" v-for="(columnItem, columnIndex) in model.columns" :key="columnIndex">
          <button v-show="columnIndex === 0" class="add-item-button" @click="addField(itemIndex)">
            &#x002B;
          </button>
           <button class="delete-button" @click="removeField(itemIndex)" v-show="itemIndex !== 0 && model.items.length > 2">
            <span class="line"/>
            <span class="line"/>
          </button>
          <span  v-if="itemIndex !== 0">
            <input
              v-show="columnIndex === 0"
              v-if="model.useCategoryAxis === true"
              class="input date-selector"
              :class="{error: model.items[itemIndex][columnIndex] === ''}"
              v-model="model.items[itemIndex][columnIndex]"
              />
            <input
              v-else
              v-show="columnIndex === 0"
              class="input date-selector"
              type="date"
              :class="{error: model.items[itemIndex][columnIndex] === ''}"
              v-model="model.items[itemIndex][columnIndex]"
              />
            <input
              v-if="columnIndex !== 0"
              class="input"
              :class="{error: model.items[itemIndex][columnIndex] === undefined || isNaN(Number(model.items[itemIndex][columnIndex]))}"
              v-model.number="model.items[itemIndex][columnIndex]"
              />
        </span>
        </td>
      </tr>
    </table>
    <div class="button-wrapper">
      <button class="button add-button" @click="addField(model.items.length)">
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
      ],
      dateIntervals: ["day", "week", "month", "year"]
    };
  },
  computed: {
    gridSpacingEmpty: function() {
      return (
        this.model.gridSpacing !== null &&
        this.model.gridSpacing !== "" &&
        isNaN(Number(this.model.gridSpacing))
      );
    }
  },
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "line-chart",
        title: "",
        subtitle: "",
        color: "",
        items: [["Date", ""], [""]],
        columns: 2,
        gridSpacing: "",
        dateFormat: "",
        dateInterval: "month",
        gridDateInterval: "year",
        gridInterval: 1,
        useCategoryAxis: false
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
    addField(index) {
      this.model.items.splice(index, 0, [""]);
      // this.model.items.push([""]);
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

.stack-wrapper {
  display: flex;
  align-items: center;
  justify-content: flex-start;
}

.stack-wrapper p {
  margin-left: 10px;
}

.column-button-wrapper {
  width: 100%;
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.hideItem {
  display: none;
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
  font-size: 10px !important;
  padding: 4px !important;
  width: 100%;
  min-width: 40px !important;
}

.input.error {
  border: 1px #f2525f solid !important;
}

.data-list {
  margin-top: 0px;
  margin-left: 0px;
  margin-bottom: 20px;
  padding: 0;
  list-style-type: none;
  position: relative;
}

.data-list-item {
  list-style-type: none;
  margin: 0;
  padding: 0;
  position: relative;
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

/* TOGGLE */
.switch {
  height: 30px;
  width: 30px;
  visibility: hidden;
  position: absolute;
}

.switch-label {
  cursor: pointer;
  text-indent: -9999px;
  width: 40px;
  height: 25px;
  background: grey;
  display: block;
  border-radius: 100px;
  position: relative;
}

.switch-label:after {
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

.switch:checked + label {
  background: #0bd28b;
}

.switch:checked + label:after {
  left: calc(100% - 5px);
  transform: translateX(-100%);
}

.switch-label:active:after {
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

.add-item-button {
  position: absolute;
  left: 0;
  top: 12px;
  margin: auto 0;
  height: 15px;
  width: 15px;
  border: none;
  padding: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #0bd28b;
  z-index: 1;
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

/* TOGGLE */

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
</style>
