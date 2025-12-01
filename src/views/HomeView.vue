<template>
    <header>
        <img src="/img/Banner.png" class="headline" id="headlineImage">
        <h1 class="headline" id="headlineText">Hello time for borger</h1>
    </header>
    <main>
        <section id="burgers">
            <h3>Pick burge</h3>
            <p>wow look at bogr</p>
            <div class="wrapper">
              <Burger
                v-for="burger in burgers"
                v-bind:key="burger.name"
                v-bind:burger="burger"
                v-on:orderedBurger="addToOrder($event)"
              />
            </div>
        </section>
        <section id="customerinfo">
            <form>
                <h3>So tell me about yourself</h3>
                <h4>Where do you live</h4>
                <p>
                    <label for="name">Full name</label><br>
                    <input type="text" id="name" v-model="fullName" required="required" placeholder="First- and last name">
                </p>
                <p>
                    <label for="email">Email</label><br>
                    <input type="text" id="email" v-model="email" required="required" placeholder="Email adress">
                </p>
                <p>
                    Gender<br>
                    <input type="radio" id="male" v-model="gender" required="required" value="Male">
                    <label for="male">Male</label><br>
                    <input type="radio" id="female" v-model="gender" required="required" value="Female">
                    <label for="female">Female</label><br>
                    <input type="radio" id="nonbinary" v-model="gender" required="required" value="Non-Binary">
                    <label for="nonbinary">Non-Binary</label><br>
                    <input type="radio" id="nosay" v-model="gender" required="required" value="Rather not say">
                    <label for="nosay">Rather not say</label><br>
                </p>
                <p>
                Point of delivery:
                <div id="mapContainer">
                  <div id="map" v-on:click="setLocation">
                    <div id="marker" v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">T</div>
                  </div>
                </div>
                </p>
                <p>
                    <label for="paymethod">How to pay?</label><br>
                    <select id="paymethod" v-model="paymentMethod">
                        <option>Credit card</option>
                        <option>Swish</option>
                        <option>Paypal</option>
                        <option>I'm gonna steal it</option>
                    </select>
                </p>
            </form>
            <button type="submit" v-on:click="submitOrder">
                <img src="/img/Checkmark.png" style="width: 12px">
                Place Order
            </button>
        </section>
    </main>
    <hr>
    <footer>
        © dont steal borgir 2025
    </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, kCal, imagesrc, gluten, lactose, extraLines, isGeorge) {
  this.name = name;
  this.kCal = kCal;
  this.src = imagesrc;
  this.containsGluten = gluten;
  this.containsLactose = lactose;
  this.extraLines = extraLines;
  this.george = isGeorge;
}

const menuArray = [
  new MenuItem("Silly Burger", 500, "/img/SillyBurger.png", false, false, "Can't take him anywhere!", false),
  new MenuItem("Scary Burger", 2800, "/img/ScaryBurger.png", true, true, "Contains cyanide", false),
  new MenuItem("George", "George", "/img/George.png", false, false, "George", true)
]

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      fullName: '',
      email: '',
      gender: '',
      paymentMethod: 'Credit card',
      orderedBurgers: {},
      location: { x: 0,
                  y: 0
                }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      
    },
    setLocation: function(event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x = event.clientX - offset.x - 10;
      this.location.y = event.clientY - offset.y - 10;
    },
    submitOrder: function() {
      console.log('order placed');
      console.log(this.orderedBurgers);

      const personalDetailsString = `${this.fullName}, ${this.email}, ${this.gender}, ${this.paymentMethod}`;

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: this.location,
                                orderItems: this.orderedBurgers,
                                personalDetails: personalDetailsString
                              }
                 );
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    }
  }
}
</script>

<style>
body {
    font-family: 'times-new-roman';
    font-size: 12pt;
}

.headline {
    margin: 5px;
    height: 400px;
    overflow: hidden;
}

#headlineText {
    font-family: 'times-new-roman';
    font-size: 50pt;
    position: absolute;
    margin-top: -200px;
    padding-top: 50px;
    padding-left: 390px;
}

#headlineImage {
    opacity: 0.2;
    width: 99%;
    height: 200px;
}

.ingredient {
    font-weight: bold;
    color: white;
}

#burgers {
    margin: 5px;
    padding: 5px;
    background-color: black;
    color: white;
    border: 3px double white;
}

#burgers ul {
    color: lightgray;
}

#customerinfo {
    margin: 5px;
}

#customerinfo form {
    padding: 5px;
    border: 3px double black;
}

button:hover {
    background-color: green;
}

button {
    margin: 5px;
}

.wrapper {
    display: grid;
    grid-gap: 20px;
    grid-template-columns: 200px 200px 200px;
}

.wrapper img {
    width: 200px;
    height: 200px;
}

.wrapper h4 {
    text-align: center;
}

#mapContainer {
  margin: 30px;
  height: 500px;
  border: 2px solid black;
  overflow: scroll;
  position: relative;
}

#map {
  background: url(/img/polacks.jpg);
  width: 1920px;
  height: 1078px;
  position: relative;
}

#marker {
  position: absolute;
  border-radius: 50%;
  background-color: black;
  color: white;
  width: 20px;
  height: 20px;
  line-height: 20px;
  text-align: center;
  font-weight: bold;
  font-size: 14px;
}

</style>