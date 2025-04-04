<template>
  <div class="time-delay-node">
    <h3>Time Delay</h3>
    <div class="delay-settings">
      <div class="duration-setting">
        <label>Wait for:</label>
        <input type="number" v-model="data.duration" min="1" />
        <select v-model="data.durationUnit">
          <option value="minutes">Minutes</option>
          <option value="hours">Hours</option>
          <option value="days">Days</option>
          <option value="weeks">Weeks</option>
        </select>
      </div>

      <div class="timing-options">
        <div class="option">
          <input type="checkbox" v-model="data.specificTime" id="specificTime" />
          <label for="specificTime">Only continue at a specific time</label>
        </div>

        <div v-if="data.specificTime" class="time-settings">
          <label>Time:</label>
          <input type="time" v-model="data.time" />
          <select v-model="data.timezone">
            <option value="recipient">Recipient's timezone</option>
            <option value="account">Account timezone</option>
          </select>
        </div>

        <div class="option">
          <input type="checkbox" v-model="data.weekdaysOnly" id="weekdaysOnly" />
          <label for="weekdaysOnly">Only continue on weekdays</label>
        </div>
      </div>
    </div>

    <Handle type="source" :position="Position.Right" />
    <Handle type="target" :position="Position.Left" />
  </div>
</template>

<script>
import { Handle, Position } from '@vue-flow/core';

export default {
  props: ['data'],
  components: {
    Handle,
  },
  computed: {
    Position() {
      return Position;
    },
  },
  mounted() {
    // Initialize default values if not present
    if (!this.data.duration) {
      this.data.duration = 1;
      this.data.durationUnit = 'days';
      this.data.specificTime = false;
      this.data.time = '09:00';
      this.data.timezone = 'recipient';
      this.data.weekdaysOnly = false;
    }
  }
}
</script>

<style scoped>
.time-delay-node {
  background-color: white;
  border: 2px solid #82b366;
  border-radius: 8px;
  padding: 10px;
  width: 300px;
}

h3 {
  margin-top: 0;
  color: #333;
  text-align: center;
}

.delay-settings {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.duration-setting {
  display: flex;
  align-items: center;
  gap: 5px;
}

input[type="number"] {
  width: 60px;
}

select,
input {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.timing-options {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.option {
  display: flex;
  align-items: center;
  gap: 5px;
}

.time-settings {
  margin-left: 20px;
  margin-top: 5px;
  display: flex;
  align-items: center;
  gap: 5px;
}
</style>