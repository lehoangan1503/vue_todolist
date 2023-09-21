<template>
    <div ref="taskRef">
        <div class="task-name" v-show="!editNameOn">{{ taskName }}</div>
        <textarea
            v-show="editNameOn"
            name="task-name"
            ref="taskNameRef"
            v-model="taskName"
            @input="resize"
            @focusin="resize"
            @focusout="setName"
            @keydown.enter="setName"
            :style="{ height: textareaHeight + 'px' }"
        ></textarea>
        <EditIcon @click="editName" v-show="!editNameOn"></EditIcon>
    </div>
</template>
<script setup>
import { ref, onUpdated, onMounted, getCurrentInstance } from "vue";
import EditIcon from "../../assets/icons/EditIcon.vue";
import { useResize } from "../../composable/useResize.vue";
import { useEditName } from "../../composable/useEditName.vue";
import { useSetName } from "../../composable/useSetName.vue";
const props = defineProps(["task"]);
const emit = defineEmits(["updateTaskName"]);
const { taskName } = props.task;

const taskRef = ref(null);
const taskNameState = ref(taskName);
const taskNameRef = ref(null);
const textareaHeight = ref(35);
const editNameOn = ref(false);

const resize = () => {
    useResize(textareaHeight, taskNameRef);
};

const editName = async () => {
    useEditName(editNameOn, taskNameRef, taskNameState);
};
const setName = () => {
    const { newName } = useSetName(editNameOn, taskNameRef, task);
    if (newName != task) {
        emit("updateTaskName", [{ newName, taskId: task.taskId }]);
    }
};
onUpdated(() => {});
onMounted(() => {});
</script>
<style scoped>
.task {
    position: relative;
    padding: 6px;
    border-radius: 4px;
    background: var(--task-bg);
    margin-bottom: 0.5rem;
    cursor: pointer;
}
.task:hover {
    background: var(--task-bg-hover);
}
.task:hover svg {
    display: block;
}
.task svg {
    display: none;
    position: absolute;
    right: 0.25rem;
    top: 0.25rem;
    padding: 4px;
    border-radius: 4px;
    width: 28px;
    height: 28px;
}
.task svg:hover {
    background: var(--aside-bg);
}
.task textarea {
    display: block;
    width: 100%;
    height: 36px;
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
.task textarea:focus-visible {
    border-color: #6dbfff;
    outline: none;
}
.task-name {
    padding: 4px;
}
</style>
