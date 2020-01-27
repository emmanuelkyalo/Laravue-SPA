                  <template>
  <div>
    <h2>Laravue</h2>
    <form @submit.prevent="addArticle" class="mb-5" v-if="hide">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Title" v-model="article.title" />
      </div>
      <div class="form-group">
        <textarea class="form-control" placeholder="Body" v-model="article.body"></textarea>
      </div>
      <div>
        <button type="submit" class="btn btn-success btn-block">Save</button>
        <button @click="clearForm()" class="btn btn-danger btn-block">Clear Form</button>
      </div>
    </form>
    <div class="mx-1 my-1">
      <button @click="unhideForm()" class="btn btn-danger btn-block">{{ this.message }}</button>
    </div>

    <h3 class="mx-3 my-3 text-center">My Articles</h3>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
          <a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a>
        </li>

        <li class="page-item disabled">
          <a
            class="page-link text-dark"
            href="#"
          >Page {{ pagination.current_page }} of {{ pagination.last_page }}</a>
        </li>

        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
          <a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a>
        </li>
      </ul>
    </nav>






    <div class="row">
      <div class="col-lg-4 card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
        <h5>
          <img height="50" width="50" :src="image" />
          {{ article.title }}
        </h5>
        <p>{{ article.body }}</p>

        <hr />
        <button @click="editArticle(article)" class="btn btn-warning mb-2">Edit</button>
        <button @click="deleteArticle(article.id)" class="btn btn-danger">Delete</button>
      </div>
    </div>


  </div>
</template>

                  <script>
export default {
  data() {
    return {
      articles: [],
      images: [
        "https://cdn4.iconfinder.com/data/icons/seo-and-development/155/vector_257_04-01-128.png",
        "https://cdn4.iconfinder.com/data/icons/seo-and-development/155/vector_257_06-01-128.png",
        "https://cdn4.iconfinder.com/data/icons/seo-and-development/155/vector_257_04-01-512.png"
      ],
      article: {
        id: "",
        title: "",
        body: "",
        imageURL: ""
      },
      article_id: "",
      singleArticle: {
        id: "",
        title: "",
        body: "",
        imageURL: ""
      },
      pagination: {},
      edit: false,

       oneArticle: false,
      hide: false,
      message: "Show Form",
      image:
        "https://cdn4.iconfinder.com/data/icons/seo-and-development/155/vector_257_04-01-512.png"
    };
  },
  created() {
    this.fetchArticles();
  },
  methods: {
    fetchArticles(page_url) {
      let vm = this;
      vm.image = vm.images[Math.floor(Math.random() * vm.images.length)];

      page_url = page_url || "/api/articles";
      fetch(page_url)
        .then(res => res.json())
        .then(res => {
          this.articles = res.data;
          vm.makePagination(res.meta, res.links);
        })
        .catch(err => console.log(err));
    },
    makePagination(meta, links) {
      let pagination = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next_page_url: links.next,
        prev_page_url: links.prev
      };
      this.pagination = pagination;
    },
    showArticle(id) {
       this.oneArticle = true;
      fetch(`api/article/${id}`, {

      })
        .then(res => res.json())
        .then(res=>{
this.singleArticle = res.data
        }
        )
        .catch(err => console.log(err));

    },
    deleteArticle(id) {
      if (confirm("Are You Sure?")) {
        fetch(`api/article/${id}`, {
          method: "delete"
        })
          .then(res => res.json())
          .then(data => {
            alert("Article Removed");
            this.fetchArticles();
          })
          .catch(err => console.log(err));
      }
    },
    addArticle() {
      if (this.edit === false) {
        // Add
        fetch("api/article", {
          method: "post",
          body: JSON.stringify(this.article),
          headers: {
            "content-type": "application/json"
          }
        })
          .then(res => res.json())
          .then(data => {
            this.clearForm();
            alert("Article Added");
            this.fetchArticles();
          })
          .catch(err => console.log(err));
      } else {
        // Update
        fetch("api/article", {
          method: "put",
          body: JSON.stringify(this.article),
          headers: {
            "content-type": "application/json"
          }
        })
          .then(res => res.json())
          .then(data => {
            this.clearForm();
            alert("Article Updated");
            this.fetchArticles();
          })
          .catch(err => console.log(err));
      }
    },
    unhideForm() {
      this.hide = !this.hide;
      if (this.message === "Hide Form") {
        this.message = "Show Form";
      } else {
        this.message = "Hide Form";
      }
    },
    editArticle(article) {
      this.edit = true;
      this.article.id = article.id;
      this.article.article_id = article.id;
      this.article.title = article.title;
      this.article.body = article.body;
      this.hide = true;
      this.message = "Hide Form";
    },
    clearForm() {
      this.edit = false;
      this.article.id = null;
      this.article.article_id = null;
      this.article.title = "";
      this.article.body = "";
    }
  }
};
</script>