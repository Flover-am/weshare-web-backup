<script setup>
import WeShare from "../assets/WESHARE.svg";
import WeShareDark from "../assets/WESHAREDARK.svg";
import { ref, watch, computed, getCurrentInstance } from "vue";
import {
  House,
  Medal,
  CirclePlus,
  Sunny,
  Reading,
  UserFilled,
  MostlyCloudy,
  User,
  Plus,
} from "@element-plus/icons-vue";
import BgImg from "../assets/img/home_pic.jpg";
import BgImgDark from "../assets/img/home_pic_dark.jpg";
import sunIcon from "@/components/SunIcon.vue";
import { useRouter, useRoute } from "vue-router";
import SearchBar from "./SearchBar.vue";
import { useDark } from "@vueuse/core";
import storage from "../utils/LocalStorage";
import axios from "axios";
import URL from "../global/url";

const isDark = useDark();

const router = useRouter();

const activeIndex = ref("/");

var hasLogin = ref(storage.get("userID") !== null).value;

const userID = ref(!hasLogin ? " 未登录" : storage.get("userID"));

var userContribution = -1;
var WeShareurl = ref(WeShare);
const userContrib = () => {
  if (!hasLogin) {
    return userContribution;
  } else {
    axios.get(URL.getContrib + userID.value).then((res) => {
      userContribution = res.data;
    });
    return userContribution;
  }
};
function change() {
  console.log(isDark._value);
  if (isDark._value) WeShareurl.value = WeShareDark;
  else WeShareurl.value = WeShare;
}
/**
 * 点击头像事件
 * 未登录：跳转至登录页面
 * 已登录：彩蛋
 */
function clickAvatar() {
  if (!storage.get("userID")) {
    router.push("/login");
  } else {
    // ElNotification({
    //     title: 'while\(true\)',
    //     message: '\{ learn\(coding\); \}',
    //     showClose: false,
    //     position:'bottom-right',
    //     duration:'1000'
    // })
  }
}
/**
 * 点击修改密码事件：跳转至/account页面
 */
function clickChangePassword() {
  router.push("/account");
}
/**
 * 点击登出事件:移除登录状态并刷新页面
 */
function clickLogout() {
  if (storage.get("userID") !== null) {
    storage.remove("userID");
    //storage.remove("courses")
  }
  router.push({ path: "/" });
  router.push({ path: "/" });
  router.go(0);
}

function clickUpload() {
  if (storage.get("userID") == null) {
    router.push({ path: "/login" });
  } else {
    router.push({ path: "/upload" });
  }
}
function clickLogo() {
  router.push({ path: "/" });
}
const route = useRoute();
</script>

