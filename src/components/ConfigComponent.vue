<script setup lang="ts">
import { ref } from 'vue';
import ConfigItem from './ConfigItem.vue';

interface Config {
  id: string;
  name: string;
  url: string;
  model: string;
  apiKey: string;
  isActive: boolean;
}

const configs = ref<Config[]>([
  {
    id: '1',
    name: 'Default Config',
    url: 'https://api.example.com',
    model: 'gpt-3.5-turbo',
    apiKey: '',
    isActive: true
  }
]);

const addConfig = () => {
  configs.value.push({
    id: Date.now().toString(),
    name: `Config ${configs.value.length + 1}`,
    url: '',
    model: '',
    apiKey: '',
    isActive: false
  });
};

const removeConfig = (id: string) => {
  configs.value = configs.value.filter(config => config.id !== id);
};

const updateConfig = (id: string, updatedConfig: Partial<Config>) => {
  const index = configs.value.findIndex(config => config.id === id);
  if (index !== -1) {
    configs.value[index] = { ...configs.value[index], ...updatedConfig };
  }
};
</script>

<template>
  <div class="config-page">
    <header class="header">
      <h1>Configuration Settings</h1>
      <button class="add-button" @click="addConfig">
        Add Configuration
      </button>
    </header>

    <div class="configs-container">
      <TransitionGroup name="list">
        <ConfigItem
          v-for="config in configs"
          :key="config.id"
          :config="config"
          @update="updateConfig(config.id, $event)"
          @remove="removeConfig(config.id)"
        />
      </TransitionGroup>
    </div>
  </div>
</template>

<style scoped>
.config-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.header h1 {
  margin: 0;
  font-size: 1.8rem;
  color: #2c3e50;
}

.add-button {
  background-color: #42b883;
  color: white;
  border: none;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.add-button:hover {
  background-color: #3aa876;
}

.configs-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>