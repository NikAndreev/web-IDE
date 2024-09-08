<template>
  <n-config-provider>
    <n-dialog-provider>
      <n-layout has-sider>
        <n-layout-sider bordered :width="240">
          <Menu
            :files="files"
            @select-file="selectFile"
            @delete-file="deleteFile"
            @rename-file="renameFile"
            @create-file="createFile" />
        </n-layout-sider>
        <n-layout-content>
          <Editor v-model="currentFile.value" />
        </n-layout-content>
      </n-layout>
    </n-dialog-provider>
  </n-config-provider>
</template>

<script>
import { defineComponent, ref, watch } from "vue";
import Editor from "./components/Editor.vue";
import Menu from "./components/Menu.vue";

export default defineComponent({
  components: {
    Editor,
    Menu,
  },
  setup() {
    const files = ref([]);
    const currentFile = ref({});

    const selectFile = (id) => {
      const file = files.value.find((file) => file.id === id);

      currentFile.value = file;
    };

    const deleteFile = (id) => {
      files.value = files.value.filter((file) => file.id !== id);

      if (currentFile.value.id === id) {
        currentFile.value = {};
      }
    };

    const renameFile = ({ id, name }) => {
      const file = files.value.find((file) => file.id === id);

      file.name = name;
    };

    const createFile = (name) => {
      files.value.push({
        name,
        value: "",
        id: Number(new Date()),
      });
    };

    watch(
      files,
      (newValue) => {
        localStorage.setItem("files", JSON.stringify(newValue));
      },
      { deep: true }
    );

    const LSFiles = localStorage.getItem("files");

    if (LSFiles) {
      files.value = JSON.parse(LSFiles);
    }

    return {
      files,
      currentFile,
      selectFile,
      deleteFile,
      renameFile,
      createFile,
    };
  },
});
</script>
