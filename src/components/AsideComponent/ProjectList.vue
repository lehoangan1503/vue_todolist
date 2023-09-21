<template>
    <div class="project-list-container">
        <ProjectItem
            v-for="item in projectList"
            :projectItem="item"
            :key="item.id"
            :id="item.id"
            :class="{ active: item.currentActive }"
            @click="changeCurrentActiveItem"
            @delteProject="deleteItem(item.id)"
        ></ProjectItem>
        <textarea
            v-show="addNewProjectShow"
            name="addNewProject"
            id="addNewProject"
            placeholder="Project name..."
            ref="addNewProjectRef"
            v-model="addNewProjectValue"
            @focusout="handleAddProject"
            @keydown.enter.prevent="handleAddProject"
        ></textarea>
    </div>
    <div class="add-project-button" @click="addNewProject">Add New Project</div>
</template>
<script setup>
import { ref, computed, nextTick, onMounted } from "vue";
import ProjectItem from "./ProjectItem.vue";

const projectList = ref([]);
onMounted(() => {
    const storedData = JSON.parse(localStorage.getItem("projectList"));
    if (storedData === null) {
        localStorage.setItem(
            "projectList",
            JSON.stringify([
                { id: 1, projectName: "HomeWork", currentActive: true },
                { id: 2, projectName: "Chatgpt", currentActive: false },
                { id: 3, projectName: "Opendream", currentActive: false },
            ])
        );

        projectList.value.push(...JSON.parse(localStorage.getItem("projectList")));
    } else {
        projectList.value.push(...storedData);
    }
});
const addNewProjectShow = ref(false);
const addNewProjectValue = ref("");
const addNewProjectRef = ref(null);
const deletedItemState = ref(null);
const handleAddProject = () => {
    if (addNewProjectValue.value.length > 0) {
        addNewProjectShow.value = false;
        if (projectList.value.length > 0) {
            projectList.value.forEach((project) => {
                project.currentActive = false;
            });
            projectList.value.push({
                id: projectList.value[projectList.value.length - 1].id + 1,
                projectName: addNewProjectValue.value,
                currentActive: true,
            });
        } else {
            projectList.value.push({
                id: 1,
                projectName: addNewProjectValue.value,
                currentActive: true,
            });
        }
        addNewProjectValue.value = "";
    } else {
        addNewProjectShow.value = false;
    }

    localStorage.removeItem("projectList");
    localStorage.setItem("projectList", JSON.stringify(projectList.value));
};
const addNewProject = async () => {
    addNewProjectShow.value = true;
    await nextTick();
    addNewProjectRef.value.focus();
};
const deleteItem = (id) => {
    if (projectList.value.length > 0) {
        projectList.value.forEach((project, index) => {
            if (project.id === id && project.currentActive) {
                [deletedItemState.value] = projectList.value.splice(index, 1);
                if (projectList.value.length > 0) {
                    projectList.value[0].currentActive = true;
                }
            } else if (project.id === id) {
                [deletedItemState.value] = projectList.value.splice(index, 1);
            }
        });
        localStorage.removeItem("projectList");
        localStorage.setItem("projectList", JSON.stringify(projectList.value));
    }
};
const deletedItem = computed(() => {
    let deletedItem = null;
    deletedItem = deletedItemState.value;

    return deletedItem;
});
const changeCurrentActiveItem = (event) => {
    if (event.target.id) {
        projectList.value.forEach((project) => {
            project.currentActive = false;
            if (project.id == event.target.id) {
                project.currentActive = true;
            }
        });
        localStorage.removeItem("projectList");
        localStorage.setItem("projectList", JSON.stringify(projectList.value));
    }
};
const currentActiveProjectName = computed(() => {
    const activeProject = {};

    projectList.value.forEach((project) => {
        if (project.currentActive) {
            activeProject.id = project.id;
            activeProject.projectName = project.projectName;
            activeProject.currentActive = project.currentActive;
        }
    });

    return activeProject;
});

defineExpose({ currentActiveProjectName, deletedItem });
</script>
<style scoped>
.add-project-button {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.2rem;
    padding: 6px 8px;
    border-radius: 4px;
    margin: 0.2rem 0;
    cursor: pointer;
    background-color: #6dbfff;
    color: black;
}
#addNewProject {
    width: 100%;
    color: var(--text-color);
    padding: 6px;
    border-radius: 4px;
    background: rgb(0 78 127 / 20%);
    margin-bottom: 0;
    border: 2px solid transparent;
    cursor: pointer;

    resize: none;
}
#addNewProject:focus-visible {
    border-color: #6dbfff;
    outline: none;
}
::placeholder {
    color: var(--text-color);
}
</style>
