<template>
  <div class="title">
    <div class="line"></div>
    <div class="text">{{wenben}}</div>
    <div class="line"></div>
  </div>
</template>

<script>
export default {
  props: {
    wenben: {
      type: String
    }
  }
}
</script>

<style lang="stylus"  scoped>
.title
  display: flex
  width: 80vw
  margin: 30px auto 24px
  .line
    flex: 1
    position: relative
    top: -6px
    border-bottom: 1px solid rgba(255,255,255,.2)
  .text
    padding: 0 12px
    font-size: 14px
</style>
