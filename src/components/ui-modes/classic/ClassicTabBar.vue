<script>
import ClassicTabButton from "./ClassicTabButton";

export default {
  name: "ClassicTabBar",
  components: {
    ClassicTabButton,
  },
  computed: {
    tabs: () => Tabs.oldUI,
  },
};
</script>

<template>
  <nav
    role="tablist"
    aria-label="Main navigation tabs"
  >
    <ClassicTabButton
      v-for="(tab, i) in tabs"
      :key="i"
      :tab-position="i"
      :tab="tab"
    />
  </nav>
</template>

<style scoped>

</style>
