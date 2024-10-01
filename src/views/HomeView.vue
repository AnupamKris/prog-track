<template>
  {{ data }}
  <button @click="clearData" class="border rounded-md p-2">Clear Data</button>
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
      class="border h-full w-1/2 !min-h-[500px]"
      group-name="list"
      :key="list.id"
    >
      {{ list.title }}
      <input type="text" v-model="newCard" />
      <button @click="addCard(listName)">Add Card</button>
      <Draggable v-for="(item, index) in list.cards" :key="item.id">
        <div
          class="w-[200px] h-[200px] rounded-md bg-gray-200 border border-gray-800"
        >
          {{ item.title }}
        </div>
      </Draggable>
    </Container>
  </div>
</template>

<script setup>
import { Draggable, Container } from "vue-dndrop";
import { v4 as uuidv4 } from "uuid";
const data = useLocalStorage("data", {});

const clearData = () => {
  localStorage.removeItem("data");
};

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
  const { removedIndex, addedIndex, payload } = dragResult;
  console.log(removedIndex, addedIndex, payload);
  if (removedIndex === null && addedIndex === null) return;

  // let itemToAdd ;
  if (removedIndex !== null) {
    console.log(data.value[arr]);

    data.value[arr].cards.splice(removedIndex, 1)[0];
  }

  if (addedIndex !== null) {
    // add card to column
    data.value[arr].cards.splice(addedIndex, 0, payload);
  }
};

const getChildPayload = (arr, index) => {
  console.log(arr, index, data.value[arr]);
  return data.value[arr].cards[index];
};
</script>

<style scoped></style>
