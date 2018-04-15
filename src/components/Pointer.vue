<template lang="pug">
  #pointer(ref='pointer' :class='{hidden: !isTracking}')
</template>

<script>
import { mapState } from 'vuex'

export default {
  computed: mapState([
    'isTracking',
    'lastFace',
    'refs'
  ]),

  watch: {
    lastFace (face) { this.drawCursor(face) }
  },

  mounted () { this.$store.commit('set', ['refs', {pointer: this.$refs.pointer}]) },

  methods: {
    /**
     * Draws the cursor
     *
     * @param {OBJ} face The face to draw for
     */
    drawCursor (face) {
      face = face.face

      const $feed = this.refs.feed
      const ratio = {
        width: window.innerWidth / $feed.width,
        height: window.innerHeight / $feed.height
      }
      // The canvas is mirrored, so left needs a few more operations
      let left = -face.translationX * ratio.width + $feed.width + $feed.offsetLeft
      let top = face.translationY * ratio.height + $feed.offsetTop

      left += Math.sin(face.rotationY) * window.innerWidth + $feed.offsetLeft
      top += Math.sin(face.rotationX) * window.innerHeight

      this.refs.pointer.style = `left: ${left}px; top: ${top}px`
    }
  }
}
</script>

<style scoped lang="stylus">
  #pointer
    position: fixed
    z-index: 99999999
    width: 15px
    height: 15px
    border-radius: 15px
    background: rgba(255, 0, 0, 0.85)
</style>