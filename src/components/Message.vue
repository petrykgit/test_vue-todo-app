<script setup>
  import { ref, watch } from 'vue';

  const props = defineProps({ hidden: Boolean });

  const isHidden = ref(props.hidden);

  const emit = defineEmits(['close']);

  watch(() => props.hidden, (newValue) => {
    isHidden.value = newValue;
  });

  const closeMessage = () => {
    isHidden.value = true;
    emit('close');
  };
</script>

<template>
  <article class="message" :class="{ 'message--hidden': isHidden }">
    <div class="message-header">
      <p>Error</p>
      <button class="delete" @click="closeMessage" type="button"></button>
    </div>

    <div class="message-body">
      <slot>No content</slot>
    </div>
  </article>
</template>

<style scoped lang="scss">
  .message {
    transform-origin: top center;
    transition-property: opacity, transform;
    transition-duration: 0.3s;

    &--hidden {
      transform: scaleY(0);
      opacity: 0;
      pointer-events: none;
    }
  }
</style>
