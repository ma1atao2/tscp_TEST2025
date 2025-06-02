<template>
  <v-container>
    <v-card elevation="10">
      <!-- <p>{{ formattedTime }}</p> -->
      <v-app-bar flat color="primary">
        <v-toolbar-title class="text-h6 white--text pl-3">
          <p class="my-3">Вопрос №{{ questionNumber }}</p>
        </v-toolbar-title>
      </v-app-bar>
      <v-card-text height="150" class="pa-10 text-h6">
        <p style="text-align: center">{{ question.name }}</p>
        <p>1) {{ question.answers[0] }}</p>
        <p>2) {{ question.answers[1] }}</p>
        <p>3) {{ question.answers[2] }}</p>
        <p>4) {{ question.answers[3] }}</p>
      </v-card-text>
      <p class="text-h6 grey--text text--darken-2 text-center">Выберите номер ответа</p>
      <v-card-actions>
        <v-col>
          <v-row>
            <v-col>
              <v-btn block style="display: block; text-align: center" min-height="75" class="mx-a pa-5" color="success"
                @click="getAnswer" :id="question.answers[0]">1</v-btn>
            </v-col>
            <v-col>
              <v-btn block style="display: block; text-align: center" min-height="75" class="mx-a pa-5" color="success"
                @click="getAnswer" :id="question.answers[1]">2</v-btn>
            </v-col>
          </v-row>
        </v-col>
        <v-col>
          <v-row>
            <v-col>
              <v-btn block style="display: block; text-align: center" min-height="75" class="mx-a pa-5" color="success"
                @click="getAnswer" :id="question.answers[2]">3</v-btn>
            </v-col>
            <v-col>
              <v-btn block style="display: block; text-align: center" min-height="75" class="mx-a pa-5" color="success"
                @click="getAnswer" :id="question.answers[3]">4</v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: "questionPage",
  data() {
    return {
      timerInterval: null,
      remainingTime: 0,
    };
  },
  props: {
    question: {},
    questionNumber: Number
  },
  methods: {
    getAnswer(event) {
      console.log(event.currentTarget.id);
      this.$emit("AnswerGot", 1);
      this.$emit("ChosenAnswer", event.currentTarget.id);
    },

  },
};
</script>

<style scoped></style>
