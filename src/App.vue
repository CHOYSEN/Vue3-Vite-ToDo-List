<template>
  <main class="mt-4 mx-auto px-3">
    <div class="alert-wrapper mb-3">
      <transition name="fade">
        <div class="alert" :class="[ alert.style ]" v-show="alert.isShow">{{ alert.text }}</div>
      </transition>
    </div>

    <h1 class="display-4 mb-5">ToDo List</h1>
    <h4 class="mb-3">共有 {{ toDo.length + finished.length }} 个任务，其中 {{ finished.length }} 项已完成</h4>
    <h5 class="mb-3">添加一个新事项</h5>
    <input
      id="event-input"
      class="w-100 form-control mb-3 rounded-0"
      v-model="eventName"
      @keypress.enter="add"
    />
    <button class="btn btn-primary btn-lg btn-block rounded-0 mb-5" @click="add">添加</button>

    <ul class="list-group mb-4">
      <li class="list-group-item active rounded-0 py-4">未完成列表</li>

      <li class="list-group-item rounded-0" v-show="toDo.length === 0">
        <h5 class="py-2 m-0">
          <small class="d-block">无</small>
        </h5>
      </li>

      <li
        class="row list-group-item d-flex m-0 rounded-0 flex-nowrap"
        v-for="(item,index) in toDo"
        :key="item.id"
      >
        <div class="col-9 p-0">
          <h5 class="py-2 m-0">
            <small class="d-block text-truncate" :title="item.name">{{ item.name }}</small>
          </h5>
        </div>
        <div class="col p-0 d-flex justify-content-end">
          <button
            class="btn btn-success p-0 rounded-circle mr-2"
            title="添加到已完成列表"
            @click="finish(index)"
          >√</button>
          <button
            class="btn btn-danger p-0 rounded-circle"
            title="删除此项"
            @click="remove('todo',index)"
          >×</button>
        </div>
      </li>
    </ul>

    <ul class="list-group">
      <li class="list-group-item active rounded-0 py-4">已完成列表</li>

      <li class="list-group-item rounded-0" v-show="finished.length === 0">
        <h5 class="py-2 m-0">
          <small class="d-block">无</small>
        </h5>
      </li>

      <li
        class="row list-group-item d-flex m-0 rounded-0 flex-nowrap"
        v-for="(item,index) in reversedFinishedList"
        :key="item.id"
      >
        <div class="col-9 p-0">
          <h5 class="py-2 m-0">
            <small class="d-block text-truncate" :title="item.name">{{ item.name }}</small>
          </h5>
        </div>
        <div class="col p-0 d-flex justify-content-end">
          <button
            class="btn btn-danger rounded-circle p-0"
            title="删除此项"
            @click="remove('finished',index)"
          >×</button>
        </div>
      </li>
    </ul>
  </main>
</template>

<script>
import { ref, reactive, computed, toRefs, onMounted } from "vue";
export default {
  setup() {
    const eventName = ref("");

    const list = reactive({
      toDo: [],
      finished: [],
    });

    const alert = reactive({
      text: "",
      style: "",
      isShow: false,
    });

    const reversedFinishedList = computed(() =>
      JSON.parse(JSON.stringify(list.finished)).reverse()
    );

    onMounted(() => document.querySelector("#event-input").focus());

    let timeoutId;
    function add() {
      if (eventName.value === "") {
        alert.text = "请输入事项名称！";
        alert.style = "alert-danger";
      } else {
        alert.text = "添加成功！";
        alert.style = "alert-success";

        list.toDo.push({
          id: Symbol(),
          name: eventName.value,
        });
        eventName.value = "";
      }

      alert.isShow = true;
      clearTimeout(timeoutId);
      document.querySelector("#event-input").focus();
      timeoutId = setTimeout(() => (alert.isShow = false), 3000);
    }

    function finish(index) {
      list.finished.push(list.toDo[index]);
      remove("todo", index);
    }

    function remove(listName, index) {
      const currentList = listName === "todo" ? list.toDo : list.finished;
      currentList.splice(index, 1);
    }

    return {
      alert,
      eventName,
      ...toRefs(list),
      reversedFinishedList,

      add,
      finish,
      remove,
    };
  },
};
</script>
<style>
main {
  max-width: 600px;
}

li {
  list-style: none;
}

li button {
  width: 37px;
  height: 37px;
}

.alert-wrapper {
  height: 58px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>