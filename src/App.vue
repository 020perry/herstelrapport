<template>
  <div class="min-h-screen bg-gray-50 py-8 px-4">
    <div class="max-w-2xl mx-auto">
      <div class="text-center mb-12">
        <h1 class="text-3xl font-light text-gray-900 mb-3">Herstelverklaring</h1>
        <p class="text-gray-600 text-sm">Vul onderstaande gegevens zorgvuldig in</p>
      </div>

      <form @submit.prevent class="space-y-8">

        <!-- ALGEMENE GEGEVENS -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">
          <h2 class="text-lg font-medium text-gray-900 mb-6 pb-3 border-b border-gray-100">
            Algemene Gegevens
          </h2>

          <div class="space-y-6">

            <!-- Naam rapport -->
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Naam rapport *</label>
              <input
                v-model="form.rapportNaam"
                ref="ref_rapportNaam"
                :class="getInputClass('rapportNaam')"
                placeholder="Voer de naam van het rapport in"
              />
              <p v-if="errors.rapportNaam" class="mt-1 text-sm text-red-600">{{ errors.rapportNaam }}</p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <!-- Datum -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Datum uitvoering *</label>
                <input
                  v-model="form.rapportDatum"
                  ref="ref_rapportDatum"
                  type="date"
                  :class="getInputClass('rapportDatum')"
                />
                <p v-if="errors.rapportDatum" class="mt-1 text-sm text-red-600">{{ errors.rapportDatum }}</p>
              </div>

              <!-- Bedrijfsnaam -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Bedrijfsnaam *</label>
                <input
                  v-model="form.bedrijf"
                  ref="ref_bedrijf"
                  :class="getInputClass('bedrijf')"
                  placeholder="Naam van het bedrijf"
                />
                <p v-if="errors.bedrijf" class="mt-1 text-sm text-red-600">{{ errors.bedrijf }}</p>
              </div>
            </div>

            <!-- Adres -->
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Adres *</label>
              <input
                v-model="form.adres"
                ref="ref_adres"
                :class="getInputClass('adres')"
                placeholder="Straatnaam en huisnummer"
              />
              <p v-if="errors.adres" class="mt-1 text-sm text-red-600">{{ errors.adres }}</p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <!-- Postcode -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Postcode *</label>
                <input
                  v-model="form.postcode"
                  ref="ref_postcode"
                  :class="getInputClass('postcode')"
                  placeholder="1234 AB"
                />
                <p v-if="errors.postcode" class="mt-1 text-sm text-red-600">{{ errors.postcode }}</p>
              </div>

              <!-- Plaats -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Plaats *</label>
                <input
                  v-model="form.plaats"
                  ref="ref_plaats"
                  :class="getInputClass('plaats')"
                  placeholder="Plaatsnaam"
                />
                <p v-if="errors.plaats" class="mt-1 text-sm text-red-600">{{ errors.plaats }}</p>
              </div>
            </div>

            <!-- Telefoon -->
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Telefoonnummer *</label>
              <input
                v-model="form.telefoon"
                ref="ref_telefoon"
                type="tel"
                :class="getInputClass('telefoon')"
                placeholder="06-12345678"
              />
              <p v-if="errors.telefoon" class="mt-1 text-sm text-red-600">{{ errors.telefoon }}</p>
            </div>

          </div>
        </section>


        <!-- HERSTELACTIES -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">

          <div class="flex justify-between items-center mb-6 pb-3 border-b border-gray-100">
            <h2 class="text-lg font-medium text-gray-900">Herstelacties</h2>
            <span class="text-xs text-gray-500 bg-gray-100 px-2 py-1 rounded">{{ form.acties.length }}</span>
          </div>

          <div class="space-y-6">

            <div
              v-for="(actie, index) in form.acties"
              :key="index"
              class="border border-gray-100 rounded-lg p-6 bg-gray-50"
            >

              <div class="flex justify-between items-start mb-4">
                <h3 class="text-sm font-medium text-gray-900">Actie {{ index + 1 }}</h3>

                <button
                  v-if="form.acties.length > 1"
                  type="button"
                  @click="removeAction(index)"
                  class="text-xs text-gray-500 hover:text-red-600 underline"
                >
                  Verwijderen
                </button>
              </div>


              <!-- Omschrijving -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Omschrijving *</label>

                <textarea
                  v-model="actie.omschrijving"
                  :ref="'ref_actieOmschrijving_' + index"
                  :class="getTextareaClass(index)"
                  rows="3"
                  placeholder="Beschrijf de uitgevoerde herstelactie..."
                ></textarea>

                <p
                  v-if="errors[`actie_${index}`] && errors[`actie_${index}`].includes('Omschrijving')"
                  class="mt-1 text-sm text-red-600"
                >
                  {{ errors[`actie_${index}`] }}
                </p>
              </div>


              <!-- FOTO UPLOAD -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Foto's *</label>

                <div class="border border-gray-200 rounded-lg p-4 bg-white">

                  <!-- FILE INPUT (VERBORGEN) -->
                  <input
                    type="file"
                    accept="image/*"
                    multiple
                    class="hidden"
                    :id="`file-${index}`"
                    @change="(e) => onFilesChange(e, index)"
                  />

                  <!-- GEEN FOTO'S -->
                  <label
                    v-if="actie.fotos.length === 0"
                    :for="`file-${index}`"
                    class="cursor-pointer text-gray-400 flex flex-col items-center"
                  >
                    <svg class="h-8 w-8 mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 4v16m8-8H4"/>
                    </svg>
                    <p class="text-xs">Foto's toevoegen</p>
                  </label>


                  <!-- FOTO GRID -->
                  <div v-if="actie.fotos.length > 0" class="grid grid-cols-3 gap-3">

                    <!-- THUMBNAILS -->
                    <div v-for="(foto, fIndex) in actie.fotos" :key="fIndex" class="relative">
                      <img :src="foto.preview" class="h-24 w-24 object-cover rounded" />
                      <button
                        type="button"
                        @click="removeSinglePhoto(index, fIndex)"
                        class="absolute top-1 right-1 bg-white shadow rounded-full w-5 h-5 flex items-center justify-center text-xs hover:text-red-600"
                      >
                        ×
                      </button>
                    </div>

                    <!-- ⭐ EXTRA FOTO TOEVOEGEN KNOP -->
                    <label
                      :for="`file-${index}`"
                      class="flex items-center justify-center border border-dashed border-gray-300 rounded cursor-pointer hover:bg-gray-100 transition h-24"
                    >
                      <span class="text-gray-500 text-sm">+ Foto</span>
                    </label>

                  </div>

                </div>

                <p
                  v-if="errors[`actie_${index}`] && errors[`actie_${index}`].includes('Foto')"
                  class="mt-1 text-sm text-red-600"
                >
                  {{ errors[`actie_${index}`] }}
                </p>
              </div>

            </div>
          </div>


          <!-- Nieuwe actie toevoegen -->
          <button
            type="button"
            @click="addAction"
            class="mt-6 w-full bg-black text-white py-3 px-4 rounded-lg hover:bg-gray-900 text-sm font-semibold"
          >
            + Herstelactie toevoegen
          </button>

        </section>


        <!-- ONDERTEKENING -->
        <section class="bg-white rounded-lg border border-gray-200 p-8">

          <h2 class="text-lg font-medium text-gray-900 mb-6 pb-3 border-b border-gray-100">Ondertekening</h2>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Plaats *</label>
              <input
                v-model="form.ondertekenPlaats"
                ref="ref_ondertekenPlaats"
                :class="getInputClass('ondertekenPlaats')"
                placeholder="Plaats van ondertekening"
              />
              <p v-if="errors.ondertekenPlaats" class="text-sm text-red-600">{{ errors.ondertekenPlaats }}</p>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Naam *</label>
              <input
                v-model="form.ondertekenNaam"
                ref="ref_ondertekenNaam"
                :class="getInputClass('ondertekenNaam')"
                placeholder="Naam ondertekenaar"
              />
              <p v-if="errors.ondertekenNaam" class="text-sm text-red-600">{{ errors.ondertekenNaam }}</p>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Functie *</label>
              <input
                v-model="form.ondertekenFunctie"
                ref="ref_ondertekenFunctie"
                :class="getInputClass('ondertekenFunctie')"
                placeholder="Functie ondertekenaar"
              />
              <p v-if="errors.ondertekenFunctie" class="text-sm text-red-600">{{ errors.ondertekenFunctie }}</p>
            </div>

          </div>

          <div class="mb-6">
            <label class="block text-sm font-medium text-gray-700 mb-2">Datum *</label>
            <input
              v-model="form.ondertekenDatum"
              ref="ref_ondertekenDatum"
              type="date"
              :class="getInputClass('ondertekenDatum')"
              readonly
            />
            <p v-if="errors.ondertekenDatum" class="text-sm text-red-600">{{ errors.ondertekenDatum }}</p>
          </div>


          <!-- HANDTEKENING -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-3">Handtekening *</label>

            <div class="border border-gray-200 rounded-lg p-4 bg-gray-50">
              <canvas
                ref="signatureCanvas"
                class="border border-gray-300 w-full h-32 rounded bg-white cursor-crosshair"
              ></canvas>

              <div class="flex justify-between items-center mt-3">
                <p class="text-xs text-gray-500">Zet uw handtekening hierboven</p>
                <button
                  type="button"
                  @click="clearSignature"
                  class="text-xs text-gray-600 hover:text-red-600 underline"
                >
                  Wissen
                </button>
              </div>

              <p v-if="errors.handtekening" class="text-sm text-red-600">{{ errors.handtekening }}</p>
            </div>

          </div>

        </section>

        <!-- DOWNLOAD -->
        <div class="flex flex-col sm:flex-row gap-3 mt-2">
          <button
            type="button"
            @click="downloadPdf"
            class="flex-1 py-3 px-6 rounded-lg bg-black text-white hover:bg-gray-900 text-sm font-semibold"
          >
            Download PDF
          </button>
        </div>

        <p
          v-if="Object.keys(errors).length"
          class="text-xs text-red-600 text-center mt-2"
        >
          Vul alle verplichte velden correct in en corrigeer de fouten hierboven.
        </p>

      </form>
    </div>
  </div>
