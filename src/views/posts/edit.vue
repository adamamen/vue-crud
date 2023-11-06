<script setup>
//import ref
import { ref, onMounted } from "vue";

//import router
import { useRouter, useRoute } from 'vue-router';

//import api
import api from "/src";

//init router
const router = useRouter();

//init route
const route = useRoute();

//define state
const image = ref("");
const title = ref("");
const content = ref("");
const errors = ref([]);

//onMounted
onMounted(async () => {

  //fetch detail data post by ID
  await api.get(`/post/${route.params.id}`)
    .then(response => {
      //set response data to state
      title.value = response.data.data[0].title
      content.value = response.data.data[0].content
    });
})

//method for handle file changes
const handleFileChange = (e) => {
  //assign file to state
  image.value = e.target.files[0];
};

//method "updatePost"
const updatePost = async () => {

  //init formData
  let formData = new FormData();

  //assign state value to formData
  formData.append("image", image.value);
  formData.append("title", title.value);
  formData.append("content", content.value);
  formData.append("_method", "PUT")

  //store data with API
  await api.post(`/post/update/${route.params.id}`, formData)
    .then(() => {
      router.push({ path: "/posts" });
    })
    .catch((error) => {
      errors.value = error.response.data.data;
    });
};

const back = async () => {
  router.push({ path: "/posts" });
}
</script>

<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-12">
        <div class="card border-0 rounded shadow">
          <div class="card-body">
            <form @submit.prevent="updatePost()">
              <div class="mb-3">
                <label class="form-label fw-bold">Image</label>
                <input type="file" class="form-control" @change="handleFileChange($event)">
                <div v-if="errors.image" class="alert alert-danger mt-2">
                  <span>{{ errors.image[0] }}</span>
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label fw-bold">Title</label>
                <input type="text" class="form-control" v-model="title" placeholder="Title Post">
                <div v-if="errors.title" class="alert alert-danger mt-2">
                  <span>{{ errors.title[0] }}</span>
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label fw-bold">Content</label>
                <textarea class="form-control" v-model="content" rows="5" placeholder="Content Post"></textarea>
                <div v-if="errors.content" class="alert alert-danger mt-2">
                  <span>{{ errors.content[0] }}</span>
                </div>
              </div>
              <button type="submit" class="btn btn-md btn-success rounded-sm shadow border-0">Update</button>&nbsp;
              <button @click.prevent="back()" type="submit" class="btn btn-md btn-primary rounded-sm shadow border-0">Back</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>