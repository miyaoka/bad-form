<script setup lang="ts">
import { computed, ref, watch } from "vue";

const props = defineProps<{
  modelValue?: number;
  min?: number;
  max?: number;
  validator?: () => string;
}>();
const emit = defineEmits<{
  (e: "update:modelValue", value: number): void;
}>();

const getBitLength = (num: number) => {
  return num.toString(2).length;
};
const min = computed(() => Math.max(0, props.min ?? 0));
const max = computed(() => Math.min(Number.MAX_SAFE_INTEGER, props.max ?? 10));
const bitLength = computed(() => getBitLength(max.value));
const bitArray = ref<boolean[]>([]);
const decimalValue = computed(() => {
  const bitStr = bitArray.value.map((check) => (check ? 1 : 0)).join("");
  return parseInt(bitStr, 2);
});
const inputEl = ref<HTMLInputElement | null>(null);
const setInputRef = (el: HTMLInputElement, i: number) => {
  // 配列中央のinputを対象にする
  if (i !== Math.ceil(bitLength.value / 2)) return;
  inputEl.value = el;
  validate();
};
const errorMsg = ref("");
const validate = () => {
  if (!inputEl.value) return;

  const getErrorMsg = () => {
    if (decimalValue.value < min.value) {
      return `${min.value}以上の値で入力してください`;
    }
    if (decimalValue.value > max.value) {
      return `${max.value}以下の値で入力してください`;
    }
    // custom validation
    return props.validator?.() ?? "";
  };
  errorMsg.value = getErrorMsg();
  inputEl.value.setCustomValidity(errorMsg.value);
};

watch(
  bitLength,
  (len) => {
    bitArray.value = [...Array(len)].map(() => false);
  },
  {
    immediate: true,
  }
);
watch(
  () => props.modelValue,
  (val) => {
    const decimal = Math.floor(val ?? 0);
    const binary = decimal.toString(2);

    const paddedBinary = binary
      .padStart(bitLength.value)
      .slice(-bitLength.value);

    bitArray.value = paddedBinary.split("").map((item) => item === "1");
  },
  { immediate: true }
);

watch(decimalValue, (val) => {
  emit("update:modelValue", val);
  validate();
});
</script>

<template>
  <div class="flex flex-row flex-wrap checkBoxContainer">
    <label
      :key="n"
      v-for="n in bitLength"
      class="flex flex-col items-center p-0.5 sm:p-1"
    >
      <input
        type="checkbox"
        v-model="bitArray[n - 1]"
        :ref="(el) => setInputRef(el as HTMLInputElement, n)"
      />
      <span class="select-none text-gray-500">
        {{ bitLength - n }}
      </span>
    </label>
    <div
      class="absolute top-[100%] mt-1 overflow-hidden text-xs h-3 leading-3 text-red-500"
      :title="errorMsg"
    >
      {{ errorMsg }}
    </div>
  </div>
</template>

<style scoped>
.checkBoxContainer:has(:invalid) {
  outline: 1px solid #f00;
}
</style>
