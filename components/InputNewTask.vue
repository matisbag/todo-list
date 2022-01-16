<template>
  <form @submit.prevent="checkForm">
    <div class="relative">
      <div
        class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"
      >
        <span class="text-gray-500 md:text-sm">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-5 w-5"
            viewBox="0 0 20 20"
            fill="currentColor"
          >
            <path
              fill-rule="evenodd"
              d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 15a1 1 0 011-1h6a1 1 0 110 2H4a1 1 0 01-1-1z"
              clip-rule="evenodd"
            />
          </svg>
        </span>
      </div>
      <input
        v-model="newTask"
        type="text"
        name="add-task"
        placeholder="Add a task..."
        class="block w-full mt-4 pl-10 pr-2 md:text-sm rounded-md"
        :class="{
          'focus:ring-error focus:border-error border-error': error
        }"
      />
    </div>
    <span
      v-if="error"
      class="tracking-wide text-error text-sm font-medium mt-1 ml-1"
    >
      {{ error }}
    </span>
  </form>
</template>

<script>
export default {
  data: () => ({
    newTask: '',
    error: null
  }),
  methods: {
    async checkForm() {
      if (this.newTask.length < 5) {
        this.error = 'Your task is too short.'
      } else {
        await this.$axios
          .post('/tasks', {
            title: this.newTask
          })
          .then((res) => {
            this.$emit('newTask', res.data)
            this.newTask = ''
            this.error = null
          })
      }
    }
  }
}
</script>
