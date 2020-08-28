<template>
  <div>
    <button @click.stop="groupControl" @mousedown.stop>Group</button>
    <button @click.stop="unGroupControl"  @mousedown.stop>Ungroup</button>

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
  @Prop() refName: any;
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
  ];
  selectedArea: any = {};
  curActiveControl = "";
  resizeDiv = "";

  isActivate = false;
  trackinIndex = -1;
  selectedGroupArray: any = [];
  activatedControl: any = [];

  labelClick(controlIndex: any, control: any) {
    if (control.group === "") {
      if (control.isActivate === true) {
        this.curActiveControl = control.group;
        EventBus.$emit("curActControl", control);
      }
      this.trackinIndex = controlIndex;
      this.controlData[this.trackinIndex].isActivate = !this.controlData[
        this.trackinIndex
      ].isActivate;
    }
  }

  handleDrag(event: any, control: any) {


    if (control.group !== "" && control.isActivate === false) {

      console.log("first")
      EventBus.$emit("dragGroup", event, control);
    } else if (control.isActivate === true && this.groupArray.length !== 0 && control.group==="") {
      for (let i = 0; i < this.groupArray.length; i++) {
        if (
          this.refName["groupName"][i].childNodes[0].style.display === "block"
        ) {
          EventBus.$emit(
            "dragMutipleControl",
            event,
            this.groupArray[i],
          );
        }
      }
    } else if(control.isActivate === true )  {

      console.log("third")

      EventBus.$emit("drag", event);
    }

    console.log("fourth")
  }

  groupingSelectedControl(groupName: string) {
    for (const controlKey in this.controlData) {
      if (this.controlData[controlKey].isActivate === true) {
        this.controlData[controlKey].group = groupName;
      }
      this.controlData[controlKey].isActivate = false;
    }
  }
  activatedControlList() {
    this.activatedControl = [];
    for (const controlKey in this.controlData) {
      if (this.controlData[controlKey].isActivate === true) {
        this.activatedControl.push(controlKey);
      }
    }
  }
  createSelectedGroupArray() {
    let groupName = "";
    this.selectedGroupArray = [];
    for (let i = 0; i < this.groupArray.length; i++) {
      if (
        this.refName["groupName"][i].childNodes[0].style.display === "block"
      ) {
        groupName = (this as any).refName["groupName"][i].childNodes[0].id;
        this.selectedGroupArray.push(groupName);
      }
    }
  }
  unGroupControl() {
    let groupName = "";

    for (const controlKey in this.controlData) {
      if (this.controlData[controlKey].isActivate === true) {
        groupName = this.controlData[controlKey].group;
        this.controlData[controlKey].isActivate = false;
      }
    }
    groupName = groupName ? groupName : "nogroup";
    console.log("------------------------>", groupName);
    for (const controlKey in this.controlData) {
      if (this.controlData[controlKey].group === groupName) {
        console.log("object",groupName);
        this.controlData[controlKey].group = "";
        this.controlData[controlKey].isActivate = true;
      }
    }
    for (let j = 0; j < this.groupArray.length; j++) {
      if (this.groupArray[j] === groupName) {
        this.groupArray.splice(j, 1);
      }
    }
    EventBus.$emit("createBoundary");
  }
  groupControl() {
    this.activatedControlList();
    if (this.groupArray.length !== 0) {
      this.createSelectedGroupArray();
      let groupName = "";
      console.log(this.selectedGroupArray.length);
      if (this.selectedGroupArray.length > 1) {
        for (let i = 1; i < this.selectedGroupArray.length; i++) {
          for (const controlKey in this.controlData) {
            if (this.controlData[controlKey].group === this.groupArray[i]) {
              this.controlData[controlKey].group = this.groupArray[0];
            }
          }
          for (let j = 0; j < this.groupArray.length; j++) {
            if (this.groupArray[j] === this.groupArray[i]) {
              this.groupArray.splice(j, 1);
            }
          }
        }
        EventBus.$emit("createBoundary");
      } else if (this.selectedGroupArray.length === 1) {
        groupName = (this as any).refName["groupName"][0].childNodes[0].id;
        console.log((this as any).refName);
        this.groupingSelectedControl(groupName);
        EventBus.$emit("createBoundary");
      } else {
        groupName = `group${this.groupArray.length + 1}`;
        this.groupArray.push(groupName);
        this.groupingSelectedControl(groupName);
      }
    } else if (this.activatedControl.length > 1) {
      const groupName = `group${this.groupArray.length + 1}`;
      this.groupArray.push(groupName);
      this.groupingSelectedControl(groupName);
    }

    console.log(this.groupArray);
    EventBus.$emit(
      "createdGroup",
      this.groupArray,
      this.controlData,
      this.$refs
    );
  }

  /*  mounted() {
    EventBus.$emit("group-control");
  } */
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

  mounted() {
    EventBus.$on("dragSelector", (selectedArea: any) => {
      console.log(selectedArea);

      this.selectedArea = selectedArea;
      const left = parseInt(this.selectedArea.left);
      const right =
        parseInt(this.selectedArea.left) + parseInt(this.selectedArea.width);
      const top = parseInt(this.selectedArea.top);
      const bottom =
        parseInt(this.selectedArea.top) + parseInt(this.selectedArea.height);

      for (let i = 0; i < this.controlData.length; i++) {
        if (
          left <=
            parseInt(this.controlData[i].left) +
              parseInt(this.controlData[i].width) &&
          right >= parseInt(this.controlData[i].left) &&
          top <=
            parseInt(this.controlData[i].top) +
              parseInt(this.controlData[i].height) &&
          bottom >= parseInt(this.controlData[i].top)
        ) {
          if (this.controlData[i].group === "") {
            this.controlData[i].isActivate = true;
          }
          else
          {
              EventBus.$emit('dragSelectGroup', this.controlData[i].group)
          }
        }
      }
    });
    EventBus.$on("mouseDown", () => {
      for (let i = 0; i < this.controlData.length; i++) {
        this.controlData[i].isActivate = false;

      }
    });
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