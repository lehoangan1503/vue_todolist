<template>
    <div class="project-item">
        <div class="project-name">{{ projectName }}</div>
        <DeleteIcon @click="emit('delteProject')"></DeleteIcon>
    </div>
</template>

<script setup>
import { ref } from "vue";
import DeleteIcon from "../../assets/icons/DeleteIcon.vue";
const props = defineProps(["projectItem"]);
const emit = defineEmits(["delteProject"]);
const { id, projectName } = props.projectItem;
</script>

<style scoped>
.project-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 5px;
    margin: 5px 0;
    border-radius: 4px;
    cursor: pointer;
}
.project-item.active {
    background-color: var(--active-color);
}
.project-item:hover {
    background: var(--gray-hover-color);
}
.project-item .project-name {
    pointer-events: none;
}
.project-item svg {
    display: none;
}

.project-item:hover svg {
    display: block;
}
</style>
