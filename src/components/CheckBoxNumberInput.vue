<script setup lang="ts">
import { computed, ref, watch } from "vue";

const props = defineProps<{
  modelValue?: number;
  min?: number;
  max?: number;
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
});
</script>

<template>
  <div class="flex flex-row">
    <label :key="n" v-for="n in bitLength" class="flex flex-col p-1">
      <span class="select-none text-gray-500">
        {{ bitLength - n + 1 }}
      </span>
      <input type="checkbox" name="" v-model="bitArray[n - 1]" />
    </label>
  </div>
</template>
