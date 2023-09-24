<script setup lag="ts">
const actions = ['encrypt', 'decrypt'];
const action = ref('');

const textInput = ref('');
const shiftAmountInput = ref('');

const getShiftAmount = () => {
  let finalShiftAmount = parseInt(shiftAmountInput.value);
  finalShiftAmount %= 256;
  
  return finalShiftAmount;
};

const handleReset = () => {
  textInput.value = '';
  shiftAmountInput.value = '';
  action.value = '';
};
</script>

<template>
  <div>

    <UCard class="w-3/4 mx-auto">
      <div class="flex flex-col space-y-2">
        <h1>Vigenere</h1>
        <USelect v-model="action" :options="actions" placeholder="Select action" />
        
        <!-- Encrypt -->
        <div v-if="action === 'encrypt'" class="flex flex-col space-y-2">
          <UInput v-model="textInput" type="text" placeholder="Text to encrypt (UTF-8)..." />
          <UInput v-model="shiftAmountInput" type="number" placeholder="Shift amount..." />
          <!-- <UButton block @click="handleEncrypt" label="Encrypt" /> -->
  
          <!-- <UCard v-if="encryptedText" class="w-2/3 mx-auto">
            <div class="flex flex-col space-y-4">
              <h1 class="text-center text-xl font-bold">{{ encryptedText }}</h1>
              <UButton @click="handleReset" block label="Clear" />
            </div>
          </UCard> -->
        </div>

      </div>
    </UCard>

  </div>
</template>