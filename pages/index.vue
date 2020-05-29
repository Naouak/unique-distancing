<template>
  <div class="container">
    <div>
      <h1>Unique Distancing</h1>

      <div>
        Grid Size : <input type="number" v-model.number="size" step="1" min="3" />
      </div>

      <table style="border: solid black 1px;">
        <tr v-for="i in size">
          <td v-for="j in size" :class="{'selected': selected[i+'-'+j],'impossible': impossible[i+'-'+j]}"
              style="border: solid black 1px; width: 100px; height: 100px;" @click="select(i,j)">
            <span v-if="selected[i+'-'+j]">SELECTED</span>
            <span v-else-if="impossible[i+'-'+j]">
              IMPOSSIBLE
            </span>
          </td>
        </tr>
      </table>

      <button @click="selected = {}">CLEAR SELECTION</button>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      size: 6,
      selected: {},
    }
  },
  computed: {
    distances() {
      let distances = [];
      let points = Object.keys(this.selected).filter(k => this.selected[k]).map(a => a.split('-').map(n => parseInt(n)));

      for (let i = 0; i < points.length - 1; i++) {
        for (let j = i + 1; j < points.length; j++) {
          let [x1, y1] = points[i];
          let [x2, y2] = points[j];

          distances.push(
            {
              a: points[i].join('-'),
              b: points[j].join('-'),
              d: Math.sqrt(Math.pow(x1 - x2, 2) + Math.pow(y1 - y2, 2)),
            }
          );
        }
      }
      return distances;
    },
    impossible() {
      let points = Object.keys(this.selected).filter(k => this.selected[k]).map(a => a.split('-').map(n => parseInt(n)));
      let vectors = [];

      let impossible = {};

      for (let i = 0; i < points.length - 1; i++) {
        for (let j = i + 1; j < points.length; j++) {
          vectors.push([
            points[i][0] - points[j][0],
            points[i][1] - points[j][1]
          ], [
            -1 * (points[i][0] - points[j][0]),
            points[i][1] - points[j][1]
          ], [
            -1 * (points[i][0] - points[j][0]),
            -1 * (points[i][1] - points[j][1])
          ], [
            (points[i][0] - points[j][0]),
            -1 * (points[i][1] - points[j][1])
          ], [
            points[i][1] - points[j][1],
            points[i][0] - points[j][0]
          ], [
            -1 * (points[i][1] - points[j][1]),
            points[i][0] - points[j][0]
          ], [
            -1 * (points[i][1] - points[j][1]),
            -1 * (points[i][0] - points[j][0])
          ], [
            (points[i][1] - points[j][1]),
            -1 * (points[i][0] - points[j][0])
          ]);
        }
      }

      for (let i = 0; i < points.length - 1; i++) {
        for (let j = i + 1; j < points.length; j++) {
          let v = [points[i][0] - points[j][0], points[i][1] - points[j][1]];

          if (v[0] === 0 && v[1] % 2 === 0) {
            for (let a = 1; a <= this.size; a++) {
              console.log([points[i][0] + v[1] / 2, a].join('-'));
              impossible[
                [a, points[i][1] - v[1] / 2].join('-')
                ] = true;
            }
          }

          if (v[1] === 0 && v[0] % 2 === 0) {
            for (let a = 1; a <= this.size; a++) {
              impossible[
                [points[i][0] - v[0] / 2, a].join('-')
                ] = true;
            }
          }

          if (Math.abs(v[0]) === Math.abs(v[1])) {
            let stop = false;

            let ydiff = v[1] > 0 ? -1 : 1;
            let xdiff = v[0] > 0 ? -1 : 1;
            console.log(v);
            for (let x = v[0], y = 0; !stop; x += xdiff, y += ydiff) {
              if (x == 0) {
                stop = true;
              }
              console.log([x, y], [points[i][0] - x, points[i][1] + y]);
              impossible[[points[i][0] - x, points[i][1] + y].join('-')] = true;
            }
          }
        }
      }


      for (let [x, y] of points) {
        for (let [x2, y2] of vectors) {
          let [xf, yf] = [x + x2, y + y2];
          if (1 <= xf && xf <= this.size && 1 <= yf && yf <= this.size) {
            impossible[xf + '-' + yf] = true;
          }
        }
      }


      return impossible;
    },
  },
  watch: {
    size(){
      this.selected = {};
    },
  },
  methods: {
    select(i, j) {
      this.$set(this.selected, i + "-" + j, !this.selected[i + "-" + j]);
    }
    ,
  }
  ,
}
</script>

<style>
  .container {
    margin: 0 auto;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
  }

  .title {
    font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    display: block;
    font-weight: 300;
    font-size: 100px;
    color: #35495e;
    letter-spacing: 1px;
  }

  .subtitle {
    font-weight: 300;
    font-size: 42px;
    color: #526488;
    word-spacing: 5px;
    padding-bottom: 15px;
  }

  .links {
    padding-top: 15px;
  }

  .impossible {
    background: black;
    color: white;
  }

  .selected {
    background: red;
    color: white;
  }

</style>
