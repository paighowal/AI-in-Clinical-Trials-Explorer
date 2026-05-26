# AI Powered Clinical Trial Data Assistant

A conversational AI agent that allows you to query clinical trial data using natural language. Ask questions about trials, and the system intelligently converts them to SQL, retrieves relevant data, and generates visualizations.

## Overview

This project combines Large Language Models with database querying to create a natural language interface for clinical trial research data. Instead of writing SQL queries manually, you describe what you want to find in plain English, and the AI handles the technical details.

## Key Features

- **Natural Language Queries**: Ask questions like "Show me the top 10 trials by enrollment" or "What is the distribution of studies by phase?"
- **Multi-LLM Support**: Works with OpenAI, Gemini, Ollama, and Groq models
- **Intelligent SQL Generation**: Uses semantic understanding to map user intent to accurate SQL queries
- **Automatic Visualizations**: Selects and generates the most appropriate chart type for your data (bar, line, pie, scatter, histogram, box plots)
- **Data Insights**: Generates contextual analysis and summaries of retrieved results
- **Web Interface**: Interactive Gradio-based UI for easy exploration
- **Error Handling**: Robust exception handling and fallback mechanisms throughout

<img width="1700" height="852" alt="image" src="https://github.com/user-attachments/assets/c13dc51f-ed99-4b68-864b-775e6b0df01d" />

## A quick demo of how agentic AI can be used with RAG based chat bot on clinical trials data:
https://github.com/user-attachments/assets/aa35d499-3b67-4848-8281-566fc768a9d2

## Architecture

### Components

1. **LLM Integration** (`initialize_llm`)
   - Abstracted LLM provider selection
   - Configurable temperature and model settings
   - Support for multiple providers

2. **SQL Generation** (`intelligent_sql_generation`)
   - Semantic mapping of user questions to database columns
   - Context-aware SQL query generation
   - Validation against actual schema

3. **Data Processing** (`autonomous_response`)
   - Multi-threaded query execution
   - Concurrent insights generation and chart selection
   - Comprehensive response composition

4. **Visualization Engine** (`generate_enhanced_plot`)
   - Type-specific chart generation using Plotly
   - Smart axis and color column selection
   - Modern styling and hover information

5. **Web Interface**
   - Gradio-based chat UI
   - Real-time chat history
   - Side-by-side data and visualization panels

## Data Flow

```
User Question 
    ↓
SQL Generation (LLM + Schema Context)
    ↓
SQL Execution (SQLite)
    ↓
Data Insights (Concurrent: LLM analysis + Chart Selection)
    ↓
Visualization Generation
    ↓
Response Composition & Display
```

## Setup

### Requirements

```bash
pip install pandas sqlalchemy llama-index llama-index-core
pip install llama-index-llms-openai llama-index-llms-gemini
pip install llama-index-llms-ollama llama-index-llms-groq
pip install llama-index-embeddings-huggingface
pip install gradio plotly
pip install python-dotenv
```

### Configuration

1. Create a `.env` file with your API keys:
```
OPENAI_API_KEY=your_key
GEMINI_API_KEY=your_key
GROQ_API_KEY=your_key
HUGGINGFACE_API_KEY=your_key
```

2. Update the data paths in the script:
   - `input_folder`: Path to your CSV data
   - `csv_path`: Full path to the clinical trial CSV file

3. Set your preferred LLM provider:
```python
LLM_PROVIDER = "groq"  # Options: "openai", "gemini", "ollama", "groq"
```

### Database Setup

The system automatically creates a SQLite database (`trial_data.db`) from your CSV on first run. The CSV should be pipe-delimited with the following expected columns:

- `nct_id` / `studyid`: Unique trial identifier
- `brief_title` / `official_title`: Study name
- `enrollment`: Number of participants
- `phase`: Trial phase
- `sponsor_name`: Funding organization
- `overall_status`: Current trial status
- `conditions`: Medical conditions
- `start_date`, `completion_date`: Timeline
- And other clinical trial metadata

## Usage

### Running the Application

```bash
python text2sql_agent_ai.ipynb
```

Or in Jupyter:
```python
demo = create_gradio_interface()
demo.launch(server_name="127.0.0.1")
```

### Example Queries

- "Show me the top 10 clinical trials by enrollment"
- "What is the distribution of studies by phase?"
- "Which sponsors have the most trials?"
- "Show me trials for diabetes conditions"
- "List studies with the highest enrollment in phase 3"
- "How many trials are recruiting vs completed?"

## How It Works

### 1. Query Understanding
The system receives a natural language question and uses an LLM with semantic context to understand intent. The enhanced context includes:
- Actual column names and data types
- Sample data for reference
- Semantic mappings (e.g., "enrollment" → enrollment column)

### 2. SQL Generation
The LLM generates SQLite-compliant SQL based on the processed question and schema information. The prompt includes specific examples to guide accurate query generation.

### 3. Data Retrieval
SQL queries are executed with error handling for various result formats (tuples, lists, DataFrames, etc.).

### 4. Insight Generation & Chart Selection
Two concurrent processes:
- **Insights**: LLM analyzes the data to provide context-specific summaries
- **Chart Selection**: LLM examines data structure to choose the most appropriate visualization

### 5. Visualization
Plotly generates the selected chart type with:
- Automatic axis assignment
- Smart color column selection
- Consistent styling and hover information
- Fallback to table display if visualization isn't applicable

## Performance Considerations

- Uses concurrent execution for insights and chart selection (ThreadPoolExecutor)
- SQLite for fast local queries
- HuggingFace embeddings for semantic understanding
- Lazy database loading (CSV → SQLite conversion on first run)

## Error Handling

Comprehensive error handling includes:
- LLM initialization fallbacks
- SQL execution error recovery
- Chart generation failures with table fallback
- Invalid column handling
- Empty result set management

## Limitations

- SQLite is suitable for datasets up to several GB
- Chart visualization is optimized for datasets under 10,000 rows
- LLM accuracy depends on data quality and model capabilities
- Concurrent processing may require fine-tuning for very large result sets

## Future Enhancements

- Support for multi-table joins
- Query optimization and execution planning
- Caching frequently asked questions
- Export results to CSV/JSON
- Advanced filtering and aggregations
- Custom metric calculations

## License

This project is provided as-is for research and educational purposes.

## Notes

- The system works best with well-structured, clean data
- LLM provider choice affects speed and accuracy (Groq is fastest, OpenAI is most accurate)
- For production use, consider adding query validation and rate limiting
- Monitor API costs if using cloud-based LLM providers



