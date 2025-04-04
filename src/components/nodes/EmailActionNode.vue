<template>
  <div class="email-action-node">
    <h3>Send Email</h3>
    <div class="form-group">
      <label>Email Name</label>
      <input v-model="data.emailName" placeholder="e.g., Welcome Email 1">
    </div>

    <div class="form-group">
      <label>Subject</label>
      <input v-model="data.subject" placeholder="Email subject">
    </div>

    <div class="sender-info">
      <div class="form-group">
        <label>Sender Name</label>
        <input v-model="data.senderName">
      </div>
      <div class="form-group">
        <label>Sender Email</label>
        <input v-model="data.senderEmail" type="email">
      </div>
    </div>

    <div class="template-section">
      <button @click="editTemplate">Edit Template</button>
      <button @click="changeTemplate">Change Template</button>
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
  methods: {
    editTemplate() {
      // Open email builder
    },
    changeTemplate() {
      // Open template library
    }
  }
}
</script>