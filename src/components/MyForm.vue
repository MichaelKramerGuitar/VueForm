<template>
  <form v-on:submit.prevent="formSubmit">
    <label for="firstName">First name: </label>
    <input type="text" id="firstName" name="firstName" v-model="form.firstName"><br><br>
    <label for="lastName">Last name: </label>
    <input type="text" id="lastName" name="lastName" v-model="form.lastName"><br><br>
    <label for="facilitator">Facilitator: </label>
    <input type="text" id="facilitator" name="facilitator" v-model="form.facilitator"><br><br>
    <fieldset>
      <legend>Select Term:</legend>
      <label for="term1">Summer 1, 2022</label>
      <input type="radio" id ="term1" v-model="form.term" value="Summer 1, 2022"><br>
      <label for="term2">Summer 2, 2022</label>
      <input type="radio" id ="term2" v-model="form.term" value="Summer 2, 2022"><br>
      <label for="term3">Fall 1, 2022</label>
      <input type="radio" id ="term3" v-model="form.term" value="Fall 1, 2022"><br>
      <label for="term3">Fall 2, 2022</label>
      <input type="radio" id ="term4" v-model="form.term" value="Fall 2, 2022"><br>
      <span v-if="form.term">Selected Term: {{ form.term }}</span>
    </fieldset>
    <fieldset>
      <legend>Select Program:</legend>
      <label for="degree1">Master of Computer Science</label>
      <input type="checkbox" name="degree1" id="degree1" v-model="form.program" value="MSCS"><br>
      <label for="degree2">Master of Software Development</label>
      <input type="checkbox" name="degree2" id="degree2" v-model="form.program" value="MSSD"><br>
      <label for="degree3">Master of Data Science</label>
      <input type="checkbox" name="degree3"  id="degree3" v-model="form.program" value="MSDS"><br>
      <div v-if="form.program.length">
        <b>Selected Program(s):</b>
        <ol>
          <li v-for="prog in form.program" :key="prog">{{ prog }}</li>
        </ol>
      </div>
    </fieldset>

    <input type="submit" value="Send form data!">
      <div class="bar" v-if="formErrors.length">
        <b>Please correct the following error(s):</b>
        <ol>
          <li class="bar error" v-for="err in formErrors" :key="err">{{ err }}</li>
        </ol>
      </div>
  </form>
</template>

<script>

import axios from 'axios';
//https://vuelidate-next.netlify.app/#installation

export default {
  name: "MyForm",
  data(){
    return {
      form: {
        firstName: '',
        lastName: '',
        facilitator: '',
        term: '',
        program: []
      },
      facilitators: ["Josh Hanson", "Fazil Harroon", "Christian Hur"],
      formErrors: []
    }
  },
  methods: {
    formSubmit() {
      try {
        if(this.form.firstName){ // first name check
          let element = document.getElementById('firstName')
          this.checkName(this.form.firstName, element)
        } else {
          throw "First Name field is a required."
        }
        if (this.form.lastName) { // check last name
          let element = document.getElementById('lastName')
          this.checkName(this.form.lastName, element)
        } else {
          throw "Last Name field is required."
        }
        if (this.form.facilitator) { // check facilitator
          this.checkFacilitator(this.form.facilitator)
        } else {
          throw "Facilitator field is required."
        }
      } catch(error)  {
        this.formErrors.push(error);
      }
      if (!this.formErrors.length) { // if no errors in the form
        let url = 'https://bucs601.com/submit.php'
        axios.post(url, this.form)
            .then((res) => {
              alert('form submitted ' + res)
              //window.location = url
            })
            .catch((error) => {
              alert(error)
            })
      }
    },
    checkFacilitator() {
      let validInput = false;
      let element = document.getElementById('facilitator')
      for (let i = 0; i < this.facilitators.length; i++) {
        // deal with whitespace chars, initially had array as facilitators first and last like "Josh Hanson" etc.
        if (this.form.facilitator.toLowerCase().replace(/\s+/g, "")
            === this.facilitators[i].toLowerCase().replace(/\s+/g, "")) {
          validInput = true;
        }
      }
      if (!validInput) {
        //throw "Invalid Facilitator"
        this.formErrors.push("Invalid Facilitator")
        this.highlightBad(element)
      } else {
        this.highlightGood(element)
      }
    },
    checkName(name, element) {
      if(!(/^[a-zA-Z]{2,}$/.test(name))){
        this.formErrors.push(
            "Invalid Name - Name Rules: i. minimum length of two (2) characters ii. only alpha chars (a-zA-Z)")
        this.highlightBad(element)
      } else {
        this.highlightGood(element)
      }
    },
    highlightBad(element) {
      element.style.border = "2px solid red";
    },
    highlightGood(element) {
      element.style.border = "2px solid green";
    }
  }
}
</script>

<style scoped>
  @import '../assets/styles/style.css';
</style>