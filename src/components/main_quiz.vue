<template>
  <v-container>
    <div class="debug" v-if="debug">
      <v-row>
        <v-col>
          <v-btn
          @click="debug=false"
          x-small block tile text
          >hide_debug</v-btn>
        </v-col>
        <v-divider vertical/>
        <v-col>
          <p> 
            quiz_state: {{ quiz_state }}
          </p>
        </v-col>
        <v-divider vertical/>
        <v-col>
          <p> 
            ral: {{ get_rand(get_seed(user)).length }}
          </p>
        </v-col>
        <v-divider vertical/>
        <v-col>
          <p> 
            un:
          </p>
        </v-col>
        <v-divider vertical/>
        <v-col>
          <p> 
            us:
          </p>
        </v-col>
        <v-divider vertical/>
        <v-col>
          <p> 
            od:
          </p>
        </v-col>

      </v-row>
    </div>
    <div class="quiz" v-if="quiz_state">
      <!-- <v-btn
      @click="get_rand(get_seed(user))">
        get_rand
      </v-btn>
      <v-btn
        @click="get_seed(user)">
        get_seed
      </v-btn> -->
      <!-- <v-btn
        @click="get_question_answers(get_rand(get_seed(user)))">
        get_question_answers
      </v-btn> -->
      <!-- <v-btn
        @click="get_questions(get_rand(get_seed(user)))">
        gen_quiz
      </v-btn> -->
      <!-- <p>rand_arr: {{ get_rand(get_seed(user)) }}</p> -->
      <!-- <v-btn
        @click="generate_questions()">
        generate_questions
      </v-btn> -->
      <p>quiz_list: {{ quiz_list }}</p>
      

      <v-btn @click="quiz_state=false">end quiz</v-btn>
      
      <p>список правильных ответов: {{ questions_ans_list }}</p>
      <p>количество вопросов в архиве: {{ questions_list.length }}</p>
      
      <p>список номеров вопроса: {{ questions_num_list }}</p>
      <p>вопросы: {{ questions_list }}</p>


      <div class="quiz">
        <v-card 
          v-for="(obj, index) in questions_final_list.slice(0,user.optional_digit)" :key="index+1"
          class="pa-3 mt-3"
        >
          <p>{{ obj }}</p>
          <p>correct answer: {{ questions_ans_list[index] }}</p>  
        
        </v-card>
      </div>

    </div>



    


    <div class="finish" v-if="!quiz_state">
      <p>user_name: {{ user.user_name }}</p>
      <p>user_surname: {{ user.user_surname }}</p>
      <v-progress-circular
        :rotate="0"
        :size="100"
        :width="15"
        :value="
        (
          (correct_count)/questions_list.length
        )*100"
        color="teal"
      >
        {{ correct_count }}
      </v-progress-circular>
    </div>
    
    




  </v-container>
</template>

