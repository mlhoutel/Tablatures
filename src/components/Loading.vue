<template>
  <v-container>
    <v-row class="fill-height" align-content="center" justify="center">
      <v-col class="text-subtitle-1 text-center" cols="12">{{ Status }}</v-col>
      <v-col cols="6">
        <v-progress-linear :value="Completion" striped color="deep-purple accent-4" rounded height="6"></v-progress-linear>
      </v-col>
    </v-row>
  </v-container>
</template>
<script lang="ts">
import Vue from "vue"
export default Vue.extend({
  name: "Loading",
  props: {
    status: { type: Array },
    completion: { type: Number },
    tasks: { type: Number },
  },
  computed: {
    Status(): string {
      const statusList: Array<any> = this.status as Array<any>
      const status = statusList.find((e: any) => {
        return e.id === this.completion
      })
      return `[${this.completion}/${this.tasks}] ${status.text}`
    },
    Completion(): number {
      return 100 * (this.completion / this.tasks)
    },
  },
})
</script>
