<template>
  <div class="flex flex-col items-center justify-center w-screen h-screen">
    <form @submit.prevent>
      <input class=" border-4 border-slate-900 w-96 h-10 text-2xl pl-3 focus-visible:outline-none mb-14" 
      type="text" 
      placeholder="Репозиторий Github..."
      ref="search"
      v-model="input">
    </form>
    <div class="smart-grid w-4/5">
      <div class="info mb-7 flex flex-col text-lg" 
      v-for="item of this.restData" 
      :key="item.id">
        <a class="text-blue-400 text-2xl" :href="item.owner.html_url">{{item.owner.login.toUpperCase()}}</a>
        <a class="text-blue-400" :href="item.html_url">{{item.name}}</a>
        <div>{{item.description}}</div>
        <div>{{item.language}}</div>
        <div>&bigstar;{{item.stargazers_count}}&bigstar;</div>
      </div>
    </div>
  </div>
</template>

<script>
import './tailwind.css'
import { Octokit } from '@octokit/core'
export default {
  name: 'App',
  data() {
    return {
      input: '',
      restData: {},
    }
  },
  methods: {
    search() {
      if (this.input) {
        (async() => {
          // { auth: `auth_key` }
          const octokit = new Octokit();
          const response = await octokit.request("GET /search/repositories?q={input}", {
            input: this.input,
            per_page: 8,
          });
          this.restData = response.data.items
          console.log(response)
        })()
      }
    },

    updateURL() {
      const url = new URL(location)
      if (this.input) {
        url.searchParams.set('q', `${this.input}`)
      } else {
        url.searchParams.delete('q')
      }
      history.pushState({}, '', url)
    },

    setInput() {
      if (location.search) {
        const url = new URL(location)
        const value = url.searchParams.get('q')
        this.input = value
      }
    }
  },
  computed: {

  },
  watch: {
    input() {
      this.search()
      this.updateURL()
    }
  },
  created() {
    this.setInput()
  },
  mounted() {
    this.$refs.search.focus();
  }
}
</script>

<style>

</style>