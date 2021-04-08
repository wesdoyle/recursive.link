<template>
  <div class="home">
    <div class="site-header">
      <h1 class="title">recursive.link</h1>
    </div>
    <div class="site-header">
      <h2 class="sub-title">sierpinksi sieve</h2>
    </div>
    <div id="work-area">
      <div id="simulation"></div>
      <div id="control">
        <div class="control-stats">
          <div>
            <span class="p1">Point 1: </span>
            <span v-if="point1Set">[{{ p1.x }}, {{ p1.y }}] - (1,2)</span>
            <span v-else>not set</span>
          </div>
          <div>
            <span class="p2">Point 2: </span>
            <span v-if="point2Set">[{{ p2.x }}, {{ p2.y }}] - (3,4)</span>
            <span v-else>not set</span>
          </div>
          <div>
            <span class="p3">Point 3: </span>
            <span v-if="point3Set">[{{ p3.x }}, {{ p3.y }}] - (5,6)</span>
            <span v-else>not set</span>
          </div>
          <div>
            <span class="dice">Seed Point: </span>
            <span v-if="seedPointSet">[{{ seed.x }}, {{ seed.y }}]</span>
            <span v-else>not set</span>
          </div>
          <div>
            <span class="last-point">Last Point: </span>
            <span v-if="seedPointSet">[{{ lastPointPlaced.x }}, {{ lastPointPlaced.y }}]</span>
            <span v-else>not set</span>
          </div>
          <div>
            <span class="dice">Dice Roll: {{ diceRoll }}</span>
          </div>
        </div>
        <div class="control-actions">
          <button :disabled="!allInitialPointsPlaced" @click="placePoint">
            Place Point
          </button>
          <button :disabled="!allInitialPointsPlaced" @click="automate">Automate</button>
          <button @click="reset">Reset</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import * as d3 from "d3";

@Component({ name: "Home" })
export default class Home extends Vue {
  isMounted = false;

  factor = 2;
  background = "#222";
  pointColor = "#e4c1f9";

  points = {
    p1: { x: 0, y: 0, color: "#a9def9" },
    p2: { x: 0, y: 0, color: "#d0f4de" },
    p3: { x: 0, y: 0, color: "#ff99c8" },
    seed: { x: 0, y: 0, color: this.pointColor },
  };

  point1Set = false;
  point2Set = false;
  point3Set = false;
  seedPointSet = false;

  lastPointPlaced = { x: 0, y: 0, color: this.pointColor };
  diceRoll: number = NaN;

  svg: any;

  // eslint-disable-next-line @typescript-eslint/explicit-module-boundary-types
  handlePointPlacement(svg: any, event: Event) {
    let mouse = d3.pointer(event, this.$refs.simulation);
    let pointPosition = [mouse[0], mouse[1]];
    let color = "";

    if (!this.point1Set) {
      this.points.p1.x = pointPosition[0];
      this.points.p1.y = pointPosition[1];
      color = this.points.p1.color;
      this.point1Set = true;
    } else if (!this.point2Set) {
      this.points.p2.x = pointPosition[0];
      this.points.p2.y = pointPosition[1];
      color = this.points.p2.color;
      this.point2Set = true;
    } else if (!this.point3Set) {
      this.points.p3.x = pointPosition[0];
      this.points.p3.y = pointPosition[1];
      color = this.points.p3.color;
      this.point3Set = true;
    } else if (!this.seedPointSet) {
      this.points.seed.x = pointPosition[0];
      this.points.seed.y = pointPosition[1];
      color = this.points.seed.color;
      this.seedPointSet = true;
      this.lastPointPlaced.x = pointPosition[0];
      this.lastPointPlaced.y = pointPosition[1];
    } else {
      return;
    }

    svg
      .append("circle")
      .attr("cx", mouse[0])
      .attr("cy", mouse[1])
      .attr("r", 3)
      .attr("class", "placed-point")
      .attr("fill", color);
  }

