<template>
  <div class="trigger-node">
    <h3>Trigger</h3>
    <div class="trigger-content">
      <select v-model="data.triggerType">
        <option value="placedOrder">Placed Order</option>
        <option value="subscribedToList">Subscribed to List</option>
        <option value="segmentBased">Segment Based</option>
        <option value="dateProperty">Date Property</option>
      </select>

      <div v-if="data.triggerType === 'placedOrder'" class="trigger-filters">
        <h4>Trigger Filters</h4>
        <div class="filter">
          <label>Order Value</label>
          <select v-model="data.orderValueOperator">
            <option value="greaterThan">Greater than</option>
            <option value="lessThan">Less than</option>
            <option value="equals">Equals</option>
          </select>
          <input type="number" v-model="data.orderValue" placeholder="Value" />
        </div>
      </div>

      <div class="flow-filters">
        <h4>Flow Filters</h4>
        <div class="filter">
          <input type="checkbox" v-model="data.excludeIfInFlow" id="excludeIfInFlow" />
          <label for="excludeIfInFlow">Don't enter if already in another flow</label>
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
    if (!this.data.triggerType) {
      this.data.triggerType = 'placedOrder';
      this.data.orderValueOperator = 'greaterThan';
      this.data.orderValue = 0;
      this.data.excludeIfInFlow = false;
    }
  }
}
</script>

<style scoped>
.trigger-node {
  background-color: white;
  border: 2px solid #d6b656;
  border-radius: 8px;
  padding: 10px;
  width: 300px;
}

h3,
h4 {
  margin-top: 0;
  color: #333;
}

h3 {
  text-align: center;
}

h4 {
  font-size: 14px;
  margin-bottom: 5px;
}

.trigger-content {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

select,
input[type="number"] {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.trigger-filters,
.flow-filters {
  border: 1px solid #eee;
  padding: 8px;
  border-radius: 4px;
}

.filter {
  display: flex;
  align-items: center;
  gap: 5px;
  margin-bottom: 5px;
}
</style>