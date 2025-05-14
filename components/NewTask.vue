<script setup lang="ts">
import type { Task } from '~/types';
import { nanoid } from 'nanoid';

const emit = defineEmits<{
    (e: "add", paylod: Task): void;
}>();

const focused = ref(false);
const title = ref("")

function createTask(e: Event){
    if (title.value.trim()){
        e.preventDefault();
        emit("add", {
            title: title.value.trim(),
            createAt: new Date(),
            id: nanoid(),
        } as Task);
    }

    title.value = "";
}
</script>
<template>
    <div>
        <textarea 
            v-model="title"
            @keydown.tab="createTask"
            @keyup.enter="createTask"
            class="hover:placeholder:text-black placeholder:text-gray-700 font-semibold focus:bg-white focus:rounded-lg focus:shadow resize-none rounded w-full border-none p-2  bg-transparent overflow-hidden cursor-pointer"
            :class="{
                'h-7': !focused,
                'h-20': !focused,
            }"
            style="outline: none !important;"
            @focus="focused = true"
            @blur="focused = false"
            :placeholder="!focused ? '+ Add A Card' : 'Enter a title for this card'"
        />
       
    </div>
</template>