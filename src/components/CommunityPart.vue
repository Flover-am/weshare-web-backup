<template>
  <div>
    <el-container>
      <el-container>
        <el-aside
          width="200px"
          style="background-color: var(--el-bg-color); padding: 20px"
        >
          <!-- 侧边栏 -->
          <el-menu :default-openeds="['1']">
            <el-menu-item index="1-1">技术讨论</el-menu-item>
            <el-menu-item index="1-2">资源分享</el-menu-item>
            <el-menu-item index="1-3">闲聊灌水</el-menu-item>
          </el-menu>
        </el-aside>

        <el-main style="width: 100%">
          <!-- 主要内容区域 -->
          <el-row :gutter="20" style="margin: 20px">
            <el-col :span="18" v-for="post in props.posts" :key="post.id">
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
                    <el-form-item style="margin-left: 88%; margin-top: 10px">
                      <el-button @click="submitComment(post.id)"
                        >发表评论</el-button
                      >
                    </el-form-item>
                  </el-form>
                </div>
              </el-card>
            </el-col>
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

const props = defineProps({
  posts: [],
});
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
  postForm.resetFields();
};

const submitPost = () => {
  postForm.validate((valid) => {
    if (valid) {
      // 处理提交新帖子的逻辑
      const newPost = {
        title: newPostForm.value.title,
        content: newPostForm.value.content,
        type: 0,
        authorName: storage.get("userID"),
      };
      props.posts.value.push(newPost);

      // 关闭发布帖子对话框
      closePublishDialog();
    }
  });
};

const submitComment = (id) => {
  // 将评论添加到当前帖子的评论列表
  const postId = id; /* 获取当前帖子的ID，例如 this.$route.params.postId */
  const postIndex = props.posts.value.findIndex((post) => post.id === postId);
  const newComment = {
    id: new Date().toLocaleString(), // 使用时间戳作为评论ID
    author: "当前用户", // 替换为实际的用户信息
    content: props.posts.value[postIndex].newCommentForm,
  };

  if (postIndex !== -1 && newComment.content !== "") {
    props.posts.value[postIndex].comments.push(newComment);
  }
  // 清空评论表单
  props.posts.value[postIndex].newCommentForm = "";
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
</style>
