<template>
  <div id="wrapper">
    <main>
      <div class="news_line">
        <div class="topic_contents" v-for="(topic, index) in topicData" :key="index">
          <div class="topic_main">
            <img v-if="topic.topicImage" :src="topic.topicImage" />
            <div class="topic_context">
              <h2>
                <a v-on:click="openTopic(index)">{{ topic.title }}</a>
              </h2>
              <span>{{ topic.context }}</span>
            </div>
          </div>
          <div class="topic_details" v-if="topic.detailShow">
            <div v-for="(details, index2) in topic.topicDetail" :key="index2">
              <div v-on:click="openLink(details.topicUri)" class="detail_topic_image">
                <img :src="details.topicImage" />
                <div class="image_filter"></div>
                <p v-html="details.title"></p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
const googleTrends = require('google-trends-api')
export default {
  name: 'top-page',
  data () {
    return {
      topicData: []
    }
  },
  mounted: function () {
    googleTrends.dailyTrends(
      {
        trendDate: new Date(),
        geo: 'JP'
      },
      this.addData
    )
  },
  methods: {
    addData: function (err, results) {
      if (err) {
        console.log(err)
      } else {
        var obj = JSON.parse(results)
        var tsd = obj['default']['trendingSearchesDays']
        for (var i = 0; i < tsd.length; i++) {
          var _trendingSearches = tsd[i]['trendingSearches']
          for (var j = 0; j < _trendingSearches.length; j++) {
            if (this.topicData && _trendingSearches[j].image) {
              var topicDetailList = []
              for (var k = 0; k < _trendingSearches[j].articles.length; k++) {
                if (this.topicData && _trendingSearches[j].articles[k].image) {
                  topicDetailList.push({
                    title: _trendingSearches[j].articles[k].title,
                    context: _trendingSearches[j].articles[k].snippet,
                    topicImage: _trendingSearches[j].articles[k].image.imageUrl,
                    topicUri: _trendingSearches[j].articles[k].url
                  })
                }
              }
              this.topicData.push({
                title: _trendingSearches[j].title.query,
                context: _trendingSearches[j].articles[0].snippet,
                topicImage: _trendingSearches[j].image.imageUrl,
                topicUri: _trendingSearches[j].image.newsUrl,
                topicDetail: topicDetailList,
                detailShow: false
              })
            }
          }
        }
        console.log(this.topicData)
      }
    },
    openTopic: function (index) {
      if (this.topicData[index].detailShow) {
        this.topicData[index].detailShow = false
      } else {
        this.topicData[index].detailShow = true
      }
    },
    openLink: function (url) {
      window.open(url, '_blank', 'nodeIntegration=no')
    }
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Source+Sans+Pro");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Source Sans Pro", sans-serif;
}

.news_line {
  width: 100%;
  display: flex;
  padding: 1em;
  justify-content: space-between;
  flex-direction: column;
}

.topic_contents {
  display: flex;
  flex-direction: column;
  margin-bottom: 2em;
}

.topic_context {
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  margin-left: 1em;
}

.topic_main {
  display: flex;
}

.topic_details {
  display: flex;
  margin-top: 0.5em;
  justify-content: start;
  overflow-x: scroll;
  overflow-y: hidden;
}

.detail_topic_image{
  position: relative;
  overflow: hidden;
  margin-right: 1em;
  width: 10em;
  height: 10em;
}

.image_filter{
  background-color: rgba(0, 0, 0, 0.5);
  width: 10em;
  height: 10em;
  position: absolute;
  top: 0;
  left: 0;
}

.detail_topic_image > img {
  width: 10em;
  height: 10em;
}

.detail_topic_image > p {
  font: 0.8em sans-serif;
  font-weight: bold;
  position: absolute;
  top: 50%;
  -ms-transform: translate(0%,-50%);
  -webkit-transform: translate(0%,-50%);
  transform: translate(0%,-50%);
  margin:0 0.5em;
  padding:0;
  color: white;
}

.detail_topic_image:hover > img {
  transition-delay: 150ms;
  -ms-transform: scale(1.3,1.3);
  -webkit-transform: scale(1.3,1.3);
  transform: scale(1.3,1.3);
}

main > div {
  flex-basis: 50%;
}

.left-side {
  display: flex;
  flex-direction: column;
}

.welcome {
  color: #555;
  font-size: 23px;
  margin-bottom: 10px;
}

.title {
  color: #2c3e50;
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 6px;
}

.title.alt {
  font-size: 18px;
  margin-bottom: 10px;
}

.doc p {
  color: black;
  margin-bottom: 10px;
}

.doc button {
  font-size: 0.8em;
  cursor: pointer;
  outline: none;
  padding: 0.75em 2em;
  border-radius: 2em;
  display: inline-block;
  color: #fff;
  background-color: #4fc08d;
  transition: all 0.15s ease;
  box-sizing: border-box;
  border: 1px solid #4fc08d;
}

.doc button.alt {
  color: #42b983;
  background-color: transparent;
}

</style>