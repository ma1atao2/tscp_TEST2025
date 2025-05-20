<template>
  <div>
    <v-app-bar flat color="primary">
      <v-toolbar-title class="text-h6 white--text pl-3">
        <p>Форма заполнения</p>
      </v-toolbar-title>
    </v-app-bar>
    <v-card rounded elevation-10 width="100%" class="mx-auto pa-3">
      <v-form ref="form" lazy-validation class="mt-3">
        <v-row>
          <v-col>
            <v-text-field
              label="Фамилия"
              v-model="surname"
              outlined
              counter
              maxlength="20"
              clearable
              :rules="[validateText]"
              required
            >
            </v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field
              label="Имя"
              v-model="name"
              outlined
              counter
              maxlength="20"
              clearable
              :rules="[validateText]"
              required
            >
            </v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-select label="Группа" outlined :items="groups" v-model="group">
            </v-select>
          </v-col>
        </v-row>
        <!-- <v-row>
          <v-col>
            <v-checkbox
              v-model="alternate_time"
              color="green"
              @onChange="use_slider"
              label="Использовать другое отображение времени?"
            >
            </v-checkbox>
          </v-col>
        </v-row>
        <v-row class="pl-7 pr-7" v-if="alternate_time">
          <v-slider
            v-model="time"
            min="1"
            max="60"
            label="Время"
            color="success"
            thumb-label
            required
          >
          </v-slider>
        </v-row> -->
        <v-row v-if="!alternate_time">
          <v-col>
            <v-text-field
              label="Время, мин"
              v-model="time"
              :rules="[validateNumber]"
              outlined
              counter
              maxlength="2"
              clearable
              @input="checkInputTime"
              required
            >
            </v-text-field>
          </v-col>
        </v-row>
        <v-row class="pa-3">
          <v-btn color="primary" @click="submit">Начать тестирование</v-btn>
        </v-row>
      </v-form>
    </v-card>
  </div>
</template>

<script>
export default {
  name: "greetingPage",
  data() {
    const defaultForm = Object.freeze({
      name: "",
      surname: "",
      group: "",
      time: 0,
      submit: false,
    });
    return {
      name: "",
      surname: "",
      group: "ТСТ-442",
      time: 5,
      conditions: false,
      defaultForm,
      form: Object.assign({}, defaultForm),
      alternate_time: false,
      rules: {
        number: (v) => !!v || "Number is required",
        name: (v) => (!!v && 20 >= v.length) || "",
      },
      validationCorrect: false,
      isValid: true,
      groups: ["ТСТ-441", "ТСТ-442"],
    };
  },
  computed: {
    formIsValid() {
      return (
        this.form.name && this.form.surname && this.form.group && this.form.time
      );
    },
  },
  methods: {
    validateNumber(value) {
      if (!value) return "Поле не должно быть пустым";
      if (isNaN(value)) return "Значение должно быть числом";
      if (value <= 0) return "Время должно быть больше 0 минут";
      if (value > 60) return "Время не должно превышать 60 минут";
      return true;
    },

    validateText(value) {
      if (!value) return "Поле не должно быть пустым";
      if (value.length > 20) return "Число символов не должно превышать 20";
      return true;
    },
    submit() {
      this.form.name = this.name;
      this.form.surname = this.surname;
      this.form.group = this.group;
      this.form.time = Number(this.time);
      if (this.$refs.form.validate()) {
        this.form.submit = true;
        this.$emit("submit", this.form);
      }
      // console.log(this.$refs.form.validate())
    },
    use_slider() {
      this.alternate_time = true;
    },
    checkInputTime() {
      if (!/^\d+$/.test(this.time)) {
        this.time = this.time.replace(/[^\d]/g, "");
      }
    },
  },
};
</script>

<style scoped></style>
