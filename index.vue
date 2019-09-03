<template>
  <div :style="{ backgroundColor: colorRGB }">
    <h3>{{ title }}</h3>

    <v-chartist :data="chartData"
                :options="chartOptions"
                type="Pie"></v-chartist>

    <h4>{{ currentDisplay }} / {{ totalDisplay }}</h4>

  </div>
</template>

<script>
import vChartist from 'v-chartist'
import 'chartist/dist/chartist.min.css'

const defaultChartOptions = {
  generic: {
    fullWidth: true,
    width: '100%',
    height: '100%'
  }
};

export default {

  components: {
    'v-chartist': vChartist
  },

  // Props
  props: {
    title: {
      type: String,
      required: true
    },

    data: {
      type: Array,
      required: true
    },

    params: {
      default: null
    }
  },

  // Computed
  computed: {

    lastState () {
      return this.data.length ? this.data[this.data.length - 1].state : null;
    },


    current () {
      return this.lastState ? this.lastState[this.params.current] : null;
    },

    _current () {
      return this.current === null ? 0 : this.current;
    },

    currentDisplay () {
      return this.current === null ? '?' : this.current;
    },


    total () {
      return this.lastState ? this.lastState[this.params.total] : null;
    },

    _total () {
      return this.total === null || this.total === 0 ? 100 : this.total;
    },

    totalDisplay () {
      return this.total === null ? '?' : this.total;
    },


    percent () {
      return 100 * this._current / this._total;
    },
    percent2 () {
      return Math.pow(this.percent, 2);
    },

    rest () {
      return 100 - this.percent;
    },


    chartData () {
      return {
        series: [ this.percent, this.rest ]
      };
    },


    colorHSL () {
      let h = 37 * this.percent2 / 5000 +
              31 * this.percent / 100 +
              0;
      let s = -77 * this.percent2 / 5000 +
              103 * this.percent / 100 +
              179;
      let l = 6 * this.percent2 / 5000 +
              -48 * this.percent / 100 +
              154;

      // Hue is a degree on the color wheel from 0 to 360. 0 is red, 120 is green, 240 is blue.
      let H = 360 * h / 255;

      // Saturation is a percentage value; 0% means a shade of gray and 100% is the full color.
      let S = 100 * s / 255;

      // Lightness is also a percentage; 0% is black, 100% is white.
      let L = 100 * l / 255;

      return `hsl(${H}, ${S}%, ${L}%)`;
    },

    colorRGB () {
      let r = -136 * this.percent2 / 5000 +
              76 * this.percent / 100 +
              255;
      let g = -113 * this.percent2 / 5000 +
              319 * this.percent / 100 +
              85;
      let b = 117 * this.percent2 / 5000 +
              -203 * this.percent / 100 +
              84;

      return `rgb(${r}, ${g}, ${b})`;
    }

  },

  // Data
  data () {
    return {
      chartOptions: {
        startAngle: 270,
        total: 200,
        showLabel: false,
        labelOffset: 0,
        chartPadding: 0,
        donut: true,
        donutWidth: '75%'
      }
    };
  }

}
</script>

<style scoped>
* {
  color: white;
}

.v-chartist-container {
  width: 100%;
  height: 80%;
}

.v-chartist-container >>> .ct-series.ct-series-a path {
  stroke: white;
}

.v-chartist-container >>> .ct-series.ct-series-b path {
  stroke: rgba(255, 255, 255, 0.5);
}

h4 {
  margin-top: -5rem;
  font-size: 2.5em;
  line-height: 1em;
  padding: 0;
}
</style>
