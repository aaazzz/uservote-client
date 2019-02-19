<template>
  <div class="hello">
    <div class="conaiter">
      <div class="columns is-centered">
        <div class="column is-half has-background-light" ref="element">
          <h2 class="subtitle">Was this page helpful?</h2>
          <div class="columns has-text-centered is-centered">
            <div class="column">
              <a @click="sendData(3)">
                <Planet :size="64" mood="blissful" color="#FCCB7E" />
                <span v-if="vote == 3">Voted!</span>
              </a>
            </div>
            <div class="column">
              <a @click="sendData(2)">
                <Planet :size="64" mood="happy" color="#FCCB7E" />
                <span v-if="vote == 2">Voted!</span>
              </a>
            </div>
            <div class="column">
              <a @click="sendData(1)">
                <Planet :size="64" mood="ko" color="#FCCB7E" />
                <span v-if="vote == 1">Voted!</span>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Planet } from 'vue-kawaii'
import axios from 'axios'
import { v1 as uuidv1 } from 'uuid'
import moment, { locale } from 'moment'

// const apiUrl = 'http://localhost:3000/'
const apiUrl =
  'https://umvu6pfbc5.execute-api.ap-northeast-1.amazonaws.com/dev/'

export default {
  name: 'HelloWorld',
  components: {
    Planet
  },
  data() {
    return {
      isFullPage: false,
      vote: 0
    }
  },
  created() {
    this.setUuid()
    this.setVote()
  },
  methods: {
    setVote() {
      this.vote = localStorage.getItem(location.pathname) || 0
    },
    async setUuid() {
      if (!localStorage.getItem('uuid')) {
        localStorage.setItem('uuid', uuidv1())
        localStorage.setItem(location.pathname, 0)
      }
    },
    toast() {
      this.$toast.open({
        duration: 1000,
        message: `Thanks you for voting!XD`,
        position: 'is-bottom',
        type: 'is-info'
      })
    },
    async sendData(score) {
      const uuid = localStorage.getItem('uuid')

      // Open Loader
      const loadingComponent = this.$loading.open({
        container: this.isFullPage ? null : this.$refs.element.$el
      })
      const headers = {
        'Content-Type': 'application/json'
      }

      await axios
        .post(
          apiUrl + 'todo',
          {
            Item: {
              id: uuid,
              score: score,
              hostname: location.hostname,
              pathname: location.pathname
            }
          },
          { headers: headers }
        )
        .then(function(response) {
          console.log(response)
        })
        .catch(function(error) {
          console.log(error)
        })

      await axios
        .get(apiUrl + 'todo/' + uuid)
        .then(response => console.table(response.data.Item))
      console.log('Data sent!')
      // Close Loader
      loadingComponent.close()

      localStorage.setItem(location.pathname, score)
      this.setVote()
      this.toast()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
