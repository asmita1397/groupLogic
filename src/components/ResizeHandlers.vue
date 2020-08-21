<template>
  <div>
    <div
      :id="controlData[index].id"
      :class="[controlData[index].isActivate?'handle handle-tl':null]"
      @mousedown.stop="handleMouseDown($event,'nw',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-tr':null]"
      @mousedown.stop="handleMouseDown($event,'ne',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-tm':null]"
      @mousedown.stop="handleMouseDown($event,'n-resize',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-ml':null]"
      @mousedown.stop="handleMouseDown($event,'w-resize',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-mr':null]"
      @mousedown.stop="handleMouseDown($event,'e-resize',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-bl':null]"
      @mousedown.stop="handleMouseDown($event,'sw',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-br':null]"
      @mousedown.stop="handleMouseDown($event,'se',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
    <div
      :class="[controlData[index].isActivate?'handle handle-bm':null]"
      @mousedown.stop="handleMouseDown($event,'s-resize',index)"
      :style="{
          backgroundColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'white':'black',
          borderColor:activatedControl===controlData[index].id && controlData[index].isActivate ?'black':'white'
      }"
    ></div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
import { EventBus } from "./event-bus";

@Component({
  components: {}
})
export default class ResizeDiv extends Vue {
  positions: any = {
    clientX: "",
    clientY: "",
    movementX: 0,
    movementY: 0
  };
  @Prop() controlData!: any;
  @Prop() index!: any;
  @Prop() refOfResizeDiv!: any;
  activatedControl = "";
  refName = "";
  resizeDiv = "";
  selectedControls: any = [];
  groupData={}
  refOfGRoup=""
  mounted() {
    console.log("entered")
    EventBus.$on("drag", (event: any,ref: any) => {
      this.refOfResizeDiv = ref;
      this.handleMouseDown(event, "drag");
    });
    EventBus.$on("curActControl", (controlId: any) => {
      this.activatedControl = controlId;
    });

     EventBus.$on("dataRef",(groupData: any,refOfGRoup: any)=>
     {
         
          this.groupData=groupData
          this.refOfGRoup=refOfGRoup
     })
     
  }

  handleMouseDown(event: any, handler: string) {
    this.selectedControls = [];

    this.resizeDiv = handler;
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;
    for (let i = 0; i < this.controlData.length; i++) {
      if (this.controlData[i].group === 'group1') {
        this.selectedControls = [...this.selectedControls, this.controlData[i]];
      }
    }
    document.onmousemove = this.elementDrag;
    document.onmouseup = this.closeDragElement;
  }
  elementDrag(event: any): void {
    event.preventDefault();

    this.positions.movementX = this.positions.clientX - event.clientX;
    this.positions.movementY = this.positions.clientY - event.clientY;
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;
    /* console.log("resize the view", this.refOfResizeDiv); */
    for (let i = 0; i < this.controlData.length; i++) {
      for (let j = 0; j < this.selectedControls.length; j++) {
        if (this.controlData[i].id === this.selectedControls[j].id) {
          const dragResizeRef = (this as any).refOfResizeDiv[this.controlData[i].id][0];
          const dragResizeControl = this.controlData[i];
        
          if (this.resizeDiv === "nw") {
            dragResizeControl.top =
              dragResizeRef.offsetTop - this.positions.movementY + "px";
            dragResizeControl.left =
              dragResizeRef.offsetLeft - this.positions.movementX + "px";
            dragResizeControl.width =
              parseInt(dragResizeControl.width) +
              this.positions.movementX +
              "px";
            dragResizeControl.height =
              parseInt(dragResizeControl.height) +
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "ne") {
            dragResizeControl.top =
              dragResizeRef.offsetTop - this.positions.movementY + "px";
            dragResizeControl.width =
              parseInt(dragResizeControl.width) -
              this.positions.movementX +
              "px";
            dragResizeControl.height =
              parseInt(dragResizeControl.height) +
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "se") {
            dragResizeControl.width =
              parseInt(dragResizeControl.width) -
              this.positions.movementX +
              "px";
            dragResizeControl.height =
              parseInt(dragResizeControl.height) -
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "sw") {
            dragResizeControl.left =
              dragResizeRef.offsetLeft - this.positions.movementX + "px";
            dragResizeControl.width =
              parseInt(dragResizeControl.width) +
              this.positions.movementX +
              "px";
            dragResizeControl.height =
              parseInt(dragResizeControl.height) -
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "n-resize") {
            dragResizeControl.top =
              dragResizeRef.offsetTop - this.positions.movementY + "px";
            dragResizeControl.height =
              parseInt(dragResizeControl.height) +
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "s-resize") {
            dragResizeControl.height =
              parseInt(dragResizeControl.height) -
              this.positions.movementY +
              "px";
          } else if (this.resizeDiv === "w-resize") {
            dragResizeControl.left =
              dragResizeRef.offsetLeft - this.positions.movementX + "px";
            dragResizeControl.width =
              parseInt(dragResizeControl.width) +
              this.positions.movementX +
              "px";
          } else if (this.resizeDiv === "e-resize") {
            dragResizeControl.width =
              parseInt(dragResizeControl.width) -
              this.positions.movementX +
              "px";
          } else if (this.resizeDiv === "drag") {
            dragResizeControl.top =
              dragResizeRef.offsetTop - this.positions.movementY + "px";
            dragResizeControl.left =
              dragResizeRef.offsetLeft - this.positions.movementX + "px";
              EventBus.$emit('dragGroup')
              if(Object.keys(this.groupData).length!==0)
              {
                 this.groupData.top=this.refOfGRoup["maindivRef"].offsetTop - this.positions.movementY + "px";
                 this.groupData.left =this.refOfGRoup["maindivRef"].offsetLeft - this.positions.movementX + "px";
              }
          }
        }
      }
    }
  }
  closeDragElement(): void {
    document.onmouseup = null;
    document.onmousemove = null;
  }
}
</script>

<style  scoped>
.handle {
  box-sizing: border-box;
  position: absolute;
  width: 6px;
  height: 6px;

  background: white;
  border: 1px solid #333;
}
.handleActivate{
box-sizing: border-box;
  position: absolute;
  width: 6px;
  height: 6px;

  background: black;
  border: 1px solid white;
}
.handle-tl {
  top: -5px;
  left: -5px;
  cursor: nw-resize;
}
.handle-tm {
  top: -5px;
  left: 50%;
  margin-left: -5px;
  cursor: n-resize;
}
.handle-tr {
  top: -5px;
  right: -5px;
  cursor: ne-resize;
}
.handle-ml {
  top: 50%;
  margin-top: -5px;
  left: -5px;
  cursor: w-resize;
}
.handle-mr {
  top: 50%;
  margin-top: -5px;
  right: -5px;
  cursor: e-resize;
}
.handle-bl {
  bottom: -5px;
  left: -5px;
  cursor: sw-resize;
}
.handle-bm {
  bottom: -5px;
  left: 50%;
  margin-left: -5px;
  cursor: s-resize;
}
.handle-br {
  bottom: -5px;
  right: -5px;
  cursor: se-resize;
}
</style>