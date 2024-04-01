<template>
  <div class="main">
    <div class="title">Caleendar</div>
    <div class="box">
      <div class="container">
        <div class="top">
          <i class="fa-solid fa-caret-left" @click="backMonth"></i>
          {{ monthFunc }} {{ yearFunc }}
          <i class="fa-solid fa-caret-right" @click="forwardMonth"></i>
        </div>
        <div class="namesOfDaysContainer">
          <div class="namesOfDays">
            <div
              v-for="(dayOfWeek, index) in daysOfWeekList"
              :key="index"
              class="dayOfWeek"
            >
              {{ dayOfWeek }}
            </div>
          </div>
        </div>

        <div class="daysContainer">
          <div class="daysBox">
            <div
              v-for="day in dayOfWeekCounter"
              :key="day"
              @click="showDate(day)"
              :class="['day', { checked: isChecked === day }]"
            >
              {{ day }}
            </div>
          </div>
        </div>
      </div>
      <input
        data-date-format="YYYY MMMM DD"
        v-model="date"
        type="date"
        name=""
        id=""
        @focusout="changeDateFunc()"
      />
    </div>
    <div class="showDate">{{ date }}</div>
    <button class="language" @click="changeLanguage">Ln</button>
    <p>Переключить язык</p>
  </div>
</template>

<script>
import {
  weekListRu,
  weekListEn,
  monListEn,
  monListRu,
} from "../utils/utils.js";
export default {
  name: "MyCalendar",
  data() {
    return {
      isChecked: Number,
      date: "",
      month: "",
      monthList: [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
      ],
      year: 2024,
      daysOfWeekList: ["Mo", "Tu", "We", "Th", "Fr", "Sa", "Su"],
    };
  },
  mounted() {
    this.newDateFunc();
  },
  methods: {
    daysCounter() {
      for (let day = 1; day < this.daysInMonth; day++) {
        this.daysList.push(day);
      }
    },
    showDate(day) {
      this.isChecked = day;
      this.date = String(day) + " " + this.month + " " + this.year;
    },
    backMonth() {
      let index = this.monthList.indexOf(this.month);
      // если мы хотим уйти меньше января, то скачем в декабрь и уменьшаем год
      if (index === 0) {
        this.month = this.monthList[11];
        this.year -= 1;
      } else {
        this.month = this.monthList[index - 1];
      }
      //обновить дату внизу в поставке на экран
      this.dayAbsentChecker();
      this.showDate(this.isChecked);
    },
    forwardMonth() {
      let index = this.monthList.indexOf(this.month);
      // если мы хотим уйти за декабрь, то скачем в январь и увеличиваем год
      if (index === this.monthList.length - 1) {
        this.month = this.monthList[0];
        console.log("до", this.year);
        this.year += 1;
        console.log("после", this.year);
      } else {
        this.month = this.monthList[index + 1];
      }
      //обновить дату внизу в поставке на экран
      this.dayAbsentChecker();
      this.showDate(this.isChecked);
    },
    changeDateFunc() {
      // по инпуту вносим изменения в календарь
      let newDate = this.date.split("-");
      this.year = newDate[0];
      this.month = this.monthList[Number(newDate[1] - 1)];
      this.isChecked = Number(newDate[2]);
    },
    newDateFunc() {
      this.year = Number(
        new Date().toLocaleString("en-EN", {
          year: "numeric",
        })
      );
      this.month = new Date().toLocaleString("en-EN", {
        month: "short",
      });
      this.isChecked = Number(
        new Date().toLocaleString("en-EN", {
          day: "numeric",
        })
      );
    },
    // функция проверки отсутствия такого числа в месяце
    dayAbsentChecker() {
      //если проверяемый день больше кол-ва дней в месяце
      if (this.isChecked > this.dayOfWeekCounter) {
        //то покажем посл день месяца
        this.isChecked = this.dayOfWeekCounter;
      }
    },
    changeLanguage() {
      const index = this.monthList.indexOf(this.month);
      if (this.daysOfWeekList[0] === "Mo") {
        this.daysOfWeekList = weekListRu;
        this.monthList = monListRu;
        this.month = this.monthList[index];
      } else {
        this.daysOfWeekList = weekListEn;
        this.monthList = monListEn;
        this.month = this.monthList[index];
      }
      // обновить дату в поставке на экран
      this.dayAbsentChecker();
      this.showDate(this.isChecked);
    },
  },
  computed: {
    monthFunc() {
      return this.month;
    },
    yearFunc() {
      return this.year;
    },
    dayOfWeekCounter() {
      if (
        this.month === "Jan" ||
        this.month === "Mar" ||
        this.month === "May" ||
        this.month === "Jul" ||
        this.month === "Aug" ||
        this.month === "Oct" ||
        this.month === "Dec" ||
        this.month === "Янв" ||
        this.month === "Мар" ||
        this.month === "Май" ||
        this.month === "Июл" ||
        this.month === "Авг" ||
        this.month === "Окт" ||
        this.month === "Дек"
      ) {
        return 31;
      } else if (
        this.month === "Apr" ||
        this.month === "Jun" ||
        this.month === "Sep" ||
        this.month === "Nov" ||
        this.month === "Апр" ||
        this.month === "Июн" ||
        this.month === "Сен" ||
        this.month === "Ноя"
      ) {
        return 30;
      }
      // Февраль
      //проверка на високосность
      if (this.year % 4 === 0) {
        if (this.year % 100 === 0) {
          if (this.year % 400 === 0) {
            return 29;
          } else {
            return 28;
          }
        }
        return 29;
      }
      return 28;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  display: flex;
  align-items: center;
  flex-direction: column;
}
.container {
  height: 185px;
}
.box {
  display: flex;
  justify-content: center;
  gap: 50px;
}
.title {
  text-align: center;
  margin-bottom: 50px;
}
.top {
  width: 270px;
  text-align: center;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  padding-right: 10px;
  padding-left: 10px;
}
.fa-solid {
  cursor: pointer;
}
.namesOfDays {
  display: grid;
  grid-template-columns: repeat(7, 42px);
}
.daysBox {
  display: grid;
  grid-template-columns: repeat(7, 42px);
}
.dayOfWeek {
  text-align: center;
}

.namesOfDaysContainer {
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
}
.daysContainer {
  display: flex;
  justify-content: center;
}
.day {
  box-sizing: border-box;
  margin-bottom: 5px;
  text-align: center;
  border: 1px solid transparent;
  cursor: pointer;
}
.checked {
  border: 1px solid blue;
}
input {
  height: 20px;
}
.showDate {
  margin-top: 30px;
  border: 1px solid greenyellow;
  width: 150px;
  text-align: center;
  margin-bottom: 50px;
}
.language{
  cursor: pointer;
}
</style>
