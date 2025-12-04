<script>
import ClassicSubtabButton from "./ClassicSubtabButton";

export default {
  name: "ClassicSubtabBar",
  components: {
    ClassicSubtabButton
  },
  data() {
    return {
      isVisible: false
    };
  },
  computed: {
    tab: () => Tabs.current,
    subtabs() {
      return this.tab.subtabs;
    }
  },
  methods: {
    update() {
      this.isVisible = this.subtabs.countWhere(subtab => subtab.isAvailable) > 1;
    }
  },
};
</script>

<template>
  <nav
    v-if="isVisible"
    class="c-subtab-button-container"
    role="tablist"
    :aria-label="`${tab.name} subtabs`"
  >
    <ClassicSubtabButton
      v-for="(subtab, i) in subtabs"
      :key="i"
      :subtab="subtab"
      :parent-name="tab.name"
    />
  </nav>
</template>

<style scoped>

</style>