</template>


<script setup>
import {
  reactive,
  ref,
  onMounted,
  nextTick,
  getCurrentInstance,
} from "vue";

/* ---------------------------------------------------------
   Helpers
--------------------------------------------------------- */
function today() {
  return new Date().toISOString().slice(0, 10);
}

/* ---------------------------------------------------------
   Reactive state
--------------------------------------------------------- */
const form = reactive({
  rapportNaam: "",
  rapportDatum: "",
  bedrijf: "",
  adres: "",
  postcode: "",
  plaats: "",
  telefoon: "",
  acties: [{ omschrijving: "", fotos: [] }],
  ondertekenPlaats: "",
  ondertekenNaam: "",
  ondertekenFunctie: "",
  ondertekenDatum: today(),
});

const errors = reactive({});
const signatureCanvas = ref(null);
let signaturePad = null;

/* ---------------------------------------------------------
   UI helpers
--------------------------------------------------------- */
function getInputClass(fieldName) {
  const base =
    "w-full border rounded-lg px-3 py-2 text-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500";

  return errors[fieldName]
    ? `${base} border-red-300 bg-red-50`
    : `${base} border-gray-300 hover:border-blue-500`;
}

function getTextareaClass(index) {
  const base =
    "w-full border rounded-lg px-3 py-2 text-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 resize-vertical";

  return errors[`actie_${index}`]
    ? `${base} border-red-300 bg-red-50`
    : `${base} border-gray-300 hover:border-blue-500`;
}

