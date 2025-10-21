# Vue Multi-Agent Creator

A modern web application for managing OpenAI assistants with a Vue.js frontend and FastAPI backend.

## 🚀 Live Demo

- **Frontend**: https://vue-multiagent-frontend-129438231958.us-central1.run.app
- **Backend**: https://vue-multiagent-backend-129438231958.us-central1.run.app
- **API Documentation**: https://vue-multiagent-backend-129438231958.us-central1.run.app/docs

## ✨ Features

- 🔐 **User Authentication** - Secure JWT token authentication with bcrypt password hashing
- 🤖 **Assistant Management** - Create, edit, update, and delete OpenAI assistants with full CRUD operations
- 💬 **Real-time Chat** - Live chat with responses using http sse connections
- 📁 **File Management** - Upload and manage files with OpenAI code interpreter integration
- 🔄 **Live Updates** - support for real-time chat and assistant interactions
- 📱 **Responsive Design** - Mobile-first design with Tailwind CSS
- ☁️ **Cloud Ready** - Fully deployed on Google Cloud Platform with Cloud Run and Cloud SQL
- 🎯 **Assistant-Specific Threads** - Each assistant maintains its own conversation history
- 🔧 **Form Validation** - Robust form validation with Vue composables

## 🆕 Latest Updates (2025-10-21)

### ✅ Image Display Persistence - FULLY WORKING
- **Fixed** image display after browser refresh - images now persist correctly
- **Resolved** OpenAI file purpose issue - images use `purpose='vision'` (downloadable) instead of `purpose='assistants'`
- **Fixed** timestamp display showing "Jan 21, 1970" - now shows correct dates with proper Unix timestamp conversion
- **Fixed** image type recognition - properly detects `image_file`, `image`, and `image/*` types
- **Removed** authentication requirement from image endpoint - allows browser `<img>` tags to load images
- **Fixed** image URL routing - uses absolute backend URLs instead of relative paths that caused 404 errors

### ✅ Forgot Password Feature
- **Added** complete password recovery workflow with email delivery
- **Implemented** Gmail SMTP integration with App Password authentication
- **Created** secure JWT reset tokens with 1-hour expiry
- **Designed** HTML email templates with reset links
- **Added** ForgotPasswordView and ResetPasswordView components

### ✅ Documentation
- **Created** comprehensive `docs/` directory with architecture, deployment, and authentication guides
- **Added** detailed changelog tracking all project changes
- **Included** token optimization strategies for cost management

## 🏗️ Architecture

- **Frontend**: Vue 3 + TypeScript + Vite + Tailwind CSS + Pinia
- **Backend**: FastAPI + SQLAlchemy + MySQL 8.0
- **Real-time**: WebSocket with native implementation
- **Deployment**: Docker + Google Cloud Run + Cloud SQL
- **Authentication**: JWT tokens with bcrypt hashing
- **Storage**: Google Cloud SQL with Secret Manager

## 🚀 Quick Start

### Prerequisites

- Node.js 18+
- Python 3.11+
- MySQL 8.0+
- OpenAI API key

### Development Setup

1. **Clone the repository**
```bash
git clone https://github.com/sbayer2/VueMultiAgentCreator.git
cd VueMultiAgentCreator
```

2. **Set up environment variables**
```bash
# Backend
cd backend
cp .env.example .env
# Edit .env with your configurations

# Frontend
cd ../frontend
cp .env.example .env.local
# Edit .env.local with your API URL
```

3. **Install dependencies**
```bash
# Backend
cd backend
pip install -r requirements.txt

# Frontend
cd ../frontend
npm install
```

4. **Start development servers**
```bash
# Backend (from backend directory)
python main.py

# Frontend (from frontend directory)
npm run dev
```

### 🐳 Docker Setup

```bash
# Start all services (frontend, backend, MySQL, Redis)
docker-compose up

# Build for production
docker-compose up --build
```

## 🌐 Production Deployment

### Google Cloud Run

The application is currently deployed on Google Cloud Platform:

1. **Set up Google Cloud project**
```bash
gcloud config set project mythic-aloe-467602-t4
```

2. **Deploy using Cloud Build**
```bash
gcloud builds submit --config cloudbuild.yaml
```

3. **Monitor logs**
```bash
gcloud logging read "resource.type=cloud_run_revision" --limit=20 --project=mythic-aloe-467602-t4
```

