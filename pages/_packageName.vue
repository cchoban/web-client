<template>
  <div class="ui container grid">
    <div class="ui segment fifteen column row">
      <div v-if="pack">
        <div class="ui medium three wide image column">
          <img :src="$store.state.api_urls.home+pack.server.icon" alt>
        </div>

        <div class="ui content twelve wide column right floated">
          <label class="ui green label">Download count: {{ pack.download_count }}</label>
          <label class="ui yellow label">View count: {{ pack.view_count }}</label>
          <label class="ui gray label">Last update:
            <timeago :datetime="pack.updated_at"/>
          </label>
          <label class="ui blue label">Submitted at:
            <timeago :datetime="pack.created_at"/>
          </label>

          <div class="ui segments">
            <div class="ui segment">
              <p>Software name: {{ pack.packageName }}</p>
            </div>
            <div class="ui segment">
              <p>Description: {{ pack.packageArgs.description }}</p>
            </div>
            <div class="ui segment">
              <p>Version: {{ pack.version }}</p>
            </div>
            <div class="ui segment">
              <p>Published by: {{ pack.user_name }}</p>
            </div>
            <div class="ui segment" v-if="pack.category_name">
              <p>
                Category:
                <a :href="category_url(pack.category_name)">{{ pack.category_name }}</a>
              </p>
            </div>
            <div
              class="ui segment"
              v-if="pack.packageArgs.dependencies && pack.packageArgs.dependencies.lenght > 0"
            >
              <ul>
                <li v-for="dep in pack.packageArgs.dependencies">{{ dep }}</li>
              </ul>
            </div>
            <div class="ui segment">
              <p>
                <CommandLine :packagename="pack.packageName"/>
              </p>
            </div>
          </div>
        </div>
      </div>

      <div class="middle" v-else>
        <div class="ui teal message">{{ $route.params.packageName }} was not found on our servers.</div>
      </div>

      <div class="ui twelve wide column left floated">
        <button class="ui positive button" @click.prevent="showIcerik('install')">Insallation Script</button>
        <button
          class="ui danger button"
          @click.prevent="showIcerik('uninstall')"
        >Uninstallation Script</button>

        <div v-if="active == 'install'">
          <pre v-highlightjs>
            <code class="json">
{{ pack.packageArgs }}
            </code>
            </pre>
        </div>

        <div v-if="active == 'uninstall'">
          <pre v-highlightjs>
              <code class="json">
{{ pack.packageUninstallArgs }}
              </code>
            </pre>
        </div>
      </div>
    </div>
    <div class="ui segment fifteen column row">
      <vue-disqus :shortname="$store.state.disqus_shortname" :url="url" :identifier="url"/>
    </div>
  </div>
</template>

<script>
import CommandLine from "@/components/SinglePage/installCommand.vue";
import axios from "axios";
const https = require("https");

export default {
  components: {
    CommandLine
  },
  head() {
    return {
      title: this.title
    };
  },
  data() {
    return {
      pack: [],
      packagename: "",
      loading: true,
      isPage: false,
      active: false,
      url: "",
      title: `${this.$capitalize(this.$route.params.packageName)} | Choban `
    };
  },
  async asyncData({ store, params }) {
    //FIXME: Dangerous!
    const config = {
      httpsAgent: new https.Agent({ rejectUnauthorized: false })
    };
    let { data } = await axios.get(
      `${store.state.api_urls.packages}/?search=${params.packageName}`,
      config
    );

    return { pack: data.results[0] };
  },
  methods: {
    showIcerik(todo) {
      if (this.active == todo) {
        this.active = false;
        return true;
      }

      this.active = todo;
    },
    category_url(category_name) {
      return `/packages/category/${this.$options.filters.slugify(
        category_name
      )}`;
    }
  }
};
</script>

<style scoped>
.content {
  width: 59%;
  float: right;
  margin-bottom: 50px !important;
}

#disqus_thread {
  width: 100%;
  padding: 17px;
}

.middle {
  margin: 0 auto;
}
</style>