# Agent SDK Documentation

## Installation and Setup


### Requirements
```bash
pip install requests python-dotenv telethon tweepy selenium langchain openai faiss-cpu
```


### Environment Variables
Create a `.env` file with:
```
CHARACTER_DIR=/path/to/character.json
ENV_DIR=/path/to/env.json
API_CREATE_ADD=http://your-server/create_session
API_QUERY_ADD=http://your-server/query
API_AGENT_INFO_ADD=http://your-server/agent_info
```


## Core Modules


### 1. Agent Class (`agent.py`)
Base class for creating agent instances.


```python
from core.agent import Agent


# Create an agent
agent = Agent(name="websearch",
             agent_name='plugin-web-search',
             TAVILY_API_KEY='your-key',
             model=ai_model)
```


Key features:
- Dynamic attribute assignment
- Plugin integration support
- Model association


### 2. Model Class (`agent_model.py`)
Defines the AI model configuration.


```python
from models.agent_model import Model


# Create a model
model = Model(model="openai",
             name="llm",
             OPENAI_API_KEY="your-key",
             bio=["Trading agent description"],
             lore=["Background story"])
```


Attributes:
- `name`: Model identifier
- `model`: Provider name
- Custom attributes via kwargs


### 3. InitializeAgent Class (`core.py`)
Handles agent initialization and session management.


```python
from core.core import InitializeAgent


# Initialize multi-agent system
multi_agent = InitializeAgent(
   agents=[web_search_agent, ai_model],
   API_KEY=keys,
   multi_agent_name="demo_agent"
)
agent_name = multi_agent.start()
```


Key methods:
```python
def display_agents(self)  # Display agent details
def generate_character_file(self)  # Create character configuration
def generate_env_file(self)  # Generate environment settings
def start(self)  # Initialize agent session
def close(self)  # Clean up session
```


### 4. Character Generation (`character.py`)
Handles character configuration generation.


```python
class GenerateCharacter:
   def get_character_info(self):
       # Returns structured character configuration
       return {
           "name": self.agents[0].name,
           "clients": [],
           "modelProvider": self.agents[0].model.model,
           "plugins": [],
           "bio": [],
           # ... other configuration
       }
```


### 5. Conversation Management (`conversation.py`)
Handles communication with agents.


```python
from core.conversation import IntitializeConversation


# Create conversation instance
conversation = IntitializeConversation(agent_name, extra_tool_key)


# Send query
response = conversation.send_query("What is the temperature in Bhopal?")
```


Methods:
```python
def parse_data(self, data_obj)  # Parse API responses
def send_query(self, query)  # Send queries to agent
```


## API Reference


### Agent Creation
```python
# Create model
ai_model = Model(
   model="openai",
   name="llm",
   OPENAI_API_KEY=keys['openai'],
   bio=["Agent description"],
   lore=["Background"]
)


# Create agent
agent = Agent(
   name="websearch",
   agent_name='plugin-web-search',
   TAVILY_API_KEY=keys['tavily'],
   model=ai_model
)
```


### Session Management
```python
# Initialize session
multi_agent = InitializeAgent(
   agents=[agent1, agent2],
   API_KEY=keys,
   multi_agent_name="unique_name"
)


# Start session
agent_name = multi_agent.start()


# Close session
multi_agent.close()
```


### Conversation
```python
# Initialize conversation
conversation = IntitializeConversation(agent_name, extra_tool_key)


# Send query
response = conversation.send_query("Your query here")


# Parse response
text, content = conversation.parse_data(response)
```


## Configuration Options


### Character Configuration
```json
{
   "name": "agent_name",
   "clients": [],
   "modelProvider": "openai",
   "settings": {
       "secrets": {},
       "voice": {"model": "en_US-male-medium"}
   },
   "plugins": ["@elizaos/plugin-name"],
   "bio": ["Agent description"],
   "lore": ["Background story"]
}
```


### Environment Configuration
```json
{
   "OPENAI_API_KEY": "your-key",
   "TAVILY_API_KEY": "your-key",
   "OTHER_API_KEY": "your-key"
}
```


## Example Use Cases


### 1. Web Search Agent
```python
def create_web_search_agent():
   ai_model = Model(
       model="openai",
       name="llm",
       OPENAI_API_KEY=keys['openai'],
       bio=["Web search specialist"]
   )
  
   agent = Agent(
       name="websearch",
       agent_name='plugin-web-search',
       TAVILY_API_KEY=keys['tavily'],
       model=ai_model
   )
  
   return ai_model, agent
```


### 2. Market Analysis System
```python
def create_market_system(conversation):
   # Get market data
   price_response = conversation.send_query("What is BTC price?")
  
   # Analyze with web search
   analysis = conversation.send_query(
       "What's the BTC prediction this week?"
   )
  
   # Get AI analysis
   final_analysis = conversation.send_query(
       f"Analyze: Price {price_response['text']}, "
       f"Research: {analysis['text']}"
   )
  
   return final_analysis
```


## Advanced Usage Patterns


### 1. Multi-Agent Composition
```python
def create_multi_agent_system():
   # Create base model
   ai_model = Model(model="openai", name="llm")
  
   # Create specialized agents
   web_agent = Agent(name="web", agent_name='web-search')
   market_agent = Agent(name="market", agent_name='market-data')
   analysis_agent = Agent(name="analysis", agent_name='analysis')
  
   # Initialize multi-agent system
   system = InitializeAgent(
       agents=[web_agent, market_agent, analysis_agent],
       multi_agent_name="market_analysis_system"
   )
  
   return system
```


### 2. Error Handling
```python
def safe_query(conversation, query):
   try:
       response = conversation.send_query(query)
       if "error" in response:
           print(f"Query error: {response['error']}")
           return None
       return response
   except Exception as e:
       print(f"Exception during query: {str(e)}")
       return None
```


### 3. Session Management Pattern
```python
class AgentSession:
   def __init__(self, agents, name):
       self.multi_agent = InitializeAgent(agents, name)
       self.conversation = None
      
   def __enter__(self):
       agent_name = self.multi_agent.start()
       self.conversation = IntitializeConversation(
           agent_name,
           self.multi_agent.extra_tool_key
       )
       return self.conversation
      
   def __exit__(self, exc_type, exc_val, exc_tb):
       self.multi_agent.close()
```


This documentation provides a comprehensive overview of the Agent SDK's capabilities and usage patterns. For specific implementation details, refer to the individual source files and their inline documentation.
