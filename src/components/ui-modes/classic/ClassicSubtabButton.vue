<script>
export default {
  name: "ClassicSubtabButton",
  props: {
    subtab: {
      type: Object,
      required: true
    },
    parentName: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      isAvailable: false,
      hasNotification: false,
      isCurrentSubtab: false,
      tabName: ""
    };
  },
  computed: {
    classObject() {
      return {
        "o-tab-btn": true,
        "o-tab-btn--secondary": true,
        "o-subtab-btn--active": this.isCurrentSubtab,
        "o-tab-btn--infinity": this.parentName === "Infinity",
        "o-tab-btn--eternity": this.parentName === "Eternity",
        "o-tab-btn--reality": this.parentName === "Reality",
        "o-tab-btn--celestial": this.parentName === "Celestials"
      };
    },
  },
  methods: {
    update() {
      this.isAvailable = this.subtab.isAvailable;
      this.hasNotification = this.subtab.hasNotification;
      this.isCurrentSubtab = this.subtab.isOpen && Theme.currentName() !== "S9";
      this.tabName = Pelle.transitionText(
        this.subtab.name,
        this.subtab.name,
        Math.max(Math.min(GameEnd.endState - (this.subtab.id) % 4 / 10, 1), 0)
      );
    }
  },
};
</script>

<template>
  <button
    v-if="isAvailable"
    :class="classObject"
    role="tab"
    :aria-selected="isCurrentSubtab"
    :aria-label="`${tabName} subtab${hasNotification ? ', has notification' : ''}`"
    :tabindex="isCurrentSubtab ? 0 : -1"
    @click="subtab.show(true)"
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
  height: 2.5rem;
  vertical-align: middle;
  padding-top: 0.2rem;
}

.o-subtab-btn--active {
  height: 2.5rem;
  border-bottom-width: 0.4rem;
}

.s-base--metro .o-subtab-btn--active {
  border-bottom-width: 0.4rem;
}
</style>
