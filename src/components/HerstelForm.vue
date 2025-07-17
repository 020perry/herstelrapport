<template>
  <div>
    <h2>Herstelrapport</h2>
    <input
  v-model="monteur"
  placeholder="Naam monteur"
  class="w-full p-2 border border-gray-300 rounded mb-4"
/>

<button
  @click="submitRapport"
  class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 transition"
>
  ✅ Rapport indienen
</button>

    <input type="date" v-model="datum" />
    <div v-for="(actie, index) in herstelacties" :key="index">
      <input type="file" @change="uploadFoto($event, index)" />
      <input v-model="actie.omschrijving" placeholder="Omschrijving" />
      <button @click="verwijderActie(index)">🗑️</button>
    </div>
    <button @click="voegActieToe">➕ Herstelactie</button>
    <button @click="submitRapport">✅ Rapport indienen</button>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'

const monteur = ref('')
const datum = ref('')
const herstelacties = reactive([])

function voegActieToe() {
  herstelacties.push({ foto: null, omschrijving: '' })
}

function uploadFoto(e, index) {
  const file = e.target.files[0]
  herstelacties[index].foto = file
}

function verwijderActie(index) {
  herstelacties.splice(index, 1)
}

function submitRapport() {
  const rapport = {
    monteur: monteur.value,
    datum: datum.value,
    herstelacties: herstelacties
  }
  console.log('Rapport:', rapport)
}
</script>
