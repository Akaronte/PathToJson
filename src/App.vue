<template>
  <v-app>
    <v-content>
      <TreeView v-bind:fileList="files"
        @selected="returnFiles"></TreeView>
    </v-content>
  </v-app>
</template>

<script>
import TreeView from "./components/TreeView.vue";
import files from "./dat.js";
export default {
  name: "App",
  components: {
    TreeView
  },
  data() {
    return {
      files: files.files,
      selectedFiles: []
    };
  },
  watch: {
    selectedFiles() {
      console.log(this.selectedFiles);
    }
  },
  methods: {
    returnFiles(selected) {
        this.selectedFiles = selected
      }
  }
};
</script>
