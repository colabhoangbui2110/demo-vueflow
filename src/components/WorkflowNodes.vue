<template>
  <div class="workflow-container">
    <!-- Left sidebar for node palette -->
    <div class="node-palette">
      <h3>Node Types</h3>
      <div v-for="(nodeType, type) in nodeTypes" :key="type" class="palette-node"
        :style="{ borderColor: nodeColors[type] }" draggable="true" @dragstart="onDragStart($event, type)">
        <div class="palette-node-icon" :style="{ backgroundColor: nodeColors[type] }">
          <component :is="nodeIcons[type]" />
        </div>
        <div class="palette-node-label">{{ getNodeLabel(type) }}</div>
      </div>
    </div>

    <!-- Main workflow area -->
    <div class="workflow-nodes" @drop="onDrop" @dragover.prevent>
      <vue-flow v-model="elements" @node-click="openNodeConfig" :node-types="nodeTypes"
        :default-edge-options="{ type: 'smoothstep' }" :connection-line-style="{ stroke: '#555' }" :snap-to-grid="true"
        @connect="onConnect" :snap-grid="[15, 15]" :connect-on-drop="true" :default-zoom="1" :min-zoom="0.2"
        :max-zoom="4" fit-view-on-init>
        <template #node-custom="{ data }">
          <div class="node-header" :style="{ backgroundColor: nodeColors[data.type] }">
            <div class="node-icon">
              <component :is="nodeIcons[data.type]" />
            </div>
            <div class="node-title">{{ data.label }}</div>
          </div>
          <div class="node-content">
            <slot />
          </div>
        </template>

        <template #controls>
          <div class="vue-flow__controls">
            <button @click="addNodeBtnClick">Add Node</button>
            <button @click="onConnect">Connect Nodes</button>
          </div>
        </template>

        <div v-if="showTriggerModal" class="trigger-modal">
          <div class="modal-content">
            <h3>Configure Trigger</h3>
            <select v-model="selectedTriggerType">
              <option value="">Select trigger type</option>
              <option value="subscriber is added to segment">Subscriber added to segment</option>
              <option value="subscriber performs action">Subscriber performs action</option>
            </select>
            <select v-model="selectedSegment" v-if="selectedTriggerType">
              <option value="">Select segment</option>
              <option value="Premium Users">Premium Users</option>
              <option value="New Subscribers">New Subscribers</option>
            </select>
            <div class="modal-actions">
              <button @click="saveTriggerConfig">Save</button>
              <button @click="showTriggerModal = false">Cancel</button>
            </div>
          </div>
        </div>

        <MiniMap :node-stroke-color="nodeStroke" :node-color="nodeColor" />
        <Background />
        <Controls />
        <InteractControls />
      </vue-flow>
    </div>

    <!-- New right sidebar for node properties -->
    <div class="node-properties" v-if="selectedNode">
      <div class="properties-header">
        <h3>{{ getNodeLabel(selectedNode.type) }} Properties</h3>
      </div>

      <div class="properties-content">
        <!-- Common properties section -->
        <div class="properties-section">
          <h4>General</h4>
          <div class="property-field">
            <label>Name</label>
            <input type="text" v-model="selectedNode.data.label" @input="updateNodeData" />
          </div>
        </div>

        <!-- Trigger node properties -->
        <div v-if="selectedNode.type === 'trigger'" class="properties-section">
          <h4>Audience</h4>
          <div class="property-field">
            <label>Email list</label>
            <select v-model="selectedNode.data.triggerType" @change="updateNodeData">
              <option value="subscriber is added to segment">Subscriber added to segment</option>
              <option value="subscriber performs action">Subscriber performs action</option>
            </select>
          </div>
          <div class="property-field" v-if="selectedNode.data.triggerType">
            <label>Segment</label>
            <select v-model="selectedNode.data.segment" @change="updateNodeData">
              <option value="All profiles">All profiles</option>
              <option value="Premium Users">Premium Users</option>
              <option value="New Subscribers">New Subscribers</option>
            </select>
          </div>
        </div>

        <!-- Email action node properties -->
        <div
          v-if="selectedNode.type === 'emailAction' || (selectedNode.type === 'action' && selectedNode.data.actionType === 'email')"
          class="properties-section">
          <h4>Template email</h4>
          <div class="property-field">
            <label>Select template</label>
            <select v-model="selectedNode.data.template" @change="updateNodeData">
              <option value="Just it!">Just it!</option>
              <option value="Welcome">Welcome</option>
              <option value="Promotional">Promotional</option>
            </select>
          </div>

          <h4>Subject and sender</h4>
          <div class="property-field">
            <label>Subject line</label>
            <input type="text" v-model="selectedNode.data.subject" @input="updateNodeData"
              placeholder="Your journey begins here." />
          </div>
          <div class="property-field">
            <label>Preview text</label>
            <input type="text" v-model="selectedNode.data.previewText" @input="updateNodeData"
              placeholder="Welcome to OCG!" />
          </div>
          <div class="property-field">
            <label>Sender name</label>
            <input type="text" v-model="selectedNode.data.senderName" @input="updateNodeData" placeholder="OCG" />
          </div>
        </div>

        <!-- Time delay node properties -->
        <div v-if="selectedNode.type === 'timeDelay'" class="properties-section">
          <h4>Delay settings</h4>
          <div class="property-field delay-field">
            <label>Wait for</label>
            <div class="delay-inputs">
              <input type="number" v-model="selectedNode.data.value" @input="updateNodeData" min="1" />
              <select v-model="selectedNode.data.unit" @change="updateNodeData">
                <option value="minutes">Minutes</option>
                <option value="hours">Hours</option>
                <option value="days">Days</option>
                <option value="weeks">Weeks</option>
              </select>
            </div>
          </div>
        </div>

        <!-- Conditional split node properties -->
        <div v-if="selectedNode.type === 'conditionalSplit'" class="properties-section">
          <h4>Conditions</h4>
          <div v-for="(condition, index) in selectedNode.data.conditions" :key="index" class="condition-item">
            <select v-model="condition.type" @change="updateNodeData">
              <option value="placedOrder">Placed Order</option>
              <option value="profileProperty">Profile Property</option>
            </select>
            <select v-model="condition.operator" @change="updateNodeData" v-if="condition.type === 'placedOrder'">
              <option value="atLeastOnce">At least once</option>
              <option value="zeroTimes">Zero times</option>
            </select>
            <button @click="removeCondition(index)" class="remove-btn">Remove</button>
          </div>
          <button @click="addCondition" class="add-btn">Add Condition</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { VueFlow, useVueFlow } from '@vue-flow/core'
