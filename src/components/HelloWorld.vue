<template>
  <div id="mountNode"><h2>This is a home page</h2></div>
</template>

<script>
// import dagre from 'dagre';
import G6 from '@antv/g6';

export default {
  name: 'HelloWorld',
  data() {
    return {
      data: {
        nodes: [
          {id: '1', x: 50, y: 50, size: 20},
          {id: '2', x: 150, y: 50, size: 20},
          {id: '3', x: 50, y: 100, size: 20},
          {id: '4', x: 150, y: 100, size: 20},
          {id: '5', x: 50, y: 150, size: 20},
          {id: '6', x: 150, y: 150, size: 20},
          {id: '7', x: 50, y: 200, size: 20},
          {id: '8', x: 150, y: 200, size: 20},
        ],
        edges: [
          {source: '1', target: '2', shape: 'line'},
          {source: '3', target: '4', shape: 'polyline', controlPoints: [{x: 100, y: 70}]},
          {
            source: '5', 
            target: '6',
            shape: 'quadratic',
            color: '#722ed1',
            size: 2,
            style: {
              lineDash: [2, 2]
            },
            label: 'quadratic',
            labelCfg: {
              position: 'center', // 其实默认就是 center，这里写出来便于理解
              autoRotate: true,
              style: {
                lineWidth: 5,
                stroke: 'white' // 给文本添加白边和白色背景
              }
            }
          },
          {
            source: '7', 
            target: '8', 
            shape: 'cubic',
            label: 'quadratic',
            labelCfg: {
              autoRotate: true,
              refY: -10 // refY 默认是顺时针方向向下，所以需要设置负值
            }
          }
        ]
      }
    }
  },
  mounted() {
    const data = this.data
    const graph = new G6.Graph({
      container: 'mountNode',
      width: 500,
      height: 500
    });
    graph.data(data);
    graph.render();
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
