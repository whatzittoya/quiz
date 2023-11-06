/* eslint-disable */
<template>
    <div class="antialiased text-slate-100 bg-slate-900 body">
        <div class="scene">
  <div class="wrap">
      <div class="wall wall-right"></div>
      <div class="wall wall-left"></div>   
      <div class="wall wall-top"></div>
      <div class="wall wall-bottom"></div> 
      <div class="wall wall-back"></div>    
  </div>
  <div class="wrap">
      <div class="wall wall-right"></div>
      <div class="wall wall-left"></div>   
      <div class="wall wall-top"></div>
      <div class="wall wall-bottom"></div>   
      <div class="wall wall-back"></div>    
  </div>
   <div class="flex w-full h-screen justify-center items-center">
            <div class="container flex items-center justify-center absolute" >
        <span id="c" class="text-gray-200" style="font-family: 'Orbitron', sans-serif ;font-size: 10vw;"></span>

            </div>
        </div>
</div>

       
    </div>
</template>

<script>
import * as $ from "../assets/js/jquery";

export default {
  name: "Countdown",

  mounted() {
    var counter = 3;
    var audio = new Audio(require("../assets/sound/beep.mp3"));
    var timer = setInterval(function () {
      if (counter > 0) {
        console.log(counter);
        audio.play();
      }

      //   $("#countdown").remove();

      //   var countdown = $(
      //     '<span id="countdown" style="font-family: \'Orbitron\' sans-serif ;">' +
      //       (counter == 0 ? "BEGIN!" : counter) +
      //       "</span>"
      //   );

      //   countdown.appendTo($(".container"));
      //   setTimeout(() => {
      // if (counter > -1) {
      //   $("#countdown").css({
      //     "font-family": "'Orbitron' sans-serif",
      //     "font-size": "10vw",
      //   });
      // } else {
      //   $("#countdown").css({
      //     "font-size": "10vw",
      //   });
      // }

      $("#c").text(counter);
      //   }, 20);
      counter--;
      if (counter == -1) {
        clearInterval(timer);
        window.location.href = "#/Quiz";
      }
    }, 1000);
  },
};
</script>
<style>
@import url("https://fonts.googleapis.com/css?family=Orbitron");
</style>

<style>
.wall {
  background: url(https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/PIA09959-1280x800_copy.jpg);
  background-size: cover;
}

.body {
  height: 100%;
  width: 100%;
  overflow: hidden;
}

.body {
  background: #000;
  text-align: center;
}

.body:before {
  content: "";
  display: inline-block;
  height: 100%;
  vertical-align: middle;
}

.scene {
  display: inline-block;
  vertical-align: middle;
  perspective: 5px;
  perspective-origin: 50% 50%;
  position: relative;
}

.wrap {
  position: absolute;
  width: 1000px;
  height: 1000px;
  left: -500px;
  top: -500px;
  transform-style: preserve-3d;
  animation: move 12s infinite linear;
  animation-fill-mode: forwards;
}

.wrap:nth-child(2) {
  animation: move 12s infinite linear;
  animation-delay: 6s;
}

.wall {
  width: 100%;
  height: 100%;
  position: absolute;
  opacity: 0;
  animation: fade 12s infinite linear;
  animation-delay: 0;
}

.wrap:nth-child(2) .wall {
  animation-delay: 6s;
}

.wall-right {
  transform: rotateY(90deg) translateZ(500px);
}

.wall-left {
  transform: rotateY(-90deg) translateZ(500px);
}

.wall-top {
  transform: rotateX(90deg) translateZ(500px);
}

.wall-bottom {
  transform: rotateX(-90deg) translateZ(500px);
}

.wall-back {
  transform: rotateX(180deg) translateZ(500px);
}

@keyframes move {
  0% {
    transform: translateZ(-500px) rotate(0deg);
  }
  100% {
    transform: translateZ(500px) rotate(0deg);
  }
}

@keyframes fade {
  0% {
    opacity: 0;
  }
  25% {
    opacity: 1;
  }
  75% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style> 
