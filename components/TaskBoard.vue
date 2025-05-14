<script setup lang="ts">
import type { Column, Task } from "~/types";
import { ref } from "vue";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
import { useKeyModifier, useLocalStorage } from '@vueuse/core' 
const columns = useLocalStorage<Column[]>("taskBoard",[
  {
    title: "To Cook",
    id: nanoid(),
    tasks: [
      {
        title: "Pork Dumplings",
        createAt: new Date(),
        id: nanoid(),
      },
      {
        title: "Creamy Caramelized Onion Pasta",
        createAt: new Date(),
        id: nanoid(),
      },
    ],
  },
  { title: "Preparing for a picnic", id: nanoid(), tasks: [
      {
        title: "Buy pork neck and Beer",
        createAt: new Date(),
        id: nanoid(),
      },
      

  ] },
  { title: "Future plans", id: nanoid(), tasks: [
  {
        title: "Make Something...",
        createAt: new Date(),
        id: nanoid(),
      },


  ] },
  { title: "Meetings", id: nanoid(), tasks: [
  {
        title: "Job Interview",
        createAt: new Date(),
        id: nanoid(),
      },
  ] },
]);

columns.value.forEach(column => {
  column.tasks.forEach(task => {
    if (typeof task.createAt === 'string') {
      task.createAt = new Date(task.createAt);
    }
  });
});

const alt = useKeyModifier("Alt")

function createColumn(){
  const column: Column = {
    id: nanoid(),
    title: "",
    tasks:[]
  }
  columns.value.push(column);
  nextTick(() => {
    (document.querySelector(".column:last-of-type .titple-input"
    ) as HTMLInputElement).focus()

  })
}

</script>
<template>
  <div class="flex items-start overflow-x-auto gap-4">
    <draggable
      v-model="columns"
      group="columns"
      :animation="150"
      handle=".drag-handle"
      item-key="id"
      class="flex gap-4 items-start"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-300 p-5 rounded-xl min-w-[250px]">
          <header class="font-bold text-lg mb-4">
            <DragHandle />
            <input
              class="titple-input bg-transparent focus:bg-white rounded-md px-1 p-1 focus:p-1 w-4/5 focus:outline-none "
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="column.title == ''  
                ? (columns = columns.filter((c) => c.id !== column.id))
                : null
                "
              type="text"
              v-model="column.title"
            >
          </header>
          <draggable
      v-model="column.tasks"
      :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
      handle=".drag-handle"
      :animation="150"
      item-key="id"
    >
    <template #item="{element: task} : {element: Task}">
        <div><TaskBoardManage :task="task" @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)" /></div>     
    </template>
    
    </draggable>
          
          <footer>
            <NewTask @add="column.tasks.push($event)"/>
          </footer>
        </div>
        
      </template>
    </draggable>
    <button
      @click="createColumn"
      class=" bg-gray-200 whitespace-nowrap p-3 rounded-lg opacity-50 hover:opacity-70"
    >
    + Add Another Column
    </button>
  </div>
  
</template>
