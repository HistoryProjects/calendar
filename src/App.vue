<template>
  <div class="container">
    <CalendarElem />

    <div class="top-wrapp">
      <select v-model="language" class="lang">
        <option value="ru">Ru</option>
        <option value="en">En</option>
      </select>
      <div class="top">
        <span>{{formatDate(selectedDate)}} </span>
    
        <button @click="isVisible" class="calendarBtn">
            <img src="./assets/calendar.png" alt="calendar" />
        </button>
      </div>
      
    </div>
    

    <hr />

    <div v-if="showCalendar">
        
        <CalendarTop 
          @prevMonth="prevMonth"
          @nextMonth="nextMonth"
          :currentDate="currentDate"
          :language="language"
        />

        <table>
          <tr>
            <th v-for="(item, key) in weekDays" :key="key">
              {{ language === 'ru' ? item : key }}
            </th>
          </tr>
          <tr v-for="(row, rowIndex) in Math.ceil(monthDays.length / 7)" :key="rowIndex" class="days">
            <td v-for="(day, dayIndex) in monthDays.slice(rowIndex * 7, (rowIndex * 7) + 7)" 
                :key="dayIndex"
                :class="{ 
                  currentDay: day.day === today && 
                  day.month === 'current' &&
                  this.selectedDate.getFullYear() === this.currentDate.getFullYear() &&
                  monthNow === currentDate.getMonth(), 

                  selectedDay: day.selected &&
                  (day.month === 'current' && 
                  selectedDate.getMonth() === currentDate.getMonth() && 
                  this.selectedDate.getFullYear() === this.currentDate.getFullYear() &&
                  day.day === this.selectedDate.getDate())
                }"
                @click="selectDay(day)"
                
            >
              {{ day.day }}
            </td>
          </tr>
        </table>

        
    </div>
  </div>
</template>

<script>
import CalendarTop from './components/CalendarTop.vue';


export default {
  name: 'App',
  components: {
    
    CalendarTop
  },
  data(){
      return{
          language: 'ru', 
          today: 0,
          daysInMonth: 0, //количество дней в месяце
          weekDays: {
             'Mon': 'Пн',
             'Tue': 'Вт',
             'Wed': 'Ср',
             'Thu': 'Чт',
             'Fn': 'Пт',
             'Sat': 'Сб',
             'Sun': 'Вс'
          },
          currentDate: new Date(),
          monthDays: [], //[{1, пн }...{}] число и день недели
          monthNow: 0, //номер текущего месяца
          selected: false, //выбранный день
          selectedDate: new Date(),
          showCalendar: false

      }
  },
  mounted() {
    this.getDay();
    this.setDaysInMonth(this.currentDate.getMonth());
    this.makeDaysOfMonth(this.daysInMonth);
    this.monthNow = this.currentDate.getMonth();
    this.selectedDate = new Date(this.currentDate);
  },
  methods:{
    formatDate(date) {
      const day = date.getDate();
      const month = date.getMonth() + 1; // Месяцы в JavaScript начинаются с 0
      const year = date.getFullYear();

      // Форматируем числа месяца и дня, чтобы добавить ведущий ноль, если число меньше 10
      const formattedDay = day < 10 ? `0${day}` : day;
      const formattedMonth = month < 10 ? `0${month}` : month;

      return `${formattedDay}.${formattedMonth}.${year}`;
    },
    selectDay(day) {

      // Сначала сбрасываем выбор у всех дней
      this.monthDays.forEach((d) => {
          d.selected = false;
      });
   
        // Затем устанавливаем выбранный день
        day.selected = true;
     
      // Сохраняем выбранную дату
      this.selectedDate = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), day.day);

    },
    makeDaysOfMonth(amt_d){            
     
     this.monthDays = [];
      let firstDayOfMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 1);
      let prevMonthLastDay = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 0).getDay();
      let daysInPrevMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 0).getDate();


      // Добавляем последние дни прошлого месяца
      for (let i = daysInPrevMonth - prevMonthLastDay + 1; i <= daysInPrevMonth; i++) {
          this.monthDays.push({ day: i, dayOfWeek: (prevMonthLastDay + i - 1) % 7, month: 'prev' });
      }

      // Добавляем дни текущего месяца
      for (let i = 1; i <= amt_d; i++) {
          this.monthDays.push({ day: i, dayOfWeek: (firstDayOfMonth.getDay() + i - 1) % 7, month: 'current' });
      }

      // Добавляем первые дни следующего месяца
      let nextMonthFirstDay = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() + 1, 1).getDay();
      let nextMonthDays = 7 - ((daysInPrevMonth + amt_d) % 7);
      for (let i = 1; i <= nextMonthDays; i++) {
          this.monthDays.push({ day: i, dayOfWeek: (nextMonthFirstDay + i - 1) % 7, month: 'next' });
      }
    },
    getDay(){ this.today = new Date().getDate() },

    checkDay(){
        //Сохранение выбранной даты при листании
        // Проверка, соответствует ли выбранный день своему месяцу и году
        if (
            this.selectedDate.getMonth() === this.currentDate.getMonth() &&
            this.selectedDate.getFullYear() === this.currentDate.getFullYear()
        ) {
            this.monthDays.forEach(day => {
                //Помечаем день / число выбранным при соответствии даты и месяца
                if (day.month === 'current' && day.day === this.selectedDate.getDate()) {
                    day.selected = true;
                } else {
                    day.selected = false;
                }
            });
        }
    },
    nextMonth() {
      let month = new Date(this.currentDate);
      month.setMonth(month.getMonth() + 1);
      this.currentDate = month;
      this.setDaysInMonth(month.getMonth());
      this.makeDaysOfMonth(this.daysInMonth);

      this.checkDay()
  
    },

    prevMonth(){
      let month = new Date(this.currentDate);
      month.setMonth(month.getMonth() - 1);
      this.currentDate = month;
      this.setDaysInMonth(month.getMonth());
      this.makeDaysOfMonth(this.daysInMonth);

      this.checkDay()
    },
    setDaysInMonth(month) {
      // Определяем количество дней в текущем месяце
      this.daysInMonth = new Date(this.currentDate.getFullYear(), month + 1, 0).getDate();
    },
    getWeekDay(date){
        return this.weekDays[date.getDay()]
    },

    isVisible(){
      this.showCalendar = !this.showCalendar
    }
  }

}
</script>

<style>
.container{
  border: solid black 2px;
  width: 350px;
  margin: 100px auto;
}

.top-wrapp{
  font-size:18px;
  font-weight: 500;
  padding: 10px;
}

.calendarBtn{
  border: none;
  background: none;
  cursor: pointer;
}

.top{
  margin-top:20px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  
}

table{ width: 100% }

table td{
  text-align: center;
  padding: 10px 0;
  cursor: pointer;
}

.currentDay{
  background-color: lightskyblue;
  color: #FFF;
}
.selectedDay{ border: solid #2962CC 2px }
</style>


