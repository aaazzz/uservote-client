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
              </a>
            </div>
            <div class="column">
              <a @click="sendData(2)">
                <Planet :size="64" mood="happy" color="#FCCB7E" />
              </a>
            </div>
            <div class="column">
              <a @click="sendData(1)">
                <Planet :size="64" mood="ko" color="#FCCB7E" />
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Planet } from "vue-kawaii";
import axios from "axios";
import { v1 as uuidv1 } from "uuid";
import moment from "moment";

export default {
  name: "HelloWorld",
  components: {
    Planet
  },
  data() {
    return {
      isFullPage: false
    };
  },
  methods: {
    toast() {
      this.$toast.open({
        duration: 1000,
        message: `Thanks you for voting!XD`,
        position: "is-bottom",
        type: "is-info"
      });
    },
    async sendData(score) {
      // Open Loader
      const loadingComponent = this.$loading.open({
        container: this.isFullPage ? null : this.$refs.element.$el
      });
      // const apiUrl = 'http://localhost:3000/'
      const apiUrl =
        "https://umvu6pfbc5.execute-api.ap-northeast-1.amazonaws.com/dev/";
      const headers = {
        "Content-Type": "application/json"
      };
      const uuid = uuidv1();
      await axios
        .post(
          apiUrl + "todo",
          {
            Item: { id: uuid, score: score }
          },
          { headers: headers }
        )
        .then(function(response) {
          console.log(response);
        })
        .catch(function(error) {
          console.log(error);
        });

      await axios
        .get(apiUrl + "todo/" + uuid)
        .then(response => console.table(response.data.Item));
      console.log("Data sent!");
      // Close Loader
      loadingComponent.close();
      this.toast();
    }
  }
};
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