/* ---------------------------------------------------------
   Validatie
--------------------------------------------------------- */
function validateField(fieldName) {
  if (!form[fieldName] || form[fieldName].toString().trim() === "") {
    errors[fieldName] = "Dit veld is verplicht";
    return false;
  }

  if (fieldName === "postcode") {
    const re = /^[1-9][0-9]{3}\s?[A-Za-z]{2}$/;
    if (!re.test(form.postcode)) {
      errors.postcode = "Voer een geldige postcode in (bijv. 1234 AB)";
      return false;
    }
  }

  if (fieldName === "telefoon") {
    const re = /^(?:\+31|0)[1-9][0-9]{8}$/;
    if (!re.test(form.telefoon.replace(/[\s-]/g, ""))) {
      errors.telefoon = "Voer een geldig Nederlands telefoonnummer in";
      return false;
    }
  }

  delete errors[fieldName];
  return true;
}

function validateAction(index) {
  const actie = form.acties[index];

  if (!actie.omschrijving || actie.omschrijving.trim() === "") {
    errors[`actie_${index}`] = "Omschrijving is verplicht";
    return false;
  }

  if (actie.omschrijving.length < 10) {
    errors[`actie_${index}`] =
      "Omschrijving moet minimaal 10 karakters bevatten";
    return false;
  }

  if (!actie.fotos || actie.fotos.length === 0) {
    errors[`actie_${index}`] = "Minimaal 1 foto is verplicht";
    return false;
  }

  delete errors[`actie_${index}`];
  return true;
}

