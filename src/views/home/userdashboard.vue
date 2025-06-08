<template>
  <div class="user-center-wrapper">
    <div class="user-center">
      <!-- 左边菜单栏 Left Sidebar -->
      <el-card class="user-left">
        <div class="avatar-section">
          <!-- 🌟 动态绑定头像 + 容错处理 -->
          <el-avatar
            :size="50"
            :src="userInfo.avatar || defaultAvatar"
            @error="handleAvatarError"
          />
          <div class="user-name">{{ username || '用户未登录' }}</div>
        </div>

        <!-- 订单中心 -->
        <div class="menu-section">
          <div class="section-title">订单中心 / Order Center</div>
          <div class="menu-item" @click="$router.push('/home/myorder')">
            <el-icon><List /></el-icon><span>我的订单 / My Orders</span>
          </div>
          <div class="menu-item" @click="$router.push('/home/myfavorite')">
            <el-icon><Link /></el-icon><span>我的收藏 / My Favorites</span>
          </div>
          <div class="menu-item" @click="$router.push('/home/chat')">
            <el-icon><ChatDotRound /></el-icon><span>消息中心 / Message</span>
          </div>
          <div class="menu-item" @click="$router.push('/home/mycomment')">
            <el-icon><Link /></el-icon><span>我的评论 / My Comments</span>
          </div>
          <div class="menu-item" @click="$router.push('/home/myaddress')">
            <el-icon><Link /></el-icon><span>我的地址 / My Addresses</span>
          </div>
        </div>

        <!-- 个人设置 -->
        <div class="menu-section">
          <div class="section-title">个人设置 / Personal Settings</div>
          <div class="menu-item" @click="$router.push('/home/UserCenter')">
            <el-icon><Odometer /></el-icon><span>编辑资料 / Edit Profile</span>
          </div>
        </div>
      </el-card>

      <!-- 右边内容区 Right Content -->
      <el-card class="user-right">
        <!-- 🌟 欢迎词带淡入动画 -->
        <div class="welcome-text fade-in">
          欢迎你，{{ username || '用户' }}！/ Welcome, {{ username || 'User' }}!
        </div>
        <RouterView />
      </el-card>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";
export default {
  name: "UserCenterPage",
  data() {
    return {
      Host: 'https://online-z16b.onrender.com',
      username: '',
      userInfo: {
        avatar: ''
      },
      defaultAvatar: 'https://gw.alipayobjects.com/zos/antfincdn/XAosXuNZyF/BiazfanxmamNRoxxVxka.png'
    };
  },
  created() {
    this.username = localStorage.getItem('userInfo') || '';
    this.userInfo.avatar = localStorage.getItem('avatar') || '';
  },
  methods: {
    handleAvatarError() {
      this.userInfo.avatar = this.defaultAvatar;
    }
  }
};
</script>

<style scoped>
.user-center-wrapper {
  display: flex;
  justify-content: center;
  padding: 40px 0;
}

.user-center {
  display: flex;
  width: 100%;
  max-width: 1100px;
  gap: 20px;
}

.user-left {
  width: 260px;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  padding: 20px;
}

.user-right {
  flex: 1;
  min-height: 500px;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.avatar-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 30px;
}

.user-name {
  margin-top: 10px;
  font-weight: bold;
  font-size: 18px;
  color: #152844;
}

.section-title {
  font-weight: bold;
  margin: 20px 0 10px;
  color: #303133;
  font-size: 16px;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 0;
  cursor: pointer;
  transition: all 0.3s;
}
.menu-item:hover {
  color: #409EFF;
}
.menu-item span {
  font-size: 14px;
  color: #303133;
}

.welcome-text {
  font-size: 26px;
  font-weight: bold;
  color: #333;
}

.fade-in {
  animation: fadeIn 1.2s ease forwards;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
