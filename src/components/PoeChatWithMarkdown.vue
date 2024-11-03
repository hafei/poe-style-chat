<template>
  <div class="flex h-full bg-gray-900 text-gray-100">
    <!-- Sidebar -->
    <div class="w-64 flex flex-col bg-gray-800 border-r border-gray-700">
      <!-- New Chat Button -->
      <button @click="createNewChat" class="m-4 p-2 bg-purple-600 hover:bg-purple-700 text-white rounded-md flex items-center justify-center transition-colors duration-200">
        <PlusIcon class="w-5 h-5 mr-2" />
        New Chat
      </button>

      <!-- Chat List -->
      <div class="flex-1 overflow-y-auto">
        <div v-for="chat in chats" :key="chat.id" @click="selectChat(chat.id)"
             :class="['p-3 cursor-pointer hover:bg-gray-700 transition-colors duration-200',
                      currentChatId === chat.id ? 'bg-gray-700' : '']">
          <MessageSquareIcon class="w-5 h-5 inline-block mr-2" />
          {{ chat.title }}
        </div>
      </div>

      <!-- Config Button -->
      <div class="m-4">
        <button @click="toggleConfig" class="w-full p-2 bg-gray-700 hover:bg-gray-600 text-white rounded-md flex items-center justify-center transition-colors duration-200">
          <SettingsIcon class="w-5 h-5 mr-2" />
          Config
        </button>
      </div>
    </div>

    <!-- Main Chat Area -->
    <div class="flex-1 flex flex-col">
      <!-- Chat messages -->
      <div class="flex-1 overflow-y-auto p-4 space-y-4" ref="chatContainer">
        <transition-group name="fade">
          <div v-for="message in currentMessages" :key="message.id" class="flex items-start space-x-2">
            <div :class="[
              'flex-shrink-0 w-8 h-8 rounded-full flex items-center justify-center',
              message.isUser ? 'bg-purple-600' : 'bg-green-600'
            ]">
              <UserIcon v-if="message.isUser" class="w-5 h-5 text-white" />
              <BotIcon v-else class="w-5 h-5 text-white" />
            </div>
            <div :class="[
              'p-3 rounded-lg max-w-[80%] overflow-hidden',
              message.isUser ? 'bg-purple-700 text-white' : 'bg-gray-800 text-gray-100'
            ]">
              <div v-html="renderMarkdown(message.content)" class="markdown-body"></div>
            </div>
          </div>
        </transition-group>
      </div>

      <!-- Input area -->
      <div class="border-t border-gray-700 p-4 bg-gray-800">
        <form @submit.prevent="sendMessage" class="flex flex-col space-y-2">
          <div class="flex space-x-2">
            <textarea
                v-model="newMessage"
                placeholder="Type your message... (Markdown supported)"
                class="flex-1 bg-gray-700 text-white rounded-lg px-4 py-2 focus:outline-none focus:ring-2 focus:ring-purple-600 resize-none"
                rows="3"
                @keydown.enter="submitOnEnter"
            ></textarea>
            <button
                type="submit"
                class="bg-purple-600 text-white rounded-full p-2 hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-purple-600 self-end"
            >
              <SendIcon class="w-5 h-5" />
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Config Component -->
    <ConfigComponent v-if="showConfig" @close="toggleConfig" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick } from 'vue'
import { UserIcon, BotIcon, SendIcon, PlusIcon, MessageSquareIcon, SettingsIcon } from 'lucide-vue-next'
import { marked } from 'marked'
import hljs from 'highlight.js'
import DOMPurify from 'dompurify'
import ConfigComponent from './ConfigComponent.vue' // Import the ConfigComponent

// Configure marked to use highlight.js for code highlighting
marked.setOptions({
  highlight: function(code, lang) {
    const language = hljs.getLanguage(lang) ? lang : 'plaintext';
    return hljs.highlight(code, { language }).value;
  },
  langPrefix: 'hljs language-'
});

const chats = ref([
  { id: 1, title: "General Chat", messages: [
      { id: 1, content: "Hello! How can I assist you today? You can use **Markdown** in your messages.", isUser: false }
    ]},
])
const currentChatId = ref(1)
const newMessage = ref('')
const chatContainer = ref(null)

const currentMessages = computed(() => {
  const chat = chats.value.find(c => c.id === currentChatId.value)
  return chat ? chat.messages : []
})

const createNewChat = () => {
  const newId = chats.value.length + 1
  chats.value.push({
    id: newId,
    title: `New Chat ${newId}`,
    messages: [{ id: 1, content: "How can I help you? Remember, you can use **Markdown** formatting!", isUser: false }]
  })
  currentChatId.value = newId
}

const selectChat = (id) => {
  currentChatId.value = id
  nextTick(() => scrollToBottom())
}

const sendMessage = async () => {
  if (newMessage.value.trim() === '') return

  const chat = chats.value.find(c => c.id === currentChatId.value)
  if (!chat) return

  // Add user message
  chat.messages.push({
    id: Date.now(),
    content: newMessage.value,
    isUser: true
  })

  // Clear input
  newMessage.value = ''

  // Simulate AI response
  await nextTick()
  scrollToBottom()

  setTimeout(() => {
    chat.messages.push({
      id: Date.now(),
      content: "I'm an AI assistant. How can I help you with your question? Here's an example of a code block:\n\n```python\nprint('Hello, World!')\n```",
      isUser: false
    })
    scrollToBottom()
  }, 1000)
}

const renderMarkdown = (content) => {
  const rawHtml = marked(content)
  return DOMPurify.sanitize(rawHtml)
}

const scrollToBottom = () => {
  if (chatContainer.value) {
    chatContainer.value.scrollTop = chatContainer.value.scrollHeight
  }
}

const submitOnEnter = (event) => {
  if (!event.shiftKey) { // Check if Shift is not pressed
    event.preventDefault(); // Prevent default Enter behavior
    sendMessage(); // Call the sendMessage function
  }
}

const showConfig = ref(false)

const toggleConfig = () => {
  showConfig.value = !showConfig.value
}

onMounted(() => {
  scrollToBottom()
})
</script>

<style>
@import 'highlight.js/styles/github-dark.css';

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.markdown-body {
  color: inherit;
  font-size: 14px;
}

.markdown-body pre {
  background-color: #1f2937;
  border-radius: 6px;
  padding: 12px;
  margin: 8px 0;
}

.markdown-body code {
  background-color: #374151;
  border-radius: 3px;
  padding: 2px 4px;
}

.markdown-body p {
  margin-bottom: 8px;
}

.markdown-body ul, .markdown-body ol {
  padding-left: 20px;
  margin-bottom: 8px;
}

.markdown-body h1, .markdown-body h2, .markdown-body h3, .markdown-body h4, .markdown-body h5, .markdown-body h6 {
  margin-top: 16px;
  margin-bottom: 8px;
}

.markdown-body blockquote {
  border-left: 4px solid #4b5563;
  padding-left: 16px;
  margin: 8px 0;
  color: #9ca3af;
}

.markdown-body table {
  border-collapse: collapse;
  margin-bottom: 8px;
}

.markdown-body th, .markdown-body td {
  border: 1px solid #4b5563;
  padding: 6px 12px;
}

.markdown-body img {
  max-width: 100%;
  height: auto;
}
</style>