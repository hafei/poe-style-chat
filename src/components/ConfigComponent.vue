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
  <div class="max-w-2xl mx-auto p-8 dark:bg-gray-900">
    <header class="flex justify-between items-center mb-8">
      <h1 class="text-2xl font-bold text-gray-800 dark:text-white">Configuration Settings</h1>
      <button class="bg-green-500 text-white font-medium py-2 px-4 rounded hover:bg-green-600 transition duration-200" @click="addConfig">
        Add Configuration
      </button>
    </header>

    <div class="flex flex-col gap-4">
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