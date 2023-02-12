<script setup lang="ts">
import CheckBoxNumberInput from "@/components/CheckBoxNumberInput.vue";
import IconSpeaker from "@/components/icons/IconSpeaker.vue";
import { ref } from "vue";
import { areaCodeList } from "./areaCode";

const year = ref(2000);
const month = ref(0);
const mday = ref(0);
const phoneNumber = ref(9012345678);
const population = ref(0);

const numberFormatter = new Intl.NumberFormat("en-US");
const formatNum = (num: number) => numberFormatter.format(num);

const phoneReg = new RegExp(
  `^(?<area>${["[^0]0", ...areaCodeList].join("|")})(?<rest1>.+)(?<rest2>.{4})`
);
const formatPhone = (num: number) => {
  if (num === 0) return "0";

  const groups = String(num).match(phoneReg)?.groups;
  if (!groups) return `0${num}`;

  const { area, rest1, rest2 } = groups;
  return `0${area}-${rest1}-${rest2}`;
};
const onSubmit = () => {};

const speech = (text: string) => {
  const uttr = new SpeechSynthesisUtterance();
  uttr.text = text;
  window.speechSynthesis.speak(uttr);
};

const dateValidator = {
  cb: () => {
    const date = new Date(`${year.value}-${month.value}-${mday.value}`);
    return date.getMonth() + 1 !== month.value;
  },
  msg: "存在しない日付です",
};

const phoneValidator = {
  cb: () => {
    return !/-/.test(formatPhone(phoneNumber.value));
  },
  msg: "有効な番号ではありません",
};
</script>

<template>
  <main class="flex justify-center p-20">
    <form @submit.prevent="onSubmit" class="flex flex-col gap-16 border p-10">
      <header>
        <h2 class="text-3xl text-center">わくわくアンケートフォーム</h2>
      </header>
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
              <CheckBoxNumberInput
                :min="1"
                :max="31"
                v-model="mday"
                :validator="dateValidator"
              />
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
                :validator="phoneValidator"
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
                :min="10000000"
                :max="10000000000"
                v-model="population"
              />
              <p class="text-2xl text-center">
                {{ formatNum(population) }}
              </p>
            </div>
            <p class="text-xl">人</p>
            <button
              type="button"
              @click="speech(`${population}人`)"
              class="flex flex-row border p-1 gap-2"
            >
              <IconSpeaker
                alt="再生"
                class="h-6 w-6"
                src="https://em-content.zobj.net/thumbs/120/openmoji/338/speaker-low-volume_1f508.png"
                @click="speech(`${population}人`)"
              />
              再生
            </button>
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
