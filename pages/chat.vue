<template>
  <div class="chat-wrap">
    <div class="chat" ref="block">
      <Message 
        v-for="m in messages" :key="m.text"
        :text="m.text"
        :name="m.name"
        :owner="m.id === user.id"
      />
    </div>
    <div class="chat-form">
      <ChatForm />
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import Message from "@/components/Message";
import ChatForm from "@/components/ChatForm";
export default {
  layout: "chatLayout",
  middleware: ["chat"],
  head() {
    return {
      title: `Room ${this.user.room}`
    };
  },
  components: {Message, ChatForm},
  computed: {
    ...mapState({
      user: state => state.user,
      messages: state => state.messages
    })
  },
  watch: {
    messages() {
      setTimeout(() => {
        this.$refs.block.scrollTop = this.$refs.block.scrollHeight
      })
    }
  }
};
</script>

<style scoped>
  .chat-wrap {
    height: 100%;
    position: relative;
    overflow: hidden;
  }

  .chat-form {
    position: absolute;
    bottom: 0;
    right: 0;
    left: 0;
    padding: 1rem;
    height: 80px;
    background: #212121;
  }

  .chat {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    bottom: 80px;
    padding: 1rem;
    overflow-y: auto;
  }
</style>

