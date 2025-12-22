<script setup>
  import { ref, defineProps, defineEmits, nextTick } from 'vue';

  const props = defineProps({
    todo: Object,
    isLoading: Boolean
  });

  const emit = defineEmits(['delete', 'update']);

  const newTitle = ref(props.todo.title);

  const editing = ref(false);
  const titleField = ref(null);

  const startEditing = async () => {
    newTitle.value = props.todo.title;
    editing.value = true;

    await nextTick();

    if (titleField.value) {
      titleField.value.focus();
    }
  };

  const rename = () => {
    if (!editing.value) return;

    editing.value = false;

    if (newTitle.value === props.todo.title) {
      return;
    }

    if (!newTitle.value) {
      emit('delete');
      return;
    }

    emit('update', { ...props.todo, title: newTitle.value });
  };

</script>

<template>
  <div class="todo" :class="{ completed: todo.completed }">
    <label class="todo__status-label">
      <input
        type="checkbox"
        class="todo__status"
        ref="titleField"
        v-model="newTitle"
        :checked="todo.completed"
        @change="emit('update', { ...todo, completed: !todo.completed })"
      />

    </label>

    <form v-if="editing" @submit.prevent="rename">
      <input
        ref="titleField"
        v-model.trim="newTitle"
        class="todo__title-field"
        placeholder="Empty todo will be deleted"
        @keyup.escape="editing = false"
        @blur="rename"
      />
    </form>

    <template v-else>
      <span class="todo__title" @dblclick="startEditing">{{ todo.title }}</span>
      <button class="todo__remove" @click="emit('delete')">×</button>
    </template>

    <div class="modal overlay" :class="{ 'is-active': isLoading }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>
