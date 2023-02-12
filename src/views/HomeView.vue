<script setup lang="ts">
import CheckBoxNumberInput from "@/components/CheckBoxNumberInput.vue";
import { ref } from "vue";

const year = ref(2000);
const month = ref(0);
const mday = ref(0);
const phoneNumber = ref(9012345678);
const population = ref(0);

const numberFormatter = new Intl.NumberFormat("en-US");
const formatNum = (num: number) => numberFormatter.format(num);
const formatPhone = (num: number) => {
  if (num === 0) return "0";
  return `0${num}`;
};
const onSubmit = () => {};
</script>

<template>
  <main>
    <form @submit.prevent="onSubmit" class="flex flex-col gap-16">
      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">Q1. 生年月日を入力してください（二進法で）</h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <CheckBoxNumberInput :min="1900" :max="2100" v-model="year" />
              <p class="text-2xl text-center">
                {{ formatNum(year) }}
              </p>
            </div>
            <p class="text-xl">年</p>
          </div>
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <CheckBoxNumberInput :min="1" :max="12" v-model="month" />
              <p class="text-2xl text-center">
                {{ month }}
              </p>
            </div>
            <p class="text-xl">月</p>
          </div>
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <CheckBoxNumberInput :min="1" :max="31" v-model="mday" />
              <p class="text-2xl text-center">
                {{ mday }}
              </p>
            </div>
            <p class="text-xl">日</p>
          </div>
        </div>
      </section>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">Q2. 連絡のつく日本国内の電話番号（二進法で）</h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <CheckBoxNumberInput
                :min="0"
                :max="9999999999"
                v-model="phoneNumber"
              />
              <p class="text-2xl text-center">
                {{ formatPhone(phoneNumber) }}
              </p>
            </div>
          </div>
        </div>
      </section>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">
            Q3. 地球の人口はどのくらいだと思いますか？（二進法で）
          </h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <CheckBoxNumberInput
                :min="0"
                :max="10000000000"
                v-model="population"
              />
              <p class="text-2xl text-center">
                {{ formatNum(population) }}
              </p>
            </div>
            <p class="text-xl">人</p>
          </div>
        </div>
      </section>

      <footer class="flex justify-center">
        <button type="submit" class="bg-blue-500 text-white px-10 py-2">
          送信
        </button>
      </footer>
    </form>
  </main>
</template>
