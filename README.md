# vue-resize-split-pane

Splittable and resizable panes for Vue.js

WIP + RAT

## Install

With yarn:
```
yarn add vue-resize-split-pane
```

With npm:

```
npm i vue-resize-split-pane --save
```

## Use

Global import

```js
//main.js
import Vue from 'vue'
import ResSplitPane from 'vue-resize-split-pane'

Vue.component('rs-panes', ResSplitPane)
```

Local import

```html
<template>
  <div id="app">
    <rs-panes split-to="columns" :allow-resize="true">
      <div slot="firstPane">
        FIRST
      </div>
      <div slot="secondPane">
        SECOND
      </div>
    </rs-panes>
    <!-- <router-view></router-view> -->
  </div>
</template>

<script>
  //App.vue or YourComponent.vue
  import ResSplitPane from 'vue-resize-split-pane'

  export default {
    name: 'app',
    components: {
      'rs-panes': ResSplitPane,
    },
    ...
</script>
```

Or you can nest them

```html
<template>
  <div id="app">
    <rs-panes split-to="columns" :allow-resize="true">
      <div slot="firstPane">
        LEFT
      </div>
      <rs-panes :allowResize="true" split-to="rows"
        slot="secondPane" primary="second">
        <rs-panes :allowResize="true" split-to="columns" 
          slot="firstPane" primary="second">
          <div slot="firstPane">
            CENTER
          </div>
          <div slot="secondPane">
            RIGHT
          </div>
        </rs-panes>
        <div slot="secondPane">
          BOTTOM
        </div>
      </rs-panes>
    </rs-panes>
  </div>
</template>
```

## Slots

`firstPane` for left column or top row

`secondPane` for right column or bottom row

## Props

```javascript
props: {
  'allow-resize': { type: Boolean, default: false },
  'split-to': { type: String, default: 'columns' }, // columns || rows
  'primary': { type: String, default: 'first' }, // first || second
  'size': { type: Number, default: 25 }, // in pixels || percents
  'units': { type: String, default: 'pixels' }, // pixels || percents
  'min-size': { type: Number, default: 16 }, // in pixels || percents
  'max-size': { type: Number, default: 0 }, // in pixels || percents
  'step': { type: Number, default: 0 }, // in pixels
},
```

The `primary` prop is used to specify which of the two panes has its size fixed.

The `size` prop is either width or height depending on how the panes are split.


## License

MIT © Valentin Bucur <valentin.bucur@gmail.com>
