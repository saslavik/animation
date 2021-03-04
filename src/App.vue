<template>
  <div id="app" ref="draggableContainer">
    <div id="square_parent">
      <div id="square"
      v-on="handlers"
      ></div>
    </div>
    <canvas id="c"
    width="120"
    height="85"
    style="top: 230px; left: 222px;"
    @mouseover="largeTri"
    @mouseleave="drawTri"
    v-on="handlers"></canvas>
    <svg id="svg">
      <line id="line" :x1="coor.c.x" :y1="coor.c.y" :x2="coor.square.x" :y2="coor.square.y"></line>
    </svg>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      // выбор действия: Мышь или палец
      handlers: {
        mousedown: this.dragMouseDown,
        touchstart: this.dragTouchDown
      },
      coor: {
        c: {
          x: null,
          y: null,
        },
        square: {
          x: null,
          y: null,
        }
      },
      positions: {
        clientX: undefined,
        clientY: undefined,
        movementX: 0,
        movementY: 0
      },
    };
  },
  mounted() {
    let c = document.getElementById("c");
    let ctx = c.getContext("2d");
    this.vueCanvas = ctx;
    this.drawTri();
  },
  created() {
    // при запуске страницы рисуем линию
    setTimeout(() => {
      this.drawLine();
    }, 0);
  },
  methods: {
    drawLine() {
      // данный метод рисует линию

      // находим координаты блоков
      let square = this.$el.querySelector('#square');
      let triangle = this.$el.querySelector('#c');

      // к объекту привязываем эти коодинаты, потом с объекта ссылаемся на координаты в линии
      this.coor.square.x = square.getBoundingClientRect().left + (square.getBoundingClientRect().width / 2);
      this.coor.square.y = square.getBoundingClientRect().top + (square.getBoundingClientRect().height / 2);
      this.coor.c.x = triangle.getBoundingClientRect().left + (triangle.getBoundingClientRect().width / 2);
      this.coor.c.y = triangle.getBoundingClientRect().top + (triangle.getBoundingClientRect().height / 2);
    },
    drawTri() {
      // очищаем канвас
      this.vueCanvas.clearRect(0, 0, 400, 200);

      // рисуем треугольник
      this.vueCanvas.fillStyle = '#333';
      this.vueCanvas.beginPath();
      this.vueCanvas.moveTo(60,10);
      this.vueCanvas.lineTo(110,75);
      this.vueCanvas.lineTo(10,75);
      this.vueCanvas.fill();
    },
    largeTri() {
      // удаляем старый треугольник
      this.vueCanvas.clearRect(0, 0, 400, 200);

      // рисуем большой
      this.vueCanvas.fillStyle = '#333';
      this.vueCanvas.beginPath();
      this.vueCanvas.moveTo(60,0);
      this.vueCanvas.lineTo(120,85);
      this.vueCanvas.lineTo(0,85);
      this.vueCanvas.fill();
    },
    dragMouseDown(event) {
      // при Нажатии маши на блок
      event.preventDefault()
      // определяем местоположение мыши по координатам
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      document.onmousemove = this.elementDrag
      document.onmouseup = this.closeDragElement
    },
    dragTouchDown(event) {
      // при нажатии пальцем на экран
      event.preventDefault()
      // определяем местоположение пальца по координатам
      this.positions.clientX = event.touches[0].pageX
      this.positions.clientY = event.touches[0].pageY
      document.ontouchmove = this.elementTouch
      document.ontouchend = this.closeDragElement
    },
    elementDrag(event) {
      // перемещение при помощи мыши
      event.preventDefault()
      this.positions.movementX = this.positions.clientX - event.clientX
      this.positions.movementY = this.positions.clientY - event.clientY
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      event.target.style.top = (event.target.offsetTop - this.positions.movementY) + 'px'
      event.target.style.left = (event.target.offsetLeft - this.positions.movementX) + 'px'
      // перерисовываем сразу линию
      this.coor[event.target.id].x = (event.target.offsetLeft - this.positions.movementX) + (event.target.getBoundingClientRect().width / 2) + 'px'
      this.coor[event.target.id].y = (event.target.offsetTop - this.positions.movementY) + (event.target.getBoundingClientRect().height / 2) + 'px'
    },
    elementTouch(event) {
      event.preventDefault()
      // перемещение при помощи пальца
      this.positions.movementX = this.positions.clientX - event.touches[0].pageX
      this.positions.movementY = this.positions.clientY - event.touches[0].pageY
      this.positions.clientX = event.touches[0].pageX
      this.positions.clientY = event.touches[0].pageY
      event.target.style.top = (event.target.offsetTop - this.positions.movementY) + 'px'
      event.target.style.left = (event.target.offsetLeft - this.positions.movementX) + 'px'
      this.coor[event.target.id].x = (event.target.offsetLeft - this.positions.movementX) + (event.target.getBoundingClientRect().width / 2) + 'px'
      this.coor[event.target.id].y = (event.target.offsetTop - this.positions.movementY) + (event.target.getBoundingClientRect().height / 2) + 'px'
    },
    closeDragElement () {
      // элемент для остановки событий
      document.onmouseup = null
      document.onmousemove = null
      document.ontouchmove = null
      document.ontouchend = null
    }
  },
}
</script>

