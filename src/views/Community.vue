<template>
  <div class="content-container">
    <CommunityPart :posts="posts"></CommunityPart>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";
import CommunityPart from "../components/CommunityPart.vue";
import { POST_URL } from "../global/url.js";
const posts = ref([]);
onMounted(() => {
  axios.get(POST_URL + "/0").then((response) => {
    posts.value = response.data.map((post) => {
      return {
        ...post, // 保留原有属性
        showAllComments: false,
        newCommentForm: "",
        // 添加新属性
      };
    });
    console.log(posts.value);
  });
});
</script>
<style scoped>
.content-container {
  z-index: 0;
  overflow: hidden;
  width: var(--home-content-container-width);
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  height: var(--home-content-container-height);
}
</style>
