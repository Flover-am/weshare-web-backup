<script setup>
import NavigationBar from "./components/NavigationBar.vue";

import BgImg from "./assets/img/home_pic.jpg";
import BgImgDark from "./assets/img/home_pic_dark.jpg";
import WeShare from "./assets/WESHARE.svg";
import { useRoute } from "vue-router";
import { watch } from "vue";
const route = useRoute();
var isDark = true;
function closeMain(val) {
  isDark = val;
  Console.log(val);
}
component: NavigationBar;

watch(route, () => {
  let container = document.getElementById("container");
  let menu = document.getElementById("menu");
  let header = document.getElementById("header");
  let navContainer = document.getElementById("nav-container");
  if (route.path === "/") {
    container.classList.add("bg-img");
    container.style.setProperty("background-image", "url(" + isDark + ")");
    container.style.setProperty("height", 120 + "px");
    navContainer.style.setProperty("border-bottom", "");
    menu.classList.remove("sticky");
    header.classList.remove("sticky");
    document.documentElement.style.setProperty("--nav-other-display", "none");
    document.documentElement.style.setProperty("--nav-menu-display", "flex");
  } else {
    container.classList.remove("bg-img");
    container.style.setProperty("background-image", "");
    container.style.setProperty("height", "auto");
    navContainer.style.setProperty(
      "border-bottom",
      "solid 1px var(--color-border)",
    );
    menu.classList.add("sticky");
    header.classList.add("sticky");
    document.documentElement.style.setProperty("--nav-other-display", "flex");
    document.documentElement.style.setProperty("--nav-menu-display", "none");
  }
});

window.onscroll = function () {
  if (route.path === "/") {
    console.log(isDark);
    let menu = document.getElementById("menu");
    let header = document.getElementById("header");
    let container = document.getElementById("container");
    let navContainer = document.getElementById("nav-container");
    if (window.scrollY < 60 && header.offsetTop == 0) {
      menu.classList.remove("sticky");
      header.classList.remove("sticky");
      navContainer.style.setProperty("border-bottom", "");
      container.style.setProperty("background-image", "url(" + isDark + ")");
      document.documentElement.style.setProperty("--nav-other-display", "none");
      document.documentElement.style.setProperty("--nav-menu-display", "flex");
    } else if (window.scrollY >= header.offsetTop) {
      menu.classList.add("sticky");
      header.classList.add("sticky");
      navContainer.style.setProperty(
        "border-bottom",
        "solid 1px var(--color-border)",
      );
      container.style.setProperty("background-image", "");
      document.documentElement.style.setProperty("--nav-other-display", "flex");
      document.documentElement.style.setProperty("--nav-menu-display", "none");
    } else {
      menu.classList.remove("sticky");
      header.classList.remove("sticky");
      navContainer.style.setProperty("border-bottom", "");
      container.style.setProperty("background-image", "url(" + isDark + ")");
      document.documentElement.style.setProperty("--nav-other-display", "none");
      document.documentElement.style.setProperty("--nav-menu-display", "flex");
    }
  }
};
</script>

<template>
  <el-container>
    <div id="container" style="width: 100%">
      <div style="height: 60px; display: flex; align-items: center">
        <img
          v-if="route.path == '/'"
          :src="WeShare"
          style="height: 60px; margin: 0 auto"
        />
      </div>
      <el-header id="header" class="header">
        <!--使用 router-link 组件进行导航 -->
        <!--通过传递 `to` 来指定链接 -->
        <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
        <!-- <router-link to="/">返回首页</router-link> -->
        <NavigationBar ref="NavigationBar" v-on:closeMain="closeMain" />
      </el-header>
    </div>
    <el-main style="top: 1rem; padding: 1rem">
      <!-- 路由出口 -->
      <!-- 路由匹配到的组件将渲染在这里 -->
      <router-view v-slot="{ Component }">
        <!-- 不缓存detail页 -->
        <keep-alive exclude="Detail">
          <component :is="Component" />
        </keep-alive>
      </router-view>
    </el-main>
    <el-footer style="width: 100%; text-align: center; margin-top: 300px">
      <el-divider
        content-position="center"
        style="
          --el-bg-color: var(--el-bg-color-page);
          padding-bottom: 100px;
          transition: all 5s;
        "
      >
      </el-divider>
    </el-footer>
  </el-container>
</template>

<style scoped>
.bg-img {
  background-size: cover;
  background-position: center;
}

.header {
  z-index: 1;
  width: 100%;
  --el-header-padding: 0;
  background-color: transparent;
  backdrop-filter: blur(5px);
  transition: all 0.5s;
}

.sticky {
  position: fixed;
  top: 0;
  background-color: var(--el-bg-color);
}
</style>
