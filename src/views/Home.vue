<template>
  <div class="home">
    <div class="site-header">
      <h1 class="title">recursive.link</h1>
    </div>
    <div class="site-header">
      <h2 class="sub-title">sierpinksi sieve</h2>
    </div>
    <div id="simulation">
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import * as d3 from "d3";

@Component()
export default class Home extends Vue {
  isMounted = false;

  generateSvg(): void {
    let svg = d3
      .select("#simulation")
      .append("svg")
      .attr("width", 800)
      .attr("height", 800);

    svg.append('rect')
      .attr('x', 0)
      .attr('y', 0)
      .attr('width', 400)
      .attr('height', 400)
      .attr('stroke', '#efefef')
      .attr('stroke-width', '2px')
      .attr('fill', '#fafafa');

    svg.append('circle')
      .attr('cx', 10)
      .attr('cy', 80)
      .attr('r', 8)
      .attr('fill', '#333');

    svg.append('circle')
      .attr('cx', 380)
      .attr('cy', 80)
      .attr('r', 8)
      .attr('fill', '#333');

    svg.append('circle')
      .attr('cx', 170)
      .attr('cy', 330)
      .attr('r', 8)
      .attr('fill', '#333');
  }

  dragstarted(d) {
    d3.select(this).raise().classed('active', true);
  }

  dragged(d) {
    d[0] = x.invert(d3.event.x);
    d[1] = y.invert(d3.event.y);
    d3.select(this)
      .attr('cx', x(d[0]))
      .attr('cy', y(d[1]))
    focus.select('path').attr('d', line);
  }

  dragended(d) {
    d3.select(this).classed('active', false);
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
.simulation {
  display: flex;
}
.title {
  font-size: 24px;
  font-weight: bold;
}
.sub-title {
  font-size: 18px;
  font-weight: bold;
  color: #1296ec
}

#logo {
  width: 80px;
}
</style>
