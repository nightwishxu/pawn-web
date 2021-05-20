<template>
  <div>
    <div class="info_item flex_box">
      <span class="name flex_item">{{ title }}</span>
      <span class="va">{{ value }}</span>
    </div>
    <div class="line"></div>
  </div>
</template>
<script>
export default {
  name: 'showInfoItem',
  props: {
    title: {
      type: String,
      default: ''
    },
    value: {
      type: String,
      default: ''
    }
  }
}
</script>
