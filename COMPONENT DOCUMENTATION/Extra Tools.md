# 4.3 Extra Tools Documentation


## Table of Contents
1. [Overview](#overview)
2. [Installation & Setup](#installation--setup)
3. [Available Tools](#available-tools)
4. [API Integration](#api-integration)
5. [Database Integration](#database-integration)
6. [Custom Tool Creation](#custom-tool-creation)
7. [Usage Examples](#usage-examples)


## Overview


The Extra Tools module provides a comprehensive suite of cryptocurrency and financial market analysis tools. It includes capabilities for:


- Market data analysis (RSI, MACD, EMA, Bollinger Bands)
- News and sentiment analysis
- Real-time price tracking
- Social media integration (Farcaster)
- Trading automation (Binance)
- Instagram posting
- Database management (DynamoDB)


## Installation & Setup


### Prerequisites
```bash
pip install -r requirements.txt
```


Required packages include:
- FastAPI
- google.generativeai
- binance-python
- selenium
- boto3
- pandas
- requests
- beautifulsoup4


### Environment Variables
Create a `.env` file with the following API keys:
```
GEMINI=your_gemini_api_key
TWELVE_API=your_twelve_data_api_key
ALPHA_VANTAGE=your_alphavantage_api_key
CRYPTO_PANIC=your_cryptopanic_api_key
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
FARCASTER=your_farcaster_mnemonic
```


## Available Tools


### 1. Market Analysis Tools
#### Technical Indicators
- **RSI (Relative Strength Index)**
 ```python
 from indicators import rsi, intraday_rsi
  # Daily RSI
 result = rsi(alpha="your_api_key", period=14, symbol="BTC")
  # Intraday RSI
 intraday_result = intraday_rsi(alpha="your_api_key", period=14, symbol="BTC")
 ```


- **MACD (Moving Average Convergence Divergence)**
 ```python
 from indicators import macd, intraday_macd
  # Parameters
 result = macd(alpha="your_api_key",
               fast_period=12,
               slow_period=26,
               signal_period=9,
               symbol="BTC")
 ```


- **EMA (Exponential Moving Average)**
 ```python
 from indicators import Ema, intraday_Ema
  result = Ema(alpha="your_api_key", period=20, symbol="BTC")
 ```


- **Bollinger Bands**
 ```python
 from indicators import bollinger_bands, intraday_bollinger_bands
  result = bollinger_bands(alpha="your_api_key",
                         window=20,
                         num_std_dev=2,
                         symbol="BTC")
 ```


### 2. News and Market Data
```python
from news import get_news, real_time_price
from alphavantage import news_sentiment, currency_exchange_rates


# Get crypto news
news = get_news(filter="rising",
               currencies="BTC,ETH",
               regions="en",
               crypto_panic="your_api_key")


# Get real-time price
price = real_time_price(twelve="your_api_key",
                      symbol="BTC",
                      relative_to="USD")


# Get news sentiment
sentiment = news_sentiment(alpha="your_api_key",
                        tickers="COIN,CRYPTO:BTC",
                        topics="blockchain")
```


### 3. Trading Integration (Binance)
```python
from binance_script import get_current_price, get_balance_future, get_open_positions


# Get current price
price = get_current_price(api_key="your_key",
                        api_secret="your_secret",
                        symbol="BTCUSDT")


# Get account balance
balance = get_balance_future(api_key="your_key", api_secret="your_secret")


# Get open positions
positions = get_open_positions(api_key="your_key", api_secret="your_secret")
```


### 4. Social Media Integration


#### Farcaster
```python
from WARP import (farcaster_publish_cast, farcaster_get_followers,
                farcaster_get_user_info, farcaster_stream_recent_casts)


# Publish a cast
hash = farcaster_publish_cast(text="Hello Farcaster!", mnemonic="your_mnemonic")


# Get user followers
followers = farcaster_get_followers(mnemonic="your_mnemonic", cast_id="user_id")
```


#### Instagram
```python
from instagram import post_to_instagram


post_to_instagram(username="your_username",
                password="your_password",
                image_path="/path/to/image.jpg",
                caption="Your post caption")
```


## API Integration


The module provides a FastAPI-based REST API for accessing all tools:


```python
from fastapi import FastAPI
app = FastAPI()


@app.post("/search")
async def search(request: SearchRequest):
   # Search implementation
   pass


@app.post("/configure")
async def configure(config: ConfigRequest):
   # Configuration implementation
   pass
```


## Database Integration


DynamoDB is used for storing user configurations and API keys:


```python
from db_utils import DynamoDBClient


# Initialize client
db_client = DynamoDBClient()


# Add item
item = {
   "user_id": {"N": "123"},
   "api_key": {"S": "your_key"},
   "date_created": {"S": "2024-03-14"}
}
db_client.add_item("table_name", item)
```


## Custom Tool Creation


To create a new tool:


1. Create a new Python file for your tool
2. Add tool description to `composio.json`
3. Import and register the tool in `main.py`
4. Update the API endpoints if needed


Example:
```python
def custom_tool(param1, param2):
   try:
       # Tool implementation
       return result
   except Exception as e:
       return {"error": f"Tool failed: {str(e)}"}
```


## Usage Examples


### Complete Workflow Example
```python
from main import PolkaToolSearch


# Initialize with API keys
api_keys = {
   "GEMINI": "your_key",
   "twelve": "your_key",
   "alpha": "your_key",
   "crypto_panic": "your_key"
}


# Create search tool
search_tool = PolkaToolSearch(api_keys=api_keys)


# Execute search
result = search_tool.search("give me analysis of intraday fluctuations for btc")
```


This documentation provides a comprehensive overview of the Extra Tools module. For specific implementation details or additional features, please refer to the individual tool documentation or source code.
