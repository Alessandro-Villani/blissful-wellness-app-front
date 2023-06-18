<script>
import AppFooter from './components/AppFooter.vue';
import AppHeader from './components/AppHeader.vue';
import AppJumbotron from './components/AppJumbotron.vue';
import AdministrationPage from './components/administration/AdministrationPage.vue';
import MassageForm from './components/administration/massages/MassageForm.vue';
import MassageManagement from './components/administration/massages/MassageManagement.vue';
import LogInForm from './components/auth/LogInForm.vue';
import EmployeesCard from './components/employees/EmployeesCard.vue';
import MassagesCard from './components/massages/MassagesCard.vue';
import ProductCard from './components/products/ProductCard.vue';
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

export default {
  name: "Blissful Wellness app",
  data() {
    return {
      menu: 1,
      administration: {
        index: 0,
        massage: 0,
      },
      massageToEdit: {},
      isLogged: false,
      logInForm: false,
      username: '',
      user: {},
      therapists: [],
      massages: []
    }
  },
  components: { AppHeader, EmployeesCard, AppJumbotron, MassagesCard, AppFooter, ProductCard, LogInForm, AdministrationPage, MassageForm, MassageManagement },
  methods: {
    setMenu(i) {
      this.menu = i;
      if (i === 2) this.fetchTherapists();
      if (i === 3) this.fetchMassages();
      if (i === 5) {
        this.administration.index = 0;
        this.administration.massage = 0;
      }
    },
    setAdministration(i) {
      this.administration.index = i;
      if (i === 2) {
        this.fetchMassages()
      }
    },
    //login - logout
    logIn(user) {
      console.log(user);
      axios.post(baseApiUrl + 'login', user)
        .then(res => {
          this.user = res.data;
          this.isLogged = true;
        })
        .catch(e => console.log(e))
    },
    logOut() {
      this.isLogged = false;
      this.user = {}
    },
    //fetches
    fetchTherapists() {
      axios.get(baseApiUrl + 'therapists')
        .then(res => this.therapists = res.data)
        .catch(e => console.log(e))
    },
    fetchMassages() {
      axios.get(baseApiUrl + 'massages')
        .then(res => this.massages = res.data)
        .catch(e => console.log(e))
    },
    // add or edit massage
    massage(massage) {
      console.log(massage);
      if (!massage.isEdit) {
        axios.post(baseApiUrl + 'massages/store', massage.massage)
          .then(() => {
            this.administration.massage = 0;
            this.fetchMassages();
          })
          .catch(e => console.log(e))
      } else {
        axios.put(baseApiUrl + 'massages/update/' + massage.id, massage.massage)
          .then(() => {
            this.administration.massage = 0;
            this.fetchMassages();
          })
          .catch(e => console.log(e))
      }
    },
    editMassage(id) {
      axios.get(baseApiUrl + 'massages/' + id)
        .then(res => {
          this.massageToEdit = res.data;
          this.administration.massage = 2;
        })
        .catch(e => console.log(e))
    }

  }
}
</script>

<template>
  <!-- HEADER -->
  <AppHeader @menu="setMenu" :isLogged="isLogged" />

  <!-- JUMBOTRON -->
  <AppJumbotron />
  <main class="container">

    <!-- HOME -->
    <section v-if="menu === 1" class="home text-center pt-5">
      <h1 class="mb-3"><span v-if="isLogged">HI {{ user.username.toUpperCase() }},<br></span> WELCOME TO BLISSFUL
        WELLNESS
        APP</h1>
      <p class="mb-5">Lorem ipsum, dolor sit amet consectetur adipisicing elit. Distinctio id possimus eum dicta porro,
        impedit iure in
        vitae. Sunt eum minima dolor nisi culpa, quibusdam error illum dolore dolores enim.</p>
      <div class="buttons">
        <button v-if="!isLogged" class="btn btn-primary me-2" @click="logInForm = true">Login</button>
        <button v-if="!isLogged" class="btn btn-secondary">Sign in</button>
        <button v-if="isLogged" class="btn btn-secondary" @click="logOut">Log out</button>
      </div>
    </section>

    <!-- THERAPISTS -->
    <section v-if="menu === 2" class="therapists pt-5">
      <h1 class="text-center mb-5">OUR THERAPISTS</h1>
      <EmployeesCard v-for="(therapist, i) in therapists" :key="therapist.id" :index="i" :therapist="therapist"
        :userId="isLogged ? user.id : null" :isLogged="isLogged" @review-store="fetchTherapists" />
    </section>

    <!-- MASSAGES -->
    <section v-if="menu === 3" class="massages pt-5">
      <h1 class="text-center mb-5">OUR MASSAGES</h1>
      <MassagesCard v-for="(massage, i) in massages" :key="massage.id" :index="i" :massage="massage" />
    </section>

    <!-- PRODUCTS -->
    <section v-if="menu === 4" class="products pt-5">
      <h1 class="text-center mb-5">OUR PRODUCTS</h1>
      <ProductCard v-for="i in 5" />
    </section>

    <!-- ADMINISTRATION -->
    <section v-if="menu === 5" class="administration pt-5">
      <h1 v-if="administration === 0" class="text-center mb-5">ADMINISTRATE YOUR APP</h1>
      <AdministrationPage v-if="administration.index === 0" @administration="setAdministration" />
      <MassageManagement v-if="administration.index === 2 && administration.massage === 0" :massages="massages"
        @back="administration.index = 0" @add-massage="administration.massage = 1" @edit-massage="editMassage"
        @refresh="fetchMassages" />
      <MassageForm v-if="administration.massage === 1 || administration.massage === 2"
        :isEdit="administration.massage === 2 ? true : false"
        :existingMassage="administration.massage === 2 ? massageToEdit : null" @back="administration.massage = 0"
        @massage="massage" />
    </section>
  </main>

  <!-- FOOTER -->
  <AppFooter />

  <!-- LOG IN FORM -->
  <LogInForm v-if="logInForm" @login="logIn" @login-close="logInForm = false" />
</template>

<style lang="scss">
@use "./assets/styles/style.scss";

main {
  min-height: calc(100vh - 335px);
}
</style>
