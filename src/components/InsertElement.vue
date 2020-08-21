<template>
  <div id="test">
    <button @click="add">add</button>
    <span v-html="items[1]"></span>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
@Component({
  components: {}
})
export default class InsertElement extends Vue {
  items = ['</br><input type="text"/>'];
  listItems = ["a", "b", "c"];
  added() {
    console.log("hiiii");
  }
  add() {
    const v = document.createElement("input");
    v.type = "button";
    v.value = "Button ";

    (document as any).getElementById("test").appendChild(v);
    v.addEventListener("mousedown", this.added);
  }
}
</script>
<style  scoped>
.div-style {
  width: 800px;
  height: 500px;
  overflow: hidden;
  position: absolute;
  background-color: lightblue;
}
</style>