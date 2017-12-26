<template>
  <a class="daily-item">
    <div class="daily-img" v-if="data.images">
      <img :src="imgPath + data.images[0]" alt="日报图片">
    </div>
    <div class="daily-title" :class="{noImg: !data.images}">{{data.title}}</div>
  </a>
</template>

<script>
import $ from '../../common/js/util.js';

export default {
  props: {
    data: {
      type: Object
    }
  },
  data() {
    return {
      imgPath: $.imgPath
    };
  }
};
</script>

<style scoped lang="less">
  .dailt-item {
    display: block;
    padding: 16px;
    overflow: hidden;
    cursor: pointer;
    transition: all .3s ease-in-out;
    color: inherit;
    &:hover {
      background-color: #e3e8ee;
    }
  }

  .daily-img {
    float: left;
    width: 80px;
    height: 80px;
    img {
      width: 100%;
      height: 100%;
      border-radius: 3px;
    }
  }

  .daily-title {
    padding: 10px 5px 10px 90px;
    &.noImg {
      padding-left: 5px;
    }
  }
</style>
