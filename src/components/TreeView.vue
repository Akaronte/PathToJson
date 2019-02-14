<template>
  <v-slide-y-transition mode="out-in">
    <v-container grid-list-md style="max-width: 1300px;" fluid>
      <v-flex xs12 sm12 md12>
       <h1>PATH TO JSON VUE COMPONENT</h1>
      </v-flex>
      <v-flex xs12 sm12 md12>
      </v-flex>
        <v-flex xs12 sm12 md12>
          <v-treeview
            v-model="active"
            :items="items"
            :open.sync="open"
            item-key="id"
            selectable
          >
            <template slot="prepend" slot-scope="{ item, open }">
              <v-icon v-if="!item.isFile">{{ open ? 'folder' : 'folder_open' }}</v-icon>
              <v-icon v-else>{{ 'description' }}</v-icon>
            </template>
          </v-treeview>
        </v-flex>
    </v-container>
  </v-slide-y-transition>
</template>

<script>
export default {
  props: {
    fileList: {
      type: Array,
      default: function() {
        return {};
      }
    },
    seledtedFiles: {
      type: Object,
      default: function() {
        return {};
      }
    }
  },
  data: () => ({
    selectedFiles: [],
    files: [],
    listFiles: [],
    list: [],
    active: [],
    open: [],
    items: [],
    idTreeView: 0
  }),
  computed: {
    activeFiles: function() {
      return this.list.filter(file => {
        return this.active.includes(file.id)
      })
    },
  },
  watch: {
    activeFiles() {
      //console.log(this.activeFiles);
    },
    active() {
      //console.log(this.active);
    },
    filteredFiles() {
      this.$emit('selected', this.activeFiles)
    }
  },
  mounted: function() {
    this.fixPaths();
    this.buildTreeView(this.files);
  },
  methods: {
fixPaths() {
      for(let path of this.fileList){
        this.files.push({ Path: path});
      }
      for (let file of this.files) {
        if (file.Path[0] == '/') {
          file.Path = file.Path.substring(1, file.Path.length - 1);
        }
        if (file.Path[file.Path.length - 1] == '/') {
          file.Path = file.Path.substring(0, file.Path.length - 1);
        }
        for (const file of this.files) {
          file.path = file.Path
          file.treeview = file.Path.split('/');
        }
      }
    },
    buildTreeView(files) {
      let items = [];
      for (let file of files) {
        this.idTreeView = this.usecounter(this.idTreeView);
        let i = file.treeview.length - 1;
        let name = file.treeview[file.treeview.length - 1];
        let temp = [];
        let fileNames = [];
        let fileIds = [];
        fileNames.push(file.name);
        fileIds.push(file.id);
        temp = [
          {
            name: name,
            fileNames: name,
            id: this.idTreeView,
            isFile: true,
            fileIds: this.idTreeView,
            path: file.path
          }
        ];
        while (i > -1) {
          this.idTreeView = this.usecounter(this.idTreeView);
          temp = this.pathToArray(
            temp,
            file.treeview[i],
            fileNames,
            this.idTreeView,
            fileIds,
            file.path
          );
          i--;
        }
        items.push(temp[0]);
      }
      items = this.joinKeys(items);
      this.items = items;
      //console.log(items);
      this.readTree(this.items);
      this.createList(this.list);
      //console.log(this.list);
    },
    isDuplicate(items) {
      let value = false;
      for (let [index, item] of items.entries()) {
        const comp = Array.from(items);
        comp.splice(index, 1);
        for (let omp of comp) {
          if (omp.name === item.name) {
            value = true;
            break;
          }
        }
        if (value) {
          break;
        }
      }
      return value;
    },
    pathToArray(temp, name, fileNames, id, fileIds, filePath) {
      this.idTreeView = this.usecounter(this.idTreeView);
      fileIds.push(this.idTreeView);
      return (temp = [
        {
          name: name,
          children: temp,
          fileNames: fileNames,
          id: id,
          isFile: false,
          fileIds: fileIds,
          path: filePath
        }
      ]);
    },
    joinKeys(items) {
      let uitems = [];
      for (let item of items) {
        if (!uitems.map(uitem => uitem.name).includes(item.name)) {
          if (item.children) {
            uitems.push({
              name: item.name,
              children: item.children,
              fileNames: item.fileNames,
              id: item.id,
              isFile: item.isFile,
              fileIds: item.fileIds,
              path: item.path
            });
          } else {
            uitems.push({
              name: item.name,
              fileNames: item.fileNames,
              id: item.id,
              isFile: item.isFile,
              fileIds: item.fileIds,
              path: item.path
            });
          }
        }
      }
      for (let uitem of uitems) {
        for (let item of items) {
          if (
            item.name === uitem.name &&
            item.isFile == false &&
            uitem.id != item.id
          ) {
            if (item.children) {
              uitem.children.push(item.children[0]);
            }
            if (!uitem.fileNames.includes(item.fileNames[0])) {
              uitem.fileNames.push(item.fileNames[0]);
            }
            if (!uitem.fileIds.includes(item.fileIds[0])) {
              uitem.fileIds.push(item.fileIds[0]);
            }
          }
        }
        if (uitem.children) {
          uitem.fileNames = [];
          uitem.fileIds = [];
          for (let child of uitem.children) {
            if (Array.isArray(child.fileNames)) {
              if (!uitem.fileNames.includes(child.fileNames[0])) {
                uitem.fileNames.push(child.fileNames[0]);
              }
              if (!uitem.fileIds.includes(child.fileIds[0])) {
                uitem.fileIds.push(child.fileIds[0]);
              }
            } else {
              if (!uitem.fileNames.includes(child.fileNames)) {
                uitem.fileNames.push(child.fileNames);
              }
              if (!uitem.fileIds.includes(child.fileIds)) {
                uitem.fileIds.push(child.fileIds);
              }
            }
          }
        }
        if (uitem.children) {
          uitem.children = this.joinKeys(uitem.children);
        }
      }
      return uitems;
    },
    readTree(items) {
      for (let item of items) {
        if(item.isFile){
          this.list.push(item);
        }
        if (item.children) {
          this.readTree(item.children);
        }
      }
    },
    createList(list) {
      for (let ele of list) {
        this.listFiles.push({
          id: ele.id,
          fileNames: ele.fileNames,
          fileIds: ele.fileIds,
          path: ele.path
        });
      }
    },
    usecounter(i){
      return i + 1;
    }
  }
};
</script>
