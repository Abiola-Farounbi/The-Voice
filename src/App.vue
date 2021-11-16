<template>
  <div class="app">
    <header>
      <h1> The Voice Feature  </h1>
      <i class="header-icon fas fa-microphone-alt"></i>
    </header>
    <main>
      <div class="mic-buttons">
        <!-- To handle the controls -->
        <button  :class = "toggle ? 'speak' : 'none' " @click="startSpeechRecognition" >  Speak  <i class="mic-icons fas fa-microphone"></i> </button>
        <button class="stop" @click="endSpeechRecognition" > Stop <i class="mic-icons fas fa-microphone-slash"></i> </button>
      </div>
      <h2> English Transcript </h2>
      <!-- Conditionals to handle errors -->
      <p v-if="error" >{{error}}</p>
      <div v-else>
         <p class='text-runtime'> Listening Text : {{runtimeTranscription}}  </p>
      <textarea v-model="sentences" class="text-transcript" cols="30" rows="10">  </textarea>
      </div>
    </main>
  </div>
</template>

<script>
  let SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition
  //  Intialize the api
    let recognition = SpeechRecognition? new SpeechRecognition() : false
      // Sets the deafult language and properties
      recognition.lang = 'en-US' 
      recognition.interimResults = true
export default {
  
  name: 'App',
  data () {
    return {
      error: false,
      toggle: false,
      sentences: '',
      runtimeTranscription: '',
    }
  },
  methods:{ 
    

    
    // Function to end the speak event
     endSpeechRecognition() {
        this.toggle = false
      recognition.stop()
         recognition.onend = () => {
           recognition.stop()
    };
    },

    // Function to call the speak event
    startSpeechRecognition(){
      this.toggle = true
      recognition.start();

      
    recognition.onend = () => {
      recognition.start();
    };
      
     

      recognition.onresult = (event) => {
        this.runtimeTranscription = ''
       for (let i = event.resultIndex; i < event.results.length; ++i){
        if (event.results[i].isFinal) {
        this.sentences += this.capitalizeFirstLetter(event.results[i][0].transcript);
        this.sentences += '. '
      } else {
        this.runtimeTranscription  += event.results[i][0].transcript;
      }
        }
        }

       recognition.onerror = (event) => {
      this.error = 'Speech recognition error detected: ' + event.error
       }

      
    },
      // Function to capitalize the first letter
      capitalizeFirstLetter (string) {
      return string.charAt(0).toUpperCase() + string.slice(1)
      }
   
  },


   mounted() {
    //  Conditional to check for browser support
      if (!recognition) {
        this.error = 'Speech Recognition is not available on this browser. Please use Chrome '
      }
    },

}
</script>

<style>
*{
  background-color: #fff0f5;
  margin: 0px;
  padding: 5px;
}
header{
  display: flex;
}
h1{
  font-size:2rem;
}
.header-icon{
  font-size:2rem;
}
main{
  margin:20px;
}
.mic-buttons{
  display:flex;
  justify-content: space-evenly;
  align-items: center;
}
.mic-buttons button{
  border:none;
  display: flex;
  background-color: white;
  box-shadow: 2px 2px 4px grey;
  font-size:1rem;
  line-height: 2rem;
  color: #00acc1;
  border-radius: 10px;
  padding:10px 20px;
}
.speak{
 animation: pop 1s ease forwards 1s infinite normal;
}

  @keyframes pop{
    0%{ transform: scale(1.02); }
    25%{ transform: scale(1.04); }
    50%{ transform: scale(1.06); }
    75%{ transform: scale(1.08); }
    100% { transform: scale(1.1);}
      }
.mic-icons{
  font-size:1rem;
  color: #00acc1;
  background-color: white;
}
.text-transcript{
  background-color: white;
  border:none;
  width:100%;
  border-radius: 20px 0px 20px 0px;
  padding:10px;
}
.text-runtime{
  text-align: center;
  color:#00acc1;
  font-weight: bold;

}

</style>
