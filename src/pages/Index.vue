<template>
  <q-page class="flex flex-center" style="overflow: hidden;" @mousemove="doDrag" >
    <div :style="{ 'transition': transitionActiveStyle }" class="allSquares" id="redSquare" @mousedown="startDrag">
      <span>
        <button type="button" @click="decideJoke">Play with me!</button>
        <q-input type="number" v-model="inputID" label="Enter Number" />
      </span>

    </div>
    <div :style="{ 'transition': transitionActiveStyle }" class="allSquares" id="yellowSquare" @mousedown="startDrag">
      <span>
        {{ yellowJoke }}
      </span>
    </div>
    <div :style="{ 'transition': transitionActiveStyle }" class="allSquares" id="blueSquare" @mousedown="startDrag">
      <span>
        {{ blueJoke }}
      </span>
    </div>
    <div :style="{ 'transition': transitionActiveStyle }" class="allSquares" id="greenSquare" @mousedown="startDrag">
      <span>
        {{ greenJoke }}
      </span>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageIndex',
  data() {
    return {
      allreadyHeardThem: [],
      countToThree: 0,
      yellowJoke: "",
      blueJoke: "",
      greenJoke: "",
      inputID: "",
      numberOfJokes: 0,
      startDragX: 0,
      startDragY: 0,
      windowHeight: 0,
      windowWidth: 0,
      layout: ["redSquare", "yellowSquare", "blueSquare", "greenSquare"],
      transitionActiveStyle: "unset",
      windowIsSmaller: false,
      selectedBox: ""
    };
  },
  mounted() {
    // Innitial load
    // We check for the number of jokes, we get 3 random jokes and save the widow size
    this.checkLength();
    this.getJoke();

    this.windowHeight = window.innerHeight;
    this.windowWidth = window.innerWidth;

    // And add event listeners for resizing the window and mouse button up
    window.addEventListener('mouseup', this.stopDrag);
    window.addEventListener('resize', this.onResize);
  },
  methods: {
    onResize() {
      // We save new window size
      this.windowHeight = window.innerHeight;
      this.windowWidth = window.innerWidth;

      // And if needed we rearange the squares
      if(this.windowWidth >=700 && this.windowIsSmaller == false){
        this.windowIsSmaller = true;
        document.getElementById(this.layout[0]).style.top = (0) + 'px';
        document.getElementById(this.layout[0]).style.left = (0) + 'px';

        document.getElementById(this.layout[1]).style.top = (0) + 'px';
        document.getElementById(this.layout[1]).style.left= (this.windowWidth/2) + 'px';

        document.getElementById(this.layout[2]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[2]).style.left = (0) + 'px';

        document.getElementById(this.layout[3]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[3]).style.left= (this.windowWidth/2) + 'px';
      } else if (this.windowWidth < 700 && this.windowIsSmaller == true){
        this.windowIsSmaller = false;
        document.getElementById(this.layout[0]).style.top = (0) + 'px';
        document.getElementById(this.layout[0]).style.left = (0) + 'px';

        document.getElementById(this.layout[1]).style.top = (this.windowHeight/4) + 'px';
        document.getElementById(this.layout[1]).style.left = (0) + 'px';

        document.getElementById(this.layout[2]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[2]).style.left = (0) + 'px';

        document.getElementById(this.layout[3]).style.top = (3*this.windowHeight/4) + 'px';
        document.getElementById(this.layout[3]).style.left = (0) + 'px';
      
      }
    },
    startDrag(event) {
      // On mouse down we start dragging, we save it because we dont want things to happen just by moving the mouse
      this.dragging = true;
      // we save the point wher the mouse is clickes
      this.startDragX = event.offsetX;
      this.startDragY = event.offsetY;
      this.selectedBox = event.toElement.id;

      // Stop all css transitions because we dont want the element to lag behinde the mouse
      this.transitionActiveStyle = "unset ";

      // and find out witch box have we clicked and bring it to the front
      switch (event.toElement.id) {
          case "redSquare":
            document.getElementById("redSquare").style.zIndex = 10;
            document.getElementById("yellowSquare").style.zIndex = 0;
            document.getElementById("blueSquare").style.zIndex = 0;
            document.getElementById("greenSquare").style.zIndex = 0;
            break;
          case "yellowSquare":
            document.getElementById("redSquare").style.zIndex = 0;
            document.getElementById("yellowSquare").style.zIndex = 10;
            document.getElementById("blueSquare").style.zIndex = 0;
            document.getElementById("greenSquare").style.zIndex = 0;
            break;
          case "blueSquare":
            document.getElementById("redSquare").style.zIndex = 0;
            document.getElementById("yellowSquare").style.zIndex = 0;
            document.getElementById("blueSquare").style.zIndex = 10;
            document.getElementById("greenSquare").style.zIndex = 0;
            break;
          case "greenSquare":
            document.getElementById("redSquare").style.zIndex = 0;
            document.getElementById("yellowSquare").style.zIndex = 0;
            document.getElementById("blueSquare").style.zIndex = 0;
            document.getElementById("greenSquare").style.zIndex = 10;
            break;

        }
    },
    async check(){
       if(this.layout.length < 4){
          this.layout.push(this.selectedBox);
          this.stopDrag()
          return false;
        } else if(this.layout.length > 4){
          debugger
          const index = this.layout.findIndex((element) =>  { 
            debugger
            if(element != "redSquare" && element != "yellowSquare" && element != "blueSquare" && element != "greenSquare")
              return element; 
            else 
              return false; 
            });
            debugger
          if(index > -1){
            console.log("bla bla")
            console.log(this.layout[index])
            console.log("bla bla")
            this.layout.splice(index, 1);
            
          } else { this.layout = ["redSquare", "yellowSquare", "blueSquare", "greenSquare"]} // saefty net
        }
      return true;
    },
    async stopDrag(event) {
      // We disable mouse move and activate transitions so that the boxes can find there place smoothly
      this.dragging = false;
      this.transitionActiveStyle = "top 0.5s, left 0.5s, right 0.5s";
      
      // we remove the selected box from the layout so that we can rearange them
      var index = this.layout.indexOf(this.selectedBox);
      if (index > -1) {
        this.layout.splice(index, 1);
      }
      
      var tmp;
      // Find out should we rearange them one beneth the other or top-left top-right, bottom-left bottom-right
        debugger
      if(this.windowWidth >=700){
        // We find out where have we dropped the box and recreate the layout
        if(event.clientX < this.windowWidth/2 && event.clientY < this.windowHeight/2){
          this.layout.splice(0, 0, this.selectedBox);
        }
        else if(event.clientX >= this.windowWidth/2 && event.clientY < this.windowHeight/2){
          this.layout.splice(1, 0, this.selectedBox);
        }
        else if(event.clientX < this.windowWidth/2 && event.clientY >= this.windowHeight/2){
          this.layout.splice(2, 0, this.selectedBox);
        }
        else{
          this.layout.splice(3, 0, this.selectedBox);
        }
        tmp = await this.check();
        // And acording to it we place the boxes in there place
        document.getElementById(this.layout[0]).style.top = (0) + 'px';
        document.getElementById(this.layout[0]).style.left = (0) + 'px';

        document.getElementById(this.layout[1]).style.top = (0) + 'px';
        document.getElementById(this.layout[1]).style.left= (this.windowWidth/2) + 'px';

        document.getElementById(this.layout[2]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[2]).style.left = (0) + 'px';

        document.getElementById(this.layout[3]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[3]).style.left= (this.windowWidth/2) + 'px';
      } else {
        // We find out where have we dropped the box and recreate the layout
        if(event.clientY < this.windowHeight/4){
          this.layout.splice(0, 0, this.selectedBox);
        }
        else if(event.clientY >= this.windowHeight/4 && event.clientY < this.windowHeight/2){
          this.layout.splice(1, 0, this.selectedBox);
        }
        else if(event.clientY >= this.windowHeight/2 && event.clientY < 3*this.windowHeight/4){
          this.layout.splice(2, 0, this.selectedBox);
        }
        else{
          this.layout.splice(3, 0, this.selectedBox);
        }
        tmp = await this.check();
        // And acording to it we place the boxes in there place
        document.getElementById(this.layout[0]).style.top = (0) + 'px';
        document.getElementById(this.layout[0]).style.left = (0) + 'px'; 

        document.getElementById(this.layout[1]).style.top = (this.windowHeight/4) + 'px';
        document.getElementById(this.layout[1]).style.left = (0) + 'px';

        document.getElementById(this.layout[2]).style.top = (this.windowHeight/2) + 'px';
        document.getElementById(this.layout[2]).style.left = (0) + 'px';

        document.getElementById(this.layout[3]).style.top = (3*this.windowHeight/4) + 'px';
        document.getElementById(this.layout[3]).style.left = (0) + 'px';
      
      }
      this.selectedBox = "";
    },
    doDrag(event) {
      // Check if an box is dragged
      if (this.dragging) {
        
        // Check if we know witch box is dragget
        if(this.selectedBox == "") return;
        if(document.getElementById(this.selectedBox) == undefined) return;

        // and now that we know the mouse starting position, current position, we can
        // Set the box position to the mouse position without choppiness
        document.getElementById(this.selectedBox).style.top = (event.clientY - this.startDragY) + 'px';
        document.getElementById(this.selectedBox).style.left = (event.clientX - this.startDragX) + 'px';
      }
    },
    // We couldn't find an api for number of the jokes so the first time the application is startet
    // We get all jokes and find the highest id and store it in numberOfJokes 
    checkLength(){
      let linkStr = "https://api.icndb.com/jokes/";
      let self = this;
      this.$axios
        .get(linkStr, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((response) => {
          self.numberOfJokes = response.data.value[response.data.value.length - 1].id + 1;
        })
        .catch((e) => {
          console.log("Greska: " + e);
        });
    },
    // Decition function for what to do when the button is pressed
    decideJoke(){
      // We check if there is an input and if there is, we check if its valid then proceed to get 3 jokes in a row
      // If there is no input, then we get 3 random jokes
      var check = parseInt(this.inputID);
      if(check > 0 ){
        // We make sure that we do not ask for jokes with id that dosen't exist
        var y = (check) % this.numberOfJokes;
        var b = (check + 1) % this.numberOfJokes
        var g = (check + 2) % this.numberOfJokes;

        if(check >= this.numberOfJokes){ y++; b++; g++; }
        else if(check >= this.numberOfJokes-1){ b++; g++; }
        else if(check >= this.numberOfJokes-2){ g++; }

        this.getJokeByID(y, "y");
        this.getJokeByID(b, "b");
        this.getJokeByID(g, "g");        
      } else this.getJoke();
    },
    // Function for getting a joke with an id
    // This time the logic for 3 jokes is in an calling function (decideJoke())
    getJokeByID(id, color) {
      let linkStr = "https://api.icndb.com/jokes/" + id;
      let self = this;
      this.$axios
        .get(linkStr, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((response) => {
          if(response.data.type == "success"){
            switch (color) {
              case "y":
                self.yellowJoke = response.data.value.joke;
                break;
              case "b":
                self.blueJoke = response.data.value.joke;
                break;
              case "g":
                self.greenJoke = response.data.value.joke;
                break;
            }
          }
        })
        .catch((e) => {
          console.log("Greska: " + e);
        });
    },
    // Function for getting a random joke
    getJoke() {
        let linkStr = "https://api.icndb.com/jokes/random";
        let self = this;
        this.$axios
          .get(linkStr, {
            headers: {
              "Content-Type": "application/json",
            },
          })
          .then((response) => {
            // We check if the joke has already been shown, if it was we try again
            if(self.allreadyHeardThem.includes(response.data.value.id)){
              self.getJoke();
            }
            else{
              // Otherwise, we record the joke, and we put it in its designated box using the counter
              self.allreadyHeardThem.push(response.data.value.id);

              switch (self.countToThree) {
                case 0:
                  self.yellowJoke = response.data.value.joke;
                  break;
                case 1:
                  self.blueJoke = response.data.value.joke;
                  break;
                case 2:
                  self.greenJoke = response.data.value.joke;
                  break;
              }
              // Witch we update every time a new joke is added
              // And we repeat it 2 more times
              self.countToThree++;
              if(self.countToThree == 3){
                self.countToThree = 0;
              } else {
                self.getJoke();
              }

            }
          })
          .catch((e) => {
            console.log("Greska: " + e);
          });
      },
  },
})
</script>
<style scoped>
.allSquares {
  
  width: 50%;
  height: 50%;
  position: absolute;
  text-align: center;
  font-weight: 700;
  

    -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none;
}

.allSquares > *{
  margin: 0;
  position: absolute;
  top: 50%;
  -ms-transform: translateY(-50%) translateX(-50%);
  transform: translateY(-50%) translateX(-50%);
}
#redSquare{
  background-color: red;
  top: 0px;
  left: 0px
}
#yellowSquare{
  background-color: yellow;
  top: 0px;
  right: 0px
}
#blueSquare{
  background-color: blue;
  bottom: 0px;
  left: 0px;
}
#greenSquare{
  background-color: green;
  bottom: 0px;
  right: 0px;
}


@media only screen and (max-width: 700px) {
  .allSquares {
    width: 100%;
    height: 25%;
  }
  #redSquare{
    top: 0px;
    left: 0px
  }
  #yellowSquare{
    top: 25%;
    left: 0px
  }
  #blueSquare{
    top: 50%;
    left: 0px;
  }
  #greenSquare{
    top: 75%;
    left: 0px;
  }
}
</style>