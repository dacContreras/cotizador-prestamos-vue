<!-- usamos el setup para importaciones -->
<script setup>

  // importamos componenetes,funciones y cosas de vue
  import { ref, computed, watch } from 'vue';
  import Header from './components/header.vue';
  import Button from './components/button.vue';
  import { calcularTotalPagar } from './helpers'

  // constantes | variables
  // el ref seria como el state en react
  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0);
  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;

  // funcion de formatear el dinero ocn la moneda del pais
  const formatearDinero = (valor) => {
    const formatter = new Intl.NumberFormat('es-GT', {
      style: 'currency',
      currency: 'GTQ'
    });
    return formatter.format(valor)
  }

  // el watch basicamente seria como el useEfect, escucha eventos
  watch([cantidad, meses], () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value)
  })

  const pagoMensual = computed (()=> {
    return total.value / meses.value;
  })

  // funciones de subir y bajar la cantidad
  const handleChangeDecremento = () => {
    const valor = cantidad.value - STEP;
    if(valor < MIN){
      alert('cantidad no valida');
      return;
    }
    cantidad.value = valor;
  }
  const handleChangeIncremento = () => {
    const valor = cantidad.value + STEP;
    if(valor > MAX){
      alert('cantidad no valida');
      return;
    }
    cantidad.value = valor;
  }

</script>

<!-- parte html -->
<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header />

    <div class="flex justify-between mt-10">

      <!-- cuando tiene los dos puntos son props -->
      <!-- cuando tiene el @ son persolizaciones -->
      <Button
        :operador="'-'"
        @fn="handleChangeDecremento"
      />
      <Button
        :operador="'+'"
        @fn="handleChangeIncremento"
      />

    </div>

    <div class="my-5">
      <input
        type="range" 
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
        :min="MIN"
        :max="MAX"
        :step="STEP"
        v-model.number="cantidad"
      />

      <p class="text-center my-10 text-5xl font-extrabold text-indigo-600">{{ formatearDinero(cantidad) }}</p>
    
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Elige un <span class="text-indigo-600">Plazo </span>a pagar
      </h2>

      <select 
        class="w-full mt-5 p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500"
        :value="meses"
        v-model.number="meses"
      >
        <option value="6">6 Meses</option>
        <option value="12">12 Meses</option>
        <option value="24">24 Meses</option>
      </select>

    </div>

    <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5">
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Resumen <span class="text-indigo-600">de Pagos</span>
      </h2>

      <p class="text-xl text-gray-500 text-center font-bold">{{ meses }} Meses</p>
      <p class="text-xl text-gray-500 text-center font-bold">Total a pagar: {{ formatearDinero(total) }}</p>
      <p class="text-xl text-gray-500 text-center font-bold">Mensuales {{ formatearDinero(pagoMensual) }} </p>
    </div>

    <p v-else class="font-extrabold text-gray-500 text-center">Añade un plazo o una cantidad a pagar</p>

  </div>
</template>
