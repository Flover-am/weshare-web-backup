<script setup>
import { ref } from "vue";
import CourseCard from "../components/CourseCard.vue";
import storage from "../utils/LocalStorage";

// 课程信息数据获取
const showCourses = ref(storage.get("courses"));
//用户信息获取
var hasLogin = ref(storage.get("userID") !== null).value;
const userID = ref(!hasLogin ? " 未登录" : storage.get("userID"));

// 控制左侧边栏折叠
const collapse = ref(false);
let rem = window.getComputedStyle(document.documentElement)["fontSize"];
let windowWidth = window.innerWidth;
if (windowWidth < 66 * Number(rem.substring(0, rem.length - 2))) {
  collapse.value = true;
} else {
  collapse.value = false;
}

window.onresize = function () {
  rem = window.getComputedStyle(document.documentElement)["fontSize"];
  let windowWidth = window.innerWidth;
  if (windowWidth < 66 * Number(rem.substring(0, rem.length - 2))) {
    collapse.value = true;
  } else {
    collapse.value = false;
  }
};

// 筛选课程
function filterCourses(index) {
  console.log(index);
  let allCourses = storage.get("courses");

  if (index == "收藏") {
    showCourses.value = allCourses.filter((x) => x.isLiked == true);
  } else {
    //这里其实应该是筛选上传的
    showCourses.value = allCourses.filter((x) => x.isLiked == true);
  }

  if (showCourses.value.length == 0) {
    ElMessage({
      message: "抱歉，未找到此类资源",
      type: "warning",
    });
  }
}
</script>

<template>
  <div class="class-side-bar">
    <el-menu
      style="
        border-right-width: 0;
        background-color: transparent;
        --el-menu-hover-bg-color: transparent;
        --el-menu-bg-color: transparent;
      "
      :collapse="collapse"
      @select="filterCourses"
      unique-opened
    >
      <el-scrollbar style="height: 200px">
        <el-menu-item index="收藏"> 我的收藏 </el-menu-item>
        <el-menu-item index="上传"> 我的上传 </el-menu-item>
      </el-scrollbar>
    </el-menu>
  </div>
  <div class="content-container">
    <CourseCard :courses="showCourses"></CourseCard>
  </div>
</template>

<style scoped>
.content-container {
  z-index: 0;
  padding-left: var(--class-content-padding-left);
  width: var(--class-content-container-width);
  margin-left: 0;
  transition: all 0.5s;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  overflow: hidden;
}

.class-side-bar {
  z-index: 1;
  position: fixed;
  margin-top: 1rem;
  width: 12rem;
  left: 0;
  display: var(--side-bar-display);
}

:deep(.el-scrollbar__bar.is-horizontal) {
  height: 0 !important;
}
</style>
