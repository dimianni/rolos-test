<script>
// import { getPosts } from '@/api/api';
// import { onBeforeMount, ref } from 'vue';

// const posts = ref();

// onBeforeMount(() => loadPosts());

// async function loadPosts() {
//   posts.value = (await getPosts()).posts;
// }

export default {
  name: 'Posts',
  data() {
    return {
      posts: [],
      page: 1,
      perPage: 5,
      pages: [],
    };
  },
  methods: {
    setPages() {
      // Calculating the number of pages
      let numberOfPages = Math.ceil(this.posts.length / this.perPage);

      // Pushing page indexes into an array
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    // Slicing posts into different pages
    paginate(posts) {
      let page = this.page;
      let perPage = this.perPage;
      let from = page * perPage - perPage;
      let to = page * perPage;
      return posts.slice(from, to);
    },
  },
  computed: {
    displayedPosts() {
      return this.paginate(this.posts);
    },
  },
  watch: {
    posts() {
      this.setPages();
    },
  },
  // Fetching posts from API
  mounted() {
    fetch(`https://dummyjson.com/posts`)
      .then((res) => res.json())
      .then((data) => {
        console.log(data);
        this.posts = data.posts;
      })
      .catch((err) => console.log(err.message));
  },
};
</script>


<template>
  <h1>Posts list</h1>

  <main>
    <article>
      <RouterLink
        class="post"
        v-for="post in displayedPosts"
        :to="{ name: 'post', params: { postId: post.id } }"
        :key="post.id"
      >
        <div class="post__id">#{{ post.id }}</div>
        <div class="post__title">{{ post.title }}</div>
        <div>{{ post.body }}</div>
      </RouterLink>
    </article>
  </main>

  <nav>
    <ul class="pagination">
      <li class="page-item">
        <!-- Display "Previous" button if it's not first page. Decrement page number on click. -->
        <button type="button" class="page-link" v-if="page != 1" @click="page--">Previous</button>
      </li>
      <li class="page-item">
         <!-- Display list of all available pages. -->
        <button
          type="button"
          v-for="pageNumber in pages.slice(page - 1, page + 5)"
          @click="page = pageNumber"
          :key="pageNumber"
          :class="pageNumber == page ? 'page-link page-current' : 'page-link'"
        >
          {{ pageNumber }}
        </button>
      </li>
      <li class="page-item">
        <!-- Display "Next" button if there is next page. Increment page number on click. -->
        <button type="button" @click="page++" v-if="page < pages.length" class="page-link">
          Next
        </button>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
main {
  background-color: #fff;
}

article{
  padding: 10px;
}
.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #fff;
}
.page-link {
  padding: 10px;
  margin: 10px;
}

.page-current {
  box-shadow: inset 0 0 0 1px;
  color: #548eaa;
  border-radius: 3px;
}

button {
  border: none;
  cursor: pointer;
}

.post {
  display: grid;
  grid-template-columns: min-content 1fr;
  grid-template-rows: repeat(2, auto);
  gap: 8px;

  & + & {
    margin-top: 12px;
    padding-top: 12px;
    border-top: 1px solid #ccc;
  }

  &__id {
    grid-row: -1/1;
  }

  &__title {
    font-weight: 700;
  }
}
</style>
