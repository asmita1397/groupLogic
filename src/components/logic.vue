<template>
  <div class="mainDiv" :style="divStyle" ref="maindivRef">
    <div class="handle handle-tl"  @mousedown="handleMouseDown($event,'nw')"></div>
    <div class="handle handle-tr" @mousedown="handleMouseDown($event,'ne')"></div>
    <div class="handle handle-tm" @mousedown="handleMouseDown($event,'n-resize')"></div>
    <div class="handle handle-ml"   @mousedown="handleMouseDown($event,'w-resize')"></div>
    <div class="handle handle-mr"  @mousedown="handleMouseDown($event,'e-resize')"></div>
    <div class="handle handle-bl"  @mousedown="handleMouseDown($event,'sw')"></div>
    <div class="handle handle-br"  @mousedown="handleMouseDown($event,'se')"></div>
    <div class="handle handle-bm"  @mousedown="handleMouseDown($event,'s-resize')"></div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
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
   divStyle = {
    width: "100px",
    height: "100px",
    left: "100px",
    top: "100px"
  };
  resizeDiv=""
  initialArray: any ={}
  mounted()
  {
     this.initialArray=Object.assign({},this.divStyle)   
  }
  handleMouseDown(event: any,handler: string) {
     this.resizeDiv = handler
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;
    document.onmousemove = this.elementDrag;
    document.onmouseup = this.closeDragElement;
  }
   elementDrag(event: any): void {
    event.preventDefault();
    this.positions.movementX = this.positions.clientX - event.clientX;
    this.positions.movementY = this.positions.clientY - event.clientY;
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;
    
    if(this.resizeDiv==="nw")
    {
    this.divStyle.top =
      (this as any).$refs["maindivRef"].offsetTop -
      this.positions.movementY +
      "px";
    this.divStyle.left =
      (this as any).$refs["maindivRef"].offsetLeft -
      this.positions.movementX +
      "px";
     this.divStyle.width =
      parseInt(this.divStyle.width)+
      this.positions.movementX +
      "px";
      this.divStyle.height =
     parseInt(this.divStyle.height) +
      this.positions.movementY +
      "px";
    }
    else  if(this.resizeDiv==="ne")
    {
       this.divStyle.top =
      (this as any).$refs["maindivRef"].offsetTop -
      this.positions.movementY +
      "px";
     this.divStyle.width =
      parseInt(this.divStyle.width)-
      this.positions.movementX +
      "px";
      this.divStyle.height =
     parseInt(this.divStyle.height) +
      this.positions.movementY +
      "px";
    }
   
     else if(this.resizeDiv==="sw")
    {
       this.divStyle.left =
      (this as any).$refs["maindivRef"].offsetLeft -
      this.positions.movementX +
      "px";
          this.divStyle.width =
      parseInt(this.divStyle.width)+
      this.positions.movementX +
      "px";
      this.divStyle.height =
     parseInt(this.divStyle.height) -
      this.positions.movementY +
      "px";
     
    }
     else if(this.resizeDiv==="n-resize")
    {
       this.divStyle.top = parseInt(this.divStyle.height)>0 ?
      (this as any).$refs["maindivRef"].offsetTop -
      this.positions.movementY +
      "px":parseInt(this.divStyle.top)+"px"
      this.divStyle.height =
     parseInt(this.divStyle.height) +
      this.positions.movementY +
      "px";
     
    }
    
    else if(this.resizeDiv==="w-resize")
    {  
    this.divStyle.left =parseInt(this.divStyle.width)>0 ?
      (this as any).$refs["maindivRef"].offsetLeft -
      this.positions.movementX +
      "px":this.divStyle.left;
     this.divStyle.width =
      parseInt(this.divStyle.width)+
      this.positions.movementX +
      "px";
    }
    
   
  }
  closeDragElement(): void {
    document.onmouseup = null;
    document.onmousemove = null;
  }

 
}
</script>
<style scoped>
.mainDiv {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 100px;
  top: 100px;
  background-color: lightsalmon;
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

.handle {
  box-sizing: border-box;
  position: absolute;
  width: 6px;
  height: 6px;

  background: white;
  border: 1px solid #333;
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