<template>
  <div class="navigation-container" id="nav-container">
    <el-menu
      id="menu"
      mode="horizontal"
      class="navigation-menu"
      :default-active="route.path"
      router
      :ellipsis="false"
      style="
        --el-menu-bg-color: transparent;
        --el-menu-hover-bg-color: transparent;
      "
    >
      <el-menu-item style="display: var(--nav-other-display)">
        <img
          @click="clickLogo"
          :src="WeShareurl"
          :key="WeShareurl"
          style="height: 60px; margin: 0 auto"
        />
      </el-menu-item>
      <!-- index属性放路径-ysh -->
      <el-menu-item index="/">
        <!-- 使用插槽放icon-ysh -->
        <template #title>
          <el-icon>
            <House />
          </el-icon>
          <span style="margin-right: 5px">首页</span>
        </template>
      </el-menu-item>
      <el-menu-item index="/class">
        <template #title>
          <el-icon>
            <Reading />
          </el-icon>
          <span style="margin-right: 5px">分类</span>
        </template>
      </el-menu-item>
      <el-menu-item index="/rank">
        <template #title>
          <el-icon>
            <Medal />
          </el-icon>
          <span style="margin-right: 5px">排行</span>
        </template>
      </el-menu-item>

      <el-menu-item style="display: var(--nav-menu-display)">
        <template #title>
          <el-icon @click="clickUpload">
            <Plus />
          </el-icon>
          <span style="margin-right: 5px" @click="clickUpload">上传</span>
        </template>
      </el-menu-item>

      <!--      <el-menu-item style="display: var(&#45;&#45;nav-menu-display)">-->
      <!--        <template #title>-->
      <!--          <el-icon @click="clickAvatar">-->
      <!--            <User />-->
      <!--          </el-icon>-->
      <!--          <span style="margin-right: 5px" @click="clickAvatar">我的</span>-->
      <!--        </template>-->
      <!--      </el-menu-item>-->

      <!--      <el-menu-item style="display: var(&#45;&#45;nav-menu-display)">-->
      <!--        <SearchBar style="margin-right: 5px" />-->
      <!--      </el-menu-item>-->

      <el-menu-item index="/mypage" v-if="hasLogin">
        <template #title>
          <el-icon>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 1024 1024"
              data-v-ea893728=""
            >
              <path
                fill="currentColor"
                d="m512 747.84 228.16 119.936a6.4 6.4 0 0 0 9.28-6.72l-43.52-254.08 184.512-179.904a6.4 6.4 0 0 0-3.52-10.88l-255.104-37.12L517.76 147.904a6.4 6.4 0 0 0-11.52 0L392.192 379.072l-255.104 37.12a6.4 6.4 0 0 0-3.52 10.88L318.08 606.976l-43.584 254.08a6.4 6.4 0 0 0 9.28 6.72zM313.6 924.48a70.4 70.4 0 0 1-102.144-74.24l37.888-220.928L88.96 472.96A70.4 70.4 0 0 1 128 352.896l221.76-32.256 99.2-200.96a70.4 70.4 0 0 1 126.208 0l99.2 200.96 221.824 32.256a70.4 70.4 0 0 1 39.04 120.064L774.72 629.376l37.888 220.928a70.4 70.4 0 0 1-102.144 74.24L512 820.096l-198.4 104.32z"
              ></path>
            </svg>
          </el-icon>
          <span style="margin-right: 5px">我的</span>
        </template>
      </el-menu-item>
    </el-menu>
    <div style="display: var(--nav-other-display); overflow: hidden">
      <div
        style="
          align-self: center;
          display: flex;
          flex-direction: row;
          overflow: hidden;
        "
      >
        <SearchBar style="align-self: center" />
        <el-button
          :icon="Plus"
          style="
            align-self: center;
            --el-button-hover-bg-color: var(--color-main-darker);
            --el-button-hover-border-color: var(--color-main-darker);
            display: var(--nav-upload-display);
            margin-left: 1rem;
          "
          type="primary"
          color="var(--color-main)"
          size="large"
          round
          @click="clickUpload"
          >上传
        </el-button>
        <el-switch
          size="large"
          v-model="isDark"
          style="
            align-self: center;
            --el-switch-on-color: var(--color-main);
            --el-switch-off-color: rgb(216, 241, 241);
            margin: 0 1rem;
          "
          inline-prompt
          :active-icon="MostlyCloudy"
          :inactive-icon="sunIcon"
          @change="
            $emit('change', this.isDark);
            change();
          "
        />
      </div>
      <div class="user-profile">
        <div
          style="
            align-self: center;
            margin-right: 1rem;
            display: var(--userID-display);
            overflow: hidden;
            white-space: nowrap;
          "
        >
          {{ userID }}
        </div>
        <el-popover
          width="15rem"
          popper-style="box-shadow: rgb(14 18 22 / 35%) 0px 10px 38px -10px, rgb(14 18 22 / 20%) 0px 10px 20px -15px; padding: 1rem; border-radius: 8px;"
        >
          <template #reference>
            <el-avatar
              style="align-self: center; cursor: pointer"
              @click="clickAvatar"
              >{{ userID === null ? "" : userID.charAt(0) }}</el-avatar
            >
          </template>
          <template #default>
            <div
              class="account"
              style="display: flex; gap: 16px; flex-direction: column"
            >
              <div v-if="hasLogin">
                <p
                  class="greetings_main"
                  style="font-size: large; margin: 1px; font-weight: 500"
                >
                  欢迎您，{{ userID }}！
                </p>
                <p
                  class="greetings_sub"
                  style="font-size: small; margin-top: 7px; font-weight: 300"
                >
                  今天要学点什么呢？
                </p>
                <el-divider />
                <p class="contribution" style="margin-top: 2px">
                  当前贡献值：{{ userContrib() }}
                </p>
                <div class="avatar-function-buttons">
                  <el-button
                    @click="clickChangePassword"
                    class="popover-button"
                    style="margin: 1rem 0 0 0"
                  >
                    修改密码
                  </el-button>
                  <el-button
                    @click="clickLogout"
                    class="popover-button"
                    style="margin: 1rem 0 0 1rem"
                  >
                    退出登录
                  </el-button>
                </div>
              </div>
              <div v-else>
                <p
                  class="greetings_main"
                  style="font-size: large; margin: 1px; font-weight: 500"
                >
                  当前尚未登录
                </p>
                <el-button
                  @click="clickAvatar"
                  class="popover-button"
                  style="margin: 1rem 0 0 0"
                >
                  立即登录
                </el-button>
              </div>
            </div>
          </template>
        </el-popover>
      </div>
    </div>
  </div>
</template>

<style scoped>
.navigation-menu {
  border-bottom-width: 0;
  align-self: center;
  width: 100%;
  height: 60px;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  transition: all 0.5s;
  padding: 0 30%;
}

.sticky {
  padding: 0;
  width: auto;
  justify-content: flex-start;
}

.navigation-container {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding: 0 1rem;
  box-shadow: var(--el-box-shadow-light);
  box-sizing: border-box;
  /* border-bottom: solid 1px var(--color-border); */
  border-top: solid 1px var(--color-border);
  overflow: hidden;
  transition: all 0.5s;
}
.user-profile {
  align-self: center;
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
  overflow: hidden;
}

.popover-button {
  --el-color-primary: var(--color-main);
  --el-button-hover-bg-color: var(--el-bg-color-page);
  --el-button-hover-border-color: var(--el-border-color-darker);
  transition: all 0.3s;
  border-radius: 4px;
}

.avatar-function-buttons {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}
</style>

<style>
:root {
  --el-menu-active-color: var(--color-main);
  --el-menu-hover-text-color: var(--color-main);
}
</style>
