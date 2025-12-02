<template>
  <div>
    <header>
      <section id="logo">
        <h1>CheeseBurgerOnline</h1>
      </section>
    </header>

    <main> 
      <section id="burgers"> 
        <h1>Welcome to the CheeseBurgerOnline</h1>
        <h2>Our Selection of cheeseburgers</h2>

        <p> How <span class="emphasis">cheesy</span> are you feeling today?</p>

        <div class="burgerswrapper"> 
          <div class ="burgers">
              <Burger
                v-for="burger in burgers"
                :key="burger.name"
                :burger="burger"
                @orderedBurger="addToOrder"
              />
          </div>
        </div>
      </section>

        <section id="contact"> 
          <h2>Customer information</h2>
          <h3>Delivery information:</h3>

          <p>
            <label for="fullname"><span class="question">Fullname</span></label><br>
            <input type="text" id="fullname" v-model="fullname" required="required" placeholder="First- and last name">
          </p>

          <p>
            <label for="email"><span class="question">E-mail</span></label><br>
            <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
          </p>

          <!--<p>
            <label for="street">  <span class="question">Street</span></label><br>
            <input type="text" v-model="street" name="st" required="required" placeholder="Street name">
          </p>

          <p>
            <label for="house">  <span class="question">House</span></label><br>
            <input type="number" v-model="house" name="hn" required="required" placeholder="House number">
          </p> -->

          <label for="position"> <span class="question">Where do you want your cheese delivered?</span></label><br>

          <div class="mapwrapper"> 
            <div id="map" @click="setLocation">
              <img src="/img/polacks.jpg">
          
              <div
              v-for="(marker, index) in markers"
              :key="index"
              class="marker"
              :style="{ left: marker.x + 'px', top: marker.y + 'px' }"
              >
                T
              </div>
            </div>
          </div>


          <p>
            <label for="paymentmethod"> <span class="question">How would you like to pay?</span></label><br>
            <select id="paymentmethod" v-model="paymentmethod" name="pm">
              <option>Credit/Debit Card</option>
              <option>PayPal</option>
              <option>Apple Pay</option>
              <option>Klarna</option>
            </select>
          </p>

          <p> 
            <span class="question">Gender?</span><br>
            <label>
              <input type="radio" name="gender" value="Male" v-model="gender"> 
              <span class="answer">Male</span></label><br>
            <label>
              <input type="radio" name="gender" value="Female" v-model="gender">
              <span class="answer">Female</span></label><br>
            <label>
              <input type="radio" name="gender" value="Non-binary" v-model="gender">
              <span class="answer">Non-binary</span></label><br>
            <label>
              <input type="radio" name="gender" value="Undisclosed"checked v-model="gender">
              <span class="answer">Undisclosed</span></label><br>
          </p>
        </section>

        <button id="place-order" type="button" v-on:click="placeOrder">
            <img src="/img/order now button.png">
            Order now!
        </button>
    </main>
    <footer>
        <p style="font-style: italic;">&copy; 2024 CheeseBurger Inc.</p>
    </footer>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

//function MenuItem(name, cheese, url, lactose, gluten) {
  //this.name = name
  //this.cheese= cheese
  //this.url = url
  //this.lactose = lactose
  //this.gluten = gluten
//}

//const menuItems = [
  //new MenuItem('Cheeseburger', 'good level cheese', '/img/Cheeseburger.jpeg', true, true),
  //new MenuItem('Double Cheeseburger', 'great level cheese', '/img/Double Cheeseburger.jpeg', true, true),
  //new MenuItem('Tripple Cheeseburger', 'fantastic level cheese', '/img/Tripple Cheeseburger.jpeg', true, true),
  //];

export default {
  name: 'HomeView',
  components: {
    Burger
  },
   data () {
    return {
      burgers: menu, // <-- härifrån kommer alla burgers nu
      fullname: "",
      email: '',
      paymentmethod: '',
      gender: '',
      orderedBurgers: {},
      markers: [{ x: 0, y: 0 }],
      location: {x: 0, y: 0}
    }
  },

  //data: function () {
    //return {
      //burgers: menuItems
    //}
  //},

  methods: {
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log("Ordered burgers:", this.orderedBurgers);
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    setLocation: function (event) {
      /*var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};*/
      const rect = event.currentTarget.getBoundingClientRect();
      const x = event.clientX - 10 - rect.left;
      const y = event.clientY - 10 - rect.top;

      this.location.x = x;
      this.location.y = y;

      this.markers = [{ x: x, y: y }];

      console.log("Clicked location:", this.location);
    }, 

    placeOrder: function () {
    console.log("Name:", this.fullname);
    console.log("Email:", this.email);;
    console.log("Payment:", this.paymentmethod);
    console.log("Gender:", this.gender);
    console.log("Burgers ordered:", this.orderedBurgers);
    console.log("Delivery location:", this.location);

    socket.emit("addOrder", { 
                                orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y },
                                orderItems: this.orderedBurgers,
                                customerInfo: {
                                  name: this.fullname,
                                  email: this.email,
                                  paymentmethod: this.paymentmethod,
                                  gender:this.gender}
                              });
                             
                      

    console.log("Order form data:", {
    fullname: this.fullname,
    email: this.email,
    paymentmethod: this.paymentmethod,
    gender: this.gender
    });
   }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-family: "Georgia", serif;
    font-size: 12pt;
}

