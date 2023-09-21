<script>
export function useSetName(edittingState, nameRef, currentName) {
    let newName = currentName;
    if (nameRef.value.value.length > 0) {
        newName = nameRef.value.value;
        edittingState.value = false;
    } else {
        edittingState.value = false;
    }
    return { newName };
}
</script>
