<template>
  <div style="padding-top: 20px;">
    <!-- Top Detail Section -->
    <div class="detail-top">
      <div class="top-left">
        <div class="cover-img">
          <img :src="Host + '/upimg/' + detailinfo.img_url" width="270" height="200" style="object-fit: cover;" />
        </div>
        <div>
          <div style="display: flex;align-items: center">
            <el-tag>景区状态</el-tag>
            <div style="margin-left:10px;font-size: 14px;">{{ detailinfo.is_kaifang }}</div>
          </div>
          <br />
          <div style="display: flex;align-items: center">
            <el-tag>审核状态</el-tag>
            <div style="margin-left:10px;font-size: 14px;">{{ detailinfo.is_shenhe }}</div>
          </div>
          <div style="line-height: 32px;font-size: 20px;margin:10px 0;">{{ detailinfo.name }}</div>
          <div class="row-item">¥{{ detailinfo.price }}元/人</div>
          <div class="row-item">分类：<span class="blue">{{ detailinfo.fenlei?.mingcheng }}</span></div>
          <div class="row-item">地区：<span class="blue">{{ detailinfo.diqu?.mingcheng }}</span></div>
          <div class="row-item">地址：<span class="blue">{{ detailinfo.dizhi }}</span></div>
          <el-button type="primary" style="margin-top: 20px;" @click="godingdan(detailinfo)">预订</el-button>
        </div>
      </div>
      <div class="top-right">
        <div style="display: flex;align-items: center;">
          <img src="https://travel.gitapp.cn/assets/want-read-hover-e9e227e2.svg" style="width: 32px;height: 32px;margin-right: 24px;" />
          <div class="right-action">
            <span>B站程序员科科</span>
            <span>x</span>
          </div>
        </div>
        <el-divider />
        <div style="display: flex;align-items: center;">
          <img src="https://travel.gitapp.cn/assets/want-read-hover-e9e227e2.svg" style="width: 32px;height: 32px;margin-right: 24px;" />
          <div class="right-action">
            <span @click="favoriteButton(detailinfo)" style="cursor: pointer; color: #409EFF;">
              {{ isFavorite ? '取消收藏' : '收藏' }}
            </span>
            <span>x</span>
          </div>
        </div>
        <el-divider />
        <div style="display: flex;align-items: center;">
          <img src="https://travel.gitapp.cn/assets/share-icon-d2ff6534.svg" style="width: 32px;height: 32px;margin-right: 24px;" />
          <div class="right-action">
            <span>V: python_kk</span>
            <span>x</span>
          </div>
        </div>
        <el-divider />
      </div>
    </div>

    <!-- Bottom Section -->
    <div class="detail-bottom">
      <div class="bottom-left">
        <el-tabs v-model="activeName">
          <el-tab-pane label="评论" name="评论">
            <div style="padding-right: 20px;">
              <div style="font-weight: 600;font-size: 14px;color: #152844;margin: 24px 0 12px;">
                评论需审核后展示~
              </div>
              <div style="display: flex;align-items: center;">
                <el-avatar :size="32" src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png" style="margin-right:10px;" />
                <el-input type="textarea" placeholder="请输入内容" v-model="textarea" style="flex:1;" />
                <el-button type="primary" style="margin-left:10px;" @click="addComment(detailinfo.id)">发表评论</el-button>
              </div>

              <div style="display: flex;justify-content: space-between;align-items: center;padding: 20px 0;">
                <div style="color: #484848;font-size: 14px;">共有{{ plgs }}条评论</div>
              </div>

              <div v-for="item in plList" :key="item.id" style="margin-bottom:20px;border-bottom: 1px dashed #cedce4;padding: 10px 0;">
                <div style="display: flex;align-items: center;">
                  <el-avatar :size="32" :src="Host + '/upimg/' + item.shui.img_url" style="margin-right:10px;" />
                  <div style="flex:1;">
                    <div style="font-weight: 600;font-size: 16px;">{{ item.shui.username }}</div>
                    <div style="font-size: 14px;color: #999;">{{ item.pl_date }}</div>
                    <div>{{ item.content }}</div>
                  </div>
                </div>
              </div>
            </div>
          </el-tab-pane>

          <el-tab-pane label="简介" name="简介">
            <div style="color: #484848;font-size: 16px;line-height: 26px;">{{ detailinfo.jianjie }}</div>
          </el-tab-pane>
        </el-tabs>
      </div>

      <div class="bottom-right">
        <div class="recommend-title">同类推荐</div>
        <el-divider style="margin-top: 10px" />
        <div>
          <div v-for="item in tongleilist" :key="item.id" class="tj-item" @click="goDetailPage(item.id)">
            <div style="height:200px">
              <img :src="Host + '/upimg/' + item.img_url" width="270" height="200" style="object-fit: cover;" />
            </div>
            <div style="padding: 10px 0;font-weight: 600;font-size: 18px;">{{ item.name }}</div>
            <div style="margin-bottom: 20px;font-size: 16px;">{{ item.dengji }} - {{ item.price }}元</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";

