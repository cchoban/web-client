<template>
  <div>
    <div class="section twentyfivepx column">
      <nuxt-link
        to="packages"
        class="header-text bold removelink"
      >
        <h2 class="left-floated">
          Editor Picks
        </h2>
        <label class="view-all unbold right-floated">
          View all
          <i class="angle right icon" />
        </label>
      </nuxt-link>
      <div class="inner-section">
        <div class="ui segments panel">
          <div class="ui segment panel-header">
            <p class="bold">
              Most Popular Softwares
            </p>
          </div>
          <div class="ui secondary segment panel-content">
            <div v-if="loading">
              <div class="ui attached segment loading">
                <br>
              </div>
            </div>
            <div
              v-for="pack in pickedPopulars"
              class="ui attached segment listings"
              @click="showPage(pack.packageName, pack.id)"
            >
              <div class="topla">
                <span>
                  <img
                    class="ui avatar image remove-circle"
                    :src="$store.state.api_urls.home+pack.server.icon"
                    alt=""
                  >
                </span>
                <span class="text">
                  {{ pack.packageArgs.softwareName }}
                </span>
                <span class="right-floated day">
                  <timeago :datetime="pack.updated_at" />
                </span>
              </div>
            </div>
          </div>
          <div class="ui panel-footer column">
            <nuxt-link
              to="/popular"
              class="removelink loadMoreBtn"
            >
              <i
                class="angle down icon light"
                style="font-size:20px;margin:0 auto"
              />
            </nuxt-link>
          </div>
        </div>
      </div>

      <div class="inner-section">
        <div class="ui segments panel">
          <div class="ui segment panel-header">
            <p class="bold">
              Other Softwares
            </p>
          </div>
          <div class="ui secondary segment panel-content">
            <div v-if="loading">
              <div class="ui attached segment loading">
                <br>
              </div>
            </div>
            <div
              v-for="pack in pickedPackages"
              class="ui attached segment listings"
              @click="showPage(pack.packageName, pack.id)"
            >
              <div class="topla">
                <span>
                  <img
                    class="ui avatar image remove-circle"
                    :src="$store.state.api_urls.home+pack.server.icon"
                    alt=""
                  >
                </span>
                <span class="text">
                  {{ pack.packageArgs.softwareName }}
                </span>
                <span class="right-floated day">
                  <timeago :datetime="pack.updated_at" />
                </span>
              </div>
            </div>
          </div>
          <div class="ui panel-footer column">
            <nuxt-link
              to="/packages"
              class="removelink loadMoreBtn"
            >
              <i
                class="angle down icon light"
                style="font-size:20px;margin:0 auto"
              />
            </nuxt-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loading: true,
      pickedPackages: {},
      pickedPopulars: {}
    }
  },
  mounted() {
    this.getPicked()
    this.getPopularPicked()
  },
  methods: {
    showPage(packageName, packageId) {
      this.$router.push(`/${packageName}`)
    },
    category_url(category_name) {
      const slugged = this.$options.filters.slugify(category_name)
      return `/packages/category/${slugged}`
    },
    getPicked() {
      const url = `${this.$store.state.api_urls.packages}?showcase=true`
      this.$axios
        .get(url)
        .then((response) => {
          this.pickedPackages = response.data.results.slice(0, 5)
          this.loading = false
        })
        .catch((err) => {
          console.log(err)
        })
    },
    getPopularPicked() {
      const url = `${this.$store.state.api_urls.packages}?showcase=true&ordering=-download_count`
      this.$axios
        .get(url)
        .then((response) => {
          this.pickedPopulars = response.data.results.slice(0, 5)
          this.loading = false
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

<style>
</style>
