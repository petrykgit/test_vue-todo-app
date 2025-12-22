<script setup>
  import { computed, ref, watch, onBeforeMount, onMounted } from 'vue';
  import * as todoApi from './api/todos';
  import StatusFilter from "./components/StatusFilter.vue";
  import TodoItem from './components/TodoItem.vue';
  import Message from './components/Message.vue';
 

  const apod = ref(null);
  const errorMessage = ref('');;
  const loadingIds = ref([]);

  const todos = ref([]);
  const title = ref("");
  const status = ref('all');

  const startLoading = (id) => loadingIds.value.push(id);
  const stopLoading = (id) => {
      loadingIds.value = loadingIds.value.filter(itemId => itemId !== id);
    };

  onMounted(async () => {
    try {
      startLoading(0);
      todos.value = await todoApi.getTodos();
    } catch (error) {
      errorMessage.value = 'Unable to load todos';
    } finally {
        stopLoading(0);
      }
  });



  const addTodo = async () => {
    if (!title.value) {
      errorMessage.value = 'Title should not be empty';
      return;
    }

    const tempId = 0;
    const newTodoPlaceholder = {
      id: tempId,
      title: title.value,
      completed: false,
    };

    todos.value.push(newTodoPlaceholder);
    startLoading(tempId);
    const currentTitle = title.value;
    title.value = '';

    try {
      const serverTodo = await todoApi.createTodo(currentTitle);
      todos.value = todos.value.map(todo => 
        todo.id === tempId ? serverTodo : todo
      );
    } catch (error) {
      errorMessage.value = 'Unable to add a todo';
      todos.value = todos.value.filter(todo => todo.id !== tempId);
      title.value = currentTitle;
    } finally {
      stopLoading(tempId);
    }
  };


  const deleteTodo = async todoId => {
    startLoading(todoId);
    try {
      await todoApi.deleteTodo(todoId);
      todos.value = todos.value.filter(todo => todoId !== todo.id);
    } catch (error) {
      errorMessage.value = 'Unable to delete a todo';
    } finally {
      stopLoading(todoId);
    }
  };


  const updateTodo = async ({ id, title, completed }) => {
    startLoading(id);
    try {
      const updatedTodo = await todoApi.updateTodo({ id, title, completed });
      const currentTodo = todos.value.find(todo => todo.id === id);

      Object.assign(currentTodo, updatedTodo);
    } catch (error) {
      errorMessage.value = 'Unable to update a todo';
    } finally {
      stopLoading(id);
    }
  };

  const clearCompleted = async () => {
    const completedTodos = todos.value.filter(todo => todo.completed);

    try {
      await Promise.all(
        completedTodos.map(todo => deleteTodo(todo.id))
      );
    } catch (error) {
      errorMessage.value = 'Some todos could not be deleted';
    }
  };

  const toggleAll = async () => {
    const shouldComplete = activeTodos.value.length > 0;
    const todosToUpdate = shouldComplete 
      ? activeTodos.value 
      : todos.value;

    try {
      await Promise.all(
        todosToUpdate.map(todo => 
          updateTodo({ ...todo, completed: shouldComplete })
        )
      );
    } catch (error) {
      errorMessage.value = 'Unable to update all todos';
    }
  };

  const activeTodos = computed(() =>
    todos.value.filter((todo) => !todo.completed)
  );

  const visibleTodos = computed(() => {
    if (status.value === 'active') {
      return activeTodos.value;
    }

    if (status.value === 'completed') {
      return todos.value.filter(todo => todo.completed);
    }
    return todos.value;
  });

</script>


<template>
  <div class="todoapp">
      <h1 class="todoapp__title">todos</h1>

      <div class="todoapp__content">
        <header class="todoapp__header">
          <button
            v-if="todos.length > 0"
            class="todoapp__toggle-all"
            :class="{ active: activeTodos.length === 0 }"
            @click="toggleAll"
          ></button>

          <form @submit.prevent="addTodo">
            <input
              class="todoapp__new-todo"
              placeholder="What needs to be done?"
              v-model="title"
              @input="errorMessage = ''"
            />
          </form>
        </header>

        <TransitionGroup
          tag="section"
          name="todolist"
          class="todoapp__main"
          v-if="todos.length > 0"
        >
             
          <TodoItem
            v-for="todo of visibleTodos"
            :key="todo.id"
            :todo="todo"
            :is-loading="loadingIds.includes(todo.id)"
            @delete="deleteTodo(todo.id)" 
            @update="updateTodo($event)"
          />

        </TransitionGroup>

        <footer class="todoapp__footer" data-cy="Footer">
          <span class="todo-count">
            {{ activeTodos.length }} items left
          </span>

          <StatusFilter v-model="status" />

          <button
            type="button"
            class="todoapp__clear-completed"
            :disabled="todos.length === activeTodos.length"
            @click="clearCompleted"
          >
            Clear completed
          </button>
        </footer>
      </div>

      <Message 
        class="is-warning" 
        :hidden="!errorMessage"
        @close="errorMessage = ''" 
      >
        <p>{{ errorMessage }}</p>
      </Message>

    </div>

</template>

<style scoped>
  .todolist-enter-active,
  .todolist-leave-active {
    max-height: 60px;
    transition: all 0.5s ease;
  }
  .todolist-enter-from,
  .todolist-leave-to {
    opacity: 0;
    max-height: 0;
    transform: scaleY(0);
  }
</style>

