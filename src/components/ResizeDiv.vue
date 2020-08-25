<template>
  <div>
    <button @click="groupControl">Group</button>
    <div v-for="(control,index) in controlData" :key="index">
      <div
        :class="[control.isActivate?'mainDiv':'mainDiv1']"
        :style="{width:control.width,  height: control.height, left:control.left,top: control.top}"
        :ref="control.id"
        @mousedown.stop="handleDrag($event,control)"
      >
        <resize-handlers
          :controlData="controlData"
          :index="index"
          :refOfResizeDiv="$refs"
          :refName="control.id"
        />
        <label
          class="label-style"
          :style="control"
          @click.self="labelClick(index,control)"
          @dblclick="labelActivate(index,control)"
        >{{control.id}}</label>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
import ResizeHandlers from "./ResizeHandlers.vue";
import { EventBus } from "./event-bus";
@Component({
  components: {
    ResizeHandlers
  }
})
export default class ResizeDiv extends Vue {
  groupArray: any = [];
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
    }
  ];

  resizeDiv = "";
  refName = "";
  isActivate = false;
  trackinIndex = -1;

  labelClick(controlIndex: any, control: any) {
     
     
    if (control.group === "") {
      if (control.isActivate === true) {
        EventBus.$emit("curActControl", control);
      }
      this.trackinIndex = controlIndex;
      this.controlData[this.trackinIndex].isActivate = !this.controlData[
        this.trackinIndex
      ].isActivate;
    }
  }

  handleDrag(event: any, control: any) {
    console.log(control)
    if (control.group !== "" && control.isActivate === false) {
    
      EventBus.$emit("dragGroup", event,control);
    } else {
      EventBus.$emit("drag", event);
    }
  }

  groupControl() {
    const groupName = `group${this.groupArray.length + 1}`;
    this.groupArray.push(groupName);

    
    for (const controlKey in this.controlData) {
      if (this.controlData[controlKey].isActivate === true) {
        this.controlData[controlKey].group = groupName;
      }
      this.controlData[controlKey].isActivate = false;
    }
    EventBus.$emit("createdGroup", this.groupArray, this.controlData, this.$refs);
  }

  mounted() {
    EventBus.$emit("group-control", this.controlData, this.$refs);
  }
  labelActivate(controlIndex: any, control: any) {
    if (control.group !== "") {
      EventBus.$emit("curActControl", control);

      for (const controlKey in this.controlData) {
        if (control.id !== this.controlData[controlKey].id) {
          this.controlData[controlKey].isActivate = false;
        }
      }
      this.trackinIndex = controlIndex;
      this.controlData[this.trackinIndex].isActivate = !this.controlData[
        this.trackinIndex
      ].isActivate;
    }
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