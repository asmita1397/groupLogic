<template>
  <div
    class="mainDiv"
    :style="divStyle"
    ref="maindivRef"
    @mousedown.stop="handleMouseDown($event,'drag')"
  >
    <div class="handle handle-tl"  @mousedown.stop="handleMouseDown($event,'nw')"></div>
    <div class="handle handle-tr" @mousedown.stop="handleMouseDown($event,'ne')"></div>
    <div class="handle handle-tm" @mousedown.stop="handleMouseDown($event,'n-resize')"></div>
    <div class="handle handle-ml" @mousedown.stop="handleMouseDown($event,'w-resize')"></div>
    <div class="handle handle-mr" @mousedown.stop="handleMouseDown($event,'e-resize')"></div>
    <div class="handle handle-bl" @mousedown.stop="handleMouseDown($event,'sw')"></div>
    <div class="handle handle-br" @mousedown.stop="handleMouseDown($event,'se')"></div>
    <div class="handle handle-bm" @mousedown.stop="handleMouseDown($event,'s-resize')"></div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
import { EventBus } from "./event-bus";

@Component({
  components: {}
})
export default class ScrollDiv extends Vue {
  positions: any = {
    clientX: "",
    clientY: "",
    movementX: 0,
    movementY: 0
  };
  divStyle = {
    width: "0px",
    height: "0px",
    left: "0px",
    top: "0px",
    display: "none"
  };
  initialArray: any = {};

  topArray: any = [];
  leftArray: any = [];
  widthArray: any = [];
  heightArray: any = [];
  rightArray: any = [];
  bottomArray: any = [];

  perctopArray: any = [];
  percleftArray: any = [];
  percwidthArray: any = [];
  percheightArray: any = [];

  resizeDiv = "";
  selectedControls: any = [];
  controlData: any = [];
  refOfResizeDiv = "";
  mounted() {
    EventBus.$on("dragGroup", () => {
      this.selfFunction();
    });
    EventBus.$on("group-control", (groupControlData: any, groupRef: any) => {
      console.log(groupControlData, groupRef);
      this.controlData = groupControlData;
      this.refOfResizeDiv = groupRef;
    });
    console.log(this.controlData);

    EventBus.$on("createGroup", () => {
      this.createGroupBoundary();
    });
  }
  selfFunction() {
    EventBus.$emit("dataRef", this.divStyle, this.$refs);
  }

  createGroupBoundary() {
    this.selectedControls = [];
    for (let i = 0; i < this.controlData.length; i++) {
      if (this.controlData[i].group === "group1") {
        this.selectedControls = [...this.selectedControls, this.controlData[i]];
      }
    }
    const styleLeft = [];
    const styleWidth = [];
    const styleTop = [];
    const styleHeight = [];

    this.topArray = [];
    this.heightArray = [];
    this.rightArray = [];
    this.widthArray = [];
    this.leftArray = [];

    this.perctopArray = [];
    this.percleftArray = [];
    this.percwidthArray = [];
    this.percheightArray = [];
    this.bottomArray = [];

    for (const controlKey in this.selectedControls) {
      styleLeft.push(parseInt(this.selectedControls[controlKey].left));
      styleTop.push(parseInt(this.selectedControls[controlKey].top));
      styleWidth.push(
        parseInt(this.selectedControls[controlKey].width) +
          parseInt(this.selectedControls[controlKey].left)
      );
      styleHeight.push(
        parseInt(this.selectedControls[controlKey].height) +
          parseInt(this.selectedControls[controlKey].top)
      );
    }
    this.divStyle = {
      ...this.divStyle,
      left: `${Math.min(...styleLeft)}px`,
      top: `${Math.min(...styleTop)}px`,
      width: `${Math.max(...styleWidth) - Math.min(...styleLeft)}px`,
      height: ` ${Math.max(...styleHeight) - Math.min(...styleTop)}px`,
      display: "block"
    };
    console.log(this.divStyle);

    for (let i = 0; i < this.controlData.length; i++) {
      this.topArray.push(
        parseInt(this.controlData[i].top) - parseInt(this.divStyle.top)
      );
      this.heightArray.push(
        parseInt(this.controlData[i].height) - parseInt(this.divStyle.height)
      );

      this.bottomArray.push(
        parseInt(this.controlData[i].top + this.controlData[i].height) -
          parseInt(this.divStyle.top + this.divStyle.height)
      );
      this.rightArray.push(
        parseInt(this.controlData[i].left + this.controlData[i].width) -
          parseInt(this.divStyle.left + this.divStyle.width)
      );

      this.widthArray.push(
        parseInt(this.controlData[i].width) - parseInt(this.divStyle.width)
      );
      this.leftArray.push(
        parseInt(this.controlData[i].left) - parseInt(this.divStyle.left)
      );

      this.percheightArray.push(
        parseInt(this.controlData[i].height) / parseInt(this.divStyle.height)
      );

      this.percwidthArray.push(
        parseInt(this.controlData[i].width) / parseInt(this.divStyle.width)
      );

      //  this.perctopArray.push((parseInt(this.controlData[i].top) + parseInt(this.controlData[i].height)) /( parseInt(this.divStyle.top)+parseInt(this.divStyle.height)))

      this.perctopArray.push(
        parseInt(this.divStyle.top) / parseInt(this.controlData[i].top)
      );

      //  this.perctopArray.push(parseInt(this.controlData[i].top) / parseInt(this.divStyle.top))
    }
    this.initialArray = Object.assign({}, this.divStyle);
    console.log(this.perctopArray);
  }

