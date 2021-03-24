<template>
    <div class="bg-white relative rounded-2xl shadow-xl mx-auto p-8 w-5/6 sm:max-w-xl">
        <div class="space-y-8 mb-8">
            <!-- Title -->
            <div class="text-2xl">
                <p>
                    ✏ Ask Me Anything! 
                </p>
            </div>

            <!-- Input Section -->
            <div class="flex space-x-4 justify-center items-center">
                <input v-model="question" type="text" :placeholder="sampleQ"
                    class="w-1/2 text-lg text-center select-text placeholder-gray-500 placeholder-opacity-75 shadow-sm rounded-lg py-2 px-4 border border-gray-300 border-opacity-75
                        focus:outline-none focus:ring focus:ring-gray-300 focus:ring-opacity-50">

                <!-- Submit Button -->
                <div class="group relative">
                    <button @click="askQ()"
                            v-bind:class="{'text-red-400': !available, 'border-red-300': !available, 'hover:bg-red-500': !available, 'active:bg-red-800': !available,
                                            'text-blue-400': available, 'border-blue-400': available, 'hover:bg-blue-500': available, 'active:bg-blue-800': available}"
                            class="text-center text-xl font-bold rounded-2xl py-2 px-8 border-2
                                    transition duration-500 ease-in-out  hover:border-transparent hover:text-white hover:shadow-lg focus:outline-none
                                    active:duration-100"
                    > 
                        <p> ? </p>
                        
                    </button>
                    
                    <!-- Conditional Div that renders if the HuggingFace API isn't available -->
                    <div v-if="!available" class="invisible group-hover:visible absolute -right-28 -top-4 p-1 text-xs text-white rounded-md border-2 border-transparent bg-red-400">
                        <span class=""> API Isn't available <br> Hold on a sec 🛠 </span>
                    </div>

                </div>
                
            </div>

            <!-- Div to Display result from NLP model -->
            <div class="flex justify-center items-center">

                <p class="text-2xl font-semibold text-indigo-500">
                    {{ answer }}
                </p>
                
            </div>
        </div>
        
        <!-- Little snippet to redirect to orginal repo. Completely optional to include -->
        <div class="absolute bottom-0 right-0">
            <a href="">
                <button class="m-4 p-2 flex space-x-2 rounded-xl transition duration-500 ease-in-out border-transparent focus:outline-none hover:bg-gray-100 
                            transform hover:translate-x-1 hover:shadow-sm active:bg-gray-200 active:duration-100">
                    <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" viewBox="0 0 24 24"><path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/></svg>
                    <p class="text-md font-semibold">
                        About 
                    </p>
                </button>
            </a>   
        </div>
    </div>
</template>

<script>
import { getAnswer } from "../utils/bertAPI"

export default {
    data: () => ({  
        //sample Qs to populate the Input Field
        samples: [
            "Where did you grow up?",
            "How old are you?",
            "What are your passions?",
            "When were you born?",
            ],
        answer: "",
        question: "",
        available: false,
        API_checker: null,
    }),

    //computed Variable to populate the Input Field
    computed: {
        sampleQ() {
            return this.samples[Math.floor(Math.random() * this.samples.length)];
        },
    },

    //automatically run a checker to wake up API
    mounted() {
        this.checkAPI_Interval();
    },

    methods: {
        //query the api with the helper function from ./bertAPI.js
        askQ(){
            var question = this.question.replace("?", "")
            getAnswer(question).then((res) => {
                console.log(res.data)
                this.answer = res.data.answer
                this.loading = false
            })
        },

        //run a test query on API every 5 seconds
        checkAPI_Interval(){
            this.checkAPI
            this.API_checker = setInterval(()=>{
                this.checkAPI()
            }, 5000)
        },

        //query the API, if succesful indicate the component throug this.available and clear the Interval(), else keep running
        checkAPI(){
            getAnswer("h").then((res) => {
                this.available = true;
                clearInterval(this.API_checker)
            }).catch((err) => {
                this.available = false;
            })
        }
    }
}
</script>