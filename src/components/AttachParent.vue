<template>
<div  class="div-style">
  <button @click="handleClick">insert</button>
  <div  :ref="'parentDiv'.concat(1)" :style="divStyle" @mousedown="dragMouseDown($event,1)">
     <label  class="label-style" for="" :style="labelStyleArray[0]" ></label>
  </div>
  <template  v-for="(value,index) in labelStyleArray" >
  <label :ref="'labelRef'.concat(index)" class="label-style" :key="'labelRef'.concat(index)" :style="value" :draggable="true" @drag.stop="handleDrag($event,index)">name</label>
  </template>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  labelStyleArray =[
    {
         width:"109px",
         height:"50px",
         top:"25px",
         left:"10px",
    },
    {
         width:"100px",
         height:"50px",
         top:"100px",
         left:"200px",
    },
    {
         width:"100px",
         height:"50px",
         top:"120px",
         left:"400px",
    }
  ]
 divStyle={
    width:"100px",
         height:"50px",
         top:"120px",
         left:"400px",
         position:"absolute",
         background:"green"
 }
  positions: any = {
    clientX: "",
    clientY: "",
    movementX: 0,
    movementY: 0
  };
  paerentDiv=""
 handleDrag(e: any, key: string)
 {
    console.log(e.clientX,e.offsetX, key)

 }
 dragMouseDown(event: any, index: any): void {
    event.preventDefault();
    this.paerentDiv = 'parentDiv'.concat(index)
    // console.log(this.$refs[this.paerentDiv].offsetTop)
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

    const top =
      (this as any).$refs[this.paerentDiv].offsetTop -
      this.positions.movementY +
      "px";
    const left =
      (this as any).$refs[this.paerentDiv].offsetLeft -
      this.positions.movementX +
      "px";

     this.divStyle = {
               ...this.divStyle,
               top:top,
               left:left
            }
  }
  closeDragElement(): void {
    document.onmouseup = null;
    document.onmousemove = null;
  }





  handleClick()
  {
            let refName=''
            const styleLeft = []
            const styleWidth = []
            const styleTop = []
            const styleHeight = []

            for (const controlKey in this.labelStyleArray) {
             refName='labelRef'.concat(controlKey)
               
                 console.log((this as any).$refs[refName][0].draggable=false)
                styleLeft.push(parseInt(this.labelStyleArray[controlKey].left))
                styleTop.push(parseInt(this.labelStyleArray[controlKey].top))
                styleWidth.push(parseInt(this.labelStyleArray[controlKey].width) + parseInt(this.labelStyleArray[controlKey].left))
                styleHeight.push(parseInt(this.labelStyleArray[controlKey].height) + parseInt(this.labelStyleArray[controlKey].top))
            }
            this.divStyle = {
                left: `${Math.min(...styleLeft)-10}px`,
                top: `${Math.min(...styleTop)-10}px`,
                width: `${Math.max(...styleWidth) - Math.min(...styleLeft) +20}px`,
                height: ` ${Math.max(...styleHeight) - Math.min(...styleTop) + 20}px`,
                position:'absolute',
                background:'red'
            }
            console.log(this.divStyle)
   
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.div-style
{
  background-color:lightgrey;
  width:100%;
  height:200px;
  position: relative;
}
.label-style
{
  position: absolute;
  background-color: white;
}



</style>
