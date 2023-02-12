<script setup lang="ts">
import CheckBoxNumberInput from "@/components/CheckBoxNumberInput.vue";
import { ref } from "vue";

const onSubmit = () => {};
const initialYear = ref(2000);
</script>

<template>
  <main>
    <form @submit.prevent="onSubmit" class="flex flex-col gap-4">
      <header>
        <h3 class="text-2xl">Q1. 生年月日を入力してください（二進法で）</h3>
      </header>
      <div class="flex flex-row gap-8">
        <div class="flex flex-row items-center gap-2">
          <CheckBoxNumberInput :min="1900" :max="2100" v-model="initialYear" />
          <p class="text-xl">年</p>
        </div>
        <div class="flex flex-row items-center gap-2">
          <CheckBoxNumberInput :min="1" :max="12" />
          <p class="text-xl">月</p>
        </div>
        <div class="flex flex-row items-center gap-2">
          <CheckBoxNumberInput :min="1" :max="31" />
          <p class="text-xl">日</p>
        </div>
      </div>
      <footer class="flex justify-center">
        <button type="submit" class="bg-blue-500 text-white px-10 py-2">
          送信
        </button>
      </footer>
    </form>
  </main>
</template>
