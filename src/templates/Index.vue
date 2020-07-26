<template>
  <component :is="type" class="index">
    <NavBar
      active="Index"
      :navItems="[
        { name: 'Home', component: 'Index', href: '/#/' },
        { name: 'Meet the Team!', href: 'http://localhost:6060/' },
      ]"
    />
    <Wrapper>
      <Heading>Who wrote this?</Heading>
      <Paragraph>
        <a>Who wrote this?</a> is an online tool for detecting which author(s) does your writing
        style adhere to. Simply type-in or paste your text in the search bar below and press submit!
      </Paragraph>
      <Input @change="gotInput($event)" />
      <Button @userInput="getOutput">Submit</Button>
      <div :key="render_it">
        <div v-for="(sim, author, index) in test_output" :key="index">
          <p class="result">{{ author }}: {{ sim }} %</p>
        </div>
      </div>
    </Wrapper>
  </component>
</template>

<script>
import Input from "../elements/Input"
import Button from "../elements/Button"
import axios from "axios"
import Wrapper from "../elements/Wrapper"
import Vue from "vue"
import ProgressBar from "vue-progressbar-component"

Vue.component("progress-bar", ProgressBar)

/**
 * Shows how to layout and structure a home page.
 */
export default {
  name: "Index",
  data() {
    return {
      test_input: null,
      show_result: false,
      test_output: {},
      render_it: 0,
    }
  },
  components: { Wrapper, Button, Input },
  status: "deprecated",
  release: "1.0.0",
  metaInfo: {
    title: "Who wrote this?",
    htmlAttrs: {
      lang: "en",
    },
  },
  props: {
    /**
     * The html element name used for the component.
     */
    type: {
      type: String,
      default: "div",
    },
  },

  methods: {
    gotInput(value) {
      this.test_input = value
    },

    async getOutput() {
      console.log("getOutput Called on " + this.test_input)
      if (this.test_input == null) {
        console.log("Empty input")
      } else {
        await this.getResultFromServer(this.test_input).then(result => {
          console.log(result)
          console.log(Object.keys(result))
          for (const key of Object.keys(result)) {
            this.test_output[key] = parseInt(parseFloat(result[key]) * 100)
          }
          this.render_it++
        })
      }
    },

    getResultFromServer(user_input) {
      const data = new FormData()
      data.append("user_input", user_input)

      return axios
        .post("http://localhost:5000/input", data, {
          headers: {
            "content-type": `multipart/form-data; boundary=${data._boundary}`,
          },
        })
        .then(
          response => {
            // success callback

            alert("Uploaded Successfully")
            return response.data
          },
          response => {
            // error callback
            alert("Something went wrong")
            return null
          }
        )
    },
  },
}
</script>

<style lang="scss" scoped>
// Design Tokens with local scope
$color-template-background: $color-rich-black;
$color-template-background-top: tint($color-template-background, 5%);
$color-template-background-bottom: shade($color-template-background, 5%);
$color-template-text: $color-white;
$color-template-link: $color-bleu-de-france;

.index {
  @include reset;
  @include inset-space($space-m);
  min-height: $space-xxl * 4;
  background: $color-template-background;
  background: linear-gradient(
    0deg,
    $color-template-background-bottom,
    $color-template-background-top 100%
  );
  text-align: center;
  position: relative;
  float: left;
  height: 100%;
  width: 100%;
  @media #{$media-query-l} {
    // This is how you’d use design tokens with media queries
  }

  .heading {
    color: $color-template-text;
  }

  .paragraph {
    color: $color-template-text;
  }

  .text-link {
    color: $color-template-link;
  }

  .wrapper {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    max-width: $space-xxl * 4.5;
    transform: translateX(-50%) translateY(-50%);
    position: absolute;
    left: 50%;
    top: 50%;
  }

  a {
    font-family: $font-text;
    color: $color-bleu-de-france;
    text-decoration: underline;
  }

  .result {
    color: $color-white;
  }
}
</style>

<docs>
    ```jsx
    <Index/>
    ```
</docs>
