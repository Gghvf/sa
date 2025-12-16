<template>
  <div :class="$style.calculator">
    <h2 :class="$style.title">Калькулятор подсетей</h2>

    <form @submit.prevent="calculate">
      <div :class="$style.fieldGroup">
        <!-- Поле ввода IP -->
        <UiField label="IP-адрес">
          <UiInput
            v-model="ip"
            placeholder="192.168.1.150"
            :class="{ [$style.error]: !isIpValidComputed }"
          />
        </UiField>

        <!-- Селект маски -->
        <UiField label="Маска подсети">
          <UiSelect
            v-model="selectedMask"
            :options="maskOptions"
          />
        </UiField>
      </div>

      <!-- Кнопка -->
      <UiButton
        type="submit"
        :isDisabled="!isIpValidComputed"
        layout="primary"
      >
        Рассчитать
      </UiButton>
    </form>

    <!-- Вывод результатов -->
    <div v-if="results" :class="$style.results">
      <p><strong>Введённый IP:</strong> {{ results.ip }}</p>
      <p><strong>Выбранная маска:</strong> {{ results.mask }}</p>
      <p><strong>Адрес сети:</strong> {{ results.networkAddress }}</p>
      <p><strong>Количество возможных адресов:</strong> {{ results.addressesCount }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { UiField, UiInput, UiSelect, UiButton } from 'maskcalculatorcomponents';
import maskOptions from '../constants/maskOptions';
import { isIpValid, getNetworkAddress, getAddressesCount } from '../utils/networkCalculations';

const ip = ref('');
const selectedMask = ref(maskOptions[24]);

// Вычисляемое свойство для проверки валидности IP
const isIpValidComputed = computed(() => isIpValid(ip.value));

// Состояние для хранения результатов
const results = ref<{ ip: string; mask: string; networkAddress: string; addressesCount: number } | null>(null);

// Функция расчёта
const calculate = () => {
  if (!isIpValidComputed.value) return;

  const networkAddr = getNetworkAddress(ip.value, selectedMask.value);
  const count = getAddressesCount(selectedMask.value);

  results.value = {
    ip: ip.value,
    mask: selectedMask.value,
    networkAddress: networkAddr,
    addressesCount: count,
  };
};
</script>

<style module lang="scss">
// Подключаем общие переменные, если они есть
@import '../styles/main.scss';

.calculator {
  max-width: 500px;
  margin: 2rem auto;
  padding: 2rem;
  border: 1px solid var(--color-gray);
  border-radius: var(--border-radius, 20px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  background-color: var(--color-white);
}

.title {
  text-align: center;
  margin-bottom: 1.5rem;
  color: var(--color-black);
}

.fieldGroup {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.error {
  border-color: var(--color-error) !important;
}

.results {
  margin-top: 1.5rem;
  padding: 1rem;
  background-color: var(--color-gray-light);
  border-radius: 8px;
  border-left: 4px solid var(--color-primary);

  p {
    margin: 0.5rem 0;
  }
}
</style>