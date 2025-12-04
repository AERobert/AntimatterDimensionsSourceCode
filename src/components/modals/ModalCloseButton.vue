<script>
import PrimaryButton from "@/components/PrimaryButton";

export default {
  name: "ModalCloseButton",
  components: {
    PrimaryButton
  },
  methods: {
    handleClick() {
      this.emitClick();
    }
  }
};
</script>

<template>
  <PrimaryButton
    class="o-primary-btn--modal-close c-modal__close-btn"
    aria-label="Close modal"
    @click="handleClick"
  >
    <span aria-hidden="true">&times;</span>
  </PrimaryButton>
</template>
