<script>
import ModalCloseButton from "@/components/modals/ModalCloseButton";
import ModalConfirmationCheck from "@/components/modals/ModalConfirmationCheck";
import PrimaryButton from "@/components/PrimaryButton";

export default {
  name: "ModalWrapperChoice",
  components: {
    PrimaryButton,
    ModalConfirmationCheck,
    ModalCloseButton
  },
  props: {
    cancelClass: {
      type: String,
      required: false,
      default: "o-primary-btn--width-medium c-modal-message__okay-btn"
    },
    confirmClass: {
      type: String,
      required: false,
      default: "o-primary-btn--width-medium c-modal-message__okay-btn c-modal__confirm-btn"
    },
    showCancel: {
      type: Boolean,
      required: false,
      default: true
    },
    showConfirm: {
      type: Boolean,
      required: false,
      default: true
    },
    option: {
      type: String,
      required: false,
      default: undefined
    },
    confirmFn: {
      type: Function,
      required: false,
      default: undefined
    },
    cancelFn: {
      type: Function,
      required: false,
      default: undefined
    }
  },
  data() {
    return {
      modalId: `modal-choice-${this._uid}`,
      titleId: `modal-choice-title-${this._uid}`,
      previousActiveElement: null
    };
  },
  mounted() {
    this.previousActiveElement = document.activeElement;
    this.$nextTick(() => {
      const modal = this.$el;
      if (modal) {
        modal.focus();
      }
    });
    document.addEventListener("keydown", this.handleEscapeKey);
  },
  beforeDestroy() {
    document.removeEventListener("keydown", this.handleEscapeKey);
    if (this.previousActiveElement && this.previousActiveElement.focus) {
      this.previousActiveElement.focus();
    }
  },
  created() {
    this.on$(GAME_EVENT.ENTER_PRESSED, this.doConfirm);
  },
  methods: {
    doConfirm() {
      if (this.confirmFn) this.confirmFn();
      else {
        this.$emit("confirm");
        EventHub.dispatch(GAME_EVENT.CLOSE_MODAL);
      }
    },
    doCancel() {
      if (this.cancelFn) this.cancelFn();
      else {
        this.$emit("cancel");
        EventHub.dispatch(GAME_EVENT.CLOSE_MODAL);
      }
    },
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
    class="c-modal-message l-modal-content--centered"
    role="alertdialog"
    aria-modal="true"
    :aria-labelledby="$slots.header ? titleId : undefined"
    tabindex="-1"
  >
    <span class="c-modal__header">
      <ModalCloseButton @click="closeModal" />
      <span
        v-if="$slots.header"
        :id="titleId"
        class="c-modal__title"
      >
        <slot name="header" />
      </span>
    </span>


    <slot />

    <ModalConfirmationCheck
      v-if="option"
      :option="option"
    />

    <div
      class="l-modal-buttons"
      role="group"
      aria-label="Dialog actions"
    >
      <PrimaryButton
        v-if="showCancel"
        :class="cancelClass"
        aria-label="Cancel"
        @click="doCancel"
      >
        <slot name="cancel-text">
          Cancel
        </slot>
      </PrimaryButton>

      <slot name="extra-buttons" />

      <PrimaryButton
        v-if="showConfirm"
        :class="confirmClass"
        aria-label="Confirm"
        @click="doConfirm"
      >
        <slot name="confirm-text">
          Confirm
        </slot>
      </PrimaryButton>
    </div>
  </div>
</template>

<style scoped>
.c-modal__header {
  margin-bottom: 0.5rem;
}
</style>
