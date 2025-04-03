# Smart Thermostat Diagnostic Assistant

## Project Description
An intelligent assistant system for diagnosing smart thermostat issues and automated creation of support tickets. The solution combines RAG (Retrieval Augmented Generation) with structured dialogue for efficient user request processing.

## Technology Stack

### Core Components:
1. **YandexGPT** - language model for response generation
2. **FAISS** - vector database for efficient information retrieval
3. **Sentence Transformers** - for text embeddings creation
4. **Python 3.11+** - main programming language

### Libraries Used:
- `faiss-cpu` - for vector search
- `sentence-transformers` - for embeddings creation
- `yandex-cloud-ml-sdk` - for YandexGPT integration
- `numpy` - for vector operations
- `datetime` - for timestamp handling
- `json` - for data serialization

## Solution Architecture

### 1. System Components:

#### YandexGPTClient
```python
class YandexGPTClient:
    def __init__(self, api_key: str, folder_id: str)
    def generate_response(self, prompt: str) -> str
```
- Handles interaction with YandexGPT
- Processes various model response formats
- Configured for thermostat assistant role

#### FAISSVectorStore
```python
class FAISSVectorStore:
    def __init__(self, dimension: int = 384)
    def add_documents(self, documents: List[Dict[str, Any]])
    def search(self, query: str, k: int = 3) -> List[Dict[str, Any]]
```
- Implements FAISS-based vector storage
- Uses Sentence Transformers for embeddings
- Provides semantic document search

#### DialogManager
```python
class DialogManager:
    def __init__(self, vector_store: FAISSVectorStore, llm_client: YandexGPTClient)
    def process_user_input(self, dialog_id: str, user_input: str) -> str
    def _save_support_ticket(self, ticket_info: Dict[str, Any]) -> str
```
- Manages user dialogue
- Processes user input
- Saves support tickets

### 2. Dialog Structure:

Dialog is built as a finite state machine with states:
1. welcome → current_temp
2. current_temp → desired_temp
3. desired_temp → time_of_day
4. time_of_day → duration_check
5. duration_check → create_ticket/wait_hour
6. create_ticket → ticket_created/end_conversation
7. wait_hour → end_conversation

### 3. Ticket Storage System:

Tickets are saved in JSON format:
```json
{
  "ticket_id": "ticket_20240320_123456",
  "status": "new",
  "created_at": "2024-03-20T12:34:56.789123",
  "problem_details": {
    "current_temp": 24.0,
    "desired_temp": 22.0,
    "time_of_day": "afternoon",
    "duration": "more than an hour"
  },
  "dialog_history": [
    {
      "step": "welcome",
      "user_input": "Thermostat not working",
      "timestamp": "2024-03-20T12:34:50.123456"
    }
  ],
  "device_info": {
    "type": "smart_thermostat",
    "error_state": true
  }
}
```

## Implementation Features

### 1. Error Handling
- Temperature validation (10°C - 35°C)
- Invalid input handling
- Profanity filtering

### 2. Smart Input Processing
- Numerical value extraction from text
- Time of day recognition
- Current time determination when user inputs "now"

### 3. Contextual Search
- RAG implementation for relevant information retrieval
- Dialog history consideration in response generation
- Problem context preservation

## Project Setup

1. Install dependencies:
```bash
pip install faiss-cpu sentence-transformers yandexcloud grpcio yandex-cloud-ml-sdk
```

2. Configure environment variables:
```python
api_key = "your_yandex_api_key"
folder_id = "your_folder_id"
```

3. Initialize components:
```python
vector_store = FAISSVectorStore()
llm_client = YandexGPTClient(api_key=api_key, folder_id=folder_id)
dialog_manager = DialogManager(vector_store, llm_client)
```

## Potential Improvements

1. **Technical Improvements:**
   - Database integration for ticket storage
   - Asynchronous request processing
   - Logging system implementation
   - Test coverage addition

2. **Functional Improvements:**
   - New diagnostic scenarios
   - Monitoring system integration
   - Problem statistics implementation
   - Feedback mechanism

3. **Interaction Improvements:**
   - Voice input support
   - Multi-language support
   - Enhanced natural language processing
   - Interactive hints implementation

## Code Examples

### Starting a New Dialog:
```python
dialog_id = dialog_manager.start_new_dialog("thermostat_diagnosis")
```

### Processing User Input:
```python
response = dialog_manager.process_user_input(dialog_id, "Thermostat shows error")
```

### Creating Support Ticket:
```python
ticket_info = {
    "current_temp": 24.0,
    "desired_temp": 22.0,
    "time_of_day": "afternoon",
    "dialog_history": dialog_history
}
ticket_id = dialog_manager._save_support_ticket(ticket_info)
```

## System Requirements
- Python 3.11 or higher
- 4GB RAM minimum
- Internet connection for YandexGPT API access

## Performance Considerations
- FAISS vector search is optimized for speed
- Response generation typically takes 1-2 seconds
- JSON ticket storage is suitable for moderate load

## Security
- API key protection
- Input sanitization
- Error state logging

## License
MIT License

## Contributing
1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request 