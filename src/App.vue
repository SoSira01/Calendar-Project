<script setup>
import { ref, computed } from 'vue'
const days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"]
const importants = ref([])
const todos = ref([])
const checklists = ref([])
const newImportant = ref('')
const newTodo = ref('')
const newChecklist = ref('')

//calendar 
let currentDate = ref(new Date())
function nextMonth(no) {
  currentDate.value = new Date(currentDate.value.getFullYear(),currentDate.value.getMonth() + no,1);
}

function nextYear(no) {
  currentDate.value = new Date(
    currentDate.value.getFullYear() + no,currentDate.value.getMonth(),1)
}

//month = 0-11
function selectMonth(month) {
  currentDate.value = new Date(currentDate.value.getFullYear(),month,1)
}

//Christian Era ex 2022
function selectYear(year) {
  currentDate.value = new Date(year,currentDate.value.getMonth(),1)
}

//ใช่วันนี้ไหม
function isToday(date) {
  return (
    date === new Date().getDate() &&
    new Date().getMonth() === currentDate.value.getMonth() &&
    new Date().getFullYear() === currentDate.value.getFullYear()
  );
}

//คำนวณว่าตารางช่องนี้เป็นวันที่เท่าไหร่
function calDay(week, day) {
  const firstDayOfMonth = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth(), 1).getDay()
  return (week - 1) * 7 + day - firstDayOfMonth;
}

//1เดือน = กี่วัน
const daysInMonth = computed(() =>new Date(currentDate.value.getFullYear(), currentDate.value.getMonth() + 1, 0).getDate())

function isNotBlank(a) {
  if (a === '') {
    return false
  }
  return true
}

function formatDate(value) {
  let date = new Date(value)
  return { 'year': date.getFullYear(), 'month': date.getMonth(), 'date': date.getDate(), 'day': date.getDay() }
}

function isMonthYear(dayinput) {
  let dayEvent = []
  for (let i = 0; i < importants.value.length; i++) {
    let date = formatDate(importants.value[i].date)
    if (date.year == currentDate.value.getFullYear() && date.month == currentDate.value.getMonth()) {
      dayEvent.push(date.date)
    }
  }
  for (let i = 0; i < checklists.value.length; i++) {
    let date = formatDate(checklists.value[i].date)
    if (date.year == currentDate.value.getFullYear() && date.month == currentDate.value.getMonth()) {
      dayEvent.push(date.date)
    }
  }
  for (let i = 0; i < todos.value.length; i++) {
    let date = formatDate(todos.value[i].date)
    if (date.year == currentDate.value.getFullYear() && date.month == currentDate.value.getMonth()) {
      dayEvent.push(date.date)
    }
  }
  for (let i = 0; i < dayEvent.length; i++) {
    if (dayinput == dayEvent[i]) {
      return true
    }
  }
  return false
}

//ลบค่าใน arr 
function removeArrItemOnce(arr, value) {
  var index = arr.indexOf(value)
  if (index > -1) {
    arr.splice(index, 1)
  }
  return arr;
}

//important
// console.log(importants.value)
// console.log(newImportant.value)
const addImportant = () => {
  if (isNotBlank(newImportant.value) && isNotBlank(mydate.value)) {
    importants.value.push({ 'note': newImportant.value, 'date': mydate.value })
  }
  newImportant.value = ''
}
const deleteImportant = (note) => removeArrItemOnce(importants.value, note)
//checklist
// console.log(checklists.value)
// console.log(newChecklist.value)
const addChecklist = () => { 
  if (isNotBlank(newChecklist.value) && isNotBlank(mydate.value)) { 
    checklists.value.push({'note' :newChecklist.value, 'date': mydate.value})
    }
    newChecklist.value = ''
}
const deleteChecklist = (note) => removeArrItemOnce(checklists.value, note)
//To do
// console.log(todos.value)
// console.log(newTodo.value)
const addTodo = () => { 
  if (isNotBlank(newTodo.value) && isNotBlank(mydate.value)) { 
    todos.value.push({'note': newTodo.value, 'date':mydate.value})
    }
    newTodo.value = ''
}
const deleteTodo = (note) => removeArrItemOnce(todos.value, note)

var mydate = ref()
</script>
 
