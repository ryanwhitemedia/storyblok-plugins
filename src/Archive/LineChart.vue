<template>
  <div>
    <fieldset class="intro-input">
      <label><b>Title</b></label>
      <input class="input title"  v-model="model.title"/>
    </fieldset>
    <span class="stack-wrapper">
      <fieldset>
        <input type="checkbox" id="switch" class="switch" v-model="model.stackedBars">
        <label class="switch-label" for="switch">Toggle</label>
      </fieldset>
      <p>Stacked Bars</p>
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
      <tr class="data-list" v-for="(item, itemIndex) in model.items" :key="itemIndex">
        <td class="data-list-item" v-for="(columnItem, columnIndex) in model.columns" :key="columnIndex">
          <span  v-if="itemIndex !== 0">
            <input
            v-if="columnIndex === 0"
            class="input date-selector"
            type="date"
            :class="{error: model.items[itemIndex][columnIndex] === ''}"
            v-model="model.items[itemIndex][columnIndex]" />
            <input
            v-if="columnIndex !== 0"
            class="input"
            :class="{error: model.items[itemIndex][columnIndex] === undefined || isNaN(Number(model.items[itemIndex][columnIndex]))}"
            v-model.number="model.items[itemIndex][columnIndex]" />
        </span>
        </td>
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
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: "line-chart",
        title: "",
        items: [["Date", ""], ["", 0]],
        columns: 2,
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
      this.model.items.push([""]);
    },
    removeField() {
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
  margin-right: 0px;
  margin-bottom: 20px;
  padding: 0;
  list-style-type: none;
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
</style>
