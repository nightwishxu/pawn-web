<template>
  <div>
    <div class="info_item flex_box">
      <span class="name flex_item">
        <p class="title">{{ title }}</p>
        <p class="intro">{{ english }}</p>
      </span>
      <div class="star_box">
        <img v-for="num in starNum" :key="num" class="show_star" src="../../../../../assets/image/star_on.png" />
        <img v-for="offNum in 5 - starNum" :key="offNum + '_index'" class="show_star" src="../../../../../assets/image/star_off.png" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'showStar',
  props: {
    title: {
      type: String,
      default: ''
    },
    english: {
      type: String,
      default: ''
    },
    starNum: {
      type: Number,
      default: 5
    }
  }
}
</script>
