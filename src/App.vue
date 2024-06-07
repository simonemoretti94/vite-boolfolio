<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      base_api_url: 'http://127.0.0.1:8000',
      projects_endpoint: '/api/projects',
      projects: '',
      search_text: '',
    }
  },
  methods: {
    callApi(url) {
      axios
        .get(url)
        .then(response => {
          console.log(response);
          this.projects = response.data.results;
        })
        .catch(error => {
          console.error(error);
        });
    },
    search() {
      const url = this.base_api_url + this.projects_endpoint + `?search=${this.search_text}`;
      console.log('search URL: ', url);
      this.callApi(url);
    },
    goTo(url) {
      console.log('goTo URL: ', url);
      this.callApi(url);
    }
  },
  mounted() {
    const url = this.base_api_url + this.projects_endpoint;
    console.log(url);
    this.callApi(url);

  }
}
</script>

<template>
  <section class="container">
    <h1 class="my-2">Projects:</h1>

    <div class="p-5 mb-4 bg-light rounded-3">
      <div class="container-fluid py-5">
        <h1 class="display-5 fw-bold">Blog</h1>
        <p class="col-md-8 fs-4">
          Read our amazing blog
        </p>

        <form @submit.prevent="search()" action="">
          <div class="input-group mb-3">
            <input type="search" class="form-control" placeholder="search..." v-model="search_text">
            <button class="btn btn-outline-secondary" type="submit">
              <i class="fas fa-search fa-lg fa-fw"></i>
            </button>
          </div>
        </form>


      </div>
    </div>
  </section>


  <section v-if="projects">
    <div class="container">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-lg-3 g-5">
        <div class="col" v-for="project in projects.data">
          <div class="card" style="height: 450px;">

            <template v-if="!project.cover_image.startsWith('https://')">
              <img :src="base_api_url + '/storage/' + project.cover_image" :alt="project.title" style="height: 80%;">
            </template>
            <template v-else>
              <img :src="project.cover_image" :alt="project.title" style="height: 65%;">
            </template>

            <div class="card-body" style="height: 25%;">
              <p><b>{{ project.title }}</b><br>
                {{ project.description }}
              </p>
            </div>

            <div class="card-footer" style="height: 10%;">

              <!-- Modal trigger button -->
              <button type="button" class="btn btn-primary btn-sm" data-bs-toggle="modal"
                :data-bs-target="`#project-${project.id}`">
                View
              </button>

              <div class="modal fade" :id="`project-${project.id}`" tabindex="-1" data-bs-backdrop="static"
                data-bs-keyboard="false" role="dialog" aria-labelledby="modalTitleId" aria-hidden="true">
                <div class="modal-dialog modal-dialog-scrollable modal-dialog-centered modal-md" role="document">
                  <div class="modal-content">
                    <div class="modal-header">
                      <h5 class="modal-title" :id="`modal-title-${project.id}`">
                        {{ project.title }}
                      </h5>
                      <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body" style="overflow-y: scroll;">

                      <img v-if="!project.cover_image.startsWith('https://')" class="img-fluid"
                        :src="base_api_url + '/storage/' + project.cover_image" :alt="project.title"
                        style="height: 80%;">
                      <img v-else :src="project.cover_image" class="img-fluid" :alt="project.title"
                        style="height: 80%;">
                      <p>
                        {{ project.description }}
                      </p>
                    </div>
                    <div class="modal-footer">
                      <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
                        Close
                      </button>
                      <!-- <button type="button" class="btn btn-primary">Save</button> -->
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <nav class="my-3" aria-label="Page navigation">
        <ul class="pagination    ">
          <li class="page-item" :class="{ 'd-none': !link.url, 'active': link.active }" v-for="link in projects.links">
            <button class="page-link" :href="link.url" type="button" @click="goTo(link.url)"><span
                v-html="link.label"></span></button>
          </li>
        </ul>
      </nav>
    </div>



  </section>

</template>

<style scoped>
div.card-body {
  overflow-y: scroll;
}
</style>
