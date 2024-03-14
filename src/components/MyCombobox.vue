<script setup>
import { ref, computed } from 'vue'
import {
  Combobox,
  ComboboxInput,
  ComboboxButton,
  ComboboxOptions,
  ComboboxOption,
  TransitionRoot
} from '@headlessui/vue'

const emit = defineEmits(['update:modelValue', 'change'])
const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  options: {
    type: Array,
    default() {
      return []
    }
  },
  placeholder: { type: String, default: '' }
})
const query = ref('')
const selectedOption = computed(() => {
  const { options, modelValue } = props
  if (options && options.length && modelValue) {
    return options.find((o) => o.value === modelValue)
  }
  return null
})

const filteredOptions = computed(() =>
  query.value === ''
    ? props.options
    : props.options.filter((o) =>
        o.label
          .toLowerCase()
          .replace(/\s+/g, '')
          .includes(query.value.toLowerCase().replace(/\s+/g, ''))
      )
)

const onUpdateSelected = (option) => {
  emit('update:modelValue', option.value)
}

const onDelete = () => {
  emit('update:modelValue', '')
}

const openBtnRef = ref()
const onClickInput = () => {
  openBtnRef.value && openBtnRef.value.el.click()
}
</script>

<template>
  <Combobox
    as="div"
    :model-value="selectedOption"
    class="dt-combobox"
    @update:model-value="onUpdateSelected"
  >
    <div class="combobox-container">
      <div class="combox-input-container">
        <ComboboxInput
          class="form-control text-truncate"
          :placeholder="placeholder"
          :display-value="
            (option) => {
              return option ? option.label : ''
            }
          "
          @change="query = $event.target.value"
          @click="onClickInput"
        />
        <button v-if="selectedOption" type="button" class="show-btn" @click="onDelete">
          <span aria-hidden="true">&times;</span>
        </button>
        <ComboboxButton ref="openBtnRef" class="d-none" aria-hidden="true" />
      </div>
      <TransitionRoot
        leave="options-leave"
        leave-from="opacity-100"
        leave-to="opacity-0"
        @after-leave="query = ''"
      >
        <div class="options-box">
          <ComboboxOptions class="combobox-options">
            <div v-if="filteredOptions.length === 0 && query !== ''" class="not-found-text">
              Nothing found.
            </div>
            <ComboboxOption
              v-for="option in filteredOptions"
              :key="option.value"
              v-slot="{ selected, active }"
              :value="option"
            >
              <div
                class="list-item"
                :class="{
                  'item-active': active,
                  'item-inactive': !active,
                  'item-selected': selected
                }"
              >
                <span class="item-label" :class="{ 'selected-item': selected }">
                  {{ option.label }}
                </span>
              </div>
            </ComboboxOption>
          </ComboboxOptions>
        </div>
      </TransitionRoot>
    </div>
  </Combobox>
</template>

<style scoped>
.combobox-container {
  position: relative;
  background-color: transparent;
}

.combox-input-container {
  width: 100%;
  overflow: hidden;
  background-color: transparent;
}

.form-control {
  border-radius: 0;
  padding: 4px 1.5rem 4px 8px;
  font-size: 14px;
}

.options-leave {
  transition-property: background-color, border-color, color, fill, stroke, opacity, box-shadow,
    transform;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 300ms;
  transition-duration: 100ms;
  transition-timing-function: cubic-bezier(0.4, 0, 1, 1);
}

.opacity-100 {
  opacity: 1;
}

.opacity-0 {
  opacity: 0;
}

.combobox-options {
  overflow: auto;
  width: fit-content;
  min-width: 100%;
  line-height: 1.5rem;
  background-color: #ffffff;
  box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.05);
  padding: 0.25rem;
  min-height: 24px;
  max-height: 15rem;
}

.option-list {
  max-height: 300px;
  overflow: auto;
  margin: -0.25rem;
}

.not-found-text {
  position: relative;
  color: #374151;
  cursor: default;
  user-select: none;
}

.list-item {
  position: relative;
  padding: 0.2em 0.5em;
  cursor: default;
  user-select: none;
  list-style: none;
  list-style-position: outside;
}

.item-active,
.item-active.item-selected {
  color: #ffffff;
  background-color: #374151;
}

.item-inactive {
  color: #111827;
}

.item-label {
  display: block;
  white-space: nowrap;
}

.seleced-item {
  font-weight: 500;
}

.show-btn {
  display: flex;
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  padding-right: 0.5rem;
  align-items: center;
  color: #9ca3af;
  background-color: transparent;
}

.item-selected {
  background-color: rgb(18, 202, 202);
  color: #fff;
}

.show-dropdown-icon {
  color: #9ca3af;
  width: 1.25rem;
  height: 1.25rem;
}

.options-box {
  position: absolute;
  margin-top: 0.25rem;
  border-radius: 0.375rem;
  background-color: #ffffff;
  box-shadow:
    0 10px 15px -3px rgba(0, 0, 0, 0.1),
    0 4px 6px -2px rgba(0, 0, 0, 0.05);
  z-index: 100;
  min-width: 100%;
}
</style>
