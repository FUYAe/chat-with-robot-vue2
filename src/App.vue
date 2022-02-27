<template>
  <div>
    <ChatPage />
  </div>
</template>

<script>
import ChatPage from "./components/ChatPage.vue";

export default {
  name: "App",
  components: {
    ChatPage,
  },
};
</script>

<style>
html,
body {
  background-color: #f8fafc;
}
</style>
