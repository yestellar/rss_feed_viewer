<template>
  <div class="home">
    <div class="content-container">
      <!-- header -->
      <div class="header">
        <span class="header__logo">Rss viewer</span>
        <div class="header__input-box">
          <input class="header__input-box--input"
            type="text"
            placeholder="Paste rss link here"
            v-model="rssInput"
            @keyup.enter="loadRss">
          <button class="header__input-box--button" @click="rssInput = ''" v-if="rssInput == link && rssInput != '' ">clear</button>
          <button class="header__input-box--button" @click="loadRss" v-else>load</button>
        </div>
      </div>
      <!-- error box -->
      <div class="error-box" v-if="showError">
        Error: {{errorString}}
      </div>
      <!-- feed -->
      <feed-preview :url="link" @error-handler="errorHandler"></feed-preview>
    </div>
  </div>
</template>

<script>
import FeedPreview from '@/components/FeedPreview.vue'

import isURL from 'validator/lib/isURL'

export default {
  name: 'Home',
  components: {
    'feed-preview': FeedPreview
  },
  data: () => {
    return {
      link: "",
      rssInput: "",
      showError: false,
      errorString: ""
    }
  },
  methods: {
    loadRss() {
      // clear error box
      this.showError = false
      this.errorString = ""
      // check for url format
      if(!isURL(this.rssInput, {require_protocol: true})) this.errorHandler("Incorrect url format")
      else {
        this.link = this.rssInput
      }
    },
    errorHandler(string) {
      this.showError = true
      this.errorString = string
      this.rssInput = ""
    }
  }
}
</script>

<style lang="sass">

.content-container
  width: 940px
  margin: 0 auto
  padding-bottom: 50px

.header
  display: flex
  background-color: #fff
  padding: 20px 20px
  border-radius: 10px
  margin: 50px 0 20px
  box-shadow: 7px 7px 27px -22px #4d4d4d
  &__logo
    display: flex
    justify-content: center
    align-items: center
    width: 267px
    text-transform: uppercase
    font-weight: 500
    letter-spacing: 2px
    color: #515151
    position: relative
    font-size: 20px
    &::before
      content: ''
      display: block
      width: 20px
      height: 20px
      position: absolute
      top: 50%
      transform: translateY(-50%)
      left: 37px
      background-image: url('../assets/img/icons/rss.svg')
      background-size: contain
  &__input-box
    flex: 1
    display: flex
    &--input
      flex: 1
      padding: 4px 10px
      border-radius: 5px
      border: 1px solid #c7c7c7
      margin-right: 5px
    &--button
      height: 26px
      width: 52px
      border-radius: 5px
      background-color: #fff
      border: 1px solid #c7c7c7
      position: relative
      // &::before
      //   content: ''
      //   display: block
      //   width: 100%
      //   height: 100%
      //   background-image: url('../assets/img/icons/eye.svg')
      //   background-size: 100% 90%
      //   background-repeat: no-repeat
      //   background-position: 50% 50%
      &:hover
        border-color: lightblue
        cursor: pointer
.error-box
  background-color: #fff
  box-shadow: 2px 2px 5px -3px red
  padding: 20px 0
  display: flex
  border-radius: 10px
  justify-content: center
  letter-spacing: 1px
</style>
