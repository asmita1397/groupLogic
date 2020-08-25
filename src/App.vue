<template>
  <div>
    <!-- <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/> -->

    <div class="div-style">
      <div v-for="groupName in groupArray" :key="groupName" ref="groupName"> 
        <ScrollDiv  :groupName="groupName" :controlData="controlData" :refOfResizeDiv="refOfResizeDiv" :refName="$refs"/>
      </div>
      
      <ResizeDiv />
    </div>
    <!--  <InsertElement/> -->
    <!-- <ScrollDiv/> -->
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import HelloWorld from "./components/HelloWorld.vue";
import AttachParent from "./components/AttachParent.vue";
import ResizeDiv from "./components/ResizeDiv.vue";
import InsertElement from "./components/InsertElement.vue";
import ScrollDiv from "./components/ScrollDiv.vue";
import { EventBus } from "./components/event-bus";
@Component({
  components: {
    HelloWorld,
    AttachParent,
    ResizeDiv,
    InsertElement,
    ScrollDiv
  }
})
export default class App extends Vue {
  groupArray: any = [];
  controlData: any= [];
  refOfResizeDiv = ""
  mounted() {
    EventBus.$on("createdGroup", (groupArray: any,controlDataArray: any,refOfResizeDiv: any) => {
     
      this.groupArray = groupArray;
      this.controlData=controlDataArray 
      this.refOfResizeDiv= refOfResizeDiv
      console.log("app.vue",this.controlData)
    });
  }
}
</script>

<style>
.div-style {
  width: 800px;
  height: 500px;
  overflow: hidden;
  position: absolute;
  background-color: lightblue;
}

.div-style1 {
  width: 500px;
  height: 500px;
  overflow: hidden;
  position: absolute;
  background-color: red;
}
</style>
