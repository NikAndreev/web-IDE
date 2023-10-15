<template>
  <n-input
    v-if="showInput"
    type="text"
    placeholder="Type..."
    :status="isValid ? '' : 'error'"
    ref="input"
    v-model:value="inputValue"
    @focusout="onFocusout" />
  <div v-else @contextmenu="handleContextMenu">{{ name }}.js</div>
  <n-dropdown
    placement="bottom-start"
    trigger="manual"
    :x="x"
    :y="y"
    :options="options"
    :show="showDropdown"
    :on-clickoutside="() => (showDropdown = false)"
    @select="handleSelect" />
</template>

<script>
import {
  defineComponent,
  ref,
  computed,
  toRefs,
  nextTick,
  onMounted,
} from "vue";
import { useDialog } from "naive-ui";

export default defineComponent({
  emits: ["delete", "rename", "close"],
  props: {
    name: {
      type: String,
      required: true,
    },
    names: {
      type: Array,
      required: true,
    },
    isNew: {
      type: Boolean,
      required: false,
    },
  },
  setup(props, { emit }) {
    const { name, names, isNew } = toRefs(props);

    const showDropdown = ref(false);
    const x = ref(0);
    const y = ref(0);

    const dialog = useDialog();

    const showInput = ref(false);
    const input = ref(null);
    const inputValue = ref("");

    const isValid = computed(() => {
      return (
        !names.value
          .filter((item) => item !== name.value)
          .includes(inputValue.value) && inputValue.value.length
      );
    });

    const handleSelect = (key) => {
      showDropdown.value = false;

      switch (key) {
        case "delete":
          dialog.warning({
            title: "Confirm",
            content: "Are you sure?",
            positiveText: "Sure",
            negativeText: "Not Sure",
            onPositiveClick: () => {
              emit("delete");
            },
          });
          break;
        case "rename":
          renderInput();
      }
    };

    const renderInput = () => {
      inputValue.value = name.value;
      showInput.value = true;

      nextTick().then(() => {
        input.value.focus();
      });
    };

    const handleContextMenu = (e) => {
      e.preventDefault();
      showDropdown.value = false;
      nextTick().then(() => {
        showDropdown.value = true;
        x.value = e.clientX;
        y.value = e.clientY;
      });
    };

    const onFocusout = () => {
      showInput.value = false;

      if (isValid.value) {
        emit("rename", inputValue.value);
      } else {
        emit("close");
      }
    };

    onMounted(() => {
      if (isNew.value) {
        renderInput();
      }
    });

    return {
      options: [
        {
          label: "Переименовать",
          key: "rename",
        },
        {
          label: "Удалить",
          key: "delete",
        },
      ],
      showDropdown,
      x,
      y,
      input,
      showInput,
      inputValue,
      isValid,
      handleSelect,
      handleContextMenu,
      onFocusout,
    };
  },
});
</script>
