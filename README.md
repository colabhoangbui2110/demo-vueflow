# Vue Flow Workflow Builder

A visual workflow builder created with Vue.js and Vue Flow, designed for creating and managing automated workflows with a drag-and-drop interface.

## Features

- **Interactive Workflow Canvas**: Drag and drop nodes to create complex workflows
- **Node Palette**: Choose from various node types to build your workflow
- **Node Configuration**: Configure each node with a dedicated properties panel
- **Connection Management**: Create connections between nodes with automatic path routing
- **Minimap Navigation**: Easily navigate large workflows with the minimap
- **Zoom and Pan Controls**: Adjust your view of the workflow

## Node Types

- **Trigger**: Start your workflow with various trigger conditions
- **Email Action**: Send emails as part of your workflow
- **Time Delay**: Add waiting periods between actions
- **Conditional Split**: Create branching logic based on conditions
- **Trigger Split**: Split workflows based on trigger conditions

## Getting Started

### Prerequisites

- Node.js (v14 or later)
- npm or yarn

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/vue-flow-test.git
cd vue-flow-test
```

2. Install dependencies:
```bash
npm install
# or
yarn install
```

3. Start the development server:
```bash
npm run serve
# or
yarn serve
```

4. Open your browser and navigate to `http://localhost:8080`

## Usage

1. **Creating a Workflow**:
   - Drag nodes from the left palette onto the canvas
   - Connect nodes by dragging from one node's output handle to another node's input handle

2. **Configuring Nodes**:
   - Click on any node to open its configuration panel on the right
   - Set properties specific to each node type

3. **Managing Connections**:
   - Connections are automatically routed
   - Conditional split nodes create labeled YES/NO paths

## Built With

- [Vue.js](https://vuejs.org/) - The progressive JavaScript framework
- [Vue Flow](https://vueflow.dev/) - A highly customizable Vue.js library for building node-based editors and workflows
- [Vue Flow Minimap](https://vueflow.dev/guide/additional-components/minimap.html) - For workflow navigation
- [Vue Flow Background](https://vueflow.dev/guide/additional-components/background.html) - For canvas styling
- [Vue Flow Controls](https://vueflow.dev/guide/additional-components/controls.html) - For zoom and fit controls

## Project Structure

```
src/
├── components/
│   ├── WorkflowNodes.vue - Main workflow component
│   ├── nodes/ - Custom node components
│   │   ├── ActionNode.vue
│   │   ├── ConditionalSplitNode.vue
│   │   ├── EmailActionNode.vue
│   │   ├── InteractControls.vue
│   │   ├── TimeDelayNode.vue
│   │   ├── TriggerNode.vue
│   │   └── TriggerSplitNode.vue
│   └── icons/ - SVG icons for nodes
├── App.vue
└── main.js
```

## Customization

You can customize the workflow builder by:

1. Adding new node types in the `nodes/` directory
2. Extending the node properties panel for specific node types
3. Customizing node colors and icons in the `WorkflowNodes.vue` component

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- [Vue Flow](https://vueflow.dev/) for the excellent node-based UI library
- All contributors who have helped with the development of this project