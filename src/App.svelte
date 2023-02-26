<script>
// Image Assets -->
import EMPTY_HOLE from './assets/empty-hole.png';
import HAM_HIT from "./assets/hit-ham.png"
import ANIM_HAM from './assets/anim-ham.gif';
import ANIM_SNAKE from './assets/anim-snake.gif';
import HEART from "./assets/heart.png";
import TEN_HOLE from "./assets/ten-hole.gif"
import HEART_HOLE from "./assets/heart-hole.gif";

// Sound Assets -->
import GAME_OVER_SOUND from "./assets/game_over.wav";
import GAME_LOAD_SOUND from "./assets/game_load.wav";
import PUNCH_SOUND from "./assets/punch.wav";
import SNAKE_BITE_SOUND from "./assets/snake_bite.wav";
import BONUS_SOUND from "./assets/bonus.wav";

const no_cells = 36;
const rand_bool_stack = new Array(100).fill(false);

function fn_grid(){
 let arr = [];
 for(let i = 0; i < no_cells; i++){
  arr.push({bg:EMPTY_HOLE,hamster:false,snake:false,heart:false,ten:false})
 }
 return arr;
}

function play_music(music_file) {
    let audioElement = new Audio();
    audioElement.src = music_file;
    audioElement.play();
  }

let cells = fn_grid();
let score = 0;
let time = 60;
let life = 3;
let speed = 1300;
let num_snake = 6;
let life_stack = [HEART,HEART,HEART];
let onScreenMessage = "Press To Start!";
let currentGameState = "idel"; //  pause, 'over', 'run';

$: score_str = `Score ${score}`;
$: highScore = localStorage.getItem('score') || 0;

function handle_click(i){
 if(cells[i].snake){
   life = life - 1;
   life_stack.length = life_stack.length - 1;
   cells[i].snake = false;
   play_music(SNAKE_BITE_SOUND);
 }

 if(cells[i].hamster){
     cells[i].hamster = false;
     score = score +1;
     const highScore = parseInt(localStorage.getItem("score")) || 0
     if(score>highScore){
      localStorage.setItem('score',score+"");
     }
     cells[i].bg = HAM_HIT;
     play_music(PUNCH_SOUND);
  }
  if(cells[i].ten){
     time = time + 10;
     cells[i].bg= EMPTY_HOLE;
     cells[i].ten = false;
     play_music(BONUS_SOUND);
  }
  if(cells[i].heart){
    life = life + 1;
    life_stack = [...life_stack,HEART];
    cells[i].bg = EMPTY_HOLE;
    cells[i].heart = false;
    play_music(BONUS_SOUND);
  }
}



function up_level(x){
  if(score === x){
    num_snake = num_snake + 3;
    speed = speed - 100;
  }
}

rand_bool_stack[Math.floor(Math.random() * rand_bool_stack.length)] = true;

function update(){

up_level(5);
up_level(10);
up_level(15);
up_level(20);
up_level(30);
up_level(40);
up_level(50);
up_level(60);

cells = fn_grid();
highScore = localStorage.getItem('score') || 0;
let snake_rand_stack = [];
let rand = Math.floor(Math.random()*cells.length);
let rand_heart = Math.floor(Math.random()*cells.length);
let rand_ten = Math.floor(Math.random()*cells.length);

for(let i = 0; i < num_snake; i++){
 let snake_rand_num = Math.floor(Math.random() * cells.length)
 if(snake_rand_num !== rand){
   snake_rand_stack.push(snake_rand_num);
 }
}

if(currentGameState==="run"){
cells[rand].bg = ANIM_HAM;
cells[rand].hamster = true;
for(let i = 0; i<snake_rand_stack.length; i++){
 cells[snake_rand_stack[i]].bg = ANIM_SNAKE;
 cells[snake_rand_stack[i]].snake = true;
 if(rand_ten !== snake_rand_stack[i] && rand_ten !== rand_heart && rand_ten !== rand && time < 40){
   let temp_rand = Math.floor(Math.random()*rand_bool_stack.length);
     if(rand_bool_stack[temp_rand]){
       cells[rand_ten].bg = TEN_HOLE;
       cells[rand_ten].ten = true;
   }
 }
 if(rand_heart !== snake_rand_stack[i] && rand_heart !== rand_ten && rand_heart !== rand && life_stack.length < 3){
   let temp_rand = Math.floor(Math.random() * rand_bool_stack.length);
    if(rand_bool_stack[temp_rand]){
      cells[rand_heart].bg = HEART_HOLE;
      cells[rand_heart].heart = true;
   }
 }
}

}
if(time <= 0 || life <= 0){
 currentGameState = "over"
 if(currentGameState == "over")
 play_music(GAME_OVER_SOUND);
}
}