import EmailActionNode from './nodes/EmailActionNode.vue'
import TimeDelayNode from './nodes/TimeDelayNode.vue'
import ConditionalSplitNode from './nodes/ConditionalSplitNode.vue'
import TriggerNode from './nodes/TriggerNode.vue'
import ActionNode from './nodes/ActionNode.vue'
import TriggerSplitNode from './nodes/TriggerSplitNode.vue'
import InteractControls from './nodes/InteractControls.vue'
import {
  MailIcon,
  ClockIcon,
  SplitIcon,
  PlayIcon,
  // PersonIcon 
} from './icons'
import { MiniMap } from '@vue-flow/minimap'
import { Background } from '@vue-flow/background'
import { Controls } from '@vue-flow/controls'

export default {
  components: {
    VueFlow,
    InteractControls,
    MiniMap,
    Background,
    Controls,
  },
  data() {
    return {
      elements: [],
      nodeTypes: {
        emailAction: ActionNode,
        timeDelay: TimeDelayNode,
        conditionalSplit: ConditionalSplitNode,
        trigger: TriggerNode,
        triggerSplit: TriggerSplitNode
      },
      nodeColors: {
        trigger: '#FF7A45', // Distinct orange color
        conditionalSplit: '#722ED1',
        timeDelay: '#13C2C2',
        emailAction: '#52C41A',
        action: '#1890FF',
        triggerSplit: '#FA8C16'
      },
      nodeIcons: {
        trigger: PlayIcon,
        conditionalSplit: SplitIcon,
        timeDelay: ClockIcon,
        emailAction: MailIcon,
        action: MailIcon, // You might want to create a specific icon for this
        triggerSplit: SplitIcon // You might want to create a specific icon for this
      },
      showTriggerModal: false,
      selectedTriggerType: '',
      selectedSegment: '',
      nodeCount: {
        trigger: 1,
        conditionalSplit: 1,
        timeDelay: 1,
        emailAction: 1,
        action: 0,
        triggerSplit: 0
      },
      selectedNode: null
    }
  },
  mounted() {
    this.initializeNodes()
  },
  methods: {
    onConnect(connection) {
      // Check if this is a connection from a conditional split node
      const sourceNode = this.elements.find(el => el.id === connection.source);
      if (sourceNode && sourceNode.type === 'conditionalSplit') {
        // Add class based on which handle was used (yes/no)
        if (connection.sourceHandle === 'yes') {
          connection.class = 'yes-path';
          connection.label = 'YES';
        } else if (connection.sourceHandle === 'no') {
          connection.class = 'no-path';
          connection.label = 'NO';
        }
      }

      // Add the connection to the elements
      this.elements = addEdge(connection, this.elements);
    },
    nodeColor(n) {
      if (n.type === 'color-selector') {
        return n.data.color
      }

      return '#fff'
    },
    nodeStroke(n) {
      switch (n.type) {
        case 'input':
          return '#0041d0'
        case 'color-selector':
          return n.data.color
        case 'output':
          return '#ff0072'
        default:
          return '#eee'
      }
    },
    initializeNodes() {
      this.elements = [
        // Trigger node - always first
        {
          id: 'trigger-1',
          type: 'trigger',
          position: { x: 100, y: 50 }, // Positioned at top
          data: {
            label: 'Begin when subscriber is added to segment',
            triggerType: this.selectedTriggerType,
            segment: this.selectedSegment,
            isConfigured: false,
            type: 'trigger'
          }
        }
      ]
    },
    openNodeConfig(event) {
      let node = event.node;
      console.log(event, node);

      // Set the selected node for the right sidebar
      this.selectedNode = node;

      // Initialize any missing properties based on node type
      this.initializeNodeProperties(node);

      // For backward compatibility, still show the modal for trigger nodes if needed
      if (node.type === 'trigger' && false) { // Disabled modal in favor of sidebar
        this.showTriggerModal = true;
      }
    },
    initializeNodeProperties(node) {
      console.log('Initializing node properties for:', node);
      // Initialize properties that might be missing based on node type
      if (node.type === 'emailAction' || (node.type === 'action' && node.data.actionType === 'email')) {
        if (!node.data.subject) node.data.subject = '';
        if (!node.data.previewText) node.data.previewText = '';
        if (!node.data.senderName) node.data.senderName = '';
        if (!node.data.template) node.data.template = 'Just it!';
      }

      if (node.type === 'conditionalSplit' && !node.data.conditions) {
        node.data.conditions = [];
      }
    },
    updateNodeData() {
      // This method is called when any property is updated in the sidebar
      // The reactivity system will automatically update the node data
      // We can add additional logic here if needed

      // Update the node label for certain types if needed
      if (this.selectedNode.type === 'trigger') {
        this.selectedNode.data.label = `Begin when ${this.selectedNode.data.triggerType}: ${this.selectedNode.data.segment || 'Any'}`;
      }

      // Force a refresh of the elements array to ensure Vue Flow updates
      this.elements = [...this.elements];
    },
    addCondition() {
      if (this.selectedNode && this.selectedNode.type === 'conditionalSplit') {
        if (!this.selectedNode.data.conditions) {
          this.selectedNode.data.conditions = [];
        }

        this.selectedNode.data.conditions.push({
          type: 'placedOrder',
          operator: 'atLeastOnce',
          timeframe: 'sinceStartingFlow'
        });

        this.updateNodeData();
      }
    },
    removeCondition(index) {
      if (this.selectedNode && this.selectedNode.type === 'conditionalSplit') {
        this.selectedNode.data.conditions.splice(index, 1);
        this.updateNodeData();
      }
    },
    saveTriggerConfig() {
      const triggerNode = this.elements.find(n => n.type === 'trigger')
      if (triggerNode) {
        triggerNode.data = {
          ...triggerNode.data,
          triggerType: this.selectedTriggerType,
          segment: this.selectedSegment,
          isConfigured: true,
          label: `Begin when ${this.selectedTriggerType}: ${this.selectedSegment}`
        }
        this.showTriggerModal = false
      }
    },
    getNodeLabel(type) {
      const labels = {
        trigger: 'Trigger',
        conditionalSplit: 'Conditional Split',
        timeDelay: 'Time Delay',
        emailAction: 'Email Action',
        action: 'Action',
        triggerSplit: 'Trigger Split'
      }
      return labels[type] || type
    },
    onDragStart(event, nodeType) {
      event.dataTransfer.setData('application/vueflow', nodeType)
      event.dataTransfer.effectAllowed = 'move'
    },
    onDrop(event) {
      const nodeType = event.dataTransfer.getData('application/vueflow')
      if (!nodeType) return

      // Get the position where the node was dropped
      const { left, top } = this.$el.getBoundingClientRect()
      const position = {
        x: event.clientX - left,
        y: event.clientY - top
      }

      this.addNode(nodeType, position)
    },
    addNode(nodeType, position) {
      // Increment the count for this node type
      this.nodeCount[nodeType] = (this.nodeCount[nodeType] || 0) + 1

      // Create a unique ID for the new node
      const id = `${nodeType}-${this.nodeCount[nodeType]}`

      // Create the node data based on type
      let nodeData = {
        label: this.getNodeLabel(nodeType),
        type: nodeType
      }

      // Add type-specific data
      switch (nodeType) {
        case 'trigger':
          nodeData = {
            ...nodeData,
            triggerType: '',
            segment: '',
            isConfigured: false
          }
          break
        case 'timeDelay':
          nodeData = {
            ...nodeData,
            value: 1,
            unit: 'days'
          }
          break
        case 'conditionalSplit':
          nodeData = {
            ...nodeData,
            conditions: []
          }
          break
        case 'emailAction':
          nodeData = {
            ...nodeData,
            emailName: '',
            subject: '',
            previewText: '',
            senderName: '',
            senderEmail: '',
            template: 'Just it!'
          }
          break
        case 'action':
          nodeData = {
            ...nodeData,
            actionType: 'email',
            name: '',
            subject: '',
            previewText: '',
            senderName: '',
            status: 'draft',
            template: 'Just it!'
          }
          break
        case 'triggerSplit':
          nodeData = {
            ...nodeData,
            triggerType: 'placedOrder',
            splitBy: 'items'
          }
          break
      }

      // Create the new node
      const newNode = {
        id,
        type: nodeType,
        position,
        data: nodeData
      }

      // Add the node to the elements
      this.elements = [...this.elements, newNode]
    },
    addNodeBtnClick() {
      // Example function to add a node programmatically
      const position = { x: 250, y: 250 }
      this.addNode('emailAction', position)
    },
    onConnect() {
      // This is a placeholder - Vue Flow handles connections automatically
      // You can add custom logic here if needed
      console.log('Connect nodes functionality')
    }
  }
}
</script>

