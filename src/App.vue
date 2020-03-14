<template>
  <div class="main-contain" @keydown="keyMove">
    <div class="tetris-block">
      <div v-for="i in elements" :key="i.pos" class="tetris-row">
        <div v-for="j in elements[i.pos].mass" :key="j.key" class="tetris-column">
            <div :class="'grid-elem' + j.style"></div>
        </div>
      </div>
    </div>
    <button @click="createFigure">wadawd</button>
  </div>
</template>

<script>
export default {
    data(){
        return{
            rows: 16,
            colums: 10,
            elements:[],
            delta: 0,
            objects:[
                {
                    width: 2,
                    height: 2
                },
                {
                    width: 4,
                    height: 1
                }
            ],
            currentFigure: {}
        }
    },
    methods:{
        createFigure(){
            let fP = this.objects[1];
            this.currentFigure = fP;
            for(let i = 0; i < fP.height; i++){
                for(let j = this.marginCalc; j < fP.width+this.marginCalc; j++){
                    this.elements[i].mass[j].style = ' created';
                }
            }
            this.moveFigure();
        },
        keyMove(e){
            if(e.key == "ArrowRight"){
                this.delta += 1;
            }
            else if(e.key == "ArrowLeft"){
                this.delta -= 1;
            }
        },
        moveFigure(){
            let speed = 100;
            let way = 1;
            let fP = this.objects[1];
            this.currentFigure = fP;
            let interval = setInterval(()=>{
                for(let i = way; i < fP.height+way; i++){
                    let elMass = this.elements;
                    if(elMass[i-fP.height] != undefined && this.rows-(fP.height-1) > way){
                        this.elements[i-fP.height].mass = elMass[i-fP.height].mass.map((i)=>{return {key : i.key,style: ""}});
                    }
                    if(elMass[i-1] != undefined && this.rows-1 > way){
                        for (let z = this.marginCalc+fP.width; z < this.colums; z++){
                            this.elements[i-1].mass[z].style = "";
                        }   
                        for (let z = 0; z < this.marginCalc; z++){
                            this.elements[i-1].mass[z].style = "";
                        }
                    }
                    if(elMass[i+1] != undefined && this.rows-1 > way){
                        let nextRow = [...elMass[i+1].mass];
                        nextRow = nextRow.splice(this.marginCalc,fP.width);
                        console.log(nextRow);
                    }
                    for(let j = this.marginCalc; j < fP.width+this.marginCalc; j++){
                        if(this.elements[i] == undefined){
                            clearInterval(interval);
                        }
                        this.elements[i].mass[j].style = ' created';
                    }
                }
                if(this.rows-1 > way){
                    way++;
                }
                else{
                    for(let x of this.elements){
                        for(let y of x.mass){
                            if(y.style == " created"){
                                y.style = " grounded";
                            }
                        }
                    }
                    clearInterval(interval);
                }
            },speed);
        },
    },
    computed:{
        marginCalc(){
            return this.colums/2 - this.currentFigure.width/2 + this.delta;
        }
    },
    mounted(){
        let elements = [];
        for (let i = 0; i < this.rows; i++){
            let rowMass = [];
            for(let j = 0; j < this.colums; j++){
                rowMass.push({
                    key: Math.random()/Math.random(),
                    style: ''
                })
            }
            elements.push({
                mass : rowMass,
                pos : i
            });
        }
        this.elements = elements;
    }
}
</script>

<style>
*{
  margin: 0;
}
body{
  width: 100%;
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.main-contain{
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgb(95, 95, 95);
}
.tetris-block{
  width: 450px;
  height: 760px;
  background: rgb(141, 141, 141);
}
.tetris-row{
    width: 100%;
    display: flex;
}
.grid-elem{
  width: 43px;
  height: 45px;
  margin: 1px;
  background: rgb(119, 119, 119);
}
.created,.grounded{
    background: rgb(184, 184, 184);
}
</style>