<style lang="scss">
#app {
  z-index: 5;
  width: 100%;
  height: 100%;
}
#square_parent {
  z-index: 9;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
#square {
  width: 100px;
  height: 100px;
  top: 50%;
  left: 50%;
  position: absolute;
  background-color: #333;
  // animation
  -webkit-animation-name: rotation;
  -webkit-animation-duration: 5s;
  -webkit-animation-iteration-count: infinite;
  -webkit-animation-timing-function: linear;
  -moz-animation-name: rotation;
  -moz-animation-duration: 5s;
  -moz-animation-iteration-count: infinite;
  -moz-animation-timing-function: linear;
  -o-animation-name: rotation;
  -o-animation-duration: 5s;
  -o-animation-iteration-count: infinite;
  -o-animation-timing-function: linear;
  animation-name: rotation;
  animation-duration: 5s;
  animation-iteration-count: infinite;
  animation-timing-function: linear;
  @-webkit-keyframes rotation {
      0% {-webkit-transform:rotate(0deg);
          -moz-transform:rotate(0deg);
          -o-transform:rotate(0deg);
          transform:rotate(0deg);}
      100% {-webkit-transform:rotate(360deg);
          -moz-transform:rotate(360deg);
          -o-transform:rotate(360deg);
          transform:rotate(360deg);}
  }
  @-moz-keyframes rotation {
      0% {-webkit-transform:rotate(0deg);
          -moz-transform:rotate(0deg);
          -o-transform:rotate(0deg);
          transform:rotate(0deg);}
      100% {-webkit-transform:rotate(360deg);
          -moz-transform:rotate(360deg);
          -o-transform:rotate(360deg);
          transform:rotate(360deg);}
  }
  @-o-keyframes rotation {
      0% {-webkit-transform:rotate(0deg);
          -moz-transform:rotate(0deg);
          -o-transform:rotate(0deg);
          transform:rotate(0deg);}
      100% {-webkit-transform:rotate(360deg);
          -moz-transform:rotate(360deg);
          -o-transform:rotate(360deg);
          transform:rotate(360deg);}
  }
  @keyframes rotation {
      0% {-webkit-transform:rotate(0deg);
          -moz-transform:rotate(0deg);
          -o-transform:rotate(0deg);
          transform:rotate(0deg);}
      100% {-webkit-transform:rotate(360deg);
          -moz-transform:rotate(720deg);
          -o-transform:rotate(360deg);
          transform:rotate(360deg);}
  }
}
canvas {
  z-index: 9;
  position: absolute;
}
#line{
  stroke-width:2px;
  stroke:rgb(0,0,0);
  z-index: -1;
}
#svg{
  z-index: -1;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
