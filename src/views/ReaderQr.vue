<template>
<StreamBarcodeReader v-if="showCamera" @decode="onDecode" @loaded="onLoaded"></StreamBarcodeReader>
<button @click="openCamera">Verificar qr</button>
<p>Ondecode: {{ ondecode }}</p>
<p>loaded: {{ onloa }}</p>
</template>

<script setup>
import { ref } from "vue";
import {StreamBarcodeReader} from "vue-barcode-reader"

const ondecode = ref(null)
const onloa = ref(null)
const showCamera = ref(false)

const openCamera = ()=>{
    showCamera.value = !showCamera.value
}

const onDecode = (result)=>{
    ondecode.value = result
    console.log('ondecode',result)

}

const onLoaded = (result)=>{
    onloa.value = result
console.log('onloades',result)
}

</script>