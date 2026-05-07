    <script setup>
    import { ref, watch } from 'vue';

    const total = ref(0);
    const prijem = ref(true)
    const operacie = ref([]);

    const cena = ref();

    watch(operacie, (newOperacie) => {
        // vypocitanie total z erraju 'operacie'
        total.value = newOperacie.reduce((acc, operacia) => acc + (operacia.prijem ? operacia.value : -operacia.value), 0);
    }, { deep: true });

    function addOperacia() {
    if(cena.value !== undefined && cena.value !== null && cena.value !== 0)
        operacie.value.push({ value: cena.value, prijem: prijem.value });
    }

    </script>

    <template>
        <div class="w-screen p-4 h-3/4 bg-blue-900 flex flex-wrap justify-center">
            <div class="min-h-16 w-1/6 mx-2 my-2 p-3 flex justify-center items-center text-3xl" :class="operacia.prijem ? 'bg-green-500 text-white' : 'bg-red-500 text-white'" v-for="operacia in operacie" :key="operacia.value">{{ operacia.value }} {{ operacia.prijem ? 'Prijem' : 'Vytrata' }}</div>
        </div>

        <div class="h-1/4">
            <div class="h-1/2 flex items-center justify-around">
                <input class="rounded-2xl bg-blue-200 px-5 w-60 h-9" v-model.number="cena" type="number" placeholder="Write a value"> </input>
                <select class="p-2 rounded-2xl" :class="prijem ? 'bg-green-500 text-white' : 'bg-red-500 text-white'" v-model.boolean="prijem" @change="console.log('prijem:', prijem)">
                    <option :value="false">Vytrata</option>
                    <option :value="true">Prijem</option>
                </select>
                <button class="bg-blue-900 p-2 w-50 rounded-2xl text-white" @click="addOperacia">Add</button>
            </div>
            <div :class="total > 0 ? 'bg-green-500 text-white' : 'bg-red-500 text-white'" class=" flex text-3xl justify-around bg-gray-400 h-9 w-screen ">
                <label  class="">TOTAL: {{ total }}</label>
            </div>
        </div>
    </template>

    <style scoped></style>
