<template>
  <n-scrollbar style="height: 100vh">
    <n-space justify="space-between" align="center" style="padding: 5px">
      <n-h3 style="margin: 0">Проводник</n-h3>
      <n-button tertiary circle @click="showNewFile = true">
        <template #icon>
          <n-icon :component="Add" size="22" />
        </template>
      </n-button>
    </n-space>
    <n-menu :options="menuOptions" :on-update:value="selectFile" />
  </n-scrollbar>
</template>

<script>
import { toRefs, ref, computed, h, defineComponent } from "vue";
import { NIcon } from "naive-ui";
import { LogoJavascript, Add } from "@vicons/ionicons5";
import MenuItem from "./MenuItem.vue";

export default defineComponent({
  emits: ["selectFile", "deleteFile", "renameFile", "createFile"],
  props: {
    files: {
      type: Array,
      required: true,
    },
  },
  setup(props, { emit }) {
    const { files } = toRefs(props);
    const showNewFile = ref(false);

    const sortedFiles = computed(() => {
      return [...files.value].sort((fileA, fileB) => {
        if (fileA.name > fileB.name) {
          return 1;
        }
        if (fileA.name < fileB.name) {
          return -1;
        }
        return 0;
      });
    });

    const getIcon = () => h(NIcon, null, { default: () => h(LogoJavascript) });

    const selectFile = (id) => {
      emit("selectFile", id);
    };

    const menuOptions = computed(() => {
      return [
        {
          label: () =>
            h(MenuItem, {
              name: "",
              files: files.value,
              isNew: true,
              onRename: (name) => {
                showNewFile.value = false;
                emit("createFile", name);
              },
              onClose: () => {
                showNewFile.value = false;
              },
            }),
          key: "new-file",
          show: showNewFile.value,
          icon: getIcon,
        },
        ...sortedFiles.value.map((file) => {
          return {
            label: () =>
              h(MenuItem, {
                name: file.name,
                files: files.value,
                onDelete: () => {
                  emit("deleteFile", file.id);
                },
                onRename: (name) => {
                  emit("renameFile", {
                    id: file.id,
                    name,
                  });
                },
              }),
            key: file.id,
            icon: getIcon,
          };
        }),
      ];
    });

    return {
      showNewFile,
      menuOptions,
      Add,
      selectFile,
    };
  },
});
</script>
