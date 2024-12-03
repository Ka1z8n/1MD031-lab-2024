<template>

<div class="header-part">
        <h1>Welcome to McBurgers Online</h1>
        <div class="header-image">
            <img id="header-image" src="@/assets/images/restaurants_happy.jpg"  alt="Delicious Burger">
        </div>
    </div>


    <section id="burger-list">
        <section>
        <h2>Select your McBurger choice:</h2>
        <p>Our Most Popular Menus:</p>
    </section>
    <div class="wrapper">
        <Burger v-for="burger in burgers"
        v-bind:burger="burger" 
        v-bind:key="burger.name"
        v-on:orderedBurger="addToOrder($event)"/>
    </div>
</section>
   
 <section id="order-info">
    <h2>
        Customer information
    </h2>
    <p>
        This is where you provide necessary information
    </p>
    <h4>
        Delivery information:
    </h4>
    <p>
        <label for="fullname">Full name</label><br>
        <input type="text" id="firstandlastname" v-model ="customerName"  placeholder="First and Last name"> {{ customerName }}
    </p>
    <p>
        <label for="E-mail">E-mail</label><br>
        <input type="email" id="email" v-model ="email"  placeholder="E-mail address">  {{ email }}
    </p>
    <p>
        <legend>Please Select Payment Method:</legend>
        <select v-model="paymentMethod" id="payment_method" required>
            <option disabled value="">Select your payment method</option>""
            <option value="cash">Cash</option>
            <option value="debit_card">Debit Card</option>
            <option value="credit_card">Credit Card</option>
            <option value="google_pay">Google Pay</option>
            <option value="applepay">Apple pay</option>
            <option value="paypal">PayPal</option>
        </select>
   </p>
    <div>
        <p>
            Please indicate point of delivery:
        </p>
    <div class="map-container">
        <div id="map" v-on:click="setLocation" style="position: relative;">
            <div v-bind:style="{ 
                position: 'absolute', 
                left: location.x + 'px', 
                top: location.y + 'px', 
                width: '10px', 
                height: '10px', 
                background: 'red'
                }">
                T
                </div>
                </div>
</div> 
</div>
    <p>
        <legend>Please Select Gender:</legend>
        <input type="radio" id="male" v-model="chosen" name="gender" value="male"> 

        <label for="male">Male</label><br>
   
        <input type="radio" id="female" v-model="chosen"  name= "gender" value="female">
        <label for="female">Female</label><br>
   
        <input type="radio" id="other" v-model="chosen" name= "gender" value="other">
        <label for="other">Other</label><br>
        <button type="submit">Submit</button>
    </p>    
    <button type="submit" v-on:click="addOrder">
        <img src="@/assets/images/tick-sign.png" style="width: 20px"  >
        Place my order please!
      </button>
    </section>
    <hr/>
    &copy; <span>2024 McBurgers Co-Inc.</span>
    <a href="http://Mcburgers.com">McBurgers.com</a>
</template>


<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, imageUrl, kCal, hasGluten, hasLactose){
  this.name = name ;
  this.kCal = kCal;
  this.imageUrl = imageUrl;
  this.hasGluten= hasGluten;
  this.hasLactose = hasLactose;
}

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu, 
      customerName: "",
      email: "",
      paymentMethod: "",
      chosen: "",
      orderedBurgers:{},
      location: { x: 0,
            y: 0
          },
       currentOrderNumber:1

    }
  },
  methods: {
    addToOrder: function (event) {
        this.orderedBurgers[event.name] = event.amount;
        console.log("orderedBurger", this.orderedBurgers);
    },
    //getOrderNumber: function () {return Math.floor(Math.random() * 100000);},
    getOrderNumber: function(){
        const orderNumber = this.currentOrderNumber;
        this.currentOrderNumber+=1;
        return orderNumber;
    },
    setLocation(event) {
        this.location = {
            x: event.clientX - 10 - event.currentTarget.getBoundingClientRect().left,
            y: event.clientY - 10 - event.currentTarget.getBoundingClientRect().top,
        };
        console.log("Location set:", this.location);
    },
    submitOrder: function () {
        console.log("Customer Name: " + this.customerName);
        console.log("Email: " + this.email);
        console.log("Payment Method: " + this.paymentMethod);
        console.log("Gender: " + this.chosen);
        console.log("Location:", this.location);
    },
    addOrder: function () {
        const orderNumber = this.getOrderNumber();
       console.log("Order Sent:" ,//controllerar bug
        {
            orderId: orderNumber,
            details: {
                x: this.location.x,
                y: this.location.y,
                name: this.customerName,
                email: this.email,
                payment: this.paymentMethod,
                gender: this.chosen,
            },
            orderItems: this.orderedBurgers,
        });

        socket.emit("addOrder", {
            orderId: orderNumber,
            details: {
                x: this.location.x,
                y: this.location.y,
                name: this.customerName,
                payment: this.paymentMethod,
                email: this.email,
                gender: this.chosen,
            },
            orderItems: this.orderedBurgers,
        });
    },
},
};


</script>

<style>
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    background-color: red;
    background-size: cover;
  }
  .map-container {
  overflow: scroll;
  margin-bottom: 20px;
}

  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

body {
    font-size: 12pt;
    font-family:'Times New Roman', Times, serif;
}



h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
}
main, header, footer, nav ul {
    max-width: 40rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: bisque;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */
 /* Your comment goes here */

header {
    background-image: url("../img/polacks.jpg");
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 200px;
    opacity: 0.5;
}

header h1 {
    width:40rem;
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
/* Started the lab here */
.ingredient {
    font-weight: bold;
    color: white;
}
#burger-list {
    background-color: black;
    color:white;
    padding: 20px;
    border: 2px dashed white;
    border-radius: 5px;
}
button:hover {
    background-color: blue; 
    cursor: pointer; 
    color: #ffcc00;
}
.burger-item {
    margin: 20px; 
    padding: 10px; 
    border-radius: 5px; 
}
#order-info{
    border:2px dashed black;
    padding: 20px;
    margin-top: 10px;
}
.header-part {
    margin: 0 15px;
    position: relative; 
    
}
.header-part h1 {
    position: absolute; /* The choice of pic plays a huge role */
    top: 70px;
    left: 100px; 
    color: black;
    z-index: 1; 
}
.header-image img {
    width: 100%; 
    height: auto; 
    border-radius: 5px; 
    opacity: 0.5;
}
.wrapper {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: repeat(3, 1fr); /* Change to 3 equal columns */
    background-color: black;
    color: #444;
}
.burger-card {
    background-color: black;
    color: #fff;
    border-radius: 5px;
    padding: 20px;
    font-size: 150%;
}

</style>