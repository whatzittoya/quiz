/* eslint-disable */
<template>
    <div id="large-header" class="large-header">
        <canvas id="demo-canvas"></canvas>
        <div class="main-title ">
            <img class="h-auto max-w-full rounded-lg w-96 mx-auto p-3" src="../assets/logo-phr.png" alt="image description">

            <h1 class=" text-cyan-50 text-5xl">Selamat Datang di Upstream Computing Quiz</h1>
            <div class="p-10">
                <a href="#/Countdown"
                    class="text-white bg-gradient-to-r from-cyan-500 to-blue-500 hover:bg-gradient-to-bl focus:ring-4 focus:outline-none focus:ring-cyan-300 dark:focus:ring-cyan-800 font-medium rounded-lg px-5 py-2.5 text-center mr-2 mb-2 text-5xl">Mulai</a>
            </div>
        </div>

    </div>
</template>

<script>
import * as raf from "../assets/js/rAF";
import * as easepack from "../assets/js/EasePack.min.js";
import * as tl from "../assets/js/TweenLite.min.js";
export default {
  name: "Welcome",
  created() {},
  mounted() {
    console.log(0);
    document.onreadystatechange = () => {
      console.log(1);
      if (document.readyState == "complete") {
        (function () {
          var width,
            height,
            largeHeader,
            canvas,
            ctx,
            points,
            target,
            animateHeader = true;

          // Main
          initHeader();
          initAnimation();
          addListeners();

          function initHeader() {
            width = window.innerWidth;
            height = window.innerHeight;
            target = { x: width / 2, y: height / 2 };

            largeHeader = document.getElementById("large-header");
            largeHeader.style.height = height + "px";

            canvas = document.getElementById("demo-canvas");
            canvas.width = width;
            canvas.height = height;
            ctx = canvas.getContext("2d");

            // create points
            points = [];
            for (var x = 0; x < width; x = x + width / 20) {
              for (var y = 0; y < height; y = y + height / 20) {
                var px = x + (Math.random() * width) / 20;
                var py = y + (Math.random() * height) / 20;
                var p = { x: px, originX: px, y: py, originY: py };
                points.push(p);
              }
            }

            // for each point find the 5 closest points
            for (var i = 0; i < points.length; i++) {
              var closest = [];
              var p1 = points[i];
              for (var j = 0; j < points.length; j++) {
                var p2 = points[j];
                if (!(p1 == p2)) {
                  var placed = false;
                  for (var k = 0; k < 5; k++) {
                    if (!placed) {
                      if (closest[k] == undefined) {
                        closest[k] = p2;
                        placed = true;
                      }
                    }
                  }

                  for (var k = 0; k < 5; k++) {
                    if (!placed) {
                      if (getDistance(p1, p2) < getDistance(p1, closest[k])) {
                        closest[k] = p2;
                        placed = true;
                      }
                    }
                  }
                }
              }
              p1.closest = closest;
            }

            // assign a circle to each point
            for (var i in points) {
              var c = new Circle(
                points[i],
                2 + Math.random() * 2,
                "rgba(255,255,255,0.3)"
              );
              points[i].circle = c;
            }
          }

          // Event handling
          function addListeners() {
            if (!("ontouchstart" in window)) {
              window.addEventListener("mousemove", mouseMove);
            }
            window.addEventListener("scroll", scrollCheck);
            window.addEventListener("resize", resize);
          }

          function mouseMove(e) {
            var posx = 0;
            var posy = 0;
            if (e.pageX || e.pageY) {
              posx = e.pageX;
              posy = e.pageY;
            } else if (e.clientX || e.clientY) {
              posx =
                e.clientX +
                document.body.scrollLeft +
                document.documentElement.scrollLeft;
              posy =
                e.clientY +
                document.body.scrollTop +
                document.documentElement.scrollTop;
            }
            // target.x = posx;
            //target.y = posy;
          }

          function scrollCheck() {
            if (document.body.scrollTop > height) animateHeader = false;
            else animateHeader = true;
          }

          function resize() {
            width = window.innerWidth;
            height = window.innerHeight;
            largeHeader.style.height = height + "px";
            canvas.width = width;
            canvas.height = height;
          }

          // animation
          function initAnimation() {
            animate();
            for (var i in points) {
              shiftPoint(points[i]);
            }
          }

          function animate() {
            if (animateHeader) {
              ctx.clearRect(0, 0, width, height);
              for (var i in points) {
                // detect points in range
                if (Math.abs(getDistance(target, points[i])) < 90000) {
                  points[i].active = 0.6;
                  points[i].circle.active = 0.9;
                } else if (Math.abs(getDistance(target, points[i])) < 150000) {
                  points[i].active = 0.3;
                  points[i].circle.active = 0.6;
                } else if (Math.abs(getDistance(target, points[i])) < 200000) {
                  points[i].active = 0.1;
                  points[i].circle.active = 0.3;
                } else {
                  points[i].active = 0;
                  points[i].circle.active = 0;
                }

                drawLines(points[i]);
                points[i].circle.draw();
              }
            }
            requestAnimationFrame(animate);
          }

          function shiftPoint(p) {
            TweenLite.to(p, 1 + 1 * Math.random(), {
              x: p.originX - 50 + Math.random() * 100,
              y: p.originY - 50 + Math.random() * 100,
              ease: Circ.easeInOut,
              onComplete: function () {
                shiftPoint(p);
              },
            });
          }

          // Canvas manipulation
          function drawLines(p) {
            if (!p.active) return;
            for (var i in p.closest) {
              ctx.beginPath();
              ctx.moveTo(p.x, p.y);
              ctx.lineTo(p.closest[i].x, p.closest[i].y);
              ctx.strokeStyle = "rgba(156,217,249," + p.active + ")";
              ctx.stroke();
            }
          }

          function Circle(pos, rad, color) {
            var _this = this;

            // constructor
            (function () {
              _this.pos = pos || null;
              _this.radius = rad || null;
              _this.color = color || null;
            })();

            this.draw = function () {
              if (!_this.active) return;
              ctx.beginPath();
              ctx.arc(
                _this.pos.x,
                _this.pos.y,
                _this.radius,
                0,
                2 * Math.PI,
                false
              );
              ctx.fillStyle = "rgba(156,217,249," + _this.active + ")";
              ctx.fill();
            };
          }

          // Util
          function getDistance(p1, p2) {
            return Math.pow(p1.x - p2.x, 2) + Math.pow(p1.y - p2.y, 2);
          }
        })();
      }
    };
  },
};
</script>


<style>
@import "https://www.marcoguglie.it/Codepen/AnimatedHeaderBg/demo-1/css/demo.css";

/* Header */
.large-header {
  position: relative;
  width: 100%;
  background: #333;
  overflow: hidden;
  background-size: cover;
  background-position: center center;
  z-index: 1;
}

#large-header {
  background-image: url("~@/assets/cloud-bg.jpg");
}

.main-title {
  position: absolute;
  margin: 0;
  padding: 0;
  text-align: center;
  top: 50%;
  left: 50%;
  -webkit-transform: translate3d(-50%, -50%, 0);
  transform: translate3d(-50%, -50%, 0);
}

.demo-1 .main-title {
  text-transform: uppercase;
  font-size: 4.2em;
  letter-spacing: 0.1em;
}

.main-title .thin {
  font-weight: 200;
}

@media only screen and (max-width: 768px) {
  .demo-1 .main-title {
    font-size: 3em;
  }
}
</style>