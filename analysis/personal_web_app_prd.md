# Personal JustPrompt Web App - Product Requirements Document

## Document Information
- **Product Name**: JustPrompt Personal
- **Version**: 1.0
- **Date**: January 2025
- **Document Type**: Personal Use PRD
- **Target Users**: 1-10 users (personal/small team)

## 1. Executive Summary

### 1.1 Product Vision
A simplified web-based AI orchestration tool for personal use that enables multi-model prompting and the unique "CEO & Board" decision-making workflow without enterprise complexity.

### 1.2 Problem Statement
You want to:
- Compare responses across multiple AI models easily
- Use the CEO & Board decision-making feature in a web interface
- Have a simple, self-hosted solution for personal/small team use
- Avoid the complexity of enterprise features you don't need

### 1.3 Solution Overview
A lightweight web app that provides:
- Multi-model prompt execution with side-by-side comparison
- CEO & Board decision-making workflow
- Simple prompt management and history
- Easy deployment for personal use
- Clean, responsive interface for desktop and mobile

### 1.4 Success Criteria
- **Deployment**: Can be deployed with a single Docker command
- **Usability**: Intuitive interface that requires no training
- **Reliability**: Works consistently for daily personal use
- **Performance**: Fast responses for 1-10 concurrent requests

## 2. Core Features (Simplified)

### 2.1 Essential Features
1. **Multi-Model Prompting**: Send prompts to multiple models simultaneously
2. **CEO & Board Workflow**: Unique decision-making feature
3. **Response Comparison**: Side-by-side view of model responses
4. **Prompt History**: Save and revisit previous prompts
5. **API Key Management**: Secure storage of provider API keys
6. **File Upload**: Upload text files as prompts

### 2.2 Removed Enterprise Features
- Complex user management (just basic auth)
- Team workspaces and collaboration
- Billing and subscription management
- Advanced analytics and reporting
- SSO integration
- Rate limiting per user
- Complex role-based permissions

## 3. Functional Requirements

### 3.1 Authentication (Simplified)

#### FR-1: Basic Authentication
- **Description**: Simple login protection for the app
- **Implementation Options**:
  - **Option A**: Single admin password (simplest)
  - **Option B**: Basic user accounts (email/password only)
  - **Option C**: No auth (if deployed privately)

### 3.2 API Key Management

#### FR-2: Provider Configuration
- **Description**: Manage API keys for LLM providers
- **Acceptance Criteria**:
  - Form to enter/update API keys for each provider
  - Test connection button for each provider
  - Encrypted storage of keys
  - Visual status indicators (working/not working)

### 3.3 Core Functionality

#### FR-3: Multi-Model Prompting
- **Description**: Execute prompts across multiple selected models
- **Acceptance Criteria**:
  - Text area for prompt input
  - Checkboxes to select desired models
  - Submit button to execute across all selected models
  - Real-time progress indicators
  - Side-by-side response display

#### FR-4: CEO & Board Decision Making
- **Description**: The signature feature from the original codebase
- **Acceptance Criteria**:
  - Select multiple models as "board members"
  - Select one model as "CEO"
  - Automatic generation of CEO prompt with board responses
  - Clear visualization of the decision process
  - Final CEO decision display

#### FR-5: Prompt Management
- **Description**: Basic prompt history and management
- **Acceptance Criteria**:
  - Auto-save prompts to history
  - List of recent prompts with timestamps
  - Click to reload previous prompts
  - Simple search/filter by date
  - Delete unwanted prompts

#### FR-6: File Upload
- **Description**: Upload text files as prompts
- **Acceptance Criteria**:
  - Drag-and-drop file upload
  - Support .txt, .md files
  - File content preview
  - Use uploaded content as prompt

#### FR-7: Response Export
- **Description**: Export responses for external use
- **Acceptance Criteria**:
  - Export individual responses as text/markdown
  - Export comparison view as markdown
  - Copy to clipboard functionality

## 4. Technical Requirements (Simplified)

### 4.1 Backend Architecture

