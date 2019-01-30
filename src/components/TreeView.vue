<template>
  <v-slide-y-transition mode="out-in">
    <v-container grid-list-md style="max-width: 1300px;" fluid>
      <v-flex xs12 sm12 md12>
       <h1>PATH TO JSON VUE COMPONENT</h1>
      </v-flex>
      <v-flex xs12 sm12 md12>
        {{fileList}}
      </v-flex>
      <v-flex xs12 sm12 md12>
        {{activeFileNames}}
      </v-flex>
        <v-flex xs12 sm6 md6>
          <v-treeview
            v-model="tree"
            :active.sync="active"
            :items="items"
            :open.sync="open"
            item-key="id"
            activatable
            multiple-active
          >
            <template slot="prepend" slot-scope="{ item, open }">
              <v-icon v-if="!item.isFile">{{ open ? 'folder' : 'folder_open' }}</v-icon>
              <v-icon v-else>{{ 'description' }}</v-icon>
            </template>
          </v-treeview>
        </v-flex>
        <v-flex xs12 sm6 md6>
          <v-chip v-for="(file, i) in activeViewFiles" :key="i" color="grey" dark small>
            <v-icon left small>description</v-icon>
            {{ file.name }}
          </v-chip>
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
    }
  },
  data: () => ({
    selectedFiles: [],
    files: [],
    filteredFiles: [],
    listFiles: [],
    list: [],
    tree: [],
    open: [],
    items: [],
    active: [],
    idTreeView: 0
  }),
  computed: {
    activeFiles: function() {
      return this.listFiles.filter(file => {
        let value = false;
        for (let active of this.active) {
          if (file.id == active) {
            value = true;
          }
        }
        return value;
      });
    },
    activeFileNames: function() {
      let myfiles = [];
      let umyfiles = [];
      for (let activeFile of this.activeFiles) {
        if (Array.isArray(activeFile)) {
          for (let file of activeFile.fileNames) {
            myfiles.push(file);
          }
        } else {
          myfiles.push(activeFile.fileNames);
        }
      }
      for (let myfile of myfiles) {
        if (Array.isArray(myfile)) {
          for (let m of myfile) {
            if (!umyfiles.includes(m)) {
              umyfiles.push(m);
            }
          }
        } else {
          if (!umyfiles.includes(myfile)) {
            umyfiles.push(myfile);
          }
        }
      }
      return umyfiles;
    },
    activeFileIds: function() {
      let myfiles = [];
      let umyfiles = [];
      for (let activeFile of this.activeFiles) {
        if (Array.isArray(activeFile)) {
          for (let file of activeFile.fileIds) {
            myfiles.push(file);
          }
        } else {
          myfiles.push(activeFile.fileIds);
        }
      }
      for (let myfile of myfiles) {
        if (Array.isArray(myfile)) {
          for (let m of myfile) {
            if (!umyfiles.includes(m)) {
              umyfiles.push(m);
            }
          }
        } else {
          if (!umyfiles.includes(myfile)) {
            umyfiles.push(myfile);
          }
        }
      }
      return umyfiles;
    },
    activeViewFiles: function() {
      let viewFiles = [];
      viewFiles = this.filteredFiles.filter(file => {
        return this.activeFileIds.includes(file.id);
      });
      return viewFiles;
    }
  },
  watch: {
    activeFileNames() {
      console.log(this.activeFileNames);
    }
  },
  mounted: function() {
    this.listToObjets(this.fileList);
    this.buildTreeView(this.files);
  },
  methods: {
    listToObjets(arrList) {
      let fileid = 0;
      for (let p in arrList) {
        if (arrList[p][0] == "/") {
          arrList[p] = arrList[p].substring(1, arrList[p].length);
        }
        if (arrList[p][arrList[p].length - 1] == "/") {
          arrList[p] = arrList[p].substring(0, arrList[p].length - 1);
        }
        this.files.push({ path: arrList[p] });
      }
      this.files = this.files.map(file => {
        file.treeview = file.path.split("/");
        file.name = file.treeview[file.treeview.length - 1];
        file.treeview.pop();
        file.id = fileid + "c21d897a";
        fileid = fileid + 1;
        console.log(file);
        return file;
      });
    },
    buildTreeView(files) {
      let items = [];
      for (let file of files) {
        this.idTreeView++;
        let i = file.treeview.length - 1;
        let temp = [];
        let fileNames = [];
        let fileIds = [];
        fileNames.push(file.name);
        fileIds.push(file.id);
        temp = [
          {
            name: file.name,
            fileNames: file.name,
            id: this.idTreeView,
            isFile: true,
            fileIds: file.id
          }
        ];
        while (i > -1) {
          this.idTreeView++;
          temp = this.pathToArray(
            temp,
            file.treeview[i],
            fileNames,
            this.idTreeView,
            fileIds
          );
          i--;
        }
        items.push(temp[0]);
      }
      items = this.joinKeys(items);
      this.items = items;
      console.log(items);
      this.readTree(this.items);
      this.createList(this.list);
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
    pathToArray(temp, name, fileNames, id, fileIds) {
      this.idTreeView++;
      return (temp = [
        {
          name: name,
          children: temp,
          fileNames: fileNames,
          id: id,
          isFile: false,
          fileIds: fileIds
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
              fileIds: item.fileIds
            });
          } else {
            uitems.push({
              name: item.name,
              fileNames: item.fileNames,
              id: item.id,
              isFile: item.isFile,
              fileIds: item.fileIds
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
        this.list.push(item);
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
    filterFiles() {
      this.tree = [];
      this.open = [];
      this.active = [];
      this.list = [];
      this.listFiles = [];
      this.filteredFiles = this.files;
      this.filteredFiles = this.filteredFiles.filter(
        file => file.Projectname === this.project
      );
      this.buildTreeView(this.filteredFiles);
    },
    fillFiles() {
      let filtered = [];
      try {
        console.log(this.filteredFiles);
        filtered = this.filteredFiles.filter(file => {
          return this.activeFileIds.includes(file.id);
        });
      } catch (err) {
        console.error(err);
      }
      return filtered;
    }
  }
};
</script>
