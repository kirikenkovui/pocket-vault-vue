<script setup>
import { ref, watch } from "vue";

const total = ref(0);
const prijem = ref(true);
const operacie = ref([]);

const pobyt = ref("Pobyt");
const potraviny = ref("Potraviny");
const darceky = ref("Darceky + Drobne veci");
const kategoria = ref(null);

const cena = ref();

watch(
    operacie,
    (newOperacie) => {
        // vypocitanie total z erraju 'operacie'
        total.value = newOperacie.reduce(
            (acc, operacia) =>
                acc + (operacia.prijem ? operacia.value : -operacia.value),
            0,
        );
    },
    { deep: true },
);

function addOperacia() {
    // overit ci cena je definovana a nenulova
    if (
        cena.value == undefined &&
        cena.value == null &&
        cena.value == 0 &&
        cena.value == ""
    )
        return;
    console.log("1");
    if (prijem.value == true) {
        operacie.value.push({
            value: cena.value,
            prijem: true,
        });
    } else {
        if (kategoria.value == null) return;
        operacie.value.push({
            value: cena.value,
            prijem: false,
            kategoria: kategoria.value,
        });
    }
}
</script>

<template>
    <div class="min-h-screen w-screen flex flex-col bg-white">
        <!-- hlavne zobrazenie -->
        <div class="flex-1 p-4 md:p-6 overflow-y-auto">
            <!-- ked nema operacie, zobrazime prazdny stav -->
            <div
                class="h-64 flex flex-col items-center justify-center"
                v-if="operacie.length === 0"
            >
                <div
                    class="text-black font-light text-4xl md:text-5xl text-center"
                >
                    Zatiaľ tu nič nie je
                </div>
                <p class="text-gray-500 mt-4 text-lg">
                    Pridajte svoju prvú operáciu nižšie
                </p>
            </div>

            <!-- operacie -->
            <div
                class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4"
                v-if="operacie.length > 0"
            >
                <div
                    class="operation-card min-h-24 p-4 md:p-5 flex flex-col justify-between rounded-2xl card-shadow backdrop-blur-sm transition-all"
                    :class="
                        operacia.prijem
                            ? 'bg-linear-to-br from-green-500 to-green-600 text-white hover:from-green-600 hover:to-green-700'
                            : 'bg-linear-to-br from-red-500 to-red-600 text-white hover:from-red-600 hover:to-red-700'
                    "
                    v-for="(operacia, index) in operacie"
                    :key="index"
                >
                    <div class="flex justify-between items-start">
                        <div class="flex flex-col flex-1">
                            <span class="text-2xl md:text-3xl font-bold">{{
                                operacia.value
                            }}</span>
                            <span
                                class="text-xs md:text-sm font-semibold opacity-90 mt-1"
                                >{{
                                    operacia.prijem ? "🔼 Príjem" : "🔽 Výdaj"
                                }}</span
                            >
                        </div>
                    </div>
                    <div
                        v-if="operacia.kategoria"
                        class="mt-3 bg-white/20 px-3 py-2 rounded-lg text-xs md:text-sm font-medium backdrop-blur-sm text-center truncate border border-white/30"
                    >
                        {{ operacia.kategoria }}
                    </div>
                </div>
            </div>
        </div>

        <!-- input -->
        <div class="bg-white/40 p-4 md:p-6 sticky bottom-0">
            <!-- form -->
            <div class="grid grid-cols-1 lg:grid-cols-5 gap-3 mb-4">
                <input
                    class="rounded-xl bg-blue-700 hover:bg-blue-800 px-4 py-3 text-white placeholder-gray-400 font-medium"
                    v-model.number="cena"
                    type="number"
                    placeholder="Sadzba"
                />

                <select
                    class="rounded-xl p-3 font-semibold text-white transition-colors"
                    :class="
                        prijem
                            ? 'bg-green-600 hover:bg-green-700'
                            : 'bg-red-600 hover:bg-red-700'
                    "
                    v-model.boolean="prijem"
                >
                    <option :value="false">Výdaj 🔽</option>
                    <option :value="true">Príjem 🔼</option>
                </select>

                <select
                    v-if="prijem === false"
                    class="md:col-span-1 lg:col-span-2 rounded-xl p-3 bg-blue-700/80 text-white font-medium"
                    v-model="kategoria"
                >
                    <option :value="null">-- Vyberte kategóriu --</option>
                    <option :value="pobyt">🏠 {{ pobyt }}</option>
                    <option :value="potraviny">🍽️ {{ potraviny }}</option>
                    <option :value="darceky">🎁 {{ darceky }}</option>
                    <option value="Zdravie">⚕️ Zdravie + Lieky</option>
                    <option value="Transport">🚗 Transport</option>
                    <option value="Zabava">🎬 Zábava + Kino</option>
                    <option value="Platby">💳 Platby + Subscripcie</option>
                    <option value="Sporenie">💰 Sporenie</option>
                    <option value="Ostatne">📋 Ostatné</option>
                </select>

                <button
                    class="md:col-span-1 lg:col-span-1 bg-blue-700 hover:bg-blue-800 p-3 rounded-xl text-white font-bold"
                    @click="addOperacia"
                >
                    Pridať
                </button>
            </div>

            <!-- Total -->
            <div
                :class="
                    total > 0
                        ? 'bg-green-600 text-white'
                        : 'bg-red-600 text-white'
                "
                class="flex flex-col md:flex-row justify-between items-center p-4 rounded-xl font-bold text-xl md:text-2xl card-shadow"
            >
                <span class="mb-2 md:mb-0">CELKOM:</span>
                <span class="text-3xl md:text-4xl font-bold">{{
                    total.toFixed(2)
                }}</span>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
