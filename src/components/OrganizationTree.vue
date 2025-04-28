<template>
  <div class="org-tree-container">
    <div id="container" ref="container" class="graph-container"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import G6 from '@antv/g6'

const container = ref(null)
let graph = null

// 模拟数据（可替换为真实数据）
const data = {
  id: 'root',
  label: '总裁',
  children: [
    {
      id: 'sub1',
      label: '副总裁',
      children: [
        {
          id: 'sub1-1',
          label: '人力资源总监',
          children: [
            { id: 'sub1-1-1', label: '招聘经理' },
            { id: 'sub1-1-2', label: '培训经理' },
            { id: 'sub1-1-3', label: '薪酬福利经理' },
            { id: 'sub1-1-4', label: '员工关系经理' }
          ]
        },
        {
          id: 'sub1-2',
          label: '财务总监',
          children: [
            { id: 'sub1-2-1', label: '会计主管' },
            { id: 'sub1-2-2', label: '财务分析师' },
            { id: 'sub1-2-3', label: '税务专员' },
            { id: 'sub1-2-4', label: '财务规划经理' }
          ]
        },
        {
          id: 'sub1-3',
          label: '行政总监',
          children: [
            { id: 'sub1-3-1', label: '办公室主管' },
            { id: 'sub1-3-2', label: '后勤管理员' }
          ]
        }
      ]
    },
    {
      id: 'sub2',
      label: '技术副总裁',
      children: [
        {
          id: 'sub2-1',
          label: '产品总监',
          children: [
            { id: 'sub2-1-1', label: '产品经理' },
            { id: 'sub2-1-2', label: '产品设计师' },
            { id: 'sub2-1-3', label: '用户体验专家' },
            { id: 'sub2-1-4', label: '产品运营专员' }
          ]
        },
        {
          id: 'sub2-2',
          label: '技术总监',
          children: [
            { id: 'sub2-2-1', label: '前端开发主管' },
            { id: 'sub2-2-2', label: '后端开发主管' },
            { id: 'sub2-2-3', label: '测试主管' },
            { id: 'sub2-2-4', label: '运维主管' },
            { id: 'sub2-2-5', label: '算法工程师' }
          ]
        },
        {
          id: 'sub2-3',
          label: '研发总监',
          children: [
            { id: 'sub2-3-1', label: '架构师' },
            { id: 'sub2-3-2', label: '安全专家' }
          ]
        }
      ]
    },
    {
      id: 'sub3',
      label: '营销副总裁',
      children: [
        { 
          id: 'sub3-1', 
          label: '市场部总监',
          children: [
            { id: 'sub3-1-1', label: '品牌经理' },
            { id: 'sub3-1-2', label: '市场策划专员' }
          ]
        },
        { 
          id: 'sub3-2', 
          label: '销售总监',
          children: [
            { id: 'sub3-2-1', label: '区域销售主管' },
            { id: 'sub3-2-2', label: '渠道经理' }
          ]
        },
        {
          id: 'sub3-3',
          label: '客户服务总监',
          children: [
            { id: 'sub3-3-1', label: '客户关系经理' },
            { id: 'sub3-3-2', label: '售后服务主管' }
          ]
        }
      ]
    }
  ]
}

onMounted(() => {
  initGraph()
})

onUnmounted(() => {
  if (graph) {
    graph.destroy()
  }
})

// 初始化图表
function initGraph() {
  if (!container.value) return
  
  // 自定义节点配置
  G6.registerNode(
    'org-node',
    {
      draw: (cfg, group) => {
        const width = 120
        const height = 40
        const offsetX = -width / 2
        const offsetY = -height / 2
        const radius = 4
        
        // 主体容器
        const container = group.addShape('rect', {
          attrs: {
            x: offsetX,
            y: offsetY,
            width,
            height,
            radius,
            fill: '#C6E5FF',
            stroke: '#5B8FF9',
            lineWidth: 1
          },
          name: 'node-container'
        })
        
        // 文本标签
        group.addShape('text', {
          attrs: {
            text: cfg.label,
            x: 0,
            y: 0,
            fontSize: 14,
            textAlign: 'center',
            textBaseline: 'middle',
            fill: '#333'
          },
          name: 'node-label'
        })
        
        return container
      },
      // 自定义锚点位置，设置一个居中的锚点，统一所有连线的起始点和终点
      getAnchorPoints() {
        return [
          [0.5, 0], // 上中
          [0.5, 1]  // 下中
        ]
      }
    },
    'single-node'
  )
  
  // 自定义连接线，让所有线都从父节点底部中心引出
  G6.registerEdge('org-edge', {
    draw(cfg, group) {
      const startPoint = cfg.startPoint;
      const endPoint = cfg.endPoint;
      
      const controlPoints = [];
      controlPoints.push({ x: startPoint.x, y: startPoint.y + 10 });
      controlPoints.push({ x: endPoint.x, y: endPoint.y - 10 });
      
      const path = [
        ['M', startPoint.x, startPoint.y],
        ['L', startPoint.x, (startPoint.y + endPoint.y) / 2],
        ['L', endPoint.x, (startPoint.y + endPoint.y) / 2],
        ['L', endPoint.x, endPoint.y]
      ];
      
      const shape = group.addShape('path', {
        attrs: {
          path,
          stroke: '#A3B1BF',
          lineWidth: 1,
          endArrow: false
        },
        name: 'edge-shape'
      });
      
      return shape;
    }
  });
  
  // 实例化图表
  graph = new G6.TreeGraph({
    container: container.value,
    width: container.value.scrollWidth || 500,
    height: container.value.scrollHeight || 500,
    modes: {
      default: ['drag-canvas', 'zoom-canvas']
    },
    layout: {
      type: 'compactBox',
      direction: 'TB', // 从上到下布局
      getId: function getId(d) {
        return d.id
      },
      getHeight: () => {
        return 40
      },
      getWidth: () => {
        return 120
      },
      getVGap: () => {
        return 30
      },
      getHGap: () => {
        return 50
      }
    },
    defaultNode: {
      type: 'org-node'
    },
    defaultEdge: {
      type: 'org-edge',
      style: {
        stroke: '#A3B1BF',
        lineWidth: 1
      }
    },
    fitView: true,
    fitViewPadding: 30
  })
  
  // 监听窗口变化，调整图表大小
  const handleResize = () => {
    if (!graph || graph.get('destroyed')) return
    if (!container.value) return
    graph.changeSize(container.value.scrollWidth, container.value.scrollHeight)
  }
  
  window.addEventListener('resize', handleResize)
  
  // 数据加载与渲染
  graph.data(data)
  graph.render()
  graph.fitView()
}
</script>

<style scoped>
.org-tree-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  overflow: hidden;
}

.graph-container {
  flex: 1;
  overflow: hidden;
  background: #fff;
}
</style> 