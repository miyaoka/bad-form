<script setup lang="ts">
import { computed, ref, watch } from "vue";

const props = defineProps<{
  modelValue?: number;
  min?: number;
  max?: number;
  validator?: {
    cb: () => boolean;
    msg: string;
  };
}>();
const emit = defineEmits<{
  (e: "update:modelValue", value: number): void;
}>();

const getBitLength = (num: number) => {
  return (Math.log2(num) + 1) | 0;
};
const min = computed(() => Math.max(0, props.min ?? 0));
const max = computed(() => Math.min(2 ** 53, props.max ?? 10));
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
const validate = () => {
  if (!inputEl.value) return;

  if (props.validator) {
    if (props.validator.cb()) {
      inputEl.value.setCustomValidity(props.validator.msg);
      return;
    }
  }

  if (decimalValue.value < min.value) {
    inputEl.value.setCustomValidity(`${min.value}以上の値で入力してください`);
    return;
  }
  if (decimalValue.value > max.value) {
    inputEl.value.setCustomValidity(`${max.value}以下の値で入力してください`);
    return;
  }
  inputEl.value.setCustomValidity("");
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
  <div class="flex flex-row">
    <label :key="n" v-for="n in bitLength" class="flex flex-col p-1">
      <input
        type="checkbox"
        name=""
        v-model="bitArray[n - 1]"
        :ref="(el) => setInputRef(el as HTMLInputElement, n)"
      />
      <span class="select-none text-gray-500">
        {{ bitLength - n }}
      </span>
    </label>
  </div>
</template>
