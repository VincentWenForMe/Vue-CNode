<template>
  <div class="user-detail-wrap">
    <div class="user-detail">
      <back-bar :title="userDetailData.loginname"></back-bar>
      <div class="user-info">
        <h2 class="user-title">个人信息</h2>
        <img class="user-avatar" :src="userDetailData.avatar_url" alt="avatar" />
        <span class="loginname">{{ userDetailData.loginname }}</span>
        <p class="score">{{ userDetailData.score }} 积分</p>
        <div class="github"><i class="iconfont icon-github"></i> <a target="_blank" :href="'https://github.com/' + userDetailData.githubUsername">@{{ userDetailData.githubUsername }}</a></div>
        <p class="create-time">注册时间{{ userDetailData.create_at | timeFormat }}</p>
      </div>
      <div class="topics">
        <h2 class="user-title">最近创建的话题</h2>
        <ul class="topic-lists" v-if="userDetailData.recent_topics.length">
          <li v-for="item in userDetailData.recent_topics">
            <article-card :article="item" :simpleMode="true"></article-card>
          </li>
        </ul>
        <p class="no-content" v-if="!userDetailData.recent_topics.length">无话题</p>
      </div>
      <div class="replies">
        <h2 class="user-title">最近参与的话题</h2>
        <ul class="reply-lists" v-if="userDetailData.recent_topics.length">
          <li v-for="item in userDetailData.recent_replies">
            <article-card :article="item" :simpleMode="true"></article-card>
          </li>
        </ul>
        <p class="no-content" v-if="!userDetailData.recent_topics.length">无话题</p>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BackBar from '../BackBar/BackBar'
  import ArticleCard from '../Content/ArticleCard/ArticleCard'
  import { mapGetters } from 'vuex'
  import timeFormat from '../../common/utils/timeFormat'

  export default {
    name: 'UserDetail',
    components: {
      BackBar,
      ArticleCard
    },
    computed: {
      ...mapGetters([
        'userDetailData'
      ])
    },
    filters: {
      timeFormat
    },
    methods: {
      getUserData () {
        // 首先恢复scrollTop值至0
        window.scrollTo(0, 0)
        // 先显示正在加载页面
        this.$store.dispatch('changeLoadingStatus', true)
        // 异步获取数据
        this.axios.get(`https://cnodejs.org/api/v1/user/${this.$route.params.loginname}`)
          .then(res => {
            this.$store.dispatch('changeLoadingStatus', false)
            this.$store.dispatch('initUserDetailData', res.data.data)
          })
      }
    },
    watch: {
      '$route': 'getUserData'
    },
    created () {
      this.getUserData()
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss">
  .user-detail-wrap {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    .user-detail {
      margin-top: 48px;
      /*所有内容下的标题使用以下统一样式*/
      .user-title {
        margin: 10px 0;
        width: 100%;
        font-size: 20px;
        line-height: 48px;
        color: #1c2438;
        background: #e9eaec;
        text-indent: 20px;
      }
      .user-info {
        .user-avatar {
          margin-left: 20px;
          width: 48px;
          height: 48px;
          border-radius: 50%;
        }
        .loginname {
          vertical-align: top;
          line-height: 48px;
          font-size: 14px;
        }
        .score, .github, .create-time {
          margin-left: 20px;
          line-height: 28px;
          font-size: 14px;
        }
      }
      .topics, .replies {
        .no-content {
          font-size: 18px;
          line-height: 48px;
          text-indent: 20px;
        }
      }
    }
  }
</style>
