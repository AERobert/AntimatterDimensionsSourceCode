<script>
export default {
  name: "ToggleButton",
  props: {
    label: {
      type: String,
      required: false,
      default: ""
    },
    on: {
      type: String,
      required: false,
      default: "ON"
    },
    off: {
      type: String,
      required: false,
      default: "OFF"
    },
    value: {
      type: Boolean,
      required: true
    },
    tooltipClass: {
      type: String,
      required: false,
      default: ""
    },
    tooltipContent: {
      type: String,
      required: false,
      default: ""
    }
  },
  computed: {
    displayText() {
      return `${this.label} ${this.value ? this.on : this.off}`.trim();
    },
    ariaLabel() {
      return `${this.label || "Toggle"}: ${this.value ? this.on : this.off}`;
    },
    tooltipId() {
      return this.tooltipContent ? `toggle-tooltip-${this._uid}` : undefined;
    }
  },
  methods: {
    handleKeydown(event) {
      if (event.key === "Enter" || event.key === " ") {
        event.preventDefault();
        this.emitInput(!this.value);
      }
    }
  }
};
</script>

<template>
  <button
    v-bind="$attrs"
    role="switch"
    :aria-checked="value.toString()"
    :aria-label="ariaLabel"
    :aria-describedby="tooltipId"
    @click="emitInput(!value)"
    @keydown="handleKeydown"
  >
    {{ displayText }}
    <div
      v-if="tooltipClass"
      :id="tooltipId"
      :class="tooltipClass"
      role="tooltip"
    >
      {{ tooltipContent }}
    </div>
  </button>
</template>

