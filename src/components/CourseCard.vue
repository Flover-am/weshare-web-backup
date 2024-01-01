<template>
  <el-col>
    <div class="course-cards">
      <el-card
        v-for="item in currentCourses"
        :key="item.courseid"
        class="course-card"
        style="--el-card-padding: 0"
      >
        <template #header>
          <img
            :src="item.pictureUrl"
            @click="goToCourse(item)"
            style="width: 16rem; height: 20rem; object-fit: cover"
          />
          <div class="card-header">
            <div class="card-header-name" @click="goToCourse(item)">
              {{ item.coursename }}
            </div>
            <div @click="addOrDelete(item)">
              <img
                src="../assets/img/unstar.png"
                v-if="!item.isLiked"
                style="float: right"
                id="shoucang"
              />

              <img
                src="../assets/img/star.png"
                v-if="item.isLiked"
                style="float: right"
                id="shoucang"
              />
            </div>

            <div class="card-header-tags">
              <CourseTag
                style="margin: 0 0.5rem 0 0"
                :tag="item.department"
                color="rgb(119, 127, 79)"
              />
              <CourseTag
                style="margin: 0 0.5rem 0 0"
                :tag="item.studytime"
                color="rgb(79, 49, 45)"
              />
            </div>
          </div>
        </template>

        <p class="card-intro" @click="goToCourse(item)" id="card-int">
          {{ item.introduction }}
        </p>
      </el-card>
    </div>
    <div class="pagination-container">
      <el-pagination
        v-if="courses.length > pageSize"
        :current-page="currentPage"
        :page-size="1"
        :total="Math.ceil(courses.length / pageSize)"
        @current-change="handlePageChange"
      />
    </div>
  </el-col>
</template>

<script setup>
import { ref, onActivated, onMounted, computed } from "vue";
import { useRouter } from "vue-router";
import storage from "../utils/LocalStorage";
import axios from "axios";
import URL from "../global/url";
const props = defineProps({
  courses: {
    type: Array,
    default: [],
    length: 0,
  },
});
console.log(props.courses);
const router = useRouter();
const currentPage = ref(1);
const pageSize = 8; // 每页显示的数量

const currentCourses = computed(() => {
  const startIndex = (currentPage.value - 1) * pageSize;
  const endIndex = startIndex + pageSize;
  if (props.courses != null) return props.courses.slice(startIndex, endIndex);
});

// 跳转课程详情页
function goToCourse(item) {
  router.push({
    path: "/detail",
    query: {
      courses: item.coursename,
      itro: item.introduction,
      teacher: item.teachername,
      during: item.studytime,
      picture: item.picture,
      id: item.id,
      viewtime: item.viewtimes,
    },
  });
}
// 处理分页变化
function handlePageChange(page) {
  currentPage.value = page;
}
function addOrDelete(item) {
  if (storage.get("userID") == null) {
    router.push("/login");
    return;
  }
  const username = storage.get("userID");
  const courses = storage.get("courses");
  const index = courses.findIndex((element) => item.id == element.id);
  if (item.isLiked == false) {
    item.isLiked = true;
    courses[index].isLiked = true;
    axios
      .get(URL.addToLike + username + "/" + item.id)
      .then(function (resp) {});
  } else {
    item.isLiked = false;
    courses[index].isLiked = false;
    axios
      .get(URL.removeFromLike + username + "/" + item.id)
      .then(function (resp) {});
  }
  storage.set("courses", courses, 6000000);
}
</script>
<style scoped>
@keyframes PopUp {
  from {
    scale: 0.6;
  }

  to {
    scale: 1;
  }
}

#shoucang {
  width: 1.5rem;
  height: 1.5rem;
  float: True;
  margin-top: 5px;
}
.course-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(14rem, 1fr));
  gap: 1rem;
}

.course-card {
  box-shadow: 0 1px 3px hsl(0deg 0% 7% / 10%);
  border-radius: 8px;
  box-sizing: border-box;
  transition: all 0.3s;
  animation-name: PopUp;
  animation-duration: 0.5s;
}

.course-card:hover {
  box-shadow: var(--el-box-shadow);
}
.pagination-container {
  margin-top: 1rem; /* 为确保翻页组件与卡片之间有一定间距 */
}
.card-header {
  font-size: large;
  font-weight: bolder;
  padding: 0.5rem 1rem;
}
.header-img {
  width: 16rem;
  height: 20rem;
  object-fit: cover; /* 保持图片比例不变，同时填充整个容器 */
}
.card-header-name {
  font-weight: bolder;
  cursor: pointer;
  transition: all 0.5s;
}

.card-header-name:hover {
  color: var(--color-main);
}

.card-header-tags {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-top: 6px;
}

.card-intro {
  padding: 0.5rem 1rem 1rem 1rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

#card-int:hover {
  cursor: pointer;
  transition: all 0.5s;
  color: var(--color-main);
}
img {
  transition: all 0.5s;
  cursor: pointer;
  width: 100%;
}
img:hover {
  transform: scale(1.1);
}
</style>
