<script>
export default {
  name: "ShowTreeItemPathMixin",
  inject: ["updateFullPath"],
  methods: {
    showCurrentItemPath() {
      this.updateFullPath(this.$attrs.path);
    },
  },
};
</script>