/* ---------------------------------------------------------
   Acties toevoegen / verwijderen
--------------------------------------------------------- */
function addAction() {
  form.acties.push({ omschrijving: "", fotos: [] });
}

function removeAction(index) {
  form.acties.splice(index, 1);
  delete errors[`actie_${index}`];
}

/* ---------------------------------------------------------
   FOTO UPLOAD — MULTIPLE & PREVIEWS
--------------------------------------------------------- */
function onFilesChange(e, index) {
  const files = Array.from(e.target.files);

  files.forEach((file) => {
    const reader = new FileReader();
    reader.onload = () => {
      form.acties[index].fotos.push({
        file,
        preview: reader.result, // base64 voor thumbnail & PDF
      });
    };
    reader.readAsDataURL(file);
  });
}

function removeSinglePhoto(aIndex, fIndex) {
  form.acties[aIndex].fotos.splice(fIndex, 1);
}

/* ---------------------------------------------------------
   Volledige form-validatie
--------------------------------------------------------- */
function validateForm() {
  let valid = true;

  Object.keys(form).forEach((key) => {
    if (key !== "acties" && !validateField(key)) valid = false;
  });

  form.acties.forEach((actie, idx) => {
    if (!validateAction(idx)) valid = false;
  });

  if (!signaturePad || signaturePad.isEmpty()) {
    errors.handtekening = "Handtekening is verplicht";
    valid = false;
  } else delete errors.handtekening;

  return valid;
}

/* ---------------------------------------------------------
   Scroll naar eerste fout
--------------------------------------------------------- */
const { proxy } = getCurrentInstance();

function scrollToFirstError() {
  nextTick(() => {
    for (const key in errors) {
      if (proxy.$refs["ref_" + key]) {
        const el = proxy.$refs["ref_" + key];
        el.scrollIntoView({ behavior: "smooth", block: "center" });
        el.focus?.();
        break;
      }

      const match = key.match(/^actie_(\d+)$/);
      if (match) {
        const idx = match[1];
        const textarea = proxy.$refs["ref_actieOmschrijving_" + idx];
        if (textarea) {
          textarea.scrollIntoView({ behavior: "smooth", block: "center" });
          textarea.focus?.();
          break;
        }
      }
    }
  });
}

/* ---------------------------------------------------------
   Handtekening
--------------------------------------------------------- */
function clearSignature() {
  signaturePad?.clear();
  errors.handtekening = "Handtekening is verplicht";
}

onMounted(async () => {
  const { default: SignaturePad } = await import("signature_pad");
  signaturePad = new SignaturePad(signatureCanvas.value, {
    backgroundColor: "#fff",
    penColor: "#000",
  });
});

/* ---------------------------------------------------------
   Hulp: automatisch foto schalen voor PDF
--------------------------------------------------------- */
async function drawImageAutoSize(doc, base64img, x, y, maxW, maxH) {
  return new Promise((resolve) => {
    const img = new Image();

    img.onload = () => {
      const ratio = Math.min(maxW / img.width, maxH / img.height);
      const w = img.width * ratio;
      const h = img.height * ratio;

      doc.addImage(base64img, "JPEG", x, y, w, h);
      resolve({ yNew: y + h + 10 });
    };

    img.src = base64img;
  });
}

