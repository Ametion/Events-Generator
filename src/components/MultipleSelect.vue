<template>
  <div class="select-container">
    <div class="select-input" @click="toggleDropdown">
        <div class="selected-options" v-if="selectedOptions.length > 0">
            <span v-for="(option, index) in selectedOptions" :key="option.value" class="selected-option">
                {{ option.label }} <i @click="removeSelectedOption(index)" class="fas fa-times"></i>
            </span>
        </div>
        <div class="placeholder" v-else>{{ placeholder }}</div>
        <i class="fas fa-angle-down"></i>
    </div>
    <div v-show="showDropdown" class="select-dropdown">
        <ul>
            <li v-for="option in options" :key="option.value" @click="selectOption(option)" :class="{ 'selected': isSelected(option) }">
                {{ option.label }}
            </li>
        </ul>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    options: {
      type: Array,
      required: true
    },
    placeholder: {
        type: String,
        default: 'Select an option',
      required: false
    },
  },
  data() {
    return {
      selectedOptions: [],
      showDropdown: false,
    }
  },
  methods: {
    toggleDropdown() {
        this.showDropdown = !this.showDropdown;
    },
    selectOption(option) {
        if (this.isSelected(option)) {
            this.removeSelectedOption(this.selectedOptions.indexOf(option));
        } else {
            this.selectedOptions.push(option);
        }
        this.showDropdown = false;
        this.$emit('select', this.selectedOptions);
    },
    removeSelectedOption(index) {
        this.selectedOptions.splice(index, 1);
        this.$emit('select', this.selectedOptions);
    },
    isSelected(option) {
        return this.selectedOptions.indexOf(option) !== -1;
    }
  }
}
</script>

<style scoped>
.select-container {
  position: relative;
  margin-top: 20px;
}
.select-input {
  display: flex;
  align-items: center;
  padding: 8px 12px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
  user-select: none;
}
.select-input .selected-options {
  display: flex;
  align-items: center;
}
.select-input .selected-options .selected-option {
  margin-right: 8px;
  padding: 4px 8px;
  background-color: #ccc;
  border-radius: 4px;
}
.select-input .selected-options .selected-option .fa-times {
  margin-left: 8px;cursor: pointer;
}
.select-input .placeholder {
  color: #ccc;
}
.select-input .fa-angle-down {
  margin-left: 8px;
}
.select-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  z-index: 1;
  overflow: hidden;
}
.select-dropdown ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
.select-dropdown li {
  padding: 8px 12px;
  cursor: pointer;
  user-select: none;
}
.select-dropdown li:hover {
  background-color: #f5f5f5;
}
.select-dropdown li.selected {
  background-color: #ccc;
}
</style>