function count_down(){
  if(time >= 1 && life >= 1 && currentGameState==="run"){
  time = time - 1;
  }
}



function startGame(count){

  onScreenMessage = count;


  if(count===0){

    changeGameState("run");
    return;
  }

  setTimeout(()=>startGame(count-1),1000);
}

function changeGameState(value){
  currentGameState  = value;
}


function reset(){
   cells = fn_grid();
   score = 0;
   time = 60;
   currentGameState = "idel"
   life = 3;
   num_snake = 6;
   speed = 1300;
   life_stack = [HEART,HEART,HEART];
   play_music(GAME_LOAD_SOUND);

   startGame(3)
}

play_music(GAME_LOAD_SOUND);
setInterval(update,speed)
setInterval(count_down,1000)

</script>

<main>
<div class="status">
<div class="lifes">
{#each life_stack as life}
<div class="life" style="background-image: url('{life}');"></div>
{/each}
</div>
<h1>{score_str}</h1>
</div>
<div style="position:relative; display:flex; flex-direction:column;justify-content:center; align-items:center">
  <div  class="grid">
    {#each cells as cell,i}
    <button style="background-image: url('{cell.bg}');" on:click={() => handle_click(i)} >{" "}</button>
    {/each}
    
    
    </div>
    {#if currentGameState !== "run" }
    <div on:click={()=>startGame(3)} class="welcome"> {currentGameState==="over"?"Game Over!": currentGameState==="pause"?"Paused!": onScreenMessage}</div>
    {/if}
</div>


<div style="display:flex; justify-content:space-between; background:#d97643; padding:0 2rem">
  <h1>{time}</h1>
<h1>High Score {highScore}</h1>
</div>


<div class="control">
<button class="reset" on:click={reset}>RESET</button>
{#if currentGameState!=="over"}
<button class="state" on:click={()=>changeGameState(currentGameState==="run"?"pause":"run")}>{ currentGameState=== "run" ?"Pause":"Start"}</button>
{/if}
</div>
</main>
<style>
main{
  zoom:0.5;
  /* padding-bottom: 70%; */
}
.grid{
  display: grid;
  background-color: #d97643;
  grid-template-columns: repeat(6,128px);
}
.reset{
  border: none;
  background-color: green;
  float: right;
  font-size:30px;
  width: 128px;
  height: 60px;
}
button{
  height:128px;
  font-size: 25px;
  text-align: center;
  border: none;
  border-radius: 0%;
  background-image:url();
  outline: none;
  padding: 10px 10px 10px 10px;
}


.welcome{
  position: absolute;
  /* top:50%; */
  /* left: 50%; */
  font-size: 80px;
  line-height: 70px;
  /* margin: auto; */
  /* transform: translate(-50%,-50%); */
  color: white;
  cursor: pointer;
  animation: scaleAnimation 1s infinite;
}


@keyframes scaleAnimation {
	0% {transform: scale(.9)  }
	50% { transform: scale(1) }
  100% { transform: scale(.9) }
}

h1{
  font-weight: bolder;
  font-size: 65px;
}

.life{
 height: 128px;
 width:  128px;
}

.lifes{
 display: flex;
 width: auto;
}

.status{
  display: flex;
  justify-content: space-between;
}
.state{
  font-size: 30px;
  width: 128px;
  height: 60px;
  background-color: blue;
}
.control{
 display: flex;
 justify-content: space-between;
}
</style>
