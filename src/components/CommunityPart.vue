<template>
  <div style="width: var(--home-content-container-width)">
    <el-container>
      <el-container>
        <el-aside
          width="20%"
          style="background-color: var(--el-bg-color); padding: 20px"
        >
          <!-- 侧边栏 -->
          <el-menu v-model="index" :default-openeds="index">
            <el-menu-item index="0" @click="getposts(0)">技术讨论</el-menu-item>
            <el-menu-item index="1" @click="getposts(1)">资源分享</el-menu-item>
            <el-menu-item index="2" @click="getposts(2)">闲聊灌水</el-menu-item>
          </el-menu>
        </el-aside>

        <el-main style="width: 200%">
          <!-- 主要内容区域 -->
          <el-row :gutter="20" style="margin: 20px">
            <el-col :span="18" v-for="post in currentPosts" :key="post.id">
              <!-- 帖子列表 -->
              <el-card class="forum-card">
                <div slot="header" class="clearfix">
                  <el-row
                    style="
                      display: flex;
                      justify-content: space-between;
                      align-items: center;
                    "
                  >
                    <strong style="font-weight: bold; font-size: 20px">{{
                      post.title
                    }}</strong>
                    <strong
                      style="
                        font-weight: bold;
                        font-size: 40px;
                        margin-top: -20px;
                      "
                      >{{ post.authorName }}</strong
                    >
                  </el-row>
                </div>
                <span style="margin-top: 10px">
                  {{ post.content }}
                </span>

                <!-- 评论区域 -->
                <el-divider></el-divider>
                <div
                  v-for="comment in allComments(post)"
                  :key="comment.id"
                  class="comment-item"
                >
                  <el-col>
                    <span>{{ comment.time }}</span>
                    <el-row>
                      <span style="font-weight: bold"
                        >{{ comment.authorName }}：</span
                      >
                      <span>{{ comment.content }}</span>
                    </el-row>
                  </el-col>
                </div>
                <!-- Add a button to toggle comments visibility -->
                <el-button
                  style="margin-top: 10px"
                  v-if="post.commentsVo.length > 3"
                  @click="toggleComments(post)"
                >
                  {{ post.showAllComments ? "收起评论" : "查看更多评论" }}
                </el-button>
                <div class="comment-section">
                  <el-form
                    :model="post.newCommentForm"
                    :rules="commentFormRules"
                    ref="commentForm"
                    class="comment-form"
                  >
                    <el-form-item prop="comment" style="margin-bottom: 0px">
                      <el-input
                        v-model="post.newCommentForm"
                        placeholder="发表评论"
                      ></el-input>
                    </el-form-item>
                    <el-form-item style="margin-left: 85%; margin-top: 10px">
                      <el-button @click="submitComment(post.id)"
                        >发表评论</el-button
                      >
                    </el-form-item>
                  </el-form>
                </div>
              </el-card>
            </el-col>
            <div class="pagination-container">
              <el-pagination
                v-if="new_post.length > pageSize"
                :current-page="currentPage"
                :page-size="1"
                :total="Math.ceil(new_post.length / pageSize)"
                @current-change="handlePageChange"
              />
            </div>
            <el-col :span="6">
              <el-button
                type="primary"
                icon="el-icon-edit"
                class="publish-button"
                @click="showPublishDialog"
                >发布帖子&nbsp;&nbsp;&nbsp;
              </el-button>

              <!-- 发布帖子对话框 -->
              <el-dialog
                v-model="publishDialogVisible"
                title="发布新帖子"
                @close="closePublishDialog"
              >
                <el-form
                  :model="newPostForm"
                  :rules="postFormRules"
                  ref="postForm"
                  label-position="top"
                >
                  <el-form-item label="标题" prop="title">
                    <el-input
                      v-model="newPostForm.title"
                      placeholder="请输入标题"
                      :maxlength="10"
                    ></el-input>
                    <span
                      v-if="newPostForm.title.length > 10"
                      style="color: red"
                      >Title should not exceed 10 characters.</span
                    >
                  </el-form-item>
                  <el-form-item label="内容" prop="content">
                    <el-input
                      v-model="newPostForm.content"
                      type="textarea"
                      placeholder="请输入内容"
                    ></el-input>
                  </el-form-item>
                  <el-form-item>
                    <el-button type="primary" @click="submitPost"
                      >发布</el-button
                    >
                  </el-form-item>
                </el-form>
              </el-dialog>
            </el-col>
          </el-row>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>
