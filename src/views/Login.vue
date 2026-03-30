<template>
  <div class="login-container">
    <!-- 左侧：品牌视觉区 -->
    <div class="left-panel">
      <div class="left-top">
        <div class="brand-mark">
          <svg width="28" height="28" viewBox="0 0 28 28" fill="none">
            <rect width="28" height="28" rx="7" fill="white" fill-opacity="0.15" />
            <path d="M7 14L12 9L17 14L12 19L7 14Z" fill="white" fill-opacity="0.9" />
            <path d="M13 14L18 9L21 12V16L18 19L13 14Z" fill="white" fill-opacity="0.5" />
          </svg>
        </div>
        <span class="brand-name">Nexus</span>
      </div>

      <div class="characters-area">
        <AnimatedCharacters
          :is-typing="isTyping"
          :show-password="showPassword"
          :password-length="passwordValue.length"
        />
      </div>

      <div class="left-footer">
        <a href="#">帮助中心</a>
        <a href="#">隐私政策</a>
      </div>

      <div class="decor-blur-1" />
      <div class="decor-blur-2" />
      <div class="decor-grid" />
    </div>

    <!-- 右侧：登录表单 -->
    <div class="right-panel">
      <div class="form-wrapper">
        <div class="mobile-logo">
          <div class="mobile-logo-icon">
            <svg width="20" height="20" viewBox="0 0 28 28" fill="none">
              <path d="M7 14L12 9L17 14L12 19L7 14Z" fill="#1E40AF" fill-opacity="0.9" />
              <path d="M13 14L18 9L21 12V16L18 19L13 14Z" fill="#3B82F6" fill-opacity="0.7" />
            </svg>
          </div>
          <span>Nexus 平台</span>
        </div>

        <div class="form-header">
          <h1 class="form-title">登录到工作台</h1>
          <p class="form-subtitle">统一接入前端平台旗下所有系统</p>
        </div>

        <el-form
          ref="formRef"
          :model="formData"
          :rules="rules"
          label-position="top"
          class="login-form"
          @submit.prevent="handleLogin"
        >
          <div class="field-label">账号</div>
          <el-form-item prop="username">
            <el-input
              v-model="formData.username"
              placeholder="输入您的账号"
              size="large"
              :prefix-icon="User"
              @focus="isTyping = true"
              @blur="isTyping = false"
            />
          </el-form-item>

          <div class="field-label">密码</div>
          <el-form-item prop="password">
            <el-input
              v-model="formData.password"
              :type="showPassword ? 'text' : 'password'"
              placeholder="输入您的密码"
              size="large"
              :prefix-icon="Lock"
              @input="passwordValue = formData.password"
            >
              <template #suffix>
                <el-icon class="eye-toggle" @click="showPassword = !showPassword">
                  <View v-if="showPassword" />
                  <Hide v-else />
                </el-icon>
              </template>
            </el-input>
          </el-form-item>

          <el-form-item v-if="errorMsg" class="error-item">
            <div class="error-box">{{ errorMsg }}</div>
          </el-form-item>

          <el-form-item>
            <el-button
              type="primary"
              size="large"
              :loading="loading"
              class="submit-btn"
              @click="handleLogin"
            >
              {{ loading ? '登录中...' : '登录' }}
            </el-button>
          </el-form-item>
        </el-form>

        <div class="divider">
          <span>或</span>
        </div>

        <el-button size="large" class="social-btn">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
            <path
              d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.95-.49-7-3.85-7-7.93 0-.62.08-1.21.21-1.79L9 15v1c0 1.1.9 2 2 2v1.93zm6.9-2.54c-.26-.81-1-1.39-1.9-1.39h-1v-3c0-.55-.45-1-1-1H8v-2h2c.55 0 1-.45 1-1V7h2c1.1 0 2-.9 2-2v-.41c2.93 1.19 5 4.06 5 7.41 0 2.08-.8 3.97-2.1 5.39z"
            />
          </svg>
          飞书账号一键登录
        </el-button>

        <div class="signup-row">
          暂无账号？
          <a href="#" class="signup-link">联系管理员申请开通</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
import { User, Lock, View, Hide } from '@element-plus/icons-vue'
import AnimatedCharacters from '../components/AnimatedCharacters/index.vue'

const formRef = ref()
const loading = ref(false)
const showPassword = ref(false)
const isTyping = ref(false)
const passwordValue = ref('')
const errorMsg = ref('')

const formData = reactive({
  username: '',
  password: '',
})

const rules = {
  username: [
    { required: true, message: '请输入账号', trigger: 'blur' },
    { min: 3, message: '账号长度不能少于3个字符', trigger: 'blur' },
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码长度不能少于6个字符', trigger: 'blur' },
  ],
}

const mockLogin = async (_values: { username: string; password: string }) => {
  await new Promise((resolve) => setTimeout(resolve, 800))
  return { data: { access_token: 'mock_token_' + Date.now() } }
}

const handleLogin = async () => {
  if (!formRef.value) return

  await formRef.value.validate(async (valid: boolean) => {
    if (!valid) return

    loading.value = true
    errorMsg.value = ''

    try {
      const { data } = await mockLogin(formData)
      localStorage.setItem('access_token', data.access_token)
      ElMessage.success('登录成功')
      setTimeout(() => {
        window.location.href = '/'
      }, 500)
    } catch {
      errorMsg.value = '账号或密码有误，请重新输入'
    } finally {
      loading.value = false
    }
  })
}
</script>

<style scoped>
.login-container {
  min-height: 100vh;
  display: grid;
  grid-template-columns: 1fr 1fr;
}

