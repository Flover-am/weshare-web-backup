<script setup>
import { useRouter } from "vue-router";

const props = defineProps({
  // 展示课程信息列表
  courses: {
    type: Array,
    default: [],
  },
});

const router = useRouter();

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
</script>

<template>
  <el-card
    v-for="item in courses"
    :key="item.courseid"
    class="course-card"
    style="--el-card-padding: 0"
  >
    <template #header>
      <img
        :src="'data:image/png;base64,' + item.picture"
        @click="goToCourse(item)"
        style="width: 14rem; height: 20rem; object-fit: cover"
      />
      <div class="card-header">
        <div class="card-header-name" @click="goToCourse(item)">
          {{ item.coursename }}
          <img
            src="../assets/img/shoucang.png"
            style="float: right"
            id="shoucang"
            alt="picture"
          />
        </div>

        <div class="card-header-tags">
          <!-- <CourseTag style="margin: 0 0.5rem 0 0;" tag="课程" color="rgb(126, 125, 187)" /> -->
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
</template>

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
.course-card {
  width: 14rem;
  box-shadow: 0 1px 3px hsl(0deg 0% 7% / 10%);
  border-radius: 8px;
  margin: 0 1rem 2rem 1rem;
  box-sizing: border-box;
  transition: all 0.3s;
  animation-name: PopUp;
  animation-duration: 0.5s;
}

.course-card:hover {
  box-shadow: var(--el-box-shadow);
}

.card-header {
  font-size: large;
  font-weight: bolder;
  padding: 0.5rem 1rem;
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
