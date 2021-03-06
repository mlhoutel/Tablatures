<template>
  <v-container fluid>
    <v-col class="tab-fill">
      <v-row>
        <v-file-input
          prepend-icon="mdi-music-note"
          :clearable="false"
          v-model="file"
          @change="onChange"
          label="tablature file"
          hint=".gp3, .gp4, .gp5, .gpx, .gp, .xml, .cap or .tex"
          :persistent-hint="true"
        ></v-file-input>
      </v-row>
      <v-row>
        <loading v-if="loading" :status="STATUS" :completion="completion" :tasks="TASKS_NUMBER"></loading>
      </v-row>
      <v-row class="tab-fill" style="padding-bottom: 100px">
        <tab-reader :file="file" ref="reader" :style="{ visibility: display ? 'visible' : 'hidden' }"></tab-reader>
      </v-row>
    </v-col>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue"
import TabReader from "@/components/TabReader.vue"
import Loading from "@/components/Loading.vue"

const DELAY = 100

const LOADING_BYTES = 1
const LOADING_SOUNDS = 2
const RENDERING_TAB = 3
const TASKS_NUMBER = 3

const STATUS = [
  { id: LOADING_BYTES, text: "Loading bytes..." },
  { id: LOADING_SOUNDS, text: "Loading sounds..." },
  { id: RENDERING_TAB, text: "Rendering tab..." },
]

export default Vue.extend({
  name: "Import",
  components: { TabReader, Loading },
  data() {
    return {
      display: false,
      file: new Blob(),
      reader: null as any,
      loading: false as boolean,
      completion: LOADING_BYTES,
      LOADING_BYTES: LOADING_BYTES,
      LOADING_SOUNDS: LOADING_SOUNDS,
      RENDERING_TAB: RENDERING_TAB,
      TASKS_NUMBER: TASKS_NUMBER,
      STATUS: STATUS,
    }
  },
  mounted() {
    this.reader = this.$refs.reader
  },
  methods: {
    async onChange(): Promise<void> {
      this.display = false
      this.loading = true

      await this.updateStatus(LOADING_SOUNDS)
      await this.reader.loadSoundsBytes()

      await this.updateStatus(LOADING_BYTES)
      await this.reader.loadScoreBytes()

      this.loading = false
      this.display = true
    },
    async updateStatus(status: number): Promise<void> {
      this.completion = status
      await this.delay(DELAY)
    },
    delay(ms: number): Promise<void> {
      return new Promise((resolve) => setTimeout(resolve, ms))
    },
  },
})
</script>
