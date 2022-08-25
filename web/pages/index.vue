<template>
  <div class="px-4 md:px-0 py-10 container mx-auto">
    <div class="py-5 flex justify-between items-center border-b border-gray-200">
      <div class="text-gray-700 font-medium text-lg">
        My tasks
      </div>
      <FormSelect :name="'select-status'" :value="select" :options="options" class="w-44" @input="updateSelect" />
    </div>
    <div class="mt-8">
      <p v-if="$fetchState.pending">
        Fetching tasks...
      </p>
      <p v-else-if="$fetchState.error" class="text-red-700 text-2xl py-5">
        Unable to fetch tasks.
      </p>
      <div v-else class="rounded-xl overflow-hidden shadow">
        <div v-for="(task, index) in tasks" :key="index" :class="[index < tasks.length - 1 ? 'border-b border-gray-200' : '', 'px-6 py-4 bg-white flex justify-between']">
          <div class="space-y-2.5 w-full">
            <div class="flex justify-between items-center space-x-2.5">
              <div class="flex space-x-2.5 font-medium truncate">
                <div v-if="task.status.value === 'DONE'" class="px-2.5 py-0.5 bg-green-100 text-green-800 text-xs rounded-xl">
                  {{ task.status.label }}
                </div>
                <div v-if="task.status.value === 'TODO'" class="px-2.5 py-0.5 bg-red-100 text-red-800 text-xs rounded-xl">
                  {{ task.status.label }}
                </div>
                <div class="text-indigo-600 text-sm truncate">
                  {{ task.title }}
                </div>
              </div>
              <div class="flex space-x-2">
                <NuxtLink :to="`/edit/${task.id}`">
                  <div class="w-5 h-5 bg-indigo-600 rounded-sm flex justify-center items-center">
                    <SvgIcon name="icons/pencil" class="w-4 h-4 text-white" />
                  </div>
                </NuxtLink>
                <div class="cursor-pointer w-5 h-5 bg-red-600 rounded-sm flex justify-center items-center" @click="remove(task.id)">
                  <SvgIcon name="icons/trash" class="w-4 h-4 text-white" />
                </div>
              </div>
            </div>
            <div class="flex flex-col-reverse md:flex-row md:justify-between md:items-center">
              <div class="text-gray-500" v-html="task.description" />
              <div v-if="task.date" class="mb-2.5 md:mb-0 flex items-center space-x-2">
                <SvgIcon name="icons/calendar" class="h-4 w-4 text-gray-400" />
                <div class="text-gray-500 text-sm">
                  {{ task.date | dateFormat }}
                </div>
              </div>
            </div>
            <div class="md:flex md:space-x-2">
              <div class="text-gray-500 text-xs">
                Created at {{ task.createdAt | timeFormat }}
              </div>
              <div class="mt-2.5 md:mt-0 text-gray-500 text-xs font-bold">
                Updated at {{ task.updatedAt | timeFormat }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import { TASK_STATUS } from '~/dewib/api/tasks/index.js'
export default {
  filters: {
    timeFormat (date) {
      return moment(date).format('MMMM D, YYYY - h:mm:ss')
    },
    dateFormat (date) {
      return moment(date).format('MMMM D, YYYY')
    }
  },
  data () {
    return {
      select: 'ALL',
      options: TASK_STATUS,
      tasks: []
    }
  },
  async fetch () {
    const [err, tasks] = await this.$api.tasks.findAll()

    if (err) {
      throw new Error(err)
    }

    this.tasks = tasks
  },
  methods: {
    async fetch () {
      const [err, tasks] = await this.$api.tasks.findAll()
      if (err) {
        throw new Error(err)
      }
      this.tasks = tasks
    },
    async updateSelect (newValue) {
      if (newValue === 'ALL') {
        this.fetch()
      } else {
        const [err, tasks] = await this.$api.tasks.findAll({ status: newValue })
        if (err) {
          throw new Error(err)
        }
        this.tasks = tasks
      }
      this.select = newValue
    },
    async remove (id) {
      const [err] = await this.$api.tasks.remove(id)
      if (err) {
        throw new Error(err)
      }
      this.fetch()
    }
  }
}
</script>