#### TR-1: Framework Choice
- **Technology**: FastAPI with Python 3.11+
- **Rationale**: 
  - Async support for concurrent model calls
  - Easy integration with existing just-prompt codebase
  - Automatic API documentation
  - Simple to deploy

#### TR-2: Database
- **Technology**: SQLite
- **Rationale**:
  - No separate database server needed
  - File-based, easy to backup
  - Sufficient for small-scale use
  - Zero configuration

#### TR-3: Provider Integration
- **Requirements**:
  - Direct migration of provider code from just-prompt
  - Maintain all existing provider implementations
  - Keep support for special features (reasoning effort, thinking tokens)

### 4.2 Frontend Architecture

#### TR-4: Frontend Framework
- **Technology**: React with TypeScript
- **Features**:
  - Single-page application
  - Responsive design for mobile
  - Real-time updates for streaming responses
  - Clean, minimal UI

#### TR-5: State Management
- **Technology**: React Context + useReducer
- **Rationale**: Simpler than Redux for small app

### 4.3 Deployment

#### TR-6: Containerization
- **Technology**: Docker with Docker Compose
- **Requirements**:
  - Single `docker-compose up` command deployment
  - Environment variables for configuration
  - Persistent volume for SQLite database
  - Automatic HTTPS with Let's Encrypt (optional)

## 5. Database Schema (Simplified)

### 5.1 Core Tables

```sql
-- API Keys table
CREATE TABLE api_keys (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    provider VARCHAR(50) UNIQUE NOT NULL,
    encrypted_key TEXT NOT NULL,
    is_valid BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Prompts table
CREATE TABLE prompts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(255),
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Responses table
CREATE TABLE responses (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    prompt_id INTEGER REFERENCES prompts(id),
    models TEXT NOT NULL, -- JSON array of model names
    responses TEXT NOT NULL, -- JSON object of responses
    response_type VARCHAR(20) DEFAULT 'multi_model', -- 'multi_model' or 'ceo_board'
    execution_time INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Basic settings table
CREATE TABLE settings (
    key VARCHAR(100) PRIMARY KEY,
    value TEXT,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 6. API Endpoints (Simplified)

### 6.1 Configuration
```
GET /api/providers - List available providers
GET /api/providers/{provider}/status - Check provider status
POST /api/api-keys - Save API key for provider
GET /api/api-keys - Get API key status for all providers
```

### 6.2 Core Features
```
POST /api/prompt/execute - Multi-model prompt execution
POST /api/prompt/ceo-board - CEO & Board decision making
GET /api/prompts - Get prompt history
POST /api/prompts - Save new prompt
DELETE /api/prompts/{id} - Delete prompt
POST /api/upload - Upload file for prompting
```

### 6.3 Real-time
```
WebSocket /ws/prompt - Real-time response streaming
```

## 7. User Interface Design

### 7.1 Main Layout
```
┌─────────────────────────────────────┐
│ JustPrompt Personal        Settings │
├─────────────────────────────────────┤
│ [Prompt Textarea]                   │
│                                     │
│ Model Selection:                    │
│ ☑ OpenAI GPT-4  ☑ Claude Sonnet   │
│ ☐ Gemini Pro    ☐ Groq Llama      │
│                                     │
│ [Execute] [CEO & Board] [Upload]    │
├─────────────────────────────────────┤
│ Response 1      │ Response 2        │
│ (GPT-4)         │ (Claude)          │
│                 │                   │
│                 │                   │
└─────────────────┴───────────────────┘
```

### 7.2 Core Pages

#### Dashboard/Main Page (`/`)
- **Purpose**: Primary interface for prompting
- **Components**:
  - Large prompt text area with markdown support
  - Model selection checkboxes grouped by provider
  - Action buttons (Execute, CEO & Board, Upload File)
  - Response comparison area
  - Recent prompts sidebar (collapsible)

#### Settings Page (`/settings`)
- **Purpose**: Configure API keys and basic settings
- **Components**:
  - API key forms for each provider
  - Test connection buttons
  - Provider status indicators
  - Basic app settings (theme, etc.)

#### CEO & Board Page (`/ceo-board`)
- **Purpose**: Specialized interface for CEO & Board workflow
- **Components**:
  - Board member selection
  - CEO model selection
  - Process visualization
  - Decision breakdown display

### 7.3 Mobile Responsive Design
- Collapsible sidebar for prompt history
- Stacked response layout on small screens
- Touch-friendly buttons and checkboxes
- Swipe gestures for response navigation

## 8. Implementation Plan

### 8.1 Phase 1: Core Backend (Week 1-2)
- FastAPI setup with basic routing
- SQLite database setup
- Migrate provider implementations from just-prompt
- Basic API endpoints for prompting

### 8.2 Phase 2: Frontend Foundation (Week 3)
- React app setup with TypeScript
- Basic UI components and layout
- API integration for providers and prompting
- Response display components

### 8.3 Phase 3: Core Features (Week 4)
- Multi-model prompting interface
- Response comparison view
- Prompt history functionality
- File upload feature

### 8.4 Phase 4: CEO & Board (Week 5)
- CEO & Board workflow implementation
- Decision process visualization
- Enhanced response analysis

### 8.5 Phase 5: Polish & Deploy (Week 6)
- Mobile responsiveness
- Docker containerization
- Documentation and deployment scripts
- Testing and bug fixes

## 9. Deployment Guide

### 9.1 Quick Start
```bash
# Clone and setup
git clone <your-repo>
cd justprompt-personal

