# vue-resize-split-pane

Resizable split pane using Vue.js

WIP + RAT

##Install
With yarn:
```
yarn add vue-resize-split-pane
```

With npm:

```
npm i vue-resize-split-pane --save
```

##Use

Global import

```js
//main.js
import Vue from 'vue'
import ResSplitPane from 'vue-resize-split-pane'

Vue.use(ResSplitPane)
```

Local import

```html
<template>
  <div id="app">
    <pane-rs split-to="columns" :allow-resize="true">
      <div slot="firstPane">
        FIRST
      </div>
      <div slot="secondPane">
        SECOND
      </div>
    </pane-rs>
    <!-- <router-view></router-view> -->
  </div>
</template>

<script>
  //App.vue or YourComponent.vue
  import ResSplitPane from 'vue-resize-split-pane'

  export default {
    name: 'app',
    components: {
      'pane-rs': ResSplitPane,
    },
    ...
</script>
```
##Slots
`firstPane` for left column or top row

`secondPane` for right column or bottom row

##Props
```javascript
props: {
  allow-resize: { type: Boolean, default: false },
  split-to: { type: String, default: 'columns' }, // columns | rows
  primary: { type: String, default: 'first' }, // first | second
  size: { type: Number, default: 100 }, // in pixels
  min-size: { type: Number, default: 16 }, // in pixels
  max-size: { type: Number, default: 0 }, // in pixels
  step: { type: Number, default: 0 },
},
```

## License

MIT © Valentin Bucur <valentin.bucur@gmail.com>