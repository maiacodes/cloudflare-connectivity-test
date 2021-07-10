<template>
  <div class="main-content">
    <div class="container has-text-centered">
      <h1 class="title">Cloudflare Datacenter Test by Maia</h1>
      <h5 class="subtitle is-5">Note: all tests run at once, so this is effectively a <a href="https://medium.com/@datapath_io/what-is-acceptable-jitter-7e93c1e68f9b">jitter test</a>.</h5>
      <p class="subtitle">Test your website:</p>
      <form @submit.prevent="loadTestHostname(testHostname)">
        <div class="field has-addons has-addons-centered">
          <div class="control">
            <input class="input" type="text" placeholder="example.com" v-model="testHostname" />
          </div>
          <div class="control">
            <a class="button is-info">GO</a>
          </div>
        </div>
      </form>

      <div class="domain-item" v-if="testHostname" :key="testHostname">
        <domain-item :hostname="testHostname" />
      </div>
      <hr />
    </div>
    <hr class="top-hr" />
    <div class="columns has-text-centered" v-if="preloaded">
      <div class="column">
        <p class="title">FREE</p>
        <div class="domain-item" v-for="(site, index) in free" :key="index">
          <domain-item :hostname="site" />
          <hr />
        </div>
      </div>
      <div class="column list-domains">
        <p class="title">PRO</p>
        <div class="domain-item" v-for="(site, index) in pro" :key="index">
          <domain-item :hostname="site" />
          <hr />
        </div>
      </div>
      <div class="column list-domains">
        <p class="title">BUSINESS</p>
        <div class="domain-item" v-for="(site, index) in business" :key="index">
          <domain-item :hostname="site" />
          <hr />
        </div>
      </div>
      <div class="column list-domains">
        <p class="title">ENTERPRISE</p>
        <div class="domain-item" v-for="(site, index) in enterprise" :key="index">
          <domain-item :hostname="site" />
          <hr />
        </div>
      </div>
    </div>
    <section class="hero">
      <div class="hero-body">
        <div class="container">
          <h1 class="title"></h1>
          <h2 class="subtitle">Note: the plan these websites are on is an educated guess.</h2>
          <p>
            Pro websites were taken from the
            <a
              href="https://developers.cloudflare.com/sponsorships/"
            >Open Source sponsorships page</a>, Business from the
            <a href="https://www.cloudflare.com/galileo/">Project Galileo page</a>, and Enterprise from the
            <a
              href="https://www.cloudflare.com/case-studies/"
            >Case Studies page</a>.
          </p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { Options, Vue } from 'vue-class-component';

import Axios from "axios";
import DomainItem from "./DomainItem";
@Options({
  components: {
    DomainItem
  }
})
export default class Main extends Vue {
  testHostname = "";
  preloaded = false;
  iata = [];
  free = [
    "maia.codes",
    "honeybankstudios.com"
  ];
  pro = [
    "tweetshift.com",
    "vle.mathswatch.co.uk",
  ];
  business = [
    "novecore.com",
  ];
  enterprise = [
    "cloudflare.com",
    "discord.com",
    "medium.com",
    "www.zendesk.com",
    "www.digitalocean.com",
    "quizlet.com",
    "i.gyazo.com"
  ];
  mounted() {
    this.preloadAirports().then(() => {
      this.preloaded = true;
    });
    // load query string hostname
    if (location.hash) {
      this.testHostname = location.hash.replace("#", "");
      this.loadTestHostname(this.testHostname);
      this.$forceUpdate();
    }
  }
  loadTestHostname() {
    this.$forceUpdate();
  }
  getAirport(code) {
    return this.iata[code];
  }
  preloadAirports() {
    return Axios.get("/iata.json").then(response => {
      this.iata = response.data;
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.domain-item {
  margin-top: 2rem;
  margin-bottom: 2rem;
}
.top-hr {
  position: relative;
  bottom: -5rem;
}
.main-content {
  padding: 1.5rem;
  line-height: 1rem;
}
</style>
