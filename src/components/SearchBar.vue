<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { Search } from "@element-plus/icons-vue";
import { ElLoading } from "element-plus";
// 搜索内容
const content = ref("");

const router = useRouter();

function goToSearch() {
  const loading = ElLoading.service({
    text: "Loading",
  });
  setTimeout(() => {
    loading.close();
    if (content.value.length > 0) {
      router.push({ path: "/search", query: { content: content.value } });
    } else {
      router.push({ path: "/" });
    }
  }, 300);
}

const inputWidth = ref("6rem");
function focus() {
  inputWidth.value = "var(--search-input-width)";
}
function blur() {
  inputWidth.value = "6rem";
}

function searchEnterFun(e) {
  var keyCode = e.keyCode;
  if (keyCode == 13) {
    goToSearch();
  }
}
</script>

<template>
  <div
    style="
      display: flex;
      flex-direction: row;
      width: var(--search-bar-width);
      justify-content: flex-end;
    "
  >
    <el-input
      class="search-input"
      @focus="focus"
      @blur="blur"
      :style="{ width: inputWidth }"
      v-model="content"
      maxlength="15"
      placeholder="快来搜索课程或资源吧"
      size="large"
      clearable
      @keyup.enter="searchEnterFun"
    >
    </el-input>
    <el-button
      :icon="Search"
      style="
        margin: 0 0 0 1rem;
        --el-button-hover-bg-color: var(--color-main-darker);
        --el-button-hover-border-color: var(--color-main-darker);
        --el-button-active-bg-color: var(--color-main-darkest);
      "
      type="primary"
      color="var(--color-main)"
      size="large"
      @click="goToSearch"
      circle
    ></el-button>
  </div>
</template>

<style scoped>
.search-input {
  --el-input-border-radius: 50px;
  --el-input-focus-border-color: var(--color-main);
  transition: all 0.5s;
  --el-input-bg-color: transparent;
}
</style>