## 📖 API Documentation

Visit the interactive API documentation:
- **Swagger UI**: https://vue-multiagent-backend-129438231958.us-central1.run.app/docs
- **ReDoc**: https://vue-multiagent-backend-129438231958.us-central1.run.app/redoc

## 🗂️ Project Structure

```
VueMultiAgentCreator/
├── frontend/                    # Vue.js 3 frontend
│   ├── src/
│   │   ├── components/         # Reusable Vue components
│   │   │   ├── auth/          # Authentication components
│   │   │   ├── chat/          # Chat interface components
│   │   │   ├── assistant/     # Assistant management
│   │   │   └── common/        # Base UI components
│   │   ├── views/             # Page components
│   │   ├── stores/            # Pinia state management
│   │   ├── composables/       # Vue composables (useForm, useWebSocket)
│   │   ├── layouts/           # Layout components
│   │   └── utils/             # Utilities and API client
│   ├── public/                # Static assets
│   └── nginx.conf             # Production nginx config
├── backend/                    # FastAPI backend
│   ├── api/                   # API endpoints
│   │   ├── auth.py           # Authentication routes
│   │   ├── assistants.py     # Assistant CRUD operations
│   │   ├── chat.py           # Chat and messaging
│   │   └── files.py          # File upload/management
│   ├── models/               # SQLAlchemy database models
│   ├── utils/                # Configuration and utilities
│   └── main.py               # FastAPI application entry point
├── cloudbuild.yaml            # Cloud Build configuration
└── docker-compose.yaml       # Local development setup
```

## 🔧 Key Components

### Frontend Features
- **Vue 3 Composition API** with TypeScript
- **Pinia** for state management with persistence
- **Tailwind CSS** for responsive styling
- **Form validation** with custom composables
- **Real-time chat** with WebSocket integration
- **File upload** with drag-and-drop support

### Backend Features
- **FastAPI** with automatic OpenAPI documentation
- **SQLAlchemy ORM** with MySQL database
- **JWT authentication** with bcrypt password hashing
- **WebSocket** support for real-time communication
- **OpenAI Assistants API** integration
- **File management** with OpenAI code interpreter

### Database Models
- **User**: Authentication and user management
- **UserAssistant**: Assistant configurations and metadata
- **FileMetadata**: File upload tracking and management

## 🧪 Testing

### Development Testing
```bash
# Frontend linting and type checking
cd frontend
npm run lint
npm run type-check

# Backend testing
cd backend
python -m pytest
```

### Production Testing
- **Health Check**: `GET /health`
- **Database Test**: `GET /test-db`
- **Authentication Test**: Register/login flow

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📋 Development Commands

### Frontend Commands
```bash
npm run dev          # Start development server
npm run build        # Production build
npm run preview      # Preview production build
npm run lint         # Lint and auto-fix
npm run type-check   # TypeScript type checking
```

### Backend Commands
```bash
python main.py       # Start development server
uvicorn main:app --reload  # Alternative start method
```

### Cloud Deployment
```bash
gcloud builds submit --config cloudbuild.yaml  # Full deployment
gcloud logging read "resource.type=cloud_run_revision" --limit=20  # Check logs
```

## 📄 License

MIT License - see the [LICENSE](LICENSE) file for details.

## 🐛 Known Issues & Fixes

### ✅ Resolved Issues
- **Image Display After Refresh**: Fixed OpenAI file purpose and URL routing - images now persist after browser refresh (2025-10-21)
- **Timestamp Display**: Fixed "Jan 21, 1970" bug with proper Unix timestamp conversion (2025-10-21)
- **Image Type Recognition**: Fixed filter to detect all image types (image_file, image, image/*) (2025-10-21)
- **Image Authentication**: Removed auth requirement from image endpoint for browser compatibility (2025-10-21)
- **Update Assistant Button**: Fixed form validation and data population (2025-09-18)
- **File Management**: Implemented proper MMACTEMP pattern for image/document separation
- **Authentication Flow**: JWT token management with proper validation
- **Chat Initialization**: Assistant-specific thread management working correctly

## 🆘 Support

For issues and feature requests, please visit the [GitHub Issues](https://github.com/sbayer2/VueMultiAgentCreator/issues) page.