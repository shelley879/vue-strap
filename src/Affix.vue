<template>
  <div class="hidden-print hidden-xs hidden-sm">
    <nav class="bs-docs-sidebar" :class="{affix:affixed}" :style="{marginTop:top}">
      <slot></slot>
    </nav>
  </div>
</template>

<script>
import {coerce} from './utils/utils.js'
import $ from './utils/NodeList.js'

export default {
  props: {
    offset: {
      type: Number,
      coerce: coerce.number,
      default: 0
    }
  },
  data () {
    return {
      affixed:false
    }
  },
  computed: {
    top () {
      return this.offset > 0 ? this.offset + 'px' : null
    }
  },
  methods: {
    // from https://github.com/ant-design/ant-design/blob/master/components/affix/index.jsx#L20
    checkScroll () {
      // if is hidden don't calculate anything
      if (!(this.$el.offsetWidth || this.$el.offsetHeight || this.$el.getClientRects().length)) { return }
      // get window scroll and element position to detect if have to be normal or affixed
      let scroll = {}
      let element = {}
      const rect = this.$el.getBoundingClientRect()
      const body = document.body
      for(let type of ['Top','Left']) {
        let t = type.toLowerCase()
        let ret = window['page' + (type==='Top' ? 'Y' : 'X') + 'Offset']
        const method = 'scroll' + type
        if (typeof ret !== 'number') {
          // ie6,7,8 standard mode
          ret = document.documentElement[method]
          if (typeof ret !== 'number') {
            // quirks mode
            ret = document.body[method]
          }
        }
        scroll[t] = ret
        element[t] = scroll[t] + rect[t] - (this.$el['client'+type] || body['client'+type] || 0)
      }
      let fix = scroll.top > element.top - this.offset
      if (this.affixed !== fix) { this.affixed = fix }
    }
  },
  ready () {
    $(window).on('scroll resize', () => this.checkScroll())
    setTimeout(() => this.checkScroll(), 0)
  },
  beforeDestroy () {
    $(window).off('scroll resize', () => this.checkScroll())
  }
}
</script>
