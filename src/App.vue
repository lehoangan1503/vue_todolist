<template>
    <div class="app-container">
        <AsideComponent>
            <ProjectList ref="projectListRef"></ProjectList>
        </AsideComponent>
        <MainBoardComponent></MainBoardComponent>
    </div>
</template>
<script setup>
import { ref, onMounted, provide, computed } from "vue";
import AsideComponent from "./components/AsideComponent/AsideComponent.vue";
import ProjectList from "./components/AsideComponent/ProjectList.vue";
import MainBoardComponent from "./components/MainBoardComponent/MainBoardComponent.vue";

const projectListRef = ref(null);

const activeProjectName = computed(() => {
    return projectListRef.value?.currentActiveProjectName;
});
const deledtedItem = computed(() => {
    return projectListRef.value?.deletedItem;
});
provide("activeProjectName", activeProjectName);
provide("deletedItem", deledtedItem);
onMounted(() => {
    provide("activeProjectName", activeProjectName);
    provide("deletedItem", deledtedItem);
});
</script>
<style scoped>
.app-container {
    height: 100vh;
    display: flex;
}
</style>
