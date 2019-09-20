<template>
    <div class="question-box-container">
            <b-jumbotron>

                <template v-slot:lead>
                {{ currentQuestion.question }}
                </template>

                <hr class="my-4">

              
                 <b-list-group>
                    <b-list-group-item 
                    v-for="(answer, index) in answers"
                    :key="index"
                     @click="selectAnswer(index)"
                    :class="answerClass(index)">
                    {{ answer}}
                    </b-list-group-item>
                </b-list-group>

                <b-button variant="primary" @click="submitAnswer" :disabled="selectedIndex === null || answered"> <!--disabled if selectedIndex is null-->
                    Submit
                    </b-button>
                <b-button  @click="next" variant="success" href="#">
                    Next
                </b-button>
            </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
import { constants } from 'fs';
export default {
    props: {
        currentQuestion : Object,
        next: Function,
        increment: Function
    },
    data(){
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
        answers(){
          let answers =  [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            // answers.push(this.currentQuestion.correct_answer)

             answers = _.shuffle(answers)
            console.log(this.currentQuestion.correct_answer)
            this.correctIndex = answers.indexOf(this.currentQuestion.correct_answer)
            return answers
        }
    },
    watch: {
        // currentQuestion(){
        //     this.selectedIndex = null
        //     // this.shuffleAnswers()
        // }
        currentQuestion: {     //We use currentQuestion as an object to save the problem that shuffledAnswers is null in the furst question
            immediate: true,
            handler(){
                    this.selectedIndex = null
                    this.answered = false
            }
        }
    },
    methods: {
        selectAnswer(index){
             this.selectedIndex = index
            
        },
        submitAnswer(){
            let isCorrect = false

            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true

            this.increment(isCorrect)
        },
        answerClass(index){
            let answerClass = ''
            
            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selected'
            } else if(this.answered && this.correctIndex === index){
                   answerClass = 'correct'
            } else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                 answerClass = 'incorrect'
            }

            return answerClass
        }
    },
    // mounted(){
    //     // console.log(this.currentQuestion)
    //     this.shuffledAnswers()
    // }
}
</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }
    .list-group-item:hover{
       background: #EEE;
       cursor: pointer;
    }
    .btn{
        margin: 0 5px;
    }
    .selected{
        background-color: lightblue
    }
    .correct{
        background-color: lightgreen
    }
    .incorrect{
        background-color: red
    }
</style>