export default {
  data() {
    return {
      Host: "https://online-z16b.onrender.com",
      activeName: "评论",
      detailinfo: [],
      tongleilist: [],
      plList: [],
      plgs: 0,
      isFavorite: false,
      textarea: "",
    };
  },
  created() {
    this.userInfo = localStorage.getItem("userInfo");
    this.userInfoid = localStorage.getItem("userInfoid");
    const id = this.$route.query.id;
    this.xiangqing(id);
    this.checkFavorite(id);
    this.ckpl(id);
  },
  methods: {
    goDetailPage(id) {
      window.open('/home/detail?id=' + id, '_blank');
    },
    xiangqing(id) {
      this.$api.xiangqing({ id }).then(res => {
        if (res.data.code === 200) {
          this.detailinfo = res.data.data;
          this.tongleilist = res.data.data2;
        }
      });
    },
    godingdan(detailinfo) {
      this.$router.push({ path: '/home/confirm', query: { id: detailinfo.id } });
    },
    checkFavorite(id) {
      this.$api.checkFavorite({
        shui_id: this.userInfoid,
        xiangqing_id: id,
      }).then(res => {
        this.isFavorite = res.data.code === 200;
      });
    },
    favoriteButton(detailinfo) {
      if (!this.userInfo) {
        this.$message.warning("请您先登录后再尝试收藏~");
        return;
      }
      if (this.isFavorite) {
        this.$api.deleteFavorite({
          shui_id: this.userInfoid,
          xiangqing_id: detailinfo.id,
        }).then(res => {
          if (res.data.code === 200) {
            this.isFavorite = false;
            this.$message.success("已取消收藏！");
          }
        });
      } else {
        const sc_date = new Date().toISOString().split("T")[0];
        this.$api.addFavorite({
          shui_id: this.userInfoid,
          jingqu_id: detailinfo.id,
          sc_date
        }).then(res => {
          if (res.data.code === 200) {
            this.isFavorite = true;
            this.$message.success("已收藏！");
          }
        });
      }
    },
    addComment(id) {
      if (!this.userInfo) {
        this.$message.warning("请您先登录后再尝试留言~");
        return;
      }
      if (!this.textarea) {
        this.$message.error("留言为空！");
        return;
      }
      const pl_date = new Date().toISOString().split("T")[0];
      this.$api.pl({
        shui_id: this.userInfoid,
        pl_date,
        jingqu_id: id,
        content: this.textarea,
      }).then(res => {
        if (res.data.code === 200) {
          this.$message.success("留言成功！留言正在审核中");
          this.textarea = '';
        } else {
          this.$message.error("留言失败！" + res.data.message);
        }
      }).catch(() => {
        this.$message.error("留言失败！");
      });
    },
    ckpl(id) {
      this.$api.ckpl({
        spid: id,
        pageNum: 1,
        pageSize: 10
      }).then(res => {
        if (res.data.code === 200) {
          this.plList = res.data.data;
          this.plgs = res.data.zs;
        }
      });
    }
  }
};
</script>

<style scoped>
.detail-top,
.detail-bottom {
  display: flex;
}
.top-left,
.bottom-left {
  flex: 1;
}
.top-right,
.bottom-right {
  width: 275px;
}
.cover-img {
  width: 255px;
  height: 200px;
  background-color: #ccc;
  margin-right: 40px;
}
.row-item {
  line-height: 18px;
  font-size: 14px;
  margin-bottom: 5px;
}
.blue {
  color: #315c9e;
}
.right-action {
  display: flex;
  justify-content: space-between;
  flex: 1;
  color: #152844;
  font-size: 16px;
  font-weight: 600;
}
.recommend-title {
  font-weight: 600;
  font-size: 18px;
  color: #152844;
}
.tj-item {
  cursor: pointer;
  margin-bottom: 20px;
}
</style>
