<template>
  <div class="app-shell">
    <header class="topbar">
      <div class="brand">
        <div class="logo-dot">
          <img :src="calculadora" alt="Icono calculadora" class="logo-img" />
        </div>
        <div>
          <div class="brand-title">PonderaU</div>
          <div class="brand-subtitle">Calculadora ponderado</div>
        </div>
      </div>

      <span class="pill-soft">Beta</span>
    </header>

    <main class="app-container">
      <section class="hero">
        <h1>Calculadora de Ponderado por Créditos</h1>
        <p>Registra tus cursos con nota (0 a 10) y créditos para obtener tu promedio ponderado.</p>
      </section>

      <section class="grid">
        <!-- ===================== PANEL DE CURSOS ===================== -->
        <div class="card card-main">
          <div class="card-header">
            <div>
              <h2>Cursos</h2>
              <p>Completa las columnas para incluir los cursos en el cálculo.</p>
            </div>
            <span class="pill">{{ cursos.length }} {{ cursos.length === 1 ? "curso" : "cursos" }}</span>
          </div>

          <div class="tabla-wrapper">
            <table class="tabla-cursos">
              <thead>
                <tr>
                  <th>Curso</th>
                  <th>Nota (0 - 10)</th>
                  <th>Créditos</th>
                  <th></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="curso in cursos" :key="curso.id">
                  <td>
                    <input type="text" v-model="curso.nombre" placeholder="Curso" />
                  </td>
                  <td>
                    <input type="number" min="0" max="10" step="0.1" v-model.number="curso.nota" />
                  </td>
                  <td>
                    <input type="number" min="0" step="0.5" v-model.number="curso.creditos" />
                  </td>
                  <td class="col-acciones">
                    <button class="btn-icon" @click="eliminarCurso(curso.id)" v-if="cursos.length > 1">×</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- BOTÓN MÁS ABAJO -->
          <div class="add-wrapper">
            <button class="btn-primary" @click="agregarCurso">+ Agregar curso</button>
          </div>
        </div>

        <!-- ===================== PANEL RESULTADOS ===================== -->
        <div class="card card-resumen">
          <div class="resumen-header">
            <h2>Resultado</h2>

            <!-- BOTÓN DE VER CÁLCULO -->
            <button class="btn-link" @click="toggleDetalle">
              {{ mostrarDetalle ? "ocultar cálculo" : "ver cálculo" }}
            </button>
          </div>

          <div class="resumen-item">
            <span>Total de créditos válidos</span>
            <strong>{{ totalCreditos }}</strong>
          </div>

          <div class="resumen-item principal">
            <span>Ponderado</span>
            <div class="ponderado-valor">
              <strong>{{ totalCreditos > 0 ? ponderado.toFixed(3) : "—" }}</strong>
              <span class="ponderado-etiqueta" v-if="totalCreditos > 0"></span>
            </div>
          </div>

          <p class="nota-ayuda" v-if="totalCreditos === 0">
            Agrega al menos un curso con nota válida y créditos para ver tu ponderado.
          </p>

          <div class="hint">
            <p>
              Fórmula utilizada:
              <code>Σ(nota × créditos) / Σ(créditos)</code>
            </p>
          </div>

          <!-- ===================== DETALLE DEL CÁLCULO ===================== -->
          <div v-if="mostrarDetalle" class="detalle-box">
            <h3>Cálculo detallado</h3>

            <div v-for="item in detalleCalculo" :key="item.text" class="detalle-linea">
              {{ item.text }}
            </div>

            <hr />

            <div class="detalle-final">
              total = {{ sumaPonderada }}
              <br />
              total créditos = {{ totalCreditos }}
              <br /><br />
              <strong>{{ sumaPonderada }} / {{ totalCreditos }} = {{ ponderado.toFixed(6) }}</strong>
            </div>
          </div>
        </div>
      </section>
    </main>

<footer class="footer">
   Gracias por usar PonderaU - ¡Muchos éxitos con tu semestre!<br />
  © {{ new Date().getFullYear() }} PonderaU · Hecho por Óscar Herra · 
  <a href="https://www.instagram.com/oscar_herraa13/" class="footer-link">Instagram</a>
</footer>

  </div>
  <AdBanner />
</template>

<script setup>
import { ref, computed } from "vue";
import calculadora from "./assets/calculadora.webp";

// ===================== DATOS =====================

const cursos = ref([{ id: 1, nombre: "Curso 1", nota: null, creditos: null }]);

let nextId = 2;
const agregarCurso = () => {
  cursos.value.push({
    id: nextId++,
    nombre: `Curso ${nextId - 1}`,
    nota: null,
    creditos: null
  });
};

const eliminarCurso = (id) => {
  cursos.value = cursos.value.filter((c) => c.id !== id);
};

// ===================== CÁLCULOS =====================

const totalCreditos = computed(() =>
  cursos.value.reduce((s, c) => {
    if (c.nota >= 0 && c.nota <= 10 && c.creditos > 0) {
      return s + Number(c.creditos);
    }
    return s;
  }, 0)
);

