<template lang="html">
  <!-- rss feed -->
  <div class="feed">
    <br>
    <!-- feed header -->
    <div class="feed__header" v-if="posts">
      <div class="feed__header--description">
        {{feedHead.title}} <span class="Y" v-if="feedHead.description">////</span> {{feedHead.description}}
        <!-- feed panel -->
        <div class="feed__header--panel-container">
          <div class="feed__header--search-box">
            <input class="feed__header--search-input"
                type="text"
                placeholder="Search in feed"
                v-model="searchInput"
                @keyup.enter="showFoundPosts">
          </div>
          <button class="feed__header--refresh-button" title="Refresh feed" @click="fetchData"></button>
          <!-- end of feed panel -->
        </div>
      </div>
      <!-- end of feed header -->
    </div>
    <!-- feed posts -->
    <div class="feed__post" v-for="(post, index) in filteredPosts" :key="index">
      <img class="feed__post--image" :src="post.enclosure.link">
      <div class="feed__post--text-container">
        <div class="feed__post--category-name">
          {{feedHead.title}}
        </div>
        <div class="feed__post--title">
          {{post.title}}
        </div>
      </div>
      <a class="feed__post--link" :href="post.link" target="_blank"></a>
      <!-- end of posts -->
    </div>
    <!-- end of feed -->
  </div>
</template>

<script>

export default {
  name: 'FeedPreview',
  props: ['url'],
  data: () => ({
    posts: "",
    feedHead: "",
    searchInput: ""
  }),
  methods: {
    fetchData() {
      fetch('https://api.rss2json.com/v1/api.json?rss_url=' + encodeURIComponent(this.url))
        .then(response => { return response.json() })
        .then(json => {
          if (json.status === 'ok') {
            console.log(json)
            this.posts = json.items
            this.feedHead = json.feed
          } else {
            throw new Error();
          }
        })
        .catch(() => {
          this.$emit('error-handler', 'Unable to get data. Invalid rss link')
        })
    }
  },
  computed: {
    filteredPosts: function() {
      if (this.posts) return this.posts.filter(post => post.title.toLowerCase().indexOf(this.searchInput.toLowerCase()) !== -1)
    }
  },
  watch: {
    url: function() {
      this.fetchData()
    }
  }
}
</script>

<style lang="sass" scoped>

  .feed
    &__header
      margin-bottom: 70px
      &--description
        text-align: center
        padding: 20px 0 40px
        background-color: #979797
        color: #fff
        border-radius: 10px
        letter-spacing: 1px
        text-transform: uppercase
        position: relative
      &--panel-container
        display: flex
        padding: 10px 20px
        box-sizing: border-box
        position: absolute
        bottom: -20px
        right: 20px
        border-radius: 10px
        background-color: #fff
        box-shadow: 0px 0px 10px -7px #000
        width: 650px
      &--search
        &-box
          display: flex
          flex: 1
          align-items: center
          padding-right: 5px
          border-right: 1px solid lightgrey
          margin-right: 5px
          position: relative
          &::before
            content: ''
            display: block
            width: 26px
            height: 26px
            background-image: url('../assets/img/icons/find.svg')
            background-size: 26px 60%
            background-position: 0 50%
            background-repeat: no-repeat
            position: absolute
            top: 0
            right: 7px
            z-index: 1
        &-input
          flex: 1
          padding: 4px 10px
          border-radius: 5px
          border: 1px solid #c7c7c7
          position: relative
          &:focus
            border-color: lightblue
      &--refresh-button
        position: relative
        height: 26px
        width: 52px
        border-radius: 5px
        background-color: transparent
        border: 1px solid #c7c7c7
        &::before
          content: ''
          display: block
          width: 100%
          height: 100%
          background-image: url('../assets/img/icons/refresh.svg')
          background-size: 100% 70%
          background-position: 50% 50%
          background-repeat: no-repeat
        &:hover
          border-color: lightblue
          cursor: pointer

    &__post
      margin-bottom: 20px
      background-color: #fff
      box-shadow: 7px 7px 27px -22px rgba(77, 77, 77, 1)
      display: flex
      height: 150px
      border-radius: 10px
      position: relative
      transform: none
      transition: .2s linear
      &:hover
        transform: translateX(10px)
        box-shadow: 7px 7px 27px -22px rgba(0,242,113,1)
        .feed__post--link
          &::before
            opacity: .7
      &--image
        height: 100%
        border-radius: 10px 0 0 10px
      &--text-container
        display: flex
        flex: 1
        flex-direction: column
        justify-content: center
        align-items: center
        position: relative
        overflow: hidden
      &--title
        width: 80%
        font-weight: 400
        text-align: center
        line-height: 1.5em
        font-size: 18px
        letter-spacing: 1px
        transform: translateY(12px)
      &--link
        position: absolute
        width: 100%
        height: 100%
        opacity: 1
        &::before
          content: ''
          width: 32px
          height: 32px
          background-image: url('../assets/img/icons/arrow-right.svg')
          background-size: contain
          position: absolute
          top: 50%
          transform: translateY(-50%)
          right: 10px
          opacity: 0
          transition: .2s linear
      &--category-name
        width: 100%
        text-align: center
        position: absolute
        top: 0
        padding: 10px 0
        background-color: #f8f8f8
        font-weight: 300
        letter-spacing: 2px

</style>
