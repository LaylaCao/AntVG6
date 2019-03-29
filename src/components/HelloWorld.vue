<template>
  <div>
    <div @click="addData" style="cursor:pointer">添加节点</div>
    <div @click="editData" style="cursor:pointer">连接节点</div>
    <div id="mountNode" ref="mountNode"></div>
  </div>
</template>

<script>
import dagre from 'dagre';
import G6 from '@antv/g6';

export default {
  /* eslint-disable no-plusplus */
  /* eslint no-underscore-dangle: off */
  /* eslint-disable no-underscore-dangle */
  /* eslint no-console: "off" */
  /* eslint no-param-reassign: "error" */
  /* eslint-disable no-restricted-syntax */
  /* eslint-disable max-len */
  name: 'HelloWorld',
  data() {
    return {
      data: {
        nodes: [{
          id: '收集日志',
        }, {
          id: '入 es 集群',
        }, {
          id: '入 hdfs',
        }, {
          id: 'hive 计算',
        }, {
          id: 'report',
        }],
        edges: [{
          source: '收集日志',
          target: '入 es 集群',
        }, {
          source: '收集日志',
          target: '入 hdfs',
        }, {
          source: '入 hdfs',
          target: 'hive 计算',
        }, {
          source: '入 es 集群',
          target: 'hive 计算',
        }, {
          source: 'hive 计算',
          target: 'report',
        }],
      },
      graph: null,
      nodeIndex: 0,
    };
  },
  mounted() {
    this.renderFlow();
  },
  methods: {
    renderFlow() {
      const data = this.data;
      const g = new dagre.graphlib.Graph();
      g.setDefaultEdgeLabel(() => ({}));
      g.setGraph({
        rankdir: 'TB',
      });
      data.nodes.forEach((node) => {
        node.label = `${node.id}`;
        g.setNode(node.id, {
          width: 150,
          height: 50,
        });
      });
      data.edges.forEach((edge) => {
        g.setEdge(edge.source, edge.target);
      });
      dagre.layout(g);
      let coord = null;
      g.nodes().forEach((node, i) => {
        coord = g.node(node);
        data.nodes[i].x = coord.x;
        data.nodes[i].y = coord.y;
      });
      g.edges().forEach((edge, i) => {
        coord = g.edge(edge);
        data.edges[i].startPoint = coord.points[0];
        data.edges[i].endPoint = coord.points[coord.points.length - 1];
        data.edges[i].controlPoints = coord.points.slice(1, coord.points.length - 1);
      });
      G6.registerNode('operation', {
        drawShape: function drawShape(cfg, group) {
          const rect = group.addShape('rect', {
            attrs: {
              x: -75,
              y: -25,
              width: 150,
              height: 50,
              radius: 10,
              stroke: '#00C0A5',
              fill: '#92949F',
              fillOpacity: 0.45,
              lineWidth: 2,
            },
          });
          return rect;
        },
      }, 'single-shape');

      const graph = new G6.Graph({
        container: 'mountNode',
        width: window.innerWidth,
        height: window.innerHeight,
        pixelRatio: 2,
        defaultNode: {
          shape: 'operation',
          labelCfg: {
            style: {
              fill: '#666',
              fontSize: 14,
              fontWeight: 'bold',
            },
          },
        },
        defaultEdge: {
          shape: 'polyline',
        },
        edgeStyle: {
          default: {
            endArrow: true,
            lineWidth: 2,
            stroke: '#ccc',
          },
        },
      });
      graph.data(data);
      graph.render();
      graph.fitView();
      this.graph = graph;
    },
    addData() {
      const node = this.graph.getNodes()[0];
      const count = this.getNotConnection();
      console.log(count);
      const xIndex = node._cfg.bboxCache.maxX + node._cfg.bboxCache.width + node._cfg.bboxCache.height;
      const yIndex = (count * node._cfg.bboxCache.height) + (node._cfg.bboxCache.height / 2) + (count * 10);
      this.nodeIndex += 1;
      this.data.nodes.push({
        label: `节点${this.nodeIndex}`,
        id: `nodeIndex${this.nodeIndex}`,
        x: xIndex,
        y: yIndex });
      this.refresh();
    },
    editData() {
      this.data.edges.push({
        source: '收集日志',
        target: `nodeIndex${this.nodeIndex}`,
      });
      this.refreshConnect();
    },
    refresh() {
      this.graph.changeData(this.data);
      this.graph.refresh();
    },
    refreshConnect() {
      this.graph.destroy();
      this.renderFlow();
    },
    getNotConnection() {
      let count = 0;
      for (const node of this.data.nodes) {
        for (const edge of this.data.edges) {
          if (node.id !== edge.source && node.id !== edge.target) {
            count++;
            break;
          }
        }
      }
      return count - this.data.edges.length;
    },
  },
};
</script>
