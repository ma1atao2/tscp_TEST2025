<template>
  <div>
    <!-- <div class="mt-3" v-for="(question, index) in questions" :key="question+ index"
    v-show="question.id == index">
      
    </div> -->
    <!-- <div v-if="test_start && !test_finished">
      <v-card>{{ formattedTime }}</v-card>
    </div> -->
    <v-app-bar flat color="primary" v-if="test_start && !test_finished">
      <v-toolbar-title
        class="text-h6 white--text pl-3"
        style="align-items: center"
      >
        <p>{{ formattedTime }}</p>
      </v-toolbar-title>
    </v-app-bar>
    <greetingPage @submit="getQuestionPage" v-if="!test_start" />
    <questionPage
      v-if="test_start && !test_finished"
      v-for="question of questions.slice(questionNumber, questionNumber + 1)"
      :key="question.id"
      :question="question"
      :questionNumber="questionCount"
      @AnswerGot="nextNumber"
      @ChosenAnswer="makeChosenAnswerList"
    />
    <finishedPage
      v-if="test_finished"
      :player_data="form_data"
      :relation="relation"
      :correctAnswersCount="correctAnswersCount"
      :wrongAnswersCount="wrongAnswersCount"
      :usedTime="usedTime"
    />
  </div>
</template>

<script>
import questionPage from "../components/questionPage";
import greetingPage from "./GreetingPage.vue";
import finishedPage from "./FinishedPage.vue";

