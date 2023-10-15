<template>
  <codemirror
    :extensions="extensions"
    :modelValue="modelValue"
    @update:modelValue="$emit('update:modelValue', $event)"
    :disabled="!fileSelected"
    :placeholder="placeholder"
    :style="{ height: '100vh' }"
    :autofocus="true"
    :indent-with-tab="true"
    :tab-size="2" />
</template>

<script>
import { defineComponent, computed, toRefs } from "vue";
import { Codemirror } from "vue-codemirror";
import { javascript } from "@codemirror/lang-javascript";
import { oneDark } from "@codemirror/theme-one-dark";

export default defineComponent({
  components: {
    Codemirror,
  },
  emits: ["update:modelValue"],
  props: {
    modelValue: {
      type: String,
      required: false,
    },
  },
  setup(props) {
    const { modelValue } = toRefs(props);
    const extensions = [javascript(), oneDark];

    const fileSelected = computed(() => {
      return modelValue.value !== undefined;
    });

    const placeholder = computed(() => {
      return fileSelected.value ? "Code goes here..." : "Choose File...";
    });

    return {
      extensions,
      fileSelected,
      placeholder,
    };
  },
});
</script>
