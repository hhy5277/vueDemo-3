<template>
  <transition name="datePicer-Toggle">
    <!-- stop bubble-->
    <div class="k-datePicker" v-if="visible" v-on:click.stop v-bind:style="position">
      <div class="datePane">
        <transition-group :name="animateTitle" class='titlePanel' tag='div'>
          <div class="yearBox" key="day" v-show="panelType =='day'">
            <ul>
              <li class="previousBtn" v-on:click="changeMonth('previous')"><i class="kDatePicker k-previous"></i></li>
              <li class="Title" v-on:click="changeType('month')">
                <div class="Title-ul">
                  <transition-group :name='changeTiltle' class="title-year" tag="div">
                    <div v-bind:key="tmpYear">{{tmpYear}}</div>
                  </transition-group>
                  <transition-group :name='changeTiltle' class="title-month" tag="div">
                    <div v-bind:key="tmpMonth">{{monthList[tmpMonth]|monthF(language)}}</div>
                  </transition-group>
                </div>
              </li>
              <li class="nextBtn" v-on:click="changeMonth('next')"><i class="kDatePicker k-next"></i></li>
            </ul>
          </div>
          <div class="yearBox" key="month" v-show="panelType =='month'">
            <ul>
              <li class="previousBtn" v-on:click="changeYear('previous')"><i class="kDatePicker k-previous"></i></li>
              <li class="Title">
                <transition-group :name='changeTiltle' class="Title-ul" tag="div">
                  <div v-bind:key="tmpYear" v-on:click="changeType('year')">{{tmpYear}}</div>
                </transition-group>
              </li>
              <li class="nextBtn" v-on:click="changeYear('next')"><i class="kDatePicker k-next"></i></li>
            </ul>
          </div>
          <div class="yearBox" key="year" v-show="panelType=='year'">
            <ul>
              <li class="previousBtn" v-on:click="changeYearRange('previous')"><i class="kDatePicker k-previous"></i></li>
              <li class="Title">
                <transition-group :name='changeTiltle' class="Title-ul" tag="div">
                  <div v-bind:key="yearList[0]"> {{yearList[0]}}-{{yearList[yearList.length-1]}}</div>
                </transition-group>
              </li>
              <li class="nextBtn" v-on:click="changeYearRange('next')"><i class="kDatePicker k-next"></i></li>
            </ul>
          </div>
          <div class="yearBox" key="hour" v-if="panelType =='hour'">
            <ul>
              <li class="previousBtn" style="width:15px" v-on:click="changeDay('previous')"><i class="kDatePicker k-previous"></i></li>
              <li class="Title" style="width:161px;padding: 0" v-on:click="changeType('day')">
                <div class="Title-ul">
                  <transition-group :name='changeTiltle' class="title-year" tag="div">
                    <div v-bind:key="tmpYear">{{tmpYear}}</div>
                  </transition-group>
                  <transition-group :name='changeTiltle' class="title-month" tag="div">
                    <div v-bind:key="tmpMonth">{{monthList[tmpMonth]|monthF(language)}}</div>
                  </transition-group>
                  <transition-group :name='changeTiltle' class="title-month" tag="div">
                    <div v-bind:key='tmpDay'>{{tmpDay}}</div>
                  </transition-group>
                </div>
              </li>
              <li class="nextBtn" style="width:15px" v-on:click="changeDay('next')"><i class="kDatePicker k-next"></i></li>
            </ul>
          </div>
          <div class="yearBox" key="minute" v-if="panelType =='minute'">
            <ul>
              <li class="previousBtn" style="width:15px" v-on:click="changeMinute('previous')"><i class="kDatePicker k-previous"></i></li>
              <li class="Title" style="width:161px;padding: 0" v-on:click="changeType('hour')">
                <div class="Title-ul">
                  <transition-group :name='changeTiltle' class="title-year" tag="div">
                    <div v-bind:key="tmpYear">{{tmpYear}}</div>
                  </transition-group>
                  <transition-group :name='changeTiltle' class="title-month" tag="div">
                    <div v-bind:key="tmpMonth">{{monthList[tmpMonth]|monthF(language)}}</div>
                  </transition-group>
                  <transition-group :name='changeTiltle' class="title-month" tag="div">
                    <div v-bind:key='tmpDay'>{{tmpDay}}</div>
                  </transition-group>
                </div>
              </li>
              <li class="nextBtn" style="width:15px" v-on:click="changeMinute('next')"><i class="kDatePicker k-next"></i></li>
            </ul>
          </div>
        </transition-group>
        <transition-group :name="animatePanel" class="chooseBox" tag='div'>
          <div class="chooseYear" key='year' v-show="panelType=='year'">
            <!--for 12 year-->
            <transition-group :name='animateMonth' class="change-year-Box" tag="div">
              <ul class="yearList" v-bind:key="yearList[0]">
                <!-- <li class="previousChoose">2011</li>-->
                <li v-for="year in yearList"
                    v-on:click="selectYear(year)"
                    v-bind:class="{singleChoosed:isSelected('year',year),canNotChoose:!validYear(year)}"
                >{{ year }}
                </li>
                <!--<li class="previousChoose">2022</li>-->
              </ul>
            </transition-group>
          </div>
          <div class="chooseMonth" key='month' v-show="panelType=='month'">
            <!--for 12 month-->
            <transition-group :name='animateMonth' class="change-year-Box" tag="div">
              <ul class="monthList" v-bind:key="tmpYear">
                <li v-for="(month,index) in monthList"
                    v-on:click="selectMonth(index)"
                    v-bind:class="{singleChoosed:isSelected('month',index),canNotChoose:!validMonth(index)}"
                >{{ month|monthF(language)}}
                </li>
              </ul>
            </transition-group>
          </div>
          <div class="chooseDay" key='day' v-show="panelType=='day'">
            <ul class="dayTitle">
              <!-- for language title-->
              <li v-for="week in weekList">{{week|weekF(language)}}</li>
            </ul>
            <transition-group :name='animateMonth' class="change-Month-Box" tag="div">
              <ul class="dayList" v-bind:key="tmpMonth">
                <!--  <li class="previousChoose">1</li>-->
                <li v-for="day in daylist"
                    v-bind:class="{previousChoose:day.previousMonthDay||day.nextMonthDay,singleChoosed:isSelected('day',day),canNotChoose:!validDay(day)}"
                    v-on:click="selectDay(day)"
                >{{Format.day=='dd'&&day.value<10?'0':''}}{{day.value}}
                </li>
                <!-- <li class="singleChoosed">18</li>-->
                <!-- <li class="mulitChoosedHead">25</li>
                 <li class="mulitChoosed">31</li>
                 <li class="mulitChoosedEnd">32</li>-->
                <!-- <li class="canNotChoose">39</li>-->
              </ul>
            </transition-group>
          </div>
          <div class="chooseHour" key='hour' v-if="panelType=='hour'">
            <transition-group :name='animateMonth' class="change-hour-Box" tag="div">
              <!--<div class="change-hour-Box">-->
              <ul class="hourList" v-bind:key="tmpDay">
                <!--  <li class="previousChoose">1</li>-->
                <li v-for="hour in hourList"
                    v-bind:class="{singleChoosed:isSelected('hour',hour),canNotChoose:!validHour(hour)}"
                    v-on:click="selectHour(hour)"
                >{{Format.hour=='HH'&&hour<10?'0':''}}{{hour}}:00
                </li>
                <!-- <li class="singleChoosed">18</li>-->
                <!-- <li class="mulitChoosedHead">25</li>
                 <li class="mulitChoosed">31</li>
                 <li class="mulitChoosedEnd">32</li>-->
                <!-- <li class="canNotChoose">39</li>-->
              </ul>
              <!-- </div>-->
            </transition-group>
          </div>
          <div class="chooseMinute" key="minute" v-if="panelType=='minute'">
            <transition-group :name='animateMonth' class="change-minute-Box" tag="div">
              <!--<div class="change-hour-Box">-->
              <ul class="hourList" v-bind:key="minuteIndex||tmpHour">
                <!--  <li class="previousChoose">1</li>-->
                <li v-for="minute in minuteList"
                    v-bind:class="{singleChoosed:isSelected('minute',minute),canNotChoose:!validMinute(minute)}"
                    v-on:click="selectMinute(minute)"
                >{{Format.hour=='HH'&&tmpHour<10?'0':''}}{{tmpHour}}:{{Format.minute=='mm'&&minute.M<10?'0'+minute.M:minute.M}}
                </li>
                <!-- <li class="singleChoosed">18</li>
                 <li class="mulitChoosedHead">25</li>
                 <li class="mulitChoosed">31</li>
                 <li class="mulitChoosedEnd">32</li>
                 <li class="canNotChoose">39</li>-->
              </ul>
              <!-- </div>-->
            </transition-group>
          </div>
        </transition-group>
      </div>
    </div>
  </transition>
</template>

<script src="./datepicker.js"></script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" rel="stylesheet/scss" src="./css/datapicker.scss" scoped>
</style>
