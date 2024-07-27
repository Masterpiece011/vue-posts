<template>
  <div class="app container">
    <h1>Страница с постами</h1>
    <my-input
    v-model="searchQuery"
    placeholder="Поиск..."
    />
    <div class="app__btns">
      <my-button class="btn" @click="showDialog">Создать пост</my-button>
      <my-select 
      v-model="selectedSort"
      :options="sortOptions"
      />
    </div>
    
    <my-dialog v-model:show="dialogVisible">
      <post-form  
      @create="createPost"
    />
    </my-dialog>
    
    <post-list 
      :posts="sortedAndSearchedPosts"
      @remove="removePost"
      v-if="!isPostsLoading"
    />
    <div v-else>Идет загрузка...</div>
    <div ref="observer" class="observer">

    </div>
    <!-- <div class="page__wrapper">
      <div 
      v-for="pageNumber in totalPages" 
      :key="pageNumber"
      class="page"
      :class="{
        'current-page': page === pageNumber
      }"
      @click="changePage(pageNumber)"
      >
        {{ pageNumber }}
      </div>
    </div> -->
  </div>
</template>

<script>
  import PostForm from '@/components/PostForm.vue';
  import PostList from '@/components/PostList.vue';
  import MyButton from '@/components/UI/MyButton.vue';
  import MyInput from './components/UI/MyInput.vue';
  import axios from 'axios';

  export default {
    components: {
      PostList, 
      PostForm, 
      MyButton, 
      MyInput
    },
    data() {
        return {
            posts: [],
            dialogVisible: false,
            isPostsLoading: false,
            selectedSort: '',
            searchQuery: '',
            page: 1,
            limit: 10,
            totalPages: 0,
            sortOptions: [
              {value: 'title', name: 'По названию'},
              {value: 'body', name: 'По содержимому'},
            ]
        }
    },
    methods: {
      createPost(post) {
        this.posts.push(post)
        this.dialogVisible = false
      },
      removePost(post) {
        this.posts = this.posts.filter(p => p.id !== post.id)
        console.log(post);
      },
      showDialog() {
        this.dialogVisible = true
      },
      async fetchPosts() {
        try {
            this.isPostsLoading = true
            const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
              params: {
                _page: this.page,
                _limit: this.limit
              }
            })
            this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
            this.posts = response.data
        } catch (error) {
          console.log('Error fetching posts: ', error);
        } finally {
          this.isPostsLoading = false
        }
      },
      async loadMorePosts() {
        try {
          this.page += 1
            const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
              params: {
                _page: this.page,
                _limit: this.limit
              }
            })
            this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
            this.posts = [...this.posts, ...response.data]
        } catch (error) {
          console.log('Error fetching posts: ', error);
        }
      },
      // changePage(pageNumber) {
      //   this.page = pageNumber
      //   this.fetchPosts()
      // }
    },
    mounted() {
      this.fetchPosts()

      const options = {
        rootMargin: '0px',
        threshold: 1.0
      }

      const callback = (entries, observer) => {
        if (entries[0].isIntersecting && this.page < this.totalPages) {
          console.log(observer);
          this.loadMorePosts()
        }
      }

      const observer = new IntersectionObserver(callback, options)

      observer.observe(this.$refs.observer)
    },
    computed: {
      sortedPosts() {
        return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
      },
      sortedAndSearchedPosts() {
        return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
      }
    },
    watch: { 
      
    }
  }
</script>


<style>
*{ 
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  padding: 20px;
}

.container {
  margin: 0 auto;
  max-width: 1140px;
}

.app__btns {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 15px 0;
}

.page__wrapper {
  display: flex;
  flex-direction: row;
  margin-top: 15px;
}

.page {
  border: 1px solid #cdcdcd;
  padding: 10px 12px;
  border-radius: 10px;
  cursor: pointer;
  margin-right: 15px;
}

.current-page {
  background-color: #cdcdcd;
}

.observer {
  height: 30px;
  background: transparent;
}

</style>