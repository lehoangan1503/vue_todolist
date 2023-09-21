<template>
    <div class="main-board-container">
        <div class="project-name p-3">
            <h1 class="h4 fw-bold mb-0">{{ projectName }}</h1>
        </div>
        <div class="section-container">
            <SectionPoolComponent
                v-for="section in currentMainBoard"
                :key="`${section.sectionName}-${projectId}`"
                :id="`${section.sectionName}-${projectId}`"
                :poolData="section"
                :updateTaskName="updateTaskName"
                @addTaskCard="addTaskCard"
                @updateData="updateData"
                @handleListTaskChange="handleListTaskChange"
            >
            </SectionPoolComponent>
        </div>
    </div>
</template>
<script setup>
import { inject, nextTick, ref, watch, onUpdated, onMounted } from "vue";
import SectionPoolComponent from "./SectionPoolComponent.vue";
const activeProjectName = inject("activeProjectName");
const deletedItem = inject("deletedItem");
const mainBoardDatas = [];

onMounted(() => {
    const storedData = JSON.parse(localStorage.getItem("mainBoardDatas"));

    if (storedData === null) {
        localStorage.setItem(
            "mainBoardDatas",
            JSON.stringify([
                {
                    projectId: 1,
                    mainBoardData: [
                        { sectionId: 1, sectionName: "Todo", listTask: [] },
                        { sectionId: 2, sectionName: "Doing", listTask: [] },
                        { sectionId: 3, sectionName: "Done", listTask: [] },
                    ],
                },
                {
                    projectId: 2,
                    mainBoardData: [
                        { sectionId: 1, sectionName: "Todo", listTask: [] },
                        { sectionId: 2, sectionName: "Doing", listTask: [] },
                        { sectionId: 3, sectionName: "Done", listTask: [] },
                    ],
                },
                {
                    projectId: 3,
                    mainBoardData: [
                        { sectionId: 1, sectionName: "Todo", listTask: [] },
                        { sectionId: 2, sectionName: "Doing", listTask: [] },
                        { sectionId: 3, sectionName: "Done", listTask: [] },
                    ],
                },
            ])
        );

        mainBoardDatas.push(...JSON.parse(localStorage.getItem("mainBoardDatas")));
    } else {
        mainBoardDatas.push(...storedData);
    }
});
const currentMainBoard = ref(null);
const projectName = ref("");
const projectId = ref(null);

const getCurrentMainBoardData = (id) => {
    let isNewProject = true;
    projectId.value = id;
    mainBoardDatas.forEach((item) => {
        if (item.projectId === id) {
            currentMainBoard.value = item.mainBoardData;
            isNewProject = false;
        }
    });
    if (isNewProject) {
        const newProject = {
            projectId: id,
            mainBoardData: [
                { sectionId: 1, sectionName: "Todo", listTask: [] },
                { sectionId: 2, sectionName: "Doing", listTask: [] },
                { sectionId: 3, sectionName: "Done", listTask: [] },
            ],
        };
        mainBoardDatas.push(newProject);
        currentMainBoard.value = mainBoardDatas[mainBoardDatas.length - 1].mainBoardData;
    }
};

watch(activeProjectName, (newactiveProjectName) => {
    projectName.value = newactiveProjectName.projectName;
    if (newactiveProjectName.id) {
        getCurrentMainBoardData(newactiveProjectName.id);
    } else {
        currentMainBoard.value = null;
    }
});
watch(deletedItem, (newDeletedItem) => {
    if (newDeletedItem) {
        mainBoardDatas.forEach((item, index) => {
            if (item.projectId === newDeletedItem.id) {
                mainBoardDatas.splice(index, 1);
                localStorage.removeItem("mainBoardDatas");
                localStorage.setItem("mainBoardDatas", JSON.stringify(mainBoardDatas));
            }
        });
    }
});
watch(
    currentMainBoard,
    (newMainBoardData) => {
        localStorage.removeItem("mainBoardDatas");
        localStorage.setItem("mainBoardDatas", JSON.stringify(mainBoardDatas));
    },
    { deep: true }
);
const updateData = (newData) => {
    currentMainBoard.value.forEach((el, index) => {
        if (el.sectionId === newData[1]) {
            currentMainBoard.value[index].sectionName = newData[0];
        }
    });
};
const updateTaskName = (newData, sectionId) => {
    currentMainBoard.value.forEach(async (el, index) => {
        if (el.sectionId === sectionId) {
            currentMainBoard.value[index].listTask.forEach((task) => {
                if (task.taskId === newData.taskId) {
                    task.taskName = newData.newName;
                }
            });
        }
    });
};
const handleListTaskChange = (newData) => {
    const [newListTask, sectionId] = newData;

    currentMainBoard.value.forEach((el, index) => {
        if (el.sectionId === sectionId) {
            currentMainBoard.value[index].listTask.splice(
                0,
                currentMainBoard.value[index].listTask.length,
                ...newListTask
            );
        }
    });
};

const addTaskCard = (taskInfor) => {
    const [taskName, sectionId] = taskInfor;

    currentMainBoard.value.forEach(async (el, index) => {
        if (el.sectionId === sectionId) {
            currentMainBoard.value[index].listTask.push({ taskId: Date.now(), taskName });
        }
    });
};
</script>
<style scoped>
.main-board-container {
    flex: 1;
    background-image: url("../../assets/images/La-Gruyere-district-Fribourg-canton-Switzerland.webp");
    background-size: cover;
}
.project-name {
    text-align: center;
    background-color: var(--black-blur-color);
    color: var(--dark-text-color);
    height: 60px;
}
.section-container {
    width: 100%;
    height: calc(100% - 60px);
    overflow-x: auto;
    padding: 1rem;
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    gap: 1rem;
}
</style>
