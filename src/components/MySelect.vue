<template>
  <div class="pickerWrapper">
    <select v-model="selectedOption" @change="handleChange">
      <option v-for="option in options" :key="option.value" :value="option.value">
        {{ option.label }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  props: {
    options: {
      type: Array,
      required: true
    },
  },
  data() {
    return {
      selectedOption: null,
    }
  },
  methods: {
    handleChange(event) {
      this.$emit('select', event.target.value)
    }
  }
}
</script>

<style scoped>
.pickerWrapper{
  width: 400px;
  margin-top: 35px;
  margin-right: -100px;
}

.pickerWrapper select{
  width: 300px;
  height: 35px;
  border-radius: 5px;
}
</style>