  handleMouseDown(event: any, handler: string) {
    this.selectedControls = [];

    this.resizeDiv = handler;
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;
    for (let i = 0; i < this.controlData.length; i++) {
      if (this.controlData[i].group === "group1") {
        this.selectedControls = [...this.selectedControls, this.controlData[i]];
      }
    }

    console.log("nnnnnn", this.divStyle, this.controlData);

    document.onmousemove = this.elementDrag;
    document.onmouseup = this.closeDragElement;
  }

  elementDrag(event: any): void {
    event.preventDefault();

    this.positions.movementX = this.positions.clientX - event.clientX;
    this.positions.movementY = this.positions.clientY - event.clientY;
    this.positions.clientX = event.clientX;
    this.positions.clientY = event.clientY;

    let dragResizeControl: any = [];
    let dragResizeControl1: any = [];
    const dragResizeRef = (this as any).$refs["maindivRef"];

    dragResizeControl = this.divStyle;

    if (this.resizeDiv === "s-resize") {
      dragResizeControl.height =
        parseInt(dragResizeControl.height) - this.positions.movementY + "px";
    } else if (this.resizeDiv === "e-resize") {
      dragResizeControl.width =
        parseInt(dragResizeControl.width) - this.positions.movementX + "px";
    } else if (this.resizeDiv === "se") {
      dragResizeControl.width =
        parseInt(dragResizeControl.width) - this.positions.movementX + "px";
      dragResizeControl.height =
        parseInt(dragResizeControl.height) - this.positions.movementY + "px";
    } else if (this.resizeDiv === "n-resize") {
      dragResizeControl.top =
        dragResizeRef.offsetTop - this.positions.movementY + "px";
      dragResizeControl.height =
        parseInt(dragResizeControl.height) + this.positions.movementY + "px";
    } else if (this.resizeDiv === "w-resize") {
      dragResizeControl.left =
        dragResizeRef.offsetLeft - this.positions.movementX + "px";
      dragResizeControl.width =
        parseInt(dragResizeControl.width) + this.positions.movementX + "px";
    } else if (this.resizeDiv === "sw") {
      dragResizeControl.left =
        dragResizeRef.offsetLeft - this.positions.movementX + "px";
      dragResizeControl.width =
        parseInt(dragResizeControl.width) + this.positions.movementX + "px";
      dragResizeControl.height =
        parseInt(dragResizeControl.height) - this.positions.movementY + "px";
    } else if (this.resizeDiv === "ne") {
      dragResizeControl.top =
        dragResizeRef.offsetTop - this.positions.movementY + "px";
      dragResizeControl.width =
        parseInt(dragResizeControl.width) - this.positions.movementX + "px";
      dragResizeControl.height =
        parseInt(dragResizeControl.height) + this.positions.movementY + "px";
    } else if (this.resizeDiv === "nw") {
      dragResizeControl.top =
        dragResizeRef.offsetTop - this.positions.movementY + "px";
      dragResizeControl.left =
        dragResizeRef.offsetLeft - this.positions.movementX + "px";
      dragResizeControl.width =
        parseInt(dragResizeControl.width) + this.positions.movementX + "px";
      dragResizeControl.height =
        parseInt(dragResizeControl.height) + this.positions.movementY + "px";
    }

    for (let i = 0; i < this.controlData.length; i++) {
      const dragResizeRef1 = (this as any).refOfResizeDiv[
        this.controlData[i].id
      ][0];
      dragResizeControl1 = this.controlData[i];

      if (this.resizeDiv === "s-resize") {
        dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.topArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(this.initialArray.top) +
          "px";
        console.log(this.positions.movementY);
        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
      } else if (this.resizeDiv === "e-resize") {
        dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.leftArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(this.initialArray.left) +
          "px";
        dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
      } else if (this.resizeDiv === "se") {
        dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.topArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(this.initialArray.top) +
          "px";

        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
        dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.leftArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(this.initialArray.left) +
          "px";
        dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
      } else if (this.resizeDiv === "n-resize") {
        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
        dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.bottomArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(dragResizeControl.top) +
          "px";
        console.log(top);
      } else if (this.resizeDiv === "w-resize") {
        dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.rightArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(dragResizeControl.left) +
          "px";
        dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
      } else if (this.resizeDiv === "sw") {
        dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.rightArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(dragResizeControl.left) +
          "px";
        dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
        dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.topArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(this.initialArray.top) +
          "px";
      } else if (this.resizeDiv === "ne") {
        dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.bottomArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(dragResizeControl.top) +
          "px";
        dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.leftArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(this.initialArray.left) +
          "px";
        dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
      }
      else if (this.resizeDiv === "nw") {
            dragResizeControl1.top =
          (parseInt(dragResizeControl.height) * this.bottomArray[i]) /
            parseInt(this.initialArray.height) +
          parseInt(dragResizeControl.top) +
          "px";
           dragResizeControl1.left =
          (parseInt(dragResizeControl.width) * this.rightArray[i]) /
            parseInt(this.initialArray.width) +
          parseInt(dragResizeControl.left) +
          "px";
            dragResizeControl1.width =
          parseInt(dragResizeControl.width) * this.percwidthArray[i] + "px";
        dragResizeControl1.height =
          parseInt(dragResizeControl.height) * this.percheightArray[i] + "px";
          }
    }
  }

  closeDragElement(): void {
    document.onmouseup = null;
    document.onmousemove = null;
    console.log(this.divStyle);
    this.createGroupBoundary();
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