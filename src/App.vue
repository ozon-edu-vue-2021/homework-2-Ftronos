<template>
  <div id="app">
    <h2 class="fullpath" v-show="fullPath">Полный путь: {{ fullPath }}</h2>
    <directory-item :treeData="treeJSON.contents" :path="`/${treeJSON.name}`">
      {{ treeJSON.name }}
    </directory-item>
  </div>
</template>

<script>
import DirectoryItem from "./components/DirectoryItem/DirectoryItem.vue";
import treeData from "../public/static/node_modules.json";

export default {
  name: "App",

  components: {
    DirectoryItem,
  },

  data() {
    return {
      treeJSON: treeData,
      fullPath: "",
      classes: {
        item: "tree__item",
        itemSel: "tree__item_selected",
      },
      pathAttrName: "path",
    };
  },

  provide() {
    return {
      updateFullPath: this.updateFullPath,
      selectItem: this.updateSelectedItem,
      isItemSelected: this.checkSelected,
    };
  },

  methods: {
    checkSelected(el) {
      return el.classList.contains(this.classes.itemSel);
    },

    treeItems() {
      return document.querySelectorAll(`.${this.classes.item}`);
    },

    selectedItemIdx() {
      return Array.from(this.treeItems()).findIndex((el) =>
        el.classList.contains(this.classes.itemSel)
      );
    },

    updateSelectedItem(elem) {
      if (document.querySelector(`.${this.classes.itemSel}`)) {
        document.querySelector(`.${this.classes.itemSel}`).classList.remove(this.classes.itemSel);
      }

      elem.classList.add(this.classes.itemSel);

      if (elem.offsetTop > window.innerHeight + window.pageYOffset) {
        window.scrollTo({
          top: elem.offsetTop + elem.clientHeight - window.innerHeight,
          left: 0,
        });
      } else if (elem.offsetTop < window.pageYOffset) {
        window.scrollTo({
          top: elem.offsetTop,
          left: 0,
        });
      }

      const path =
        elem.getAttribute(this.pathAttrName) ||
        elem.closest(`[${this.pathAttrName}]`).getAttribute(this.pathAttrName);

      this.updateFullPath(path);
    },

    selectNextElement() {
      const selectedItem = document.querySelector(`.${this.classes.itemSel}`);

      if (!selectedItem) {
        this.updateSelectedItem(this.treeItems()[0]);
        return;
      }

      if (this.selectedItemIdx() === this.treeItems().length - 1) {
        return;
      }

      this.updateSelectedItem(this.treeItems()[this.selectedItemIdx() + 1]);
    },

    selectPrevElement() {
      const selectedItem = document.querySelector(`.${this.classes.itemSel}`);

      if (!selectedItem) {
        this.updateSelectedItem(this.treeItems()[0]);
        return;
      }

      if (this.selectedItemIdx() === 0) {
        return;
      }

      this.updateSelectedItem(this.treeItems()[this.selectedItemIdx() - 1]);
    },

    updateFullPath(path) {
      this.fullPath = path;
    },

    keyPressHandler(e) {
      e.preventDefault();

      switch (e.key) {
        case "ArrowDown": {
          this.selectNextElement();
          break;
        }

        case "ArrowUp": {
          this.selectPrevElement();
          break;
        }
      }
    },
  },

  mounted() {
    document.body.addEventListener("keydown", this.keyPressHandler);
  },

  beforeDestroy() {
    document.body.removeEventListener("keydown", this.keyPressHandler);
  },
};
</script>

<style>
#app {
  font: normal 14px/1.2 "Arial", sans-serif;
}

html,
body {
  margin: 0;
  padding: 0;
}

.fullpath {
  box-shadow: 0 0 2px #000;
  background-color: #fff;
  padding: 10px 20px;
  margin: 0;
  word-break: break-word;
}

.tree__item {
  display: flex;
  align-items: center;
  cursor: pointer;
  color: #333;
  transition: background-color 0.5s, color 0.5s;
  font-weight: bold;
  margin: 0;
  padding: 4px 0;
}

.tree__item_selected {
  background-color: blue;
  color: #fff;
}

.tree__item__icon {
  margin-right: 5px;
}
</style>
