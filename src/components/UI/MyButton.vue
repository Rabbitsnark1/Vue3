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

<style scoped>
.btn {
    align-self: flex-end;
    padding: 10px 15px;
    background-color: none;
    color: mediumslateblue;
    border: 1px solid mediumslateblue;
}
</style>