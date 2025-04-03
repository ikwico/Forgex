# CORE CONCEPTS
## Agents and Models
A typical agent based on the character generation system shown in the code. Here's how to structure an agent with all the available character attributes:


````python
from core.agent import Agent
from models.agent_model import Model


# Initialize the LLM model with full character definition
ai_model = Model(
   model="openai",  # Model provider (required)
   name="trading_expert",  # Agent name
   OPENAI_API_KEY="your_openai_key",
  
   # Core character attributes
   clients=["web", "telegram"],  # List of client platforms
   bio=[
       "You are an expert trading analyst with deep knowledge of financial markets.",
       "You specialize in technical analysis and market sentiment."
   ],
   lore=[
       "You have been analyzing markets for over 10 years.",
       "You have a proven track record of successful predictions."
   ],
  
   # Additional knowledge and examples
   knowledge=[
       "Technical analysis principles",
       "Market psychology",
       "Trading strategies"
   ],
   messageExamples=[
       "When BTC forms a double bottom pattern, it's often a bullish signal.",
       "The RSI entering oversold territory suggests potential buying opportunities."
   ],
   postExamples=[
       "Market Analysis: BTC showing strong support at $45,000 with increasing volume.",
       "Technical Alert: ETH/USD approaching key resistance level at $3,000."
   ],
  
   # Conversation style and topics
   topics=[
       "cryptocurrency",
       "technical analysis",
       "market trends",
       "trading strategies"
   ],
   style={
       "all": ["professional", "analytical"],
       "chat": ["concise", "informative"],
       "post": ["formal", "detailed"]
   },
   adjectives=[
       "precise",
       "knowledgeable",
       "analytical",
       "strategic"
   ]
)


# Create an agent with the defined model
trading_agent = Agent(
   name="trading_analysis",
   agent_name='plugin-trading',  # Plugin identifier
   TAVILY_API_KEY="your_tavily_key",
   model=ai_model
)


# Initialize multi-agent system
multi_agent = InitializeAgent(
   agents=[trading_agent],
   API_KEY="your_api_key",
   multi_agent_name="market_analyst"
)
````


This example shows how to create a trading expert agent. Here's what each character attribute means:


1. **Core Attributes**:
  - `model`: The LLM provider (e.g., "openai")
  - `name`: Identifier for the agent
  - `clients`: Platforms where the agent can operate


2. **Character Background**:
  - `bio`: Main characteristics and capabilities
  - `lore`: Background story and experience
  - `knowledge`: Specific areas of expertise


3. **Communication Examples**:
  - `messageExamples`: Sample chat responses
  - `postExamples`: Sample formal posts/announcements
  - `topics`: Main subjects the agent can discuss


4. **Personality**:
  - `style`: Communication style for different contexts
    - `all`: General style
    - `chat`: Conversational style
    - `post`: Formal communication style
  - `adjectives`: Personality traits


You can create different types of agents by adjusting these attributes. Here are a few examples:


````python
# News Analysis Agent
news_model = Model(
   model="openai",
   name="news_analyst",
   bio=["You are a news analyst specializing in crypto market news."],
   topics=["market news", "regulatory updates", "industry trends"],
   style={
       "all": ["journalistic", "objective"],
       "chat": ["informative", "clear"],
       "post": ["headline-style", "factual"]
   }
)


# Technical Analysis Agent
ta_model = Model(
   model="openai",
   name="tech_analyst",
   bio=["You are a technical analysis expert focusing on chart patterns."],
   topics=["chart patterns", "indicators", "price action"],
   style={
       "all": ["technical", "precise"],
       "chat": ["educational", "detailed"],
       "post": ["analytical", "data-driven"]
   }
)


# Market Sentiment Agent
sentiment_model = Model(
   model="openai",
   name="sentiment_analyst",
   bio=["You analyze market sentiment and social media trends."],
   topics=["social sentiment", "trend analysis", "market psychology"],
   style={
       "all": ["observant", "intuitive"],
       "chat": ["empathetic", "insightful"],
       "post": ["trend-focused", "predictive"]
   }
)
````


When creating agents, remember that:
1. All agents must have a valid model provider
2. The `plugins` field is automatically populated based on the agent_name
3. Character attributes should be consistent with the agent's intended purpose
4. You can combine multiple agents in a multi-agent system for more complex interactions



