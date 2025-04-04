<template>
    <div class="action-node" :class="data.actionType">
        <h3>{{ actionTitle }}</h3>
        <div class="action-content">
            <select v-model="data.actionType">
                <option value="email">Send Email</option>
                <option value="sms">Send SMS</option>
                <option value="updateProfile">Update Profile Property</option>
                <option value="notification">Send Notification</option>
            </select>

            <div v-if="data.actionType === 'email'" class="email-settings">
                <input v-model="data.name" placeholder="Email Name (e.g., Welcome Email 1)" />
                <input v-model="data.subject" placeholder="Subject Line" />
                <select v-model="data.status">
                    <option value="draft">Draft</option>
                    <option value="manual">Manual</option>
                    <option value="live">Live</option>
                </select>
                <div class="smart-sending">
                    <input type="checkbox" v-model="data.smartSending" id="smartSending" />
                    <label for="smartSending">Enable Smart Sending</label>
                </div>
            </div>

            <div v-if="data.actionType === 'sms'" class="sms-settings">
                <input v-model="data.name" placeholder="SMS Name" />
                <textarea v-model="data.message" placeholder="SMS Message"></textarea>
                <select v-model="data.status">
                    <option value="draft">Draft</option>
                    <option value="manual">Manual</option>
                    <option value="live">Live</option>
                </select>
            </div>

            <div v-if="data.actionType === 'updateProfile'" class="profile-settings">
                <input v-model="data.propertyName" placeholder="Profile Property Name" />
                <input v-model="data.propertyValue" placeholder="New Value" />
            </div>

            <div v-if="data.actionType === 'notification'" class="notification-settings">
                <input v-model="data.name" placeholder="Notification Name" />
                <textarea v-model="data.message" placeholder="Notification Message"></textarea>
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
        actionTitle() {
            switch (this.data.actionType) {
                case 'email': return 'Send Email';
                case 'sms': return 'Send SMS';
                case 'updateProfile': return 'Update Profile';
                case 'notification': return 'Send Notification';
                default: return 'Action';
            }
        }
    },
    mounted() {
        // Initialize default values if not present
        if (!this.data.actionType) {
            this.data.actionType = 'email';
            this.data.name = '';
            this.data.status = 'draft';
            this.data.smartSending = true;
        }
    }
}
</script>

<style scoped>
.action-node {
    background-color: white;
    border: 2px solid #9673a6;
    border-radius: 8px;
    padding: 10px;
    width: 300px;
}

.action-node.email {
    border-color: #9673a6;
}

.action-node.sms {
    border-color: #6c8ebf;
}

.action-node.updateProfile {
    border-color: #d79b00;
}

.action-node.notification {
    border-color: #b85450;
}

h3 {
    margin-top: 0;
    color: #333;
    text-align: center;
}

.action-content {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

select,
input,
textarea {
    padding: 5px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

textarea {
    min-height: 80px;
    resize: vertical;
}

.smart-sending {
    display: flex;
    align-items: center;
    gap: 5px;
}

.email-settings,
.sms-settings,
.profile-settings,
.notification-settings {
    display: flex;
    flex-direction: column;
    gap: 8px;
}
</style>