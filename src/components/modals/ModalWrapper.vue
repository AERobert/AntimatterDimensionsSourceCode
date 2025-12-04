<script>
import ModalCloseButton from "@/components/modals/ModalCloseButton";

export default {
  name: "ModalWrapper",
  components: {
    ModalCloseButton,
  },
  data() {
    return {
      modalId: `modal-${this._uid}`,
      titleId: `modal-title-${this._uid}`,
      previousActiveElement: null
    };
  },
  mounted() {
    // Store the previously focused element to restore focus later
    this.previousActiveElement = document.activeElement;
    // Focus the modal for screen readers
    this.$nextTick(() => {
      const modal = this.$el;
      if (modal) {
        modal.focus();
      }
    });
    // Add escape key listener
    document.addEventListener("keydown", this.handleEscapeKey);
  },
  beforeDestroy() {
    document.removeEventListener("keydown", this.handleEscapeKey);
    // Restore focus to the previously focused element
    if (this.previousActiveElement && this.previousActiveElement.focus) {
      this.previousActiveElement.focus();
    }
  },
  methods: {
    closeModal() {
      EventHub.dispatch(GAME_EVENT.CLOSE_MODAL);
    },
    handleEscapeKey(event) {
      if (event.key === "Escape") {
        this.closeModal();
      }
    }
  }
};
</script>

<template>
  <div
    :id="modalId"
    class="c-modal__inner"
    role="dialog"
    aria-modal="true"
    :aria-labelledby="$slots.header ? titleId : undefined"
    tabindex="-1"
  >
    <div class="c-modal__header">
      <ModalCloseButton @click="closeModal" />
      <span
        v-if="$slots.header"
        :id="titleId"
        class="c-modal__title"
      >
        <slot name="header" />
      </span>
    </div>
    <slot />
  </div>
</template>

<style scoped>
.c-modal__header {
  margin-bottom: 0.5rem;
}
</style>