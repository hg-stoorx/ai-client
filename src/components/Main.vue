<script lang="js">
import ChatInput from "./ChatInput.vue";
import {generateUniqueId, typeText} from "../helpers/index"
export default {
    components: {
        ChatInput
  },
  data() {
    return {
      chats: [],
    };
  },
  methods: {
    onSubmit(text) {
      //console.log("onSubmit", text);
      const id = generateUniqueId()
      this.addChat(text, true, id)
    },
    onResult(data) {
      //console.log("onResult", data);
      const id = generateUniqueId()
      this.addChat(data, false, id)
    },

    addChat(data, isPrompt, id) {
        const chats = [...this.chats, {
        text: isPrompt ? data : "",
        id,
        isPrompt
      }]
      this.chats = chats;
      const chatsContainer = document.getElementById("chats-list");
      
      setTimeout(() => {
        chatsContainer.scrollTop = chatsContainer.scrollHeight;
        if(!isPrompt) {
          const messageDiv = document.getElementById(id);
            typeText(messageDiv, data)
        }
      }, 100);

   },
  },
};
</script>

<template>
  <div class="bg-slate-800">
    <div class="mx-auto max-w-4xl px-4 h-screen flex flex-col justify-center">
      <div class="text-4xl text-white text-center font-serif mt-2">
        OPEN AI INTEGRATION
      </div>
      <div id="chats-list" class="mt-6 mb-6 flex-1">
        <div
          :key="chat.id"
          :id="chat.id"
          v-for="chat in chats"
          :class="[
            chat.isPrompt
              ? 'bg-transparent font-mono mb-2'
              : 'bg-slate-700 font-serif mb-5',
            'p-6 text-white rounded-md whitespace-pre-line',
          ]"
        >
          {{ chat.text }}
        </div>
      </div>
    </div>
    <ChatInput @submitted="onSubmit" @resultFetched="onResult" />
  </div>
</template>

<style>
#chats-list {
  flex: 1;
  width: 100%;
  height: 100%;
  overflow-y: scroll;

  display: flex;
  flex-direction: column;
  gap: 10px;

  -ms-overflow-style: none;
  scrollbar-width: none;

  padding-bottom: 20px;
  scroll-behavior: smooth;
}
</style>