export default {
  name: "MainPage",
  components: { questionPage, greetingPage, finishedPage },
  data() {
    return {
      selected: null,
      questionNumber: null,
      questionCount: 1,
      correctAnswersCount: 0,
      wrongAnswersCount: 0,
      questions: [
        {
          id: 0,
          name: "Диапазон частот человеческой речи?",
          answers: [
            "0 – 4 кГц",
            "300 – 3400 кГц",
            "0.4 – 3.3 кГц",
            "0.3 – 3.4 кГц",
          ],
        },
        {
          id: 1,
          name: "Динамический диапазон речи для аппаратуры?",
          answers: ["48 Дб", "40 Дб", "50 Дб", "42 Дб"],
        },
        {
          id: 2,
          name: "Каков минимальный уровень для защищенности от шумов квантования?",
          answers: ["48 Дб", "30 Дб", "53 Дб", "35 Дб"],
        },
        {
          id: 3,
          name: "Какие стандарт(-ы) скоростей PDH был(-и) изучен(-ы) в лекциях?",
          answers: [
            "Европейский, Американский, Японский",
            "Северо-Американский ",
            "Европейский, Северо-Американский, Японский",
            "Европейский",
          ],
        },
        {
          id: 4,
          name: "Какова скорость передачи информации в основном цифровом канале (ОЦК)?",
          answers: ["64 Кбайт/c", "32 Кбит/c", "8 Кбайт/c", "64 Кбит/c"],
        },
        {
          id: 5,
          name: "Сколько основных цифровых каналов, не учитывая служебных, в первом потоке Е1?",
          answers: ["7", "4", "32", "30"],
        },
        {
          id: 6,
          name: "Сколько уровней скоростей в европейском стандарте PDH?",
          answers: ["4", "3", "6", "5"],
        },
        {
          id: 7,
          name: "Сколько интервалов в структуре потока E1?",
          answers: ["31", "30", "16", "32"],
        },
        {
          id: 8,
          name: "Какие интервалы в структуре потока E1 являются служебными?",
          answers: ["1 и 16", "0 и 15", "1 и 15", "0 и 16"],
        },
        {
          id: 9,
          name: "Какой код используется для нахождения ошибок в потоке E1?",
          answers: ["CRC7", "CRC3", "CRC5", "CRC4"],
        },
        {
          id: 10,
          name: "Как называется явление при котором изменяется фаза тактового сигнала выше 10 Гц?",
          answers: ["Частотирование", "Фазирование", "Вандер", "Джитер"],
        },
        {
          id: 11,
          name: "Как называется явление при котором изменяется частота тактового сигнала до 10 Гц?",
          answers: ["Частотирование", "Фазирование", "Джитер", "Вандер"],
        },
        {
          id: 12,
          name: "Как обозначается кодек речевых сигналов европейского стандарта?",
          answers: ["AAC", "H-264", "A-87,6", "A-86,7"],
        },
        {
          id: 13,
          name: "Как называется устройство для преобразования аналогово сигнала в цифровой?",
          answers: ["ЦАП", "ФВЧ", "РФ", "АЦП"],
        },
        {
          id: 14,
          name: "Как называется устройство для преобразования цифрового сигнала в аналоговый?",
          answers: ["АЦП", "ФНЧ", "ПФ", "ЦАП"],
        },
        {
          id: 15,
          name: "Сколько потоков Е1 входит в один поток Е2?",
          answers: ["7", "5", "30", "4"],
        },
        {
          id: 16,
          name: "Какой код используется для нахождения ошибок в потоке E2?",
          answers: ["CRC4", "CRC5", "CRC3", "CRC7"],
        },
        {
          id: 17,
          name: "Какова скорость потока E2 без учёта служебной информации?",
          answers: [
            "2048 Кбайт/с",
            "1024 Кбит/с",
            "2112 Кбайт/с",
            "2048 Кбит/с",
          ],
        },
        {
          id: 18,
          name: "Как называется устройство для согласования скоростей потоков Е1 в потоке Е2?",
          answers: [
            "Блок синхронного сопряжения ",
            "Блок согласования скоростей",
            "Согласующий блок",
            "Блок асинхронного сопряжения",
          ],
        },
        {
          id: 19,
          name: "Какова скорость потока E2 с учётом служебной информации?",
          answers: [
            "2048 Кбит/с",
            "1024 Кбайт/с",
            "2112 Кбайт/с",
            "2112 Кбит/с",
          ],
        },
        {
          id: 20,
          name: "Какие есть команды согласования скоростей?",
          answers: ["11 и 00", "101 и 010", "010 и 101", "111 и 000"],
        },
        {
          id: 21,
          name: "Как называется разделение каналов за счёт использования разных частот?",
          answers: ["ВРК", "КРК", "МРК", "ЧРК"],
        },
        {
          id: 22,
          name: "Как называется разделение каналов за счёт использования разных интервалов времени передачи информации?",
          answers: ["ЧРК", "ПРК", "ФРК", "ВРК"],
        },
        {
          id: 23,
          name: "Какое устройство нужно для соединения двухполосной линии с однополосной линией?",
          answers: [
            "Дифференциал",
            "Балансный контур",
            "Конвертор",
            "Дифференциальная система",
          ],
        },
        {
          id: 24,
          name: "Какой спектр используется для формирования первичного группового тракта?",
          answers: [
            "312 – 552 кГц",
            "12 – 256 кГц",
            "812 – 2024 кГц",
            "60 – 108 кГц",
          ],
        },
        {
          id: 25,
          name: "Какой спектр используется для формирования вторичного группового тракта?",
          answers: [
            "60 – 108 кГц",
            "812 – 2024 кГц ",
            "12 – 256 кГц",
            "312 – 552 кГц",
          ],
        },
        {
          id: 26,
          name: "Какой спектр используется для формирования третичного группового тракта?",
          answers: [
            "12 – 256 кГц",
            "312 – 552 кГц ",
            "60 – 108 кГц",
            "812 – 2024 кГц",
          ],
        },
        {
          id: 27,
          name: "Сколько каналов в первичном групповом тракте?",
          answers: ["60 каналов", "300 каналов", "30 каналов", "12 каналов"],
        },
        {
          id: 28,
          name: "Сколько каналов в вторичном групповом тракте?",
          answers: ["12 каналов", "30 каналов", "300 каналов", "60 каналов"],
        },
        {
          id: 29,
          name: "Сколько каналов в третичном групповом тракте?",
          answers: ["30 каналов ", "12 каналов", "60 каналов", "300 каналов"],
        },
        {
          id: 30,
          name: "Сколько диодов отмечено на схеме однотактного преобразователя частоты?",
          answers: ["два", "четыре", "шесть", "один"],
        },
        {
          id: 31,
          name: "Сколько диодов отмечено на схеме двухтактного однопериодного преобразователя частоты?",
          answers: ["один", "четыре", "шесть", "два"],
        },
        {
          id: 32,
          name: "Сколько диодов отмечено на схеме двухтактного преобразователя частоты?",
          answers: ["один", "два", "шесть", "четыре"],
        },
        {
          id: 33,
          name: "Назначение генераторного оборудования в временном разделении каналов? ",
          answers: [
            "Выделение несущих сигналов",
            "Согласование скоростей",
            "Синхронизация работы устройств",
            "Разделение временных каналов",
          ],
        },
        {
          id: 34,
          name: "Назначение генераторного оборудования в частотном разделении каналов? ",
          answers: [
            "Разделение временных каналов",
            "Выделение несущих сигналов ",
            "Синхронизация работы устройств",
            "Выделение несущих сигналов и их формирование",
          ],
        },
        {
          id: 35,
          name: "Какие генераторы используют в задающих генераторах? ",
          answers: [
            "С индуктивной обратной связью",
            "Транзисторные",
            "LC-Генераторы",
            "Кварцевые",
          ],
        },
        {
          id: 36,
          name: "Какие современные скоростные технологии были рассмотрены? ",
          answers: [
            "PDH, ISDN, Frame relay",
            "PDH, SDH, SONET, ISDN ",
            "PDH, SDH, SONET",
            "PDH, SDH, SONET, ISDN, Frame relay",
          ],
        },
        {
          id: 37,
          name: "Как расшифровывается STM? ",
          answers: [
            "Согласованный транспортный модуль",
            "Служебный тракт мультиплексирования",
            "Синхронизирующийся транспортный модуль",
            "Синхронный транспортный модуль",
          ],
        },
        {
          id: 38,
          name: "Какова скорость передачи информации в контейнере C12?",
          answers: ["8 Мбит/с", "34 Мбит/с", "20 Мбит/с", "2 Мбит/с"],
        },
        {
          id: 39,
          name: "Какова скорость передачи информации в контейнере C22?",
          answers: ["34 Мбит/с", "2 Мбит/с", "20 Мбит/с", "8 Мбит/с"],
        },
        {
          id: 40,
          name: "Какова скорость передачи информации в контейнере C32?",
          answers: ["2 Мбит/с", "20 Мбит/с", "8 Мбит/с", "34 Мбит/с"],
        },
        {
          id: 41,
          name: "Какие есть подуровни у контейнера C4, каким уровням иерархии они соответствуют?",
          answers: [
            "C41 – E4, C42 – T4",
            "C41 – T4, C42 – E4",
            "C4 – T4",
            "C4 – E4",
          ],
        },
        {
          id: 42,
          name: "Какой формулой определяется структура виртуального контейнера VC?",
          answers: ["PAU + VC", "PTR + VC", "SOH + PL", "POH + PL"],
        },
        {
          id: 43,
          name: "Сколько уровней скоростей STM рассмотрено в курсе лекций?",
          answers: [
            "STM-1, STM-4, STM-8, STM-16, STM-64, STM-256",
            "STM-1, STM-4, STM-16, STM-64",
            "STM-1, STM-4, STM-64, STM-256",
            "STM-1, STM-4, STM-16, STM-64, STM-256",
          ],
        },
        {
          id: 44,
          name: "Какие топологии сетей SDH были рассмотрены?",
          answers: [
            "Точка-точка, последовательная линейная цепь, звезда, кольцо",
            "Последовательная линейная цепь, уплощённое кольцо, звезда",

            "Точка-точка, последовательная линейная цепь, упрощённое кольцо, звезда",

            "Точка-точка, последовательная линейная цепь, уплощённое кольцо, звезда, кольцо",
          ],
        },
        {
          id: 45,
          name: "Какие есть способы резервирования участков?",
          answers: ["1*1", "1:1", "1+1", "1+1, 1:1"],
        },
        {
          id: 46,
          name: "Какие есть архитектуры сетей SDH?",
          answers: [
            "Радиально-кольцевая, кольцо-точка ",
            "Кольцо-кольцо",
            "Радиально-кольцевая ",
            "Радиально-кольцевая, кольцо-кольцо",
          ],
        },
      ],
      questionList: [],
      answersList: [],
      chosenAnswerList: [],
      test_start: false,
      form_data: [],
      timerInterval: null,
      remainingTime: 0,
      test_finished: false,
      time: 5,
      relation: null,
      usedTime: null,
    };
  },
  methods: {
    startTimer() {
      this.remainingTime = this.time * 60;

      this.timerInterval = setInterval(() => {
        this.remainingTime--;
        console.log(this.remainingTime);
        if (this.remainingTime <= 0) {
          this.finishTest();
          clearInterval(this.timerInterval);
        }
      }, 1000);
    },
    nextNumber(i) {
      this.questionNumber += i;
      console.log(this.questionNumber);
      this.questionCount++;
    },
    getQuestionPage(data) {
      this.test_start = data.submit;
      console.log(data);
      this.form_data = data;
      this.time = this.form_data.time;
      // console.log(data);
      this.makeRandomQuestions();
      // console.log(this.questions);
      this.getCorrectAnswersList();

      for (let i = 0; i < this.questions.length; i++) this.makeRandomAnswers(i);
      // console.log(this.questions);
      this.startTimer();
    },
    makeRandomQuestions() {
      let currentIndex = this.questions.length;
      // console.log(currentIndex);
      while (currentIndex != 0) {
        let randomIndex = this.getRandomInt(currentIndex);
        console.log(randomIndex);
        currentIndex--;
        [this.questions[currentIndex], this.questions[randomIndex]] = [
          this.questions[randomIndex],
          this.questions[currentIndex],
        ];
      }
    },
    makeChosenAnswerList(data) {
      this.chosenAnswerList.push(data);
      if (this.questions.length == this.chosenAnswerList.length)
        this.finishTest();
      // console.log(this.chosenAnswerList);
    },
    makeRandomAnswers(index) {
      let currentIndex = this.questions[index].answers.length;
      // console.log(currentIndex);
      while (currentIndex != 0) {
        let randomIndex = this.getRandomInt(currentIndex);
        currentIndex--;
        [
          this.questions[index].answers[currentIndex],
          this.questions[index].answers[randomIndex],
        ] = [
          this.questions[index].answers[randomIndex],
          this.questions[index].answers[currentIndex],
        ];
      }
    },
    getCorrectAnswersList() {
      for (let i = 0; i < this.questions.length; i++) {
        this.questionList.push({
          id: i,
          answers: this.questions[i].answers[3],
        });
      }
      // console.log(this.questionList);
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * max);
    },
    finishTest() {
      console.log("test finished");
      this.test_finished = true;
      clearInterval(this.timerInterval);
      this.checkAnswers();
      // console.log(this.correctAnswersCount)
      // console.log(this.chosenAnswerList.length)
      this.relation =
        (this.correctAnswersCount / this.chosenAnswerList.length).toFixed(2) *
        100;
      // console.log(this.relation)
      this.wrongAnswersCount =
        this.chosenAnswerList.length - this.correctAnswersCount;
      this.usedTime = this.time * 60 - this.remainingTime;
    },
    checkAnswers() {
      for (let i = 0; i < this.chosenAnswerList.length; i++) {
        // console.log(this.chosenAnswerList[i])
        // console.log(this.questionList[i].answer)
        if (this.chosenAnswerList[i] == this.questionList[i].answers) {
          this.correctAnswersCount++;
          // console.log(this.correctAnswersCount)
        }
      }
    },
  },
  computed: {
    formattedTime() {
      if (this.remainingTime <= 0 && this.test_start) {
        return "00:00";
      }
      const minutes = Math.floor(this.remainingTime / 60);
      const seconds = this.remainingTime % 60;
      return `${minutes.toString().padStart(2, "0")}:${seconds
        .toString()
        .padStart(2, "0")}`;
    },
  },
};
</script>

<style scoped></style>
