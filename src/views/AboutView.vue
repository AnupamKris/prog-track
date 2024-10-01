<template>
  <div class="copy p-0 flex">
    <div
      v-for="(container, key) in containers"
      :key="key"
      class="item h-screen max-w-[100px]"
    >
      <h3>{{ container.name }}</h3>
      <Container
        :class="{ 'source-container': key === 'items1' }"
        class="bg-gray-200 rounded-md p-2 h-full"
        :behaviour="key === 'items1' ? 'copy' : undefined"
        group-name="1"
        :get-child-payload="(index) => getChildPayload(key, index)"
        @drop="(e) => onDrop(key, e)"
      >
        <Draggable
          v-for="item in container.items"
          :key="item.id"
          class="min-h-[160px] p-2 flex justify-center items-center"
        >
          <div class="draggable-item">
            {{ item.data }}
          </div>
        </Draggable>
      </Container>
      <button @click="() => addItem(key)" class="add-item-btn">Add Item</button>
    </div>
    <button @click="addColumn" class="add-column-btn">Add Column</button>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { Container, Draggable } from "vue-dndrop";
import { applyDrag, generateItems } from "@/utils/helpers";

const containers = ref({
  items1: {
    name: "Source Items",
    items: [],
  },
  items2: {
    name: "Column 2",
    items: [],
  },
  items3: {
    name: "Column 3",
    items: [],
  },
});

const onDrop = (key, dropResult) => {
  if (key !== "items1") {
    containers.value[key].items = applyDrag(
      containers.value[key].items,
      dropResult
    );
  }
};

const getChildPayload = (key, index) => containers.value[key].items[index];

const addColumn = () => {
  const newKey = `items${Object.keys(containers.value).length + 1}`;
  containers.value[newKey] = {
    name: `Column ${Object.keys(containers.value).length + 1}`,
    items: generateItems(15, (i) => ({
      id: `${newKey}${i}`,
      data: `Draggable ${newKey} - ${i}`,
    })),
  };
};

const addItem = (key) => {
  const newIndex = containers.value[key].items.length;
  containers.value[key].items.push({
    id: `${key}${newIndex}`,
    data: `New Item - ${newIndex + 1}`,
  });
};
</script>

<style>
.copy {
  display: flex;
  gap: 1rem;
  height: 100vh;
}
.item {
  flex: 1;
  min-width: 200px;
  display: flex;
  flex-direction: column;
}
.source-container {
  background-color: #f0f0f0;
}
.add-column-btn,
.add-item-btn {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 1rem;
}
.add-item-btn {
  background-color: #2196f3;
}
h3 {
  margin-top: 0;
  margin-bottom: 0.5rem;
}

.draggable-item {
  @apply bg-gray-50 rounded-md p-2 h-full;
  width: 100%;
  height: 100%;
  min-height: 160px;
}
</style>
