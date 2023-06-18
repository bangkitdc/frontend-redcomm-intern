<template>
  <div class="pb-4">
    <form @submit.prevent="search" class="flex justify-end">
      <div class="relative max-xs:w-full">
        <input v-model="searchQuery" @input="handleInputChange(false)" class="border-2 border-black bg-transparent h-10 max-md:h-9 px-3 pr-10 max-md:pr-8 text-sm focus:outline-none placeholder:text-black hover:bg-1-darken max-xs:w-full" placeholder="Search">
        <button type="submit" class="absolute right-0 top-0 mt-1 pt-1 max-md:pt-0.5 h-8 w-8 max-md:h-7 max-md:w-7 mx-2 max-md:mx-1 hover:bg-1-darken rounded-full">
          <i class="material-icons">search</i>
        </button>
        <button @click="handleInputChange(true)" v-if="searchQuery" class="absolute right-7 top-0 mt-1 pt-1 max-md:pt-0.5 h-8 w-8 max-md:h-7 max-md:w-7 mx-2 max-md:mx-1 hover:bg-1-darken rounded-full">
          <i class="material-icons">clear</i>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
  // Using lodash library
  import { debounce } from "lodash";

  export default {
    data() {
      return {
        searchQuery: this.$route.query.searchQuery,
        debounceQuery: null,
      };
    },
    created() {
      if (this.searchQuery != null) {
        this.performSearch()
      }
    },
    watch: {
      '$route.query.searchQuery': {
        handler: function(newVal, oldVal) {

          // To prevent multiple API calls, check if the same as value before
          if (newVal != oldVal) {
            this.searchQuery = this.$route.query.searchQuery
            this.performSearch()
          }
        },
        immediate: true 
      }
    },
    methods: {
      /*
        Separate button and actual call to prevent
        2x or multiple API calls at a time.
      */

      search() {
        // this is call when user click search Button
        this.$router.push({query: { 'searchQuery' : this.searchQuery}})
      },
      performSearch() {
        this.$emit('search', this.searchQuery);
      },
      handleInputChange(param) {
        if (param) {
          this.searchQuery = '';
        }

        /*
          Debouncing search,
          create responsive search,
          preventing useless API calls
        */

        // Debounce the input change to delay API call
        if (!this.debounceQuery) {
          this.debounceQuery = debounce(() => {
            this.performSearch();
          }, 500); // Adjust the debounce delay as per your requirements
        }
        this.debounceQuery();
      },
    },
  };
</script>

<style scoped>

</style>