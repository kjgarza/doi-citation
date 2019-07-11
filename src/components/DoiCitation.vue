<template>
  <div class="citation">

   {{citation}}

   <!-- <button v-clipboard="citation"  class="fa fa-clipboard">
    Copy
  </button>

  <i v-clipboard="citation" class="fa fa-clipboard"></i> -->

  </div>
</template>

<script>
import axios from 'axios';


export default {
  name: 'DoiCitation',
  props: {
    doi: {
      type: String,
      required: true,
      validator: function (value) {
        return value.match(/^[A-Za-z0-9][-._;()/:A-Za-z0-9]+$/) !== -1
        }
      },
    citationStyle:{
      type: String,
      required: false,
      default: "apa",
      validator: function (value) {
        // The value must match one of these strings
        return ['apa', 'ieee'].indexOf(value) !== -1
      }
    },
    language:{
      type: String,
      required: false,
      default: "en-GB",
      validator: function (value) {
        // The value must match one of these strings
        return ['en-GB', 'fr-FR'].indexOf(value) !== -1
      }
    }
  },
  data: function(){
    return{
      citation: null
    }
  },    
  methods: {
    clipboardSuccessHandler ({ value, event }) {
      console.log('success', value)
    },

    clipboardErrorHandler ({ value, event }) {
      console.log('error', value)
    }
  },
  mounted () {
    axios
      .get('https://api.datacite.org/dois/text/x-bibliography/' + this.doi,
          {
        params: {
          style: this.citationStyle,
          language: this.language,
          email: "doi-citation-component"
        }
      }
       )
      .then(response => {
        this.citation = response.data
        // const vueContainer = document.createElement('script')
        // stat.appendChild(vueContainer);
        // vueContainer.appendChild(app.$el)
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
  }
}
</script>
<style scoped>
@import url('https://maxcdn.bootstrapcdn.com/font-awesome/4.6.1/css/font-awesome.min.css');

.citation {
  background-color: #eeeeee;
  margin-top: 30px;
  margin-left: auto;
  margin-right: auto;
  max-width: 800px;
  font-family: Helvetica, sans-serif;
  padding: 5pt;
}
</style>

