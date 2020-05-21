<template lang="pug">
  div.main-contents
    email-token(:locations="locations" :attractions="attractions" :num="num" :scheduleId="scheduleId" @passToken="next" @goStep1="goStep1")
</template>
<script>
import EmailToken from "./EmailToken.vue";

export default {
  name: "Step3",
  //props: ['postData'],
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
    scheduleId: {
      required: true
    },
    attractionTitle: {
      type: String,
      required: true
    },
    amount: {
      type: Number,
      required: true
    }
  },
  components: {
    EmailToken
  },
  created: function() {
    if (!(this.attractions && this.amount)) {
      this.goStep1();
      return;
    }
  },
  data() {
    return {};
  },
  watch: {
    attractionTitle: function(val) {
      if (!val) {
        this.goStep1();
      }
    },
    amount: function(val) {
      if (!val) {
        this.goStep1();
      }
    }
  },
  methods: {
    next: function(name, email, pincode, startDate, startTime) {
      this.$router.push({
        name: "Step4",
        params: {
          locations: this.locations,
          attractions: this.attractions,
          num: this.num,
          scheduleId: this.scheduleId,
          email: email,
          pincode: pincode,
          name: name,
          attractionTitle: this.attractionTitle,
          startDate: startDate,
          startTime: startTime,
          amount: this.amount
        }
      });
    },
    prev: function() {
      this.$router.push({
        name: "Step2",
        params: {
          locations: this.locations,
          attractions: this.attractions,
          num: this.num,
          attractionTitle: this.attractionTitle,
          amount: this.amount
        }
      });
    },
    goStep1: function() {
      this.$router.push({
        name: "Step1",
        params: {
          locations: this.locations,
          attractions: this.attractions
        }
      });
    }
  }
};
</script>

