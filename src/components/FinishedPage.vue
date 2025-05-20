<template>
  <v-container>
    <v-card elevation-10>
      <!-- <p>{{ formattedTime }}</p> -->
      <v-app-bar flat color="primary">
        <v-toolbar-title class="text-h6 white--text pl-3">
          <!-- <p>Вопрос №{{ question.id + 1 }}</p> -->
          <p>Подведение итогов</p>
        </v-toolbar-title>
        <!-- <p class="text-h6 white--text pl-3">{{ formattedTime }}</p> -->
      </v-app-bar>
      <v-card-text height="150" class="pa-10 text-h6">
        <v-simple-table>
          <tbody>
            <tr>
              <td>
                <p class="text-subtitle-1">Имя:</p>
              </td>
              <td>
                <p class="text-subtitle-1">
                  {{ player_data.name }}
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p class="text-subtitle-1">Фамилия:</p>
              </td>
              <td>
                <p class="text-subtitle-1">
                  {{ player_data.surname }}
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p class="text-subtitle-1">Группа:</p>
              </td>
              <td>
                <p class="text-subtitle-1">
                  {{ player_data.group }}
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p class="text-subtitle-1">Затраченное время:</p>
              </td>
              <td>
                <p class="text-subtitle-1">
                  {{ formattedTime }}
                </p>
              </td>
            </tr>
          </tbody>
        </v-simple-table>
        <v-row class="mt-2">
          <v-progress-circular
            :value="relation"
            :rotate="270"
            :size="128"
            :width="40"
            style="color: dodgerblue"
          >
            {{ correctAnswersCount }}
          </v-progress-circular>
        </v-row>
        <v-row>
          <p style="color: dodgerblue" class="text-subtitle-2 mt-1">
            Количество правильных ответов: {{ correctAnswersCount }}
          </p>
        </v-row>
        <v-row>
          <p style="color: red" class="text-subtitle-2 mt-1">
            Количество неправильных ответов: {{ wrongAnswersCount }}
          </p>
        </v-row>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: "FinishPage",
  props: {
    player_data: {},
    relation: null,
    correctAnswersCount: null,
    wrongAnswersCount: null,
    usedTime: Number,
  },
  computed: {
    formattedTime() {
      const minutes = Math.floor(this.usedTime / 60);
      const seconds = this.usedTime % 60;
      return `${minutes.toString().padStart(2, "0")}:${seconds
        .toString()
        .padStart(2, "0")}`;
    },
  },
};
</script>

<style>
.v-progress-circular__underlay {
  stroke: red;
  z-index: 1;
}
</style>
