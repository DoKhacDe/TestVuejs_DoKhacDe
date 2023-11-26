<template>
  <DataTable :headers="headers" :data="list">
    <template v-slot:url="item">
      <div class="w-[160px]">
        <template v-if="isImage(item)">
            <img :src="item.url" :alt="item.name" class="w-[160px] h-[80px] rounded object-cover"/>
        </template>
        <template v-else-if="isVideo(item)">
          <video :src="item.url" controls width="160" height="80"></video>
        </template>
        <template v-else>
          <p>Unsupported format</p>
        </template>
      </div>
    </template>
    <template v-slot:size="item">
      <span>{{formatSize(item.size)}} kB</span>
    </template>
  </DataTable>
</template>
<script lang="ts" setup>
import DataTable from '@/components/DataTable.vue';
import {defineProps, ref, reactive, watch} from "vue";

const headers = reactive([
  {text: '', value: 'url'},
  {text: 'Name', value: 'name'},
  {text: 'Dimmension', value: 'dimmension'},
  {text: 'Size', value: 'size'},
])


const props = defineProps({
  item: {
    type: Array,
    default: () => [] ,
  },
})

const list = ref()

watch(() => props.item, (value) => {
  list.value = value
}, {deep: true,immediate:true})

const isImage = (item:any) => {
  return item.type && item.type.startsWith('image');
}

const isVideo = (item:any) => {
  return item.type && item.type.startsWith('video');
}

const formatSize = (bytes:number) => {
  const kilobytes = bytes / 1024;
  return kilobytes.toFixed(1);
};
</script>
