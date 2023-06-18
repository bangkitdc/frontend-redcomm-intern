<template>
  <div class="flex-grow mx-16 max-md:mx-12 max-xs:mx-8 px-8 max-md:px-7 max-sm:px-6 max-xs:px-5 pt-4 pb-8 max-md:pb-5 border-x-2 border-black bg-1 relative">
    <div class="pb-[4.5rem] flex flex-col">
      <div>
        <SearchBar @search="searchComment"/>
      </div>
      <div class="flex-grow">
        <div class="grid grid-cols-1 gap-6 max-md:gap-5 max-sm:gap-4">
          <div v-for="c in visibleComments">
            <Card :comment="c"/>
          </div>
        </div>
        <div class="py-6" v-show="visibleComments.length === 0 && !isLoading">
          <p class="text-lg max-md:text-lg max-xs:text-base">
            Couldn't find result for
            <span class="font-medium">
              "{{ searchQuery }}"
            </span>
          </p>
          <p class="text-sm max-md:text-xs pt-1 max-md:pt-0.5">
            Try checking your spelling or use more general terms
          </p>
        </div>
        <div class="flex justify-center my-4 max-md:mb-3 max-sm:mb-0 max-xs:mb-8">
          <button @click="loadMore" v-if="!isAllLoaded && !isLoading" class="btn max-md:btn-md max-sm:btn-sm hover:bg-1-darken">Load More</button>
        </div>
      </div>
    </div>
    <div class="absolute bottom-0 right-0 left-0 p-8 max-md:p-7 max-sm:p-6 max-xs:p-5">
      <div v-if="visibleComments.length !== 0" class="flex items-center justify-between border-t-2 border-black pt-6 max-md:pt-5 max-sm:pt-4 w-full max-xs:flex-col">
        <div>
          <p class="text-base max-md:text-sm max-sm:text-xs max-xs:text-sm text-gray-700">
            Showing
            <span class="font-medium">{{ from }}</span>
            to
            <span class="font-medium">{{ to }}</span>
            of
            <span class="font-medium">{{ total }}</span>
            results
          </p>
        </div>
        <div class="max-xs:pt-3 max-xs:max-w-full">
          <nav class="isolate inline-flex -space-x-px shadow-sm" aria-label="Pagination">
            <button @click="goToPage(1)" :disabled="currentPage === 1" :class="{'hover:bg-3-darken': currentPage !== 1}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs bg-3">
              <i class="material-icons max-md:text-xl max-md:leading-6 max-sm:text-lg max-sm:leading-5">keyboard_double_arrow_left</i>
            </button>
            <button @click="goToPage(currentPage - 1)" :disabled="currentPage === 1" :class="{'hover:bg-1-darken': currentPage !== 1}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">
              <i class="material-icons max-md:text-xl max-md:leading-6 max-sm:text-lg max-sm:leading-5">chevron_left</i>
            </button>

            <template v-if="totalPages <= 5">
              <button v-for="pageNumber in Math.min(totalPages, 5)" :key="pageNumber" @click="goToPage(pageNumber)" :class="{'bg-1-darken': pageNumber === currentPage, 'hover:bg-1-darken': pageNumber !== currentPage}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">{{ pageNumber }}</button>
            </template>
            <template v-else>
              <template v-if="currentPage === 1"> 
                <button v-for="pageNumber in [currentPage, currentPage + 1, currentPage + 2]" :key="pageNumber" @click="goToPage(pageNumber)" :class="{'bg-1-darken': pageNumber === currentPage, 'hover:bg-1-darken': pageNumber !== currentPage}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">{{ pageNumber }}</button>
              </template>
              <template v-else-if="currentPage === totalPages">
                <button v-for="pageNumber in [totalPages - 2, totalPages - 1, totalPages]" :key="pageNumber" @click="goToPage(pageNumber)" :class="{'bg-1-darken': pageNumber === currentPage, 'hover:bg-1-darken': pageNumber !== currentPage}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">{{ pageNumber }}</button>
              </template>
              <template v-else>
                <button v-for="pageNumber in [currentPage - 1, currentPage, currentPage + 1]" :key="pageNumber" @click="goToPage(pageNumber)" :class="{'bg-1-darken': pageNumber === currentPage, 'hover:bg-1-darken': pageNumber !== currentPage}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">{{ pageNumber }}</button>
              </template>
            </template>
            
            <button @click="goToPage(currentPage + 1)" :disabled="currentPage === totalPages" :class="{'hover:bg-1-darken': currentPage !== totalPages}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs">
              <i class="material-icons max-md:text-xl max-md:leading-6 max-sm:text-lg max-sm:leading-5">chevron_right</i>
            </button>
            <button @click="goToPage(totalPages)" :disabled="currentPage === totalPages" :class="{'hover:bg-3-darken': currentPage != totalPages}" class="box-page max-md:box-page-md max-sm:box-page-sm max-xs:box-page-xs bg-3">
              <i class="material-icons max-md:text-xl max-md:leading-6 max-sm:text-lg max-sm:leading-5">keyboard_double_arrow_right</i>
            </button>
          </nav>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SearchBar from '../components/SearchBar.vue';

export default {
  components: {
    SearchBar,
  },  

  data() {
    return {
      comments: [],
      currentPage: 1,
      totalPages: 1,
      from: 1,
      to: 1,
      total: 0,
      isAllLoaded: false,
      itemsPerPage: 0,
      isSearch: false,
      searchQuery: '',
      isLoading: true,
    };
  },

  async mounted() {
    this.fetchComments();
  },

  computed: {
    visibleComments() {
      // Extract a subset of comments based on the number of items per page
      return this.comments.slice(0, this.itemsPerPage);
    },
  },

  methods: {
    goToPage(pageNumber) {
      this.currentPage = pageNumber;
      if (this.isSearch) {
        this.searchComment(this.searchQuery, pageNumber);
      } else {
        this.fetchComments();
      }
    },

    /*
      Load More function
    */

    loadMore() {
      this.itemsPerPage = 10;
      this.isAllLoaded = true;
    },

    async fetchComments() {
      try {
        this.isSearch = false;
        this.isLoading = true;

        const config = useRuntimeConfig();

        const response = await $fetch(config.public.API_URL + '/comments', {
          method: 'GET',
          params: { page: this.currentPage },
        });
        
        this.handleCommentsResponse(response);
      } catch (error) {
        console.error(error);
      } finally {
        this.isLoading = false;
      }
    },
    async searchComment(searchQuery, page = 1) {
      try {
        this.isLoading = true;
        if (this.isSearching) { // just to make sure call API 1 time
          return;
        }

        this.isSearching = true;
        this.isSearch = true;
        this.searchQuery = searchQuery;

        const config = useRuntimeConfig();

        const response = await $fetch(config.public.API_URL + '/comments/search', {
          method: 'GET',
          params: { query: searchQuery, page: page },
        });

        this.handleCommentsResponse(response);
      } catch (error) {
        console.error(error);
      } finally {
        this.isSearching = false;
        this.isLoading = false;
      }
    },
    handleCommentsResponse(response) {
      this.itemsPerPage = 5; // Number of items to display initially
      this.comments = response.data;

      if (this.comments.length > 5) {
        this.isAllLoaded = false;
      } else {
        this.isAllLoaded = true;
      }

      this.currentPage = response.meta.current_page;
      this.totalPages = response.meta.last_page;
      this.from = response.meta.from;
      this.to = response.meta.to;
      this.total = response.meta.total;
    },
  },
};
</script>

<style scoped>

</style>
