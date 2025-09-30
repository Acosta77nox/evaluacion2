<template>
  <section class="wrap">
    <form class="card" @submit.prevent="handleSubmit" novalidate>
      <h2 class="title">Registro rápido</h2>
      <p class="subtitle">Rellena los campos para continuar</p>

      <div class="grid">
        <div class="field" :class="{ invalid: errors.name }">
          <input id="name" v-model="form.name" @blur="touch('name')" type="text" required />
          <label for="name">Nombre completo</label>
          <small v-if="errors.name" class="error">{{ errors.name }}</small>
        </div>

        <div class="field" :class="{ invalid: errors.email }">
          <input id="email" v-model="form.email" @blur="touch('email')" type="email" required />
          <label for="email">Correo electrónico</label>
          <small v-if="errors.email" class="error">{{ errors.email }}</small>
        </div>

        <div class="field" :class="{ invalid: errors.phone }">
          <input id="phone" v-model="form.phone" @blur="touch('phone')" type="tel" inputmode="tel" />
          <label for="phone">Teléfono (opcional)</label>
          <small v-if="errors.phone" class="error">{{ errors.phone }}</small>
        </div>

        <div class="field">
          <input id="date" v-model="form.date" type="date" />
          <label for="date" class="label-top">Fecha de nacimiento (opcional)</label>
        </div>

        <div class="field">
          <select id="role" v-model="form.role" aria-label="Rol">
            <option value="" disabled>Selecciona un rol</option>
            <option>Estudiante</option>
            <option>Docente</option>
            <option>Administrador</option>
          </select>
          <label for="role" class="label-top"></label>
        </div>

        <div class="field textarea" :class="{ invalid: errors.notes }">
          <textarea id="notes" v-model="form.notes" rows="3" @blur="touch('notes')"></textarea>
          <label for="notes">Notas (opcional)</label>
          <small v-if="errors.notes" class="error">{{ errors.notes }}</small>
        </div>
      </div>

      <div class="controls">
        <label class="checkbox">
          <input type="checkbox" v-model="form.news" />
          <span>Quiero recibir novedades</span>
        </label>

        <div class="actions">
          <button type="button" class="btn ghost" @click="resetForm" :disabled="submitting">Limpiar</button>
          <button type="submit" class="btn" :disabled="submitting || !isDirty">
            <span v-if="submitting" class="dot-pulse" aria-hidden></span>
            {{ submitting ? 'Enviando...' : 'Enviar' }}
          </button>
        </div>
      </div>

      <div v-if="submitted" class="toast success" role="status" aria-live="polite">
        ✅ Formulario enviado correctamente. (Aquí manejarías el envío real)
      </div>
      <div v-if="submitError" class="toast error" role="alert">
        ❌ Ocurrió un error. Revisa los datos e inténtalo de nuevo.
      </div>
    </form>
  </section>
</template>

<script setup>
import { reactive, computed, ref } from 'vue'

const initial = {
  name: '',
  email: '',
  phone: '',
  date: '',
  role: '',
  notes: '',
  news: false
}

const form = reactive({ ...initial })
const touched = reactive({})
const submitting = ref(false)
const submitted = ref(false)
const submitError = ref(false)

const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
const phoneRegex = /^[0-9+\-\s()]{7,20}$/

function touch(field) {
  touched[field] = true
}

const errors = computed(() => {
  const e = {}
  if (touched.name && !form.name.trim()) e.name = 'El nombre es obligatorio.'
  if (touched.email) {
    if (!form.email.trim()) e.email = 'El correo es obligatorio.'
    else if (!emailRegex.test(form.email)) e.email = 'Introduce un correo válido.'
  }
  if (touched.phone && form.phone && !phoneRegex.test(form.phone)) e.phone = 'Número inválido.'
  if (touched.notes && form.notes.length > 500) e.notes = 'Máximo 500 caracteres.'
  return e
})

const isDirty = computed(() => {
  return Object.keys(initial).some(k => {
    return String(form[k]) !== String(initial[k])
  })
})

function validateAll() {
  // mark all fields as touched then compute errors
  Object.keys(form).forEach(k => (touched[k] = true))
  return Object.keys(errors.value).length === 0
}

function resetForm() {
  Object.keys(initial).forEach(k => (form[k] = initial[k]))
  Object.keys(touched).forEach(k => (touched[k] = false))
  submitted.value = false
  submitError.value = false
}

async function handleSubmit() {
  submitError.value = false
  submitted.value = false

  if (!validateAll()) {
    // scroll to first error for UX
    const firstErr = document.querySelector('.field.invalid input, .field.invalid textarea, .field.invalid select')
    if (firstErr) firstErr.focus()
    return
  }

  submitting.value = true
  try {
    // Simula envío: aquí reemplaza con tu fetch/axios
    await new Promise(res => setTimeout(res, 700))
    submitted.value = true
    resetForm()
  } catch (err) {
    submitError.value = true
  } finally {
    submitting.value = false
  }
}
</script>

<style scoped>
:root {
  --bg: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(0,0,0,0.02));
  --card-bg: rgba(255,255,255,0.06);
  --glass: rgba(255,255,255,0.06);
  --accent: #6c5ce7;
  --accent-2: #00b894;
  --muted: rgba(255,255,255,0.7);
  --error: #ff7675;
}

