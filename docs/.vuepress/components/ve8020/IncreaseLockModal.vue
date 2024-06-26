<script setup lang="ts">
import { defineProps, ref, computed, watch } from 'vue';
import { locale2utc } from '../../utils';

type ModalPropsType = {
  open: boolean;
  allowance: number;
  isLoadingApprove: boolean;
  onClose: () => void;
  onIncreaseLock: (amount: number) => void;
  onIncreaseReleaseTime: (releaseTime: number) => void;
  onApprove: (amount: number) => void;
};

const props = defineProps<ModalPropsType>();

const amountInput = ref<string>('');
const releaseTimeInput = ref<string>('');

watch(
  () => props.open,
  () => {
    amountInput.value = '';
  }
);

const amount = computed<number>(() =>
  amountInput.value === '' ? 0 : parseFloat(amountInput.value)
);

const releaseTime = computed<number>(() => {
  if (releaseTimeInput.value === '') return 0;

  const date = new Date(releaseTimeInput.value);

  return locale2utc(date) / 1000;
});
</script>

<template>
  <div v-if="props.open" class="modal-container">
    <div class="modal-popup early-penalty">
      <h3 class="modal-title">Increase Lock</h3>
      <div class="body">
        <div class="item-row">
          <p class="item-name">Amount</p>
          <div class="action-group">
            <div class="input-group">
              <input
                v-model="amountInput"
                type="number"
                class="input"
                placeholder="12000"
              />
            </div>
            <button
              v-show="allowance >= amount"
              class="btn submit"
              :disabled="amountInput === ''"
              @click="onIncreaseLock(amount)"
            >
              Submit
            </button>
          </div>

          <button
            v-show="allowance < amount"
            class="btn submit approve"
            :disabled="props.isLoadingApprove"
            @click="onApprove(amount)"
          >
            {{ isLoadingApprove ? 'Approving' : 'Approve' }}
          </button>
        </div>
        <div class="item-row">
          <p class="item-name">Release time</p>
          <div class="action-group">
            <div class="input-group">
              <input
                v-model="releaseTimeInput"
                type="datetime-local"
                class="input"
              />
            </div>
            <button
              class="btn submit"
              :disabled="releaseTimeInput === ''"
              @click="onIncreaseReleaseTime(releaseTime)"
            >
              Submit
            </button>
          </div>
        </div>
      </div>
      <div class="btn-group">
        <button class="btn close" @click="props.onClose">Close</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
input[type='number']::-webkit-inner-spin-button,
input[type='number']::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type='number'] {
  -moz-appearance: textfield;
  appearance: textfield;
}

.modal-container {
  position: fixed;
  height: 100%;
  z-index: 100;
  width: 100%;
  top: 0;
  left: 0;
  backdrop-filter: blur(1px);
  background-color: rgba(0, 0, 0, 0.2);
}

.modal-popup {
  position: sticky;
  border-radius: 16px;
  background-color: #eaf0f6;
  border-radius: 16px;
  border: 1px solid #384aff;
  box-shadow: 0px 4px 20px 0px rgba(51, 65, 85, 0.5);
  width: 90%;
  max-width: 560px;
  left: calc(50% - 280px);
  padding: 16px 24px;
}

@media (max-width: 1520px) {
  .modal-popup {
    left: calc(50% - 160px);
  }
}

@media (max-width: 1200px) {
  .modal-popup {
    left: 35%;
  }
}

@media (max-width: 1000px) {
  .modal-popup {
    max-width: 450px;
  }
}

@media (max-width: 720px) {
  .modal-popup {
    left: calc(50% - 225px);
  }
}

@media (max-width: 480px) {
  .modal-popup {
    left: 5%;
  }
}

.dark .modal-popup {
  background-color: #0d1834;
}

.modal-popup.early-penalty {
  top: 300px;
  display: flex;
  flex-direction: column;
}

.modal-popup .modal-title {
  padding: 0;
  margin: 0;
}

.modal-popup .body {
  margin-block: 20px 32px;
}

.modal-popup .body .text {
  margin: 0;
  font-size: 14px;
  font-weight: 500;
}

.modal-popup .body .item-row {
  display: flex;
  align-items: center;
  width: 100%;
  justify-content: center;
  height: 40px;
  margin-bottom: 16px;
  gap: 20px;
}

.modal-popup .body .item-row .action-group {
  display: flex;
  align-items: center;
  height: 100%;
  gap: 20px;
  width: 100%;
}

@media (max-width: 470px) {
  .modal-popup.early-penalty {
    top: 250px;
  }
  .modal-popup .body {
    margin-bottom: 10px;
  }
  .modal-popup .body .item-row {
    flex-direction: column;
    height: 80px;
    align-items: flex-start;
    gap: 5px;
  }
}

.item-row .item-name {
  margin: 0;
  min-width: 90px;
}

.item-row .input-group {
  height: 100%;
  display: flex;
  align-items: center;
  width: 100%;
  max-width: 270px;
  justify-content: flex-end;
}

.item-row .input-group .input {
  border-radius: 6px;
  height: 100%;
  width: 100%;
  padding-inline: 10px;
  font-size: 14px;
  outline: none;
  border: 1px solid #384aff;
  background-color: rgba(56, 74, 255, 0.2);
  min-width: 190px;
}

.item-row .btn.submit {
  width: 90px;
  height: 40px;
  background-color: #384aff;
  border-radius: 6px;
  cursor: pointer;
  border: none;
  font-weight: 600;
  font-size: 14px;
  color: #ffffff;
}

.item-row .btn.submit:disabled {
  cursor: not-allowed;
  background-color: rgba(56, 74, 255, 0.2);
  color: grey;
}

.modal-popup .btn-group {
  display: flex;
  align-items: center;
  gap: 10px;
  justify-content: flex-end;
}

.modal-popup .btn-group .btn:disabled {
  cursor: not-allowed;
  background-color: rgba(56, 74, 255, 0.2);
  color: grey;
}

.modal-popup .btn-group .btn {
  width: 125px;
  height: 40px;
  background-color: #384aff;
  border-radius: 6px;
  cursor: pointer;
  border: none;
  font-weight: 600;
}

.modal-popup .btn-group .btn.submit {
  color: #ffffff;
}

.modal-popup .btn-group .btn.approve {
  background-color: #1dcc37;
}

.modal-popup .btn-group .btn.close {
  border: 1px solid #384aff;
  background-color: rgba(56, 74, 255, 0.2);
}
</style>
