<script setup lang="ts">
import { ref, watch } from 'vue';
import { useVeSystem } from '../../../providers/veSystem';
import { useTabs, Tab } from '../../../providers/tabs';
import TokenCard from '../TokenCard.vue';
import { useWeb3ModalAccount } from '@web3modal/ethers/vue';
import { useNetwork } from '../../../providers/network';

const { address } = useWeb3ModalAccount();

const { network } = useNetwork();

const searchTerm = ref<string>('');

const { data: veSystems, select, isLoading, fetchByAdmin } = useVeSystem();

const { select: selectTab } = useTabs();

watch([address, network], ([account]) => {
  if (!account) return;

  fetchByAdmin(account);
});

const showConfig = async (id: string) => {
  await select(id);
  selectTab(Tab.VE_SYSTEM_CONFIG);
};

const showRewards = async (id: string) => {
  await select(id);
  selectTab(Tab.REWARDS_DISTRIBUTION);
};

const handleSearch = async () => {
  if (!address.value) return;
  if (searchTerm.value !== '') {
    await fetchByAdmin(address.value, searchTerm.value);
  } else {
    await fetchByAdmin(address.value);
  }
};
</script>

<template>
  <section class="section-container">
    <section class="section-head">
      <div class="address-group">
        <div class="input-group">
          <svg width="16" height="16" class="icon">
            <use href="/images/search.svg#icon"></use>
          </svg>
          <input
            v-model="searchTerm"
            class="input"
            placeholder="Search by locked token address"
          />
        </div>
      </div>
      <button
        class="search-btn btn"
        :disabled="isLoading"
        @click="handleSearch"
      >
        Search
      </button>
    </section>
    <div class="section-body">
      <p>{{ isLoading ? 'loading' : '' }}</p>
      <div v-for="veSystem in veSystems" :key="veSystem.id">
        <TokenCard
          :name="veSystem.votingEscrow.name"
          :vestedToken="veSystem.bptTokenName"
          :totalValueVested="veSystem.votingEscrow.lockedAmount"
          :availableTokensForRewards="
            veSystem.rewardDistributor.rewardTokens.map(rt => rt.name) || []
          "
        />
        <div class="group-buttons">
          <button class="btn-config" @click="showConfig(veSystem.id)">
            veSystem config
          </button>
          <button class="btn-rewards" @click="showRewards(veSystem.id)">
            Rewards Distribution
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.section-container {
  margin: 16px 0;
  display: flex;
  flex-direction: column;
  width: 100%;
}
.section-head {
  border-bottom: 1px solid #e2e8f0;
  padding-bottom: 15px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 10px;
  width: 100%;
}
.dark .section-head {
  border-bottom: 1px solid #3e4c5a;
}

.section-head .address-group {
  display: flex;
  align-items: center;
  gap: 16px;
  height: 30px;
  width: 100%;
  justify-content: flex-end;
}

.section-head .address-group .icon {
  position: absolute;
  top: 7px;
  left: 10px;
  fill: #eaf0f6;
}

.dark .section-head .address-group .icon {
  fill: #3e4c5a;
}

.section-head .address-group .input-group {
  height: 100%;
  position: relative;
  max-width: 250px;
  width: 100%;
}

.section-head .address-group .input {
  background-color: transparent;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  height: 100%;
  width: 100%;
  padding-left: 35px;
  font-size: 14px;
  outline: none;
}

.dark .section-head .address-group .input {
  border: 1px solid #3e4c5a;
}

.section-head .btn {
  height: 30px;
  min-width: 100px;
  background-color: #384aff;
  border-radius: 6px;
  cursor: pointer;
  border: none;
  font-weight: 600;
  font-size: 14px;
  color: #ffffff;
}

.section-head .btn:disabled {
  background-color: rgba(56, 74, 255, 0.2);
  cursor: not-allowed;
  color: #e2e8f0;
}

.dark .section-head .btn:disabled {
  color: #3e4c5a;
}

.section-body {
  margin-top: 16px;
  display: flex;
  flex-direction: column;
  gap: 24px;
  max-height: 800px;
  overflow: auto;
}

.group-buttons {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
  max-width: 560px;
  margin-top: 10px;
  margin-bottom: 20px;
  width: 90%;
  margin-inline: auto;
}
.group-buttons .btn-rewards,
.group-buttons .btn-config {
  width: 150px;
  height: 40px;
  border-radius: 6px;
  border: none;
  background-color: #eaf0f6;
  font-weight: 600;
  cursor: pointer;
}

.dark .group-buttons .btn-rewards,
.dark .group-buttons .btn-config {
  background-color: #384aff;
}
</style>