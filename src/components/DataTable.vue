<template>
  <div>
    <table class="min-w-full bg-white border border-gray-300">
      <thead>
      <tr>
        <th v-for="(column, index) in headersWithSelect" :key="index" class="py-2 px-4 border-b">
          <div v-if="column.value === 'select'" class="min-w-[150px]">
              Select all<input type="checkbox" v-model="allSelected" class="ml-2" @click="checkedAll"/>
          </div>
          <div v-else class="text-left">
            {{ column.text || '' }}
          </div>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(item, index) in dataWithSelected" :key="index">
        <td v-for="(column, colIndex) in headersWithSelect" :key="colIndex" class="py-2 px-4 border-b">
          <div v-if="column.value === 'select'" class="min-w-[150px]">
            <input type="checkbox" v-model="item.selected" />
          </div>
          <div v-else class="text-left">
            <slot :name="column.value" v-bind="item">{{ item[column.value] }}</slot>
          </div>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts" setup>
import {defineProps, ref,watch} from "vue";

const props = defineProps({
  headers: {
    headers: Array,
    default: () => [] ,
  },
  data: {
    type: Array,
    default: () => []
  },
})
const dataWithSelected = ref();

const headersWithSelect = [{ text: 'Select', value: 'select' },...props.headers];

watch(() => props.data, (value) => {
  dataWithSelected.value = value.map((item:any) => ({ ...item, selected: false }));
}, {deep: true,immediate:true})

const allSelected = ref(false)

const checkedAll = () => {
  dataWithSelected.value.forEach((item:any) => {
    item.selected = !allSelected.value
  })
}
</script>

<style scoped></style>
