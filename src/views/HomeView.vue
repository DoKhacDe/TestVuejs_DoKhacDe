<template>
  <div class="mt-[50px]">
    <div class="home">
      <div class="content-left">
        <div class="title-content">
          <span>Storage</span>
        </div>
        <div class="mb-4 text-left w-full">
          <progress :value="progressValue" :max="maxSize" class="w-full mt-3 h-[10px] custom-progress-bar"></progress>
          <p class="font-bold"><span class="text-[#1E90FF]">{{progressPercentage}}%</span> Used of 2GB</p>
        </div>
        <hr>
        <div class="title-content">
          <span>Search</span>
        </div>
        <input type="text" @input="search" class="style-input" placeholder="e.g.image.png"/>
        <hr>
        <div class="title-content">
          <span>Folders</span>
        </div>
        <FolderDropdown :data="list" @update:goToDetail="getDetail"/>
        <hr>
        <div class="title-content">
          <span>Members</span>
          <span class="underline cursor-pointer" @click="selectAll">Select all</span>
        </div>
        <div class="text-left mt-3">
          <ul v-for="(item, index) in members" :key="index">
            <li class="flex items-center">
              <input type="checkbox" :checked="item.value" @click="checkedMember(item, $event)" width="24"/>
              <span class="ml-2 font-[500]">{{item.name}}</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="content-right">
        <TableComponent :item="filteredList"/>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import data from '@/common/data.json';
import {ref, computed, reactive, onMounted} from 'vue';
import TableComponent from "@/components/TableComponent.vue";
import FolderDropdown from "@/components/FolderDropdown.vue";

const list = ref([...data]);
const listFolder = ref([]);
const searchText = ref('');
const AdminChecked = ref(false);
const AppleChecked = ref(false);
const progressValue = ref(0);
const progressPercentage = ref(0)
const maxSize = ref(0)
const members = reactive([
    {name:'Admin', value: false},
    {name:'Apple', value: false},
])

function debounce<T> (fn: T, wait: number) {
  let timer: ReturnType<typeof setTimeout>
  return (event: Event) => {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => {
      if (typeof fn === 'function') {
        fn(event)
      }
    }, wait)
  }
}

const filteredList = computed(() => {
  const regex = new RegExp(searchText.value, 'i');
  let selectedPhotoBy = ['Admin', 'Apple']
  if(AdminChecked.value && !AppleChecked.value) {
    selectedPhotoBy = ['Admin']
  }
  if(AppleChecked.value && !AdminChecked.value) {
    selectedPhotoBy = ['Apple']
  }
  return listFolder.value.filter((item) => {
    return selectedPhotoBy.includes(item.photo_by) && regex.test(item.name);
  });
});

const search = debounce((event) => {
  searchText.value = event.target.value;
}, 500);

const getDetail = (item) => {
  if (item.children && item.children.length > 0) {
    listFolder.value = getItemsWithUrl(item);
  } else {
    listFolder.value = [];
  }
};

function getItemsWithUrl(node) {
  let result = [];
  if (node.children && node.children.length > 0) {
    for (const child of node.children) {
      result = result.concat(getItemsWithUrl(child));
    }
  }
  if (node.url) {
    result.push(node);
  }
  return result;
}

const selectAll = () => {
  members.map(item => {
    item.value = !item.value
    AdminChecked.value = item.value
    AppleChecked.value = item.value
  })
}
const checkedMember = (item, event) => {
  const isChecked = event.target.checked;
  if(isChecked) {
    if(item.name == 'Admin') {
      AdminChecked.value = true
    }
    if(item.name == 'Apple') {
      AppleChecked.value = true
    }
  }
  else {
    if(item.name == 'Admin') {
      AdminChecked.value = false
    }
    if(item.name == 'Apple') {
      AppleChecked.value = false
    }
  }
}

const calculateProgress = () => {
  const totalSize = listFolder.value.reduce((acc, item) => acc + parseInt(item.size), 0);
  maxSize.value = 2 * 1024 * 1024 * 1024;
  progressValue.value = totalSize;
  progressPercentage.value = ((totalSize / maxSize.value) * 100).toFixed(0);
};

onMounted(() => {
  calculateProgress();
});
</script>

<style lang="scss" scoped>
.home {
  @apply grid grid-cols-8 bg-white mx-[30px] shadow-lg shadow-gray-400 rounded-lg min-h-[500px];
  .content-left {
    @apply col-span-2 p-3 w-full border-r border-gray-300;
  }
  .content-right {
    @apply col-span-6 p-3 w-full;
  }
  .title-content {
    @apply flex justify-between text-gray-400 text-[12px] mt-3;
  }
  .style-input {
    @apply w-full border border-gray-300 h-[40px] px-4 rounded mb-6 mt-3;
    background: url('../assets/search.svg') no-repeat right center;
  }
  .custom-progress-bar::-webkit-progress-value {
    background-color: #1E90FF;
    border-radius: 5px;
  }
  .custom-progress-bar::-webkit-progress-bar {
    background-color: #E0E0E0;
    border-radius: 5px;
  }
}
</style>
