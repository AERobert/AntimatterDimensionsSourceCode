<script>
import CostDisplay from "@/components/CostDisplay";
import DescriptionDisplay from "@/components/DescriptionDisplay";
import EffectDisplay from "@/components/EffectDisplay";

export default {
  name: "InfinityUpgradeButton",
  components: {
    DescriptionDisplay,
    EffectDisplay,
    CostDisplay,
  },
  props: {
    upgrade: {
      type: Object,
      required: true
    },
    columnPosition: {
      type: Number,
      required: false,
      default: 0
    },
    rowPosition: {
      type: Number,
      required: false,
      default: 0
    }
  },
  data() {
    return {
      showWorstChallenge: false,
      worstChallengeString: "",
      isUseless: false,
      canBeBought: false,
      chargePossible: false,
      canBeCharged: false,
      isBought: false,
      isCharged: false,
      isDisabled: false,
      showingCharged: false,
      hasTS31: false,
      ts31Effect: new Decimal(0)
    };
  },
  computed: {
    isBasedOnInfinities() {
      return /(18|27|36|45)Mult/u.test(this.upgrade.id) || this.upgrade.id === "infinitiedMult";
    },
    shiftDown() {
      return ui.view.shiftDown;
    },
    showChargedEffect() {
      return this.chargePossible && (this.isCharged || this.showingCharged || this.shiftDown);
    },
    config() {
      const config = this.upgrade.config;
      return this.showChargedEffect
        ? config.charged
        : config;
    },
    classObject() {
      return {
        "o-infinity-upgrade-btn": true,
        "o-infinity-upgrade-btn--bought": !this.isUseless && this.isBought,
        "o-infinity-upgrade-btn--available": !this.isUseless && !this.isBought && this.canBeBought,
        "o-infinity-upgrade-btn--unavailable": !this.isUseless && !this.isBought && !this.canBeBought,
        "o-infinity-upgrade-btn--useless": this.isUseless,
        "o-pelle-disabled": this.isUseless,
        "o-infinity-upgrade-btn--chargeable": !this.isCharged && this.chargePossible &&
          (this.showingCharged || this.shiftDown),
        "o-infinity-upgrade-btn--charged": this.isCharged,
        "o-pelle-disabled-pointer": this.isUseless
      };
    },
    isImprovedByTS31() {
      return this.hasTS31 && this.isBasedOnInfinities && !this.showChargedEffect;
    },
    accessibilityLabel() {
      const name = this.config.description ?
        (typeof this.config.description === "function" ? this.config.description() : this.config.description) :
        "Upgrade";
      let status = "";
      if (this.isUseless) {
        status = "disabled in Doomed reality";
      } else if (this.isCharged) {
        status = "purchased and charged";
      } else if (this.isBought) {
        status = "purchased";
      } else if (this.canBeBought) {
        status = "available for purchase";
      } else {
        status = "locked, purchase upgrades above first";
      }
      let position = "";
      if (this.columnPosition > 0 || this.rowPosition > 0) {
        position = `, column ${this.columnPosition + 1} row ${this.rowPosition + 1}`;
      }
      return `${name}${position}. Status: ${status}`;
    }
  },
  methods: {
    update() {
      // Note that this component is used by both infinity upgrades and break infinity upgrades
      // (putting this comment here rather than at the top of the component since this function
      // seems more likely to be read).
      const upgrade = this.upgrade;
      this.isBought = upgrade.isBought || upgrade.isCapped;
      this.chargePossible = Ra.unlocks.chargedInfinityUpgrades.canBeApplied &&
        upgrade.hasChargeEffect && !Pelle.isDoomed;
      this.canBeBought = upgrade.canBeBought;
      this.canBeCharged = upgrade.canCharge;
      this.isCharged = upgrade.isCharged;
      // A bit hacky, but the offline passive IP upgrade (the one that doesn't work online)
      // should hide its effect value if offline progress is disabled, in order to be
      // consistent with the other offline progress upgrades which hide as well.
      // Also, the IP upgrade that works both online and offline should not
      // show 0 if its value is 0. This is a bit inconvenient because sometimes,
      // like after eternity, it can be bought but have value 0, but not showing the effect
      // in this case doesn't feel too bad. Other upgrades, including the cost scaling
      // rebuyables, should never hide their effect.
      this.isDisabled = upgrade.config.isDisabled && upgrade.config.isDisabled(upgrade.config.effect());
      this.isUseless = Pelle.uselessInfinityUpgrades.includes(upgrade.id) && Pelle.isDoomed;
      this.hasTS31 = TimeStudy(31).canBeApplied;
      if (!this.isDisabled && this.isImprovedByTS31) this.ts31Effect = Decimal.pow(upgrade.config.effect(), 4);
      if (upgrade.id !== "challengeMult") return;
      this.showWorstChallenge = upgrade.effectValue !== upgrade.cap &&
        player.challenge.normal.bestTimes.sum() < Number.MAX_VALUE;
      const worstChallengeTime = GameCache.worstChallengeTime.value;
      const worstChallengeIndex = 2 + player.challenge.normal.bestTimes.indexOf(worstChallengeTime);
      this.worstChallengeString = `(Challenge ${worstChallengeIndex}: ${timeDisplayShort(worstChallengeTime)})`;
    }
  }
};
</script>

<template>
  <button
    :class="classObject"
    :aria-label="accessibilityLabel"
    :aria-pressed="isBought"
    tabindex="0"
    @mouseenter="showingCharged = canBeCharged"
    @mouseleave="showingCharged = false"
    @click="upgrade.purchase()"
  >
    <span :class="{ 'o-pelle-disabled': isUseless }">
      <DescriptionDisplay
        :config="config"
      />
      <span v-if="showWorstChallenge">
        <br>
        {{ worstChallengeString }}
      </span>
      <EffectDisplay
        v-if="!isDisabled"
        br
        :config="config"
      />
      <template v-if="!isDisabled && isImprovedByTS31">
        <br>
        After TS31: {{ formatX(ts31Effect, 2, 2) }}
      </template>
    </span>
    <CostDisplay
      v-if="!isBought"
      br
      :config="config"
      name="Infinity Point"
    />
    <span class="visually-hidden" v-if="isBought"> (Purchased)</span>
    <span class="visually-hidden" v-else-if="!canBeBought"> (Locked)</span>
    <slot />
  </button>
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
