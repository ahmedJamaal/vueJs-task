<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="10">
      <v-card class="logo py-4 d-flex justify-center">
        <NuxtLogo />
        <VuetifyLogo />
      </v-card>
    </v-col>
    <v-col cols="12">
      <v-card>
        <v-card-title class="headline">
          <div class="headline-header">
            <v-btn class="mx-2" fab dark color="indigo" @click="addTimeSheet">
              <v-icon dark> mdi-plus </v-icon>
            </v-btn>
            Time Sheet
          </div>
        </v-card-title>
        <v-simple-table>
          <template #default>
            <thead>
              <tr>
                <th>Date</th>
                <th>WeekDay</th>
                <th>In</th>
                <th>Out</th>
                <th>Lunch</th>
                <th>status</th>
                <th>+ / -</th>
                <th>plan</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in timeSheet" :key="index">
                <td>
                  <v-chip class="ma-2" color="success" small>
                    {{ item.date }}
                  </v-chip>
                </td>
                <td>{{ item.weekDay }}</td>
                <td>
                  <vue-timepicker
                    v-model="item.in"
                    hide-dropdown
                    manual-input
                    format="HH:mm"
                    placeholder="IN Time"
                  ></vue-timepicker>
                </td>
                <td>
                  <vue-timepicker
                    v-model="item.out"
                    hide-dropdown
                    manual-input
                    format="HH:mm"
                    placeholder="Out Time"
                  ></vue-timepicker>
                </td>
                <td>
                  <vue-timepicker
                    v-model="item.lunch"
                    hide-dropdown
                    manual-input
                    format="HH:mm"
                    placeholder="Lunch Time"
                  ></vue-timepicker>
                </td>
                <td>
                  <v-select v-model="item.status" :items="status"></v-select>
                </td>
                <td>
                  {{ getCompensation(item, index) }}
                </td>
                <td>
                  {{ getPlan(item, index) }}
                </td>
              </tr>
              <tr class="pink--text">
                <td class="title">Totals</td>
                <td colspan="6" class="text-right">{{ TotalCompensation }}</td>
                <td class="text-right">{{ calculateTotalPlan }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import VueTimepicker from 'vue2-timepicker'

export default {
  name: 'IndexPage',
  components: {
    VueTimepicker,
  },
  data() {
    return {
      status: [
        { text: 'At Work', value: 'work' },
        { text: 'Holiday', value: 'holiday' },
      ],
      weekdays: [
        'Sunday',
        'Monday',
        'Tuesday',
        'Wednesday',
        'Thursday',
        'Friday',
        'Saturday',
      ],
      timeSheet: [
        {
          date: '28/04',
          weekDay: 'Saturday',
          in: '09:30',
          out: '18:00',
          lunch: '01:00',
          status: 'work',
          compensation: 0,
          plan: 8,
        }
      ],
    }
  },
  computed: {
    calculateTotalPlan() {
      // eslint-disable-next-line dot-notation
      return this.timeSheet.reduce((a, b) => a + (b['plan'] || 0), 0)
    },
    TotalCompensation() {
      // eslint-disable-next-line no-var
    return this.timeSheet.reduce((a, b) => parseFloat(a) + (parseFloat(b.compensation) || 0), 0)


    },
  },
  methods: {
    getPlan(val, index) {
      // eslint-disable-next-line no-console
     if (val.status === 'work') {
        // this.timeSheet[index].plan = val.plan
        return val.plan
      } else {
        // this.timeSheet[index].plan = 0
        return 0
      }
    },
    getCompensation(val, index) {
      // eslint-disable-next-line no-console
      const time = this.calculateCompensation(val)

      // eslint-disable-next-line prefer-const
      let { hour, min } = time
      console.log(time);
/*
      if (time.min < 0) {
        hour = time.hour - 1
        min = time.min + 60
      }
     */
    // if min negitive sign
     if (time.min < 0) {
       if(time.min % 60 === 0){
         hour = time.hour + time.min / 60;
        min=0;
       }else if( time.hour ===0 ){
         hour = '-0';
        min=Math.abs(time.min);
       }else{
          hour = time.hour;
           min=Math.abs(time.min);
       }

      }
      // eslint-disable-next-line no-console
      this.timeSheet[index].compensation = `${hour}.${min*100 / 60}`
      // this.timeSheet[index].plan = `${this.FormatNumber(hour)}:${this.FormatNumber(min)}`
      console.log(`${hour}.${min*100 / 60}`)
      return `${hour}.${min*100 / 60}`
    },
    calculateCompensation(item) {
      if (item.status === 'holiday') {
        return { hour: 0, min: 0 }
      } else {
        const inArray = item.in.split(':')
        const OutArray = item.out.split(':')
        const LunchArray = item.lunch.split(':')
        // eslint-disable-next-line no-unused-vars
        const hour = OutArray[0] - inArray[0] - LunchArray[0] - item.plan
        const min = OutArray[1] - inArray[1] - LunchArray[1]

        // eslint-disable-next-line object-shorthand
        return { hour: hour, min: min }
      }
    },

    FormatNumber(j) {
      return ('0' + j).slice(-2)
    },
    addTimeSheet() {
      const d = new Date()
      /// d.getD
      this.timeSheet.push({
        date: `${this.FormatNumber(d.getDate())}/${this.FormatNumber(
          d.getMonth()
        )}`,
        weekDay: this.weekdays[d.getDay()],
        in: '08:00',
        out: '15:00',
        lunch: '00:00',
        status: 'work',
        compensation: 0,
        plan: 8,
      })
    },
  },
}
</script>
<style>
.controls {
  display: none;
}
th {
  text-transform: uppercase;
}
td,
th {
  text-align: center;
}
</style>
