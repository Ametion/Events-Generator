<template>
  <div class="countryPickerWrapper">
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
      selectedOption: [],
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
.countryPickerWrapper{
  width: 400px;
  margin-top: 35px;
}

.countryPickerWrapper select{
  width: 300px;
  height: 35px;
  border-radius: 5px;
}
</style>