<template>
    <div>
        <p v-for="weatherDay in weatherFiveDays.list" :key="weatherDay.dt">{{weatherDay.dt_txt}} -- {{weatherDay.main.temp}}</p>
    </div>
</template>

<script>
    export default {
        name: "ListDays",
        data(){
            return{

            }
        },
        props: [
            'weatherFiveDays'
        ],
        computed:{
            listFiveDays(){
                let counterday = this.weatherFiveDays.list[0].dt
                let result = this.weatherFiveDays.list.filter((item) => {
                    if(item.dt === counterday){
                        counterday += 86400
                        return item
                    }
                })
                return result
            }
        }
    }
</script>