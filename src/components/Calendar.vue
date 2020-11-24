<template>
  <div>
    <Toolbar dark :currentday="currentday" :viewdays="viewdays" @goTo="goTo" @changeView="changeView">
      <template v-slot:toolbar="{today, forward, back, title, daily, weekly, monthly, type}">
        <slot name="toolbar" :today="today" :forward="forward" :back="back" :title="title" :daily="daily" :weekly="weekly" :monthly="monthly" :type="type">
        </slot>
      </template>
    </Toolbar>

    <transition name="mapfade" appear>
      <v-simple-table dense v-if="transition">

        <!--Header for Daily view-->

        <thead v-if="viewdays<7">
          <tr>
            <td class="calDies">
            </td>
          </tr>
          <tr class="calDies">
            <td class="text-center overline ">
              Itineraris
            </td>
            <th  v-for="hour in 24" :key="hour" style="text-align: center;">
              <a>{{(hour-1+currentHour)%24}} h</a>
            </th>
          </tr>
        </thead>

        <!--Header for Weekly and Montly views-->

        <thead v-else-if="viewdays>=7">
          <tr class="calDies">
            <td></td>
            <td v-for="(day,i) in daysofweek"
                :key="i"
                style="border-bottom: none; text-align: center;  text-transform: capitalize;"
                >
              {{day}}
            </td>
          </tr>
          <tr class="calDies">
            <td class="text-center overline ">
              Itineraris
            </td>
            <th v-for="(day, i) in daysofmonth.day"
                :key="daysofmonth.days[i]"
                style=" border-bottom: none; text-align: center;"
                @click="goTo(daysofmonth.days[i])"
                :class="[daysofmonth.days[i] === todaydate ? 'today' : '']"
                >
              <a>{{day}}</a>
            </th>
          </tr>
        </thead>


        <tbody>


          <!-- Daily View -->
          <template v-if="viewdays<7">
            <tr v-for="(it,i) in exampleit" :key="it.itinerari_id">
              <template v-show="it.itinerary_id === focusonitinerari || focusonitinerari === 0"> <!--So we can focus on a row-->
              <td
                class="nomsitineraris"
                @click="selectItinerari(it.itinerary_id)">
                <a>
                        {{it.itinerary_id}}
                </a>
              </td>
              <template v-if="typeof dailyMapedDates[i]!=='object'">
                <template v-for="(i) in 24">
                  <td
                  class="higherheight"
                  :key="i"
                  style="background: #f1edeecc;border-bottom: 1px solid #cfd0d2 !important; text-align: center; color: #fff;font-size: 12px;border-right: 1px solid #999 !important;"
                  />
              </template>
            </template>
            <template v-else>
              <template v-for="(i,hour) in dailyMapedDates[i]">
                <td
                class="higherheight"
                v-if="typeof i ==='number' && hour<24-i"
                :key="i+hour"
                style="background: #f1edeecc;bordcurrentDayer-bottom: 1px solid #cfd0d2 !important; text-align: center; color: #fff;font-size: 12px;border-right: 1px solid #999 !important;"
                />
                <td
                class="nopadding higherheight"
                v-else-if="typeof i ==='object'"
                :colspan="i.span+1"
                :key="i+hour"
                style=" text-align: center; color: #fff;font-size: 12px;"

                >
                  <v-btn
                    :color="i.color+'8C'"
                    dark
                    small
                    tile
                    depressed
                    block
                    :ripple="false"
                    style="border-right: 1px solid grey !important;"
                    class="tooltip"

                  >
                  <v-icon
                    color="white"
                    class="mr-1"
                  >
                    mdi-car
                  </v-icon>
                  <template v-if="i.span+1 > 1">
                    {{i.matricula}}
                  </template>
                    <span class="tooltiptextdaily">
                      <div class="chip">
                        <span class="icon mx-auto">
                            <i class="mdi mdi-car"></i>
                        </span>
                        <span>{{i.matricula}}</span>
                      </div>
                      <br/>
                      <div class="chip">
                        <span class="icon mx-auto">
                            <i class="mdi mdi-clock-time-nine"></i>
                        </span>
                        <span>{{i.hora}}h - {{hour + i.span+1 > 9 ? hour + i.span+1 : '0' + parseInt(hour + i.span + 1) }}h</span>
                      </div>
                    </span>
                  </v-btn>
                </td>
              </template>
            </template>
          </template>
        </tr>
      </template>


          <!-- Weekly view -->

          <template v-else-if="viewdays===7">
            <tr v-for="(it,j) in exampleit" :key="it.itinerari_id">
              <template v-if="it.itinerary_id === focusonitinerari || focusonitinerari === 0">
              <td
                class="nomsitineraris"
                @click="selectItinerari(it.itinerary_id)">
                <a>
                        {{it.itinerary_id}}
                </a>
              </td>
              <template v-if="viewdays != startindex">
                <td v-for="(data,i) in mapDates[j]"
                    :key="i"
                    :style="[Math.floor((i+currentDayOfWeek)%7/5) ?  {background: '#dcd2d399'} :  {background: '#f1edeecc'},
                    daysofmonth.days[i] === todaydate ?
                    {'border-left': '1px solid #222 !important','border-right': '1px solid #222 !important'} :
                    {'border-right': '1px solid #ccc !important'}
                    ]"
                    style="border-bottom: 1px solid #ccc !important; text-align: center; padding: 0px !important;"
                    class="tooltip"
                >
                  <div v-if="typeof data === 'string'">
                      <v-btn
                        :color="it.color+'8C'"
                        dark
                        small
                        tile
                        depressed
                        block
                        :ripple="false"
                        class="tooltip my-0"

                      >
                      <v-icon
                        left
                        color="white"
                        class="mr-1"
                      >
                        mdi-car
                      </v-icon>
                        {{it.vehicle_registration}}
                        <span class="tooltiptext2">
                          <div class="chip">
                            <span class="icon mx-auto">
                                <i class="mdi mdi-car"></i>
                            </span>
                            <span>{{it.vehicle_registration}}</span>
                          </div>
                          <br/>
                          <div class="chip">
                            <span class="icon mx-auto">
                                <i class="mdi mdi-clock-time-nine"></i>
                            </span>
                            <span>{{data.slice(0,2)}} - {{ (parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1) > 9 ? (parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1) : '0'+(parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1)}}h</span>
                          </div>
                        </span>
                      </v-btn>
                  </div>
                </td>
              </template>
            </template>
            </tr>
          </template>
          <template v-else-if="viewdays>7">
            <tr v-for="(it,j) in exampleit" :key="it.itinerari_id">
              <template v-if="it.itinerary_id === focusonitinerari || focusonitinerari === 0">
              <td
                class="nomsitineraris"
                @click="selectItinerari(it.itinerary_id)">
                <a>
                        {{it.itinerary_id}}
                </a>
              </td>
              <template v-if="viewdays != startindex">
              <td v-for="(data,i) in mapDates[j]"
                  :key="i"
                  :style="[typeof data === 'string' ? {background: it.color+'8C'} :
                  Math.floor((Math.abs(data)+currentDayOfWeek)/7)%2 ?  {background: '#dcd2d399'} :  {background: '#f1edeecc'},
                  daysofmonth.days[i] === todaydate ?
                  {'border-left': '1px solid #222 !important','border-right': '1px solid #222 !important'} :
                  {'border-right': '1px solid #ccc !important'}
                  ]"
                  style="border-bottom: 1px solid #ccc !important; text-align: center;
                  padding: 0 5px !important; font-size: 13px; font-weight: 700;"
                  class="tooltip"
              >
                <div v-if="typeof data === 'string'">
                    {{data.slice(0,2)}}h
                    <span class="tooltiptext">
                      <div class="chip">
                        <span class="icon">
                            <i class="mdi mdi-car"></i>
                        </span>
                        <span>{{it.vehicle_registration}}</span>
                      </div>
                      <br/>
                      <div class="chip">
                        <span class="icon">
                            <i class="mdi mdi-clock-time-nine"></i>
                        </span>
                        <span>{{data.slice(0,2)}} - {{ (parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1) > 9 ? (parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1) : '0'+(parseInt(data.slice(0,2)) + parseInt(data.slice(2))+1)}}h</span>
                      </div>
                    </span>
                </div>
                <!-- NUMERO DE DIA EN LAS CELDAS VACIAS, ES REDUNDANTE? div v-else style="color: #b6b6b6">
                  {{(Math.abs(data)+currentDayOfMonth)%(currentDaysOfMonth)}}
                </div -->
              </td>
              </template>
            </template>
            </tr>
          </template>
        </tbody>

      </v-simple-table>

  </transition>

  <transition name="mapfade">

  </transition>

  </div>