p {
    color: black;
}

h1 {
    font-weight: bold;
    font-size: 36pt;
    text-transform: uppercase;
}
main, header, footer, nav ul {
    max-width: 70rem;
    margin: 0 auto 0 auto;
}
main {
    padding: 1rem;
    width: 100%;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
    margin: 20px auto 0;
    position: relative; /* för att kunna positionera loggan inuti header */
    height: 220px;
    display: flex; /* för att centrera texten */
    justify-content: center;  /* centrera horisontellt */
    align-items: center;      /* centrera vertikalt */
    text-align: center;       /* centrera text inne i h1 */
}

header::before { /* för att lägga ett mörkt lager över bilden = bättre kontrast, och ::before används för att inte få detta overlay lager över texten också. */
    content: "";
    position: absolute;
    background-image: url("/img/cheese-illustration.jpg");
    background-size: cover; /* ändrat från contain till cover för att bilden ska täcka hela header */
    background-position: center 30%; /* ändrat från center center, dvs att övre delen av bild syns */
    overflow: hidden; /* för att dölja överflödig del av bilden */
    width:100%;
    height:100%;
    opacity: 0.75; /* gör lagret halvtransparent */
    z-index: 1; /* läggs bakom texten (z=order i InDesign) */
}

header h1 {
    position: relative; /* för att texten ska ligga över bilden */
    z-index: 2; /* läggs över overlay lagret */
    margin: 0 auto;
    text-transform: none;
    font-size: 42pt;
    color:  rgb(122, 63, 27);
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


.allergi{
    font-weight: bold;
    color: #ff5500;
    font-style: italic;
    text-transform: uppercase;
}

.question{ /* fråga för forms*/
    font-weight: bold;
    color: rgb(247, 240, 217);
    font-style: italic;
}

.answer{ /* fråga för forms*/
    color: rgb(247, 240, 217);
}

#burgers {
    background-color: rgb(247, 240, 217);
    color:rgb(122, 63, 27);
    padding: 2rem;
    margin: 20px auto;
    justify-content: center;
    border: 3px dashed rgb(122, 63, 27);
}

.burgerswrapper{
    display: flex;
    gap: 4rem;
    flex-wrap: wrap;
    justify-content: center;
}

.burgers img {
    width: 200px;
    height: 220px;
    object-fit: cover;
    display: block;
    margin: 0 auto;
}

#burgers h1,
#burgers h2,
#burgers h3 {
    text-align: center;
    letter-spacing: 0.015em;
    font-family: georgia;
}
#burgers p:first-of-type {
    text-align: center;
    font-size: 1.2rem;
     color:rgb(122, 63, 27);
}

.burgers {
     display: grid;
     grid-gap: 2.5%;
     grid-template-columns: 32% 32% 32%;
     justify-content: center;
 }

 .box{
    border-radius: 5px;
    padding: 20px;
    margin-top: 30px;
    margin-bottom: 30px;
    border: 3px solid rgb(122, 63, 27);
 }

#contact {
    background-color:rgb(122, 63, 27);
    color:rgb(247, 240, 217);
    padding: 2rem;
    margin: 20px auto;
    border: 3px dashed rgb(247, 240, 217);
}

#place-order {
    background-color:#ff5500;
    color:rgb(247, 240, 217);
    border: none;
    font-weight: bold;
    font-size: 1.2rem;
    border-radius: 0.5rem; 
    padding: 10px;
    margin: 0px; /*looked better without margin*/
}

#place-order img {
    width: 50px;    /* prova 60px, 80px, 100px osv */
    height: auto;
}

#place-order:hover {
    background-color:rgb(122, 63, 27);
    color:rgb(247, 240, 217);
    padding: 10px;
    margin: 0px; /*looked better without margin*/

}

.emphasis {
    font-style: italic;
    font-weight: bold;
    color: #ff5500;
}

@media screen and (max-width: 800px) {
    h1 {
        font-size: 6vw;
    }
  }
 /* testade bara att lägga in denna..., förstår inte riktigt hur funkar lol sorry */
  #map {
    position: relative;
    width: 1920px;
    height: 1078px;
  }

  .mapwrapper{
    width: 1050px;
    height: 600px;
    overflow: scroll;
  }

  .marker {
  position: absolute;
  font-weight: bold;
  color: #ff5500;
  width: 20px;
  height: 20px;
  text-align: center;
  line-height: 20px;
  }

</style>