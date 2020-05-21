<template>
  <div>
    <transition name="v-modal">
      <div class="v-modal-wrapper" v-if="isActive" @click.prevent="close()">
        <div class="v-modal" :aria-hidden="!isActive" @click.stop="">
          <button class="v-modal-close" @click.prevent="close()"></button>
          <div class="v-modal-timePicker" v-cloak slot="v-modal-body">
            <div class="time-table-header">{{day}}</div>
            <div class="time-table-body">
              <ol class="time-table">
                <li v-for="schedule in schedules" :key="schedule.id">
                  <div v-if="isReservable(schedule)">
                    <a href="javascript:void(0);" class="timePicker-link" @click="selectSchedule(schedule)">
                      <span class="label-available">予約可</span>
                      <span class="playTime">{{getTimeStr(schedule.startTime)}}</span>
                      <span class="vacancyStatus">空席状況： {{schedule.maxCapacity - schedule.rsvNum}}/{{schedule.maxCapacity}}</span>
                    </a>
                  </div>
                  <div v-else>
                    <div class="timePicker-link _is-fulled">
                      <span class="label-fulled">満員</span>
                      <span class="playTime">{{getTimeStr(schedule.startTime)}}</span>
                      <span class="vacancyStatus">空席状況： {{schedule.maxCapacity - schedule.rsvNum}}/{{schedule.maxCapacity}}</span>
                    </div>
                  </div>
                </li>
              </ol>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>
<script>
import Api from "../utils/api.js";
import DateTimeUtils from "../../utils/datetime-utils.js";

export default {
  name: "TimePicker",
  props: {
    locations: {
      required: true
    },
    attractions: {
      required: true
    },
    num: {
      required: true
    },
    date: {
      type: Date
    }
  },
  data() {
    return {
      isActive: false,
      day: "",
      schedules: []
    };
  },
  computed: {},
  watch: {
    $route: function() {
      this.close();
    },
    date: function(val) {
      if (val) {
        this.open();
      } else {
        if (this.isActive) {
          this.close();
        }
      }
    }
  },
  methods: {
    clearData: function() {
      this.isActive = false;
      this.day = "";
      this.schedules = [];
    },
    open: function() {
      let nextDate = new Date(this.date);
      nextDate.setDate(nextDate.getDate() + 1);

      const requestURL = Api.url.getLocAtraSchedules(
        this.locations,
        this.attractions
      );
      const op = {
        credentials: true,
        params: {
          startDate: DateTimeUtils.formatDate(this.date),
          endDate: DateTimeUtils.formatDate(nextDate)
        }
      };
      this.$http
        .get(requestURL, op)
        .then(this.handleApiSuccess, this.handleApiError);
    },
    close: function() {
      document
        .getElementsByTagName("html")[0]
        .classList.toggle("v-modal-is-locked", false);
      this.clearData();
      this.$emit("close");
    },
    selectSchedule: function(schedule) {
      this.close();
      this.$emit("selectDate", schedule);
    },
    getTimeStr: function(time) {
      return DateTimeUtils.convertTimeToDisplay(time);
    },
    handleApiSuccess: function(response) {
      const allSchedules = response.body.schedules;
      const startTimes = allSchedules
        .map(function(elm, idx) {
          return elm.startTime;
        })
        .filter(function(elm, idx, arr) {
          return arr.indexOf(elm) == idx;
        });
      for (var i = 0; i < startTimes.length; i++) {
        this.schedules.push(
          allSchedules
            .filter(function(elem) {
              return elem.startTime == startTimes[i];
            })
            .reduce(function(prev, cur) {
              return prev.rsvNum <= cur.rsvNum ? prev : cur;
            })
        );
      }
      document
        .getElementsByTagName("html")[0]
        .classList.toggle("v-modal-is-locked", true);

      this.day = DateTimeUtils.format(this.date, "YYYY/MM/DD");
      this.isActive = true;
    },
    handleApiError: function(response) {
      // 全部サーバエラー
      if (
        window.confirm(
          "読込エラーが発生しました。再読込しますか？しない場合は最初に戻ります"
        )
      ) {
        this.open();
      } else {
        this.$emit("goStep1");
      }
    },
    isReservable: function(schedule) {
      if (
        this.attractions == 1 ||
        this.attractions == 2 ||
        this.attractions == 3 ||
        this.attractions == 4
      ) {
        return schedule.rsvNum == 0 && schedule.maxCapacity >= this.num;
      } else {
        return schedule.maxCapacity - schedule.rsvNum >= this.num;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@charset "utf-8";
$_fontSize-min: 0.75rem;
$_fontSize-S: 0.875rem;
$_fontSize-M: 1.125rem;
$_fontSize-L: 1.25rem;
$_fontSize-LL: 1.5rem;
$_fontSize-max: 1.875rem;
$_gutter: 20px;

.v-modal-is-opened {
  display: block;
}

// ---------------------------
.v-modal-timePicker {
  text-align: left;
  .time-table-header {
    font-size: 16px;
    text-align: center;
    border-bottom: 1px solid #ccc;
    background: #fff;
    padding: 20px;
  }
  .time-table-body {
    max-height: 500px;
    overflow-y: auto;
  }
  .time-table {
    background: linear-gradient(to bottom, #8a67ff 0%, #7950ff 100%);
    width: 100%;
    padding: 2px;
    .label-available,
    .label-few,
    .label-fulled {
      color: #fff;
      display: block;
      padding: 5px 10px 4px;
      text-align: center;
      font-size: $_fontSize-S;
    }
    .label-available {
      background: #3f9bd1;
      color: #fff;
      display: block;
      padding: 5px 10px 4px;
    }
    .label-few {
      background: #a9a539;
    }
    .label-fulled {
      background: #bbb;
    }
    li {
      margin-bottom: 1px;
      background: #fff;
      .timePicker-link,
      .timePicker-link-disabled {
        color: #333;
        display: flex;
        justify-content: space-between;
        align-items: center;
        width: 100%;
        text-align: left;
        padding: 10px;
        &:hover {
          text-decoration: none;
        }
      }
      ._is-fulled {
        background: #ccc;
      }
      .playTime {
        font-size: 18px;
        flex-grow: 1;
        padding: 0 20px;
      }
      .vacancyStatus {
        font-size: $_fontSize-S;
      }
    }
  }
  .v-modal-timePicker-title {
    font-size: $_fontSize-L;
    text-align: center;
    margin-bottom: $_gutter;
  }
}
</style>