</template>

<script>
import axios from 'axios'
import moment from 'moment'
import Toolbar from '@/components/Toolbar'
import dataexample from '@/assets/DataExample.json'
  export default {
    name: 'Calendar',
    components: {
      Toolbar
    },
    props: {
      borderBodyColor: {
        type: String,
        default: '#fff'
      },
      primaryColor: {
        type: String,
        default: '#dcd2d399'
      },
      secondaryColor: {
        type: String,
        default: '#f1edeecc'
      },
      primaryTextColor: {
        type: String,
        default: '#b6b6b6'
      },
      secondaryTextColor: {
        type: String,
        default: '#fff'
      },
      borderHeadColor: {
        type: String,
        default: '#b6b6b6'
      },
      borderTodayColor: {
        type: String,
        default: '#222'
      },
      locale: {
        type: String,
        default: 'es'
      },
    },
    mounted() {
      this.startindex= this.currentDayOfYear;
    },
    data: () => ({
      focusonitinerari: 0,
      todaydate: moment().format('l'),
      initialday: moment().startOf('month'),
      currentday: moment().startOf('day'),
      viewdays: parseInt(moment().daysInMonth()),
      startindex: 0,
      datalength: 7,
      transition: true,
      exampleit: null
    }),
    created() {
      this.fetchItems();
    },
    methods: {
      fetchItems() {
       axios
        .get('http://localhost:3000/calendar')
        .then(response => {
          this.exampleit = response.data
        })
      },
      goTo(day) {
        let nextday = moment(day);
        // Set current date to the day requested
        // Get diff between fristday with data available and current date
        if ( this.viewdays > 7 ) {
          this.viewdays = parseInt(moment(day).daysInMonth());
        }
        this.currentday = moment(day).startOf('day');
        let firstdaywithdata  = moment(this.exampleit[0].initial_day).startOf('day')
        let difftoday = -1*firstdaywithdata.diff(nextday,'days');
        if ( difftoday >= 0 ) {
          this.startindex = difftoday;
        } else {
          // WE HAVE TO ASK FOR MORE DATA
          this.startindex = this.viewdays
        }
      },
      goToHour(h) { //Unused function to move from hours on Daily View, we have to change dailyMapedDates to make it work
          let diff = 0;

          if (this.currentHour > h) {
            this.startindex += 1;
            if(h===0) {
              diff = 24 - this.currentHour;
            }
            else {
              diff = 24 - this.currentHour + h;
            }
          }
          else {
            diff = h - this.currentHour;
          }
          this.currentday = moment(this.currentday).add(diff, 'hours');
      },
      selectItinerari(focus) {
        if ( this.focusonitinerari === focus) {
          this.focusonitinerari = 0;
        } else {
          this.focusonitinerari = focus;
        }
      },
      changeView(type) {
        //Handle changes of type of view from Toolbar buttons
        //Options: 1,7 or 31 -- DAILY, WEEKLY, MONTHLY
        this.viewdays = type;
        this.goTo(moment(this.currentday))
      }
    },
    watch: {},
    computed: {
      hasToolbarSlot() {
        console.log(!!this.$slots)
        return !!this.$slots.toolbar
      },
      daysofweek() {
        let index = Array.apply(null, Array(this.viewdays)).map( (d, i) => (this.currentDayOfWeek + i) % 7 )
        let days = index.map(function (_, i) {
                    return moment().startOf('week').isoWeekday(_ + 1).format('dd');
                });
        return days
      },
      daysofmonth() {
        const b = [...Array(this.viewdays).keys()]
        let currentmonthdays = parseInt(moment(this.currentday).daysInMonth()) + 1
        let days = {
          day: b.map(
            (d, i) =>
              ((this.currentDayOfMonth + i) % currentmonthdays) +
              Math.floor((this.currentDayOfMonth + i) / currentmonthdays)
          ),
          days: b.map((d, i) =>
            moment(this.currentday)
              .add(i, 'days')
              .format('l')
          )
        }
        return days
      },
      currentHour() {
        return parseInt(
          moment(this.currentday)
            .format('H')
        )
      },
      currentDayOfWeek() {
        return parseInt(
          moment(this.currentday)
            .format('e')
        )
      },
      currentDayOfMonth() {
        return parseInt(
          moment(this.currentday)
            .format('D')
        )
      },
      currentDayOfYear() {
        return parseInt(
          moment(this.currentday)
            .dayOfYear()
        )-1;
      },
      currentDaysOfMonth() {
        return parseInt(
          moment(this.currentday)
            .daysInMonth()
        )
      },
      mapDates() {
        return this.exampleit.map( it =>
          {
            return it.dates.slice(this.startindex,this.viewdays+this.startindex).map( (day, i) =>
              {
                if (day!==0) {
                  return moment(day.substr(0,day.indexOf("/"))).format('HH')+moment.duration(day.substr(1+day.indexOf("/"))).hours();
                } else return -i;
              }
            )
          }
        )
      },
      dailyMapedDates() {
        let daily = [];
        let threedays = this.exampleit.map( it => it.dates.slice(this.startindex-1, this.viewdays+this.startindex+1));
        for ( let it=0; it<this.exampleit.length; it++) {
          let prevday = threedays[it][0];
          let day = threedays[it][1];
          let span = 0;
          let arr = [];
          for ( let h=0; h<24; h++) {
            if (prevday) {
              let hora = parseInt(moment(prevday.substr(0,prevday.indexOf("/"))).format('HH'));
              let duration = moment.duration(prevday.substr(1+prevday.indexOf("/"))).hours();
              if (h<=(hora +duration)%24 && hora + duration > 23 ) {
                  span = (hora + duration)%24;
                      arr.push({
                        hora: moment(prevday.substr(0,prevday.indexOf("/"))).format('HH'),
                        duration: moment.duration(prevday.substr(1+prevday.indexOf("/"))).hours() + 'H' + moment.duration(prevday.substr(1+prevday.indexOf("/"))).minutes() + 'M',
                        span: (hora + duration)%24,
                        id: this.exampleit[it].itinerary_id,
                        matricula: this.exampleit[it].vehicle_registration,
                        color: this.exampleit[it].color
                      })
                      h = (hora + duration)%24;
              }
            }
            if (day) {
              if(h === parseInt(moment(day.substr(0,day.indexOf("/"))).format('HH'))) {
                arr.push({
                  hora: moment(day.substr(0,day.indexOf("/"))).format('HH'),
                  duration: moment.duration(day.substr(1+day.indexOf("/"))).hours() + 'H' + moment.duration(day.substr(1+day.indexOf("/"))).minutes() + 'M',
                  span: (moment.duration(day.substr(1+day.indexOf("/"))).hours()),
                  id: this.exampleit[it].itinerary_id,
                  matricula: this.exampleit[it].vehicle_registration,
                  color: this.exampleit[it].color
                })
              }
              else {
                arr.push(0)
              }
            }
          }
          if ( arr.length ) {
            if (arr.length < 24) {
              let a = Array(23).fill(span)
              a.unshift(arr[0])
              daily.push(a);
            }
            else {
              let totalduration = arr.filter( i => typeof(i)=== 'object').reduce( (total,item) => (parseInt(item.hora) + item.span)>24 ? total + (parseInt(item.hora)+item.span)%24 : total + item.span ,0)
              daily.push(arr.map(i => typeof(i) !== 'object' ? totalduration : i));
            }
          }
          else {
            daily.push(0)
          }
        }
        return (daily);
      },
    }
  }

