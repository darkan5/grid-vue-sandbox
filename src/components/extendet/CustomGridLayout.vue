<script>
import VueGridLayout from "vue-grid-layout";

export default {
  extends: VueGridLayout.GridLayout,
  props: {
    masterLayouts: {
      type: Object,
      default: {},
    },
  },
  mounted: function () {
    this.layouts = this.masterLayouts;
  },
  updated: function () {
    this.layouts = this.masterLayouts;
  },
  watch: {
    masterLayouts: function () {
      this.layouts = this.masterLayouts;
    },
  },
};
</script>

