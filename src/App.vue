<template>
  <v-app>
    <v-content>
      <TreeView v-bind:fileList="files"></TreeView>
    </v-content>
  </v-app>
</template>

<script>
import TreeView from "./components/TreeView.vue";

export default {
  name: "App",
  components: {
    TreeView
  },
  data() {
    return {
      files: [
        "raw/print/Spain/Madrid/2019/01/01/music2.mp3",
        "raw/print/Spain/Madrid/2016/01/01/picture.png",
        "raw/print/Spain/Madrid/2016/01/01/sand.png",
        "raw/print/Spain/Madrid/2016/01/01/war.rar/",
        "raw/print/Spain/Madrid/2016/01/02/win.rar/",
        "raw/print/Spain/Madrid/2016/01/02/adobe.pdf",
        "raw/print/Spain/Madrid/2016/01/03/adobe.pdf",
        "raw/print/Spain/Madrid/2016/02/03/adobe.pdf",
        "raw/print/Spain/Bilbao/2016/02/03/iron.pdf",
        "raw/print/Spain/Bilbao/2016/02/03/wine.pdf",
        "raw/print/Spain/Bilbao/2016/02/03/wine.pdf",
        "raw/print/Spain/Bilbao/2016/02/03/iron.mp3",
        "raw/print/Spain/Bilbao/2016/02/04/iron.mp3",
        "raw/print/Spain/Bilbao/2016/02/04/never.mp3",
        "raw/print/Spain/Bilbao/2016/02/04/iron.mp3",
        "raw/print/Spain/Bilbao/2016/03/04/iron.json",
        "raw/print/richt.png",
        "raw/print/left.png",
        "users/program/xampp/MercuryMail/full.xls",
        "users/program/xampp/MercuryMail/id.xls",
        "users/program/outlook/client/mail1.md",
        "users/program/outlook/client/mail2.md",
        "/users/program/outlook/client/mail4.md",
        "/users/program/outlook/config/set,md",
        "/users/program/outlook/config/set/hide.js",
        "/users/program/monkey/island/lechuck.png",
        "/users/program/monkey/island/guybrush.png"
      ]
    };
  }
};
</script>
