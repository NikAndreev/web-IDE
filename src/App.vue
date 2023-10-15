<template>
  <n-config-provider>
    <n-dialog-provider>
      <App />
    </n-dialog-provider>
  </n-config-provider>
</template>

<script>
import { defineComponent } from "vue";
import App from "./components/App.vue";

export default defineComponent({
  components: {
    App,
  },
});
</script>
