<template>
  <input :value="modelValue" @input="updateInput" class="input" type="text">
</template>

<script>
export default {
    name: 'my-input',
    props: {
        modelValue: [String, Number]
    },
    methods: {
        updateInput(event) {
            this.$emit('update:modelValue', event.target.value)
        }
    }
}
</script>

<style>
    .input {
    width: 100%;
    border: 2px solid #cdcdcd;
    margin-top: 15px;
    padding: 10px;
    outline: none;
    border-radius: 10px;
    }
</style>