  get p1() {
    return this.points.p1;
  }
  get p2() {
    return this.points.p2;
  }
  get p3() {
    return this.points.p3;
  }
  get seed() {
    return this.points.seed;
  }
  get allInitialPointsPlaced() {
    return (
      this.point1Set && this.point2Set && this.point3Set && this.seedPointSet
    );
  }

  automate() {
    let i = 0;;
    while (i < 1000) {
      setTimeout(this.placePoint, 300);
      i++;
    }
  }

  placePoint() {
    let number = this.rollDice();
    let attractorTarget;
    if (number === 1 || number === 2) {
      attractorTarget = this.points.p1;
    }
    if (number === 3 || number === 4) {
      attractorTarget = this.points.p2;
    }
    if (number === 5 || number === 6) {
      attractorTarget = this.points.p3;
    }

    let nextVector = this.getNextVector(this.lastPointPlaced, attractorTarget);

    this.$refs.svg
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      .append("circle")
      .attr("cx", nextVector.x)
      .attr("cy", nextVector.y)
      .attr("r", 0.8)
      .attr("class", "placed-point")
      .attr("fill", this.lastPointPlaced.color);

    this.lastPointPlaced = nextVector;
  }

  getNextVector(origin: any, target: any) {
    let x = Math.abs((target.x + origin.x)) / this.factor;
    let y = Math.abs((target.y + origin.y)) / this.factor;
    return {x, y, color: this.pointColor };
  }

  rollDice() {
    let roll = Math.floor(Math.random() * 6) + 1;
    this.diceRoll = roll;
    return roll;
  }

  reset() {
    d3.select('#simulation').html("");
    this.generateSvg();
    this.points = {
      p1: { x: 0, y: 0, color: "#a9def9" },
      p2: { x: 0, y: 0, color: "#d0f4de" },
      p3: { x: 0, y: 0, color: "#ff99c8" },
      seed: { x: 0, y: 0, color: this.pointColor },
    };
    this.point1Set = false;
    this.point2Set = false;
    this.point3Set = false;
    this.seedPointSet = false;
    this.lastPointPlaced = { x: 0, y: 0, color: this.pointColor };
  }

  generateSvg(): void {
    let svg = d3
      .select("#simulation")
      .append("svg")
      .attr("ref", "simulation")
      .attr("id", "work-area")
      .attr("width", 420)
      .attr("height", 420);

    svg
      .append("rect")
      .attr("x", 0)
      .attr("y", 0)
      .attr("id", "sim")
      .attr("width", 420)
      .attr("height", 420)
      .attr("fill", this.background)
      .attr("stroke-alignment", "outer");

    svg.on("click", (event) => this.handlePointPlacement(svg, event));

    // eslint-disable-next-line @typescript-eslint/ban-ts-comment
    // @ts-ignore
    this.$refs.svg = svg;
    this.svg = svg;
  }

  mounted(): void {
    this.generateSvg();
  }
}
</script>

<style lang="scss">
.site-header {
  display: flex;
  align-items: center;
}

#work-area {
  display: flex;
  border: 1px dashed #ddd;
  #simulation {
  }
  #control {
    width: 420px;
  }
}

.title {
  font-size: 24px;
  font-weight: bold;
}

.sub-title {
  font-size: 18px;
  font-weight: bold;
  color: #1296ec;
}

#logo {
  width: 80px;
}

#control {
  padding: 1rem;

  .control-stats {
    margin-bottom: 1rem;
    font-size: 1.2rem;
  }
}

.p1 {
  color: #a9def9;
  font-weight: bold;
}
.p2 {
  color: #d0f4de;
  font-weight: bold;
}
.p3 {
  color: #ff99c8;
  font-weight: bold;
}
.seed {
  color: #e4c1f9;
  font-weight: bold;
}

.dice {
  color: #333;
  font-weight: bold;
}

.last-point {
  color: #333;
  font-weight: bold;
}
</style>