/* ---------------------------------------------------------
   PDF GENERATIE
--------------------------------------------------------- */
async function downloadPdf() {
  if (!validateForm()) {
    scrollToFirstError();
    return;
  }

  try {
    const { default: jsPDF } = await import("jspdf");
    const doc = new jsPDF();
    let y = 16;

    /* ---- Header ---- */
    doc.setFontSize(11);
    const intro = [
      "Indien alle te herstellen punten zijn uitgevoerd kan onderstaande herstelverklaring worden ingevuld en ondertekend.",
      "Deze verklaring kunt u vervolgens samen met een kopie van het inspectierapport opsturen naar uw verzekeraar.",
      "Alle gebreken geclassificeerd als I (A) en II (B) fouten zijn vakkundig hersteld conform NEN 1010.",
    ];

    intro.forEach((t) => {
      const lines = doc.splitTextToSize(t, 180);
      doc.text(lines, 14, y);
      y += lines.length * 5;
    });

    y += 8;

    /* ---- Inspectie info ---- */
    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text("Inspectierapport", 14, y);
    y += 8;
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);

    doc.text(`Naam rapport: ${form.rapportNaam}`, 14, y);
    y += 6;
    doc.text(`Datum uitvoer: ${form.rapportDatum}`, 14, y);
    y += 10;

    /* ---- Bedrijf ---- */
    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text("Installatiebedrijf", 14, y);
    y += 8;
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);

    doc.text(`Bedrijfsnaam: ${form.bedrijf}`, 14, y);
    y += 6;
    doc.text(`Adres: ${form.adres}`, 14, y);
    y += 6;
    doc.text(`Postcode/plaats: ${form.postcode} ${form.plaats}`, 14, y);
    y += 6;
    doc.text(`Telefoonnummer: ${form.telefoon}`, 14, y);
    y += 10;

    /* ---- Herstelacties ---- */
    doc.setFontSize(14);
    doc.setTextColor(0, 80, 200);
    doc.text("Herstelacties", 14, y);
    y += 10;

    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);

    for (let idx = 0; idx < form.acties.length; idx++) {
      const actie = form.acties[idx];

      // Nieuwe pagina indien nodig
      if (y > 240) {
        doc.addPage();
        y = 20;
      }

      // Titel
      doc.setFont(undefined, "bold");
      doc.text(`Actie ${idx + 1}:`, 14, y);
      y += 6;
      doc.setFont(undefined, "normal");

      // Tekst
      const textLines = doc.splitTextToSize(actie.omschrijving, 180);
      doc.text(textLines, 14, y);
      y += textLines.length * 5 + 4;

      // Foto's
      if (actie.fotos.length > 0) {
        for (let f = 0; f < actie.fotos.length; f++) {
          if (y > 230) {
            doc.addPage();
            y = 20;
          }

          const foto = actie.fotos[f];
          const imgRes = await drawImageAutoSize(doc, foto.preview, 14, y, 170, 120);

          y = imgRes.yNew;
        }
      }

      y += 10;
    }

    /* ---- Ondertekening ---- */
    if (y > 220) {
      doc.addPage();
      y = 20;
    }

    doc.setFontSize(14);
    doc.setTextColor(0, 128, 0);
    doc.text("Ondertekening", 14, y);

    y += 8;
    doc.setFontSize(11);
    doc.setTextColor(0, 0, 0);

    doc.text(`Plaats: ${form.ondertekenPlaats}`, 14, y);
    y += 6;
    doc.text(`Naam: ${form.ondertekenNaam}`, 14, y);
    y += 6;
    doc.text(`Functie: ${form.ondertekenFunctie}`, 14, y);
    y += 6;
    doc.text(`Datum: ${form.ondertekenDatum}`, 14, y);
    y += 10;

    try {
      const signature = signaturePad.toDataURL("image/jpeg");
      doc.addImage(signature, "JPEG", 14, y, 60, 30);
      y += 40;
    } catch {}

    /* ---- PDF opslaan ---- */
    const now = new Date();
    const dateStr = now.toISOString().split("T")[0];
    const safeBedrijf = form.bedrijf.replace(/[^a-zA-Z0-9]/g, "_") || "rapport";

    doc.save(`herstelverklaring_${safeBedrijf}_${dateStr}.pdf`);
  } catch (e) {
    alert("Er is iets misgegaan bij het genereren van de PDF.");
    console.error("PDF fout:", e);
  }
}
</script>
