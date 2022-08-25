<template>
  <div class="py-10 max-w-xl mx-auto">
    <div class="flex justify-center">
      <SvgIcon name="icons/pencil" class="h-12 w-12" />
    </div>
    <div class="mt-7 text-center text-gray-900 font-extrabold text-3xl">
      Edit a task
    </div>
    <form class="mt-8 px-10 py-8 bg-white rounded-xl shadow space-y-6">
      <FormInput label="Title" name="title" :value="title" @input="updateTitle" />
      <FormInputTextArea label="Describe It" name="description" :value="description" @input="updateDescription" />
      <div class="flex space-x-6">
        <FormInput
          label="Date"
          name="date"
          :value="date ? date : ''"
          type="date"
          class="flex-1"
          @input="updateDate"
        />
        <FormSelect
          label="Status"
          name="status"
          :value="status"
          :options="options"
          class="flex-1"
          @input="updateStatus"
        />
      </div>
      <div
        class="cursor-pointer py-3 w-full bg-indigo-600 text-white text-sm font-medium text-center rounded-md shadow-sm hover:bg-indigo-800 transition"
        @click="submit"
      >
        Submit
      </div>
      <div>
        <NuxtLink :to="{ name: 'index' }">
          <button class="py-3 w-full bg-gray-50 text-sm font-medium text-center rounded-md shadow-sm hover:bg-gray-200 transition">
            Cancel
          </button>
        </NuxtLink>
      </div>
    </form>
  </div>
</template>
<script>
export default {
  data () {
    return {
      title: '',
      description: '',
      date: null,
      status: '',
      options: [{
        label: 'ToDo',
        value: 'TODO'
      }, {
        label: 'Done',
        value: 'DONE'
      }]
    }
  },
  async fetch () {
    const [err, task] = await this.$api.tasks.findOne(this.$route.params.id)

    if (err) {
      throw new Error(err)
    }

    this.title = task.title
    this.description = task.description
    if (task.date) {
      this.date = task.date.split('T')[0]
    }
    this.status = task.status.value
  },
  methods: {
    updateTitle (newValue) {
      this.title = newValue
    },
    updateDescription (newValue) {
      this.description = newValue
    },
    updateDate (newValue) {
      this.date = newValue
    },
    updateStatus (newValue) {
      this.status = newValue
    },
    async submit () {
      if (!this.title || !this.description || !this.status) {
        alert('Please fill title, description and status')
        return
      }
      const date = new Date(this.date).toISOString().replace('Z', '')
      const [err] = await this.$api.tasks.update(this.$route.params.id, {
        title: this.title,
        description: this.description,
        date: (this.date ? date : null),
        status: this.status
      })
      if (err) {
        throw new Error(err)
      } else {
        this.$router.push({ name: 'index' })
      }
    }
  }
}
</script>
