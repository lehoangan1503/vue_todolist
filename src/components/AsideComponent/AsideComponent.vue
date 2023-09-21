<template>
    <div class="aside-container p-3">
        <div class="fw-bold text-center mb-4">Your projects</div>
        <slot></slot>
    </div>
</template>
<script setup></script>
<style scoped>
.aside-container {
    width: 300px;
    height: inherit;
    background-color: var(--aside-bg);
}
</style>