</script>
<style scoped>
  th {
    font-weight: 600;
    vertical-align: bottom;
    line-height: 1.49;
    vertical-align: middle;
    border-top: 1px solid #ccc;
    border-bottom: none;
  }
  td {
    min-width: 33px !important;
    height: 2.1em !important;
    text-align: center;
    padding: 0px 0.5vh !important;
  }
  td.nomsitineraris {
    width: 17vh !important;
    padding: 0px 5.5vh !important;
  }
  td.higherheight {
    height: 2em !important;
    padding:0px !important;
    vertical-align:top;
  }
  td.nopadding {
    padding: 0px !important;
  }
  .today {
    border-left: 1px solid #222 !important;
    border-right: 1px solid #222 !important;
    text-align: center;
  }
  .calDies {
    text-align: center;
    color: #b6b6b6;
    padding: 5px;
  }
  .mapfade-enter-active {
    transition: opacity 0.5s;
  }
  .mapfade-leave-active {
    transition: opacity 0.5s;
  }
  .mapfade-leave-to {
    opacity: 0;
  }
  .mapfade-enter {
    opacity: 0;
  }
  .max-width-chip.v-chip {
    margin: 0px !important;
    margin-left: 4px;
    padding-left: 40px !important;
    padding-right: 40px !important;
    width: 150px;
    border-radius: 0 !important;
  }
  .max-width-chip .v-chip__content {
    line-height: 32px;
    padding-right: 30px !important;
    display: inline-block !important;
    white-space: nowrap;
    overflow: hidden;    width: 135px;

    text-overflow: ellipsis;
    position: relative;
  }
  .tooltip {
    position: relative;
    color: #fff;
    cursor: pointer;
    z-index: 30;
  }
  /* Tooltip text */
  .tooltip .tooltiptextdaily {
    position: absolute;
    visibility: hidden;
    background-color: rgba(97,97,97,.9);
    color: #fff;
    right: 0;
    bottom: 100%;
    padding: 5px 6px 5px 6px;
    z-index: 30;
    font-weight: 700;
  }
  .tooltip .tooltiptext {
    position: absolute;
    visibility: hidden;
    background-color: rgba(97,97,97,.9);
    color: #fff;
    width: 135px;
    bottom: 100%;
    right: 0%;
    padding: 5px 6px 5px 6px;
    z-index: 30;
    font-weight: 700;
  }
  .tooltip .tooltiptext2 {
    position: absolute;
    visibility: hidden;
    background-color: rgba(97,97,97,.9);
    color: #fff;
    width: 125px;
    bottom: 100%;
    left: 50%;
    margin-left: -60px;
    padding: 5px 6px 5px 6px;
    z-index: 30;
    font-weight: 700;


  }
  /* Show the tooltip text when you mouse over the tooltip container */
  .tooltip:hover .tooltiptext {
    visibility: visible;
  }
  .tooltip:hover .tooltiptext2 {
    visibility: visible;
  }
  .tooltip:hover .tooltiptextdaily {
    visibility: visible;
  }
.chip {
    padding: 0 12px 0 12px;
    background-color: #e0e0e0;
    border-radius: 5px;
    display: inline-flex;
    margin: 2px;
    color: rgba(97,97,97,.9);
    align-items: center;
    justify-content: center;
    height: 26px;
    font-size: 12px;
    cursor: pointer;
    min-width: 105px;
}

.chip .icon {
    font-size: 2.3vh;
    color: rgba(97,97,97,.9);
    margin: -4px 4px;
}

</style>
