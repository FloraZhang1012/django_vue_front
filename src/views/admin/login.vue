<template>
    <div class="login-container">
        <el-card class="box-card">
            <div class="login-body">
                <div class="login-title">Django+Vue Tourism Recommendation System</div>
                <div class="login-title">Login</div>
                <el-form ref="form" :model="userForm">
                    <el-input placeholder="Enter Username..." v-model="userForm.accountNumber" class="login-input" />

                    <el-input placeholder="Enter Password..." v-model="userForm.userPassword" class="login-input"
                        @keyup.enter.native="login" show-password />

                    <el-select v-model="userForm.value" clearable placeholder="Select Role" class="login-input">
                        <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
                    </el-select>

                    <div class="login-submit">
                        <el-button type="primary" @click="login">Login</el-button>
                    </div>
                    <div>
                        <span class="register-link">
                            Don't have an account?
                            <router-link to="/register">Register</router-link>
                        </span>
                    </div>
                </el-form>
            </div>
        </el-card>
    </div>
</template>

<script>
import request from "@/utils/request";
export default {
    name: "login",
    data() {
        return {
            userForm: {
                accountNumber: '',
                userPassword: '',
            },
            options: [
                {
                    value: '1',
                    label: 'Administrator'
                },
                {
                    value: '2',
                    label: 'Business Owner'
                },
                {
                    value: '3',
                    label: 'User'
                }
            ],
        };
    },

    methods: {
        login() {
            request.post("/hello/login/", {
                username: this.userForm.accountNumber.trim(),
                password: this.userForm.userPassword.trim(),
                value: this.userForm.value
            }).then(res => {
                console.log(res);
                if (res.data.meta.status === 200) {
                    localStorage.setItem("userInfo", res.data.data.username);
                    localStorage.setItem("userInfoid", res.data.data.user_id);
                    localStorage.setItem("user_id", res.data.data.user_id); // ✅ 评论功能用这个字段
                    localStorage.setItem("img_url_touxiang", res.data.data.img_url);
                    localStorage.setItem("jianjie", res.data.data.jianjie);
                    localStorage.setItem("isAdmin", res.data.data.isAdmin);
                    localStorage.setItem("token", res.data.data.token);
                    

                    this.$message.success("Login successful");

                    const role = res.data.data.isAdmin;
                    if (role == 1) {
                        this.$router.push('/home/index');
                    } else if (role == 2) {
                        this.$router.push('home/index');
                    } else if (role == 3) {
                        this.$router.push('/home/index');
                    }
                } else {
                this.$message.error(res.data.meta.message);
                
                    
                }
            });
        },
        toIndex() {
            this.$router.replace({ path: '/home/index' });
        }
    }
}
</script>

<style scoped>
.login-container {
  background: linear-gradient(to right, #c6f1f8, #fdfbfb);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100%;
}

.el-card.box-card {
  border-radius: 16px;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.1);
  padding: 30px 20px;
  width: 360px;
  background-color: #fff;
}

.login-body {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.login-title {
  font-size: 22px;
  font-weight: bold;
  color: #409EFF;
  text-align: center;
  margin-bottom: 10px;
}

.login-input {
  width: 100%;
  margin-bottom: 20px;
}

.el-input,
.el-select {
  border-radius: 8px;
}

.login-submit {
  width: 100%;
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.el-button {
  width: 100%;
  border-radius: 8px;
  font-weight: bold;
  font-size: 16px;
}

.register-link {
  margin-top: 15px;
  font-size: 14px;
  color: #333;
}

.register-link a {
  color: #409EFF;
  text-decoration: none;
  font-weight: bold;
  margin-left: 5px;
}

.register-link a:hover {
  text-decoration: underline;
}


</style>