const sumaPonderada = computed(() =>
  cursos.value.reduce((s, c) => {
    if (c.nota >= 0 && c.nota <= 10 && c.creditos > 0) {
      return s + Number(c.nota) * Number(c.creditos);
    }
    return s;
  }, 0)
);

const ponderado = computed(() => {
  if (totalCreditos.value === 0) return 0;
  return sumaPonderada.value / totalCreditos.value;
});

// ===================== DETALLE DE CÁLCULO =====================

const mostrarDetalle = ref(false);
const toggleDetalle = () => (mostrarDetalle.value = !mostrarDetalle.value);

const detalleCalculo = computed(() =>
  cursos.value
    .filter((c) => c.nota >= 0 && c.creditos > 0)
    .map((c) => ({
      text: `${c.nota} × ${c.creditos} = ${(c.nota * c.creditos).toFixed(2)}`
    }))
);
</script>

<style scoped>
/* ======== LAYOUT GENERAL ======== */

.app-shell {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* ======== TOPBAR ======== */

.topbar {
  position: sticky;
  top: 0;
  z-index: 10;
  backdrop-filter: blur(18px);
  background-color: rgba(249, 250, 251, 0.9);
  border-bottom: 1px solid rgba(226, 232, 240, 0.9);
  padding: 1.4rem 3.2rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.brand {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.logo-dot {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: #ffffff;
  border: 1px solid rgba(148, 163, 184, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8px 16px rgba(15, 23, 42, 0.12);
}

.logo-img {
  width: 70%;
  height: 70%;
  object-fit: contain;
  display: block;
}

.brand-title {
  font-size: 1.35rem;
  font-weight: 700;
}

.brand-subtitle {
  font-size: 0.95rem;
  color: #6b7280;
}

.pill-soft {
  padding: 0.35rem 0.9rem;
  border-radius: 999px;
  background: rgba(0, 0, 0, 0.05);
  color: #4b5563;
  font-size: 0.9rem;
}

/* ======== CONTENIDO PRINCIPAL ======== */

.app-container {
  max-width: 1280px;
  margin: 0 auto;
  padding: 3.5rem 2.2rem 4rem;
  flex: 1;
}

/* ======== HERO ======== */

.hero {
  text-align: center;
  margin-bottom: 3.4rem;
}

.hero h1 {
  margin: 0;
  font-size: 3rem;
  font-weight: 800;
  letter-spacing: -0.02em;
  color: #0f172a;
}

.hero p {
  margin-top: 0.5rem;
  color: #6b7280;
  font-size: 1.25rem;
}

/* ======== GRID ======== */

.grid {
  display: grid;
  grid-template-columns: minmax(0, 2.3fr) minmax(340px, 1fr);
  gap: 2.8rem;
}

/* ======== CARDS ======== */

.card {
  background: rgba(255, 255, 255, 0.97);
  border-radius: 22px;
  padding: 2rem;
  box-shadow: 0 25px 50px rgba(15, 23, 42, 0.12);
  border: 1px solid rgba(226, 232, 240, 0.9);
  backdrop-filter: blur(16px);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1.8rem;
}

.card-header h2 {
  margin: 0;
  font-size: 1.55rem;
  font-weight: 700;
}

.card-header p {
  margin: 0.4rem 0 0;
  font-size: 1rem;
  color: #6b7280;
}

.pill {
  padding: 0.4rem 0.9rem;
  border-radius: 999px;
  background: #e0edff;
  color: #1d4ed8;
  font-weight: 600;
  font-size: 1rem;
}

/* ======== TABLA ======== */

.tabla-wrapper {
  border-radius: 16px;
  border: 1px solid #e5e7eb;
  overflow: hidden;
  background: #f9fafb;
}

.tabla-cursos {
  width: 100%;
  border-collapse: collapse;
  font-size: 1.05rem;
}

.tabla-cursos th {
  background: #f3f4f6;
  padding: 1rem 1.2rem;
  font-weight: 700;
  color: #475569;
  font-size: 1rem;
}

.tabla-cursos td {
  padding: 1.1rem 1.2rem;
}

.tabla-cursos tbody tr:nth-child(even) {
  background-color: #fdfdfd;
}

.tabla-cursos tbody tr:hover {
  background-color: #eef2ff;
}

.tabla-cursos input {
  width: 100%;
  padding: 0.75rem 0.85rem;
  border-radius: 12px;
  border: 1px solid #d1d5db;
  font-size: 1.05rem;
  background-color: white;
}

.tabla-cursos input:focus {
  outline: none;
  border-color: #2563eb;
  box-shadow: 0 0 0 2px rgba(37, 99, 235, 0.25);
}

/* ======== BOTONES ======== */

.btn-primary {
  border: none;
  cursor: pointer;
  border-radius: 999px;
  padding: 0.85rem 1.8rem;
  font-size: 1.1rem;
  font-weight: 600;
  background: linear-gradient(135deg, #2563eb, #1d4ed8);
  color: #ffffff;
  box-shadow: 0 14px 30px rgba(37, 99, 235, 0.35);
  transition: 0.2s;
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 18px 40px rgba(37, 99, 235, 0.45);
}

.btn-icon {
  border: none;
  cursor: pointer;
  width: 34px;
  height: 34px;
  border-radius: 50%;
  background: #fee2e2;
  color: #b91c1c;
  font-size: 1.4rem;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

/* ======== RESULTADO ======== */

.card-resumen h2 {
  margin-top: 0;
  font-size: 1.5rem;
  font-weight: 700;
}

.resumen-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.resumen-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.1rem;
  font-size: 1.1rem;
}

.resumen-item span {
  color: #6b7280;
}

.resumen-item strong {
  font-size: 1.35rem;
}

.resumen-item.principal {
  margin-top: 1.2rem;
  padding-top: 1.2rem;
  border-top: 1px dashed #e5e7eb;
}

.ponderado-valor strong {
  font-size: 2.4rem;
  font-weight: 800;
}

.ponderado-etiqueta {
  font-size: 1.1rem;
  color: #6b7280;
}

.hint {
  margin-top: 1.6rem;
  padding: 1.2rem 1rem;
  border-radius: 14px;
  background: #eff6ff;
  border: 1px dashed #bfdbfe;
  font-size: 0.95rem;
}

.hint code {
  font-size: 1.05rem;
}

/* ======== FOOTER ======== */

.footer {
  padding: 1.6rem 3rem;
  text-align: center;
  font-size: 1rem;
  color: #64748b;
  background: linear-gradient(to right, #f8fafc, #eef2ff);
  border-top: 1px solid rgba(226, 232, 240, 0.8);
  margin-top: 3rem;
}
.footer a {
  color: #2563eb;
  text-decoration: none;
}
.footer a:hover {
  text-decoration: underline;
}

/* ===== BOTÓN "VER CÁLCULO" ===== */
.btn-link {
  background: none;
  border: none;
  color: #2563eb;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  text-decoration: underline;
}

/* ===== CAJA DETALLE ===== */

.detalle-box {
  margin-top: 1.4rem;
  padding: 1.2rem 1.1rem;
  border-radius: 14px;
  background: #f3f8ff;
  border: 1px solid #bfdbfe;
}

.detalle-box h3 {
  margin: 0 0 0.8rem;
  font-size: 1.1rem;
  font-weight: 700;
}

.detalle-linea {
  font-size: 1rem;
  margin-bottom: 0.4rem;
}

.detalle-final {
  font-size: 1.1rem;
  margin-top: 0.8rem;
  line-height: 1.5;
}

/* ===== BOTÓN AGREGAR CURSO ABAJO ===== */
.add-wrapper {
  margin-top: 1.8rem;
  display: flex;
  justify-content: flex-start;
}

/* ================== RESPONSIVE MOBILE ================== */

@media (max-width: 768px) {
  .topbar {
    padding: 0.8rem 1.1rem;
  }

  .brand {
    gap: 0.6rem;
  }

  .logo-dot {
    width: 34px;
    height: 34px;
    box-shadow: 0 4px 10px rgba(15, 23, 42, 0.16);
  }

  .brand-title {
    font-size: 1.05rem;
  }

  .brand-subtitle {
    font-size: 0.8rem;
  }

  .pill-soft {
    font-size: 0.75rem;
    padding: 0.25rem 0.6rem;
  }

  .app-container {
    padding: 1.8rem 1.1rem 2.4rem;
  }

  .hero {
    margin-bottom: 2rem;
  }

  .hero h1 {
    font-size: 2.1rem;
    line-height: 1.15;
  }

  .hero p {
    font-size: 0.95rem;
  }

  .grid {
    grid-template-columns: 1fr;
    gap: 1.6rem;
  }

  .card {
    padding: 1.4rem 1.2rem;
    border-radius: 18px;
    box-shadow: 0 12px 30px rgba(15, 23, 42, 0.15);
  }

  .card-header {
    flex-direction: column;
    gap: 0.4rem;
  }

  .card-header h2 {
    font-size: 1.25rem;
  }

  .card-header p {
    font-size: 0.9rem;
  }

  .pill {
    align-self: flex-start;
    font-size: 0.85rem;
    padding: 0.25rem 0.7rem;
  }

  .tabla-cursos th,
  .tabla-cursos td {
    padding: 0.7rem 0.6rem;
    font-size: 0.9rem;
  }

  .tabla-cursos input {
    font-size: 0.9rem;
    padding: 0.55rem 0.6rem;
  }

  .btn-primary {
    width: 100%;
    justify-content: center;
    font-size: 1rem;
  }

  .resumen-item {
    font-size: 0.95rem;
  }

  .resumen-item strong {
    font-size: 1.15rem;
  }

  .ponderado-valor strong {
    font-size: 2rem;
  }

  .hint {
    font-size: 0.85rem;
  }

  .footer {
    padding: 1rem 1.2rem 1.5rem;
    text-align: center;
    font-size: 0.85rem;
  }
}

</style>
