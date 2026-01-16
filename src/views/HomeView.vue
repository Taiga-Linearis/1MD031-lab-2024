<template>
  <div>
    <header>
      <h1>Welcome to BurgerHeaven Online</h1>

      <h3>Select burger</h3>
      <p>This is where you execute burger selection</p>
    </header>

    <div class="burger-section">
      <div v-for="(burger, idx) in burgers" :key="burger.name" class="burger-card">
        <Burger ref="burger" :burger="burger" />
      </div>
    </div>

    <main>
      <section class="contact">
        <h4>Customer Information</h4>
        <p>This is where you provide necessary information</p>
        <h4>Delivery Information:</h4>
        <form @submit.prevent>
          <p>
            <label for="fullName">Full name</label><br>
            <input type="text" id="fullName" v-model="fullName" required placeholder="Full name">
            {{ fullName }}
          </p>
          <p>
            <label for="email">E-mail</label><br>
            <input type="email" id="email" v-model="email" placeholder="E-mail">
            {{ email }}
          </p>

          <p>
            <label for="payment">Payment options</label>
            <select id="payment" v-model="payment" name="payment">
              <option>Debit card</option>
              <option>Credit card</option>
              <option>Klarna</option>
              <option>Apple pay</option>
            </select>
            {{ payment }}
          </p>

          <p>
            <input type="radio" id="gender-male" value="Male" v-model="gender" required>
            <label for="gender-male">Male</label><br>

            <input type="radio" id="gender-female" value="Female" v-model="gender" required>
            <label for="gender-female">Female</label><br>

            <input type="radio" id="gender-nb" value="Non-binary" v-model="gender" required>
            <label for="gender-nb">Non-binary</label><br>

            <input type="radio" id="gender-other" value="Other" v-model="gender" required>
            <label for="gender-other">Other</label><br>

            {{ gender }}
          </p>

        </form>
      </section><br>

      <div class="map-wrapper">
        <div id="map" @click="setLocation">
          <div
            class="target"
            v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }"
          >T</div>
        </div>
      </div>

      <button type="button" v-on:click="submitOrder">Submit Order
        <img src="/img/send_orders.jpg" alt="Burger Icon" width="50" height="20">
      </button>
    </main>

    <hr>
    <footer>
      &copy; 2018 Hypothetical Burgers Inc.
    </footer>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("http://localhost:3000");


function MenuItem(name, img, kCal, { gluten = false, lactose = false } = {}) {
    this.name = name;
    this.img = img;      
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
}


const burgers = [
  new MenuItem('The Fire Burger', '/img/fire_burger.jpg', 510, { gluten: true, lactose: true }),
  new MenuItem('Fried Turkey Burger', '/img/turkey_burger.jpg', 420, { gluten: false, lactose: true }),
  new MenuItem('A Double w/ Cheese', '/img/dobule_cheese.jpg', 300, { gluten: true, lactose: true })
];

export default {
  name: 'Home',
  components: {
    Burger
  },
  data() {
    return {
      burgers: menu,

      fullName: '',
      email: '',
      payment: 'Debit card',
      gender: '',

      OrderedBurgers: {},
      location: {
        x: 0,
        y: 0
      }
    };
  },
  methods: {
    getOrderNumber() {
      return Math.floor(Math.random()*100000);
    },

    
    setLocation(event) {
      var rect = event.currentTarget.getBoundingClientRect();
      const x = event.clientX - 10 - rect.left;
      const y = event.clientY - 10 - rect.top;

      this.location.x = x;
      this.location.y = y;
    },

    submitOrder() {

      const ordered = {};
      const refs = this.$refs.burger || [];

      refs.forEach(comp => {
        if (comp && comp.amountOrdered && comp.amountOrdered > 0) {
          ordered[comp.burger.name] = comp.amountOrdered;
        }
      });

      this.OrderedBurgers = ordered;

      console.log({
        fullName: this.fullName,
        email: this.email,
        payment: this.payment,
        gender: this.gender
      });

      console.log('Ordered burgers:', this.OrderedBurgers);

      const customer = {
        fullName: this.fullName,
        email: this.email,
        payment: this.payment,
        gender: this.gender
      };
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y },
        orderItems: ordered,     
        customer: customer   
      });

      console.log('Emitted order:', {
        orderId: 'sent',
        details: { x: this.location.x, y: this.location.y },
        orderItems: ordered,
        customer: customer
      });
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-family: 'Times New Roman', Times, serif;
}

header {
    background-image: url('/img/Tavern.jpg');
    opacity: 0.5;
    color: white;
    padding: 10px;
    text-align: center;
    font-family: 'Cormorant', serif;
}


.burger-section {
    background-color: black;
    color:white;
}

.burger-card {
    display: inline-block;
    margin: 10px;
    vertical-align: top;
}


.lactose {
    color: darkorange;
    font-weight: bold;
}

button:hover {
    background-color: darkgrey;
    color: black;
}

button:active {
    background-color: grey;
    color: white;
}

section {
    margin: 20px;
    padding: 20px;
    color: black;
    border: 2px dashed black;
    border-radius: 10px;
    background-color: #f2f2f2;
}

.map-wrapper {
  width: 85vw;
  max-width: 100%;
  height: 500px;
  overflow: auto;
  border: 2px solid #333;
  margin: 16px 0;
}


#map {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  width: auto;
  min-width: 1920px;
  height: 1078px;
  cursor: crosshair;
  background-image: url('/img/polacks.jpg');
  background-size: auto; 
}

.target {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
  line-height: 20px;
  font-weight: bold;
  user-select: none;
}
</style>