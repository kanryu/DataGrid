<template>
  <div>
    [DataGrid Sample] first row: {{beginRow}}, max row: {{maxRow}}, current display rows: {{renderRowCount}}
  </div>
  <div id="gridframe" class="gridframe"
    :style="{height:gridFrameHeight+'px', width: gridFrameWidth+'px'}">
    <div class="columnheader"
      :style="{minWidth: 100+150*maxColumn+'px'}">
      <div class="rowheader">⊿</div>
        <!-- column header -->
      <div class="colmnheadercell" v-for="(i,x) in Array(maxColumn)"
                    :key="x">A{{x+1}}</div>
    </div>
    <div class="framelayouter"
      :style="{height:gridHeight*(1+maxRow-beginRow)+'px', paddingTop:gridHeight*(1+beginRow)+'px'}">
      <div class="gridbase">
        <div class="row" v-for="(row, y) in modelValue.slice(beginRow,beginRow+renderRowCount)"
                      :key="y">
          <!-- row header -->
          <div class="rowheader">{{y+1+beginRow}}</div>
          <!-- data cells -->
          <div class="cell" v-for="(cell, x) in row"
                        :key="x">{{cell}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
export default {
  name: 'DataGrid',
  setup() {
    let maxRow = 65535
    let gridHeight = 25
    let maxColumn = 8
    let modelValue = []
    const beginRow = ref([])
    const scrollY = ref([])
    const gridFrameWidth = ref([])
    const gridFrameHeight = ref([])
    const renderRowCount = ref([])
    let scrollCash = 0
    beginRow.value = 0
    scrollY.value = 0
    for(let y = 0; y < maxRow; y++) {
      let line = []
      for(let x = 0; x < maxColumn; x++) {
        line.push(`cell (${x}.${y})`)
      }
      modelValue.push(line)
    }
    const windowResized = () => {
      // Resize the DataGrid to fit the size of the Window
      gridFrameWidth.value = window.innerWidth - 10
      gridFrameHeight.value = window.innerHeight - 100
      renderRowCount.value = Math.floor(gridFrameHeight.value/gridHeight)
    }
    onMounted(() => {
      let gridframe = document.getElementById('gridframe')
      gridframe.addEventListener('scroll', () => {
        // Window scrolling is in 1px units, so it is not suitable for cell scrolling.
        // Therefore, it is corrected so that it moves one line at a time.
        if (gridframe.scrollTop % gridHeight) {
          let adjusted = gridHeight*Math.floor((scrollCash+gridframe.scrollTop)/gridHeight) 
          scrollCash += gridframe.scrollTop - adjusted 
          gridframe.scrollTop = adjusted
        } else {
          scrollY.value = gridframe.scrollTop
          beginRow.value = Math.floor(scrollY.value/gridHeight)
        }
      })
      window.addEventListener('resize', windowResized)
      windowResized()
    })
    return {
      modelValue,
      maxRow, 
      maxColumn,
      gridHeight,
      beginRow,
      renderRowCount,
      scrollY,
      gridFrameWidth,
      gridFrameHeight
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  padding: 0px;
  margin: 0px;
}
.gridframe {
  width: 800px;
  height: 800px;
  scrollbar-width: auto;
  overflow: scroll;
}
.gridbase {
  background-color: #a0a0a0;
  display: table;
}
.row {
  margin: 1px;
  display: table-row;
}
.columnheader {
  position: fixed;
}
.colmnheadercell { /** top header */
  background-color: #d8d8d8;
  display: table-cell;
  width: 150px;
  height: 24px;
  border-bottom: 1px #a0a0a0 solid;
  border-right: 1px #a0a0a0 solid;
  text-align: center;
  vertical-align: bottom;
}
.rowheader { /** left header */
  background-color: #d8d8d8;
  display: table-cell;
  width: 50px;
  min-width: 50px;
  height: 24px;
  border-bottom: 1px #a0a0a0 solid;
  border-right: 1px #a0a0a0 solid;
  text-align: center;
  vertical-align: middle;
}
.cell {
  background-color: #ffffff;
  display: table-cell;
  width: 150px;
  min-width: 150px;
  height: 24px;
  border-bottom: 1px #a0a0a0 solid;
  border-right: 1px #a0a0a0 solid;
  text-align: center;
  vertical-align: middle;
}
</style>
