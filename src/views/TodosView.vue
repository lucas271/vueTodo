<script setup lang="ts">
import { ref, watch, computed } from 'vue'
import TodoCreator from '../components/TodoCreator.vue'
import TodoItem from '../components/TodoItem.vue'
import { uid } from 'uid'
import { Icon } from '@iconify/vue'
import draggable from 'vuedraggable'

type todoType = {
  id: string
  todo: string
  isCompleted: null | boolean
  isEditing: null | boolean
}
const todoList = ref<todoType[]>([])

watch(todoList, () => setTodoListLocalStorage(), {
  deep: true
})

const todoCompleted = computed<boolean>((): boolean => {
  return todoList.value.every((todo) => todo.isCompleted)
})

const fetchTodoList = (): void => {
  const savedTodoList = JSON.parse(String(localStorage.getItem('todoList')))
  if (savedTodoList) {
    todoList.value = savedTodoList
  }
}
fetchTodoList()

const setTodoListLocalStorage = (): void => {
  localStorage.setItem('todoList', JSON.stringify(todoList.value))
}

const createTodo = (todo: string): void => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null
  })
}

const toggleTodoComplete = (todoPos: number): void => {
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted
}
const toggleEditTodo = (todoPos: number): void => {
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing
}

const updateTodo = (todoVal: string, todoPos: number): void => {
  todoList.value[todoPos].todo = todoVal
  console.log(todoVal)
}
const deleteTodo = (todoId: string): void => {
  const todoIndex = todoList.value.findIndex((todo) => todo.id === todoId)
  if (todoIndex < 0) return

  todoList.value.splice(todoIndex, 1)
}
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <draggable v-model="todoList" :component-data="{ name: 'fade', appear: true }" item-key="id">
        <template #item="{ element: todo }">
          <TodoItem
            :key="todo.id"
            :todo="todo"
            :index="todoList.findIndex((element) => element.id === todo.id)"
            @toggle-complete="toggleTodoComplete"
            @edit-todo="toggleEditTodo"
            @update-todo="updateTodo"
            @delete-todo="deleteTodo"
          />
        </template>
      </draggable>
    </ul>

    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todos to complete add one</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" width="22" />
      <span>You've done it all!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
.fade-enter-from {
  opacity: 0;
}
.fade-enter-to {
  opacity: 1;
}
.fade-enter-active {
  transition: all 1s ease;
}
.fade-leave-from {
  opacity: 1;
}
.fade-leave-to {
  opacity: 0;
  transform: translateX(20%);
}
.fade-leave-active {
  transition: all 1s ease;
}
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;
  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }
  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
