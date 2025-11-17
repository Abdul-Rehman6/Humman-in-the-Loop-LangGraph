# Human-in-the-Loop Workflow with LangGraph

A powerful demonstration of implementing human feedback loops in autonomous agent workflows using LangGraph and LangChain. This project showcases how to build AI systems that can pause execution, wait for human input, and adapt their behavior based on user corrections.

## 📋 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [How It Works](#how-it-works)
- [Use Cases](#use-cases)
- [Advanced Features](#advanced-features)
- [Contributing](#contributing)

## 🎯 Overview

This project demonstrates a sophisticated pattern for building AI agents that respect human oversight. Instead of fully autonomous systems that run to completion without intervention, this implementation creates agents that can be interrupted at critical decision points, allowing humans to review, correct, or redirect the agent's actions before continuing.

The system implements arithmetic operations as a practical example, but the pattern generalizes to any workflow requiring human validation—from content moderation to financial transactions to medical diagnostics.

## ✨ Key Features

- **Interrupt Points**: Strategically pause workflow execution before critical nodes execute
- **State Management**: Robust checkpoint system to save and restore agent state
- **Human Feedback Integration**: Seamlessly accept and apply user corrections mid-execution
- **Tool Integration**: Agents can access predefined tools (multiply, add, divide) while respecting human oversight
- **Message History**: Complete conversation history tracking for audit trails and context
- **Thread-based Execution**: Support for multiple concurrent workflows with isolated state

## 🏗️ Architecture

The system consists of three main components:

**State Graph**: A directed graph that defines the workflow with nodes representing different stages:
- `assistant`: The AI decision-making node that determines next actions
- `tools`: Execution node that runs tool calls (arithmetic operations)
- `human_feedback`: Critical node where human input is collected and applied

**Interruption Strategy**: Interrupts occur before the `human_feedback` node, allowing humans to:
- Review the current state
- Inject new instructions or corrections
- Approve or modify pending operations

**Checkpoint System**: Uses in-memory storage to maintain conversation state and allow resumption from interruption points without losing context.

## 📦 Installation

### Prerequisites

- Python 3.9+
- pip package manager

### Dependencies

```bash
pip install langgraph langchain langchain-openai langchain-community python-dotenv
```

### Environment Setup

Create a `.env` file in your project root:

```env
OPENAI_API_KEY=your_api_key_here
```

## 🚀 Quick Start

### Basic Usage

The simplest workflow demonstrates arithmetic operations with human oversight:

1. User provides initial instruction ("Multiply 2 and 3")
2. System interrupts before processing
3. Human can review and modify the request
4. System resumes with updated instructions
5. Result is returned with full context

### Running the Example

Execute the notebook cells in order to see the complete workflow in action. The system will:
- Create a conversational agent with tool access
- Demonstrate initial execution with interruption
- Show how to inject human feedback
- Resume and complete the workflow

## 🔄 How It Works

### Workflow Phases

**Phase 1: Initial Execution**
The assistant receives a user message and determines what action to take. Before executing any tools, the workflow pauses at the `human_feedback` node, waiting for human approval.

**Phase 2: Human Review**
At the interruption point, humans can access the current state and update it with corrections or new instructions using `graph.update_state()`.

**Phase 3: Resumed Execution**
The workflow continues from the interruption point with the updated state, respecting all human corrections and modifications.

**Phase 4: Completion**
Once all operations complete successfully, the final response is generated and returned to the user.

### State Updates

Human feedback is applied by updating the message state:

```python
graph.update_state(thread, {
    "messages": HumanMessage(content="Corrected instruction here")
}, as_node="human_feedback")
```

This approach maintains the complete audit trail while allowing trajectory correction.

## 💡 Use Cases

**Content Moderation**: AI flags potentially problematic content, humans review and approve actions before enforcement

**Financial Systems**: Automated systems propose transactions, humans verify before settlement

**Medical Diagnostics**: AI suggests diagnoses, medical professionals review and approve before notification

**Data Processing**: Bulk operations pause for human validation at critical stages

**Customer Support**: AI drafts responses, humans refine before sending

## 🔧 Advanced Features

### Multiple Concurrent Workflows

Use thread IDs to maintain separate state for different users or conversations:

```python
thread_1 = {"configurable": {"thread_id": "user_123"}}
thread_2 = {"configurable": {"thread_id": "user_456"}}
```

### State History

Access complete execution history to understand decision flow:

```python
history = graph.get_state_history(thread)
for state_snapshot in history:
    # Examine state at each checkpoint
```

### Custom Tools

Extend the system with your own tools by defining functions with proper documentation and registering them with the agent.

### Checkpoint Persistence

The memory saver stores checkpoints in-memory by default. For production systems, integrate with persistent storage backends for durability across restarts.

## 📊 Workflow Diagram

```
START 
  ↓
HUMAN_FEEDBACK (INTERRUPT POINT)
  ↓
ASSISTANT (LLM decides on action)
  ↓
tools_condition (Route: Tools or END?)
  ├→ TOOLS (Execute tool call)
  │   ↓
  │   HUMAN_FEEDBACK (INTERRUPT POINT)
  │   ↓ (loop continues)
  │
  └→ END (No more tools needed)
```

## 🎓 Learning Outcomes

By exploring this project, you'll understand:

- Building stateful AI workflows with LangGraph
- Implementing human-in-the-loop patterns
- Managing conversation state and checkpoints
- Creating flexible agent architectures
- Designing systems that combine AI autonomy with human control

## 🤝 Contributing

Contributions are welcome! Areas for enhancement:

- Integration with persistent storage backends
- Web UI for human feedback submission
- Advanced routing strategies
- Multi-agent coordination patterns
- Performance optimizations

## 📝 License

This project is open source and available under the MIT License.

## 🙋 FAQ

**Q: Can I use this with models other than OpenAI?**  
A: Yes! Replace `ChatOpenAI` with any LangChain-compatible model provider.

**Q: How do I handle production-scale workflows?**  
A: Integrate with persistent checkpointers, implement queuing systems, and add comprehensive logging and monitoring.

**Q: What happens if a human never provides feedback?**  
A: The workflow remains paused indefinitely. Implement timeout mechanisms for production systems.

**Q: Can I have multiple interruption points?**  
A: Absolutely. Add `interrupt_before` for any node where you want human oversight.

---

**For more information about LangGraph**, visit the [official documentation](https://github.com/langchain-ai/langgraph).