<template>
  <div class="app">
    <svg :style="rect">
      <g transform="translate(0, 0)">
        <Grid :grid="priceGrid">
          <MyPath :d="priceLine"></MyPath>
        </Grid>
      </g>
    </svg>
  </div>
</template>

<script>
import {scaleLinear, scaleBand} from 'd3-scale';
import {line} from 'd3-shape';
import maxBy from 'lodash/maxBy';
import MyPath from './components/MyPath';
import Grid from './components/Grid';
import generateData from './generateData';

const data = generateData();


export default {
  name: 'app',
  data: function() {
    return {
      chartData: [],
      rect: {
        width: 500,
        height: 500,
        left: 0,
        top: 0,
      },
      margin: {
        left: 56,
        right: 56,
        top: 10,
        bottom: 30,
      },
      chartWidth: 400,
      priceHeight: 400,
    }
  },
  computed: {
    yDomain: function () {
      return [0, maxBy(this.chartData, (o) => {return o.close}).close];
    },
    xData: function() {
      return this.chartData.map(d => d.date);
    },
    yData: function() {
      return this.chartData.map(d => d.close);
    },
    priceYG: function (){
      return scaleLinear()
        .domain(this.yDomain)
        .range([this.priceHeight, 0])
    },
    xG: function() {
      return scaleBand()
        .domain(this.xData)
        .range([0, this.chartWidth]).paddingInner(0.5)
    },
    priceLine: function() {
      const l = line()
        .x(d => this.xG(d.date) + this.xG.bandwidth()/2)
        .y(d => this.priceYG(d.close));
      return l(this.chartData);
    },
    priceGrid: function() {
      return {
        left: this.margin.left,
        top: this.margin.top,
        width: this.chartWidth,
        height: this.priceHeight
      }
    }
  },
  components: {
    MyPath,
    Grid
  },
  created: function() {
    this.chartData = data;
    console.log(this.yDomain);
  }
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
svg {
  position: relative;
  width: 100%;
  height: 100%;
}
</style>
