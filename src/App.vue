<script setup>
import HelloWorld from './components/HelloWorld.vue'
import {ref, onMounted} from 'vue'
import PocketBase from 'pocketbase'

const pb = new PocketBase("http://127.0.0.1:8090")
const title = "친구"
let number = ref(0)
let name = ref("")
let capitalCities = ref([])
const isTrue = ref(true);
let solatapi = "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=SGR01";
let prayerTime = ref({});
const records = ref([]); 
const todoInput = ref("");

function increment(){
  number.value++;
  console.log(number);
}

function startInterval(){
  setInterval(increment, 1000);
}

//append data to array
function addToList(){
  const newTodo = {
    item: todoInput.value,
    isDone: false
  }
  
  pb.collection('todos').create(newTodo).then(result => {
    records.value.push(result)
    todoInput.value = ""
  })
}

//fetch api data
function getPrayerTime(){
  fetch(solatapi).then(result => result.json()).then(data => {prayerTime.value = data.prayerTime[0]
    console.log(prayerTime.value)})
}

//immediately used after load web
onMounted(() => {
  getPrayerTime()
  pb.collection('todos').getFullList({}).then(data => records.value = data);
})
</script>

<template>
  <div class="max-w-3xl mx-auto p-6 bg-white shadow-lg rounded-xl space-y-6">
    <!-- Counter Section -->
    <div class="text-center">
      <h1 class="text-4xl font-bold text-gray-800 mb-4">{{ number }}</h1>
      <button
        @click="startInterval"
        class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded transition"
      >
        Increment
      </button>
    </div>

    <!-- Name Input and List -->
    <div class="flex items-center gap-4">
      <input
        type="text"
        v-model="todoInput"
        placeholder="Anything"
        class="flex-1 px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400"
      />
      <button
        @click="addToList"
        class="bg-green-600 hover:bg-green-700 text-white font-medium py-2 px-4 rounded transition"
      >
        Add
      </button>
    </div>

    <div>
      <h1 class="text-2xl font-semibold text-gray-700">{{ title }}</h1>

      <ol class="list-decimal pl-6 mt-2 space-y-1 text-gray-700">
        <li v-for="(i, index) in records" :key="index">{{ i.item }}</li>
      </ol>
    </div>

    <!-- Conditional Statement -->
    <div class="text-center space-y-2">
      <h3 v-if="isTrue" class="text-xl font-medium text-pink-600">"England is my city"</h3>
      <button
        @click="isTrue = !isTrue"
        class="bg-purple-600 hover:bg-purple-700 text-white font-medium py-2 px-4 rounded transition"
      >
        Toggle Message
      </button>
    </div>

    <!-- Prayer Time Section -->
    <div class="text-center">
      <button
        @click="getPrayerTime"
        class="bg-yellow-500 hover:bg-yellow-600 text-white font-medium py-2 px-6 rounded transition mb-4"
      >
        Get Prayer Time
      </button>

      <div class="overflow-x-auto border rounded-lg bg-gray-100 p-4 shadow-md">
        <table class="w-full table-auto text-center">
          <thead class="bg-gray-200 text-gray-700">
            <tr>
              <th class="py-2">Subuh</th>
              <th class="py-2">Dhuha</th>
              <th class="py-2">Zohor</th>
              <th class="py-2">Asar</th>
              <th class="py-2">Maghrib</th>
              <th class="py-2">Isha'</th>
            </tr>
          </thead>
          <tbody>
            <tr class="bg-white text-gray-800">
              <td class="py-2">{{ prayerTime.fajr }}</td>
              <td class="py-2">{{ prayerTime.dhuha }}</td>
              <td class="py-2">{{ prayerTime.dhuhr }}</td>
              <td class="py-2">{{ prayerTime.asr }}</td>
              <td class="py-2">{{ prayerTime.maghrib }}</td>
              <td class="py-2">{{ prayerTime.isha }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>


<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
