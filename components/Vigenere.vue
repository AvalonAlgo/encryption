<script setup lang="ts">
import {Base64} from 'js-base64';

const actions = ['encrypt', 'decrypt'];
const action = ref('');

const textInput = ref('');
const encryptionKey = ref('');
const encryptedText = ref('');
const decryptedText = ref('');

const handleEncrypt = () => {
  if (!encryptionKey || !textInput) {
    return;
  };

  const encoder: TextEncoder = new TextEncoder();
  const byteArray: Uint8Array = encoder.encode(textInput.value);
  const byteArrayKey: Uint8Array = encoder.encode(encryptionKey.value);

  for (let i = 0; i < byteArray.length; i++) {
    byteArray[i] = (byteArray[i] + byteArrayKey[i % byteArrayKey.length]) % 256;
  };

  encryptedText.value = Base64.fromUint8Array(byteArray);
};

const handleDecrypt = () => {
  if (!encryptionKey || !textInput) {
    return;
  };
  
  const encoder: TextEncoder = new TextEncoder();
  const cypheredByteArray = Base64.toUint8Array(textInput.value);
  const keyByteArray: Uint8Array = encoder.encode(encryptionKey.value);

  for (let i = 0; i < cypheredByteArray.length; i++) {
    cypheredByteArray[i] = (cypheredByteArray[i] - keyByteArray[i % keyByteArray.length] + 256) % 256;
  };

  const decoder: TextDecoder = new TextDecoder();
  decryptedText.value = decoder.decode(cypheredByteArray);
};

const handleReset = () => {
  textInput.value = '';
  encryptionKey.value = '';
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
          <h2 class=" text-lg mb-1 font-medium title-font">Vigenere</h2>
          <USelect v-model="action" :options="actions" placeholder="Select action" class="mb-2" />

          <!-- Encrypt -->
          <div v-if="action === 'encrypt'" class="flex flex-col space-y-2">
            <UInput v-model="textInput" type="text" placeholder="Text to encrypt (UTF-8)..." />
            <UInput v-model="encryptionKey" type="text" placeholder="Key..." />
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
              <UInput v-model="encryptionKey" type="text" placeholder="Key..." />
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