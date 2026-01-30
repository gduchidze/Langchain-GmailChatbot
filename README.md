# Gmail AI Agent

An intelligent email assistant that automatically processes and responds to Gmail messages using AI. Built for Gianti Logistics to handle customer inquiries about transport and logistics services.

## Overview

This application monitors a Gmail inbox, processes unread emails using OpenAI's GPT-4, and automatically generates and sends appropriate responses. It maintains conversation history, tracks email threads, and provides a FastAPI interface for monitoring and manual intervention.

## Features

- **Automatic Email Processing**: Monitors Gmail inbox every 15 seconds for unread emails
- **AI-Powered Responses**: Uses OpenAI GPT-4o-mini to generate contextually relevant replies
- **Thread Awareness**: Maintains conversation history across email threads
- **Manual Override**: REST API endpoints for human intervention when needed
- **Professional Formatting**: Generates well-formatted HTML email responses
- **Conversation Tracking**: Stores chat history for all email threads

## Architecture

The application consists of several key components:

- **Main Application** (`main.py`): FastAPI server with background email processing
- **AI Service** (`app/services/ai_service.py`): LangChain-based AI agent for response generation
- **Gmail Services** (`app/services/gmail_services.py`): Custom Gmail operations (send, get, thread management)
- **Helper Functions** (`app/utils/helpers.py`): Email fetching, marking as read, thread message retrieval
- **Configuration** (`app/config.py`): Environment and credentials management
- **Prompts** (`app/prompt.py`): Business-specific instructions for the AI agent

## Requirements

```
openai
langchain
langchain-community
langchain_openai
google-auth-oauthlib
google-auth-httplib2
google-api-python-client
pydantic
python-dotenv
fastapi
uvicorn
langchain-core
langchain_google_community
```

## Setup

### 1. Prerequisites

- Python 3.8+
- Gmail account with API access enabled
- OpenAI API key

### 2. Google Cloud Setup

1. Create a project in [Google Cloud Console](https://console.cloud.google.com/)
2. Enable the Gmail API
3. Create OAuth 2.0 credentials
4. Download the credentials file as `credentials.json`
5. Place it in `app/credentials/credentials.json`

### 3. Installation

```bash
# Clone the repository
git clone https://github.com/gduchidze/gmail-ai-agent.git
cd gmail-ai-agent

# Install dependencies
pip install -r requirements.txt
```

### 4. Configuration

Create a `.env` file in the root directory:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

### 5. First Run (Authentication)

On the first run, you'll need to authenticate with Google:

```bash
python main.py
```

This will open a browser window for OAuth authentication. After successful authentication, a `token.json` file will be created in `app/credentials/`.

## Usage

### Running the Server

```bash
python main.py
```

The server will start on `http://0.0.0.0:8000` and begin monitoring emails every 15 seconds.

### API Endpoints

#### Get All Chats
```http
GET /chats
```
Returns all chat history for all email threads.

#### Get Thread IDs
```http
GET /thread_ids
```
Returns a list of all tracked email thread IDs.

#### Get Chat History for a Thread
```http
GET /chat_history/{thread_id}
```
Returns the complete message history for a specific thread.

#### Manual Response
```http
POST /manual_respond/{thread_id}
```
Send a manual response to a specific email thread.

**Request Body:**
```json
{
  "message": "Your response message",
  "to": "recipient@example.com",
  "subject": "Re: Original Subject",
  "thread_id": "thread_id_here"
}
```

## How It Works

1. **Email Monitoring**: Background task checks for unread emails every 15 seconds
2. **Thread Processing**: For each unread email:
   - Fetches the complete thread history
   - Stores messages in chat history
   - Analyzes the conversation context
3. **AI Response Generation**: 
   - Sends thread context to OpenAI GPT-4
   - Follows business-specific instructions
   - Generates appropriate response
4. **Email Sending**: 
   - Automatically sends the AI-generated response
   - Includes BCC to monitoring email
   - Marks original email as read
5. **History Tracking**: Stores all messages (user, assistant, bot) in memory

## Customization

### Modifying AI Instructions

Edit `app/prompt.py` to customize how the AI responds to emails. The current configuration is tailored for Gianti Logistics but can be adapted for any business.

### Adjusting Processing Interval

In `main.py`, modify the sleep duration:

```python
async def process_emails_periodically():
    while True:
        await assistant.process_emails()
        await asyncio.sleep(15)  # Change this value (in seconds)
```

## Project Structure

```
gmail-ai-agent/
├── main.py                          # FastAPI application & email processing
├── requirements.txt                 # Python dependencies
├── .gitignore                      # Git ignore file
├── app/
│   ├── config.py                   # Configuration & environment variables
│   ├── prompt.py                   # AI agent instructions
│   ├── services/
│   │   ├── ai_service.py          # LangChain AI service
│   │   └── gmail_services.py      # Gmail API operations
│   ├── utils/
│   │   └── helpers.py             # Utility functions
│   ├── models/
│   │   └── schemas.py             # Pydantic models
│   └── credentials/
│       ├── credentials.json        # OAuth credentials (not in repo)
│       └── token.json             # OAuth token (not in repo)
```

## Security Considerations

- Never commit `credentials.json` or `token.json` to version control
- Keep your OpenAI API key secure
- Use environment variables for sensitive data
- Consider implementing rate limiting for production use
- Review and audit AI-generated responses regularly

## Logging

The application uses Python's built-in logging module. Logs include:
- Email processing events
- Error messages
- Credential file status
- API operations

## Limitations

- Requires active internet connection
- Gmail API has daily quota limits
- AI responses depend on OpenAI API availability
- Chat history is stored in memory (lost on restart)

## Future Enhancements

- [ ] Persistent storage for chat history (database)
- [ ] Support for email attachments
- [ ] Advanced filtering and prioritization
- [ ] Multi-language support
- [ ] Response templates and caching
- [ ] Analytics dashboard
- [ ] Webhook support for real-time processing

## License

This project is private and proprietary.

## Support

For issues or questions, contact the development team.

---

**Note**: This is an automated system. Always monitor AI-generated responses to ensure quality and accuracy.
