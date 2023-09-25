<script setup lang="ts">
const actions = ['encrypt', 'decrypt'];
const action = ref('');

const textInput = ref('');
const shiftAmountInput = ref('');
const encryptedText = ref('');
const decryptedText = ref('');

const getShiftAmount = () => {
  let finalShiftAmount = parseInt(shiftAmountInput.value);
  finalShiftAmount %= 256;
  
  return finalShiftAmount;
};

const bytesToBase64 = (bytes: Uint8Array) => {
  const binString = String.fromCodePoint(...bytes);
  return btoa(binString);
};

function base64ToBytes(base64: string) {
  const binString = atob(base64);
  return Uint8Array.from(binString, (m) => m.codePointAt(0));
};

const handleEncrypt = () => {
  const encoder: TextEncoder = new TextEncoder();
  const bitsArray: Uint8Array = encoder.encode(textInput.value);

  const shiftAmount = getShiftAmount();

  for (let i = 0; i < bitsArray.length; i++) {
    bitsArray[i] += shiftAmount;
  };

  const decoder: TextDecoder = new TextDecoder();
  const resultString: string = decoder.decode(bitsArray);

  encryptedText.value = bytesToBase64(encoder.encode(resultString));
};

// for decrypting
const handleDecrypt = () => {
  const decoder: TextDecoder = new TextDecoder();
  const cypheredString = decoder.decode(base64ToBytes(textInput.value));

  const encoder: TextEncoder = new TextEncoder();
  const bitsArray: Uint8Array = encoder.encode(cypheredString);

  const shiftAmount = getShiftAmount();

  for (let i = 0; i < bitsArray.length; i++) {
    bitsArray[i] -= shiftAmount;
  };

  decryptedText.value = decoder.decode(bitsArray);
};

const handleReset = () => {
  textInput.value = '';
  shiftAmountInput.value = '';
  encryptedText.value = '';
  decryptedText.value = '';
  action.value = '';
};
</script>

<template>
  <div>
    <section class="body-font relative">
      <div class="container px-5 py-12 mx-auto flex">
        <div class="rounded-lg p-8 flex flex-col md:ml-auto w-full mt-10 md:mt-0 relative z-10 shadow-md">
          <h2 class=" text-lg mb-1 font-medium title-font">Caesar</h2>
          <USelect v-model="action" :options="actions" placeholder="Select action" class="mb-2" />

          <!-- Encrypt -->
          <div v-if="action === 'encrypt'" class="flex flex-col space-y-2">
            <UInput required v-model="textInput" type="text" placeholder="Text to encrypt (UTF-8)..." />
            <UInput required v-model="shiftAmountInput" type="number" placeholder="Shift amount..." />
            <UButton block @click="handleEncrypt" label="Encrypt" />
    
            <UCard v-if="encryptedText" class="w-full mx-auto">
              <div class="flex flex-col space-y-4">
                <h1 class="text-center text-xl font-bold break-words">{{ encryptedText }}</h1>
                <UButton @click="handleReset" block label="Clear" />
              </div>
            </UCard>
          </div>

          <!-- Decrypt -->
          <div class="flex flex-col space-y-2">      
            <div v-if="action === 'decrypt'" class="flex flex-col space-y-2">
              <UInput v-model="textInput" type="text" placeholder="Text to decrypt (Base64)..." />
              <UInput v-model="shiftAmountInput" type="number" placeholder="Shift amount..." />
              <UButton block @click="handleDecrypt" label="Decrypt" />
      
              <UCard v-if="decryptedText" class="w-full mx-auto">
                <div class="flex flex-col space-y-4">
                  <h1 class="text-center text-xl font-bold break-words">{{ decryptedText }}</h1>
                  <UButton @click="handleReset" block label="Clear" />
                </div>
              </UCard>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>