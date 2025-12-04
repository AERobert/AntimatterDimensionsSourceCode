<script>
export default {
  name: "AutobuyerToggleLabel",
  props: {
    isActive: Boolean,
    isDisabled: Boolean,
    name: {
      type: String,
      required: true
    },
  },
  computed: {
    autobuyerToggleClass() {
      if (this.isDisabled) {
        return this.isActive ? "fas fa-pause" : "fas fa-times";
      }
      return this.isActive ? "fas fa-check" : "fas fa-times";
    },
    autobuyerStateClass() {
      if (this.isDisabled) {
        return {
          "o-autobuyer-toggle-checkbox__label": true,
          "o-autobuyer-toggle-checkbox__label--active-paused": this.isActive,
          "o-autobuyer-toggle-checkbox__label--deactive-paused": !this.isActive,
          "o-autobuyer-toggle-checkbox__label--disabled": this.isDisabled
        };
      }
      return {
        "o-autobuyer-toggle-checkbox__label": true,
        "o-autobuyer-toggle-checkbox__label--active": this.isActive,
        "o-autobuyer-toggle-checkbox__label--disabled": this.isDisabled
      };
    },
    stateDescription() {
      if (this.isDisabled) {
        return this.isActive ? "paused" : "disabled";
      }
      return this.isActive ? "active" : "inactive";
    },
    ariaLabel() {
      return `${this.name} autobuyer: ${this.stateDescription}. Click to toggle.`;
    },
    checkboxId() {
      return `autobuyer-${this.name.replace(/\s+/gu, "-").toLowerCase()}`;
    }
  },
  methods: {
    handleKeydown(event) {
      if (event.key === "Enter" || event.key === " ") {
        event.preventDefault();
        this.emitClick();
      }
    }
  }
};
</script>

<template>
  <div
    class="l-autobuyer-box__footer"
    role="switch"
    :aria-checked="isActive && !isDisabled"
    :aria-label="ariaLabel"
    :aria-disabled="isDisabled"
    tabindex="0"
    @click="emitClick"
    @keydown="handleKeydown"
  >
    <label
      :class="autobuyerStateClass"
      :for="checkboxId"
      aria-hidden="true"
    >
      <span :class="autobuyerToggleClass" />
    </label>
    <input
      :id="checkboxId"
      :checked="isActive && !isDisabled"
      :disabled="isDisabled"
      :name="name"
      type="checkbox"
      class="visually-hidden"
      tabindex="-1"
      @change="emitClick"
    >
    <span class="visually-hidden">
      {{ name }} autobuyer is {{ stateDescription }}
    </span>
  </div>
</template>

<style scoped>
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
