<template>
  <div ref="chart" />
</template>

<script setup>
  import { onMounted, ref } from 'vue'

  const chart = ref(null)

  const treeData = {
    name: '根節點',
    children: [
      {
        name: '子節點 A',
        children: [
          { name: '孫節點 A1' },
          { name: '孫節點 A2' },
        ],
      },
      { name: '子節點 B' },
    ],
  }

  onMounted(() => {
    const d3 = window.d3

    const margin = { top: 20, right: 90, bottom: 30, left: 90 }
    const width = 960 - margin.left - margin.right
    const height = 500 - margin.top - margin.bottom

    const svg = d3
      .select(chart.value)
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`)

    let i = 0
    const duration = 750
    const treemap = d3.tree().size([height, width])

    const root = d3.hierarchy(treeData)
    root.x0 = height / 2
    root.y0 = 0

    if (root.children) {
      for (const child of root.children) {
        collapse(child)
      }
    }
    update(root)

    function collapse (d) {
      if (d.children) {
        d._children = d.children
        for (const child of d._children) {
          collapse(child)
        }
        d.children = null
      }
    }

    function update (source) {
      const treeData = treemap(root)
      const nodes = treeData.descendants()
      const links = treeData.links()

      for (const d of nodes) (d.y = d.depth * 180)

      const node = svg
        .selectAll('g.node')
        .data(nodes, d => d.id || (d.id = ++i))

      const nodeEnter = node
        .enter()
        .append('g')
        .attr('class', 'node')
        .attr('transform', `translate(${source.y0},${source.x0})`)
        .on('click', click)

      nodeEnter
        .append('circle')
        .attr('r', 1e-6)
        .style('fill', d => (d._children ? 'lightsteelblue' : '#fff'))

      nodeEnter
        .append('text')
        .attr('dy', '.35em')
        .attr('x', d => (d._children ? -13 : 13))
        .attr('text-anchor', d => (d._children ? 'end' : 'start'))
        .text(d => d.data.name)

      const nodeUpdate = nodeEnter.merge(node)
      nodeUpdate
        .transition()
        .duration(duration)
        .attr('transform', d => `translate(${d.y},${d.x})`)

      nodeUpdate
        .select('circle')
        .attr('r', 10)
        .style('fill', d => (d._children ? 'lightsteelblue' : '#fff'))

      const nodeExit = node
        .exit()
        .transition()
        .duration(duration)
        .attr('transform', `translate(${source.y},${source.x})`)
        .remove()

      nodeExit.select('circle').attr('r', 1e-6)
      nodeExit.select('text').style('fill-opacity', 1e-6)

      const link = svg
        .selectAll('path.link')
        .data(links, d => d.target.id)

      const linkEnter = link
        .enter()
        .insert('path', 'g')
        .attr('class', 'link')
        .attr('d', () => {
          const o = { x: source.x0, y: source.y0 }
          return elbow(o, o)
        })

      const linkUpdate = linkEnter.merge(link)
      linkUpdate
        .transition()
        .duration(duration)
        .attr('d', d => elbow(d.source, d.target))

      link
        .exit()
        .transition()
        .duration(duration)
        .attr('d', () => {
          const o = { x: source.x, y: source.y }
          return elbow(o, o)
        })
        .remove()

      for (const d of nodes) {
        d.x0 = d.x
        d.y0 = d.y
      }
    }

    function elbow (s, d) {
      return `M${s.y},${s.x}H${(s.y + d.y) / 2}V${d.x}H${d.y}`
    }

    function click (event, d) {
      if (d.children) {
        d._children = d.children
        d.children = null
      } else {
        d.children = d._children
        d._children = null
      }
      update(d)
    }
  })
</script>

<style scoped>
.node circle {
  fill: #fff;
  stroke: steelblue;
  stroke-width: 2px;
}

.node text {
  font: 12px sans-serif;
}

.link {
  fill: none;
  stroke: #ccc;
  stroke-width: 2px;
}
</style>
