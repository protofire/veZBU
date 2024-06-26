<script setup lang="ts">
import { defineProps, ref, watch } from 'vue';

type ModalPropsType = {
  open: boolean;
  onClose: () => void;
  onSubmit: (penalty: number) => void;
  earlyPenalty: number;
};

const props = defineProps<ModalPropsType>();

const earlyPenaltyInput = ref<number | undefined>();

watch(
  () => props.earlyPenalty,
  value => {
    console.log(value);
    earlyPenaltyInput.value = value;
  }
);
</script>

<template>
  <div v-if="props.open" class="modal-container">
    <div class="modal-popup early-penalty">
      <h3 class="modal-title">Set early penalty speed</h3>
      <div class="body">
        <div class="item-row">
          <p class="item-name">Penalty</p>
          <div class="input-group">
            <input
              v-model="earlyPenaltyInput"
              type="number"
              class="input"
              placeholder="10"
            />
          </div>
        </div>
        <p class="text">
          The penalty coefficient indicating the penalty speed for early unlock.
          Must be between 0 and 50. Default value is 10 means that penalty has
          linear speed.
        </p>
      </div>
      <div class="btn-group">
        <button class="btn close" @click="props.onClose">Close</button>
        <button
          class="btn submit"
          :disabled="
            earlyPenaltyInput === undefined ||
            earlyPenaltyInput.toString() === ''
          "
          @click="
            earlyPenaltyInput !== undefined && props.onSubmit(earlyPenaltyInput)
          "
        >
          Set Penalty
        </button>
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
    left: 40%;
  }
}

@media (max-width: 1000px) {
  .modal-popup {
    max-width: 400px;
  }
}

@media (max-width: 720px) {
  .modal-popup {
    left: calc(50% - 200px);
  }
}

@media (max-width: 460px) {
  .modal-popup {
    left: 5%;
  }
}

.dark .modal-popup {
  background-color: #0d1834;
}

.modal-popup.early-penalty {
  top: 350px;
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
  justify-content: space-between;
  height: 40px;
  margin-bottom: 16px;
  gap: 10px;
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
  padding-inline: 20px;
  font-size: 14px;
  outline: none;
  border: 1px solid #384aff;
  background-color: rgba(56, 74, 255, 0.2);
}

.modal-popup .btn-group {
  display: flex;
  align-items: center;
  gap: 10px;
  justify-content: flex-end;
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

.modal-popup .btn-group .btn.close {
  border: 1px solid #384aff;
  background-color: rgba(56, 74, 255, 0.2);
}

.modal-popup .btn-group .btn:disabled {
  cursor: not-allowed;
  background-color: rgba(56, 74, 255, 0.2);
  color: grey;
}
</style>
