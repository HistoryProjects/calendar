<template>
    <div class="date">
        <button @click="prevMonth" class="arrow">
            <img src="../assets/prev.png" alt="prev" />
        </button>

        <p>{{ currentMonth }}</p>

        <button @click="nextMonth" class="arrow">
            <img src="../assets/next.png" alt="next" />
        </button>
    </div>
</template>

<script>
export default {
    name: 'CalendarTop',
    props:{
        currentDate: Object,
        language: String
    },
    data(){
        return{
            test: "test"
        }
    },
    computed: {
        currentMonth() {
            const options = { month: 'long', year: 'numeric' };
            
            return this.currentDate.toLocaleDateString(this.language, options);
        }
    },
    methods: {
        nextMonth(){
            this.$emit('nextMonth')
        },
        prevMonth(){
            this.$emit('prevMonth')
        }
    }
}
</script>

<style scoped>
.date{
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px;
    font-size: 18px;
    font-weight: bold;
}

.arrow{
   background: none;
   border: none;
   cursor: pointer;
}
</style>