<script>
export default {
  name: "ClassicTabButton",
  props: {
    tab: {
      type: Object,
      required: true
    },
    tabPosition: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      isAvailable: false,
      hasNotification: false,
      tabName: ""
    };
  },
  computed: {
    isCurrentTab() {
      return this.tab.isOpen && Theme.currentName() !== "S9";
    }
  },
  methods: {
    update() {
      this.isAvailable = this.tab.isAvailable;
      this.hasNotification = this.tab.hasNotification;
      if (this.tabPosition < Pelle.endTabNames.length) {
        this.tabName = Pelle.transitionText(
          this.tab.name,
          Pelle.endTabNames[this.tabPosition],
          Math.max(Math.min(GameEnd.endState - (this.tab.id) % 4 / 10, 1), 0)
        );
      } else {
        this.tabName = this.tab.name;
      }
    }
  },
};
</script>

<template>
  <button
    v-if="isAvailable"
    :class="
      [tab.config.UIClass,
       { 'o-tab-btn--active': isCurrentTab }]
    "
    class="o-tab-btn"
    role="tab"
    :aria-selected="isCurrentTab"
    :aria-label="`${tabName} tab${hasNotification ? ', has notification' : ''}`"
    :tabindex="isCurrentTab ? 0 : -1"
    @click="tab.show(true)"
    @keydown.left="$emit('navigate', -1)"
    @keydown.right="$emit('navigate', 1)"
    @keydown.home="$emit('navigate', 'first')"
    @keydown.end="$emit('navigate', 'last')"
  >
    {{ tabName }}
    <div
      v-if="hasNotification"
      class="fas fa-circle-exclamation l-notification-icon"
      aria-hidden="true"
    />
  </button>
</template>

<style scoped>
.o-tab-btn {
  position: relative;
  height: 3.1rem;
  vertical-align: middle;
  margin: 0.2rem;
  margin-bottom: 0.7rem;
}

.o-tab-btn--active {
  height: 3.1rem;
  border-bottom-width: 0.5rem;
}

.s-base--metro .o-tab-btn--active {
  border-bottom-width: 0.5rem;
}
</style>
