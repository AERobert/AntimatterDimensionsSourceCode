<script>
export default {
  name: "PrimaryButton",
  props: {
    enabled: {
      type: Boolean,
      required: false,
      default: true
    },
    ariaLabel: {
      type: String,
      required: false,
      default: ""
    }
  },
  computed: {
    classObject() {
      return {
        "o-primary-btn--disabled": !this.enabled,
      };
    },
    accessibilityAttrs() {
      const attrs = {
        "aria-disabled": this.enabled ? undefined : "true",
        tabindex: this.enabled ? "0" : "-1"
      };
      if (this.ariaLabel) {
        attrs["aria-label"] = this.ariaLabel;
      }
      return attrs;
    }
  },
  methods: {
    handleKeydown(event) {
      if (event.key === "Enter" || event.key === " ") {
        event.preventDefault();
        if (this.enabled) {
          this.$emit("click", event);
        }
      }
    }
  }
};
</script>

<template>
  <button
    class="o-primary-btn"
    :class="classObject"
    v-bind="accessibilityAttrs"
    @keydown="handleKeydown"
    v-on="$listeners"
  >
    <slot />
  </button>
</template>

