<template>
  <div class="mainWrapper">
    <div class="eventWrapper">
      <h1 class="eventTitle">Event</h1>
      <div class="event" v-if="showEventError">
        <p>Event Description: {{eventDescription}}</p>
        <p>Event Date: {{eventDate}}</p>
        <p>Event Country: {{eventCountry}}</p>
      </div>
      <h1 class="eventError" v-if="showEventError == false">There is no events with that parameters</h1>
    </div>

    <div class="settingsWrapper">
      <div class="dateError" v-if="showDatesError">
        <h1>You must to peek a date period</h1>
      </div>
      <div class="pickerWrapper">
        <div class="datePicker">
          <Datepicker v-model="dateBefore" :format="chooseDateBefore"></Datepicker>
          <p>Date Before is: {{ chosenDateBefore }}</p>
        </div>
        <div class="datePicker">
          <Datepicker v-model="dateAfter" :format="chooseDateAfter"></Datepicker>
          <p>Date Before is: {{ chosenDateAfter }}</p>
        </div>
      </div>

      <mySelect :options="countryOptions" @select="countryHandle"></mySelect>
      <p>Chosen Country: {{chosenCountry}}</p>

      <multipleSelect :options="tagsOptions" @select="tagsHandle"></multipleSelect>

      <button @click="getEvent">Get Event</button>
    </div>
  </div>
</template>

<script>
import Datepicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';
import mySelect from "@/components/MySelect.vue";
import multipleSelect from "@/components/MultipleSelect.vue";
import axios from "axios";
import { ref, onMounted } from 'vue'

export default {
  components:{ Datepicker, mySelect, multipleSelect},
  name: 'App',
  setup() {
    const dateBefore = ref(null)
    const chosenDateBefore = ref(null)
    const dateAfter = ref(null)
    const chosenDateAfter = ref(null)
    const countryOptions = ref([
      {label: "", value: -1}
    ])
    const tagsOptions = ref([])
    const chosenCountry = ref(null)
    const chosenTags = ref([])
    const eventDescription = ref('')
    const eventCountry = ref('')
    const eventDate = ref('')
    const eventTags = ref([])
    const showDatesError = ref(false)
    const showEventError = ref(null)

    const formatDate = (date) => {
      return [
        padTo2Digits(date.getDate()),
        padTo2Digits(date.getMonth() + 1),
        date.getFullYear(),
      ].join('-')
    }

    const padTo2Digits = (num) => {
      return num.toString().padStart(2, '0')
    }

    const chooseDateBefore = (date) => {
      chosenDateBefore.value = formatDate(date)
      showDatesError.value = false
      return chosenDateBefore.value
    }

    const chooseDateAfter = (date) => {
      chosenDateAfter.value = formatDate(date)
      showDatesError.value = false
      return chosenDateAfter.value
    }

    const countryHandle = (value) => {
      if(value == -1){
        chosenCountry.value = null
        return
      }

      chosenCountry.value = value
    }

    const selectOption = (option) => {
      if (isSelected(option)) {
        removeSelectedOption(chosenTags.value.indexOf(option))
      } else {
        chosenTags.value.push(option)
      }
    }

    const removeSelectedOption = (index) => {
      chosenTags.value.splice(index, 1)
    }

    const isSelected = (option) => {
      return chosenTags.value.indexOf(option) !== -1
    }

    const tagsHandle = (value) => {
      if(value.length === 0){
        chosenTags.value = []
      }

      value.forEach(v => {
        selectOption(v.value)
      })
    }

    const getEvent = async () => {
      try{
        if(!dateAfter.value || !dateBefore.value){
          showDatesError.value = true
          return
        }

        const event = await axios.post("http://localhost:2305/event", {
          "countryId": chosenCountry.value == null ? 0 : chosenCountry.value,
          "dateBefore": chosenDateBefore.value,
          "dateAfter": chosenDateAfter.value,
          "tags": chosenTags.value
        })

        eventDescription.value = event.data.eventDescription
        eventCountry.value = event.data.eventCountry
        eventDate.value = event.data.eventDate
        eventTags.value = event.data.eventTags

        showEventError.value = true
      }catch{
        showEventError.value = false
      }
    }

    onMounted(async () => {
      const countries = await axios.get("http://localhost:2305/country")
      countries.data.forEach(c => {
        const country = {label: c.country, value:c.countryId}
        countryOptions.value.push(country)
      })
      const tags = await axios.get("http://localhost:2305/tag")
      tags.data.forEach(c => {
        const tag = {label: c.tag, value: c.tagId}
        tagsOptions.value.push(tag)
      })
    })
    return {
      dateBefore, chosenDateBefore, dateAfter, chosenDateAfter, countryOptions, tagsOptions,
      chosenCountry, chosenTags, eventDescription, eventCountry, eventDate, eventTags,
      formatDate, padTo2Digits, chooseDateBefore, chooseDateAfter, countryHandle, selectOption,
      removeSelectedOption, isSelected, tagsHandle, getEvent, showDatesError, showEventError
    }}
}

</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes bounce {
  from {
    transform: scale(0);
  }
  to {
    transform: scale(1);
  }
}

.dateError{
  color: red;
  text-align: center;
  padding: 20px;
  position: relative;
}

.eventError{
  text-align: center;
  color: red;
  margin-top: 50px;
  padding: 20px;
}

.mainWrapper {
  width: 100%;
  height: 100vh;
  background-color: #f1f1f1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.eventWrapper {
  width: 80%;
  height: 60%;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px #333;
  overflow: hidden;
  animation: fadeIn 0.5s ease-in-out;
}

.event {
  width: 100%;
  padding: 20px;
  font-size: 40px;
}

.eventTitle {
  font-size: 2em;
  font-weight: bold;
  text-align: center;
  color: #333;
  margin-top: 25px;
  margin-bottom: 20px;
  animation: bounce 0.5s ease-in-out;
}

.settingsWrapper {
  width: 80%;
  height: 30%;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px #333;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-items: center;
  justify-content: center;
  animation: fadeIn 0.5s ease-in-out 0.5s;
}

.pickerWrapper {
  width: 100%;
  margin-top: 50px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  justify-items: center;
  margin-bottom: 20px;
}

.datePicker {
  width: 48%;
  padding: 20px;
}

.datePicker p {
  font-size: 0.8em;
  color: #333;
  margin-top: 10px;
}

mySelect, multipleSelect {
  width: 200%;
  padding: 20px;
}

p {
  font-size: 0.8em;
  color: #333;
  margin-top: 10px;
}

button {
  width: 40%;
  padding: 10px;
  background-color: #333;
  color: #fff;
  border: none;
  border-radius: 10px;
  font-size: 1em;
  font-weight: bold;
  margin-top: 20px;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}

button:hover {
  background-color: #fff;
  color: #333;
  box-shadow: 0 0 10px #333;
}

@media (max-width: 900px) {
  .mainWrapper{
    flex-direction: column;
  }

  .eventWrapper{
    height: 400px;
  }
}
</style>