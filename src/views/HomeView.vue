<script setup lang="ts">
import CheckBoxNumberInput from "@/components/CheckBoxNumberInput.vue";
import { computed, ref } from "vue";
import areaCodeList from "@/assets/areaCode.json";

const score = ref(0);
const year = ref(2000);
const month = ref(0);
const day = ref(0);
const phoneNumber = ref(9012345678);
const population = ref(0);
const formattedPhoneNumber = computed(() => formatPhone(phoneNumber.value));

const numberFormatter = new Intl.NumberFormat("en-US");
const formatNum = (num: number) => numberFormatter.format(num);

// 固定電話: 市外局番-市内局番-4桁番号
const homePhoneReg = new RegExp(
  `^(?<area>${areaCodeList.join("|")})(?<localArea>.+)(?<rest>.{4})`
);
// 携帯電話: 0x0-市内局番-4桁番号
const mobilePhoneReg = new RegExp(
  `^(?<area>[^0]0)(?<localArea>.+)(?<rest>.{4})`
);

// 電話番号の形式が正しい場合にハイフン区切りにして返す
const formatPhone = (num: number) => {
  if (num === 0) return "0";
  const strNum = String(num);

  const homePhoneMatchedGroups = strNum.match(homePhoneReg)?.groups;
  if (homePhoneMatchedGroups) {
    const { area, localArea, rest } = homePhoneMatchedGroups;
    // 固定電話は市外局番と市内局番が合計5桁
    if (area.length + localArea.length === 5)
      return `0${area}-${localArea}-${rest}`;
  }

  const mobilePhoneMatchedGroups = strNum.match(mobilePhoneReg)?.groups;
  if (mobilePhoneMatchedGroups) {
    const { area, localArea, rest } = mobilePhoneMatchedGroups;
    return `0${area}-${localArea}-${rest}`;
  }

  // マッチしない場合はハイフン無しで返す
  return `0${strNum}`;
};
const onSubmit = () => {
  const date = [
    year.value,
    String(month.value).padStart(2, "0"),
    String(day.value).padStart(2, "0"),
  ].join("-");
  alert(`セミナーの点数：${score.value}
生年月日：${date}
電話番号：${formattedPhoneNumber.value}
地球の人口：${population.value}
で入力を受け付けました
（特に送信してません）`);
};

const speech = (text: string) => {
  const uttr = new SpeechSynthesisUtterance();
  uttr.text = text;
  window.speechSynthesis.cancel();
  window.speechSynthesis.speak(uttr);
};

const dateValidator = {
  cb: () => {
    const date = new Date(`${year.value}-${month.value}-${day.value}`);
    // Invalid date、または2/30->3/2のように月が合わなくなる場合にエラーにする
    return date.getMonth() + 1 !== month.value;
  },
  msg: "存在しない日付です",
};

const phoneValidator = {
  cb: () => {
    return !/-/.test(formattedPhoneNumber.value);
  },
  msg: "有効な番号ではありません",
};
</script>

<template>
  <main class="flex flex-col items-center p-20 gap-10">
    <form
      @submit.prevent="onSubmit"
      class="flex flex-col gap-16 border p-10 surveyForm"
    >
      <header>
        <h2 class="text-3xl text-center">わくわくアンケートフォーム</h2>
      </header>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">Q1. 当セミナーの感想（5点満点。二進法で）</h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ score }}
              </p>
              <CheckBoxNumberInput :min="1" :max="5" v-model="score" />
            </div>
          </div>
        </div>
      </section>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">Q2. 生年月日を入力してください（二進法で）</h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ formatNum(year) }}
              </p>
              <CheckBoxNumberInput :min="1900" :max="2100" v-model="year" />
            </div>
            <p class="text-xl">年</p>
          </div>
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ month }}
              </p>
              <CheckBoxNumberInput :min="1" :max="12" v-model="month" />
            </div>
            <p class="text-xl">月</p>
          </div>
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ day }}
              </p>
              <CheckBoxNumberInput
                :min="1"
                :max="31"
                v-model="day"
                :validator="dateValidator"
              />
            </div>
            <p class="text-xl">日</p>
          </div>
        </div>
      </section>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">Q3. 連絡のつく日本国内の電話番号（二進法で）</h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ formattedPhoneNumber }}
              </p>
              <CheckBoxNumberInput
                :min="0"
                :max="9999999999"
                v-model="phoneNumber"
                :validator="phoneValidator"
              />
            </div>
          </div>
        </div>
      </section>

      <section class="flex flex-col gap-4">
        <header>
          <h3 class="text-2xl">
            Q4. 地球の人口はどのくらいだと思いますか？（二進法で）
          </h3>
        </header>

        <div class="flex flex-row gap-8">
          <div class="flex flex-row items-center gap-2">
            <div class="flex flex-col">
              <p class="text-2xl text-center">
                {{ formatNum(population) }}
              </p>
              <CheckBoxNumberInput
                :min="10000000"
                :max="10000000000"
                v-model="population"
              />
            </div>
            <p class="text-xl">人</p>
            <button
              type="button"
              @click="speech(`${population}人`)"
              class="flex flex-row border p-1 gap-2"
            >
              <img
                alt="再生"
                class="h-6 w-6"
                src="@/assets/IconSpeaker.svg"
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
    <footer class="text-center underline">
      <a href="https://github.com/miyaoka/bad-form">Source</a>
    </footer>
  </main>
</template>

<style scoped>
.surveyForm:has(:invalid) button[type="submit"] {
  background: #999;
}
</style>
