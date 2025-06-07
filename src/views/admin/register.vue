<template>
  <div class="register-container">
      <el-card class="box-card">
          <div class="register-body">
              <div class="register-title">Django+Vue Tourism Recommendation System</div>
              <div class="register-title">Register</div>
              <el-form ref="form" :model="userForm">
                  <el-input placeholder="Enter Username..." v-model="userForm.username" class="register-input" />

                  <el-input placeholder="Enter Password..." v-model="userForm.password" class="register-input"
                      show-password />

                  <el-select v-model="userForm.role" clearable placeholder="Select Role" class="register-input">
                      <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
                  </el-select>

                  <div class="register-submit">
                      <el-button type="primary" @click="register">Register</el-button>
                  </div>
                  <div>
                        <span class="login-link">
                            Already have an account?
                            <router-link to="/login">Login</router-link>
                        </span>
                    </div>

              </el-form>
          </div>
      </el-card>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "register",
  data() {
    return {
      userForm: {
        username: '',
        password: '',
        role: ''
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
    async register() {
      const { username, password, role } = this.userForm;

      if (!username || !password || !role) {
        this.$message.error("Please fill in all fields! / 请填写所有字段！");
        return;
      }

      // ✅ 密码复杂性检查：至少8位，包含大小写字母和数字
      const regex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/;
      if (!regex.test(password)) {
        this.$message.error("Password must be at least 8 characters and include uppercase, lowercase letters and a number. / 密码需至少8位，包含大写、小写字母和数字。");
        return;
      }

      try {
        const response = await axios.post("http://127.0.0.1:8000/hello/register/", {
          username,
          password,
          value: role
        });

        if (response.data.meta.status === 200) {
          this.$message.success("Registration successful! Redirecting to login... / 注册成功，正在跳转登录页...");
          setTimeout(() => {
            this.$router.push("/login");
          }, 1000);
        } else {
          this.$message.error(response.data.meta.message);
        }
      } catch (error) {
        console.error("Registration failed:", error);
        this.$message.error("An error occurred during registration. / 注册过程中发生错误。");
      }
    }
  }
};
</script>


<style scoped>
.register-container {
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

.register-body {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.register-title {
  font-size: 22px;
  font-weight: bold;
  color: #409EFF;
  margin-bottom: 10px;
  text-align: center;
}

.register-input {
  width: 100%;
  margin-bottom: 20px;
}

.el-input,
.el-select {
  border-radius: 8px;
}

.register-submit {
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

.login-link {
  margin-top: 15px;
  font-size: 14px;
  color: #333;
}

.login-link a {
  color: #409EFF;
  text-decoration: none;
  font-weight: bold;
  margin-left: 5px;
}

.login-link a:hover {
  text-decoration: underline;
}

</style>
