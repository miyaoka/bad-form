<script setup lang="ts">
import { computed, ref, watch } from "vue";

const props = defineProps<{
  value?: number;
  min?: number;
  max?: number;
}>();

const getBitLength = (num: number) => {
  return (Math.log2(num) + 1) | 0;
};
const min = computed(() => Math.max(0, props.min ?? 0));
const max = computed(() => Math.min(2 ** 53, props.max ?? 10));
const bitLength = computed(() => getBitLength(max.value));
const checked = ref<boolean[]>([]);
const sum = computed(() => {
  const bitStr = checked.value.map((check) => (check ? 1 : 0)).join("");
  return parseInt(bitStr, 2);
});

watch(
  bitLength,
  (len) => {
    checked.value = [...Array(len)].map(() => false);
  },
  {
    immediate: true,
  }
);
</script>

<template>
  <div>
    <div class="flex flex-row">
      <label :key="n" v-for="n in bitLength" class="flex flex-col p-1">
        <span class="select-none">
          {{ bitLength - n + 1 }}
        </span>
        <input type="checkbox" name="" v-model="checked[n - 1]" />
      </label>
    </div>
    <div class="text-xl text-center">
      {{ sum }}
    </div>
  </div>
</template>
