<template>
  <main class="bg-dark main-container min-vh-100">
    <div class="container-xl">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-md-2 m-5 w-auto">
        <div class="col">
          <div class="d-grid gap-2 mt-2">
            <button class="btn btn-primary" type="button" @click="openCamera">
              Leer QR
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="bi bi-camera-fill"
                viewBox="0 0 16 16"
              >
                <path d="M10.5 8.5a2.5 2.5 0 1 1-5 0 2.5 2.5 0 0 1 5 0" />
                <path
                  d="M2 4a2 2 0 0 0-2 2v6a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V6a2 2 0 0 0-2-2h-1.172a2 2 0 0 1-1.414-.586l-.828-.828A2 2 0 0 0 9.172 2H6.828a2 2 0 0 0-1.414.586l-.828.828A2 2 0 0 1 3.172 4zm.5 2a.5.5 0 1 1 0-1 .5.5 0 0 1 0 1m9 2.5a3.5 3.5 0 1 1-7 0 3.5 3.5 0 0 1 7 0"
                />
              </svg>
            </button>
            <QrcodeStream
              v-if="showCamera"
              @detect="onDecode"
              :track="paintOutline"
              :formats="selectedBarcodeFormats"
            ></QrcodeStream>
          </div>
        </div>
        <div class="col">
          <div class="card mt-2 bg-secondary">
            <div class="card-header text-white fs-5 fw-bolder">
              Estado de la invitación
            </div>
            <div v-if="showStatus" class="card-body">
              <div
                v-if="errorFetch"
                class="alert alert-warning d-flex align-items-center"
                role="alert"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="16"
                  height="16"
                  fill="currentColor"
                  class="bi bi-exclamation-triangle-fill"
                  viewBox="0 0 16 16"
                >
                  <path
                    d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5m.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2"
                  />
                </svg>
                <div style="margin-left: 1rem">
                  El código QR escaneado no es valido
                </div>
              </div>
              <div v-else>
                <h5 class="card-title text-white fw-lighter">
                  <span
                    class="badge rounded-pill"
                    :class="
                      detail.verified_at != null ? 'bg-success' : 'bg-danger'
                    "
                  >
                    {{
                      detail.verified_at !== null
                        ? "QR Verificado"
                        : "QR No Verificado"
                    }}
                    <svg
                    v-if="detail.verified_at == null"
                      xmlns="http://www.w3.org/2000/svg"
                      width="16"
                      height="16"
                      fill="currentColor"
                      class="bi bi-x-circle"
                      viewBox="0 0 16 16"
                    >
                      <path
                        d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14m0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16"
                      />
                      <path
                        d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708"
                      />
                    </svg>
                    <svg
                    v-else
                      xmlns="http://www.w3.org/2000/svg"
                      width="16"
                      height="16"
                      fill="currentColor"
                      class="bi bi-check2-circle"
                      viewBox="0 0 16 16"
                    >
                      <path
                        d="M2.5 8a5.5 5.5 0 0 1 8.25-4.764.5.5 0 0 0 .5-.866A6.5 6.5 0 1 0 14.5 8a.5.5 0 0 0-1 0 5.5 5.5 0 1 1-11 0"
                      />
                      <path
                        d="M15.354 3.354a.5.5 0 0 0-.708-.708L8 9.293 5.354 6.646a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0z"
                      />
                    </svg>
                  </span>
                </h5>
                <p class="card-text text-white fst-italic fw-bolder">
                  {{
                    detail.verified_at == null
                      ? "El código QR escaneado no está verificado"
                      : `El código QR fue verificado el ${formatDate(
                          detail.verified_at
                        )} a las ${formatTime(detail.verified_at)}`
                  }}
                </p>
                <button
                  v-if="detail.verified_at == null"
                  class="btn btn-primary"
                  type="button"
                  @click="verifyCode"
                >
                  <span>Verificar Código QR </span>
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="16"
                    height="16"
                    fill="currentColor"
                    class="bi bi-patch-check-fill"
                    viewBox="0 0 16 16"
                  >
                    <path
                      d="M10.067.87a2.89 2.89 0 0 0-4.134 0l-.622.638-.89-.011a2.89 2.89 0 0 0-2.924 2.924l.01.89-.636.622a2.89 2.89 0 0 0 0 4.134l.637.622-.011.89a2.89 2.89 0 0 0 2.924 2.924l.89-.01.622.636a2.89 2.89 0 0 0 4.134 0l.622-.637.89.011a2.89 2.89 0 0 0 2.924-2.924l-.01-.89.636-.622a2.89 2.89 0 0 0 0-4.134l-.637-.622.011-.89a2.89 2.89 0 0 0-2.924-2.924l-.89.01zm.287 5.984-3 3a.5.5 0 0 1-.708 0l-1.5-1.5a.5.5 0 1 1 .708-.708L7 8.793l2.646-2.647a.5.5 0 0 1 .708.708"
                    />
                  </svg>
                </button>
              </div>
            </div>

            <div class="card-footer text-white text-center fs-6 fw-lighter">
              Todos los derechos reservados
            </div>
          </div>
        </div>
      </div>
      <div
        v-if="alert"
        class="alert alert-success d-flex align-items-center mt-2"
        role="alert"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-check-circle-fill"
          viewBox="0 0 16 16"
        >
          <path
            d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0m-3.97-3.03a.75.75 0 0 0-1.08.022L7.477 9.417 5.384 7.323a.75.75 0 0 0-1.06 1.06L6.97 11.03a.75.75 0 0 0 1.079-.02l3.992-4.99a.75.75 0 0 0-.01-1.05z"
          />
        </svg>
        <div style="margin-left: 1rem">Código QR verificado exitosamente !</div>
      </div>
    </div>
    <div class="fixed-bottom" style="margin-bottom: 1rem; margin-left: 75%;">
      <button type="button" class="btn btn-warning" @click="logout">Cerrar Sesión</button>
    </div>
  </main>
