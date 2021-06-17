<template>
  <div>
     <fieldset>
        <label><b>Title</b></label>
        <input class="input title"  v-model="model.title"/>
      </fieldset>
    <div class="dataHeading">
      <fieldset>
        <label>Label</label>
        <input class="input" :class="{error: model.dataType === ''}"  v-model="model.dataType"/>
      </fieldset>
      <fieldset>
        <label>Measurement</label>
        <input class="input" :class="{error: model.dataMeasurement === ''}"  v-model="model.dataMeasurement"/>
      </fieldset>
    </div>
    <div>
      <ol class="data-list">
        <li class="data-list-item" v-for="(item, index) in model.items" :key="index">
          <fieldset class="fieldset">
            <input class="input" v-model="model.items[index][0]"/>
            <input
            class="input"
            :class="{error: isNaN(Number(model.items[index][1]))}"
            v-model.number="model.items[index][1]" />
          </fieldset>
        </li>
      </ol>
      <button class="button add-button" @click="addField()">
        ADD ITEM
      </button>
      <button v-show="model.items && model.items.length >= 2" class="button remove-button" @click="removeField()">
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
        plugin: "pie-chart",
        title: "",
        dataType: "",
        dataMeasurement: "",
        items: [["Name", 0]]
      };
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log(
        "View source and customize: https://github.com/storyblok/storyblok-fieldtype"
      );
    },
    addField() {
      // const length = this.model.items.length - 1;
      if (this.model.dataMeasurement === "" || this.model.dataType === "") {
        alert("Pleas provide a label and measurement type.");
        return;
      }
      // if (
      //   isNaN(Number(this.model.items[length][1])) ||
      //   this.model.items[length][0] === ""
      // ) {
      //   alert(
      //     "The Label must not be empty and the measurement must be a valid number."
      //   );
      //   return;
      // }
      this.model.items.push(["", 0]);
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

.dataHeading,
.fieldset {
  display: grid;
  grid-template-columns: 1fr 1fr;
  column-gap: 10px;
}

.dataHeading {
  padding-bottom: 10px;
  margin-bottom: 10px;
  margin-top: 25px;
  border-bottom: 1px solid;
  width: 100%;
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
</style>
