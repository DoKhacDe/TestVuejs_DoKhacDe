<template>
  <div class="text-left">
    <div v-for="(item, index) in data" :key="index">
      <div v-if="item.children" @click="toggleDropdown(item)" class="btn-dropdown" :class="item === activeItem ? 'text-[#1E90FF] bg-[#F5F5F5]':''">
         <BaseIconSvg class="mr-1" :active="item === activeItem"/>{{ item.name }}
        <div class="count" :class="item === activeItem ? 'text-white bg-[#1E90FF]':'bg-gray-300'">{{item.children && item.children.length || 0}}</div>
      </div>
      <template v-if="item.children && item.children.length >= 0 && isOpen(item)">
        <FolderDropdown :data="item.children" :style="`margin-left:${getIndentation(0)}`" @update:goToDetail="getDetail"/>
      </template>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {onMounted, ref} from "vue";
import { defineProps, defineEmits, computed} from "vue";
import BaseIconSvg from "@/components/BaseIconSvg.vue";

const emit = defineEmits(['update:goToDetail'])
const props = defineProps({
  data: {
    type: Array,
    default: () => [],
  },
});

const openState = ref({});
const activeItem = ref(null);

onMounted(() => {
  activeItem.value = props.data?.find(item => item.name == 'Uploads')
  if (activeItem.value) {
    emit('update:goToDetail', activeItem.value)
  }
})

const isOpen = (item) => {
  return openState.value[item.id];
};

const closeAllDropdowns = () => {
  Object.keys(openState.value).forEach((itemId) => {
    openState.value[itemId] = false;
  });
};

const toggleDropdown = (item:any) => {
  emit('update:goToDetail', item)
  closeAllDropdowns();
  openState.value[item.id] = !openState.value[item.id];
  activeItem.value = item;
};

const getDetail = (item:any) => {
  emit('update:goToDetail', item)
}

const getIndentation = (item:number) => {
  return  item + 20 + "px";
};
</script>

<style lang="scss" scoped>
.btn-dropdown {
  @apply flex items-center cursor-pointer my-[2px] py-1 px-2 rounded-lg;
  .count {
    @apply ml-1 rounded w-[18px] h-[18px] text-center leading-[20px] text-[12px];
  }
}
</style>
