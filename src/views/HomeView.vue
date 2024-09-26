<template>
  <div>
    <input type="text" v-model="newColumn" />
    <button @click="addColumn">Add Column</button>
  </div>
  <div class="flex gap-2">
    <Container
      v-for="(list, listName) in data"
      @drop="onDrop(listName, $event)"
      :get-child-payload="(index) => getChildPayload(listName, index)"
      orientation="vertical"
      class="border h-full w-1/2"
      group-name="list"
    >
      {{ list.title }}
      <input type="text" v-model="newCard" />
      <button @click="addCard(listName)">Add Card</button>
      <Draggable v-for="item in list.cards" :key="item.id">
        <div
          class="w-[200px] h-[200px] rounded-md bg-gray-200 border border-gray-800"
        >
          {{ item.title }}
        </div>
      </Draggable>
    </Container>
  </div>
  {{ data.list1 }} - {{ data.list2 }}
</template>

<script setup>
import { Draggable, Container } from "vue-dndrop";
import { v4 as uuidv4 } from "uuid";
const data = ref({});

const newColumn = ref("");
const newCard = ref("");

const addColumn = () => {
  data.value[uuidv4()] = {
    title: newColumn.value,
    cards: [],
  };
};

const addCard = (columnId) => {
  data.value[columnId].cards.push({
    id: uuidv4(),
    title: newCard.value,
  });
};

const onDrop = (arr, dragResult) => {
  console.log(arr, dragResult);
  const { removedIndex, addedIndex, payload } = dragResult;
  if (removedIndex === null && addedIndex === null) return;

  // let itemToAdd ;
  if (removedIndex !== null) {
    data.value[arr].splice(removedIndex, 1)[0];
  }

  if (addedIndex !== null) {
    data.value[arr].splice(addedIndex, 0, payload);
  }
};

const getChildPayload = (arr, index) => {
  console.log(arr);
  return data.value[arr].cards[index];
};
</script>

<style scoped></style>
