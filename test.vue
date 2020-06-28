<template>
  <div class="test">
    <div class="left">
      <ul>
        <li>laskjdfl;</li>
        <li>lasdkjfl;skjdf路上看到就烦；老师的</li>
      </ul>
    </div>

    <div class="wrap" @mousemove="handleMousemove" @mouseup="handleSvgMouseup">
      <svg width="1" height="1" style="overflow:visible;vertical-align:top;border:1px solid red;">
        <defs>
          <marker
            id="mark-arrow"
            viewBox="0 0 14 14"
            refX="8"
            refY="6"
            markerWidth="12"
            markerHeight="12"
            orient="auto"
          >
            <path d="M2,2 L10,6 L2,10 L6,6 L2,2" />
          </marker>
        </defs>
        <polyline :points="mouseEdge" stroke="red" stroke-width="5" marker-end="url(#mark-arrow)" />
        <g>
          <path
            v-for="edge in edges"
            :key="edge.id"
            :class="['link', {selected: edge.selected}]"
            :d="getEdgeData(edge)"
            marker-end="url(#arrow)"
          />
        </g>
      </svg>
      <div class="inputNode node" @mousedown="handleNodeMousedown" @mouseup="handleMouseup">
        <p class="herader">填写节点</p>
        <p class="herader">参数校验</p>
      </div>
      <div class="robotNode node" @mouseup="handleMouseup">
        <p class="herader">填写节点</p>
        <p class="herader">参数校验</p>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      isLinking: false,
      mouseEdge: "",
      mouseNode: {},
      edges: []
    };
  },
  methods: {
    handleNodeMousedown(e) {
      this.isLinking = true;
      console.log("handleNodeMousedown", e);
      const posi = this.getNodePosition(e.target);
      this.mouseNode = {
        x: posi.x - 200 + posi.width / 2,
        y: posi.y + posi.height / 2,
        width: e.target.offsetWidth,
        height: e.target.offsetHeight
      };
    },
    handleMousemove(e) {
      if (this.isLinking) {
        this.mouseEdge = `${this.mouseNode.x},${this.mouseNode.y} ${e.clientX -
          200},${e.clientY}`;
        console.log(this.mouseEdge);
      }
    },
    getEdgeData(edge) {
      const source = edge.source;
      const target = edge.target;
      if (target.x > source.x) {
        return `M${source.x},${source.y}L${target.x},${target.y}`;
      }
      return `M${source.x},${source.y}L${target.x},${target.y}`;
    },
    handleSvgMouseup(e) {
      this.isLinking = false;
    },
    handleMouseup(e) {
      this.edges.push({
        source: { ...this.mouseNode },
        target: this.getNodePosition(e.target)
      });
      console.log("this.edges", this.edges);
      this.mouseNode = {};
    },
    getNodePosition(node) {
      const posi = {
        x: node.offsetLeft,
        y: node.offsetTop,
        width: node.offsetWidth,
        height: node.offsetHeight
      };
      let parentNode = node.parentNode;
      while (parentNode) {
        posi.x += parentNode.offsetLeft || 0;
        posi.y += parentNode.offsetTop || 0;
        parentNode = parentNode.parentNode;
      }
      return posi;
    }
  }
};
</script>
<style>
.test {
  display: flex;
}
.left {
  width: 200px;
}
.wrap {
  flex: 1;
  position: relative;
  border: 1px solid gray;
  height: 500px;
}
.node {
  position: absolute;
  width: 200px;
  height: 100px;
  background: grey;
}
.inputNode {
  left: 10px;
}
.robotNode {
  left: 100px;
  top: 200px;
}
</style>