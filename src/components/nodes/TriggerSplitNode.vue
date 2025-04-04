<template>
    <div class="trigger-split-node">
        <h3>Trigger Split</h3>
        <div class="split-content">
            <div v-if="data.triggerType === 'placedOrder'" class="order-split">
                <select v-model="data.splitBy">
                    <option value="items">Items Purchased</option>
                    <option value="orderValue">Order Value</option>
                </select>

                <div v-if="data.splitBy === 'items'" class="items-condition">
                    <input v-model="data.itemName" placeholder="Product Name" />
                    <select v-model="data.itemCondition">
                        <option value="contains">Contains</option>
                        <option value="doesNotContain">Does Not Contain</option>
                        <option value="equals">Equals</option>
                    </select>
                </div>

                <div v-if="data.splitBy === 'orderValue'" class="value-condition">
                    <select v-model="data.valueCondition">
                        <option value="greaterThan">Greater Than</option>
                        <option value="lessThan">Less Than</option>
                        <option value="equals">Equals</option>
                    </select>
                    <input type="number" v-model="data.orderValue" placeholder="Value" />
                </div>
            </div>

            <div v-else class="no-trigger-type">
                <p>This split is only available for certain trigger types.</p>
            </div>
        </div>

        <div class="branches">
            <div class="yes-branch">YES Path</div>
            <div class="no-branch">NO Path</div>
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
            this.data.splitBy = 'items';
            this.data.itemName = '';
            this.data.itemCondition = 'contains';
            this.data.valueCondition = 'greaterThan';
            this.data.orderValue = 0;
        }
    }
}
</script>

<style scoped>
.trigger-split-node {
    background-color: white;
    border: 2px solid #d79b00;
    border-radius: 8px;
    padding: 10px;
    width: 300px;
}

h3 {
    margin-top: 0;
    color: #333;
    text-align: center;
}

.split-content {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 15px;
}

select,
input {
    padding: 5px;
    border: 1px solid #ccc;
    border-radius: 4px;
    margin-bottom: 5px;
}

.items-condition,
.value-condition {
    display: flex;
    flex-direction: column;
    gap: 5px;
    margin-top: 5px;
}

.no-trigger-type {
    color: #666;
    font-style: italic;
}

.branches {
    display: flex;
    justify-content: space-between;
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