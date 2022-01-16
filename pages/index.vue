<template>
  <main>
    <h1 class="text-3xl font-bold text-center">To do list!</h1>
    <section class="py-2">
      <InputNewTask @newTask="new_task" />
      <div class="flex flex-col space-y-4 py-4 pl-8 lg:pl-0">
        <Task
          v-for="pinnedTask in pinnedTasks"
          :key="pinnedTask.id"
          :task="pinnedTask"
          @delTask="delete_task"
          class="flex justify-items-stretch w-full"
        />
        <hr class="border-[#2d3338]" />
        <Task
          v-for="task in basicTasks"
          :key="task.id"
          :task="task"
          @delTask="delete_task"
          class="flex justify-items-stretch w-full"
        />
      </div>
    </section>
  </main>
</template>

<script>
export default {
  computed: {
    pinnedTasks() {
      return this.tasks.filter((task) => task.pinned === true)
    },
    basicTasks() {
      return this.tasks.filter((task) => !task.pinned || task.pinned === false)
    }
  },
  async asyncData({ $axios }) {
    let { data } = await $axios.get('/tasks')
    return { tasks: data }
  },
  methods: {
    new_task(task) {
      this.tasks.push(task)
    },
    async delete_task(task) {
      await this.$axios.delete('/tasks/' + task.id).then((res) => {
        this.tasks.splice(this.tasks.indexOf(task), 1)
      })
    }
  }
}
</script>
