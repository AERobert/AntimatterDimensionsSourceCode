<script>
import AntimatterDimensionProgressBar from "./AntimatterDimensionProgressBar";
import AntimatterDimensionRow from "./ClassicAntimatterDimensionRow";
import AntimatterDimensionsTabHeader from "./ClassicAntimatterDimensionsTabHeader";
import AntimatterGalaxyRow from "./ClassicAntimatterGalaxyRow";
import DimensionBoostRow from "./ClassicDimensionBoostRow";
import PrimaryButton from "@/components/PrimaryButton";
import TickspeedRow from "./TickspeedRow";

export default {
  name: "ClassicAntimatterDimensionsTab",
  components: {
    PrimaryButton,
    AntimatterDimensionRow,
    AntimatterDimensionsTabHeader,
    AntimatterGalaxyRow,
    DimensionBoostRow,
    AntimatterDimensionProgressBar,
    TickspeedRow,
  },
  data() {
    return {
      hasDimensionBoosts: false,
      isQuickResetAvailable: false,
      isSacrificeUnlocked: false,
      buy10Mult: new Decimal(0),
      currentSacrifice: new Decimal(0),
      hasRealityButton: false,
      multiplierText: ""
    };
  },
  methods: {
    update() {
      this.hasDimensionBoosts = player.dimensionBoosts > 0;
      this.isQuickResetAvailable = Player.isInAntimatterChallenge && Player.antimatterChallenge.isQuickResettable;
      this.isSacrificeUnlocked = Sacrifice.isVisible;
      this.buy10Mult.copyFrom(AntimatterDimensions.buyTenMultiplier);
      this.currentSacrifice.copyFrom(Sacrifice.totalBoost);
      this.hasRealityButton = PlayerProgress.realityUnlocked() || TimeStudy.reality.isBought;
      const sacText = this.isSacrificeUnlocked
        ? ` | Dimensional Sacrifice multiplier: ${formatX(this.currentSacrifice, 2, 2)}`
        : "";
      this.multiplierText = `Buy 10 Dimension purchase multiplier: ${formatX(this.buy10Mult, 2, 2)}${sacText}`;
    },
    quickReset() {
      softReset(-1, true, true);
    }
  }
};
</script>

<template>
  <div class="l-old-ui-antimatter-dim-tab">
    <h2 class="c-subtab-title">Antimatter Dimensions</h2>
    <AntimatterDimensionsTabHeader />
    <p class="c-multiplier-info">{{ multiplierText }}</p>

    <section aria-labelledby="tickspeed-section">
      <h3 id="tickspeed-section" class="visually-hidden">Tickspeed</h3>
      <TickspeedRow />
    </section>

    <section aria-labelledby="dimensions-section">
      <h3 id="dimensions-section" class="visually-hidden">Dimensions</h3>
      <div class="l-dimensions-container" role="list" aria-label="Antimatter Dimensions list">
        <AntimatterDimensionRow
          v-for="tier in 8"
          :key="tier"
          :tier="tier"
          role="listitem"
        />
      </div>
    </section>

    <section aria-labelledby="prestige-section">
      <h3 id="prestige-section" class="visually-hidden">Prestige Options</h3>
      <DimensionBoostRow />
      <AntimatterGalaxyRow />
    </section>

    <PrimaryButton
      v-if="isQuickResetAvailable"
      class="o-primary-btn--quick-reset"
      aria-label="Perform a quick reset"
      @click="quickReset"
    >
      Perform a Dimension Boost reset
      <span v-if="hasDimensionBoosts"> but lose a Dimension Boost</span>
      <span v-else> for no gain</span>
    </PrimaryButton>
    <div class="l-flex" aria-hidden="true" />
    <AntimatterDimensionProgressBar class="l-antimatter-dim-tab__progress_bar" />
  </div>
</template>

<style scoped>
.c-subtab-title {
  font-size: 2rem;
  margin-bottom: 0.5rem;
}

.c-multiplier-info {
  margin: 0.5rem 0;
}

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

.l-flex {
  flex: 1 0;
}
</style>
