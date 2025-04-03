# Agent Server Documentation


## Server Architecture


### Overview
The Agent Server acts as a middleware between the Agent SDK and specialized servers (Eliza and Extra-Tools), handling:
- Database operations (DynamoDB)
- Session management
- Request routing
- Authentication
- Memory management


### Component Structure
```
agent-server/
├── server/
│   ├── core/
│   │   ├── agent_map.py      # Agent-tool mapping
│   │   ├── agent_session.py  # Session management
│   │   ├── memory.py         # Conversation history
│   │   ├── session_app.py    # Main Flask application
│   │   └── verify.py         # Authentication
│   └── utils/
│       └── db_utils.py       # Database operations
```


## Deployment Guide


### Prerequisites
```bash
# Required packages
pip install flask boto3 python-dotenv requests langchain openai faiss-cpu
```


### Environment Configuration
```bash
# .env file
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
OPENAI_API_KEY=your_openai_key
ELIZA_CREATE=http://eliza-server/create
ELIZA_QUERY=http://eliza-server/query
TOOLS_SET=http://tools-server/set
TOOLS_QUERY=http://tools-server/query
```


### Database Setup
```python
# Initialize DynamoDB tables
tables = {
   "agent_tool_id": {
       "primary_key": "multi_agent_main_name",
       "attributes": ["agent_id", "tools_agent_id", "history"]
   },
   "api_keys": {
       "primary_key": "api_key",
       "attributes": ["user_id", "expiration_date", "is_active"]
   }
}
```


## API Endpoints


### 1. Session Creation
```python
@app.route("/create_session", methods=["POST"])
def create_session():
   """
   Create new agent sessions
  
   Request:
   {
       "character_file": dict,
       "api_key": str,
       "env_json": dict,
       "multiple_agents_name": str,
       "multi_agent_main_name": str
   }
  
   Response:
   {
       "multi_agent_name": str,
       "eliza_response": dict,
       "tools_response": dict
   }
   """
```


### 2. Query Processing
```python
@app.route("/query", methods=["POST"])
def process_query():
   """
   Process user queries
  
   Request:
   {
       "query": str,
       "agent_name": str,
       "extra_tool_key": str
   }
  
   Response:
   {
       "status": str,
       "data1": dict,  # Eliza response
       "extra_tool_response": dict  # Tools response
   }
   """
```


### 3. Agent Information
```python
@app.route("/agent_info", methods=["POST"])
def get_agents_info():
   """
   Retrieve agent information
  
   Request:
   {
       "api_key": str,
       "user_id": str
   }
  
   Response:
   {
       "status": str,
       "data": {
           "user_id": str,
           "agents": dict
       }
   }
   """
```


## Session Management


### Session Creation Flow
```python
class Session:
   def create_session(self):
       """
       1. Generate unique session ID
       2. Create session folder
       3. Save character configuration
       4. Save environment variables
       5. Initialize connections to specialized servers
       """
```


### Session Storage
```python
class AgentToolMapper:
   def save_agent_tool_mapping(self, multi_agent_main_name, eliza_response, tools_response, api_key):
       """
       Store agent-tool mappings in DynamoDB:
       - Agent main name
       - Eliza agent ID
       - Tools agent ID
       - Session history
       """
```


### Memory Management
```python
def retrieve_relevant(user_input: str, dyno_list: List[str]) -> str:
   """
   Retrieve relevant conversation history:
   1. Create FAISS vector store
   2. Add conversation history
   3. Perform similarity search
   4. Return top 4 relevant interactions
   """
```


## Authentication and Security


### API Key Management
```python
class APIKeyManager:
   def create_api_key(self, user_id):
       """
       Generate and store API keys:
       1. Check for existing keys
       2. Generate UUID-based key
       3. Store in DynamoDB with expiration
       4. Return new key
       """
```


### Request Verification
```python
class ApiVerify:
   def verify(self, api_key):
       """Verify API key validity"""


class AgentVerify:
   def verify_agent_name(self, multi_agent_main_name, api_key, multiple_agents_name):
       """Verify agent existence and permissions"""
```


