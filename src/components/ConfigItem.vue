<script setup lang="ts">
import { ref } from 'vue';
import VSelect from 'vue-select';

interface Config {
  id: string;
  name: string;
  url: string;
  model: string;
  apiKey: string;
  isActive: boolean;
}

const props = defineProps<{
  config: Config;
}>();

const emit = defineEmits<{
  (e: 'update', value: Partial<Config>): void;
  (e: 'remove'): void;
}>();

const isExpanded = ref(true);

const models = [
  'gpt-3.5-turbo',
  'gpt-4',
  'gpt-4-turbo',
  'claude-2',
  'claude-instant'
];

const toggleExpand = () => {
  isExpanded.value = !isExpanded.value;
};

const toggleActive = () => {
  emit('update', { isActive: !props.config.isActive });
};
</script>

<template>
  <div class="bg-white dark:bg-gray-800 rounded-lg border border-gray-300 dark:border-gray-700 shadow-sm overflow-hidden transition-all duration-300" :class="{ 'border-green-500': config.isActive }">
    <div class="flex justify-between items-center p-2 bg-gray-100 dark:bg-gray-700 cursor-pointer select-none min-h-[40px]" @click="toggleExpand">
      <div class="flex items-center gap-1">
        <button class="transform transition-transform duration-200" :class="{ 'rotate-90': isExpanded }">▶</button>
        <span class="font-medium text-gray-800 dark:text-white">{{ config.name }}</span>
      </div>
      <div class="flex items-center gap-1">
        <button 
          class="px-2 py-1 rounded-full text-sm border border-gray-300 dark:border-gray-600 transition-all duration-200"
          :class="{ 'bg-green-500 text-white border-green-500': config.isActive }"
          @click.stop="toggleActive"
        >
          {{ config.isActive ? 'Active' : 'Inactive' }}
        </button>
        <button class="text-gray-500 dark:text-gray-400 hover:text-red-500 transition-colors duration-200" @click.stop="emit('remove')">×</button>
      </div>
    </div>

    <div class="p-3 border-t border-gray-300 dark:border-gray-700" v-show="isExpanded">
      <div class="grid grid-cols-2 gap-3">
        <div class="space-y-1">
          <label class="block text-sm text-gray-600 dark:text-gray-300">Name:</label>
          <input
            type="text"
            v-model="config.name"
            @input="emit('update', { name: config.name })"
            placeholder="Configuration name"
            class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
          >
        </div>

        <div class="space-y-1">
          <label class="block text-sm text-gray-600 dark:text-gray-300">Model:</label>
          <v-select
            v-model="config.model"
            :options="models"
            taggable
            @input="emit('update', { model: config.model })"
            class="w-full"
          />
        </div>

        <div class="space-y-1">
          <label class="block text-sm text-gray-600 dark:text-gray-300">URL:</label>
          <input
            type="url"
            v-model="config.url"
            @input="emit('update', { url: config.url })"
            placeholder="API endpoint URL"
            class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
          >
        </div>

        <div class="space-y-1">
          <label class="block text-sm text-gray-600 dark:text-gray-300">API Key:</label>
          <input
            type="password"
            v-model="config.apiKey"
            @input="emit('update', { apiKey: config.apiKey })"
            placeholder="Enter API key"
            class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
          >
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Remove existing styles as they are replaced by Tailwind CSS */
</style>