<template>
  <div>
    <el-container style="height: 100vh">
      <el-container style="height: 80%">
        <el-aside
          width="300px"
          style="background-color: #f4f4f5; padding: 20px"
        >
          <!-- 侧边栏 -->
          <el-menu
            :default-openeds="['1']"
            background-color="#f4f4f5"
            text-color="#303133"
            active-text-color="#409eff"
          >
            <el-menu-item index="1-1">技术讨论</el-menu-item>
            <el-menu-item index="1-2">资源分享</el-menu-item>
            <el-menu-item index="1-3">闲聊灌水</el-menu-item>
          </el-menu>
        </el-aside>

        <el-main>
          <!-- 主要内容区域 -->
          <el-row :gutter="20" style="margin: 20px">
            <el-col :span="18" v-for="post in posts">
              <!-- 帖子列表 -->
              <el-card :key="post.id" class="forum-card">
                <div slot="header" class="clearfix">
                  <span>{{ post.title }}</span>
                  <el-button type="text" class="right">查看详情</el-button>
                </div>
                <div>{{ post.content }}</div>

                <!-- 评论区域 -->
                <el-divider></el-divider>
                <div class="comment-section">
                  <el-form
                    :model="post.newCommentForm"
                    :rules="commentFormRules"
                    ref="commentForm"
                    class="comment-form"
                  >
                    <el-form-item prop="comment" style="margin-bottom: 0">
                      <el-input
                        v-model="post.newCommentForm.comment"
                        placeholder="发表评论"
                      ></el-input>
                    </el-form-item>
                    <el-form-item style="margin-bottom: 0">
                      <el-button type="primary" @click="submitComment(post.id)"
                        >发表评论</el-button
                      >
                    </el-form-item>
                  </el-form>

                  <!-- 显示评论列表 -->
                  <div
                    v-for="comment in post.comments"
                    :key="comment.id"
                    class="comment-item"
                  >
                    <span>{{ comment.author }}：</span>
                    <span>{{ comment.content }}</span>
                  </div>
                </div>
              </el-card>
            </el-col>
            <el-col :span="6">
              <el-button
                type="primary"
                icon="el-icon-edit"
                class="publish-button"
                @click="showPublishDialog"
              >
                发布帖子
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
                    ></el-input>
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

<script>
export default {
  data() {
    return {
      publishDialogVisible: false,
      newPostForm: {
        title: "",
        content: "",
      },
      postFormRules: {
        title: [{ required: true, message: "请输入标题", trigger: "blur" }],
        content: [{ required: true, message: "请输入内容", trigger: "blur" }],
      },
      posts: [
        {
          id: 1,
          title: "Vue.js 教程",
          content: "Vue.js 是一款渐进式 JavaScript 框架。",
          newCommentForm: {
            comment: "", // 每个帖子都有独立的评论输入框
          },
          comments: [],
        },
        {
          id: 2,
          title: "Element Plus 使用指南",
          content: "Element Plus 是一套基于 Vue.js 2.0 的桌面端组件库。",
          newCommentForm: {
            comment: "", // 每个帖子都有独立的评论输入框
          },
          comments: [],
        },
        // 添加更多帖子数据
      ],

      // 评论表单验证规则
      commentFormRules: {
        comment: [{ required: true, message: "请输入评论", trigger: "blur" }],
      },
    };
  },
  methods: {
    showPublishDialog() {
      this.publishDialogVisible = true;
    },
    // 关闭发布帖子对话框
    closePublishDialog() {
      this.publishDialogVisible = false;
      // 重置发布帖子表单
      this.$refs.postForm.resetFields();
    },
    // 提交新帖子
    submitPost() {
      this.$refs.postForm.validate((valid) => {
        if (valid) {
          // 处理提交新帖子的逻辑
          const newPost = {
            id: Date.now(),
            title: this.newPostForm.title,
            content: this.newPostForm.content,
            comments: [], // 初始化评论为空数组
            newCommentForm: {
              comment: "", // 每个帖子都有独立的评论输入框
            },
          };
          this.posts.push(newPost);

          // 关闭发布帖子对话框
          this.closePublishDialog();
        }
      });
    },
    // 提交评论
    submitComment(id) {
      // 将评论添加到当前帖子的评论列表
      const postId = id; /* 获取当前帖子的ID，例如 this.$route.params.postId */
      const postIndex = this.posts.findIndex((post) => post.id === postId);
      const newComment = {
        id: Date.now(), // 使用时间戳作为评论ID
        author: "当前用户", // 替换为实际的用户信息
        content: this.posts[postIndex].newCommentForm.comment,
      };

      if (postIndex !== -1) {
        this.posts[postIndex].comments.push(newComment);
      }
      // 清空评论表单
      this.posts[postIndex].newCommentForm.comment = "";
    },
  },
};
</script>

<style scoped>
.forum-card {
  margin-bottom: 20px;
}
.comment-section {
  margin-top: 20px;
}

.comment-item {
  margin-top: 10px;
}
.publish-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
}
</style>
