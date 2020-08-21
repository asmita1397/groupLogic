<template>
  <div >
    <!-- <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/> -->
  
   <div class="div-style">
     <ScrollDiv />
   <ResizeDiv/>
   </div>
 <!--  <InsertElement/> -->
 <!-- <ScrollDiv/> -->
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';
import AttachParent from "./components/AttachParent.vue";
import ResizeDiv from "./components/ResizeDiv.vue";
import InsertElement from "./components/InsertElement.vue";
import ScrollDiv from "./components/ScrollDiv.vue";
@Component({
  components: {
    HelloWorld,
    AttachParent,
    ResizeDiv,
    InsertElement,
    ScrollDiv
  },
})
export default class App extends Vue {
  
}
</script>

<style>
.div-style{
  width:800px;
  height:500px;
  overflow:hidden;
  position:absolute;
  background-color:lightblue;
}

.div-style1{
  width:500px;
  height:500px;
  overflow:hidden;
  position:absolute;
  background-color:red;
}
</style>
