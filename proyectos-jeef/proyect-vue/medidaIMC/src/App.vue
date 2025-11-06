<template>
  <div>
    <input v-model="peso" type="number" placeholder="Peso (kg)" />
    <input v-model="altura" type="number" placeholder="Altura (cm)" />

    <button @click="calcularIMC">Calcular IMC</button>
    <button @click="guardarRegistro">Guardar Registro</button>

    <!-- Mostrar IMC -->
    <p v-if="imc !== null">IMC: {{ imc.toFixed(2) }}</p>
    <p v-if="estadoIMC">{{ estadoIMC }}</p>

    <!-- Mostrar mensajes de error -->
    <p v-if="error" class="error">{{ error }}</p>
  </div>
</template>

<script setup>
import { ref } from "vue";

// Variables de entrada
let peso = ref("");
let altura = ref("");

// Variables de salida
let imc = ref(null);
let estadoIMC = ref("");
let error = ref("");

// Función para calcular IMC
const calcularIMC = () => {
  // Validaciones
  if (!peso.value || !altura.value) {
    error.value = "Por favor, ingrese ambos valores.";
    return;
  }
  if (peso.value <= 0 || altura.value <= 0) {
    error.value = "El peso y la altura deben ser mayores a cero.";
    return;
  }

  // Limpiar errores
  error.value = "";

  // Calcular IMC
  const alturaMetros = altura.value / 100; // Convertir a metros
  imc.value = peso.value / (alturaMetros * alturaMetros);

  // Determinar el estado del IMC
  if (imc.value < 18.5) {
    estadoIMC.value = "Bajo peso";
  } else if (imc.value >= 18.5 && imc.value < 24.9) {
    estadoIMC.value = "Peso normal";
  } else if (imc.value >= 25 && imc.value < 29.9) {
    estadoIMC.value = "Sobrepeso";
  } else {
    estadoIMC.value = "Obesidad";
  }
};

// Función para guardar el registro
const guardarRegistro = () => {
  if (!imc.value) {
    error.value = "Por favor, calcule el IMC antes de guardar.";
    return;
  }

  // Aquí puedes guardar los datos en una lista o en el almacenamiento local si es necesario
  alert("Registro guardado con éxito:\n" +
    `Peso: ${peso.value} kg\nAltura: ${altura.value} cm\nIMC: ${imc.value.toFixed(2)}\nEstado: ${estadoIMC.value}`);

  // Limpiar campos después de guardar
  peso.value = "";
  altura.value = "";
  imc.value = null;
  estadoIMC.value = "";
};
</script>

<style scoped>
.error {
  color: red;
}
</style>
