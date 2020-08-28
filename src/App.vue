<template>
  <div>
    <!-- <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/> -->

    <div class="div-style" @mouseup="dargSelector" @mousedown="handleMouseDown">
    <drag-selector v-model="checkedList" @change="handleDragSelectorChange" class="drag-selector" ref="dragSelector">
      <div v-for="groupName in groupArray" :key="groupName" ref="groupName"> 
        <ScrollDiv  :groupName="groupName" :controlData="controlData" :refOfResizeDiv="refOfResizeDiv" />
      </div>
      
      <ResizeDiv  :refName="$refs"/>
    </drag-selector>
    </div>


    <!-- <DragSelect1/> -->
    <!--  <InsertElement/> -->
    <!-- <ScrollDiv/> -->
   <!--  <logic/> -->
   <!--  -->
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

import HelloWorld from "./components/HelloWorld.vue";
import AttachParent from "./components/AttachParent.vue";
import ResizeDiv from "./components/ResizeDiv.vue";
import InsertElement from "./components/InsertElement.vue";
import ScrollDiv from "./components/ScrollDiv.vue";
import logic from "./components/logic.vue";
import { EventBus } from "./components/event-bus";

import DragSelector from "./components/DragSelector.vue";
@Component({
  components: {
    logic,
    HelloWorld,
    AttachParent,
    ResizeDiv,
    InsertElement,
    ScrollDiv,
  
    DragSelector,

  }
})
export default class App extends Vue {
  groupArray: any = [];
  controlData = [
    {
      width: "100px",
      height: "100px",
      left: "100px",
      top: "50px",
      isActivate: false,
      id: "Label1",
      group: ""
    },
    {
      width: "120px",
      height: "100px",
      left: "120px",
      top: "250px",
      isActivate: false,
      id: "Label2",
      group: ""
    },
    {
      width: "200px",
      height: "300px",
      left: "500px",
      top: "10px",
      isActivate: false,
      id: "Label3",
      group: ""
    },
    {
      width: "100px",
      height: "100px",
      left: "300px",
      top: "300px",
      isActivate: false,
      id: "Label4",
      group: ""
    },
    {
      width: "100px",
      height: "100px",
      left: "350px",
      top: "20px",
      isActivate: false,
      id: "Label5",
      group: ""
    }
  ]
 /*  controlData=[] */
  selectedAraea: any ={}
  refOfResizeDiv = ""
  checkedList=[]
  handleDragSelectorChange()
  {
    console.log(this.checkedList)
  }
  mounted() {
    EventBus.$on("createdGroup", (groupArray: any,controlDataArray: any,refOfResizeDiv: any) => {
     
      this.groupArray = groupArray;
      this.controlData=controlDataArray 
      this.refOfResizeDiv= refOfResizeDiv
      console.log("app.vue",this.controlData)
    });
  }
  dargSelector()
  {
    EventBus.$emit('dragSelector',(this as any).$refs["dragSelector"].selectAreaStyle)

  }
  handleMouseDown()
  {
        EventBus.$emit('mouseDown')
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
.drag-selector {
  display: flex;
  align-content: flex-start;
  flex-wrap: wrap;
  padding: 10px;
  margin-top: 20px;
}
</style>
