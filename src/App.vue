<script setup>
import { ref } from 'vue'
import QRCode from 'qrcode'

const url = ref('')
const qrCodeDataUrl = ref('')
const errorMessage = ref('')

const generateQRCode = async () => {
  errorMessage.value = ''
  
  if (!url.value.trim()) {
    errorMessage.value = 'Please enter a URL'
    return
  }

  try {
    // Generate QR code as data URL
    const dataUrl = await QRCode.toDataURL(url.value, {
      width: 300,
      margin: 2,
      color: {
        dark: '#000000',
        light: '#FFFFFF'
      }
    })
    qrCodeDataUrl.value = dataUrl
  } catch (err) {
    errorMessage.value = 'Failed to generate QR code: ' + err.message
    console.error(err)
  }
}

const downloadQRCode = () => {
  if (!qrCodeDataUrl.value) return

  const link = document.createElement('a')
  link.download = 'qrcode.png'
  link.href = qrCodeDataUrl.value
  link.click()
}
</script>

<template>
  <div class="container">
    <h1>QR Code Generator</h1>
    <p class="subtitle">Enter a URL to generate a QR code</p>

    <div class="input-section">
      <input
        v-model="url"
        type="text"
        placeholder="https://example.com"
        @keyup.enter="generateQRCode"
        class="url-input"
      />
      <button @click="generateQRCode" class="generate-btn">
        Generate QR Code
      </button>
    </div>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <div v-if="qrCodeDataUrl" class="qr-section">
      <img :src="qrCodeDataUrl" alt="QR Code" class="qr-image" />
      <button @click="downloadQRCode" class="download-btn">
        Download QR Code
      </button>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
}

h1 {
  color: #42b883;
  margin-bottom: 0.5rem;
}

.subtitle {
  color: #666;
  margin-bottom: 2rem;
}

.input-section {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.url-input {
  flex: 1;
  padding: 0.75rem;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 4px;
  transition: border-color 0.3s;
}

.url-input:focus {
  outline: none;
  border-color: #42b883;
}

.generate-btn,
.download-btn {
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  font-weight: 600;
  color: white;
  background-color: #42b883;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.generate-btn:hover,
.download-btn:hover {
  background-color: #35a372;
}

.generate-btn:active,
.download-btn:active {
  background-color: #2d8c60;
}

.error {
  color: #dc3545;
  margin-top: 0.5rem;
}

.qr-section {
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.qr-image {
  border: 2px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

@media (max-width: 600px) {
  .input-section {
    flex-direction: column;
  }
}
</style>