@media (max-width: 1024px) {
  .login-container {
    grid-template-columns: 1fr;
  }
}

/* 左侧面板 */
.left-panel {
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 48px;
  background: linear-gradient(145deg, #0f172a 0%, #1e3a8a 50%, #1e40af 100%);
  overflow: hidden;
}

@media (max-width: 1024px) {
  .left-panel {
    display: none;
  }
}

.left-top {
  position: relative;
  z-index: 20;
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 20px;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 0.5px;
}

.brand-mark {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  backdrop-filter: blur(8px);
}

.brand-name {
  color: #ffffff;
  font-size: 20px;
  font-weight: 700;
  letter-spacing: 1px;
}

.characters-area {
  position: relative;
  z-index: 20;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  height: 500px;
}

.left-footer {
  position: relative;
  z-index: 20;
  display: flex;
  align-items: center;
  gap: 24px;
}

.left-footer a {
  font-size: 13px;
  color: rgba(255, 255, 255, 0.45);
  text-decoration: none;
  transition: color 0.2s;
  cursor: pointer;
}

.left-footer a:hover {
  color: rgba(255, 255, 255, 0.85);
}

.decor-blur-1 {
  position: absolute;
  top: 15%;
  right: 10%;
  width: 300px;
  height: 300px;
  background: rgba(59, 130, 246, 0.25);
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
  z-index: 0;
}

.decor-blur-2 {
  position: absolute;
  bottom: 10%;
  left: 5%;
  width: 400px;
  height: 400px;
  background: rgba(30, 64, 175, 0.3);
  border-radius: 50%;
  filter: blur(100px);
  pointer-events: none;
  z-index: 0;
}

.decor-grid {
  position: absolute;
  inset: 0;
  background-image: linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
  background-size: 40px 40px;
  pointer-events: none;
  z-index: 1;
}

/* 右侧面板 */
.right-panel {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 32px;
  background: #ffffff;
}

.form-wrapper {
  width: 100%;
  max-width: 400px;
}

.mobile-logo {
  display: none;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-size: 18px;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 48px;
}

@media (max-width: 1024px) {
  .mobile-logo {
    display: flex;
  }
}

.mobile-logo-icon {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  background: #eff6ff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.form-header {
  text-align: center;
  margin-bottom: 40px;
}

.form-title {
  font-size: 26px;
  font-weight: 700;
  letter-spacing: -0.02em;
  color: #0f172a;
  margin: 0 0 10px 0;
  line-height: 1.3;
}

.form-subtitle {
  font-size: 14px;
  color: #6b7280;
  margin: 0;
  line-height: 1.6;
}

.login-form {
  margin-top: 20px;
}

.field-label {
  font-size: 13px;
  font-weight: 500;
  color: #374151;
  margin-bottom: 6px;
  letter-spacing: 0.2px;
}

:deep(.el-input__wrapper) {
  height: 48px !important;
  background: #fafafa !important;
  border: 1px solid #e5e7eb !important;
  border-radius: 10px !important;
  box-shadow: none !important;
}

:deep(.el-input__wrapper:hover) {
  border-color: #3b82f6 !important;
}

:deep(.el-input__wrapper.is-focus) {
  border-color: #1e40af !important;
  box-shadow: 0 0 0 3px rgba(30, 64, 175, 0.08) !important;
  background: #ffffff !important;
}

:deep(.el-input__inner) {
  font-size: 14px !important;
  color: #111827 !important;
}

:deep(.el-input__inner::placeholder) {
  color: #c0c4cc !important;
}

:deep(.el-form-item) {
  margin-bottom: 20px;
}

:deep(.el-form-item__error) {
  font-size: 13px !important;
  margin-top: 4px !important;
}

.eye-toggle {
  color: #6b7280;
  cursor: pointer;
  font-size: 16px;
  display: flex;
  align-items: center;
  transition: color 0.2s;
}

.eye-toggle:hover {
  color: #374151;
}

.error-item {
  margin-bottom: 16px;
}

.error-box {
  padding: 10px 14px;
  font-size: 13px;
  color: #dc2626;
  background: #fef2f2;
  border: 1px solid #fecaca;
  border-radius: 8px;
  width: 100%;
}

.submit-btn {
  height: 48px !important;
  font-size: 15px !important;
  font-weight: 600 !important;
  border-radius: 10px !important;
  background: #1e40af !important;
  border-color: #1e40af !important;
  letter-spacing: 1px;
  width: 100%;
}

.submit-btn:hover {
  background: #1d4ed8 !important;
  border-color: #1d4ed8 !important;
}

.divider {
  display: flex;
  align-items: center;
  gap: 12px;
  margin: 20px 0 0;
  color: #d1d5db;
  font-size: 13px;
}

.divider::before,
.divider::after {
  content: '';
  flex: 1;
  height: 1px;
  background: #e5e7eb;
}

.divider span {
  color: #9ca3af;
  white-space: nowrap;
}

.social-btn {
  height: 48px !important;
  font-size: 14px !important;
  border-radius: 10px !important;
  margin-top: 12px !important;
  background: #ffffff !important;
  border: 1px solid #e5e7eb !important;
  color: #374151 !important;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.social-btn:hover {
  background: #eff6ff !important;
  border-color: rgba(30, 64, 175, 0.25) !important;
  color: #1e40af !important;
}

.signup-row {
  text-align: center;
  font-size: 13px;
  color: #6b7280;
  margin-top: 28px;
}

.signup-link {
  color: #1e40af;
  font-weight: 500;
  text-decoration: none;
  cursor: pointer;
}

.signup-link:hover {
  text-decoration: underline;
  color: #1d4ed8;
}
</style>
