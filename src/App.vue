<template>
  <div class="main-contain" @keydown="keyMove">
    <div class="tetris-block">
      <div v-for="i in elements" :key="i.pos" class="tetris-row">
        <div v-for="j in elements[i.pos].mass" :key="j.key" class="tetris-column">
            <div :class="'grid-elem ' + j.style"></div>
        </div>
      </div>
    </div>
    <button @click="createFigure">start</button>
  </div>
</template>

<script>
export default {
    data(){
        return{
            rows: 20,
            colums: 10,
            elements:[],
            delta: 0,
            objects:[
                {
                    width: 2,
                    height: 2,
                    Lbuff: 0,
                    Rbuff: 0,
                    guideLine:[
                        [1,1],
                        [1,1],
                    ]
                },
                {
                    width: 4,
                    height: 4,
                    Lbuff: 2,
                    Rbuff: 1,
                    guideLine:[
                        [0,0,1,0],
                        [0,0,1,0],
                        [0,0,1,0],
                        [0,0,1,0]
                    ],
                    rotateGuideLine:[
                        [
                            [0,0,1,0],
                            [0,0,1,0],
                            [0,0,1,0],
                            [0,0,1,0]
                        ],
                        [
                            [0,0,0,0],
                            [0,0,0,0],
                            [1,1,1,1],
                            [0,0,0,0]
                        ],
                        [
                            [0,0,1,0],
                            [0,0,1,0],
                            [0,0,1,0],
                            [0,0,1,0]
                        ],
                        [
                            [0,0,0,0],
                            [0,0,0,0],
                            [1,1,1,1],
                            [0,0,0,0]
                        ]
                    ]
                },
                {
                    width: 3,
                    height: 3,
                    Lbuff: 0,
                    Rbuff: 1,
                    guideLine:[
                        [1,0,0],
                        [1,1,0],
                        [0,1,0]
                    ],
                    rotateGuideLine:[
                        [
                            [0,1,1],
                            [1,1,0],
                            [0,0,0]
                        ],
                        [
                            [0,0,1],
                            [0,1,1],
                            [0,1,0]
                        ],
                        [
                            [0,1,1],
                            [1,1,0],
                            [0,0,0]
                        ],
                        [
                            [1,0,0],
                            [1,1,0],
                            [0,1,0]
                        ]
                    ]
                },
                {
                    width: 3,
                    height: 3,
                    Lbuff: 0,
                    Rbuff: 1,
                    guideLine:[
                        [0,1,0],
                        [1,1,0],
                        [1,0,0]
                    ],
                    rotateGuideLine:[
                        [
                            [1,1,0],
                            [0,1,1],
                            [0,0,0]
                        ],
                        [
                            [0,1,0],
                            [1,1,0],
                            [1,0,0]
                        ],
                        [
                            [1,1,0],
                            [0,1,1],
                            [0,0,0]
                        ],
                        [
                            [1,0,0],
                            [1,1,0],
                            [0,1,0]
                        ]
                    ]
                },
                {
                    width: 3,
                    height: 3,
                    Lbuff: 0,
                    Rbuff: 0,
                    guideLine:[
                        [0,1,0],
                        [1,1,1],
                        [0,0,0]
                    ],
                    rotateGuideLine:[
                        [
                            [0,1,0],
                            [0,1,1],
                            [0,1,0]
                        ],
                        [
                            [0,0,0],
                            [1,1,1],
                            [0,1,0]
                        ],
                        [
                            [0,1,0],
                            [1,1,0],
                            [0,1,0]
                        ],
                        [
                            [0,1,0],
                            [1,1,1],
                            [0,0,0]
                        ]
                    ]
                },
            ],
            currentFigure: {},
            currentStep: 0,
            nextFigure: 4,
            currentRotate: -1,
        }
    },
    methods:{
        createFigure(){
            let fP = this.objects[this.nextFigure];
            this.currentFigure = fP;
            for(let i = 0; i < fP.height; i++){
                for(let j = this.marginCalc; j < fP.width+this.marginCalc; j++){
                    if(fP.guideLine[i][j] != 0){
                        this.elements[i].mass[j].style = 'created';
                    }
                }
            }
            this.moveFigure();
        },
        keyMove(e){
            let f = this.currentFigure;
            if(e.key == "ArrowRight"){
                if (this.marginCalc < this.colums-f.width + f.Rbuff){
                    this.delta += 1;
                }
            }
            else if(e.key == "ArrowLeft"){
                if (this.marginCalc > 0 - f.Lbuff){
                    this.delta -= 1;
                }
            }
            if(e.key == "ArrowUp"){
                this.rotateFigure();
            }
        },
        async moveFigure(){
            let speed = 300;
            let nextClear = true;
            this.currentFigure = this.objects[this.nextFigure];
            let interval = await setInterval(()=>{
                let way = this.currentStep;
                let fP = this.currentFigure;
                for(let i = way; i < fP.height+way; i++){
                    let elMass = this.elements;
                    this.renderFigure();
                    if(elMass[i-fP.height] != undefined && this.rows-(fP.height-1) > way){
                        this.elements[i-fP.height].mass = elMass[i-fP.height].mass.map((i)=>{
                            if(i.style === "created"){
                                return {
                                    key : i.key,style: ""
                                }
                            }
                            return {
                                key : i.key,style: i.style
                            }
                    });
                    }
                    if(elMass[i+1] != undefined && this.rows-1 > way){
                        let nextRow = [...elMass[i+1].mass];
                        let prevRow = [...elMass[i].mass];
                        nextRow = nextRow.splice(this.marginCalc,fP.width);
                        prevRow = prevRow.splice(this.marginCalc,fP.width);
                        for(let i = 0; i < nextRow.length; i++){
                            if(nextRow[i].style === "grounded" && prevRow[i].style === "created"){
                                nextClear = false;
                            }
                        }
                    }
                }
                if(this.rows-fP.height > way && nextClear){
                    this.currentStep++;
                }
                else{
                    for(let x of this.elements){
                        for(let y of x.mass){
                            if(y.style === "created"){
                                y.style = "grounded";
                            }
                        }
                    }
                    clearInterval(interval);
                    this.currentFigure = {};
                    this.delta = 0;
                    this.currentStep = 0;
                    this.currentRotate = -1;
                    this.lineCheck();
                }
            },speed);
            this.choseNextFigure();
        },
        choseNextFigure(){
            this.nextFigure = Math.floor(Math.random() * (this.objects.length-1 + 1));
        },
        lineCheck(){
            let toClear = [];
            for(let row of this.elements){
                if(row.mass.every((i)=>{return i.style == "grounded"})){
                    toClear.push(row);
                }
            }
            if (toClear.length > 0){
                for(let clear of toClear){
                    this.elements[clear.pos].mass = clear.mass.map((i)=>{return {key: i.key, style: ""}});
                    let mass = [...this.elements.slice(0,clear.pos)];
                    mass = mass.map((i)=> {return {mass: i.mass,pos: i.pos+1}});
                    this.elements.splice(1,clear.pos,...[...mass]);
                }
            }
            this.createFigure();
        },
        createRow(){
            let rowMass = [];
            for(let j = 0; j < this.colums; j++){
                rowMass.push({
                    key: Math.random()/Math.random(),
                    style: ''
                })
            }
            return rowMass;
        },
        rotateFigure(){
            let f = this.currentFigure.rotateGuideLine;
            this.currentRotate += 1;
            this.currentRotate %= 4;
            this.currentFigure.guideLine =f[this.currentRotate];
            this.renderFigure();
        },
        renderFigure(margin = this.marginCalc){
            let figure = this.currentFigure;
            for(let i = 0; i < figure.height; i++){
                if(this.elements[this.currentStep+i] != undefined){
                    let row = this.elements[this.currentStep+i].mass;
                    for(let j = 0; j < this.colums; j++){
                        if (j >= margin && j < figure.width+margin && figure.guideLine[i][j-margin] != 0)
                            row[j].style = 'created';
                        else if(row[j].style != 'grounded'){
                            row[j].style = '';
                        }
                    }
                }
            }
        }
    },
    computed:{
        marginCalc(){
            if (this.currentFigure.width === 3){
                return this.colums/2 - this.currentFigure.width/2 + this.delta + 0.5;
            }
            return this.colums/2 - this.currentFigure.width/2 + this.delta;
        }
    },
    watch:{
        marginCalc(newVal){
            let figure = this.currentFigure;
            this.renderFigure(newVal);
            for(let i = this.currentStep; i < figure.height+this.currentStep; i++){
                let elMass = this.elements;
                    if(elMass[i-figure.height] != undefined && this.rows-(figure.height-1) > this.currentStep){
                        this.elements[i-figure.height].mass = elMass[i-figure.height].mass.map((i)=>{
                            if(i.style === "created"){
                                return {
                                    key : i.key,style: ""
                                }
                            }
                            return {
                                key : i.key,style: i.style
                            }
                    });
                }
            }
        }
    },
    mounted(){
        let elements = [];
        for (let i = 0; i < this.rows; i++){
            elements.push({
                mass : this.createRow(),
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
html{
    height: 100%;
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
.created{
    background: rgb(184, 184, 184);
}
.grounded{
    background: rgb(158, 158, 158);
}
</style>
