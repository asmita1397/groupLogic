<template>
  <div >
    <button @click="groupControl"> Group</button>
    <div v-for="(control,index) in controlData" :key="index">
      <div
        :class="[control.isActivate?'mainDiv':'mainDiv1']"
        :style="{width:control.width,  height: control.height, left:control.left,top: control.top}"
        :ref="control.id"
        @mousedown.stop="control.isActivate?handleDrag($event,index,control.id):null"
      >
         <resize-handlers :controlData="controlData" :index="index"  :refOfResizeDiv="$refs" :refName="control.id"/>
        <label class="label-style" :style="control" for @click.self="labelClick(index,control.id)">{{control.id}}</label>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import ResizeHandlers from "./ResizeHandlers.vue";
import { EventBus } from "./event-bus";
@Component({
  components: {
    ResizeHandlers
  }
})
export default class ResizeDiv extends Vue {
  positions: any = {
    clientX: "",
    clientY: "",
    movementX: 0,
    movementY: 0
  };
  controlData = [
    {
      width: "100px",
      height: "100px",
      left: "100px",
      top: "50px",
      isActivate:false,
      id:"Label1",
      group:''
    
    },
    {
      width: "120px",
      height: "100px",
      left: "120px",
      top: "250px",
      isActivate:false,
       id:"Label2",
       group:''

    },
    {
      width: "200px",
      height: "300px",
      left: "500px",
      top: "10px",
      isActivate:true,
       id:"Label3",
       group:''
    }
  ];

  resizeDiv = "";
  refName = "maindivRef";
  isActivate = false;
  trackinIndex = -1;
  


  labelClick(control: any,controlId: any) {
     
     EventBus.$emit("curActControl",controlId)
     this.trackinIndex = control;
    this.controlData[this.trackinIndex].isActivate = !this.controlData[this.trackinIndex].isActivate;
  }

  handleDrag(event: any)
  {
    
     EventBus.$emit('drag',event,this.$refs)
    
  }
 
  groupControl()
  {
     for (const controlKey in this.controlData) {
           if(this.controlData[controlKey].isActivate===true)
           {
               this.controlData[controlKey].group='group1'
           }  
     }          
    console.log(this.controlData)
        EventBus.$emit('createGroup')
  }
 
  mounted()
  {
      EventBus.$emit('group-control',this.controlData,this.$refs)
  }
}
</script>
<style scoped>
.mainDiv {
  position: absolute;
  --border-width: 5;
  --stripe-distance: 2px;
  border: calc(var(--border-width) * 1px) solid transparent;
  border-image: repeating-linear-gradient(
      -45deg,
      black,
      transparent 2px,
      transparent var(--stripe-distance),
      black calc(var(--stripe-distance) + 0.2px)
    )
    var(--border-width);
}
.mainDiv1 {
  position: absolute;
}

.label-style {
  float: left;
  overflow: hidden;
  background-color: white;
}
</style>