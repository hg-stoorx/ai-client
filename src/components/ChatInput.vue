
<template>
  <footer class="absolute bottom-0 left-0 w-full bg-slate-900">
    <form
      @submit="handleSubmit"
      @key-up="
        (e) => {
          if (e.keyCode === 13) {
            handleSubmit();
          }
        }
      "
      class="mx-auto max-w-4xl px-4 flex items-center mt-3"
    >
      <textarea
        class="
          w-full
          outline-none
          border-none
          bg-transparent
          text-white text-lg
          rounded-md
        "
        rows="1"
        cols="1"
        placeholder="Ask AI..."
        v-model="prompt"
      />
      <button
        v-if="!isLoading"
        type="submit"
        class="outline-none border-none cursor-pointer"
      >
        <img src="../assets/send.svg" alt="send" class="w-8 h-8" />
      </button>
      <div v-if="isLoading" class="flex items-center">
        <div
          :key="index"
          v-for="(dot, index) in dots"
          class="w-2 h-2 mr-[1px] rounded-full bg-white"
        />
      </div>
    </form>
  </footer>
</template>


<script>
export default {
  data() {
    return {
      prompt: "",
      isLoading: false,
      dots: 0,
    };
  },
  methods: {
    async handleSubmit(e) {
      e.preventDefault();

      try {
        this.isLoading = true;
        this.$emit("submitted", this.prompt);

        const loader = setInterval(() => {
          this.dots++;
          if (this.dots > 3) {
            this.dots = 0;
          }
        }, 300);

        const body = {
          prompt: this.prompt,
        };

        const res = await fetch("http://localhost:5004/ask", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(body),
        });
        if (res.ok) {
          const json = await res.json();
          //console.log("JSON", json.bot);
          this.$emit("resultFetched", json.bot.trim());
          this.prompt = "";
          this.isLoading = false;
          clearInterval(loader);
        }
      } catch (error) {
        console.error(error);
      }
    },
  },
};
</script>