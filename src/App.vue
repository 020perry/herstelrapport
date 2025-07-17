<template>
  <form @submit.prevent="downloadPdf" class="bg-brand-gray min-h-screen py-12 px-6">
    <div class="space-y-12 max-w-3xl mx-auto">
      <!-- Titel -->
      <div class="border-b border-gray-900/10 pb-12">
        <h2 class="text-2xl font-semibold text-gray-900">Herstelrapport</h2>
        <p class="mt-1 text-sm text-gray-600">Vul dit formulier in om een herstelrapport vast te leggen.</p>
      </div>

      <!-- Naam & E-mail -->
      <div class="grid grid-cols-1 gap-6 sm:grid-cols-2">
        <div>
          <label for="naam" class="block text-sm font-medium text-gray-700">Naam *</label>
          <input id="naam" name="naam" v-model="form.naam" type="text" required
            class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue" />
        </div>
        <div>
          <label for="email" class="block text-sm font-medium text-gray-700">E-mail *</label>
          <input id="email" name="email" v-model="form.email" type="email" required
            class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue" />
        </div>
      </div>

      <!-- Datum & Adres -->
      <div class="grid grid-cols-1 gap-6 sm:grid-cols-2">
        <div>
          <label for="datum" class="block text-sm font-medium text-gray-700">Datum *</label>
          <input id="datum" name="datum" v-model="form.datum" type="date" required
            class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue" />
        </div>
        <div>
          <label for="adres" class="block text-sm font-medium text-gray-700">Adres *</label>
          <input id="adres" name="adres" v-model="form.adres" type="text" required
            class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue" />
        </div>
      </div>

      <!-- Herstelacties -->
      <div>
        <h3 class="text-lg font-medium text-gray-900 mb-4">Herstelacties</h3>
        <div v-for="(actie, index) in form.acties" :key="index"
          class="mb-6 border border-dashed rounded-lg p-6 bg-white shadow-sm">
          <div class="mb-4">
            <label :for="`foto-${index}`" class="block text-sm font-medium text-gray-700 mb-2">Foto van de herstelactie {{ index + 1 }}</label>
            <div class="flex flex-col sm:flex-row gap-4">
              <div
                class="flex-1 flex flex-col items-center justify-center border-2 border-dashed border-brand-light-blue rounded-lg p-4 bg-brand-light-blue/20 text-center cursor-pointer hover:bg-brand-light-blue/40 transition">
                <label :for="`foto-${index}`" class="w-full cursor-pointer">
                  <svg class="mx-auto h-10 w-10 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 48 48">
                    <path d="M14 32l10-10 10 10M24 22v14" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                  </svg>
                  <p class="mt-2 text-sm text-gray-600">Klik hier om een foto toe te voegen of te maken</p>
                  <input :id="`foto-${index}`" type="file" accept="image/*" capture="environment" @change="onFileChange($event, index)" class="sr-only" />
                </label>
              </div>
              <div v-if="actie.foto" class="mt-2">
                <img :src="actie.foto" alt="Foto herstelactie" class="max-h-48 mx-auto rounded-md border" />
              </div>
            </div>
          </div>
          <div>
            <label :for="`omschrijving-${index}`" class="block text-sm font-medium text-gray-700">Omschrijving {{ index + 1 }}</label>
            <textarea :id="`omschrijving-${index}`" :name="`omschrijving-${index}`" v-model="actie.omschrijving" rows="3"
              placeholder="Beschrijf de actie"
              class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue"></textarea>
          </div>
          <button @click.prevent="removeAction(index)" type="button"
            class="text-sm text-red-600 hover:underline mt-2">Verwijder actie</button>
        </div>
        <button @click.prevent="addAction" type="button"
          class="rounded-md bg-brand-blue px-3 py-2 text-sm font-semibold text-black shadow-sm hover:bg-brand-navy">Voeg
          nog een actie toe</button>
      </div>

      <!-- PDF Knop -->
      <div class="pt-8 border-t border-gray-900/10 text-right">
        <button @click.prevent="downloadPdf" type="submit"
          class="rounded-md bg-brand-blue px-6 py-2 text-sm font-semibold text-black shadow-sm hover:bg-brand-navy focus:outline-none focus:ring-2 focus:ring-brand-blue">
          Download PDF
        </button>
      </div>
    </div>
  </form>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  naam: '',
  email: '',
  datum: '',
  adres: '',
  acties: [{ omschrijving: '', foto: null }]
})

function addAction() {
  form.acties.push({ omschrijving: '', foto: null })
}

function removeAction(index) {
  form.acties.splice(index, 1)
}

function onFileChange(event, index) {
  const file = event.target.files[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (e) => {
      form.acties[index].foto = e.target.result
    }
    reader.readAsDataURL(file)
  }
}

function downloadPdf() {
  // Validatie
  if (!form.naam || !form.email || !form.datum || !form.adres) {
    alert('Vul alle verplichte velden in.')
    return
  }

  for (const [index, actie] of form.acties.entries()) {
    if (!actie.omschrijving || !actie.foto) {
      alert(`Herstelactie ${index + 1} mist een omschrijving of foto.`)
      return
    }
  }

  const element = document.querySelector('form')
  const options = {
    filename: 'herstelrapport.pdf',
    image: { type: 'jpeg', quality: 0.98 },
    html2canvas: { scale: 2 },
    jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
  }

  try {
    if (!window.html2pdf) {
      console.error('html2pdf.js is niet geladen.')
      alert('PDF-generatie mislukt: html2pdf is niet beschikbaar.')
      return
    }

    window.html2pdf().set(options).from(element).save()
  } catch (err) {
    console.error('Fout bij PDF-generatie:', err)
    alert('Er is iets misgegaan bij het genereren van de PDF.')
  }
}
</script>
