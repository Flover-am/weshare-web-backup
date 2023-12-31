<script setup>
import {
  Collection,
  Tickets,
  EditPen,
  Help,
  Link,
  Reading,
  Notebook,
  ChatDotSquare,
  Tools,
  Service,
  CircleCheck,
  DCaret,
} from "@element-plus/icons-vue";
</script>
<script>
// 后端课程信息数据获取
import {ref} from "vue";
import axios from "axios";

const courses = ref([]);
import storage from "../utils/LocalStorage.js";
import {ElMessageBox} from "element-plus";

const userName = storage.get("userID");
import URL from "../global/url";
// url为对应接口的映射
axios.get(URL.findAllName).then(function (resp) {
  courses.value = resp.data;
  // console.log(courses.value);
});

const options = [
  {
    value: "文件",
    label: "文件",
  },
  {
    value: "链接",
    label: "链接",
  },
];

export default {
  data() {
    return {
      uploadFileURL: URL.uploadFile , // "http://127.0.0.1:996/resource/uploadfile"
      uploadLinkURL: URL.uploadLink, // 上传文件的地址! URL.uploadLink
      loadFileParams: {
        // 上传文件的参数！
        name: "",
        coursename: "",
        username: userName,
        type: "",
        intro: "",
        interlinking: "",
        file: "",
      },
      headers: {
        Authorization: window.sessionStorage.getItem("myToken"),
      },
      coursesU: [],
      type: [],
      fileList: []
    };
  },
  methods: {
    baseUploadBefore() {
      if (this.loadFileParams.name == "") {
        ElMessageBox.alert("请您检查资源名输入", {
          confirmButtonText: "确定",
        });
        return false;
      }
      if (this.loadFileParams.coursename == "") {
        ElMessageBox.alert("请您检查课程名称输入", {
          confirmButtonText: "确定",
        });
        return false;
      }
      if (this.loadFileParams.type == "") {
        ElMessageBox.alert("请您检查资源类型输入", {
          confirmButtonText: "确定",
        });
        return false;
      }
      if (this.loadFileParams.intro == "") {
        ElMessageBox.alert("请您检查资源介绍输入", {
          confirmButtonText: "确定",
        });
        return false;
      }
      if (storage.get("userID") == null) {
        ElMessageBox.alert("请您先登录", {
          confirmButtonText: "确定",
        });
        return false;
      }
      return true
    },
    fileUploadBefore() {
      var isPrepared = this.baseUploadBefore()
      if (!isPrepared) {
        return false
      }
      if (this.fileList.length == 0) {
        ElMessageBox.alert("文件不能为空", {
          confirmButtonText: "确定",
        });
        return false;
      }
      return true
    },
    linkUploadBefore() {
      var isPrepared = this.baseUploadBefore()
      if (!isPrepared) {
        return false
      }

      if (this.loadFileParams.interlinking == "") {
        ElMessageBox.alert("请您检查链接输入", {
          confirmButtonText: "确定",
        });
        return;
      }
      return true
    },
    uploadLink() {
      var isPrepared = this.linkUploadBefore()
      if (!isPrepared) {
        return
      }
      ElMessageBox.alert('上传后可在\"我的\"界面更改修改上传内容', '请确认上传内容', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
          .then(() => {
            let that = this
            axios
                .post(this.uploadLinkURL, this.loadFileParams)
                .then(function (res) {
                  let data = res.data
                  if (data == "link_exist") {
                    ElMessageBox.alert("资源名重复,请更换资源名", {
                      confirmButtonText: "确定",
                    })
                  } else if (data == "fail") {
                    ElMessageBox.alert("上传错误,请联系管理员", {
                      confirmButtonText: "确定",
                    })
                  } else {
                    ElMessageBox.alert("您已成功上传链接", {
                      confirmButtonText: "确定",
                    });
                    that.clear()
                  }
                });

          })
          .catch(() => {
          });
    },
    uploadFile() {
      let isPrepared = this.fileUploadBefore()
      if (!isPrepared) {
        return
      }
      ElMessageBox.alert('上传后可在\"我的\"界面更改修改上传内容', '请确认上传内容', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
          .then(() => {
            // 本来想一次传多个文件,但数据库设计原因,只能一次传一个了
            let formData = new FormData()
            formData.append("name", this.loadFileParams.name)
            formData.append("coursename", this.loadFileParams.coursename)
            formData.append("username", this.loadFileParams.username)
            formData.append("type", this.loadFileParams.type)
            formData.append("intro", this.loadFileParams.intro)
            formData.append("file", this.fileList[0].raw)
            axios
                .post(this.uploadFileURL, formData)
                .then(function (res) {
                  let data = res.data
                  console.log(data)

                  if (data == "exists") {
                    ElMessageBox.alert("资源名重复,请更换资源名", {
                      confirmButtonText: "确定",
                    })
                  } else if (data == "fail") {
                    ElMessageBox.alert("上传错误,请联系管理员", {
                      confirmButtonText: "确定",
                    })
                  } else {
                    ElMessageBox.alert("您已成功上传文件", {
                      confirmButtonText: "确定",
                    });
                    that.clear()
                  }
                })
          })
          .catch(() => {
          });
    },
    clear() {
      this.loadFileParams.name = ""
      this.loadFileParams.coursename = ""
      this.loadFileParams.type = ""
      this.loadFileParams.intro = ""
      this.loadFileParams.interlinking = ""
      this.loadFileParams.file = ""
      this.fileList = ""
    },
    handleRemove(file, fileList) {
    },
    handlePreview(file) {
    },


    // successResave(response, file, fileList) {
    //   console.log("success")
    //   console.log(response);
    //   ElMessageBox.alert("您已成功上传" + file.name, {
    //     confirmButtonText: "确定",
    //   });
    //   if (response.code == 10001) {
    //     alert("保存成功");
    //     this.proType = "";
    //     this.mobanname = "";
    //     this.value5 = "";
    //     this.$router.push("/moban");
    //   }
    // },

    // createFilter(queryString) {
    //   return (coursesU) => {
    //     return (
    //         coursesU.coursename
    //             .toLowerCase()
    //             .indexOf(queryString.toLowerCase()) === 0
    //     );
    //   };
    // },
    handleSelect(item) {
      if (this.loadFileParams.type == "链接") console.log(item);
    },
  },
};
</script>

<template>
  <div
      style="
      z-index: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
    "
  >
    <el-card class="table-container">
      <div>
        <el-col :span="12" style="margin-bottom: 20px">
          <div class="sub-title">
            <el-icon
                style="vertical-align: -0.2em; margin-right: 5px"
                size="large"
            >
              <EditPen/>
            </el-icon>
            资源名
          </div>
          <el-input
              style="width: 14rem"
              v-model="loadFileParams.name"
              clearable
              placeholder="请输入资源名"
              size="large"
          ></el-input>
        </el-col>

        <el-row>
          <el-col :span="12">
            <div class="sub-title">
              <el-icon
                  style="vertical-align: -0.2em; margin-right: 5px"
                  size="large"
              >
                <Collection/>
              </el-icon>
              课程名称
            </div>
            <el-select
                style="width: 14rem"
                v-model="loadFileParams.coursename"
                class="m-2"
                placeholder="Select"
                size="large"
            >
              <el-option
                  v-for="item in courses"
                  :key="item.id"
                  :label="item.coursename"
                  :value="item.coursename"
              />
            </el-select>
          </el-col>
          <el-col :span="12">
            <div class="sub-title">
              <el-icon
                  style="vertical-align: -0.2em; margin-right: 5px"
                  size="large"
              >
                <Help/>
              </el-icon>
              资源类型
            </div>
            <el-select
                style="width: 14rem"
                v-model="loadFileParams.type"
                class="m-2"
                placeholder="Select"
                size="large"
            >
              <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
              />
            </el-select>
          </el-col>
        </el-row>
        <el-form-item
            prop="child"
            v-if="loadFileParams.type == '链接'"
            style="margin-bottom: 30px"
        >
          <div class="sub-title">
            <el-icon
                style="vertical-align: -0.2em; margin-right: 5px"
                size="large"
            >
              <Link/>
            </el-icon>
            链接
          </div>
          <el-input
              v-model="loadFileParams.interlinking"
              clearable
              placeholder="请输入链接"
              size="large"
          ></el-input>
        </el-form-item>
        <el-row style="margin-bottom: 30px">
          <div class="sub-title">
            <el-icon
                style="vertical-align: -0.2em; margin-right: 5px"
                size="large"
            >
              <Tickets/>
            </el-icon>
            资源介绍
          </div>
          <el-input
              type="textarea"
              :autosize="{ minRows: 2, maxRows: 4 }"
              placeholder="请输入资源介绍"
              clearable
              v-model="loadFileParams.intro"
          >
          </el-input>
        </el-row>
      </div>
      <el-upload
          class="upload-demo"
          drag
          :headers="headers"
          :limit="1"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          v-model:file-list="fileList"
          multiple
          v-if="loadFileParams.type == '文件'"
          :auto-upload="false"
      >
        <i class="el-icon-upload"></i>
        <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
        <div class="el-upload__tip" slot="tip">贡献你的资源吧</div>
        <div class="el-upload__tip text-red" slot="tip">一次只能传一个文件哦</div>
      </el-upload>
      <el-button
          size="large"
          @click="uploadLink"
          v-if="loadFileParams.type == '链接'"
          plain
          color="rgb(1, 132, 127)"
      >将信息上传到服务器
      </el-button>

      <el-button
          size="large"
          @click="uploadFile"
          v-if="loadFileParams.type == '文件'"
          plain
          color="rgb(1, 132, 127)"
      >将信息上传到服务器
      </el-button>
    </el-card>
  </div>
</template>

<style scoped>
@keyframes PopUp {
  from {
    scale: 0.9;
  }

  to {
    scale: 1;
  }
}

.table-container {
  width: var(--rank-table-width);
  border-radius: 8px;
  margin-bottom: 1rem;
  background-color: var(--el-bg-color);
  box-sizing: border-box;
  box-shadow: 0 1px 3px hsl(0deg 0% 7% / 10%);
  padding: 1rem;
  animation-name: PopUp;
  animation-duration: 0.5s;
}

.sub-title {
  margin-bottom: 0.5rem;
}
</style>
