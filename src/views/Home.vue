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
            <span v-if="point1Set"
              >[{{ p1.x }}, {{ p1.y }}] - (1,2)</span
            >
            <span v-else>not set</span>
          </div>
          <div>
            <span class="p2">Point 2: </span>
            <span v-if="point2Set"
              >[{{ p2.x }}, {{ p2.y }}] - (3,4)</span
            >
            <span v-else>not set</span>
          </div>
          <div>
            <span class="p3">Point 3: </span>
            <span v-if="point3Set"
              >[{{ p3.x }}, {{ p3.y }}] - (5,6)</span
            >
            <span v-else>not set</span>
          </div>
        </div>
        <div class="control-actions">
          <button :disabled="!allInitialPointsPlaced">
            Place Point
          </button>
          <button :disabled="!allInitialPointsPlaced">
            Automate
          </button>
          <button @click="reset">
            Reset
          </button>
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

  background = "#222";

  points = {
    p1: {x: 0, y: 0, color: "#a9def9"},
    p2: {x: 0, y: 0, color: "#d0f4de"},
    p3: {x: 0, y: 0, color: "#ff99c8"},
  };

  point1Set = false;
  point2Set = false;
  point3Set = false;

  handlePointPlacement(svg, event: Event) {
    let mouse = d3.pointer(event, this.$refs.simulation);

    console.log(mouse);

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
    } else {
      return;
    }

    console.log(this.points);

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

  get allInitialPointsPlaced() {
    return this.point1Set && this.point2Set && this.point3Set;
  }

  reset() {
    console.log("TODO")
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
</style>
