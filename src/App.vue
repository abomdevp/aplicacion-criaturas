<template>
  <div class="container-fluid py-5 px-4">

    <div class="row justify-content-center">
      <div class="col-12 col-xl-10">

        <!-- HEADER -->
        <div class="text-center mb-5">
          <h1 class="fw-bold">Intergalactics Pet Finder</h1>
          <p class="text-muted">Sistema de registro galáctico en tiempo real</p>
        </div>

        <!-- FORM CARD -->
        <div class="card shadow-lg mb-5">
          <div class="card-body p-4">
            <h4 class="mb-4">
              {{ editandoIndex !== null ? "Editar Criatura" : "Registrar una nueva Criatura" }}
            </h4>

            <form @submit.prevent="guardarMascota">
              <div class="row g-4">

                <div class="col-md-6">
                  <label class="form-label">Nombre</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model.trim="formulario.nombre"
                  />
                </div>

                <div class="col-md-6">
                  <label class="form-label">Edad</label>
                  <input
                    type="number"
                    class="form-control"
                    v-model.number="formulario.edad"
                  />
                </div>

                <div class="col-md-6">
                  <label class="form-label">Planeta</label>
                  <select class="form-select" v-model="formulario.planeta">
                    <option disabled value="">Selecciona un planeta</option>
                    <option>Marte</option>
                    <option>Saturno</option>
                    <option>Kepler-22b</option>
                  </select>
                </div>

                <div class="col-md-6">
                  <label class="form-label d-block">Dieta</label>

                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="Herbívoro" v-model="formulario.dieta">
                    <label class="form-check-label">Herbívoro</label>
                  </div>

                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="Carnívoro" v-model="formulario.dieta">
                    <label class="form-check-label">Carnívoro</label>
                  </div>

                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="Consume Energía" v-model="formulario.dieta">
                    <label class="form-check-label">Consume Energía</label>
                  </div>
                </div>

                <div class="col-12">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" v-model="formulario.amigable">
                    <label class="form-check-label">
                      ¿Es amigable con humanos?
                    </label>
                  </div>
                </div>

                <div class="col-12">
                  <label class="form-label">Habilidades</label>
                  <textarea class="form-control" rows="3" v-model="formulario.habilidades"></textarea>
                </div>

                <div class="col-12 d-flex gap-3">
                  <button
                    class="btn btn-primary"
                    type="submit"
                    :disabled="!formulario.nombre.trim()"
                  >
                    {{ editandoIndex !== null ? "Guardar Cambios" : "Guardar" }}
                  </button>

                  <button
                    type="button"
                    class="btn btn-outline-secondary"
                    @click="cancelarEdicion"
                  >
                    Cancelar
                  </button>
                </div>

              </div>
            </form>
          </div>
        </div>

        <!-- TABLA -->
        <div class="card shadow-lg">
          <div class="card-body p-4">
            <div class="d-flex justify-content-between mb-4">
              <h4>Base de Datos Galáctica</h4>
              <span class="badge bg-dark">Total: {{ mascotas.length }}</span>
            </div>

            <div v-if="mascotas.length === 0" class="alert alert-info">
              Todos los alienígenas han sido adoptados. ¡Agrega nuevas criaturas para llenar la galaxia!
            </div>

            <div v-else class="table-responsive">
              <table class="table table-hover align-middle">
                <thead class="table-light">
                  <tr>
                    <th>Nombre</th>
                    <th>Edad</th>
                    <th>Planeta</th>
                    <th>Dieta</th>
                    <th>Amigable</th>
                    <th>Habilidades</th>
                    <th>Acciones</th>
                  </tr>
                </thead>

                <tbody>
                  <tr
                    v-for="(m, index) in mascotas"
                    :key="m.id"
                    :class="{ 'table-warning': !m.amigable }"
                  >
                    <td class="fw-semibold">{{ m.nombre }}</td>
                    <td>{{ m.edad }}</td>

                    <td>
                      <span class="badge" :class="badgePlaneta(m.planeta)">
                        {{ m.planeta }}
                      </span>
                    </td>

                    <td>{{ m.dieta }}</td>

                    <td>
                      <span
                        class="badge"
                        :class="m.amigable ? 'bg-success' : 'bg-danger'"
                      >
                        {{ m.amigable ? "Sí" : "No" }}
                      </span>
                    </td>

                    <td>{{ m.habilidades }}</td>

                    <td>
                      <button class="btn btn-sm btn-outline-primary me-2" @click="editarMascota(index)">
                        Editar
                      </button>
                      <button class="btn btn-sm btn-outline-danger" @click="eliminarMascota(index)">
                        Eliminar
                      </button>
                    </td>
                  </tr>
                </tbody>

              </table>
            </div>
          </div>
        </div>

      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from "vue"

const formulario = ref({
  nombre: "",
  edad: 0,
  planeta: "",
  dieta: "Herbívoro",
  amigable: true,
  habilidades: ""
})

const mascotas = ref([])

const editandoIndex = ref(null)

function guardarMascota() {
  if (!formulario.value.nombre.trim()) return

  if (editandoIndex.value !== null) {
    mascotas.value[editandoIndex.value] = {
      ...mascotas.value[editandoIndex.value],
      ...formulario.value
    }
  } else {
    mascotas.value.push({
      id: crypto.randomUUID(),
      ...formulario.value
    })
  }

  cancelarEdicion()
}

function editarMascota(index) {
  editandoIndex.value = index
  formulario.value = { ...mascotas.value[index] }
}

function eliminarMascota(index) {
  if (confirm("¿Eliminar esta criatura?")) {
    mascotas.value.splice(index, 1)
  }
}

function cancelarEdicion() {
  editandoIndex.value = null
  formulario.value = {
    nombre: "",
    edad: 0,
    planeta: "",
    dieta: "Herbívoro",
    amigable: true,
    habilidades: ""
  }
}

function badgePlaneta(planeta) {
  if (planeta === "Marte") return "bg-danger"
  if (planeta === "Saturno") return "bg-warning text-dark"
  if (planeta === "Kepler-22b") return "bg-success"
  return "bg-secondary"
}
</script>
