<template>
  <el-button
    :type="isCollected ? 'danger' : 'primary'"
    :icon="isCollected ? 'el-icon-star-on' : 'el-icon-star-off'"
    size="small"
    @click="toggleCollect"
    plain
  >
    {{ isCollected ? '取消收藏 / Unfavorite' : '收藏 / Favorite' }}
  </el-button>
</template>

<script>
import request from "@/utils/request";
import axios from "axios";

const host = 'https://online-z16b.onrender.com';

export default {
  props: {
    jingquId: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      isCollected: false,
      userId: localStorage.getItem("user_id")
    };
  },
  mounted() {
    this.checkIfCollected();
  },
  methods: {
    checkIfCollected() {
      request.get(`${host}/hello/sc/check/`, {
        params: {
          userId: this.userId,
          jingquId: this.jingquId
        }
      }).then(res => {
        this.isCollected = res.data.favorited;
      });
    },
    toggleCollect() {
      if (!this.userId) {
        this.$message.warning("请先登录 / Please log in");
        return;
      }

      if (this.isCollected) {
        axios.delete(`${host}/hello/sc/delete/`, {
          params: {
            userId: this.userId,
            jingquId: this.jingquId
          }
        }).then(res => {
          this.isCollected = false;
          this.$message.success(res.data.message);
        }).catch(() => {
          this.$message.error("取消收藏失败 / Failed to unfavorite");
        });
      } else {
        request.post(`${host}/hello/sc/add/`, {
          userId: this.userId,
          jingquId: this.jingquId
        }).then(res => {
          this.isCollected = true;
          this.$message.success(res.data.message);
        }).catch(() => {
          this.$message.error("收藏失败 / Failed to favorite");
        });
      }
    }
  }
};
</script>
