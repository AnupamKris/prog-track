<template>
  <div class="copy p-0 flex bg-gray-200">
    <div class="w-[70px]"></div>
    <div class="flex flex-col w-full">
      <div class="h-[7vh] w-full"></div>
      <div
        class="flex flex-col bg-slate-50 w-full rounded-xl overflow-hidden shadow-xl p-5 h-full gap-5"
      >
        <div class="flex gap-3 items-center">
          <h1 class="text-xl font-semibold py-3">My Tasks</h1>
          <button
            class="flex items-center gap-1 bg-indigo-700 h-fit py-2 px-3 text-slate-50 rounded-md shadow-xl"
            @click="addItem"
          >
            <PlusCircle class="w-4 h-4" />
            <p class="text-xs">Add Task</p>
          </button>
          <button
            class="flex items-center gap-1 bg-indigo-700 h-fit py-2 px-3 text-slate-50 rounded-md shadow-xl"
            @click="addColumn"
          >
            <PlusCircle class="w-4 h-4" />
            <p class="text-xs">Add Column</p>
          </button>
        </div>
        <div class="flex bg-slate-50 w-full h-full gap-5">
          <div
            v-for="(container, key) in containers"
            :key="key"
            class="item h-full min-h-full"
          >
            <h3 class="text-sm text-slate-500">
              <EditableText v-model="container.name" />
            </h3>
            <Container
              class="bg-slate-100 rounded-lg px-4 py-2 h-full"
              :behaviour="key === 'items1' ? 'move' : undefined"
              group-name="1"
              :get-child-payload="(index) => getChildPayload(key, index)"
              @drop="(e) => onDrop(key, e)"
            >
              <Draggable
                v-for="(item, index) in container.items"
                :key="item.id"
                class="p-6 flex justify-center bg-white rounded-xl flex-col mt-2"
                :class="
                  (index !== 0 ? '!mt-4 ' : ' ') +
                  key.toLowerCase().split(' ').join('')
                "
              >
                <span
                  class="bg-indigo-200 text-indigo-800 text-xs rounded-full py-1.5 px-4 w-fit"
                >
                  UI Design
                </span>
                <div class="text-xl mt-4">
                  {{ item.data }}
                </div>
                <div class="flex items-center gap-3">
                  <div class="progress w-full h-1 bg-gray-200 rounded-full">
                    <div
                      class="bg-indigo-500 h-full rounded-full"
                      :style="{ width: `${73}%` }"
                    ></div>
                  </div>
                  <p class="flex items-center text-xs">73%</p>
                </div>
                <div class="flex items-center justify-between gap-3 mt-4">
                  <div class="flex items-center gap-2">
                    <Clock class="w-4 h-4 text-slate-400" />
                    <p class="text-xs text-slate-400">Due : Feb 03, 2024</p>
                  </div>
                  <div class="flex">
                    <span
                      class="bg-indigo-200 text-indigo-800 border-2 border-white text-[8px] rounded-full min-h-6 min-w-6 flex items-center justify-center scale-[1.25]"
                    >
                      AK
                    </span>
                    <span
                      class="bg-green-200 text-green-800 border-2 border-white text-[8px] rounded-full min-h-6 min-w-6 flex items-center justify-center scale-[1.25]"
                    >
                      AK
                    </span>
                  </div>
                </div>
              </Draggable>
            </Container>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { Container, Draggable } from "vue-dndrop";
import { applyDrag, generateItems } from "@/utils/helpers";
import { Clock } from "lucide-vue-next";
import { PlusCircle } from "lucide-vue-next";

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
  containers.value[key].items = applyDrag(
    containers.value[key].items,
    dropResult
  );
};

const getChildPayload = (key, index) => containers.value[key].items[index];

const addColumn = () => {
  const newKey = `items${Object.keys(containers.value).length + 1}`;
  containers.value[newKey] = {
    name: `Column ${Object.keys(containers.value).length + 1}`,
    items: [],
  };
};

const addItem = () => {
  const key = Object.keys(containers.value)[0];
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

.items1 {
  @apply border-l-4 border-red-300;
}
.items2 {
  @apply border-l-4 border-blue-300;
}
.items3 {
  @apply border-l-4 border-green-300;
}
</style>
