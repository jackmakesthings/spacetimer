<template>
  <div id="app">
    <div>
      <output class="clock digital">
        {{ getFormattedDate() }}
      </output>

      <div class="fields manual-fields">
        <!-- 				<label>Set date to</label> -->
        <button @click="setDate">Set date manually</button>
      </div>

      <div class="fields increment-fields">
        <div class="increment-field">
          <label>Increment by:</label>
          <input
            class="increment-input digital"
            v-model="incrementAmount"
            type="number"
            value="5"
          />
        </div>

        <div class="unit-boxes">
          <div class="unit-box">
            <span class="unit-label">years</span>
            <button @click="incrementBy(incrementAmount, 'year')">+</button>
            <button @click="decrementBy(incrementAmount, 'year')">-</button>
          </div>
          <div class="unit-box">
            <span class="unit-label">days</span>
            <button @click="incrementBy(incrementAmount, 'day')">+</button>
            <button @click="decrementBy(incrementAmount, 'day')">-</button>
          </div>
          <div class="unit-box">
            <span class="unit-label">hours</span>
            <button @click="incrementBy(incrementAmount, 'hour')">+</button>
            <button @click="decrementBy(incrementAmount, 'hour')">-</button>
          </div>
          <div class="unit-box">
            <span class="unit-label">minutes</span>
            <button @click="incrementBy(incrementAmount, 'minute')">+</button>
            <button @click="decrementBy(incrementAmount, 'minute')">-</button>
          </div>
          <div class="unit-box">
            <span class="unit-label">seconds</span>
            <button @click="incrementBy(incrementAmount, 'second')">+</button>
            <button @click="decrementBy(incrementAmount, 'second')">-</button>
          </div>
        </div>
      </div>
    </div>

    <div class="fields timestamp-fields">
      <button @click="saveTimestamp">
        Save timestamp
      </button>

      <dl class="timestamps">
        <div v-for="ts in savedTimestamps" :key="ts.time">
          <dt>{{ ts.time }}</dt>
          <dd>{{ ts.label }}</dd>
        </div>
      </dl>
    </div>
  </div>
</template>

<script>
// Universal time constants for this system
const SECONDS_PER_MINUTE = 60;
const MINUTES_PER_HOUR = 60;
const HOURS_PER_DAY = 30;
const DAYS_PER_YEAR = 300;

const SECONDS_PER_HOUR = SECONDS_PER_MINUTE * MINUTES_PER_HOUR;
const SECONDS_PER_DAY = SECONDS_PER_HOUR * HOURS_PER_DAY;
const SECONDS_PER_YEAR = SECONDS_PER_DAY * DAYS_PER_YEAR;

export default {
  name: "App",
  data() {
    return {
      currentSeconds: 0,
      incrementAmount: 5,
      savedTimestamps: []
    };
  },
  computed: {
    currentDate() {
      return this.secondsToDate(parseInt(this.currentSeconds, 10));
    }
  },
  methods: {
    dateToSeconds(date) {
      return (
        parseInt(date.second, 10) +
        parseInt(date.minute, 10) * SECONDS_PER_MINUTE +
        parseInt(date.hour, 10) * SECONDS_PER_HOUR +
        parseInt(date.day, 10) * SECONDS_PER_DAY +
        parseInt(date.year, 10) * SECONDS_PER_YEAR
      );
    },

    secondsToDate(seconds) {
      let date = {};
      let remainingSeconds = seconds;

      // Year
      date.year = Math.floor(remainingSeconds / SECONDS_PER_YEAR);
      remainingSeconds -= date.year * SECONDS_PER_YEAR;
      // Day
      date.day = Math.floor(remainingSeconds / SECONDS_PER_DAY);
      remainingSeconds -= date.day * SECONDS_PER_DAY;
      // Hour
      date.hour = Math.floor(remainingSeconds / SECONDS_PER_HOUR);
      remainingSeconds -= date.hour * SECONDS_PER_HOUR;
      // Minute
      date.minute = Math.floor(remainingSeconds / SECONDS_PER_MINUTE);
      remainingSeconds -= date.minute * SECONDS_PER_MINUTE;
      // Second
      date.second = remainingSeconds;
      return date;
    },

    getFormattedDate(date = this.currentDate) {
      return `${date.year}.${date.day} ${date.hour
        .toString()
        .padStart(2, "0")}:${date.minute
        .toString()
        .padStart(2, "0")}:${date.second.toString().padStart(2, "0")}`;
    },

    incrementBy(amount, unit) {
      let multiplier = 1;
      switch (unit) {
        case "year":
          multiplier = SECONDS_PER_YEAR;
          break;
        case "day":
          multiplier = SECONDS_PER_DAY;
          break;
        case "hour":
          multiplier = SECONDS_PER_HOUR;
          break;
        case "minute":
          multiplier = SECONDS_PER_MINUTE;
          break;
        default:
          multiplier = 1;
      }
      this.currentSeconds += amount * multiplier;
    },

    decrementBy(amount, unit) {
      this.incrementBy(amount * -1, unit);
    },

    saveTimestamp() {
      let label = window.prompt("Label?");
      let timestamp = {
        time: this.getFormattedDate(),
        label: label
      };

      this.savedTimestamps.push(timestamp);
    },

    setDate() {
      let date = window.prompt(
        "Enter date. Separate units with a period. (ex: 299.69.4.20.0)"
      );
      let dateArray = date.split(".");
      let newDate = {
        year: dateArray[0],
        day: dateArray[1],
        hour: dateArray[2],
        minute: dateArray[3],
        second: dateArray[4]
      };
      this.currentSeconds = this.dateToSeconds(newDate);
    }
  }
};
</script>

<style lang="scss">
* {
  font-family: "Open Sans", sans-serif;
  font-weight: lighter;
}

body {
  background: #202030;
  color: #cff;
}

.digital {
  font-family: monospace;
  font-variant-numeric: tabular-nums;
}

.clock {
  display: table;
  margin: 20px auto;
  font-size: calc(16px + 6vw);
}

.fields {
  width: 90vw;
  max-width: 380px;
  margin: auto;
}

.increment-fields {
  display: flex;
  // justify-content: space-evenly;
  justify-content: space-between;

  label {
    display: block;
    margin-bottom: 8px;
  }
}

.increment-input {
  font-size: calc(12px + 6vw);
  display: inline-block;
  max-width: 5ch;
  padding: 8px;
  // text-align: center;
}

.unit-boxes {
  margin-top: 24px;
}

.unit-box {
  display: flex;
  padding: 0 8px;
  align-items: center;

  span {
    display: inline-block;
    width: 8ch;
    text-align: right;
    margin-right: 20px;
  }

  button {
    font-size: 22px;
    padding: 4px 6px;
    min-width: 40px;
  }
}

dd,
dt {
  display: inline;
}

.timestamps {
  border-top: 2px solid;
  padding: 10px;

  div {
    margin-bottom: 10px;
  }
}
</style>