<template>
  <!--navbar-->
  <div class="border-solid bg-pink-100 col-start-1 col-end-7">
    <h3 class="text-center text-1xl p-3 text-zinc-900">CALENDAR DIARY</h3>
  </div>

  <div id="content" class="grid grid-cols-6 gap-4">
    <!--calendar-->
    <div class="col-start-1 col-end-5">
      <div class="p-5">
        <div class="card p-10 card-compact bg-zinc-400 shadow-xl rounded-lg">
          <table class="text-center text-zinc-900 pl-50 w-full shadow-xl rounded-lg bg-zinc-200">
            <thead>
              <tr>
                <th
                  class="h-20"
                  colspan="7"
                >{{ currentDate.toLocaleString("en-US", { month: "long", year: "numeric", }) }}</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <th v-for="(day, i) in days" :key="i" class="h-10">{{ day }}</th>
              </tr>
              <tr v-for="week in 6" :key="week">
                <td v-for="day in 7" :key="day" class="h-10">
                  <div>
                    <span :class="{ 'text-red-500': isToday(calDay(week, day)) }">
                      {{ calDay(week, day) <= 0 || calDay(week, day) > daysInMonth ? "" : calDay(week, day) }}
                      <div :class="{ 'badge badge-error gap-2 absolute': isMonthYear(calDay(week, day)) }"></div>
                    </span>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <!-- todo -->
          <div class="card-body">
            <h2 class="card-title text-zinc-900">Option!</h2>
            <!--important-checklist-todo-->
            <div class="grid grid-cols-5 gap-2">
              <div class="border-solid p-5 m-5 px-10 bg-indigo-100 rounded-lg col-start-1 col-end-3">
                <h2 class="text-zinc-900">SELECT DATE!</h2>
                <input class="p-0.5 text-zinc-900 m-2 py-0.5 rounded-lg" type="date" v-model="mydate" /></div>
              <!--important-->
              <div class="border-solid p-5 m-5 px-10 bg-indigo-100 rounded-lg col-start-3 col-end-5">
                <h2 class="text-zinc-900">IMPORTANT!</h2>
                <input type="text" placeholder="Typing..." v-model="newImportant" class="p-0.5 text-zinc-900">
                <button @click="addImportant" class="m-2 py-0.5 text-zinc-900 bg-indigo-100 rounded-lg btn btn-primary">ADD</button>
                <br>

                <ul class="leading-2 list-none hover:list-disc pl-7 text-zinc-900">
                  <li v-for="(important, index) in importants" :key="index">
                    {{ important.note + ' ( date :  '+important.date + ' )' }}
                    <button @click="deleteImportant(important)" class="m-2 py-0.5 text-zinc-900 bg-indigo-100 rounded-lg btn btn-primary">delete</button>
                  </li>
                </ul>
              </div>

              <!--checklist-->
              <div class="border-solid float-right p-5 m-5 px-10 bg-sky-100 rounded-lg bottom-0 right-0 col-start-1 col-end-3" >
                <h2 class="text-zinc-900">CHECKLIST</h2>
                <input type="text" placeholder="Typing..." v-model="newChecklist" class="p-0.5 text-zinc-900"/>
                <button @click="addChecklist" class="m-2 py-0.5 text-zinc-900 bg-indigo-100 rounded-lg btn btn-primary">ADD</button>
                <br>

                <ul class="leading-2 list-none pl-7 text-zinc-900">
                  <li v-for="(checklist, index) in checklists" :key="index">
                    <input type="checkbox" />
                    <label class="p-1">{{ checklist.note + ' ( date : ' + checklist.date + ' )' }}</label>
                    <button @click="deleteChecklist(checklist)" class="m-1 p-1 py-0.5 bg-sky-100 rounded-lg text-zinc-900" >delete</button>
                  </li>
                </ul>
              </div>
              
              <div class="border-solid p-5 m-5 px-10 bg-green-100 rounded-lg text-zinc-900 col-start-3 col-end-5">
                <h2 class="text-zinc-900">TO DO</h2>
                <input type="text" placeholder="Typing..." v-model="newTodo" class="p-0.5 text-zinc-900">
                <button @click="addTodo" class="m-2 py-0.5 text-zinc-900 bg-indigo-100 rounded-lg btn btn-primary">ADD</button>
                <br>

                <ul class="leading-2 list-none hover:list-disc pl-7 text-zinc-900">
                  <li v-for="(todo, index) in todos" :key="index">
                    {{ todo.note +' ( date : '+ todo.date +' )' }}
                    <button @click="deleteTodo(todo)" class="m-1 p-1 py-0.5 bg-green-100 rounded-lg text-zinc-900">delete</button>
                  </li>
                </ul>
              </div>
            </div>
            <!-- ข้างบนนี้คือ todo, important, checklist -->
          </div>
        </div>
      </div>
    </div>
    <!-- สุด Calendar -->

    <!-- Button select month year etc0... -->
    <div class="button-select p-5 col-start-5 col-end-8">
      <div class="grid grid-cols-8 gap-4 p-10 bg-orange-100 shadow-xl rounded-lg">

        <div class="col-start-1 col-end-3">
          <button class="btn btn-primary grid flex-grow card bg-indigo-100 rounded-lg" @click="nextMonth(-1)">Previous Month</button>
        </div>
        <div class="col-start-3 col-end-5">
          <button class="btn btn-primary grid flex-grow card bg-indigo-100 rounded-lg" @click="nextMonth(1)">Next Month</button>
        </div>

        <div class="col-start-5 col-end-7">
          <button class="btn btn-primary grid flex-grow card bg-indigo-100 rounded-lg" @click="nextYear(-1)">Previous Year</button>
        </div>
        <div class="col-start-7 col-end-9">
          <button class="btn btn-primary flex-grow card bg-indigo-100 rounded-lg">Next Year</button>
        </div>

        <h2 class="bg-zinc-400 rounded-box">Select</h2>
        <div class="col-start-1 col-end-3">
          <select id="monthList" v-model="selectedMonth" class="select select-primary w-full">
            <option disabled selected>Select Month</option>
            <option v-for="month in 12" :key="month" :value="month - 1">
              {{ new Date(currentDate.getFullYear(), month - 1, 1).toLocaleString('default', { month: 'long' }) }}
            </option>
          </select>
        </div>

        <div class="col-start-3 col-end-5">
          <select id="yearList" v-model="selectedYear" class="select select-primary w-full">
            <option selected>Select Year</option>
            <option v-for="year in Array.from({ length: new Date().getFullYear() - 1900 }, (value, index) => 1951 + index)" :value="year">
              {{ year }}
            </option>
          </select>
        </div>

        <div class="col-start-5 col-end-7">
          <button class="btn btn-primary w-full bg-indigo-100 rounded-lg " @click="selectMonth(selectedMonth); selectYear(selectedYear)">Enter</button>
        </div>

      </div>
    </div>
  </div>
  
</template>


<style>
</style>