<template>
  <div class="flex justify-items-stretch w-full relative">
    <svg
      v-show="task.pinned"
      xmlns="http://www.w3.org/2000/svg"
      class="h-5 w-5 text-primary drop-shadow absolute -left-8 mt-1"
      viewBox="0 0 20 20"
      fill="currentColor"
    >
      <path
        fill-rule="evenodd"
        d="M17.707 9.293a1 1 0 010 1.414l-7 7a1 1 0 01-1.414 0l-7-7A.997.997 0 012 10V5a3 3 0 013-3h5c.256 0 .512.098.707.293l7 7zM5 6a1 1 0 100-2 1 1 0 000 2z"
        clip-rule="evenodd"
      />
    </svg>
    <input
      v-model="task.done"
      class="h-5 w-5 bg-[#484f59] border-[#212529] focus:border-[#212529] rounded-sm ring-0 ring-offset-0 mt-1 mr-4 cursor-pointer"
      type="checkbox"
      :id="'task' + task.id"
    />
    <label :for="'task' + task.id" class="flex-1">
      {{ task.title }}
      <br />
      <input
        v-if="editMemo"
        v-model="memo"
        @keyup.enter="
          task.memo = memo
          editMemo = false
          memo = null
        "
        type="text"
        maxlength="70"
        class="w-full border-[#212529] text-xs rounded-md px-2 py-1 mt-1"
      />
      <span v-show="!editMemo" class="text-gray-400">{{ task.memo }}</span>
    </label>
    <div class="relative">
      <button
        @click="dropdown = !dropdown"
        class="z-10 ml-1 px-1 rounded-full hover:bg-[#212529]"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-5 w-5 self-center relative"
          viewBox="0 0 20 20"
          fill="currentColor"
        >
          <path
            d="M6 10a2 2 0 11-4 0 2 2 0 014 0zM12 10a2 2 0 11-4 0 2 2 0 014 0zM16 12a2 2 0 100-4 2 2 0 000 4z"
          />
        </svg>
      </button>
      <div
        v-show="dropdown"
        class="absolute top-6 right-0 w-[145px] hover:cursor-pointer bg-[#484f59] text-gray-300 rounded shadow-lg z-20"
      >
        <a
          @click="
            $set(task, 'pinned', !task.pinned)
            dropdown = false
          "
          class="flex items-center px-2.5 py-1.5 text-sm capitalize hover:text-primary hover:bg-gray-700"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-4 w-4 mr-1.5"
            viewBox="0 0 20 20"
            fill="currentColor"
          >
            <path
              fill-rule="evenodd"
              d="M17.707 9.293a1 1 0 010 1.414l-7 7a1 1 0 01-1.414 0l-7-7A.997.997 0 012 10V5a3 3 0 013-3h5c.256 0 .512.098.707.293l7 7zM5 6a1 1 0 100-2 1 1 0 000 2z"
              clip-rule="evenodd"
            />
          </svg>
          {{ task.pinned ? 'unpin' : 'Pin on the top' }}
        </a>
        <a
          @click="
            memo = task.memo || null
            editMemo = true
            dropdown = false
          "
          class="flex items-center px-2.5 py-1.5 text-sm capitalize hover:text-primary hover:bg-gray-700"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-4 w-4 mr-1.5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M17 8h2a2 2 0 012 2v6a2 2 0 01-2 2h-2v4l-4-4H9a1.994 1.994 0 01-1.414-.586m0 0L11 14h4a2 2 0 002-2V6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2v4l.586-.586z"
            />
          </svg>
          {{ task.memo ? 'Edit memo' : 'Add a memo' }}
        </a>
        <a
          @click="del_task(task)"
          class="flex items-center px-2.5 py-1.5 text-sm capitalize hover:text-primary hover:bg-gray-700"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-4 w-4 mr-1.5"
            viewBox="0 0 20 20"
            fill="currentColor"
          >
            <path
              fill-rule="evenodd"
              d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
              clip-rule="evenodd"
            />
          </svg>
          Delete
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      default: () => {}
    }
  },
  data: () => ({
    dropdown: false,
    editMemo: false
  }),
  watch: {
    task: {
      handler(val) {
        this.update_task(val)
      },
      deep: true
    }
  },
  methods: {
    async update_task(task) {
      await this.$axios.put('/tasks/' + task.id, {
        title: task.title,
        pinned: task.pinned,
        memo: task.memo,
        done: task.done
      })
    },
    async del_task(task) {
      await this.$emit('delTask', task)
      this.dropdown = false
    }
  }
}
</script>
