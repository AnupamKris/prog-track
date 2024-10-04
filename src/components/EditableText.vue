<template>
  <div>
    <p v-if="!editing" @click="edit">{{ value }}</p>
    <input
      v-else
      type="text"
      v-model="value"
      ref="inputRef"
      class="outline-none bg-transparent"
      @blur="editing = false"
      @keydown.enter="editing = false"
    />
  </div>
</template>

<script setup lang="js">
// const props = defineProps({
//   text: {
//     type: String,
//     required: true,
//   },
// });

const value = defineModel();
const editing = ref(false);
const inputRef = ref(null);
const edit = () => {
  editing.value = true;
  nextTick(() => {
    inputRef.value.focus();
  });
};
</script>

<style scoped></style>
