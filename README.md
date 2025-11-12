# AI Voice Assistant Vapi - Backend API

A comprehensive FastAPI backend system that powers voice assistant functionality through Vapi integration. This system manages todos, reminders, and calendar events with SQLite database storage and provides structured API endpoints for voice-activated commands.

## Author
**Macha Praveen**

## Overview

This project implements a sophisticated backend API designed to work with Vapi (Voice AI Platform) for creating voice-activated personal assistance features. The system provides full CRUD operations for todos, reminders, and calendar events with proper data validation and error handling.

## Features

### Core Functionality
- **Todo Management**: Create, read, update, complete, and delete tasks
- **Reminder System**: Set and manage reminders with importance levels
- **Calendar Integration**: Full calendar event management with datetime support
- **Voice Command Processing**: Structured API endpoints for Vapi tool calls
- **Database Management**: SQLite with SQLAlchemy ORM

### API Architecture
- **FastAPI Framework**: High-performance async API with automatic documentation
- **Pydantic Models**: Request/response validation and serialization
- **SQLAlchemy ORM**: Database abstraction with relationship management
- **Dependency Injection**: Database session management
- **Error Handling**: Comprehensive HTTP exception handling

## Technology Stack

- **FastAPI**: Modern Python web framework for building APIs
- **SQLAlchemy**: SQL toolkit and ORM
- **Pydantic**: Data validation using Python type annotations
- **SQLite**: Lightweight database for data persistence
- **Python 3.7+**: Core programming language

## Database Schema

### Tables Structure

```python
# Todo Model
class Todo(Base):
    __tablename__ = 'todos'
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, nullable=True)
    completed = Column(Boolean, default=False)

# Reminder Model  
class Reminder(Base):
    __tablename__ = 'reminders'
    id = Column(Integer, primary_key=True, index=True)
    reminder_text = Column(String)
    importance = Column(String)

# Calendar Event Model
class CalendarEvent(Base):
    __tablename__ = 'calendar_events'
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String)
    event_from = Column(DateTime)
    event_to = Column(DateTime)
```

## API Endpoints

### Todo Management

**Create Todo**
```python
POST /create_todo/
# Vapi Request Format:
{
  "message": {
    "toolCalls": [{
      "id": "call_id",
      "function": {
        "name": "createTodo",
        "arguments": {
          "title": "Task title",
          "description": "Task description"
        }
      }
    }]
  }
}
```

**Get Todos**
```python
POST /get_todos/
# Returns all todos with completion status
```

**Complete Todo**
```python
POST /complete_todo/
# Arguments: {"id": todo_id}
```

**Delete Todo**
```python
POST /delete_todo/
# Arguments: {"id": todo_id}
```

### Reminder Management

**Add Reminder**
```python
POST /add_reminder/
# Arguments: {
#   "reminder_text": "Reminder content",
#   "importance": "high|medium|low"
# }
```

**Get Reminders**
```python
POST /get_reminders/
# Returns all active reminders
```

**Delete Reminder**
```python
POST /delete_reminder/
# Arguments: {"id": reminder_id}
```

### Calendar Management

**Add Calendar Event**
```python
POST /add_calendar_entry/
# Arguments: {
#   "title": "Event title",
#   "description": "Event description",
#   "event_from": "2024-01-01T10:00:00",
#   "event_to": "2024-01-01T11:00:00"
# }
```

**Get Calendar Events**
```python
POST /get_calendar_entries/
# Returns all calendar events with datetime info
```

**Delete Calendar Event**
```python
POST /delete_calendar_entry/
# Arguments: {"id": event_id}
```

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd "AI Voice Assistant Vapi"

# Install dependencies
pip install fastapi uvicorn sqlalchemy pydantic python-multipart
```

## Usage

### Running the Server

```python
# Start the FastAPI server
uvicorn app:app --reload

