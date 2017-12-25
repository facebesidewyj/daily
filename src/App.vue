<template>
  <div class="daily">
    <div class="daily-menu">
      <div class="daily-menu-item" :class="{on: type === 'recommend'}" @click="handleToRecommend">每日推荐</div>
      <div class="daily-menu-item" :class="{on: type === 'daily'}" @click="showThemes = !showThemes">主题日报</div>
      <ul v-show="showThemes">
        <li v-for="item in themes">
          <a :class="{on: item.id === themeId && type === 'daily'}" @click="handleToTheme(item.id)">{{item.name}}</a>
        </li>
      </ul>
    </div>
    <div class="daily-list" ref="list" @scroll="handleScroll">
      <template v-if="type === 'recommend'">
        <div v-for="list in recommendList">
          <div class="daily-date">{{formatDay(list.date)}}</div>
          <Item v-for="item in list.stories" :data="item" :key="item.id" @click.native="handleClick(item.id)"></Item>
        </div>
      </template>
      <template v-if="type === 'daily'">
          <Item v-for="item in list" :data="item" :key="item.id" @click.native="handleClick(item.id)"></Item>
      </template>
    </div>
    <daily-article :id="articleId"></daily-article>
  </div>
</template>

<script>
import Item from './components/item/item.vue';
import dailyArticle from './components/daily-article/daily-article.vue';
import $ from './common/js/util.js';

export default {
  data() {
    return {
      themes: [],
      showThemes: false,
      type: 'recommend',
      themeId: 0,
      isLoading: false,
      recommendList: [],
      dailyTime: $.getTodayTime(),
      list: [],
      articleId: 0
    };
  },
  methods: {
    getThemes() {
      $.ajax.get('themes').then((res) => {
        this.themes = res.others;
      });
    },
    handleToTheme(id) {
      this.type = 'daily';
      this.themeId = id;
      this.list = [];
      $.ajax.get('theme/' + id).then((res) => {
        this.list = res.stories.filter((item) => {
          return item.type !== 1;
        });
      });
    },
    handleToRecommend() {
      this.type = 'recommend';
      this.recommendList = [];
      this.dailyTime = $.getTodayTime();
      this.getRecommendList();
    },
    getRecommendList() {
      this.isLoading = true;
      const prevDay = $.getPrevDay(this.dailyTime - 86400000);
      $.ajax.get('news/before/' + prevDay).then((res) => {
        this.recommendList.push(res);
        this.isLoading = false;
      });
    },
    formatDay(date) {
      let month = date.substr(4, 2);
      let day = date.substr(6, 2);
      if (month.substr(0, 1) === '0') {
        month = month.substr(1, 1);
      }

      if (day.substr(0, 1) === '0') {
        day = day.substr(1, 1);
      }
      return `${month}月${day}日`;
    },
    handleScroll() {
      const $list = this.$refs.list;

      if (this.type === 'daliy' || this.isLoading) {
        return false;
      }

      if ($list.scrollTop + document.body.clientHeight >= $list.scrollHeight) {
        this.dailyTime -= 86400000;
        this.getRecommendList();
      }
    },
    handleClick(id) {
      this.articleId = id;
    }
  },
  mounted() {
    this.getRecommendList();
    this.getThemes();
  },
  components: {
    Item,
    dailyArticle
  }
};
</script>

<style scoped lang="less">
.daily-menu {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  width: 150px;
  overflow: auto;
  background-color: #f5f7f9;
  ul {
    list-style: none;
    li a{
      display: block;
      padding: 5px 0;
      margin: 5px 0;
      text-align: center;
      cursor: pointer;
      color: inherit;
      &:hover, .on{
        color: #3399ff;
      }
    }
  }
}

.daily-menu-item {
  margin: 5px 0;
  padding:  10px 0;
  border-right: 2px solid transparent;
  transition: all .3s ease-in-out;
  text-align: center;
  cursor: pointer;
  font-size: 18px;
}

.daily-menu-item:hover {
  background-color: #e3e8ee;
}

.daily-menu-item.on {
  border-right: 2px solid #3399ff;
}

.dily-list {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  overflow: auto;
  border-right: 1px solid #d7dde4;
  width: 300px;
}

.daily-item {
  display: block;
  padding: 16px;
  text-decoration: none;
  overflow: hidden;
  cursor: pointer;
  transition: all .3s ease-in-out;
  color: inherit;
}

.daily-item:hover{
  background-color: #e3e8ee;
}

.daily-article {
  margin-left: 450px;
  padding: 20px;
}
</style>
