<template>
  <div class="container">
    <div class="toolbar">
      <div>{{ title }}</div>
      <button @click="toggleCheck">全选/反选</button>
    </div>
    <div v-for="group in groupList" :key="group" class="group">
    <div class="title">{{ group }}</div>
    <div class="content">
      <div class="item" v-for="item in groupAndListMap.get(group)" :key="item.id">
        <div class="name">{{ item.name }}</div>
        <input type="checkbox" v-model="item.checked" @change="checkChange($event, item)"/>
      </div>
    </div>
  </div>
  </div>
</template>

<script setup>
import { ref, computed, toValue } from 'vue'

// 按照时间和top标识进行分组
const fetchList = ref([
  {
    id: 109,
    time: '今天',
    top: false,
    name: '你好世界'
  },
  {
    id: 103,
    time: '今天',
    top: true,
    name: '置顶条目'
  },
  {
    id: 102,
    time: '今天',
    top: false,
    name: '今天的第一个对话'
  },
  {
    id: 1,
    time: '昨天',
    top: false,
    name: '久远的古老的对话'
  },
]);

// methods
const getGroupListAndGroupListMap = (list) => {
  return list.reduce((acc, cur, index, arr) => {
    let group = cur.time;
    // 置顶类型
    if (cur.top) {
      group = '置顶';
      if (!acc.list.includes(group)) {
        acc.list.push(group);
        acc.map.set(group, []);
      }
      acc.map.get(group).push(cur);
      return acc;
    }
    // 非置顶且不在list中
    if (!acc.list.includes(group)) {
      acc.list.push(group);
      acc.map.set(group, []);
    }
    acc.map.get(group).push(cur);
    return acc;
  }, { list: [], map: new Map() });
}

const allChecked = ref(false);
const toggleCheck = () => {
  const checked = !allChecked.value;
  allChecked.value = checked;
  fetchList.value = fetchList.value.map(item => ({ ...item, checked: checked }));
}

// groupList, groupAndListMap
const groupList = computed(() => {
  const { list } = getGroupListAndGroupListMap(fetchList.value);
  console.log(list);
  return list;
})

const groupAndListMap = computed(() => {
  const { map } = getGroupListAndGroupListMap(fetchList.value);
  console.log(map);
  return map;
})

const checked = computed(() => {
  return fetchList.value.filter(item => item.checked);
})

const title = computed(() => {
  return checked.value.length ? `已选${ checked.value.length }项目` : '请选择项目'
})

const checkChange = ($event, item) => {
  const checked = $event.target.checked;
  if (!checked && allChecked.value) {
    allChecked.value = false;
  }
  const id = item.id;
  const targetItem = fetchList.value.find(item => item.id === id);
  targetItem.checked = checked;
}
</script>

<style scoped>
.container {
  width: 230px;
  height: 500px;
  overflow: auto;
  border: 1px solid red;
}
.toolbar{
  display: flex;
  align-items: center;
}
.toolbar div{

}
.toolbar button{

}
.group{
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.group .title{
  color: #999;
}
.group .content{
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding-left: 20px;
}
.group .content .item {
  width: 200px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 10px;
  color: #000;
}
.group .content .item .name{
  flex: 1;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: calc(100% - 30px);
  text-align: left;
}
.group .content .item input{
  flex-shrink: 0;
  width: 30px;
}
</style>
