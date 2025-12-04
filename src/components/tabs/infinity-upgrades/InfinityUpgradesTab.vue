<script>
import InfinityUpgradeButton from "@/components/InfinityUpgradeButton";
import IpMultiplierButton from "./IpMultiplierButton";
import PrimaryButton from "@/components/PrimaryButton";

export default {
  name: "InfinityUpgradesTab",
  components: {
    PrimaryButton,
    InfinityUpgradeButton,
    IpMultiplierButton
  },
  data() {
    return {
      isUseless: false,
      chargeUnlocked: false,
      totalCharges: 0,
      chargesUsed: 0,
      disCharge: false,
      ipMultSoftCap: 0,
      ipMultHardCap: 0,
      eternityUnlocked: false,
      bottomRowUnlocked: false,
      styleOfColumnBg: undefined
    };
  },
  computed: {
    grid() {
      return [
        [
          InfinityUpgrade.totalTimeMult,
          InfinityUpgrade.dim18mult,
          InfinityUpgrade.dim36mult,
          InfinityUpgrade.resetBoost
        ],
        [
          InfinityUpgrade.buy10Mult,
          InfinityUpgrade.dim27mult,
          InfinityUpgrade.dim45mult,
          InfinityUpgrade.galaxyBoost
        ],
        [
          InfinityUpgrade.thisInfinityTimeMult,
          InfinityUpgrade.unspentIPMult,
          InfinityUpgrade.dimboostMult,
          InfinityUpgrade.ipGen
        ],
        [
          InfinityUpgrade.skipReset1,
          InfinityUpgrade.skipReset2,
          InfinityUpgrade.skipReset3,
          InfinityUpgrade.skipResetGalaxy
        ]
      ];
    },
    allColumnUpgrades() {
      return this.grid.flat();
    },
    disChargeClassObject() {
      return {
        "o-primary-btn--subtab-option": true,
        "o-primary-btn--charged-respec-active": this.disCharge
      };
    },
    offlineIpUpgrade: () => InfinityUpgrade.ipOffline
  },
  watch: {
    disCharge(newValue) {
      player.celestials.ra.disCharge = newValue;
    }
  },
  created() {
    this.on$(GAME_EVENT.INFINITY_UPGRADE_BOUGHT, () => this.setStyleOfColumnBg());
    this.on$(GAME_EVENT.INFINITY_UPGRADE_CHARGED, () => this.setStyleOfColumnBg());
    this.on$(GAME_EVENT.INFINITY_UPGRADES_DISCHARGED, () => this.setStyleOfColumnBg());

    this.setStyleOfColumnBg();
  },
  methods: {
    update() {
      this.isUseless = Pelle.isDoomed;
      this.chargeUnlocked = Ra.unlocks.chargedInfinityUpgrades.canBeApplied && !Pelle.isDoomed;
      this.totalCharges = Ra.totalCharges;
      this.chargesUsed = Ra.totalCharges - Ra.chargesLeft;
      this.disCharge = player.celestials.ra.disCharge;
      this.ipMultSoftCap = GameDatabase.infinity.upgrades.ipMult.costIncreaseThreshold;
      this.ipMultHardCap = GameDatabase.infinity.upgrades.ipMult.costCap;
      this.eternityUnlocked = PlayerProgress.current.isEternityUnlocked;
      this.bottomRowUnlocked = Achievement(41).isUnlocked;
    },
    btnClassObject(column) {
      const classObject = {
        "l-infinity-upgrade-grid__cell": true
      };
      if (column > 0) {
        // Indexing starts from 0, while css classes start from 2 (and first column has default css class)
        classObject[`o-infinity-upgrade-btn--color-${column + 1}`] = true;
      }
      return classObject;
    },
    getColumnColor(location) {
      if (location.isCharged) return "var(--color-teresa--base)";
      if (location.isBought) return "var(--color-infinity)";
      return "transparent";
    },
    setStyleOfColumnBg() {
      this.styleOfColumnBg = this.grid.map(col => ({
        background:
          `linear-gradient(to bottom,
          ${this.getColumnColor(col[0])} 15%,
          ${this.getColumnColor(col[1])} 35% 40%,
          ${this.getColumnColor(col[2])} 60% 65%,
          ${this.getColumnColor(col[3])} 85% 100%`
      }));
    },
  }
};
</script>

