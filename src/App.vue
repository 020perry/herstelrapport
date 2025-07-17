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
          <label :for="`foto-${index}`" class="block text-sm font-medium text-gray-700 mb-2">Klik hier om foto of bestand toe te voegen van herstelactie {{ index + 1 }}</label>
          <input :id="`foto-${index}`" type="file" @change="onFileChange($event, index)" accept="image/*" class="sr-only" />
          <div v-if="actie.foto">
            <img :src="actie.foto" alt="Foto herstelactie" class="max-h-48 mx-auto rounded-md border mb-2" />
          </div>
          <textarea :id="`omschrijving-${index}`" v-model="actie.omschrijving" rows="3"
            placeholder="Beschrijf de actie"
            class="mt-2 block w-full rounded-md bg-brand-white px-3 py-2 text-gray-900 shadow-sm border border-gray-300 focus:outline-none focus:ring-2 focus:ring-brand-blue"></textarea>
          <button @click.prevent="removeAction(index)" type="button" class="text-sm text-red-600 hover:underline mt-2">Verwijder actie</button>
        </div>
        <button @click.prevent="addAction" type="button"
          class="rounded-md bg-brand-blue px-3 py-2 text-sm font-semibold text-black shadow-sm hover:bg-brand-navy">Voeg nog een actie toe</button>
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
import jsPDF from 'jspdf'

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
  // Formulier validatie
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

  // Maak de PDF aan
  const doc = new jsPDF()

  doc.setFontSize(22)
  doc.text('Herstelrapport', 14, 20)

  doc.setFontSize(12)
  doc.text(`Naam: ${form.naam}`, 14, 30)
  doc.text(`E-mail: ${form.email}`, 14, 40)
  doc.text(`Datum: ${form.datum}`, 14, 50)
  doc.text(`Adres: ${form.adres}`, 14, 60)

  // Verhoog de afstand tussen herstelacties en zorg ervoor dat ze niet overlappen
  let yPosition = 70;

  form.acties.forEach((actie, index) => {
    if (yPosition > 260) {
      doc.addPage();  // Voeg een nieuwe pagina toe als de tekst te veel ruimte in beslag neemt
      yPosition = 20; // Reset de y-positie na het toevoegen van een nieuwe pagina
    }

    // Zet de tekst voor de herstelactie
    doc.text(`Herstelactie ${index + 1}:`, 14, yPosition);
    yPosition += 10; // Voeg ruimte tussen de tekst en de afbeelding

    // Voeg de omschrijving toe
    doc.text(`Omschrijving: ${actie.omschrijving}`, 14, yPosition);
    yPosition += 10; // Voeg ruimte tussen omschrijving en afbeelding

    // Voeg de foto toe (zorg ervoor dat de foto niet te groot is)
    if (actie.foto) {
      doc.addImage(actie.foto, 'JPEG', 14, yPosition, 50, 50);
      yPosition += 60; // Voeg ruimte toe onder de afbeelding
    }
    
    // Voeg een extra regel toe voor de volgende actie
    yPosition += 10;
  });

  // Genereer de PDF en download deze
  doc.save('herstelrapport.pdf')
}

// function downloadPdf() {
//   // Validation
//   if (!form.naam || !form.email || !form.datum || !form.adres) {
//     alert('Vul alle verplichte velden in.')
//     return
//   }

//   for (const [index, actie] of form.acties.entries()) {
//     if (!actie.omschrijving || !actie.foto) {
//       alert(`Herstelactie ${index + 1} mist een omschrijving of foto.`)
//       return
//     }
//   }

//   // Generate PDF
//   const doc = new jsPDF()
  
//   doc.setFontSize(22)
//   doc.text('Herstelrapport', 14, 20)
  
//   doc.setFontSize(12)
//   doc.text(`Naam: ${form.naam}`, 14, 30)
//   doc.text(`E-mail: ${form.email}`, 14, 40)
//   doc.text(`Datum: ${form.datum}`, 14, 50)
//   doc.text(`Adres: ${form.adres}`, 14, 60)
  
//   form.acties.forEach((actie, index) => {
//     doc.text(`Herstelactie ${index + 1}: ${actie.omschrijving}`, 14, 70 + (index * 20))
//     doc.addImage(actie.foto, 'JPEG', 14, 80 + (index * 20), 50, 50)
//   })

//   doc.save('herstelrapport.pdf')
// }
</script>

<style scoped>
/* Customize any extra styling for your form */
.bg-brand-gray {
  background-color: #F2F6FA;
}

.bg-brand-white {
  background-color: #FFFFFF;
}

.bg-brand-blue {
  background-color: #0096FF;
}

.bg-brand-navy {
  background-color: #003F6D;
}

.text-gray-700 {
  color: #333333;
}
</style>
