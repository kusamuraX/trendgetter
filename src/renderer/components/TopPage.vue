<template>
  <div id="wrapper">
    <main>
      <div class="news_line">
        <div class="topic_contents" v-for="(topic,index) in topicData" :key="index">
          <img v-if="topic.topicImage" :src="topic.topicImage">
          <span>{{ topic.title }}</span>
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
            for (var k = 0; k < _trendingSearches[j].articles.length; k++) {
              if (this.topicData && _trendingSearches[j].articles[k].image) {
                this.topicData.push({
                  title: _trendingSearches[j].articles[k].title,
                  context: _trendingSearches[j].articles[k].snippet,
                  topicImage: _trendingSearches[j].articles[k].image.imageUrl
                })
              }
            }
          }
        }
      }
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
  display: flex;
  justify-content: space-between;
  flex-direction: column;
}

.topic_contents{
  display: flex;
}

main {
  display: flex;
  justify-content: space-between;
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