# Configure environment
cp .env.example .env
# Edit .env with your API keys

# Deploy with Docker
docker-compose up -d

# Access at http://localhost:3000
```

### 9.2 Environment Variables
```bash
# Required API Keys (add the ones you want to use)
OPENAI_API_KEY=your_key_here
ANTHROPIC_API_KEY=your_key_here
GEMINI_API_KEY=your_key_here
GROQ_API_KEY=your_key_here
DEEPSEEK_API_KEY=your_key_here
OLLAMA_HOST=http://localhost:11434

# Optional
APP_SECRET_KEY=random_secret_for_encryption
APP_PASSWORD=optional_basic_auth_password
```

### 9.3 Docker Compose Configuration
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=sqlite:///data/app.db
    volumes:
      - ./data:/app/data
      - ./uploads:/app/uploads
    restart: unless-stopped
```

## 10. File Structure
```
justprompt-personal/
├── backend/
│   ├── app/
│   │   ├── api/          # API endpoints
│   │   ├── core/         # Config, database
│   │   ├── providers/    # LLM provider code (from just-prompt)
│   │   └── main.py       # FastAPI app
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── components/   # React components
│   │   ├── pages/        # Page components
│   │   ├── services/     # API calls
│   │   └── App.tsx
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml
├── .env.example
└── README.md
```

## 11. Security Considerations (Simplified)

### 11.1 Basic Security
- API keys encrypted at rest using Fernet
- HTTPS in production (via reverse proxy)
- Basic input validation and sanitization
- File upload restrictions (size, type)

### 11.2 Access Control
- Optional basic authentication with app password
- No complex user management needed
- Environment-based configuration

## 12. Future Enhancements (Optional)

### 12.1 Nice-to-Have Features
- Prompt templates/favorites
- Response export to various formats
- Basic usage statistics
- Dark/light theme toggle
- Keyboard shortcuts

### 12.2 Advanced Features (if needed later)
- Multiple user support
- Prompt sharing between users
- API for external integration
- Browser extension for quick access

## 13. Success Metrics (Personal Use)

### 13.1 Functional Goals
- Deploys successfully in under 5 minutes
- All provider integrations work correctly
- CEO & Board feature produces useful results
- Interface is intuitive for daily use

### 13.2 Performance Goals
- Page load time < 2 seconds
- Prompt execution starts immediately
- Responsive on mobile devices
- Handles 5-10 concurrent model calls without issues

---

This PRD provides a focused, implementable plan for a personal-use version of JustPrompt that maintains the core value proposition while eliminating enterprise complexity. The result is a clean, self-hosted tool perfect for individual or small team use.