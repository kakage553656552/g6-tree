<template>
  <div class="org-tree-container">
    <div id="container" ref="container" class="graph-container"></div>
  </div>
</template>

<script>
import G6 from '@antv/g6'

export default {
  name: 'OrganizationTree',
  data() {
    return {
      graph: null,
      // 模拟数据（可替换为真实数据）
      data: {
        id: 'root',
        label: '总裁',
        collapsed: false,
        children: [
          {
            id: 'sub1',
            label: '技术副总裁',
            collapsed: false,
            children: [
              {
                id: 'sub1-1',
                label: '研发总监',
                collapsed: false,
                children: [
                  { id: 'sub1-1-1', label: '前端开发经理' },
                  { id: 'sub1-1-2', label: '后端开发经理' },
                  { id: 'sub1-1-3', label: '测试经理' }
                ]
              },
              {
                id: 'sub1-2',
                label: '产品总监',
                children: [
                  { id: 'sub1-2-1', label: '产品经理' },
                  { id: 'sub1-2-2', label: '设计师' }
                ]
              }
            ]
          },
          {
            id: 'sub2',
            label: '运营副总裁',
            children: [
              {
                id: 'sub2-1',
                label: '市场总监',
                children: [
                  { id: 'sub2-1-1', label: '市场主管' },
                  { id: 'sub2-1-2', label: '品牌主管' }
                ]
              },
              {
                id: 'sub2-2',
                label: '销售总监',
                children: [
                  { id: 'sub2-2-1', label: '销售经理' },
                  { id: 'sub2-2-2', label: '客户经理' }
                ]
              }
            ]
          },
          {
            id: 'sub3',
            label: '人力行政副总裁',
            children: [
              { 
                id: 'sub3-1', 
                label: '人力资源总监',
                children: [
                  { id: 'sub3-1-1', label: '招聘主管' }
                ]
              },
              { 
                id: 'sub3-2', 
                label: '行政总监',
                children: [
                  { id: 'sub3-2-1', label: '行政主管' }
                ]
              }
            ]
          }
        ]
      }
    }
  },
  mounted() {
    this.initGraph()
  },
  beforeDestroy() {
    if (this.graph) {
      this.graph.destroy()
    }
  },
  methods: {
    // 初始化图表
    initGraph() {
      if (!this.$refs.container) return
      
      // 自定义节点配置
      G6.registerNode(
        'org-node',
        {
          draw: (cfg, group) => {
            const width = 120
            const height = 40
            const offsetX = -width / 2
            const offsetY = -height / 2
            const radius = 6
            
            // 主体容器
            const container = group.addShape('rect', {
              attrs: {
                x: offsetX,
                y: offsetY,
                width,
                height,
                radius,
                fill: '#FFF0F7',
                stroke: 'deeppink',
                lineWidth: 1,
                shadowColor: 'rgba(0,0,0,0.1)',
                shadowBlur: 5,
                shadowOffsetX: 0,
                shadowOffsetY: 2
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
                fontWeight: 500,
                textAlign: 'center',
                textBaseline: 'middle',
                fill: '#333'
              },
              name: 'node-label'
            })
            
            // 只有有子节点的节点才添加展开/折叠按钮
            if (cfg.children && cfg.children.length) {
              // 添加展开/折叠按钮
              const buttonSize = 16
              group.addShape('circle', {
                attrs: {
                  x: offsetX + width - buttonSize/2 - 5,
                  y: offsetY + buttonSize/2 + 5,
                  r: buttonSize/2,
                  fill: '#fff',
                  stroke: 'deeppink',
                  lineWidth: 1.5,
                  cursor: 'pointer',
                  shadowColor: 'rgba(0,0,0,0.1)',
                  shadowBlur: 3,
                  shadowOffsetX: 0,
                  shadowOffsetY: 1
                },
                name: 'collapse-button-bg'
              })
              
              // 按钮内的加号或减号使用更美观的符号
              const collapsed = cfg.collapsed
              
              if (collapsed) {
                // 加号图标（展开）
                group.addShape('path', {
                  attrs: {
                    path: [
                      ['M', offsetX + width - buttonSize/2 - 5 - 4, offsetY + buttonSize/2 + 5],
                      ['L', offsetX + width - buttonSize/2 - 5 + 4, offsetY + buttonSize/2 + 5],
                      ['M', offsetX + width - buttonSize/2 - 5, offsetY + buttonSize/2 + 5 - 4],
                      ['L', offsetX + width - buttonSize/2 - 5, offsetY + buttonSize/2 + 5 + 4],
                    ],
                    stroke: 'deeppink',
                    lineWidth: 1.5,
                    lineCap: 'round',
                    cursor: 'pointer'
                  },
                  name: 'collapse-button-text'
                });
              } else {
                // 减号图标（收起）
                group.addShape('path', {
                  attrs: {
                    path: [
                      ['M', offsetX + width - buttonSize/2 - 5 - 4, offsetY + buttonSize/2 + 5],
                      ['L', offsetX + width - buttonSize/2 - 5 + 4, offsetY + buttonSize/2 + 5],
                    ],
                    stroke: 'deeppink',
                    lineWidth: 1.5,
                    lineCap: 'round',
                    cursor: 'pointer'
                  },
                  name: 'collapse-button-text'
                });
              }
            }
            
            return container
          },
          // 自定义锚点位置，设置一个居中的锚点，统一所有连线的起始点和终点
          getAnchorPoints() {
            return [
              [0.5, 0], // 上中
              [0.5, 1]  // 下中
            ]
          },
          // 添加交互状态样式
          setState(name, value, item) {
            const group = item.getContainer()
            if (name === 'hover') {
              const container = group.find(element => element.get('name') === 'node-container')
              container.attr('fill', value ? '#FFCFE3' : '#FFF0F7')
              container.attr('shadowBlur', value ? 10 : 5)
            }
          }
        },
        'single-node'
      )
      
      // 自定义连接线，让所有线都从父节点底部中心引出
      G6.registerEdge(
        'org-edge',
        {
          draw(cfg, group) {
            const startPoint = cfg.startPoint
            const endPoint = cfg.endPoint
            
            // 绘制连接线路径
            const path = [
              ['M', startPoint.x, startPoint.y], // 起点
              ['L', startPoint.x, startPoint.y + 10], // 先垂直向下一小段
              ['L', startPoint.x, (startPoint.y + endPoint.y) / 2], // 继续向下到中点
              ['L', endPoint.x, (startPoint.y + endPoint.y) / 2], // 水平
              ['L', endPoint.x, endPoint.y - 10], // 向下到终点前
              ['L', endPoint.x, endPoint.y] // 到终点
            ]
            
            const shape = group.addShape('path', {
              attrs: {
                path,
                stroke: 'deeppink',
                lineWidth: 1.5,
                endArrow: {
                  path: 'M 0,0 L 6,3 L 6,-3 Z',
                  fill: 'deeppink'
                },
                lineDash: [0],
                opacity: 0.8
              },
              name: 'path-shape'
            })
            
            return shape
          }
        }
      )
      
      // 图表配置
      const width = this.$refs.container.scrollWidth || 800
      const height = this.$refs.container.scrollHeight || 600
      
      // 实例化图表
      this.graph = new G6.TreeGraph({
        container: this.$refs.container,
        width,
        height,
        modes: {
          default: [
            {
              type: 'collapse-expand',
              onChange: (item, collapsed) => {
                const model = item.getModel()
                model.collapsed = collapsed
                return true
              }
            },
            'drag-canvas',
            'zoom-canvas'
          ]
        },
        defaultNode: {
          type: 'org-node'
        },
        defaultEdge: {
          type: 'org-edge'
        },
        layout: {
          type: 'compactBox',
          direction: 'TB', // 从上到下排布
          getId: function getId(d) {
            return d.id
          },
          getHeight: function getHeight() {
            return 60
          },
          getWidth: function getWidth() {
            return 150
          },
          getVGap: function getVGap() {
            return 30
          },
          getHGap: function getHGap() {
            return 50
          }
        },
        // 节点点击事件
        nodeStateStyles: {
          hover: {
            fill: '#FFCFE3',
            shadowBlur: 10
          }
        },
        // 带有交互样式的边
        edgeStateStyles: {
          hover: {
            stroke: '#FF69B4',
            lineWidth: 2,
            opacity: 1
          }
        }
      })
      
      // 监听窗口大小变化，调整图表大小
      window.addEventListener('resize', () => {
        if (!this.graph || this.graph.get('destroyed')) return
        if (!this.$refs.container || !this.$refs.container.scrollWidth || !this.$refs.container.scrollHeight) return
        this.graph.changeSize(this.$refs.container.scrollWidth, this.$refs.container.scrollHeight)
      })
      
      // 绑定事件，监听节点交互
      this.graph.on('node:click', (evt) => {
        const node = evt.item
        const model = node.getModel()
        const name = evt.target.get('name')
        
        // 处理折叠按钮点击
        if (name === 'collapse-button-bg' || name === 'collapse-button-text') {
          model.collapsed = !model.collapsed
          this.graph.updateItem(node, model)
          
          // 手动更新图形
          this.graph.layout()
          this.graph.fitView()
        }
      })
      
      // 添加节点hover效果
      this.graph.on('node:mouseenter', (evt) => {
        const node = evt.item
        this.graph.setItemState(node, 'hover', true)
      })
      
      this.graph.on('node:mouseleave', (evt) => {
        const node = evt.item
        this.graph.setItemState(node, 'hover', false)
      })
      
      // 添加边hover效果
      this.graph.on('edge:mouseenter', (evt) => {
        const edge = evt.item
        this.graph.setItemState(edge, 'hover', true)
      })
      
      this.graph.on('edge:mouseleave', (evt) => {
        const edge = evt.item
        this.graph.setItemState(edge, 'hover', false)
      })
      
      // 渲染图表
      this.graph.data(this.data)
      this.graph.render()
      this.graph.fitView()
    }
  }
}
</script>

<style>
.org-tree-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  background-color: #FFF5F8;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
}

.graph-container {
  flex: 1;
  width: 100%;
  min-height: 500px;
  border-radius: 4px;
  overflow: hidden;
}
</style> 