### Security Best Practices
1. API Key Protection:
  ```python
  # Environment variable usage
  api_key = os.getenv("API_KEY")
 
  # Secure storage
  item = {
      "api_key": {"S": new_api_key},
      "expiration_date": {"S": expiration},
      "is_active": {"BOOL": True}
  }
  ```


2. Request Validation:
  ```python
  def validate_request(data):
      required_fields = ["api_key", "query", "agent_name"]
      return all(field in data for field in required_fields)
  ```


## Scaling Considerations


### Database Scaling
```python
class DynamoDBClient:
   """
   Scalability features:
   - Auto-incrementing IDs
   - Batch operations
   - Efficient querying
   - Connection pooling
   """
```


### Memory Optimization
```python
def optimize_history(history_list: List[str], max_length: int = 20):
   """
   Maintain optimal history size:
   1. Keep latest 20 interactions
   2. Remove oldest entries
   3. Maintain vector store efficiency
   """
```


### Load Balancing
1. Session Distribution:
  ```python
  def distribute_sessions():
      """
     1. Monitor server load
     2. Distribute new sessions
     3. Balance existing sessions
     """
  ```


2. Request Routing:
  ```python
  def route_request(query, agent_name):
      """
     1. Check agent availability
     2. Route to least loaded server
     3. Handle failover
     """
  ```


### Performance Monitoring
```python
def monitor_performance():
   """
   Track:
   1. Response times
   2. Memory usage
   3. Database connections
   4. Error rates
   """
```


## Error Handling


### Database Operations
```python
try:
   response = self.client.put_item(
       TableName=table_name,
       Item=item,
       ConditionExpression=condition
   )
except ClientError as e:
   if e.response['Error']['Code'] == 'ConditionalCheckFailedException':
       raise ValueError("Item already exists")
   raise Exception(f"Failed to add item: {str(e)}")
```


### Request Processing
```python
@app.errorhandler(Exception)
def handle_error(error):
   return jsonify({
       "status": "error",
       "message": str(error),
       "type": error.__class__.__name__
   }), 500
```


## Configuration Options


### Server Settings
```python
SERVER_CONFIG = {
   "HOST": "0.0.0.0",  # Server host
   "PORT": 5000,       # Server port
   "DEBUG": False,     # Debug mode
   "THREADED": True    # Enable threading
}
```


### Memory Settings
```python
MEMORY_CONFIG = {
   "MAX_HISTORY_LENGTH": 20,     # Maximum conversation history length
   "RELEVANCE_THRESHOLD": 0.85,  # Minimum similarity score for relevant history
   "TOP_K_RESULTS": 4,          # Number of relevant history items to return
   "EMBEDDING_MODEL": "text-embedding-ada-002"  # OpenAI embedding model
}
```


### Database Settings
```python
DB_CONFIG = {
   "MAX_RETRIES": 3,            # Maximum retry attempts for DB operations
   "TIMEOUT": 5000,             # Connection timeout in milliseconds
   "MAX_CONNECTIONS": 100,      # Maximum connection pool size
   "TABLE_PREFIX": "prod_"      # Prefix for all DynamoDB tables
}
```


### Security Settings
```python
SECURITY_CONFIG = {
   "API_KEY_EXPIRY_DAYS": 30,   # API key validity period
   "MAX_REQUEST_SIZE": "10MB",  # Maximum request payload size
   "RATE_LIMIT": {
       "WINDOW": 3600,          # Time window in seconds
       "MAX_REQUESTS": 1000     # Maximum requests per window
   }
}
```


### Specialized Server Settings
```python
SERVER_ENDPOINTS = {
   "ELIZA": {
       "TIMEOUT": 10,           # Request timeout in seconds
       "MAX_RETRIES": 2,        # Maximum retry attempts
       "BATCH_SIZE": 50         # Maximum batch size for requests
   },
   "TOOLS": {
       "TIMEOUT": 15,
       "MAX_RETRIES": 2,
       "BATCH_SIZE": 25
   }
}
```


This documentation provides a comprehensive overview of the Agent Server's architecture, deployment, operational considerations, and configuration options. For specific implementation details, refer to the individual source files and their inline documentation.


