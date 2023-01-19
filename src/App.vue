<template>
  <div class="mainWrapper">
    <div class="eventWrapper">
      <div class="event" v-if="eventDescription">
        <h1 class="eventTitle">Event</h1>

        <p>Event Description: {{eventDescription}}</p>
        <p>Event Date: {{eventDate}}</p>
        <p>Event Country: {{eventCountry}}</p>
      </div>
    </div>

    <div class="settingsWrapper">
      <div class="countryPickerWrapper">
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
    const countryOptions = ref([])
    const tagsOptions = ref([])
    const chosenCountry = ref(null)
    const chosenTags = ref([])
    const eventDescription = ref('')
    const eventCountry = ref('')
    const eventDate = ref('')
    const eventTags = ref([])

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
      return chosenDateBefore.value
    }

    const chooseDateAfter = (date) => {
      chosenDateAfter.value = formatDate(date)
      return chosenDateAfter.value
    }

    const countryHandle = (value) => {
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
      dateBefore, chosenDateBefore, dateAfter, chosenDateAfter, countryOptions, tagsOptions, chosenCountry, chosenTags, eventDescription, eventCountry, eventDate, eventTags,
      formatDate, padTo2Digits, chooseDateBefore, chooseDateAfter, countryHandle, selectOption, removeSelectedOption, isSelected, tagsHandle, getEvent
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

/* Keyframes for bouncing in the eventTitle */
@keyframes bounce {
  from {
    transform: scale(0);
  }
  to {
    transform: scale(1);
  }
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
}

.eventTitle {
  font-size: 2em;
  font-weight: bold;
  text-align: center;
  color: #333;
  margin-bottom: 20px;
  animation: bounce 0.5s ease-in-out;
}

.settingsWrapper {
  width: 80%;
  height: 40%;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px #333;
  overflow: hidden;
  animation: fadeIn 0.5s ease-in-out 0.5s;
}

.countryPickerWrapper {
  width: 100%;
  display: flex;
  justify-content: space-between;
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
  width: 100%;
  padding: 20px;
}

p {
  font-size: 0.8em;
  color: #333;
  margin-top: 10px;
}

button {
  width: 100%;
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
</style>