</template>

<script setup>
import { ref, watchEffect, computed, onMounted } from "vue";
import { api } from "../api/client";
import { QrcodeStream } from "vue-qrcode-reader";
import router from "../router";



const ondecode = ref("");

const showCamera = ref(false);

const showStatus = ref(false);

const detail = ref({});

const barcodeFormats = ref({
  aztec: true,
  code_128: true,
  code_39: true,
  code_93: true,
  codabar: true,
  databar: true,
  databar_expanded: true,
  data_matrix: true,
  dx_film_edge: true,
  ean_13: true,
  ean_8: true,
  itf: true,
  maxi_code: true,
  micro_qr_code: true,
  pdf417: true,
  qr_code: true,
  rm_qr_code: true,
  upc_a: true,
  upc_e: true,
  linear_codes: true,
  matrix_codes: true,
});

const errorFetch = ref(false);
const alert = ref(false);

/*** track functons ***/
const selectedBarcodeFormats = computed(() => {
  return Object.keys(barcodeFormats.value).filter(
    (format) => barcodeFormats.value[format]
  );
});

const openCamera = () => {
  showCamera.value = !showCamera.value;
};

const onDecode = (detectedCodes) => {
  let code = JSON.stringify(detectedCodes.map((code) => code.rawValue));
  ondecode.value = JSON.parse(code);
  getStatus().then(() => (showCamera.value = false));
  console.log("ondecode", detectedCodes);
};

function paintOutline(detectedCodes, ctx) {
  for (const detectedCode of detectedCodes) {
    const [firstPoint, ...otherPoints] = detectedCode.cornerPoints;

    ctx.strokeStyle = "red";

    ctx.beginPath();
    ctx.moveTo(firstPoint.x, firstPoint.y);
    for (const { x, y } of otherPoints) {
      ctx.lineTo(x, y);
    }
    ctx.lineTo(firstPoint.x, firstPoint.y);
    ctx.closePath();
    ctx.stroke();
  }
}

const formatDate = (date) => {
  return new Date(date).toLocaleDateString("es-ES", {
    weekday: "long",
    year: "numeric",
    month: "long",
    day: "numeric",
  });
};

const formatTime = (date) => {
  return new Date(date).toLocaleTimeString("en-US");
};
/****API functions ***/
const verifyCode = async () => {
  try {
    const res = await api.get(`qr/validate`, {
      params: {
        hash: ondecode.value,
      },
      headers: {
        "Content-Type": "application/json",
      },
    });
    alert.value = true;
    console.log(res);
  } catch (error) {
    console.log(error);
  } finally {
    ondecode.value = "";
    showStatus.value = false;
  }
};

const getStatus = async () => {
  showStatus.value = true;
  try {
    const res = await api.get(`qr/validate`, {
      params: {
        hash: ondecode.value,
        detail: true,
      },
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
    });
    if (res.status === 200) {
      detail.value = res.data;
      errorFetch.value = false;
    }
    console.log(res);
  } catch (error) {
    errorFetch.value = true;
    console.log(error.response.data.message);
  }
};

const logout = ()=>{
  localStorage.removeItem("auth")
  router.replace({name:'login'})
}
/**hooks */
watchEffect(() => {
  if (alert.value) {
    setTimeout(() => {
      alert.value = false;
    }, 3000);
  }
});

onMounted(()=>{
  if(!localStorage.getItem("auth")){
    router.replace({name:'login'})
  }
})
</script>

<style scoped>
.main-container {
  padding: 0.1px;
}
</style>