<script>
  export default {
    name: 'main_quiz',
    props:{
      user: Object
    },

    data: () => ({
      quiz_state: true,
      debug: true,
      questions_num_list: [],
      questions_ans_list: [],
      full_answers: [],
      questions_list: 
      [
        {
          id: 1,
          question: 'idk, guess',
          answer: 'correct_idea',
          alt_answers: [
            'incorrect_1',
            'incorrect_2',
            'incorrect_3'
          ],
        },
        {
          id: 2,
          question: 'idk, guess2',
          answer: 'correct_idea',
          alt_answers: [
            'incorrect_1',
            'incorrect_2',
            'incorrect_3'
          ]
        },
        {
          id: 3,
          question: 'idk, guess3',
          answer: 'correct_idea',
          alt_answers: [
            'incorrect_1',
            'incorrect_2',
            'incorrect_3'
          ]
        }
      ],
      correct_count: 1,
      quiz_list: [],
      questions_final_list: []

    }),
    methods:{
      get_seed(user){
        var seed = `${user['group']}${user['user_name']}${user['user_surname']}`
        // console.log(seed)
        return seed
      },
      cyrb128(str) {
        let h1 = 1779033703, h2 = 3144134277,
            h3 = 1013904242, h4 = 2773480762;
        for (let i = 0, k; i < str.length; i++) {
            k = str.charCodeAt(i);
            h1 = h2 ^ Math.imul(h1 ^ k, 597399067);
            h2 = h3 ^ Math.imul(h2 ^ k, 2869860233);
            h3 = h4 ^ Math.imul(h3 ^ k, 951274213);
            h4 = h1 ^ Math.imul(h4 ^ k, 2716044179);
        }
        h1 = Math.imul(h3 ^ (h1 >>> 18), 597399067);
        h2 = Math.imul(h4 ^ (h2 >>> 22), 2869860233);
        h3 = Math.imul(h1 ^ (h3 >>> 17), 951274213);
        h4 = Math.imul(h2 ^ (h4 >>> 19), 2716044179);
        h1 ^= (h2 ^ h3 ^ h4), h2 ^= h1, h3 ^= h1, h4 ^= h1;
        return [h1>>>0, h2>>>0, h3>>>0, h4>>>0];
      },
      // Side note: Only designed & tested for seed generation,
      // may be suboptimal as a general 128-bit hash.
      get_rand(str){
        var arr = this.cyrb128(str)
        // console.log(arr)
        for(var j=0; j<7; j++){
          for(var i=0; i<4; i++){
            arr.push(this.cyrb128(arr[j])[i])
          }
        }
        // console.log(arr)
        // возвращаю массив из случайных чисел
        return arr
      },
      get_question_answers(r_num_array, range=4){
        var r_num_array_length = r_num_array.length
        var answer = []
        for(var i=0; i<r_num_array_length; i++){
          console.log(`${r_num_array[i]} % ${range} = ${r_num_array[i] % range}`)
          answer.push(r_num_array[i] % range)
        }
        console.log(answer)
        this.questions_ans_list = answer
        return answer
      },




      // получаю номера вопросов
      get_questions(r_num_array){


        // что я делаю:
        // 1) создаю массив номеров доступных вопросов (они по порядку)
        // 2) начинаю цикл, в котором:
        //    2.1) выбирается случайный индекс массива доступных вопросов
        var answer_arr = []
        this.questions_num_list = answer_arr
        // список номеров вопросов
        var options_list = []
        for (var j=0; j<this.questions_list.length;j++){
          options_list.push(j)
        }
        console.log('options_list', options_list)
        this.quiz_list = options_list
        console.log('this.questions_list.length', this.questions_list.length)
        this.shuffle_arr(options_list, r_num_array)
        // this.shuffle_arr(this.quiz_list, r_num_array)
        this.questions_num_list = options_list
        
        for (var i=0; i<this.questions_num_list.length; i++){
          this.questions_final_list.push(
            this.questions_list[this.questions_num_list[i]]
            )
        }
        console.log(this.questions_final_list)

      },

      shuffle_arr(array, rand_num_arr){
        console.log('array',array)
        let currentIndex = array.length;
        // While there remain elements to shuffle...
        while (currentIndex != 0) {
          // Pick a remaining element...
          let randomIndex = rand_num_arr[0] % (array.length-currentIndex+1)
          currentIndex--;
          // And swap it with the current element.
          [array[currentIndex], array[randomIndex]] = [
            array[randomIndex], array[currentIndex]];
        }
      },


      generate_questions(){
        var i = this.questions_list.length
        while(i<this.get_rand(this.get_seed(this.user)).length)
        {
          this.questions_list.push(
            {
              id: i,
              question: `idk, guess${i}`,
              answer: 'correct_idea',
              alt_answers: [
                'incorrect_1',
                'incorrect_2',
                'incorrect_3'
              ]
            }
          )
          i++
        }
      },



    },
    mounted(){
      var rand_arr = this.get_rand(this.get_seed(this.user))
      this.generate_questions()
      this.get_questions(rand_arr)
      this.get_question_answers(rand_arr)
    }
  }
</script>

<style>
.debug{
  color: grey;
  max-height: 25px;

  overflow: hidden;
}
</style>

