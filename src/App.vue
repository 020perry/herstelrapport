<template>
  <div class="min-h-screen bg-gray-50 py-8 px-4">
    <div class="max-w-2xl mx-auto">
      <div class="text-center mb-12">
        <h1 class="text-3xl font-light text-gray-900 mb-3">Herstelverklaring</h1>
        <p class="text-gray-600 text-sm">Vul onderstaande gegevens zorgvuldig in</p>
      </div>
      <form @submit.prevent class="space-y-8">
        <!-- Algemene gegevens -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">
          <h2 class="text-lg font-medium text-gray-900 mb-6 pb-3 border-b border-gray-100">Algemene Gegevens</h2>
          <div class="space-y-6">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Naam rapport *</label>
              <input v-model="form.rapportNaam" ref="ref_rapportNaam" :class="getInputClass('rapportNaam')" placeholder="Voer de naam van het rapport in" />
              <p v-if="errors.rapportNaam" class="mt-1 text-sm text-red-600">{{ errors.rapportNaam }}</p>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Datum uitvoering *</label>
                <input v-model="form.rapportDatum" ref="ref_rapportDatum" type="date" :class="getInputClass('rapportDatum')" />
                <p v-if="errors.rapportDatum" class="mt-1 text-sm text-red-600">{{ errors.rapportDatum }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Bedrijfsnaam *</label>
                <input v-model="form.bedrijf" ref="ref_bedrijf" :class="getInputClass('bedrijf')" placeholder="Naam van het bedrijf" />
                <p v-if="errors.bedrijf" class="mt-1 text-sm text-red-600">{{ errors.bedrijf }}</p>
              </div>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Adres *</label>
              <input v-model="form.adres" ref="ref_adres" :class="getInputClass('adres')" placeholder="Straatnaam en huisnummer" />
              <p v-if="errors.adres" class="mt-1 text-sm text-red-600">{{ errors.adres }}</p>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Postcode *</label>
                <input v-model="form.postcode" ref="ref_postcode" :class="getInputClass('postcode')" placeholder="1234 AB" />
                <p v-if="errors.postcode" class="mt-1 text-sm text-red-600">{{ errors.postcode }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Plaats *</label>
                <input v-model="form.plaats" ref="ref_plaats" :class="getInputClass('plaats')" placeholder="Plaatsnaam" />
                <p v-if="errors.plaats" class="mt-1 text-sm text-red-600">{{ errors.plaats }}</p>
              </div>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Telefoonnummer *</label>
              <input v-model="form.telefoon" ref="ref_telefoon" type="tel" :class="getInputClass('telefoon')" placeholder="06-12345678 of 010-1234567" />
              <p v-if="errors.telefoon" class="mt-1 text-sm text-red-600">{{ errors.telefoon }}</p>
            </div>
          </div>
        </section>

        <!-- Herstelacties -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">
          <div class="flex justify-between items-center mb-6 pb-3 border-b border-gray-100">
            <h2 class="text-lg font-medium text-gray-900">Herstelacties</h2>
            <span class="text-xs text-gray-500 bg-gray-100 px-2 py-1 rounded">{{ form.acties.length }}</span>
          </div>
          <div class="space-y-6">
            <div v-for="(actie, index) in form.acties" :key="index" class="border border-gray-100 rounded-lg p-6 bg-gray-50">
              <div class="flex justify-between items-start mb-4">
                <h3 class="text-sm font-medium text-gray-900">Actie {{ index + 1 }}</h3>
                <button v-if="form.acties.length > 1" type="button" @click="removeAction(index)" class="text-xs text-gray-500 hover:text-red-600 underline">Verwijderen</button>
              </div>
              <div class="space-y-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Omschrijving *</label>
                  <textarea v-model="actie.omschrijving" :ref="'ref_actieOmschrijving_' + index" :class="getTextareaClass(index)" rows="3" placeholder="Beschrijf de uitgevoerde herstelactie..."></textarea>
                  <p v-if="errors[`actie_${index}`] && errors[`actie_${index}`].includes('Omschrijving')" class="mt-1 text-sm text-red-600">{{ errors[`actie_${index}`] }}</p>
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Foto *</label>
                  <div class="border border-gray-200 rounded-lg p-4 text-center bg-white">
                    <input type="file" accept="image/*" @change="e => onFileChange(e, index)" :ref="'ref_actieFoto_' + index" class="hidden" :id="`file-${index}`" />
                    <label :for="`file-${index}`" class="cursor-pointer">
                      <div v-if="!actie.foto" class="text-gray-400">
                        <svg class="mx-auto h-8 w-8 mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 4v16m8-8H4"></path>
                        </svg>
                        <p class="text-xs">Foto toevoegen</p>
                      </div>
                    </label>
                    <div v-if="actie.foto" class="relative">
                      <img :src="actie.foto"  :id="`preview_foto_${index}`" alt="Preview" class="max-h-32 mx-auto rounded" />
                      <button type="button" @click="removePhoto(index)" class="absolute top-1 right-1 bg-white rounded-full w-5 h-5 flex items-center justify-center text-xs text-gray-600 hover:text-red-600 shadow">×</button>
                    </div>
                  </div>
                  <p v-if="errors[`actie_${index}`] && errors[`actie_${index}`].includes('Foto')" class="mt-1 text-sm text-red-600">
                    {{ errors[`actie_${index}`] }}
                  </p>
                </div>
              </div>
            </div>
          </div>
          <button
  type="button"
  @click="addAction"
  class="mt-6 w-full bg-black text-white py-3 px-4 rounded-lg hover:bg-gray-900 text-sm font-semibold"
>
  + Herstelactie toevoegen
</button>

        </section>

        <!-- Ondertekening -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">
          <h2 class="text-lg font-medium text-gray-900 mb-6 pb-3 border-b border-gray-100">Ondertekening</h2>
          <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Plaats *</label>
              <input v-model="form.ondertekenPlaats" ref="ref_ondertekenPlaats" :class="getInputClass('ondertekenPlaats')" placeholder="Plaats van ondertekening" />
              <p v-if="errors.ondertekenPlaats" class="mt-1 text-sm text-red-600">{{ errors.ondertekenPlaats }}</p>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Naam *</label>
              <input v-model="form.ondertekenNaam" ref="ref_ondertekenNaam" :class="getInputClass('ondertekenNaam')" placeholder="Naam ondertekenaar" />
              <p v-if="errors.ondertekenNaam" class="mt-1 text-sm text-red-600">{{ errors.ondertekenNaam }}</p>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Functie *</label>
              <input v-model="form.ondertekenFunctie" ref="ref_ondertekenFunctie" :class="getInputClass('ondertekenFunctie')" placeholder="Functie ondertekenaar" />
              <p v-if="errors.ondertekenFunctie" class="mt-1 text-sm text-red-600">{{ errors.ondertekenFunctie }}</p>
            </div>
          </div>
          <div class="mb-6">
            <label class="block text-sm font-medium text-gray-700 mb-2">Datum *</label>
            <input v-model="form.ondertekenDatum" ref="ref_ondertekenDatum" type="date" :class="getInputClass('ondertekenDatum')" readonly />
            <p v-if="errors.ondertekenDatum" class="mt-1 text-sm text-red-600">{{ errors.ondertekenDatum }}</p>
          </div>
          <!-- Handtekening -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-3">Handtekening *</label>
            <div class="border border-gray-200 rounded-lg p-4 bg-gray-50">
              <canvas ref="signatureCanvas" class="border border-gray-300 w-full h-32 rounded bg-white cursor-crosshair"></canvas>
              <div class="flex justify-between items-center mt-3">
                <p class="text-xs text-gray-500">Zet uw handtekening hierboven</p>
                <button type="button" @click="clearSignature" class="text-xs text-gray-600 hover:text-red-600 underline">Wissen</button>
              </div>
              <p v-if="errors.handtekening" class="mt-1 text-sm text-red-600">{{ errors.handtekening }}</p>
            </div>
          </div>
        </section>

        <div class="flex flex-col sm:flex-row gap-3 mt-2">
          <button
  type="button"
  @click="downloadPdf"
  class="flex-1 py-3 px-6 rounded-lg bg-black text-white hover:bg-gray-900 text-sm font-semibold"
>
  Download PDF
</button>

        </div>
        <p v-if="Object.keys(errors).length" class="text-xs text-red-600 text-center mt-2">
          Vul alle verplichte velden correct in en corrigeer de fouten hierboven.
        </p>
      </form>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, onMounted, computed, nextTick, getCurrentInstance } from 'vue'

function today() {
  const d = new Date()
  return d.toISOString().slice(0, 10)
}

const form = reactive({
  rapportNaam: '',
  rapportDatum: '',
  bedrijf: '',
  adres: '',
  postcode: '',
  plaats: '',
  telefoon: '',
  acties: [{ omschrijving: '', foto: null }],
  ondertekenPlaats: '',
  ondertekenNaam: '',
  ondertekenFunctie: '',
  ondertekenDatum: today(),
})

const errors = reactive({})
const signatureCanvas = ref(null)
let signaturePad = null
const signatureState = ref(0)

function getInputClass(fieldName) {
  const base = 'w-full border rounded-lg px-3 py-2 text-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500'
  return errors[fieldName] ? `${base} border-red-300 bg-red-50` : `${base} border-gray-300 hover:border-blue-500`
}
function getTextareaClass(index) {
  const baseClass = "w-full border rounded-lg px-3 py-2 text-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 resize-vertical"
  return errors[`actie_${index}`] 
    ? `${baseClass} border-red-300 bg-red-50` 
    : `${baseClass} border-gray-300 hover:border-blue-500`;
}
function validateField(fieldName) {
  if (!form[fieldName] || form[fieldName].toString().trim() === '') {
    errors[fieldName] = 'Dit veld is verplicht'
    return false
  }
  if (fieldName === 'postcode') {
    const re = /^[1-9][0-9]{3}\s?[A-Za-z]{2}$/
    if (!re.test(form.postcode)) {
      errors.postcode = 'Voer een geldige postcode in (bijv. 1234 AB)'
      return false
    }
  }
  if (fieldName === 'telefoon') {
    const re = /^(?:\+31|0)[1-9][0-9]{8}$/
    if (!re.test(form.telefoon.replace(/[\s-]/g, ''))) {
      errors.telefoon = 'Voer een geldig Nederlands telefoonnummer in'
      return false
    }
  }
  delete errors[fieldName]
  return true
}
function validateAction(index) {
  const actie = form.acties[index]
  if (!actie.omschrijving || actie.omschrijving.trim() === '') {
    errors[`actie_${index}`] = 'Omschrijving is verplicht'
    return false
  }
  if (actie.omschrijving && actie.omschrijving.length < 10) {
    errors[`actie_${index}`] = 'Omschrijving moet minimaal 10 karakters bevatten'
    return false
  }
  if (!actie.foto) {
    errors[`actie_${index}`] = 'Foto is verplicht'
    return false
  }
  delete errors[`actie_${index}`]
  return true
}
function addAction() {
  form.acties.push({ omschrijving: '', foto: null })
}
function removeAction(index) {
  form.acties.splice(index, 1)
  delete errors[`actie_${index}`]
}
function removePhoto(index) {
  form.acties[index].foto = null
}

// -------- FOTO UPLOAD ZONDER EXIF --------
function onFileChange(event, index) {
  const file = event.target.files[0];
  if (file) {
    if (file.size > 10 * 1024 * 1024) {
      alert('Bestand is te groot. Maximaal 10MB toegestaan.');
      return;
    }
    const reader = new FileReader();
    reader.onload = function(e) {
      form.acties[index].foto = e.target.result;
    };
    reader.readAsDataURL(file);
  }
}

function validateForm() {
  let valid = true
  Object.keys(form).forEach((key) => {
    if (key !== 'acties' && !validateField(key)) valid = false
  })
  form.acties.forEach((actie, idx) => {
    if (!validateAction(idx)) valid = false
  })
  if (!form.ondertekenPlaats || form.ondertekenPlaats.trim() === '') {
    errors.ondertekenPlaats = 'Dit veld is verplicht'
    valid = false
  } else {
    delete errors.ondertekenPlaats
  }
  if (!form.ondertekenNaam || form.ondertekenNaam.trim() === '') {
    errors.ondertekenNaam = 'Dit veld is verplicht'
    valid = false
  } else {
    delete errors.ondertekenNaam
  }
  if (!form.ondertekenFunctie || form.ondertekenFunctie.trim() === '') {
    errors.ondertekenFunctie = 'Dit veld is verplicht'
    valid = false
  } else {
    delete errors.ondertekenFunctie
  }
  if (!form.ondertekenDatum) {
    errors.ondertekenDatum = 'Dit veld is verplicht'
    valid = false
  } else {
    delete errors.ondertekenDatum
  }
  if (!signaturePad || signaturePad.isEmpty()) {
    errors.handtekening = 'Handtekening is verplicht'
    valid = false
  } else {
    delete errors.handtekening
  }
  return valid
}

const { proxy } = getCurrentInstance()
function scrollToFirstError() {
  nextTick(() => {
    for (const key in errors) {
      if (proxy.$refs['ref_' + key]) {
        proxy.$refs['ref_' + key].scrollIntoView({ behavior: 'smooth', block: 'center' })
        if (proxy.$refs['ref_' + key].focus) proxy.$refs['ref_' + key].focus()
        break
      }
      const match = key.match(/^actie_(\d+)$/)
      if (match) {
        const idx = match[1]
        if (proxy.$refs['ref_actieOmschrijving_' + idx]) {
          proxy.$refs['ref_actieOmschrijving_' + idx].scrollIntoView({ behavior: 'smooth', block: 'center' })
          proxy.$refs['ref_actieOmschrijving_' + idx].focus()
          break
        }
        if (proxy.$refs['ref_actieFoto_' + idx]) {
          proxy.$refs['ref_actieFoto_' + idx].scrollIntoView({ behavior: 'smooth', block: 'center' })
          break
        }
      }
      if (key === 'handtekening' && proxy.$refs.signatureCanvas) {
        proxy.$refs.signatureCanvas.scrollIntoView({ behavior: 'smooth', block: 'center' })
        break
      }
    }
  })
}

function clearSignature() {
  if (signaturePad) {
    signaturePad.clear()
    errors.handtekening = 'Handtekening is verplicht'
    signatureState.value++
  }
}

onMounted(async () => {
  const { default: SignaturePad } = await import('signature_pad')
  signaturePad = new SignaturePad(signatureCanvas.value, {
    backgroundColor: '#fff',
    penColor: '#000',
    minWidth: 1,
    maxWidth: 2,
  })
  signatureCanvas.value.addEventListener('mouseup', () => { signatureState.value++ })
  signatureCanvas.value.addEventListener('touchend', () => { signatureState.value++ })
})

async function drawImageAutoSize(doc, base64img, x, y, targetWidth = 120, targetHeight = 80, previewId = null) {
  return new Promise(resolve => {
    const img = new window.Image();
    img.onload = function () {
      // Haal base64 uit browser-rendered preview <img> als previewId is opgegeven
      if (previewId) {
        const imgEl = document.getElementById(previewId);
        if (imgEl) {
          const canvas = document.createElement('canvas');
          canvas.width = imgEl.naturalWidth;
          canvas.height = imgEl.naturalHeight;
          const ctx = canvas.getContext('2d');
          ctx.drawImage(imgEl, 0, 0);
          base64img = canvas.toDataURL('image/jpeg');
        }
      }
      // Daarna als altijd: schalen naar target
      const ratio = Math.min(targetWidth / img.width, targetHeight / img.height);
      const drawWidth = img.width * ratio;
      const drawHeight = img.height * ratio;
      let offsetX = x, offsetY = y;
      if (drawWidth < targetWidth) offsetX += (targetWidth - drawWidth) / 2;
      if (drawHeight < targetHeight) offsetY += (targetHeight - drawHeight) / 2;
      doc.addImage(base64img, 'JPEG', offsetX, offsetY, drawWidth, drawHeight);
      resolve({ yNew: y + targetHeight + 8, height: targetHeight });
    };
    img.src = base64img;
  });
}


async function downloadPdf() {
  if (!validateForm()) {
    scrollToFirstError();
    return;
  }
  try {
    const { default: jsPDF } = await import('jspdf');
    const doc = new jsPDF();
    let y = 16;

    // Headertekst
    doc.setFontSize(11);
    const herstelTekst = [
      'Indien alle te herstellen punten zijn uitgevoerd kan onderstaande herstelverklaring worden ingevuld en ondertekend.',
      'De verklaring kunt u vervolgens samen met een kopie van het inspectierapport opsturen naar uw verzekeraar.',
      'Alle gebreken geclassificeerd als I (A) en II (B) fouten, zoals vastgelegd in het inspectierapport vakkundig zijn hersteld.',
      'De werkzaamheden zijn uitgevoerd conform de geldende NEN 1010 installatievoorschriften.'
    ];
    herstelTekst.forEach(t => {
      const lines = doc.splitTextToSize(t, 180);
      doc.text(lines, 14, y);
      y += lines.length * 5;
    });
    y += 8;

    // Inspectierapport
    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text('Inspectierapport:', 14, y);
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);
    y += 6;
    doc.text(`Naam rapport: ${form.rapportNaam}`, 14, y); y += 6;
    doc.text(`Datum uitvoer: ${form.rapportDatum}`, 14, y); y += 10;

    // Installatiebedrijf
    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text('Installatiebedrijf:', 14, y);
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);
    y += 6;
    doc.text(`Bedrijfsnaam: ${form.bedrijf}`, 14, y); y += 6;
    doc.text(`Adres: ${form.adres}`, 14, y); y += 6;
    doc.text(`Postcode/plaats: ${form.postcode} ${form.plaats}`, 14, y); y += 6;
    doc.text(`Telefoonnummer: ${form.telefoon}`, 14, y); y += 10;

    // Herstelacties
    doc.setFontSize(14);
    doc.setTextColor(0, 100, 200);
    doc.text('Herstelacties', 14, y); y += 8;
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);

    // Voor elke actie: tekst + foto, met paginawissel waar nodig
    for (let idx = 0; idx < form.acties.length; idx++) {
      const actie = form.acties[idx];

      // Bepaal ruimte die nodig is voor tekst + foto (indien foto aanwezig)
      const lines = doc.splitTextToSize(actie.omschrijving, 180);
      const textHeight = lines.length * 5 + 6;
      const photoHeight = actie.foto ? 80 + 8 : 0;
      const roomNeeded = textHeight + photoHeight + 8;

      if (y + roomNeeded > 265) { // Houd voldoende ondermarge (A4)
        doc.addPage();
        y = 20;
      }

      doc.setFont(undefined, "bold");
      doc.text(`Actie ${idx + 1}:`, 14, y); y += 6;
      doc.setFont(undefined, "normal");
      doc.text(lines, 14, y); y += lines.length * 5 + 2;

      if (actie.foto) {
  const previewId = `preview_foto_${idx}`;
  const result = await drawImageAutoSize(doc, actie.foto, 14, y, 120, 80, previewId);
  y = result.yNew;
}

      y += 4;
    }

    // Ondertekening (ook met ruimte-check!)
    const ondertekenHoogte = 6 * 4 + 35 + 25; // velden + handtekening + marge
    if (y + ondertekenHoogte > 265) {
      doc.addPage();
      y = 20;
    }

    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text('Ondertekening:', 14, y);
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);
    y += 6;
    doc.text(`Plaats: ${form.ondertekenPlaats}`, 14, y); y += 6;
    doc.text(`Naam: ${form.ondertekenNaam}`, 14, y); y += 6;
    doc.text(`Functie: ${form.ondertekenFunctie}`, 14, y); y += 6;
    doc.text(`Datum: ${form.ondertekenDatum}`, 14, y); y += 8;
    doc.text('Handtekening:', 14, y); y += 5;

    if (signaturePad && !signaturePad.isEmpty()) {
      try {
        const signatureData = signaturePad.toDataURL('image/jpeg');
        doc.addImage(signatureData, 'JPEG', 14, y, 60, 30);
        y += 35;
      } catch (e) {
        doc.text('⚠️ Fout bij het toevoegen van de handtekening', 14, y);
        y += 12;
      }
    } else {
      y += 12;
    }

    // Opslaan
    const now = new Date();
    const dateStr = now.toISOString().split('T')[0];
    const safeBedrijf = form.bedrijf ? form.bedrijf.replace(/[^a-zA-Z0-9]/g, '_') : 'herstelverklaring';
    const filename = `herstelverklaring_${safeBedrijf}_${dateStr}.pdf`;
    doc.save(filename);
  } catch (e) {
    alert('Er is iets misgegaan bij het genereren van de PDF:\n' + e);
    console.error('PDF fout:', e);
  }
}
</script>

