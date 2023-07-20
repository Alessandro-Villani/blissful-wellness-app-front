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
import OrderCard from './components/orders/OrderCard.vue';
import SwitchBar from './components/generics/SwitchBar.vue';
import BookingCalendar from './components/bookings/BookingCalendar.vue';
import BookingCard from './components/bookings/BookingCard.vue';
import SignInForm from './components/auth/SignInForm.vue';
import ChatFrame from './components/chat/ChatFrame.vue';

import ProfileOverview from './components/profile/ProfileOverview.vue';
export default {
  name: "Blissful Wellness app",
  data() {
    return {
      //MENUS
      menu: 1,
      massageMenu: 0,
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
      orders: [],
      bookings: null,
      chats: [],
      massageToEdit: {},
      massageToBook: {},
      selectedTherapist: {},
      productToEdit: {},
      productToPurchase: {},
      selectedChat: null,
      //FILTERS
      //bookings
      bookingNo: null,
      bookingsFilter: 'All',
      hideOtherBookingFilters: false,
      bookingsDateFilter: {
        isActive: false,
        date: null,
      },
      bookingUsernameFilter: '',
      bookingsPlaceFilter: null,
      bookingErrorCode: null,
      //orders
      orderNo: null,
      ordersFilter: 'All',
      hideOtherOrderFilters: false,
      ordersPickUpDateFilter: {
        isActive: false,
        date: null,
      },
      orderUsernameFilter: '',
      ordersPlaceFilter: null,
      orderErrorCode: null,
      //VISIBILITY BOOLEANS
      showBookingsAdditionalFilters: false,
      showOrdersAdditionalFilters: false,
    }
  },
  components: { AppHeader, EmployeesCard, AppJumbotron, MassagesCard, AppFooter, ProductCard, LogInForm, AdministrationPage, MassageForm, MassageManagement, TherapistsManagement, TherapistSelectForm, TherapistForm, SignInForm, ProductsManagement, ProductForm, OrderForm, OrderCard, SwitchBar, BookingCalendar, BookingCard, ChatFrame, ProfileOverview },
  methods: {
    //menus
    setMenu(i) {
      this.menu = i;
      if (i === 2) this.fetchTherapists();
      if (i === 3) {
        this.massageMenu = 0;
        this.fetchMassages();
      }
      if (i === 4) this.fetchProducts();
      if (i === 5) {
        this.administration.index = 0;
        this.administration.therapists = 0;
        this.administration.massage = 0;
        this.administration.products = 0;
      }
      if (i === 6) {
        this.fetchOrders();
        this.showOrdersAdditionalFilters = false;
        this.orderNo = null;
        this.ordersFilter = 'All';
        this.hideOtherOrderFilters = false;
        this.ordersPickUpDateFilter.isActive = false;
        this.ordersPickUpDateFilter.date = null;
        this.orderUsernameFilter = '';
        this.ordersPlaceFilter = null;
        this.orderErrorCode = null;
      }
      if (i === 7) {
        this.fetchBookings();
        this.showBookingsAdditionalFilters = false;
        this.bookingNo = null;
        this.bookingsFilter = 'All';
        this.hideOtherBookingFilters = false;
        this.bookingsDateFilter.isActive = false;
        this.bookingsDateFilter.date = null;
        this.bookingUsernameFilter = '';
        this.bookingsPlaceFilter = null;
        this.bookingErrorCode = null;
      }
      if (i === 8) {
        this.fetchChats();
        this.selectedChat = null;
      };
      if (i === 9) this.fetchUser();
    },
    setAdministration(i) {
      this.administration.index = i;
      if (i === 1 || i === 2) {
        this.fetchMassages()
      }
    },
    openMassageBooking(massage) {
      this.massageToBook = massage;
      this.massageMenu = 1;
      this.fetchTherapists();
    },

    //login - logout -signin
    logIn(user) {
      axios.post(baseApiUrl + 'login', user)
        .then(res => {
          this.therapists = [],
            this.massages = [],
            this.products = [],
            this.orders = [],
            this.bookings = [],
            this.menu = 1,
            this.massageMenu = 0,
            this.administration = {
              index: 0,
              therapists: 0,
              massage: 0,
              products: 0
            },
            this.ordersFilter = 'All',
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
      const formData = new FormData()
      formData.append('user', JSON.stringify(user.user));
      formData.append('file', user.file);
      axios.post(baseApiUrl + 'signin', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
        .then(() => {
          this.signInForm = false;
          console.log('signin-success');
        })
        .catch(e => console.log(e))
    },
    refreshUser(user) {
      this.user = user;
    },

    //fetches
    fetchTherapists() {
      console.log('fetch therapists');
      axios.get(baseApiUrl + 'therapists')
        .then(res => this.therapists = res.data)
        .catch(e => console.log(e))
    },
    fetchMassages() {
      console.log('fetch massages');
      axios.get(baseApiUrl + 'massages')
        .then(res => this.massages = res.data)
        .catch(e => console.log(e))
    },
    fetchProducts() {
      console.log('fetch products');
      axios.get(baseApiUrl + 'products')
        .then(res => this.products = res.data)
        .catch(e => console.log(e))
    },
    fetchUser() {
      console.log('fetch user');
      axios.get(baseApiUrl + 'users/' + this.user.id)
        .then(res => {
          this.user = res.data;
        })
        .catch(e => console.log(e))
    },
    fetchOrders() {
      console.log('fetch orders');
      let date = null;
      if (this.ordersPickUpDateFilter.isActive) date = this.ordersPickUpDateFilter.date;
      const params = {
        date: date,
        username: this.orderUsernameFilter,
      }
      const url = this.userRole === 'user' ? baseApiUrl + 'purchaseorders/user/' + this.user.id : baseApiUrl + 'purchaseorders'
      axios.get(url, { params: params })
        .then(res => this.orders = res.data)
        .catch(e => console.log(e))
    },
    fetchOrder() {
      this.ordersFilter = 'All';
      this.ordersPickUpDateFilter.isActive = false;
      this.ordersPickUpDateFilter.date = null;
      this.orderUsernameFilter = '';
      this.ordersPlaceFilter = null;
      axios.get(baseApiUrl + 'purchaseorders/inlist/' + this.orderNo)
        .then(res => {
          this.orders = res.data;
          this.hideOtherOrderFilters = true;
        })
        .catch(e => {
          console.log(e);
          this.orderErrorCode = e.response.status;
          if (this.orderErrorCode === 404) {
            this.orders = [];
            this.hideOtherOrderFilters = true;
          }
        })
    },
    fetchBookings() {
      console.log('fetch bookings');
      let date = null
      if (this.bookingsDateFilter.isActive) date = this.bookingsDateFilter.date;
      const params = {
        date: date,
        username: this.bookingUsernameFilter,
      }
      console.log(params.username);
      if (!this.userRoles.length) {
        axios.get(baseApiUrl + 'bookings/user/' + this.user.id, { params: params })
          .then(res => this.bookings = res.data)
          .catch(e => console.log(e))

      }
      if (this.isTherapist) {
        axios.get(baseApiUrl + 'therapists/user/' + this.user.id)
          .then(res => {
            const therapist = res.data
            axios.get(baseApiUrl + 'bookings/therapist/' + therapist.id, { params: params })
              .then(res => this.bookings = res.data)
              .catch(e => console.log(e))
          })
          .catch(e => console.log(e))

      }
      if (this.isAdmin) {
        axios.get(baseApiUrl + 'bookings', { params: params })
          .then(res => this.bookings = res.data)
          .catch(e => console.log(e))

      }
    },
    fetchBooking() {
      this.bookingsFilter = 'All';
      this.bookingsDateFilter.isActive = false;
      this.bookingsDateFilter.date = null;
      this.bookingUsernameFilter = '';
      this.bookingsPlaceFilter = null;
      axios.get(baseApiUrl + 'bookings/inlist/' + this.bookingNo)
        .then(res => {
          this.bookings = res.data;
          this.hideOtherBookingFilters = true;
        })
        .catch(e => {
          this.bookingErrorCode = e.response.status;
          if (this.bookingErrorCode === 404) {
            this.bookings = [];
            this.hideOtherBookingFilters = true;
          }
        })
    },
    fetchChats() {
      console.log('fetch chats');
      if (this.userRole === "therapist") {
        console.log(this.userRole)
        axios.get(baseApiUrl + 'therapists/user/' + this.user.id,)
          .then(res => {
            const therapist = res.data
            console.log(therapist.id);
            axios.get(baseApiUrl + 'chats/therapist/' + therapist.id)
              .then(res => this.chats = res.data)
              .catch(e => console.log(e))
          })
          .catch(e => console.log(e))
      }
      else {
        axios.get(baseApiUrl + 'chats/user/' + this.user.id)
          .then(res => this.chats = res.data)
          .catch(e => console.log(e))
      }
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
        const formData = new FormData();
        formData.append('file', massage.imageUrl)
        formData.append('massage', JSON.stringify(massage.massage))
        axios.post(baseApiUrl + 'massages/store', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })
          .then(() => {
            this.administration.massage = 0;
            this.fetchMassages();
          })
          .catch(e => console.log(e))
      } else {
        const formData = new FormData();
        formData.append('massage', JSON.stringify(massage.massage))
        if (massage.imageUrl) {
          formData.append('file', massage.imageUrl)
        }
        axios.put(baseApiUrl + 'massages/update/' + massage.id, formData)
          .then(() => {
            this.administration.massage = 0;
            this.fetchMassages();
          })
          .catch(e => console.log(e))
      }
    },
    addMassage() {
      this.fetchTherapists();
      this.administration.massage = 1;
    },
    editMassage(id) {
      axios.get(baseApiUrl + 'massages/' + id)
        .then(res => {
          this.fetchTherapists();
          this.massageToEdit = res.data;
          this.administration.massage = 2;
        })
        .catch(e => console.log(e))
    },

    // add or edit product
    product(product) {
      console.log(product);
      if (!product.isEdit) {
        const formData = new FormData();
        formData.append('file', product.imageUrl)
        formData.append('product', JSON.stringify(product.product))
        axios.post(baseApiUrl + 'products/store', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })
          .then(() => {
            this.administration.products = 0;
            this.fetchProducts;
          })
          .catch(e => console.log(e))
      } else {
        const formData = new FormData();
        formData.append('product', JSON.stringify(product.product))
        if (product.imageUrl) {
          formData.append('file', product.imageUrl)
        }
        axios.put(baseApiUrl + 'products/update/' + product.id, formData)
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
          this.fetchOrders();
          this.setMenu(6);
        })
        .catch(e => console.log(e))
    },
    refreshOrders() {
      this.fetchOrders();
    },
    toggleOrdersAdditionalFilters() {
      this.showOrdersAdditionalFilters = !this.showOrdersAdditionalFilters;
    },
    // bookings
    sentBooking() {
      this.setMenu(7);
    },
    toggleBookingsAdditionalFilters() {
      this.showBookingsAdditionalFilters = !this.showBookingsAdditionalFilters;
    },
    //FILTERS
    setOrdersFilter(filter) {
      this.refreshOrders();
      this.ordersFilter = filter;
    },
    setBookingsFilter(filter) {
      this.fetchBookings();
      this.bookingsFilter = filter;
    },
    resetBookingNoFilter() {
      this.fetchBookings();
      this.bookingNo = null;
      this.hideOtherBookingFilters = false;
    },
    setPlaceBookingFilter(filter) {
      this.fetchBookings();
      this.bookingsPlaceFilter = this.bookingsPlaceFilter === filter ? null : filter;
    },
    setPlaceOrderFilter(filter) {
      this.fetchOrders();
      this.ordersPlaceFilter = this.ordersPlaceFilter === filter ? null : filter;
      if (this.orderPlaceFilter !== 'Pick up') this.ordersPickUpDateFilter.isActive = false;
    },
    resetOrderNoFilter() {
      this.fetchOrders();
      this.orderNo = null;
      this.hideOtherOrderFilters = false;
    },
    //CHATS
    goToChats(chatId) {
      this.bookingsFilter = 'All';
      this.setMenu(8);
      this.selectedChat = chatId
    },
    selectChat(id) {
      this.selectedChat = id;
    }
  },
  computed: {
    //ROLES AND MENU
    userRoles() {
      if (this.user.roles) {
        return this.user.roles.map(role => role.name);
      } else return null;
    },
    isAdmin() {
      if (this.userRoles) return this.userRoles.includes("admin");
      return null
    },
    isTherapist() {
      if (this.userRoles) return this.userRoles.includes("therapist");
      return null
    },
    userRole() {
      if (this.userRoles && !this.userRoles.length) return "user";
      if (this.isAdmin) return "admin";
      if (this.isTherapist) return "therapist";
    },
    mainClasses() {
      let classes = '';
      if (this.menu != 1) classes += 'no-jumbo';
      if (this.isLogged) classes += ' logged';
      return classes;
    },
    //PURCHASE ORDERS
    noOrders() {
      const noOrders = !this.user.orders.filter(order => !order.canceled).length && !this.orders.length;
      return noOrders;
    },
    filteredOrdersByPlace() {
      if (!this.orders) this.fetchOrders();
      let orders = this.orders;
      switch (this.ordersPlaceFilter) {
        case 'Delivery':
          orders = orders.filter(order => order.delivery);
          return orders;
        case 'Pick up':
          orders = orders.filter(order => !order.delivery)
          return orders;
        default:
          return orders;
      }
    },
    filteredOrders() {
      let orders = this.filteredOrdersByPlace;
      switch (this.ordersFilter) {
        case 'Pending':
          orders = orders.filter(order => !order.accepted && !order.rejected && !order.canceled);
          return orders;
        case 'Accepted':
          orders = orders.filter(order => order.accepted && !order.delivered && !order.canceled);
          return orders;
        case 'Declined':
          orders = orders.filter(order => order.rejected);
          return orders;
        case 'Archived':
          orders = orders.filter(order => order.delivered);
          return orders;
        case 'Canceled':
          orders = orders.filter(order => order.canceled);
          return orders;
        default:
          return orders;
      }
    },
    filteredUserOrders() {
      let orders = this.filteredOrdersByPlace.filter(order => !order.canceled);
      switch (this.ordersFilter) {
        case 'Pending':
          orders = orders.filter(order => !order.accepted && !order.rejected);
          return orders;
        case 'Accepted':
          orders = orders.filter(order => order.accepted && !order.delivered);
          return orders;
        case 'Declined':
          orders = orders.filter(order => order.rejected);
          return orders;
        case 'Delivered':
          orders = orders.filter(order => order.delivered);
          return orders;
        default:
          return orders;
      }
    },
    //BOOKINGS
    filteredBookingsByPlace() {
      if (!this.bookings) this.fetchBookings()
      let bookings = this.bookings;
      switch (this.bookingsPlaceFilter) {
        case 'Home Service':
          console.log('home');
          bookings = bookings.filter(booking => booking.homeService);
          return bookings;
        case 'In Spa':
          console.log('spa');
          bookings = bookings.filter(booking => !booking.homeService);
          return bookings;
        default:
          return bookings;
      }
    },
    filteredBookings() {
      let bookings = this.filteredBookingsByPlace;
      switch (this.bookingsFilter) {
        case 'Pending':
          bookings = bookings.filter(booking => !booking.accepted && !booking.rejected);
          return bookings;
        case 'Accepted':
          bookings = bookings.filter(booking => booking.accepted && !booking.completed);
          return bookings;
        case 'Declined':
          bookings = bookings.filter(booking => booking.rejected);
          return bookings;
        case 'Completed':
          bookings = bookings.filter(booking => booking.completed);
          return bookings;
        default:
          return bookings;
      }
    },
    //DATES
    today() {
      const today = new Date();
      const day = today.getDate() < 10 ? '0' + today.getDate() : today.getDate();
      const month = today.getMonth() + 1 < 10 ? '0' + (today.getMonth() + 1) : today.getMonth + 1;
      const year = today.getFullYear();
      return year + '-' + month + '-' + day;
    }
  },
  mounted() {
    this.bookingsDateFilter.date = this.today;
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
        :userId="isLogged ? user.id : null" :isLogged="isLogged" />
    </section>

    <!-- MASSAGES -->
    <section v-if="menu === 3" class="massages pt-5">
      <div v-if="massageMenu === 0" class="massage-list">
        <h1 class="text-center mb-5">OUR MASSAGES</h1>
        <MassagesCard v-for="(massage, i) in massages" :key="massage.id" :index="i" :massage="massage"
          :userRole="userRole" @book="openMassageBooking" />
      </div>
      <BookingCalendar v-if="massageMenu === 1" :massage="massageToBook" :therapists="therapists" :user="user"
        @booking-sent="sentBooking()" />
    </section>

    <!-- PRODUCTS -->
    <section v-if="menu === 4" class="products pt-5">
      <h1 class="text-center mb-5">OUR PRODUCTS</h1>
      <ProductCard v-for="product in products" :product="product" :userRole="userRole" @order-form="openOrderForm" />
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
        @back="administration.index = 0" @add-massage="addMassage" @edit-massage="editMassage" @refresh="fetchMassages" />
      <MassageForm v-if="administration.massage === 1 || administration.massage === 2"
        :isEdit="administration.massage === 2 ? true : false"
        :existingMassage="administration.massage === 2 ? massageToEdit : null" :therapists="therapists"
        @back="administration.massage = 0" @massage="massage" />

      <!-- PRODUCTS MANAGEMENT -->
      <ProductsManagement v-if="administration.index === 3 && administration.products === 0"
        @back="administration.index = 0" @add-product="administration.products = 1" @edit-product="editProduct" />
      <ProductForm v-if="administration.products === 1 || administration.products === 2"
        @back="administration.products = 0" :formType="administration.products"
        :existingProduct="administration.products === 2 ? productToEdit : null" @product="product" />
    </section>

    <!-- ORDERS -->
    <section v-if="menu === 6" class="purchase-orders d-flex flex-column  pt-5">
      <!-- USER ORDERS -->
      <SwitchBar class="align-self-center" v-if="!noOrders"
        :menus="!isAdmin ? ['All', 'Pending', 'Accepted', 'Declined', 'Delivered'] : ['All', 'Pending', 'Accepted', 'Declined', 'Archived', 'Canceled']"
        :selectedMenu="ordersFilter" @switch="setOrdersFilter" />
      <p class="text-center mb-1" v-if="!noOrders && !hideOtherOrderFilters">Additional Filters</p>
      <!-- ADDITIONAL FILTERS -->
      <div class="additional-filters d-flex flex-column" :class="showOrdersAdditionalFilters ? 'open' : ''">
        <div class="col-6 align-self-center">
          <SwitchBar class="w-auto" v-if="!noOrders && !hideOtherOrderFilters" :menus="['Delivery', 'Pick up']"
            :selectedMenu="ordersPlaceFilter" @switch="setPlaceOrderFilter" />
        </div>
        <div class="filters d-flex justify-content-center align-items-center mb-3">
          <div class="date-filter d-flex flex-column align-items-center"
            v-if="((!noOrders || ordersPickUpDateFilter.isActive) && ordersPlaceFilter === 'Pick up') && !hideOtherOrderFilters">
            <label for="booking-date" class="mb-1">Pick up date</label>
            <input class="mb-2" id="booking-date" type="date" v-model="ordersPickUpDateFilter.date"
              @change="ordersPickUpDateFilter.isActive ? fetchOrders() : null">
            <div class="date-filter-checkbox d-flex">
              <input class="me-1" type="checkbox" id="apply-bookings-filter" v-model="ordersPickUpDateFilter.isActive"
                @change="fetchOrders()">
              <label for="apply-bookings-filter">Apply filter</label>
            </div>
          </div>
          <div v-if="userRole != 'user' && (!noOrders || orderErrorCode === 404)"
            class="d-flex flex-column align-items-center justify-content-center"
            :class="hideOtherOrderFilters || (!hideOtherOrderFilters && ordersPlaceFilter !== 'Pick up') ? '' : 'ms-3'">
            <label for="booking-number" class="mb-1">Order number</label>
            <input type="number" id="booking-number" class="mb-2" v-model="orderNo">
            <div class="buttons d-flex justify-content-center">
              <button class="btn btn-primary me-2" @click="fetchOrder()"><i
                  class="fa-solid fa-magnifying-glass"></i></button>
              <button class="btn btn-danger" @click="resetOrderNoFilter()"><i class="fa-solid fa-ban"></i></button>
            </div>
          </div>
        </div>
        <div class="col-6 align-self-center text-center mb-3" v-if="userRole != 'user' && !hideOtherOrderFilters">
          <label for="username">Username</label>
          <input type="text" id="username" v-model="orderUsernameFilter" @keyup="fetchOrders()">
        </div>
      </div>
      <i v-if="orders.length" class="fa-solid fa-chevron-down text-center mb-3"
        :class="showOrdersAdditionalFilters ? 'rotated' : ''" @click="toggleOrdersAdditionalFilters()"></i>
      <!-- NO ORDERS -->
      <div v-if="noOrders" class="text-center">
        <h2 class="mb-5">No Orders</h2>
        <button v-if="!isAdmin && !ordersPickUpDateFilter.isActive" class="btn btn-primary" @click="setMenu(4)">Check our
          products</button>
      </div>
      <div v-if="!isAdmin && !noOrders" class="customer row">
        <OrderCard v-for="order in filteredUserOrders" :purchaseOrder="order" :forAdmin="isAdmin"
          @order-handled="refreshOrders" />
      </div>
      <div v-if="isAdmin && !noOrders" class="customer row">
        <OrderCard v-for="order in filteredOrders" :purchaseOrder="order" :forAdmin="isAdmin"
          @order-handled="refreshOrders" />
      </div>
    </section>

    <!-- BOOKINGS -->
    <section v-if="menu === 7" class="bookings d-flex flex-column pt-5 ">
      <SwitchBar class="align-self-center" v-if="bookings.length && !hideOtherBookingFilters"
        :menus="['All', 'Pending', 'Accepted', 'Declined', 'Completed']" :selectedMenu="bookingsFilter"
        @switch="setBookingsFilter" />
      <p class="text-center mb-1" v-if="bookings.length && !hideOtherBookingFilters">Additional Filters</p>
      <!-- ADDITIONAL FILTERS -->
      <div class="additional-filters d-flex flex-column" :class="showBookingsAdditionalFilters ? 'open' : ''">
        <div class="col-6 align-self-center">
          <SwitchBar class="w-auto" v-if="bookings.length && !hideOtherBookingFilters" :menus="['In Spa', 'Home Service']"
            :selectedMenu="bookingsPlaceFilter" @switch="setPlaceBookingFilter" />
        </div>
        <div class="filters d-flex justify-content-center align-items-center mb-3">
          <div class="date-filter d-flex flex-column align-items-center"
            v-if="(bookings.length || bookingsDateFilter.isActive) && !hideOtherBookingFilters">
            <label for="booking-date" class="mb-1">Booking date</label>
            <input class="mb-2" id="booking-date" type="date" v-model="bookingsDateFilter.date"
              @change="bookingsDateFilter.isActive ? fetchBookings() : null">
            <div class="date-filter-checkbox d-flex">
              <input class="me-1" type="checkbox" id="apply-bookings-filter" v-model="bookingsDateFilter.isActive"
                @change="fetchBookings()">
              <label for="apply-bookings-filter">Apply filter</label>
            </div>
          </div>
          <div v-if="userRole != 'user' && (bookings.length || bookingErrorCode === 404)"
            class="d-flex flex-column align-items-center justify-content-center"
            :class="hideOtherBookingFilters ? '' : 'ms-3'">
            <label for="booking-number" class="mb-1">Booking number</label>
            <input type="number" id="booking-number" class="mb-2" v-model="bookingNo">
            <div class="buttons d-flex justify-content-center">
              <button class="btn btn-primary me-2" @click="fetchBooking()"><i
                  class="fa-solid fa-magnifying-glass"></i></button>
              <button class="btn btn-danger" @click="resetBookingNoFilter()"><i class="fa-solid fa-ban"></i></button>
            </div>
          </div>
        </div>
        <div class="col-6 align-self-center text-center mb-3" v-if="userRole != 'user' && !hideOtherBookingFilters">
          <label for="username">Username</label>
          <input type="text" id="username" v-model="bookingUsernameFilter" @keyup="fetchBookings()">
        </div>
      </div>
      <i v-if="bookings.length && !hideOtherBookingFilters" class="fa-solid fa-chevron-down text-center mb-3"
        :class="showBookingsAdditionalFilters ? 'rotated' : ''" @click="toggleBookingsAdditionalFilters()"></i>
      <h2 class="text-center mb-5" v-if="!bookings.length">No Bookings</h2>
      <BookingCard v-for="booking in filteredBookings" :booking="booking" :key="booking.id" :userRole="userRole"
        @booking-handled="fetchBookings()" @review-store="fetchBookings" @chat="goToChats" />
    </section>

    <!-- CHATS -->
    <section v-if="menu === 8" class="chats d-flex flex-column pt-5">
      <ChatFrame v-for="chat in chats" :key="chat.id" :chat="chat" :userRole="userRole" :selectedChat="selectedChat"
        @open="selectChat" @message-sent="fetchChats" />
    </section>

    <!-- PROFILE -->
    <section v-if="menu === 9" class="profile d-flex flex-column pt-5">
      <ProfileOverview :user="user" :userRole="userRole" @change-pic="refreshUser" />
    </section>
  </main>

  <!-- FOOTER -->
  <AppFooter :isLogged="isLogged" :userRole="userRole" :menuState="menu" @menu="setMenu" />

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

  .additional-filters {
    max-height: 0;
    overflow-y: hidden;
    transition: max-height 1s;

    &.open {
      max-height: 1000px;
      overflow-y: auto;
    }

  }

  .fa-chevron-down {
    transition: all 0.5s;

    &.rotated {
      transform: rotate(180deg);
    }
  }

  .date-filter-checkbox {

    padding-top: 0.375rem;
    padding-bottom: 0.375rem;

  }

  #booking-number {
    width: 145px;
    height: 28px;
  }

}
</style>
