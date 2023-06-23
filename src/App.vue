<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

import AppFooter from './components/AppFooter.vue';
import AppHeader from './components/AppHeader.vue';
import AppJumbotron from './components/AppJumbotron.vue';
import AdministrationPage from './components/administration/AdministrationPage.vue';
import MassageForm from './components/administration/massages/MassageForm.vue';
import MassageManagement from './components/administration/massages/MassageManagement.vue';
import LogInForm from './components/auth/LogInForm.vue';
import TherapistsManagement from './components/administration/therapists/TherapistsManagement.vue';
import EmployeesCard from './components/employees/EmployeesCard.vue';
import MassagesCard from './components/massages/MassagesCard.vue';
import ProductCard from './components/products/ProductCard.vue';
import TherapistForm from './components/administration/therapists/TherapistForm.vue';
import TherapistSelectForm from './components/administration/therapists/TherapistSelectForm.vue';
import ProductsManagement from './components/administration/products/ProductsManagement.vue';
import ProductForm from './components/administration/products/ProductForm.vue';
import OrderForm from './components/orders/OrderForm.vue';

import SignInForm from './components/auth/SignInForm.vue';
export default {
  name: "Blissful Wellness app",
  data() {
    return {
      //MENUS
      menu: 1,
      administration: {
        index: 0,
        therapists: 0,
        massage: 0,
        products: 0
      },
      isLogged: false,
      logInForm: false,
      signInForm: false,
      orderForm: false,
      //DATAS
      therapists: [],
      massages: [],
      products: [],
      user: {},
      massageToEdit: {},
      selectedTherapist: {},
      productToEdit: {},
      productToPurchase: {}
    }
  },
  components: { AppHeader, EmployeesCard, AppJumbotron, MassagesCard, AppFooter, ProductCard, LogInForm, AdministrationPage, MassageForm, MassageManagement, TherapistsManagement, TherapistSelectForm, TherapistForm, SignInForm, ProductsManagement, ProductForm, OrderForm },
  methods: {
    //menus
    setMenu(i) {
      this.menu = i;
      if (i === 2) this.fetchTherapists();
      if (i === 3) this.fetchMassages();
      if (i === 4) this.fetchProducts();
      if (i === 5) {
        this.administration.index = 0;
        this.administration.therapists = 0;
        this.administration.massage = 0;
        this.administration.products = 0;
      }
    },
    setAdministration(i) {
      this.administration.index = i;
      if (i === 1 || i === 2) {
        this.fetchMassages()
      }
    },

    //login - logout -signin
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
      this.user = {};
      this.menu = 1;
    },
    signIn(user) {
      axios.post(baseApiUrl + 'signin', user)
        .then(() => {
          this.signInForm = false;
          console.log('signin-success');
        })
        .catch(e => console.log(e))
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
    fetchProducts() {
      axios.get(baseApiUrl + 'products')
        .then(res => this.products = res.data)
        .catch(e => console.log(e))
    },
    //edit annd update therapist
    editTherapist(therapist) {
      this.selectedTherapist = therapist;
      this.administration.therapists = 2;
    },
    updateTherapist(therapist) {
      console.log(therapist);
      axios.put(baseApiUrl + 'therapists/update/' + therapist.userId, therapist)
        .then(() => {
          this.administration.therapists = 0;
          this.fetchTherapists();
        })
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
    },

    // add or edit product
    product(product) {
      if (!product.isEdit) {
        axios.post(baseApiUrl + 'products/store', product.product)
          .then(() => {
            this.administration.products = 0;
            this.fetchProducts;
          })
          .catch(e => console.log(e))
      } else {
        axios.put(baseApiUrl + 'products/update/' + product.id, product.product)
          .then(() => {
            this.administration.products = 0;
            this.fetchProducts();
          })
          .catch(e => console.log(e))
      }
    },
    editProduct(id) {
      axios.get(baseApiUrl + 'products/' + id)
        .then(res => {
          this.productToEdit = res.data;
          this.administration.products = 2;
        })
        .catch(e => console.log(e))
    },

    // purchase orders
    openOrderForm(product) {
      this.productToPurchase = product;
      this.orderForm = true;
    },
    emitOrder(order) {
      axios.post(baseApiUrl + 'purchaseorders/store', order)
        .then(() => {
          console.log('order successfull')
          this.orderForm = false;
          this.fetchProducts();
        })
        .catch(e => console.log(e))
    }
  },
  computed: {
    userRoles() {
      if (this.user.roles) {
        return this.user.roles.map(role => role.name);
      } else return null;
    },
    mainClasses() {
      let classes = '';
      if (this.menu != 1) classes += 'no-jumbo';
      if (this.isLogged) classes += ' logged';
      return classes;
    }

  }
}
</script>