## Tools


### Available Tools


1. **Telegram Integration** (`tools.py`)
```python
send_telegram_message(message: str, telegram_cred, username)
```
- Sends messages to Telegram users using the Telethon library
- Requires Telegram API credentials and target username
- Handles connection lifecycle automatically


2. **Twitter Integration**
- Two implementations available:
 - API-based (`TwitterBot` class in `tools.py`)
 - Selenium-based (`TwitterSeleniumBot` class in `twitter.py`)
- Supports:
 - Text tweets
 - Media tweets
 - Scheduled tweets
 - Rate limiting handling


 3. **Extra Tools**
[Link](https://github.com/ikwico/Forgex/blob/main/COMPONENT%20DOCUMENTATION/Extra%20Tools.md)


### Tool Selection
- Tools are mapped to agents using `AgentToolMapper` class
- Each multi-agent setup has unique tool IDs stored in DynamoDB
- Tool mappings are created during session initialization

## Sessions


### Session Management


Sessions are handled by the `Session` class in `agent_session.py`:


```python
class Session:
   def __init__(self, character_file, env_file, api_key)
   def create_session()
   def close_session()
```


Key features:
- Unique session ID generation using API key hash
- Session folder creation for temporary storage
- Character and environment file management
- Automatic cleanup on session close


### Session Creation Flow
1. Client sends POST to `/create_session`
2. API validates credentials and agent name
3. Creates parallel sessions with Eliza and Extra-Tools servers
4. Maps agent IDs and tool IDs in DynamoDB
5. Returns session details to client

## Memory


Memory management is implemented in `memory.py` using LangChain and FAISS:


### Features
- Vector-based similarity search for relevant conversations
- OpenAI embeddings for semantic understanding
- Conversation history stored in DynamoDB
- Automatic retrieval of relevant past interactions


### Usage
```python
retrieve_relevant(user_input: str, dyno_list: List[str]) -> str
```
- Takes current user input and conversation history
- Returns most relevant past interactions
- Automatically limits to top 4 most relevant entries
- Maintains a maximum of 20 historical entries per agent

## Routing


### Request Flow
1. Client SDK → Agent Server
2. Agent Server → Authentication & Validation
3. Agent Server → Tool Selection & Routing
4. Parallel routing to:
  - Eliza Server (Web3 operations)
  - Extra-Tools Server (Market data)
5. Response aggregation and return


### Key Endpoints
- `/create_session`: Initialize new agent sessions
- `/query`: Process user queries and route to appropriate services
- `/agent_info`: Retrieve agent configuration and status
- `/create_api_key`: Generate new API credentials

## Authentication


### API Key Management
Implemented in `generate_api_key.py`:


```python
class APIKeyManager:
   def generate_api_key()
   def get_existing_api_key(user_id)
   def create_api_key(user_id)
```


Features:
- UUID4-based key generation
- User-based key management
- 90-day expiration
- Active status tracking


### Verification
Implemented in `verify.py`:


```python
class ApiVerify:
   def verify(api_key)


class AgentVerify:
   def verify_agent_name(multi_agent_main_name, api_key, multiple_agents_name)
   def save_agent_to_db(multi_agent_main_name, api_key, multiple_agents_name)
```


Security measures:
- API key validation on every request
- Agent name uniqueness verification
- User-agent mapping validation
- DynamoDB-based credential storage


### Database Integration
- DynamoDB tables for:
 - API keys
 - Agent configurations
 - Tool mappings
 - Session history
- Automatic ID generation
- Timestamp tracking
- Active status management

## Best Practices


1. **Error Handling**
  - Always check API responses
  - Implement proper cleanup in finally blocks
  - Log errors with appropriate detail


2. **Session Management**
  - Close sessions explicitly when done
  - Monitor session duration
  - Clean up temporary files


3. **Security**
  - Never expose API keys in logs
  - Validate all user inputs
  - Use secure communication channels


4. **Performance**
  - Implement proper connection pooling
  - Cache frequently accessed data
  - Monitor memory usage in vector stores


This documentation provides a high-level overview of the system's components and their interactions. For specific implementation details, refer to the individual source files and their inline documentation.