# Server will be available at:
# http://localhost:8000
# API Documentation: http://localhost:8000/docs
```

### Vapi Integration

The API is designed to work with Vapi tool calls. Each endpoint expects a specific request format:

```python
class VapiRequest(BaseModel):
    message: Message

class Message(BaseModel):
    toolCalls: list[ToolCall]

class ToolCall(BaseModel):
    id: str
    function: ToolCallFunction

class ToolCallFunction(BaseModel):
    name: str
    arguments: str | dict
```

### Database Initialization

The database is automatically created when the application starts:

```python
Base.metadata.create_all(bind=engine)
```

## Code Examples

### Creating a Todo via API

```python
import requests

# Create todo through Vapi format
response = requests.post("http://localhost:8000/create_todo/", json={
    "message": {
        "toolCalls": [{
            "id": "call_123",
            "function": {
                "name": "createTodo",
                "arguments": {
                    "title": "Complete project documentation",
                    "description": "Write comprehensive README files"
                }
            }
        }]
    }
})

# Response format:
# {
#   "results": [{
#     "toolCallId": "call_123",
#     "result": "success"
#   }]
# }
```

### Adding Calendar Event

```python
# Add calendar event
response = requests.post("http://localhost:8000/add_calendar_entry/", json={
    "message": {
        "toolCalls": [{
            "id": "call_456", 
            "function": {
                "name": "addCalendarEntry",
                "arguments": {
                    "title": "Team Meeting",
                    "description": "Weekly sync with development team",
                    "event_from": "2024-01-15T10:00:00",
                    "event_to": "2024-01-15T11:00:00"
                }
            }
        }]
    }
})
```

## Error Handling

The API includes comprehensive error handling:

```python
# Missing required fields
raise HTTPException(status_code=400, detail="Missing required fields")

# Resource not found
raise HTTPException(status_code=404, detail="Todo not found")

# Invalid request format
raise HTTPException(status_code=400, detail="Invalid Request")

# Invalid date format
raise HTTPException(status_code=400, detail="Invalid date format. Use ISO format.")
```

## Project Structure

```
AI Voice Assistant Vapi/
├── app.py                  # Main FastAPI application
├── README.md               # This documentation
└── database.db             # SQLite database (auto-generated)
```

## Response Models

The API uses Pydantic models for structured responses:

```python
class TodoResponse(BaseModel):
    id: int
    title: str
    description: Union[str, None]
    completed: bool
    
    class Config:
        orm_mode = True

class ReminderResponse(BaseModel):
    id: int
    reminder_text: str
    importance: str

class CalendarEventResponse(BaseModel):
    id: int
    title: str
    description: Union[str, None]
    event_from: datetime
    event_to: datetime
```

## Development Features

- **Auto-reload**: Development server with hot reload
- **Interactive Documentation**: Swagger UI at `/docs`
- **Type Safety**: Full typing with Pydantic and SQLAlchemy
- **Database Migrations**: Automatic table creation
- **Session Management**: Proper database connection handling

## Use Cases

### Personal Assistant Integration
- Voice-activated todo creation and management
- Calendar event scheduling through voice commands
- Reminder setting with importance prioritization

### Smart Home Integration
- Task management for household activities
- Appointment scheduling and reminders
- Family calendar coordination

### Business Applications
- Meeting scheduling and task tracking
- Project milestone reminders
- Team coordination through voice commands

## Requirements

- Python 3.7+
- FastAPI
- SQLAlchemy
- Pydantic
- Uvicorn (ASGI server)
- Vapi platform account for voice integration

## Future Enhancements

- **Authentication**: User-based access control
- **Notifications**: Push notification integration
- **Recurring Events**: Support for repeating calendar events
- **Categories**: Todo and reminder categorization
- **Search**: Full-text search across all data
- **Export**: Data export capabilities

## License

This project is available for educational and research purposes.

---

*This backend API demonstrates professional-grade FastAPI development with voice assistant integration, showcasing modern Python web development practices.*"# -VoiceTask-Pro-FastAPI-Voice-Assistant-Backend" 
