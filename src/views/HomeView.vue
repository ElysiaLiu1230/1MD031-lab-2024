<template>
  <div>

    <body>

      <header>
        <h1>Welcome to BurgerHeaven Online</h1>
        <img src="/img/polacks.jpg" alt="header image">
      </header>

      <main>

        <section id="burgers">
          <h2>Select burger</h2>
          <p>This is where you can select your burger.</p>

          <div class="burger-wrapper">
            <Burger v-for="(burger, index) in burgersArray" :key="index" :burger="burger"
              @orderedBurger="updateOrder($event)" />
          </div>

        </section>

        <section id="order">
          <h2>Customer information</h2>
          <p>This is where you provide necessary information.</p>

          <form @submit.prevent>
            <p>
              <label for="fullname">Full name</label><br>
              <input v-model="fullName" type="text" id="fullname" placeholder="First and Last name">
            </p>

            <p>
              <label for="email">E-mail</label><br>
              <input v-model="email" type="email" id="email" placeholder="E-mail address">
            </p>

            <!-- <p>
              <label for="street">Street</label><br>
              <input v-model="street" type="text" id="street" placeholder="Street name">
            </p>

            <p>
              <label for="house">House number</label><br>
              <input v-model="houseNumber" type="number" id="house" placeholder="House number" min="1" step="1">
            </p> -->

            <p>
              <label for="payment">Payment options</label><br>
              <select v-model="payment" id="payment">
                <option value="credit_card">Credit card</option>
                <option value="apple_pay">Apple Pay</option>
                <option value="swish">Swish</option>
                <option value="klarna">Klarna</option>
              </select>
            </p>

            <p>
              <span>Gender</span><br>

              <input v-model="gender" type="radio" id="male" value="male">
              <label for="male">Male</label><br>

              <input v-model="gender" type="radio" id="female" value="female">
              <label for="female">Female</label><br>

              <input v-model="gender" type="radio" id="noinfo" value="noinfo">
              <label for="noinfo">Do not wish to provide</label><br>
            </p>

            <div id="mapWrapper">
              <div id="map" @click="setLocation"></div>
            </div>

            <button type="button" @click="addOrder">
              Place my order!
            </button>
          </form>
        </section>

      </main>

      <hr>

      <footer>
        © 2018 Hypothetical Burgers Inc.
      </footer>

    </body>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io('localhost:3000')

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data() {
    return {
      burgersArray: menu,

      fullName: "",
      email: "",
      street: "",
      houseNumber: "",
      payment: "credit_card",
      gender: "noinfo",

      orderedBurgers: {},
      location: { x: 0, y: 0 }
    }
  },
  methods: {

    getOrderNumber() {
      return Math.floor(Math.random() * 100000)
    },

    updateOrder(event) {
      this.orderedBurgers[event.name] = event.amount
      console.log("Ordered burgers:", this.orderedBurgers)
    },

    setLocation(event) {
      const offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      }

      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      }

      console.log("Location selected:", this.location)
    },

    addOrder() {
      const orderItems = Object.keys(this.orderedBurgers)
        .filter(name => this.orderedBurgers[name] > 0)

      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: this.location.x,
          y: this.location.y
        },
        orderItems: orderItems,
        customer: {
          name: this.fullName,
          email: this.email,
          payment: this.payment,
          gender: this.gender
        }
      })

      console.log("Order sent:", orderItems)
    }




  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

body {
  font-size: 12pt;
  font-family: arial;
}

p {
  color: red;
}

h1 {
  font-family: 'Agbalumo';
  font-size: 36pt;
}

main,
header,
footer,
nav ul {
  max-width: 40rem;
  margin: 0 auto 0 auto;
}

main {
  background-color: bisque;
}

header {
  background-image: url("../img/polacks.jpg");
  background-size: cover;
  overflow: hidden;
  width: 100%;
  height: 200px;
  opacity: 0.5;
}

header h1 {
  width: 40rem;
  margin: 0 auto;
  text-align: center;
}

nav ul {
  display: grid;
  grid-template-columns: repeat(auto-fill, 9.25em);
  gap: 1em;
  padding: 0;
}

nav li {
  display: block;
  background-color: grey;
  padding: 1em;
}

.Very-good {
  color: green;
}

.Master {
  color: green;
  font-weight: bold;
}

.allergy {
  font-weight: bold;
}

button:hover {
  background-color: #ffcc00;
  cursor: pointer;
}

button {
  padding: 10px 20px;
  margin-top: 20px;
}

section {
  margin: 10px;
  border: 2px dashed black;
  padding: 10px;
}

#burgers {
  background-color: black;
  color: white;
  border: 2px dashed bisque;
  padding: 20px;
}

.burger-wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  padding: 20px;
}

@media screen and (max-width: 800px) {
  h1 {
    font-size: 6vw;
  }
}

#burgers {
  border: 2px dashed white;
  padding: 1em 2em;
}

.burger-wrapper {
  display: grid;
  justify-content: space-around;
  grid-template-columns: repeat(3, 1fr);
  gap: 2em;
}

.burger-wrapper h3 {
  text-align: left;
  grid-column: span 3;
  font-size: small;
}

.burger h3 {
  margin-bottom: 0.5em;
}

#map {
  width: 1920px;
  height: 1078px;
  background: url("/img/polacks.jpg");
  overflow: scroll;
}

#mapWrapper {
  width: 600px;
  height: 300px;
  overflow: scroll;
}
</style>
