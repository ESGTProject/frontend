<template lang="html">
  <div id="container">
    <h1 class="goleft">Mail</h1>
    <div id="ev-container">
      <div class="goleft" id='mail' v-for="m in mail">
        <p id="eventline" class="goleft">At: {{ m.date }}, From: {{ m.from }}</p>
        <p id="subj" class="goleft">{{ m.subject }}</p>
      </div>
    </div>
  </div>
</template>

<script>
  var moment = require('moment')
  export default {
    name: 'Gmail',
    data () {
      return {
        today: moment(),
        mail: []
      }
    },
    props: {
      dataUrl: String,
      userUID: String
    },
    mounted: function () {
      setInterval(function () {
        this.fetchData()
      }.bind(this), 10 * 1000)
    },
    watch: {
      userUID: function (newVal, oldVal) {
        if (newVal !== '') {
          this.fetchData()
        }
        return newVal
      }
    },
    computed: {
    },
    methods: {
      fetchData: function () {
        var self = this
        this.$http.get(this.dataUrl, {params: {user_uid: this.userUID, limit: 5}}).then((resp) => {
          var re = /"?(.+?)"*\s*"*</i
          self.mail = resp.body.map((mail) => {
            return {
              date: moment(mail.timestamp.slice(0, -6)).format('MM-DD|hh:mm a'),
              from: re.exec(mail.from)[1],
              subject: mail.subject
            }
          })
        })
      }
    }
  }
</script>

<style lang="css" scoped>
  #container {
    flex: auto 1 auto;
    overflow: auto;
    display: flex;
    flex-direction: column;
    align-self: flex-start;
  }
  #ev-container {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  #eventline {
    margin: 0px;
    font-size: 20px;
  }
  #subj {
    font-size: 22px;
  }
  #mail {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  .goleft {
    align-self: flex-start;
  }
  h1 {
    margin: 0px;
    font-size: 3em;
  }
  p {
    margin: 0px;
    font-size: 1em;
  }
</style>
