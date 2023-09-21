<script>
import { nextTick } from "vue";
export async function useEditName(edittingState, nameRef, nameState) {
    edittingState.value = true;
    nameRef.value.value = nameState.value;
    await nextTick();
    nameRef.value.focus();
    nameRef.value.selectionStart = nameRef.value.value.length;
}
</script>
