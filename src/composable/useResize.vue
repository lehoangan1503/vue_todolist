<script>
import { nextTick } from "vue";
export async function useResize(textareaHeight, textareaRef) {
    textareaHeight.value = 35;
    await nextTick();
    textareaHeight.value = textareaRef.value.scrollHeight + 2;
    if (textareaHeight.value > 200) {
        textareaRef.value.style["overflow"] = "auto";
    } else {
        textareaRef.value.style["overflow"] = "hidden";
    }
}
</script>
