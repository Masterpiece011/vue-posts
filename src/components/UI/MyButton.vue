<template>
    <button class="btn">
        <slot></slot>
    </button>
</template>

<script>
export default {
    name: 'my-button'
}
</script>

<style>
.btn {
    margin-top: 15px;
    background-color: transparent;
    padding: 10px;
    border: 2px solid #cdcdcd;
    cursor: pointer;
    transition: all .3s ease;
    border-radius: 8px;
    }

    .btn:hover {
        background: #cdcdcd;
    }
</style>