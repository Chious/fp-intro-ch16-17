<template>
  <div class="dropdown-container">
    <div class="dropdown" @click="toggleDropdown" :class="{ open: isOpen }">
      <div class="dropdown-header">
        <span class="dropdown-text">{{ selectedOption || placeholder }}</span>
        <i class="dropdown-arrow" :class="{ rotated: isOpen }">▼</i>
      </div>
      <transition name="dropdown">
        <ul v-if="isOpen" class="dropdown-list">
          <li
            v-for="option in options"
            :key="option.value"
            @click="selectOption(option)"
            class="dropdown-item"
            :class="{ selected: selectedOption === option.label }"
          >
            {{ option.label }}
          </li>
        </ul>
      </transition>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

interface DropdownOption {
  label: string;
  value: string;
}

const props = withDefaults(
  defineProps<{
    placeholder?: string;
    modelValue?: string;
    options?: DropdownOption[];
  }>(),
  {
    placeholder: "請選擇節目",
    options: () => [
      { id: 1, label: "FM 93.7", value: "fm937" },
      { id: 2, label: "FM 99.9", value: "fm999" },
    ],
  },
);

const emit = defineEmits<{
  "update:modelValue": [value: string];
  change: [option: DropdownOption];
}>();

const isOpen = ref(false);
const selectedOption = ref<string>("");

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};

const selectOption = (option: DropdownOption) => {
  selectedOption.value = option.label;
  isOpen.value = false;
  emit("update:modelValue", option.value);
  emit("change", option);
};

const closeDropdown = (event: Event) => {
  const target = event.target as HTMLElement;
  if (!target.closest(".dropdown")) {
    isOpen.value = false;
  }
};

onMounted(() => {
  document.addEventListener("click", closeDropdown);
  if (props.modelValue) {
    const option = props.options.find((opt) => opt.value === props.modelValue);
    if (option) {
      selectedOption.value = option.label;
    }
  }
});

onUnmounted(() => {
  document.removeEventListener("click", closeDropdown);
});
</script>

<style scoped>
.dropdown-container {
  position: relative;
  display: inline-block;
  min-width: 200px;
}

.dropdown {
  position: relative;
  cursor: pointer;
  user-select: none;
}

.dropdown-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  background-color: white;
  transition: all 0.2s ease;
  font-size: 14px;
}

.dropdown:hover .dropdown-header {
  border-color: #cbd5e0;
}

.dropdown.open .dropdown-header {
  border-color: #3182ce;
  box-shadow: 0 0 0 3px rgba(49, 130, 206, 0.1);
}

.dropdown-text {
  color: #2d3748;
  flex: 1;
}

.dropdown-arrow {
  color: #718096;
  font-size: 12px;
  transition: transform 0.2s ease;
  margin-left: 8px;
}

.dropdown-arrow.rotated {
  transform: rotate(180deg);
}

.dropdown-list {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  border: 2px solid #e2e8f0;
  border-top: none;
  border-radius: 0 0 8px 8px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  max-height: 200px;
  overflow-y: auto;
  margin: 0;
  padding: 0;
  list-style: none;
}

.dropdown-item {
  padding: 12px 16px;
  cursor: pointer;
  transition: background-color 0.15s ease;
  font-size: 14px;
  color: #2d3748;
}

.dropdown-item:hover {
  background-color: #f7fafc;
}

.dropdown-item.selected {
  background-color: #3182ce;
  color: white;
}

.dropdown-item:last-child {
  border-radius: 0 0 6px 6px;
}

/* 動畫效果 */
.dropdown-enter-active,
.dropdown-leave-active {
  transition: all 0.2s ease;
}

.dropdown-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.dropdown-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
