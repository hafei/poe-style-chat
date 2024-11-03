<script setup lang="ts">
import { ref } from 'vue';

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
  <div class="config-item" :class="{ active: config.isActive }">
    <div class="config-header" @click="toggleExpand">
      <div class="header-left">
        <button class="expand-button" :class="{ expanded: isExpanded }">▶</button>
        <span class="config-name">{{ config.name }}</span>
      </div>
      <div class="header-right">
        <button 
          class="status-toggle"
          :class="{ active: config.isActive }"
          @click.stop="toggleActive"
        >
          {{ config.isActive ? 'Active' : 'Inactive' }}
        </button>
        <button class="remove-button" @click.stop="emit('remove')">×</button>
      </div>
    </div>

    <div class="config-content" v-show="isExpanded">
      <div class="form-grid">
        <div class="form-group">
          <label>Name:</label>
          <input
            type="text"
            v-model="config.name"
            @input="emit('update', { name: config.name })"
            placeholder="Configuration name"
          >
        </div>

        <div class="form-group">
          <label>Model:</label>
          <select
            v-model="config.model"
            @change="emit('update', { model: config.model })"
          >
            <option value="">Select a model</option>
            <option v-for="model in models" :key="model" :value="model">
              {{ model }}
            </option>
          </select>
        </div>

        <div class="form-group">
          <label>URL:</label>
          <input
            type="url"
            v-model="config.url"
            @input="emit('update', { url: config.url })"
            placeholder="API endpoint URL"
          >
        </div>

        <div class="form-group">
          <label>API Key:</label>
          <input
            type="password"
            v-model="config.apiKey"
            @input="emit('update', { apiKey: config.apiKey })"
            placeholder="Enter API key"
          >
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.config-item {
  background: #ffffff;
  border-radius: 6px;
  border: 1px solid #e2e8f0;
  overflow: hidden;
  transition: all 0.3s ease;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}

.config-item.active {
  border-color: #42b883;
}

.config-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0.75rem;
  background: #f8fafc;
  cursor: pointer;
  user-select: none;
  min-height: 40px;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 0.35rem;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 0.35rem;
}

.expand-button {
  background: none;
  border: none;
  font-size: 0.7rem;
  padding: 0;
  cursor: pointer;
  transition: transform 0.2s;
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.expand-button.expanded {
  transform: rotate(90deg);
}

.config-name {
  font-weight: 500;
  color: #2c3e50;
  font-size: 0.9rem;
}

.status-toggle {
  padding: 0.2em 0.6em;
  border-radius: 10px;
  font-size: 0.75rem;
  border: 1px solid #cbd5e1;
  background: white;
  cursor: pointer;
  transition: all 0.2s;
}

.status-toggle.active {
  background: #42b883;
  color: white;
  border-color: #42b883;
}

.remove-button {
  background: none;
  border: none;
  color: #94a3b8;
  font-size: 1.1rem;
  cursor: pointer;
  padding: 0 0.2rem;
  transition: color 0.2s;
  line-height: 1;
}

.remove-button:hover {
  color: #ef4444;
}

.config-content {
  padding: 0.75rem;
  border-top: 1px solid #e2e8f0;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.75rem;
}

.form-group {
  margin: 0;
}

.form-group label {
  display: block;
  margin-bottom: 0.25rem;
  color: #64748b;
  font-size: 0.8rem;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 0.35rem 0.5rem;
  border: 1px solid #e2e8f0;
  border-radius: 4px;
  font-size: 0.85rem;
  transition: border-color 0.2s;
  height: 32px;
}

.form-group input:focus,
.form-group select:focus {
  outline: none;
  border-color: #42b883;
}

.form-group input::placeholder {
  color: #94a3b8;
}
</style>