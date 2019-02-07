<template>
  <div id="app">
    <p>
      Has Karmik passed
      <b>1:15</b> yet?
    </p>
    <h1 v-if="!tie">{{ vueHasPassedReact ? 'YES' : 'NO' }}</h1>
    <h1 :class="{ pad : tie }" v-else>TIE!</h1>
    <p>
      <small v-if="!vueHasPassedReact && !tie" class="away">
        <b>1:17</b> now
      </small>
      <small v-else-if="vueHasPassedReact && !tie" class="ahead">...</small>
    </p>
    <div class="iframe">
      <iframe
        src="https://player.twitch.tv/?channel=karmikkoala"
        frameborder="0"
        allowfullscreen="true"
        scrolling="no"
      ></iframe>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import GithubCorner from "./components/GithubCorner";
import { VueIcon, ReactIcon, StarIcon } from "./components/icons";

const FUNCTIONS_ENDPOINT =
  "https://wt-13e53fa81a1f88b8fd161c9e57aeaac4-0.sandbox.auth0-extend.com/fetchGithubStars";

export default {
  name: "App",

  data() {
    return {
      repos: null,
      error: false,
      reloading: false
    };
  },

  components: {
    VueIcon,
    ReactIcon,
    StarIcon,
    GithubCorner
  },

  mounted() {
    if ("ontouchstart" in window || navigator.msMaxTouchPoints) {
      document.body.classList.remove("no-touch");
    }
  },

  computed: {},

  filters: {
    formatNumber(number) {
      return new Intl.NumberFormat().format(number);
    }
  },

  methods: {
    async fetchRepos() {
      try {
        const { data: res } = await axios.get(FUNCTIONS_ENDPOINT);
        if (res.errors && res.errors.length) {
          this.error = true;
          this.repos = null;
          // eslint-disable-next-line
          console.log(res.errors);
        } else {
          this.error = true;
          this.repos = res.data;
        }
      } catch (err) {
        // eslint-disable-next-line
        console.log(err);
      }
    },

    async reload() {
      if (this.reloading) return;
      this.reloading = true;
      await this.fetchRepos();
      setTimeout(() => {
        this.reloading = false;
      }, 900);
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
}

::selection {
  background: rgba(0, 0, 0, 0);
}
::-moz-selection {
  background: rgba(0, 0, 0, 0);
}

html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  text-align: center;
  color: #333;
  background: #efefef;
}

#app {
  width: 800px;
  border: 1px solid #dddddd;
  border-radius: 4px;
  background: #ffffff;
  box-shadow: 0 15px 35px rgba(50, 50, 93, 0.1), 0 5px 15px rgba(0, 0, 0, 0.07);
  overflow: hidden;
  position: relative;
}

@media (max-width: 768px) {
  #app {
    height: 100%;
  }
}

h1 {
  font-size: 100px;
  margin: 10px 0;
}

ul {
  margin: 0;
  padding: 0;
  display: flex;
}

li {
  list-style-type: none;
  width: 50%;
}

li a {
  display: flex;
  align-items: center;
  justify-content: center;
  border-top: 1px solid #dddddd;
  padding: 10px;
  text-decoration: none;
  color: #333;
}

.no-touch li:hover {
  background: #eeeeee;
}

li a > svg {
  display: block;
  width: 22px;
}

li a > * {
  margin: 5px;
}

li:last-of-type {
  border-left: 1px solid #dddddd;
}

h1.error {
  font-size: 2em;
}

h1.pad {
  margin-bottom: 30px;
}

p {
  padding: 0 1em;
}

.away,
.ahead {
  display: block;
  margin-bottom: 40px;
}

.reload {
  position: absolute;
  left: 50%;
  margin-left: -20px;
  bottom: 30px;
  background: #ffffff;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid #dddddd;
  border-radius: 50%;
}

.reload svg {
  transform-origin: center center;
}

.no-touch .reload:hover {
  cursor: pointer;
  background: #eeeeee;
}

.reloading {
  animation: rotate 1s infinite ease-in-out;
}

.iframe {
  position: relative;
  overflow: hidden;
  padding-top: 56.25%;
}

iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 0;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(-360deg);
  }
}
</style>
