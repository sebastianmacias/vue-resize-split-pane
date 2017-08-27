<template>
  <div
    class="split-pane root"
    :class="classObject"
    @mousemove="onMouseMove"
    @mouseup="onMouseUp">
      <pane-comp
        ref="panel1"
        :class="{column: split === 'columns', row: split === 'rows'}"
        :style="iStyle('first')">
          <slot name='firstPane'></slot>
      </pane-comp>
      <resizer-comp
      @mousedown.native="onMouseDown"
        v-if="allowResize"
        :class="{
          rows: split === 'rows',
          columns: split === 'columns'}"></resizer-comp>
      <pane-comp
        ref="panel2"
        :class="{column: split === 'columns', row: split === 'rows'}"
        :style="iStyle('second')">
          <slot name='secondPane'></slot>
      </pane-comp>
  </div>
</template>

<script>
import Resizer from './Resizer'
import Pane from './Pane'

function unFocus(document, window) {
  if (document.selection) {
    document.selection.empty()
  } else {
    try {
      window.getSelection().removeAllRanges()
      // eslint-disable-next-line no-empty
    } catch (e) {
      console.log(e)
    }
  }
}

export default {
  name: 'split-pane',
  components: {
    'resizer-comp': Resizer,
    'pane-comp': Pane,
  },
  props: {
    allowResize: { type: Boolean, default: false },
    split: { type: String, default: 'columns' },
    primary: { type: String, default: 'first' },
    size: { type: Number, default: 100 },
    minSize: { type: Number, default: 16 },
    maxSize: { type: Number, default: 0 },
    step: { type: Number, default: 0 },
  },
  data() {
    return {
      active: false,
      position: 0,
      localSize: this.size,
    }
  },
  computed: {
    classObject() {
      return {
        columns: this.split === 'columns',
        rows: this.split === 'rows',
      }
    },
    iStyle: function() {
      return function(el) {
        let style = { flex: 1, position: 'relative', outline: 'none' }

        if (el === this.primary) {
          style.flex = '0 0 auto'
          this.split === 'columns'
            ? (style.width = this.localSize + 'px')
            : (style.height = this.localSize + 'px')
        } else {
          style.flex = '1 1 0%'
        }
        return style
      }
    },
  },
  methods: {
    onMouseDown(event) {
      if (this.allowResize) {
        const eventWithTouches = Object.assign({}, event, {
          touches: [
            {
              clientX: event.clientX,
              clientY: event.clientY,
            },
          ],
        })
        this.onTouchStart(eventWithTouches)
      }
    },
    onTouchStart(event) {
      if (this.allowResize) {
        unFocus(document, window)
        const position =
          this.split === 'columns'
            ? event.touches[0].clientX
            : event.touches[0].clientY

        if (typeof this.onDragStarted === 'function') {
          onDragStarted()
        }
        this.active = true
        this.position = position
      }
    },
    onMouseMove(event) {
      if (this.allowResize) {
        const eventWithTouches = Object.assign({}, event, {
          touches: [
            {
              clientX: event.clientX,
              clientY: event.clientY,
            },
          ],
        })
        this.onTouchMove(eventWithTouches)
      }
    },
    onTouchMove(event) {
      const { active, position } = this.$data
      const {
        maxSize,
        minSize,
        step,
        allowResize,
        split,
        primary,
      } = this.$props
      if (allowResize && active) {
        unFocus(document, window)
        const isPrimaryFirst = primary === 'first'
        const ref = isPrimaryFirst ? 'panel1' : 'panel2'
        if (ref) {
          const node = this.$refs[ref].$vnode.elm
          if (node.getBoundingClientRect) {
            const width = node.getBoundingClientRect().width
            const height = node.getBoundingClientRect().height
            const current =
              split === 'columns'
                ? event.touches[0].clientX
                : event.touches[0].clientY
            const size = split === 'columns' ? width : height
            let positionDelta = position - current
            const sizeDelta = isPrimaryFirst ? positionDelta : -positionDelta
            let newSize = size - sizeDelta
            const newPosition = position - positionDelta

            if (this.step) {
              if (Math.abs(positionDelta) < this.step) {
                return
              }
              // Integer division
              // eslint-disable-next-line no-bitwise
              positionDelta = ~~(positionDelta / this.step) * this.step
            }

            if (minSize && newSize < minSize) {
              newSize = minSize
            }
            if (maxSize && newSize > maxSize) {
              newSize = maxSize
            }
            this.localSize = newSize
            this.position = newPosition
          }
        }
      }
    },
    onMouseUp() {
      // console.log(this)
      const { allowResize, onDragFinished } = {
        allowResize: this.allowResize,
        onDragFinished: this.onDragFinished,
      }
      const { active, draggedSize } = {
        active: this.active,
        draggedSize: this.draggedSize,
      }
      if (allowResize && active) {
        if (typeof onDragFinished === 'function') {
          onDragFinished(draggedSize)
        }
        this.active = false
      }
    },
  },
}
</script>

<style scoped>
.root {
  height: 100%;
  width: 100%;
}

.split-pane {
  display: flex;
  flex: 1;
  /*align-items: stretch;
  align-content: stretch;*/
  position: absolute;
  outline: none;
  overflow: hidden;
  user-select: text;
}
</style>