<template>
  <div class="l-infinity-upgrades-tab">
    <h2 class="c-subtab-title">Infinity Upgrades</h2>

    <section
      v-if="chargeUnlocked"
      class="c-subtab-option-container"
      aria-labelledby="charge-section"
    >
      <h3 id="charge-section" class="visually-hidden">Charged Upgrades</h3>
      <PrimaryButton
        :class="disChargeClassObject"
        :aria-pressed="disCharge"
        aria-label="Respec Charged Infinity Upgrades on next Reality"
        @click="disCharge = !disCharge"
      >
        Respec Charged Infinity Upgrades on next Reality
      </PrimaryButton>
      <p aria-live="polite">
        You have charged {{ formatInt(chargesUsed) }}/{{ formatInt(totalCharges) }} Infinity Upgrades.
        Charged Infinity Upgrades have their effect altered.
        <br>
        Hold shift to show Charged Infinity Upgrades. You can freely respec your choices on Reality.
      </p>
    </section>

    <p v-if="isUseless" role="alert">
      You cannot Charge Infinity Upgrades while Doomed.
    </p>

    <p class="c-upgrade-instructions">
      Within each column, the upgrades must be purchased from top to bottom.
    </p>

    <section aria-labelledby="main-upgrades">
      <h3 id="main-upgrades" class="visually-hidden">Main Infinity Upgrades Grid</h3>
      <div
        class="l-infinity-upgrade-grid l-infinity-upgrades-tab__grid"
        role="grid"
        aria-label="Infinity Upgrades grid, 4 columns, upgrades must be bought top to bottom"
      >
        <div
          v-for="(column, columnId) in grid"
          :key="columnId"
          class="c-infinity-upgrade-grid__column"
          role="row"
          :aria-label="`Column ${columnId + 1}`"
        >
          <InfinityUpgradeButton
            v-for="(upgrade, rowId) in column"
            :key="upgrade.id"
            :upgrade="upgrade"
            :column-position="columnId"
            :row-position="rowId"
            :class="btnClassObject(columnId)"
            role="gridcell"
          />
          <div
            class="c-infinity-upgrade-grid__column--background"
            :style="styleOfColumnBg[columnId]"
            aria-hidden="true"
          />
        </div>
      </div>
    </section>

    <section
      v-if="bottomRowUnlocked"
      class="l-infinity-upgrades-bottom-row"
      aria-labelledby="bonus-upgrades"
    >
      <h3 id="bonus-upgrades" class="visually-hidden">Bonus Upgrades</h3>
      <IpMultiplierButton class="l-infinity-upgrades-tab__mult-btn" />
      <InfinityUpgradeButton
        :upgrade="offlineIpUpgrade"
        :class="btnClassObject(1)"
      />
    </section>

    <p v-if="eternityUnlocked && bottomRowUnlocked" class="c-upgrade-note">
      The Infinity Point multiplier becomes more expensive
      <br>
      above {{ formatPostBreak(ipMultSoftCap) }} Infinity Points, and cannot be purchased past
      {{ formatPostBreak(ipMultHardCap) }} Infinity Points.
    </p>
  </div>
</template>

<style scoped>
.c-subtab-title {
  font-size: 2rem;
  margin-bottom: 1rem;
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

.c-upgrade-instructions {
  margin: 0.5rem 0;
  font-style: italic;
}

.c-upgrade-note {
  margin-top: 1rem;
}

.c-infinity-upgrade-grid__column {
  display: flex;
  overflow: hidden;
  flex-direction: column;
  position: relative;
  border-radius: var(--var-border-radius, 0.3rem);
  margin: 0 0.3rem;
}

.c-infinity-upgrade-grid__column--background {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
  opacity: 0.7;
}

.s-base--dark .c-infinity-upgrade-grid__column--background {
  opacity: 0.5;
}

.l-infinity-upgrades-bottom-row .l-infinity-upgrade-grid__cell,
.l-infinity-upgrades-bottom-row .l-infinity-upgrades-tab__mult-btn {
  margin: 0.5rem 1.1rem;
}
</style>
