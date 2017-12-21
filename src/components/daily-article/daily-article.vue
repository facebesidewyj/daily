<template>
  <div class="daily-article">
    <div class="daily-article-title">{{data.title}}</div>
    <div class="daily-article-content" v-html="data.body"></div>
  </div>
</template>

<script>
import $ from '../../common/js/util.js';

export default {
  props: {
    id: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      data: {}
    };
  },
  methods: {
    getArticle() {
      $.ajax.get('news/' + this.id).then((res) => {
        res.body = res.body.replace(/src="http/g, 'src="' + $.imPath + 'http');
        res.body = res.body.replace(/src="https/g, 'src="' + $.imPath + 'https');
        this.data = res;
        window.scrollTo(0, 0);
      });
    }
  },
  watch: {
    id(val) {
      if (val) {
        this.getArticle();
      }
    }
  }
};
</script>

<style>
</style>
