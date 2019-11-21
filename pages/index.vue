<template>
  <section>
    <div>
      <pre v-html="searchKey"></pre>
      <input class="big-input" type="text" v-model="searchKey">
    </div>
    <div class="d-flex">
      <div>
        <h2>Old APi</h2>
        <json-viewer :value="filteredOld" :expand-depth="5" copyable boxed sort></json-viewer>
      </div>
      <div>
        <h2>New APi</h2>
        <json-viewer :value="filteredNew" :expand-depth="5" copyable boxed sort></json-viewer>
      </div>
    </div>
  </section>
</template>

<script>
import axios from "axios";
import { get } from "lodash";
import Logo from "~/components/Logo.vue";
import IconLink from "~/components/IconLink.vue";
import 'vue-json-viewer/style.css'

const old_game_id = 570164;
const new_game_id = 607275;

const getGame = id =>
  axios.get(`https://statsapi.mlb.com/api/v1/game/${id}/feed/live`);
export default {
  components: {
    Logo,
    IconLink
  },

  watch: {
    searchKey(value) {
      this.$router.push({ path: this.$route.path, query: { search: value } });
    }
  },
  computed: {
    filteredOld() {
      if (!this.searchKey) {
        return this.oldAPI;
      }

      return get(this.oldAPI, this.searchKey) || {};
    },
    filteredNew() {
      if (!this.searchKey) {
        return this.newAPI;
      }

      return get(this.newAPI, this.searchKey) || {};
    }
  },

  async asyncData({ query }) {
    return {
      searchKey: query.search,
      oldAPI: await getGame(old_game_id).then(({ data }) => data),
      newAPI: await getGame(new_game_id).then(({ data }) => data)
    };
  }
};
</script>

<style scoped>
.big-input {
  width: 100%;
  font-size: 3em;
  text-align: center;
}

.d-flex {
  width: 100vw;
  display: flex;
}

.d-flex div {
  width: 50%;
  text-align: left;
  overflow: hidden;
}
</style>
