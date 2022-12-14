<template>
  <div ref="controlWindowRef" :style="style" class="control-window">
    <div>
      <slot />
    </div>
  </div>
</template>

<script lang="ts" setup>
const props = defineProps<{
  storageKey?: string;
  offset?: { x?: number; y?: number };
}>();
const { offset, storageKey } = toRefs(props);
const initialPosition = {
  x: 40 + (offset?.value?.x ?? 0),
  y: 30 + (offset?.value?.y ?? 0),
};

if (window.localStorage && storageKey?.value) {
  const storageVal = window.localStorage.getItem(storageKey.value);
  if (storageVal) {
    const val = JSON.parse(
      window.localStorage.getItem(storageVal) ?? JSON.stringify(initialPosition)
    );
    initialPosition.x = val.x ?? 0;
    initialPosition.y = val.y ?? 0;
  }
}

const controlWindowRef = ref<HTMLDivElement | undefined>();
const { style, x, y } = useDraggable(controlWindowRef, {
  initialValue: {
    x: Math.min(window.innerWidth - 200, initialPosition.x),
    y: Math.min(window.innerHeight - 100, initialPosition.y),
  },
  preventDefault: true,
  stopPropagation: true,
  exact: true,
});

watch([x, y], () => {
  if (!window.localStorage || !storageKey?.value) return;
  window.localStorage.setItem(
    storageKey.value,
    JSON.stringify({ x: x.value, y: y.value })
  );
});
</script>

<style lang="scss" scoped>
.control-window {
  position: fixed;
  top: 30px;
  left: 40px;
  padding: 10px;
  border-radius: 5px;
  background: #f0f0f0;
  border: 1px solid #b4b4b4;
  cursor: move;
  pointer-events: initial;
  user-select: none;
  touch-action: none;
  z-index: 10;

  div {
    cursor: initial;
    background-color: whitesmoke;
    border-radius: 6px;
  }
}
</style>
