<template>
    <div class="section-pool">
        <div class="section-name">
            <div v-show="!editNameOn" class="hard-name" @click="editSectionName">
                {{ sectionName }}
            </div>
            <textarea
                v-show="editNameOn"
                name="section-name"
                ref="sectionNameRef"
                @input="resize"
                @focusin="resize"
                @focusout="setSectionName"
                :style="{ height: textareaHeight + 'px' }"
            ></textarea>
        </div>
        <div class="list-task">
            <draggable v-model="listTaskState" group="task" @start="drag = true" @end="drag = false" item-key="taskId">
                <template #item="{ element, index }">
                    <Task
                        class="task"
                        :taskindex="index"
                        :id="element.taskId"
                        :task="element"
                        @updateTaskName="updateTaskName"
                    ></Task>
                </template>
            </draggable>

            <div v-if="addNewCardShow" class="add-new-card" v-on-click-outside="clickOutside">
                <textarea
                    name="addNewCard"
                    id="addNewCard"
                    placeholder="Card title..."
                    ref="addNewCardRef"
                    v-model="addNewCardValue"
                    @keydown.enter.prevent="handleAddCardButton"
                ></textarea>
                <div class="button-group">
                    <div class="submit-add-card" @click="handleAddCardButton">Add card</div>
                    <PlusIcon class="cancel-add-card" @click="cancelAddCard"></PlusIcon>
                </div>
            </div>
        </div>
        <div v-show="!addNewCardShow" class="add-card-button" @click="showAddCard">
            <PlusIcon></PlusIcon>
            Add a card
        </div>
    </div>
</template>
<script setup>
import { nextTick, onUpdated, onMounted, ref, watch, getCurrentInstance } from "vue";
import Task from "./Task.vue";
import PlusIcon from "../../assets/icons/PlusIcon.vue";
import { useResize } from "../../composable/useResize.vue";
import { useEditName } from "../../composable/useEditName.vue";
import { useSetName } from "../../composable/useSetName.vue";
import { vOnClickOutside } from "@vueuse/components";
import Draggable from "vuedraggable";

const props = defineProps(["poolData", "updateTaskName"]);
const emit = defineEmits(["updateData", "addTaskCard", "handleListTaskChange"]);

const { sectionId, sectionName, listTask } = props.poolData;
const sectionNameState = ref(sectionName);
const sectionNameRef = ref(null);
const addNewCardRef = ref(null);
const textareaHeight = ref(35);
const editNameOn = ref(false);
const addNewCardShow = ref(false);
const addNewCardValue = ref("");
const listTaskState = ref(listTask);
const resize = async () => {
    useResize(textareaHeight, sectionNameRef);
};

const editSectionName = async () => {
    useEditName(editNameOn, sectionNameRef, sectionNameState);
};
const setSectionName = () => {
    const { newName } = useSetName(editNameOn, sectionNameRef, sectionName);
    if (newName != sectionName) {
        emit("updateData", [newName, sectionId]);
    }
};
const updateTaskName = (newData) => {
    props.updateTaskName(newData, sectionId);
};
const showAddCard = async () => {
    addNewCardShow.value = true;
    await nextTick();

    addNewCardRef.value.focus();
};
const clickOutside = () => {
    if (addNewCardValue.value.length > 0) {
        addNewCardShow.value = false;
        emit("addTaskCard", [addNewCardValue.value, sectionId]);
        addNewCardValue.value = "";
    } else {
        addNewCardShow.value = false;
    }
};
const handleAddCardButton = () => {
    if (addNewCardValue.value.length > 0) {
        emit("addTaskCard", [addNewCardValue.value, sectionId]);
        addNewCardValue.value = "";
    } else {
        addNewCardRef.value.focus();
    }
};
const cancelAddCard = () => {
    addNewCardShow.value = false;
};

watch(listTaskState, (newListTask, oldListTask) => {
    listTaskState.value = listTask;
    emit("handleListTaskChange", [newListTask, sectionId]);
});
onUpdated(() => {});
onMounted(() => {});
</script>
<style scoped>
.section-pool {
    border-radius: 10px;
    background-color: var(--pool-bg-color);
    padding: 0.5rem;
    width: 272px;
    max-height: 100%;
}
.section-name {
    margin-bottom: 10px;
}
.section-name textarea {
    display: block;
    width: 100%;
    height: 35px;
    max-height: 200px;
    resize: none;
    overflow: hidden;
    font-weight: 500;
    line-height: 1.5;
    color: var(--text-color);
    border-radius: 4px;
    border-color: transparent;
    border-width: 1px;
    background: rgb(0 78 127 / 20%);
    padding: 4px;
}
.section-name textarea:focus-visible {
    border-color: #6dbfff;
    outline: none;
}
.hard-name {
    line-height: 1.5;
    cursor: pointer;
    padding: 4px;
    font-weight: 500;
    border-radius: 4px;
    min-height: 35px;
    word-break: break-all;
    border: 1px solid transparent;
    max-height: 200px;
    overflow: auto;
}
.add-card-button {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 2px;
    border-radius: 4px;
    margin: 0.2rem 0;
    cursor: pointer;
}
.add-card-button:hover {
    background-color: var(--active-color);
}
.list-task {
    max-height: calc(580px - 35px - 28px);
    overflow: auto;
}
#addNewCard {
    width: 100%;
    color: var(--text-color);
    padding: 6px;
    border-radius: 4px;
    background: var(--task-bg);
    margin-bottom: 0;
    cursor: pointer;
    border: none;
    resize: none;
}
#addNewCard:focus-visible {
    outline: none;
}
::placeholder {
    color: var(--text-color);
}
.button-group {
    display: flex;
    justify-content: start;
    align-items: center;
    gap: 1rem;
}
.submit-add-card {
    cursor: pointer;
    color: black;
    background-color: #6dbfff;
    padding: 4px 6px;
    border-radius: 4px;
}
.cancel-add-card {
    transform: rotate(45deg);
    cursor: pointer;
}
.cancel-add-card:hover,
.submit-add-card:hover {
    opacity: 0.8;
}
</style>