* { box-sizing: border-box; }
.wrap {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 28px;
  background: radial-gradient(1200px 400px at 10% 10%, rgba(108,92,231,0.12), transparent),
              radial-gradient(800px 300px at 90% 90%, rgba(0,184,148,0.08), transparent),
              #0f1724;
  font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  color: #e6eef8;
}

/* Card */
.card{
  width: 100%;
  max-width: 920px;
  background: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));
  border-radius: 16px;
  padding: 28px;
  box-shadow: 0 10px 30px rgba(2,6,23,0.6), inset 0 1px 0 rgba(255,255,255,0.02);
  backdrop-filter: blur(8px) saturate(120%);
  border: 1px solid rgba(255,255,255,0.04);
  transition: transform .18s ease;
}
.card:focus-within { transform: translateY(-4px); }

/* Titles */
.title {
  margin: 0 0 6px 0;
  font-size: 20px;
  letter-spacing: -0.2px;
  color: white;
}
.subtitle {
  margin: 0 0 18px 0;
  font-size: 13px;
  color: var(--muted);
}

/* Grid */
.grid{
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 12px 16px;
  margin-bottom: 18px;
}

/* Field */
.field{
  position: relative;
  background: rgba(255,255,255,0.02);
  border-radius: 10px;
  padding: 12px 12px 8px 12px;
  border: 1px solid rgba(255,255,255,0.03);
  transition: box-shadow .18s ease, border-color .18s ease, transform .18s ease;
  min-height: 56px;
  display: flex;
  align-items: center;
}
.field:hover { transform: translateY(-2px); box-shadow: 0 6px 18px rgba(2,6,23,0.45); }

.field.invalid { border-color: rgba(255,118,117,0.28); box-shadow: 0 4px 18px rgba(255,118,117,0.06); }

/* Inputs */
.field input[type="text"],
.field input[type="email"],
.field input[type="tel"],
.field input[type="date"],
.field textarea,
.field select {
  width: 100%;
  background: transparent;
  border: 0;
  outline: none;
  color: #eaf2ff;
  font-size: 14px;
  padding: 6px 0 8px 0;
}

/* Floating label */
.field label {
  position: absolute;
  left: 14px;
  top: 12px;
  transform-origin: left top;
  transition: transform .14s ease, top .14s ease, font-size .14s ease, color .14s ease;
  color: rgba(255,255,255,0.75);
  font-size: 13px;
  pointer-events: none;
}
.field input:focus + label,
.field textarea:focus + label,
.field select:focus + label,
.field input:not(:placeholder-shown) + label,
.field textarea:not(:placeholder-shown) + label {
  top: 6px;
  transform: scale(.86);
  color: var(--accent);
  font-weight: 600;
}

/* special for date/select because browsers show placeholder differently */
.label-top { top: 6px; transform: scale(.86); font-weight:600; }

/* Textarea */
.textarea { align-items: start; min-height: auto; padding-top: 16px; }
.field.textarea label { left: 14px; top: 10px; transform: translateY(-6px) scale(.92); }

/* Errors */
.error {
  display: block;
  margin-top: 6px;
  color: var(--error);
  font-size: 12px;
}

/* Controls */
.controls{
  display:flex;
  gap:12px;
  align-items:center;
  justify-content:space-between;
  flex-wrap:wrap;
}
.checkbox{
  display:flex;
  gap:10px;
  align-items:center;
  font-size:13px;
  color:var(--muted);
}
.checkbox input { width:16px; height:16px; }

/* Actions */
.actions{ display:flex; gap:10px; align-items:center; }
.btn{
  background: linear-gradient(90deg,var(--accent), #8e7bff);
  border: 0;
  color: white;
  padding: 10px 16px;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  box-shadow: 0 6px 18px rgba(108,92,231,0.15);
  transition: transform .12s ease, box-shadow .12s ease, opacity .12s ease;
}
.btn:active{ transform: translateY(1px); }
.btn:disabled { opacity: .6; cursor:not-allowed; transform:none; box-shadow:none; }

.btn.ghost{
  background: transparent;
  border: 1px solid rgba(255,255,255,0.06);
  color: var(--muted);
  box-shadow: none;
}

/* Toasts */
.toast{
  margin-top:14px;
  padding: 10px 12px;
  border-radius: 10px;
  font-size: 14px;
  display:inline-block;
}
.toast.success{ background: rgba(76,216,147,0.08); color: #bff0d6; border: 1px solid rgba(76,216,147,0.12); }
.toast.error{ background: rgba(255,118,117,0.06); color: #ffd6d6; border: 1px solid rgba(255,118,117,0.12); }

/* Subtle loading pulse */
.dot-pulse{
  display:inline-block;
  width:12px; height:12px;
  border-radius:50%;
  margin-right:8px;
  background: white;
  opacity: .9;
  box-shadow: 0 0 8px rgba(255,255,255,0.12);
  animation: pulse 1s infinite ease-in-out;
}
@keyframes pulse {
  0% { transform: scale(.9); opacity: .9; }
  50% { transform: scale(1.15); opacity: .45; }
  100% { transform: scale(.9); opacity: .9; }
}

/* Responsive */
@media (max-width:640px){
  .card { padding: 18px; border-radius: 12px; }
  .title { font-size:18px; }
}
</style>