<style scoped>
.workflow-container {
  display: flex;
  height: 100vh;
  width: 100vw;
}

.node-palette {
  width: 250px;
  background-color: #f0f2f5;
  padding: 16px;
  border-right: 1px solid #d9d9d9;
  overflow-y: auto;
}

.node-palette h3 {
  margin-top: 0;
  margin-bottom: 16px;
  color: #333;
}

.palette-node {
  display: flex;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  background-color: white;
  border: 2px solid #d9d9d9;
  border-radius: 4px;
  cursor: grab;
  transition: all 0.3s;
}

.palette-node:hover {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

.palette-node-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 4px;
  margin-right: 10px;
  color: white;
}

.palette-node-label {
  font-weight: 500;
}

.workflow-nodes {
  flex: 1;
  background-color: #f5f5f5;
  position: relative;
}

/* Right sidebar for node properties */
.node-properties {
  width: 300px;
  background-color: white;
  border-left: 1px solid #d9d9d9;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.properties-header {
  padding: 16px;
  border-bottom: 1px solid #e8e8e8;
  background-color: #fafafa;
}

.properties-header h3 {
  margin: 0;
  color: #333;
  font-size: 16px;
}

.properties-content {
  padding: 16px;
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.properties-section {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.properties-section h4 {
  margin: 0;
  color: #333;
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 4px;
}

.property-field {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.property-field label {
  font-size: 12px;
  color: #666;
}

.property-field input,
.property-field select {
  padding: 8px;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  font-size: 14px;
}

.delay-field {
  margin-bottom: 8px;
}

.delay-inputs {
  display: flex;
  gap: 8px;
}

.delay-inputs input {
  width: 60px;
}

.condition-item {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 8px;
  border: 1px solid #f0f0f0;
  border-radius: 4px;
  margin-bottom: 8px;
}

.add-btn,
.remove-btn {
  padding: 6px 12px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
  font-size: 14px;
}

.add-btn {
  background-color: #1890ff;
  color: white;
}

.remove-btn {
  background-color: #ff4d4f;
  color: white;
  align-self: flex-end;
}

.node-header {
  display: flex;
  align-items: center;
  padding: 8px 12px;
  border-radius: 4px 4px 0 0;
  color: white;
  font-weight: 500;
}

.node-icon {
  margin-right: 8px;
  display: flex;
  align-items: center;
}

.node-content {
  padding: 12px;
  background: white;
  border-radius: 0 0 4px 4px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
}

.handle {
  width: 10px;
  height: 10px;
  background: #555;
  border-radius: 50%;
}

.trigger-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  margin-top: 16px;
  gap: 8px;
}

/* Make trigger node header more prominent */
.node-header[style*="background-color: #FF7A45"] {
  font-weight: bold;
  padding: 12px;
}

.vue-flow__controls {
  display: flex;
  flex-direction: column;
}

.vue-flow__controls button {
  background-color: white;
  border: 1px solid #d9d9d9;
  padding: 6px 12px;
  margin-bottom: 5px;
  border-radius: 4px;
  cursor: pointer;
}

.vue-flow__controls button:hover {
  background-color: #f5f5f5;
}
</style>