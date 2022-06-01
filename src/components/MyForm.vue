<template>
  <form v-on:submit.prevent="formSubmit">
    <label for="firstName">First name: </label>
    <input type="text" id="firstName" name="firstName" v-model="form.firstName"><br><br>
    <label for="lastName">Last name: </label>
    <input type="text" id="lastName" name="lastName" v-model="form.lastName"><br><br>
    <label for="facilitator">Facilitator: </label>
    <input type="text" id="facilitator" name="facilitator" v-model="form.facilitator"><br><br>
    <input type="submit" value="Send form data!">
      <div v-if="formErrors.length">
        <b>Please correct the following error(s):</b>
        <ul>
          <li v-for="err in formErrors" :key="err">{{ err }}</li>
        </ul>
      </div>
  </form>
</template>

<script>

import axios from 'axios';

export default {
  name: "MyForm",
  data(){
    return {
      form: {
        firstName: '',
        lastName: '',
        facilitator: ''
      },
      facilitators: ["josh hanson", "fazil harroon", "christian hur"],
      formErrors: []
    }
  },
  methods: {
    formSubmit() {

      try {
        this.checkFacilitator(this.form.facilitator) // check facilitator
        axios.post('http://bucs601.com/submit.php', this.form)
            .then((res) => {
              alert('form submitted ' + res)

            })
            .catch((error) => {
              alert(error)
            })
      } catch(error)  {
        this.formErrors.push(error);
      }

    },
    checkFacilitator() {
      if (this.form.facilitator) {
        let validInput = false;
        for (let i = 0; i < this.facilitators.length; i++) {
          // deal with whitespace chars
          if (this.form.facilitator.toLowerCase().replace(/\s+/g, "")
              === this.facilitators[i].replace(/\s+/g, "")) {
            validInput = true;
          }
          // alert("valid facilitator: " + validInput)
        }
        if (!validInput) {
          throw "Invalid Facilitator"
        }
      } else { // catch no input in facilitator field
        throw "Must enter facilitator"
      }
    }
  }
}
</script>

<style scoped>

</style>