<script setup>
import { ref, onActivated, onMounted, computed, watch } from "vue";
import { useRouter } from "vue-router";
import storage from "../utils/LocalStorage";

import URL from "../global/url";
import axios from "axios";
import { POST_URL } from "../global/url.js";
const props = defineProps({
  posts: [],
});
const router = useRouter();
const new_post = ref([]);
new_post.value = props.posts;
const currentPage = ref(1);
const pageSize = 8; // 每页显示的数量
onActivated: if (!storage.get("userID")) {
  router.push("/login");
}

const currentPosts = computed(() => {
  const startIndex = (currentPage.value - 1) * pageSize;
  const endIndex = startIndex + pageSize;
  if (props.posts != null)
    return new_post.value.length == 0
      ? props.posts.slice(startIndex, endIndex)
      : new_post.value.slice(startIndex, endIndex);
});
const index = ref(0);
const postForm = ref();
const commentForm = ref();
const publishDialogVisible = ref(false);
const newPostForm = ref({
  title: "",
  content: "",
});
const postFormRules = ref({
  title: [{ required: true, message: "请输入标题", trigger: "blur" }],
  content: [{ required: true, message: "请输入内容", trigger: "blur" }],
});

const commentFormRules = ref({
  comment: [{ required: true, message: "请输入评论", trigger: "blur" }],
});

const toggleComments = (post) => {
  post.showAllComments = !post.showAllComments;
};

const allComments = (post) => {
  return post != null
    ? post.showAllComments
      ? post.commentsVo
      : post.commentsVo.slice(0, 3)
    : [];
};

const showPublishDialog = () => {
  publishDialogVisible.value = true;
};

const closePublishDialog = () => {
  publishDialogVisible.value = false;
  // 重置发布帖子表单
  postForm.value.resetFields();
};
function handlePageChange(page) {
  currentPage.value = page;
}
const submitPost = () => {
  // 处理提交新帖子的逻辑
  const newPost = {
    title: newPostForm.value.title,
    content: newPostForm.value.content,
    type: index.value,
    authorName: storage.get("userID"),
  };
  axios({ method: "post", url: URL.PUBLISH_POST_URL, data: newPost }).then(
    (response) => {
      new_post.value.unshift({
        ...response.data,
        showAllComments: false,
        newCommentForm: "",
        // 添加新属性
      });
      console.log({
        ...response.data,
        showAllComments: false,
        newCommentForm: "",
        // 添加新属性
      });
    },
  );

  // 关闭发布帖子对话框
  closePublishDialog();
};
const getposts = (type) => {
  axios.get(POST_URL + "/" + type).then((response) => {
    new_post.value = response.data.map((post) => {
      return {
        ...post, // 保留原有属性
        showAllComments: false,
        newCommentForm: "",
        // 添加新属性
      };
    });
    index.value = type;
    currentPage.value = 1;
    console.log(index.value);
    console.log(new_post.value);
  });
};
const submitComment = (id) => {
  // 将评论添加到当前帖子的评论列表
  const postId = id; /* 获取当前帖子的ID，例如 this.$route.params.postId */
  const postIndex = new_post.value.findIndex((post) => post.id === postId);
  const newComment = {
    postId: postId,
    authorName: storage.get("userID"), // 替换为实际的用户信息
    content: new_post.value[postIndex].newCommentForm,
  };
  if (postIndex !== -1 && newComment.content !== "") {
    axios({
      method: "post",
      url: URL.COMMENT_PUBLISH_URL,
      data: newComment,
    }).then((response) => {
      new_post.value[postIndex].commentsVo.push(response.data);
      console.log(response.data);
    });
  }
  // 清空评论表单
  new_post.value[postIndex].newCommentForm = "";
};
</script>
<style scoped>
.forum-card {
  margin-bottom: 20px;
  width: 120%;
}
.comment-section {
  margin-top: 20px;
}

.comment-item {
  margin-top: 10px;
}
.publish-button {
  height: 40px;
  position: fixed;
  bottom: 10px;
  right: 10px;
}
.pagination-container {
  margin-top: 1rem; /* 为确保翻页组件与卡片之间有一定间距 */
}
</style>