<template>
  <!-- HEADER -->
  <AppHeader :isLogged="isLogged" :roles="userRoles" :menuState="menu" :user="user" @menu="setMenu"
    @login-form="logInForm = true" @signin-form="signInForm = true" @logout="logOut" />

  <!-- JUMBOTRON -->
  <AppJumbotron v-if="menu === 1" />
  <main class="container" :class="mainClasses">

    <!-- HOME -->
    <section v-if="menu === 1" class="home text-center pt-5">
      <h1 class="mb-3"><span v-if="isLogged">HI {{ user.username.toUpperCase() }},<br></span> WELCOME TO BLISSFUL
        WELLNESS
        APP</h1>
      <p class="mb-5">Lorem ipsum, dolor sit amet consectetur adipisicing elit. Distinctio id possimus eum dicta porro,
        impedit iure in
        vitae. Sunt eum minima dolor nisi culpa, quibusdam error illum dolore dolores enim.</p>
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
      <ProductCard v-for="product in products" :product="product" @order-form="openOrderForm" />
    </section>

    <!-- ADMINISTRATION -->
    <section v-if="menu === 5" class="administration pt-5">
      <h1 v-if="administration === 0" class="text-center mb-5">ADMINISTRATE YOUR APP</h1>

      <AdministrationPage v-if="administration.index === 0" @administration="setAdministration" />

      <!-- THERAPIST MANAGEMENT -->
      <TherapistsManagement v-if="administration.index === 1 && administration.therapists === 0"
        @back="administration.index = 0" @add-therapist="administration.therapists = 1" @edit-therapist="editTherapist" />
      <TherapistSelectForm v-if="administration.therapists === 1" @back="administration.therapists = 0"
        :massages="massages" />
      <TherapistForm v-if="administration.therapists === 2" :user="selectedTherapist" :formState="2"
        @back="administration.therapists = 0" @therapist="updateTherapist" :massages="massages" />

      <!-- MASSAGE MANAGEMENT -->
      <MassageManagement v-if="administration.index === 2 && administration.massage === 0" :massages="massages"
        @back="administration.index = 0" @add-massage="administration.massage = 1" @edit-massage="editMassage"
        @refresh="fetchMassages" />
      <MassageForm v-if="administration.massage === 1 || administration.massage === 2"
        :isEdit="administration.massage === 2 ? true : false"
        :existingMassage="administration.massage === 2 ? massageToEdit : null" @back="administration.massage = 0"
        @massage="massage" />

      <!-- PRODUCTS MANAGEMENT -->
      <ProductsManagement v-if="administration.index === 3 && administration.products === 0"
        @back="administration.index = 0" @add-product="administration.products = 1" @edit-product="editProduct" />
      <ProductForm v-if="administration.products === 1 || administration.products === 2"
        @back="administration.products = 0" :formType="administration.products"
        :existingProduct="administration.products === 2 ? productToEdit : null" @product="product" />
    </section>
  </main>

  <!-- FOOTER -->
  <AppFooter :isLogged="isLogged" :roles="userRoles" :menuState="menu" @menu="setMenu" />

  <!-- FORMS -->
  <LogInForm v-if="logInForm" @login="logIn" @login-close="logInForm = false" />
  <SignInForm v-if="signInForm" @signin="signIn" @signin-close="signInForm = false" />
  <OrderForm v-if="orderForm" :user-id="user.id" :product="productToPurchase" @order-form-close="orderForm = false"
    @emit-order="emitOrder" />
</template>

<style lang="scss">
@use "./assets/styles/style.scss";

main {
  min-height: calc(100vh - 335px);

  &.logged {
    min-height: calc(100vh - 390px);
  }

  &.no-jumbo {
    min-height: calc(100vh - 155px);

    &.logged {
      min-height: calc(100vh - 190px);
    }
  }
}
</style>
