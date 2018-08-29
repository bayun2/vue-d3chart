<template>
  <div class="app">
    <canvas width="960" height="500" ref="canvas" @mousewheel.stop.prevent="handlWheel"></canvas>
  </div>
</template>

<script>
import {scaleLinear, scaleBand} from 'd3-scale';
import {line} from 'd3-shape';
import maxBy from 'lodash/maxBy';
import MyPath from './components/MyPath';
import Grid from './components/Grid';
import generateData2 from './generateData2';

const data = generateData2();


export default {
  name: 'app',
  data: function() {
    return {
      chartData: [],
      margin: {
        left: 40,
        right: 20,
        top: 20,
        bottom: 30,
      },
      scaleX: 1,
      defaultUnitWidth: 50,
      translateX: 0,
    }
  },
  computed: {
    canvas: function() {
      return this.$refs.canvas;
    },
    context: function() {
      return this.canvas.getContext('2d');
    },
    width: function() {
      return this.canvas.getBoundingClientRect().width - this.margin.left - this.margin.right;
    },
    unitWidth: function() {
      return this.scaleX * this.defaultUnitWidth;
    },
    contentWidth: function() {
      return this.chartData.length * this.unitWidth;
    },
    height: function() {
      return this.canvas.getBoundingClientRect().height - this.margin.top - this.margin.bottom;
    },
    yDomain: function () {
      return [0, maxBy(this.chartData, (o) => {return o.frequency}).frequency];
    },
    xData: function() {
      return this.chartData.map(d => d.letter);
    },
    yData: function() {
      return this.chartData.map(d => d.frequency);
    },
    yG: function (){
      return scaleLinear()
        .domain(this.yDomain)
        .range([this.height, 0])
    },
    xG: function() {
      return scaleBand()
        .domain(this.xData)
        .range([0, this.contentWidth]).paddingInner(0.1)
    },
    isAtRight: function() {
      if (this.contentWidth === 0) return true;
      return this.width + this.translateX === this.contentWidth;
    }
  },
  methods: {
    handlWheel: function(e) {
      const target = e.target
      if (Math.abs(e.deltaX) >= Math.abs(e.deltaY)) {
        console.log('translatex', this.translateX)
        console.log('deltax', e.deltaX)
        let nextTranslate = this.translateX - e.deltaX;
        if (nextTranslate > 0) {
          nextTranslate = 0;
        } else if (Math.abs(nextTranslate) > this.contentWidth - this.width) {
          nextTranslate = -Math.abs(this.contentWidth - this.width);
        }
        this.translateX = nextTranslate;
      }
    },
    moveToRight: function() {
      let translateX = 0
      if (this.width < this.contentWidth) {
        translateX = this.width - this.contentWidth;
      }
      this.translateX = translateX;
    },
    drawChart: function() {
      this.context.save();
      this.context.beginPath();
      this.context.strokeStyle = "transparent";
      this.context.rect(0.5, 0.5, this.width , this.height + 6 + 12);
      this.context.stroke();
      this.context.clip();
      this.context.translate(this.translateX, 0);

      this.context.beginPath();
      this.xG.domain().forEach(d => {
        this.context.moveTo(this.xG(d) + this.xG.bandwidth() / 2, this.height);
        this.context.lineTo(this.xG(d) + this.xG.bandwidth() / 2, this.height + 6);
      })
      this.context.strokeStyle = "black";
      this.context.stroke();

      this.context.textAlign = "center";
      this.context.textBaseline = "top";
      this.xG.domain().forEach(d => {
        this.context.fillText(d, this.xG(d) + this.xG.bandwidth() / 2, this.height + 6);
      })

      this.context.fillStyle = "steelblue";
      this.chartData.forEach(d => {
        this.context.fillRect(this.xG(d.letter), this.yG(d.frequency), this.xG.bandwidth(), this.height-this.yG(d.frequency))
      })
      this.context.restore();
    }
  },
  watch: {
    translateX: function() {
      this.context.clearRect(0+0.5, 0, this.width + 3 , this.height + 6 + 12);
      this.drawChart();
    }
  },
  created: function() {
    this.chartData = data;
    console.log(this.yDomain);
  },
  mounted: function() {
    this.moveToRight();
    this.context.translate(this.margin.left, this.margin.top);
    const yTickCount = 10,
          yTicks = this.yG.ticks(yTickCount),
          yTickFormat = this.yG.tickFormat(yTickCount, '%');

    this.context.beginPath();
    yTicks.forEach(d => {
      this.context.moveTo(0, this.yG(d) + 0.5);
      this.context.lineTo(-6, this.yG(d) + 0.5);
    })
    this.context.strokeStyle = "black";
    this.context.stroke();
    this.context.textAlign = "right";
    this.context.textBaseline = "middle";
    yTicks.forEach(d => {
      this.context.fillText(yTickFormat(d), -9, this.yG(d));
    })

    this.context.beginPath();
    this.context.moveTo(0.5, 0 + 0.5);
    this.context.lineTo(0.5, this.height+0.5);
    this.context.strokeStyle = "black";
    this.context.stroke();

    this.drawChart();
  },
}
</script>

<style>
.app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  border: 1px solid #318BEB;
}
canvas {
  border: 1px solid red;
}
</style>
