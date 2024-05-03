<template>
  <section class="bg-dark py-3 py-md-5 py-xl-8 vh-100">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-12 col-sm-10 col-md-8 col-lg-6 col-xl-5 col-xxl-4 mt-5">
          <div class="card border border-light-subtle rounded-4">
            <div class="card-body p-3 p-md-4 p-xl-5">
              <form>
                <div class="row gy-3 overflow-hidden">
                  <div class="col-12">
                    <div class="form-floating mb-3">
                      <input
                        v-model="data.username"
                        class="form-control"
                        placeholder="user123"
                        required
                      />
                      <label for="email" class="form-label">Usuario</label>
                    </div>
                  </div>
                  <div class="col-12">
                    <div class="form-floating mb-3">
                      <input
                        v-model="data.password"
                        type="password"
                        class="form-control"
                        placeholder="Password"
                        required
                      />
                      <label for="password" class="form-label">Password</label>
                    </div>
                  </div>

                  <div class="col-12">
                    <div class="d-grid">
                      <button
                        class="btn btn-primary btn-lg"
                        type="submit"
                        @click.prevent="login"
                      >
                        Iniciar Sesión
                      </button>
                    </div>
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="data.error" class="fixed-top">
      <div class="alert alert-danger d-flex align-items-center" role="alert">
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
        <div style="margin-left: 1rem">Credenciales Incorrectas</div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { onMounted, reactive, watchEffect } from "vue";
import { api } from "../api/client";
import router from "../router";

const data = reactive({
  username: "",
  password: "",
  error: false,
});

const login = async () => {
  let datos = {
    username: data.username,
    password: data.password,
  };
  try {
    const res = await api.post("auth/login", datos, {
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
    });

    localStorage.setItem("auth", true);
    console.log(res);
    router.replace({ name: "home" });
  } catch (error) {
    console.log(error);
    data.error = true;
  }
};

watchEffect(() => {
  if (data.error) {
    setTimeout(() => {
      data.error = false;
    }, 3000);
  }
});

onMounted(() => {
  if (localStorage.getItem("auth")) {
    router.replace({ name: "home" });
  }
});
</script>
