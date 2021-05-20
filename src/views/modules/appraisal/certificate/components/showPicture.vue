<template>
  <div>
    <div class="picture_box">
      <div class="picture_big"><img class="show_big" style="height: 500px;" :src="showData.mainPicture" /></div>
      <div class="small_box flex_box">
        <div class="small_item flex_item" v-for="(item, index) in showData.subPictures" :key="index"><img class="show_small" :src="item" /></div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'showPicture',
  props: {
    showData: {
      type: Object,
      default: {}
    }
  }
}
</script>
