<template>
  <div class="conditional-split-node">
    <h3>Conditional Split</h3>
    <div v-for="(condition, index) in data.conditions" :key="index" class="condition">
      <select v-model="condition.type">
        <option value="placedOrder">Placed Order</option>
        <option value="profileProperty">Profile Property</option>
        <option value="listMembership">List/Segment Membership</option>
        <option value="randomSample">Random Sample</option>
      </select>

      <select v-model="condition.operator" v-if="condition.type === 'placedOrder'">
        <option value="atLeastOnce">At least once</option>
        <option value="zeroTimes">Zero times</option>
      </select>

      <select v-model="condition.timeframe" v-if="condition.type === 'placedOrder'">
        <option value="sinceStartingFlow">Since starting flow</option>
        <option value="last30Days">In the last 30 days</option>
        <option value="allTime">All time</option>
      </select>

      <input v-if="condition.type === 'profileProperty'" v-model="condition.propertyName" placeholder="Property name" />

      <select v-if="condition.type === 'profileProperty'" v-model="condition.propertyOperator">
        <option value="equals">equals</option>
        <option value="notEquals">does not equal</option>
        <option value="contains">contains</option>
      </select>

      <input v-if="condition.type === 'profileProperty'" v-model="condition.propertyValue" placeholder="Value" />

      <button @click="removeCondition(index)" class="remove-btn">Remove</button>
    </div>
    <button @click="addCondition" class="add-btn">Add Condition</button>
    <div class="branches">
      <div class="yes-branch">YES Path</div>
      <div class="no-branch">NO Path</div>
    </div>
  </div>
  <div class="handles">
    <div class="handle source-handle yes-handle" data-handle-id="yes"></div>
    <div class="handle source-handle no-handle" data-handle-id="no"></div>
  </div>

  <Handle type="source" :position="Position.Right" />
  <Handle type="target" :position="Position.Left" />
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
  methods: {
    addCondition() {
      this.data.conditions.push({
        type: 'placedOrder',
        operator: 'atLeastOnce',
        timeframe: 'sinceStartingFlow'
      })
    },
    removeCondition(index) {
      this.data.conditions.splice(index, 1)
    }
  }
}
</script>

<style scoped>
.conditional-split-node {
  background-color: white;
  border: 2px solid #6c8ebf;
  border-radius: 8px;
  padding: 10px;
  width: 300px;
}

h3 {
  margin-top: 0;
  color: #333;
  text-align: center;
}

.condition {
  margin-bottom: 10px;
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
}

select,
input {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-btn,
.remove-btn {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
}

.remove-btn {
  background-color: #f44336;
}

.branches {
  display: flex;
  justify-content: space-between;
  margin-top: 15px;
}

.yes-branch,
.no-branch {
  padding: 5px 10px;
  background-color: #e1e1e1;
  border-radius: 4px;
  text-align: center;
}

.yes-branch {
  background-color: #dff0d8;
}

.no-branch {
  background-color: #f2dede;
}
</style>