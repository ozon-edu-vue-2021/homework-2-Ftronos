<template>
  <div class="tree__item-wrapper" @click="clickHandler">
    <p class="tree__item" ref="item">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        x="0px"
        y="0px"
        width="16"
        height="16"
        viewBox="0 0 16 16"
        style=" fill:#000000;"
        class="tree__item__icon"
      >
        <path
          d="M 2.5 2 C 1.671875 2 1 2.671875 1 3.5 L 1 12.5 C 1 13.328125 1.671875 14 2.5 14 L 13.5 14 C 14.328125 14 15 13.328125 15 12.5 L 15 5.5 C 15 4.671875 14.328125 4 13.5 4 L 6.796875 4 L 6.144531 2.789063 C 5.882813 2.300781 5.378906 2 4.824219 2 Z"
        ></path>
      </svg>
      <slot></slot>
    </p>
    <div style="padding-left: 20px;" v-if="isOpened" @click.stop="">
      <component
        v-for="(t, c) in treeData"
        :key="`${t.name}-${c}`"
        :is="componentName(t)"
        :treeData="t.contents"
        :path="`${$attrs.path}/${t.name}`"
      >
        {{ t.name }}
      </component>
    </div>
  </div>
</template>

<script>
import LinkItem from "../LinkItem/LinkItem.vue";
import FileItem from "../FileItem/FileItem.vue";
import DirectoryItem from "../DirectoryItem/DirectoryItem.vue";
import ShowTreeItemPathMixin from "../ShowTreeItemPathMixin.vue";
import SelectTreeItemMixin from "../SelectTreeItemMixin.vue";

export default {
  name: "DirectoryItem",

  mixins: [ShowTreeItemPathMixin, SelectTreeItemMixin],

  inject: ["isItemSelected"],

  components: {
    LinkItem,
    FileItem,
    DirectoryItem,
  },

  props: {
    treeData: {
      type: Array,
      default: () => [],
    },
  },

  data() {
    return {
      isOpened: false,
    };
  },

  methods: {
    componentName(t) {
      switch (t.type) {
        case "directory":
          return "directory-item";

        case "file":
          return "file-item";

        case "link":
          return "link-item";
      }
    },

    toggleDirectory() {
      this.isOpened = !this.isOpened;
    },

    clickHandler() {
      this.toggleDirectory();
      this.showCurrentItemPath();
      this.selectItem(this.$refs.item);
    },

    keyPressHandler(e) {
      if (e.key === "Enter" && this.isItemSelected(this.$refs.item)) {
        this.toggleDirectory();
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
