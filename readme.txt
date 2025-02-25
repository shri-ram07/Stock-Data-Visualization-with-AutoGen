CalQuity AI Engineer Intern Assignment - Task 1: Stock Data Visualization with AutoGen

## Project Overview
This project demonstrates the creation of a chatbot system using AutoGen that fetches and visualizes stock data using the yfinance API and matplotlib. The chatbot can handle predefined queries, fetch stock data, and generate insightful visualizations.

### Key Features
- Multi-Agent System: Uses AutoGen's UserProxyAgent and AssistantAgent to manage conversations.
- Custom Tools: Fetches and visualizes stock data, including closing prices, volume, and moving averages.
- Advanced Queries: Handles complex queries like comparing multiple stocks or analyzing trends.

---

## Table of Contents
1. Setup Instructions
2. Code Explanation
3. Usage Examples
4. Demo Video
5. Contributing
6. License

---

## Setup Instructions

### Prerequisites
- Python 3.8 or higher
- Google Colab or a local Python environment

### Installation
1. Clone the repository:
   git clone https://github.com/your-username/calquity-ai-intern-task1.git
   cd calquity-ai-intern-task1

2. Install the required libraries:
   pip install pyautogen yfinance matplotlib

3. Replace the OpenAI API key in the code with your actual API key:
   config_list = [
       {
           "model": "gpt-4",  # Use GPT-4 or GPT-3.5
           "api_key": "your_openai_api_key_here"  # Replace with your OpenAI API key
       }
   ]

---

## Code Explanation

### 1. Custom Tool: fetch_and_visualize_stock_data
This function fetches stock data using yfinance and visualizes it using matplotlib.

import yfinance as yf
import matplotlib.pyplot as plt

def fetch_and_visualize_stock_data(stock_symbol: str, period: str = "1mo", include_volume: bool = False, moving_average: int = None, compare_symbol: str = None):
    """
    Fetches stock data and visualizes it.

    Args:
        stock_symbol (str): The stock symbol (e.g., "AAPL").
        period (str): The time period for the data (e.g., "1mo", "1y").
        include_volume (bool): Whether to include volume in the graph.
        moving_average (int): The window for the moving average (e.g., 30 for 30-day moving average).
        compare_symbol (str): Another stock symbol to compare with.

    Returns:
        str: A message indicating success or failure.
    """
    try:
        # Fetch stock data
        stock_data = yf.Ticker(stock_symbol)
        history = stock_data.history(period=period)

        # Plot the stock data
        plt.figure(figsize=(12, 6))
        plt.plot(history['Close'], label=f'{stock_symbol} Close Price')

        # Add moving average if specified
        if moving_average:
            history['MA'] = history['Close'].rolling(window=moving_average).mean()
            plt.plot(history['MA'], label=f'{moving_average}-Day Moving Average')

        # Add volume if specified
        if include_volume:
            plt.bar(history.index, history['Volume'], label='Volume', alpha=0.3)

        # Add comparison stock if specified
        if compare_symbol:
            compare_data = yf.Ticker(compare_symbol).history(period=period)
            plt.plot(compare_data['Close'], label=f'{compare_symbol} Close Price')

        # Customize the graph
        plt.title(f"Stock Data for {stock_symbol} ({period})")
        plt.xlabel("Date")
        plt.ylabel("Price (USD)")
        plt.legend()
        plt.grid()
        plt.show()

        return f"Stock data for {stock_symbol} fetched and visualized successfully."
    except Exception as e:
        return f"Error: {str(e)}"

### 2. Multi-Agent System
The chatbot uses AutoGen's UserProxyAgent and AssistantAgent to handle queries and execute tasks.

import autogen

# Configure the AutoGen agents
config_list = [
    {
        "model": "gpt-4",  # Use GPT-4 or GPT-3.5
        "api_key": "your_openai_api_key_here"  # Replace with your OpenAI API key
    }
]

# Create the AssistantAgent
assistant = autogen.AssistantAgent(
    name="assistant",
    llm_config={"config_list": config_list}
)

# Create the UserProxyAgent
user_proxy = autogen.UserProxyAgent(
    name="user_proxy",
    human_input_mode="NEVER",
    code_execution_config={"work_dir": "coding"},
    default_auto_reply="TERMINATE"
)

# Register the custom tool with the assistant
@user_proxy.register_for_execution()
@assistant.register_for_llm(description="Fetch and visualize stock data.")
def stock_tool(stock_symbol: str, period: str = "1mo", include_volume: bool = False, moving_average: int = None, compare_symbol: str = None):
    return fetch_and_visualize_stock_data(stock_symbol, period, include_volume, moving_average, compare_symbol)

---

## Usage Examples

### Example 1: Fetch and Visualize Stock Data
user_proxy.initiate_chat(
    assistant,
    message="Fetch and visualize the stock data for AAPL for the last 6 months."
)

### Example 2: Compare Two Stocks
user_proxy.initiate_chat(
    assistant,
    message="Compare the stock performance of AAPL and MSFT for the last 1 year."
)

### Example 3: Analyze Stock Trends
user_proxy.initiate_chat(
    assistant,
    message="Analyze the stock trend for TSLA for the last 2 years. Include the 50-day moving average."
)

---

## Demo Video
Watch the demo video to see the project in action:
Demo Video Link: https://youtu.be/your_video_link

---

## Contributing
Contributions are welcome! If you'd like to improve this project, please follow these steps:
1. Fork the repository.
2. Create a new branch (git checkout -b feature/YourFeature).
3. Commit your changes (git commit -m 'Add some feature').
4. Push to the branch (git push origin feature/YourFeature).
5. Open a pull request.

---

## License
This project is licensed under the MIT License. See the LICENSE file for details.

---

## Acknowledgments
- AutoGen (https://microsoft.github.io/autogen/) for the multi-agent framework.
- yfinance (https://pypi.org/project/yfinance/) for fetching stock data.
- matplotlib (https://matplotlib.org/) for data visualization.