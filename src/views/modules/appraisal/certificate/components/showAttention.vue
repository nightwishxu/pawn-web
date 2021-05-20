<template>
  <div class="attention_item flex_box">
    <div :class="{ attention_side: true, selected: showObject.isSelect }">
      <p class="title">{{ showObject.title }}</p>
      <p class="intro">{{ showObject.english }}</p>
    </div>
    <div class="remark flex_item flex_box">
      <span class="name flex_item">
        <p class="title">备注</p>
        <p class="intro">Remark</p>
      </span>
      <span class="remark_content">{{ showObject.remark }}</span>
    </div>
  </div>
</template>
<script>
export default {
  name: 'showAttention',
  props: {
    showObject: {
      type: Object,
      default: {}
    }
  }
}
</script>
