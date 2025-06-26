<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-2xl mx-auto space-y-6">
      
      <!-- shadcn-vue Components Test -->
      <div class="bg-card p-6 rounded-lg border shadow-sm">
        <h1 class="text-2xl font-bold text-foreground mb-6">
          🎨 shadcn-vue + Tailwind v3 Test
        </h1>
        
        <!-- Button variants test -->
        <div class="space-y-4">
          <div class="flex flex-wrap gap-3">
            <Button>Default Button</Button>
            <Button variant="secondary">Secondary</Button>
            <Button variant="destructive">Destructive</Button>
            <Button variant="outline">Outline</Button>
            <Button variant="ghost">Ghost</Button>
            <Button variant="link">Link</Button>
          </div>
          
          <!-- Button sizes -->
          <div class="flex flex-wrap gap-3 items-center">
            <Button size="sm">Small</Button>
            <Button>Default</Button>
            <Button size="lg">Large</Button>
          </div>
        </div>
      </div>

      <!-- Login Form with shadcn-vue components -->
      <div class="bg-card p-6 rounded-lg border shadow-sm">
        <h2 class="text-xl font-semibold text-card-foreground mb-4">Login Test</h2>
        
        <form @submit.prevent="testLogin" class="space-y-4">
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">Email</label>
            <input 
              v-model="loginForm.email"
              type="email" 
              class="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
              placeholder="Enter your email"
            />
          </div>
          
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">Password</label>
            <input 
              v-model="loginForm.password"
              type="password" 
              class="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
              placeholder="Enter your password"
            />
          </div>
          
          <div class="flex gap-3 pt-2">
            <Button 
              type="submit" 
              :disabled="loading"
              class="flex-1"
            >
              {{ loading ? 'Testing...' : 'Test Login' }}
            </Button>
            
            <Button 
              type="button" 
              variant="outline"
              @click="testBackend"
              class="flex-1"
            >
              Test Backend
            </Button>
          </div>
        </form>
        
        <div v-if="message" :class="messageClass" class="mt-4 p-3 rounded-md">
          {{ message }}
        </div>
      </div>

      <!-- Status -->
      <div class="bg-card p-6 rounded-lg border shadow-sm">
        <h3 class="text-lg font-semibold text-card-foreground mb-4">🛠️ System Status</h3>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
          <div class="flex justify-between items-center">
            <span class="text-sm text-muted-foreground">Tailwind CSS v3</span>
            <span class="text-green-600 font-medium">✅</span>
          </div>
          <div class="flex justify-between items-center">
            <span class="text-sm text-muted-foreground">shadcn-vue</span>
            <span class="text-green-600 font-medium">✅</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import axios from 'axios'
import Button from '@/components/ui/button/Button.vue'

export default {
  name: 'App',
  components: {
    Button
  },
  setup() {
    const loginForm = ref({
      email: '',
      password: ''
    })
    
    const loading = ref(false)
    const message = ref('')
    const messageType = ref('')
    
    const messageClass = computed(() => {
      if (messageType.value === 'error') {
        return 'bg-destructive/15 text-destructive border border-destructive/20'
      } else if (messageType.value === 'success') {
        return 'bg-green-100 text-green-800 border border-green-200'
      }
      return 'bg-blue-100 text-blue-800 border border-blue-200'
    })

    const testLogin = async () => {
      if (!loginForm.value.email || !loginForm.value.password) {
        message.value = 'Please enter email and password'
        messageType.value = 'error'
        return
      }
      
      loading.value = true
      message.value = ''
      
      try {
        const response = await axios.post('http://localhost:3000/auth/login', {
          email: loginForm.value.email,
          password: loginForm.value.password
        })
        
        message.value = '🎉 Login successful!'
        messageType.value = 'success'
        console.log('Login response:', response.data)
        
      } catch (error) {
        message.value = `❌ Login failed: ${error.response?.data?.message || error.message}`
        messageType.value = 'error'
      } finally {
        loading.value = false
      }
    }

    const testBackend = async () => {
      message.value = ''
      
      try {
        const response = await axios.get('http://localhost:3000/users')
        message.value = `🚀 Backend connected! Found ${response.data.length} users.`
        messageType.value = 'success'
      } catch (error) {
        message.value = '🔌 Failed to connect to backend.'
        messageType.value = 'error'
      }
    }

    return {
      loginForm,
      loading,
      message,
      messageClass,
      testLogin,
      testBackend
    }
  }
}
</script>