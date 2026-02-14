# Tutor AI - AI-Powered Personalized Learning Platform - Design Document

## 1. System Architecture

### 1.1 High-Level Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend API   │    │   AI Services   │
│   (React.js)    │◄──►│   (Node.js)     │◄──►│   (Python/LLM)  │
│   Tailwind CSS  │    │   Express.js    │    │   NLP/Vision    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │                        │
                              ▼                        ▼
                    ┌─────────────────┐    ┌─────────────────┐
                    │   PostgreSQL    │    │  File Storage   │
                    │   Database      │    │   (AWS S3)      │
                    └─────────────────┘    └─────────────────┘
                              │                        │
                              ▼                        ▼
                    ┌─────────────────┐    ┌─────────────────┐
                    │ Authentication  │    │ Image Gen API   │
                    │     (JWT)       │    │ (Diffusion)     │
                    └─────────────────┘    └─────────────────┘
```

### 1.2 Component Overview
- **Frontend**: React.js application with Tailwind CSS for responsive UI
- **Backend API**: Node.js/Express.js REST API with JWT authentication
- **AI Services**: Python-based services for LLM, NLP, and image generation
- **Database**: PostgreSQL for structured data storage
- **File Storage**: AWS S3 for book files and generated images
- **Authentication**: JWT-based user authentication and authorization
- **Image Generation**: Integration with diffusion models for visual content

## 2. Data Models

### 2.1 User Model
```typescript
interface User {
  id: string;
  email: string;
  name: string;
  skillLevel: 'beginner' | 'intermediate' | 'advanced';
  preferences: UserPreferences;
  createdAt: Date;
  updatedAt: Date;
}

interface UserPreferences {
  dailyStudyTime: number; // minutes
  learningStyle: 'visual' | 'textual' | 'mixed';
  notificationsEnabled: boolean;
  preferredStudyTime: string; // HH:MM format
  timezone: string;
}
```

### 2.2 Learning Path Model
```typescript
interface LearningPath {
  id: string;
  userId: string;
  title: string;
  goal: string;
  skillLevel: 'beginner' | 'intermediate' | 'advanced';
  estimatedDuration: number; // weeks
  status: 'active' | 'completed' | 'paused';
  progress: number; // percentage
  modules: LearningModule[];
  createdAt: Date;
  updatedAt: Date;
}

interface LearningModule {
  id: string;
  title: string;
  description: string;
  order: number;
  estimatedTime: number; // minutes
  sessions: LearningSession[];
  prerequisites: string[];
  resources: Resource[];
}

interface LearningSession {
  id: string;
  title: string;
  content: string;
  sessionType: 'reading' | 'practice' | 'quiz' | 'project';
  estimatedTime: number; // minutes
  completed: boolean;
  completedAt?: Date;
  visualContent: VisualContent[];
}
```

### 2.3 Book Processing Model
```typescript
interface UploadedBook {
  id: string;
  userId: string;
  originalName: string;
  fileName: string;
  fileSize: number;
  mimeType: string;
  uploadedAt: Date;
  processingStatus: 'pending' | 'processing' | 'completed' | 'failed';
  extractedContent?: ExtractedBookContent;
}

interface ExtractedBookContent {
  title: string;
  author?: string;
  totalPages: number;
  chapters: BookChapter[];
  estimatedReadingTime: number; // minutes
  difficulty: 'beginner' | 'intermediate' | 'advanced';
  prerequisites: string[];
  keyTopics: string[];
}

interface BookChapter {
  id: string;
  number: number;
  title: string;
  content: string;
  wordCount: number;
  estimatedTime: number; // minutes
  visualContent: VisualContent[];
  keyPoints: string[];
}
```

### 2.4 Visual Content Model
```typescript
interface VisualContent {
  id: string;
  type: 'diagram' | 'infographic' | 'illustration' | 'chart';
  url: string;
  altText: string;
  caption: string;
  prompt: string; // AI generation prompt
  relatedConcept: string;
  generatedAt: Date;
  optimizedUrls: {
    thumbnail: string;
    medium: string;
    large: string;
  };
}
```

### 2.5 Assessment and Analytics Model
```typescript
interface Quiz {
  id: string;
  moduleId: string;
  title: string;
  questions: QuizQuestion[];
  passingScore: number;
  timeLimit?: number; // minutes
}

interface QuizQuestion {
  id: string;
  question: string;
  type: 'multiple_choice' | 'true_false' | 'short_answer';
  options?: string[];
  correctAnswer: string;
  explanation: string;
  difficulty: 'easy' | 'medium' | 'hard';
}

interface UserProgress {
  id: string;
  userId: string;
  learningPathId: string;
  completedSessions: string[];
  quizScores: QuizResult[];
  totalTimeSpent: number; // minutes
  currentStreak: number; // days
  lastActivityAt: Date;
  performanceMetrics: PerformanceMetrics;
}

interface PerformanceMetrics {
  averageQuizScore: number;
  learningVelocity: number; // sessions per week
  retentionRate: number; // percentage
  strongAreas: string[];
  weakAreas: string[];
}
```

## 3. Core Components

### 3.1 Learning Path Generator (AI Service)
```python
class LearningPathGenerator:
    def __init__(self, llm_client, embedding_model):
        self.llm_client = llm_client
        self.embedding_model = embedding_model
    
    def generate_path(self, user_profile: UserProfile, goal: str) -> LearningPath:
        """Generate personalized learning path based on user profile and goal"""
        
    def adapt_path(self, path: LearningPath, performance: PerformanceMetrics) -> LearningPath:
        """Adapt existing path based on user performance"""
        
    def recommend_resources(self, topic: str, skill_level: str) -> List[Resource]:
        """Recommend cost-effective learning resources"""
        
    def estimate_timeline(self, content_volume: int, daily_time: int) -> int:
        """Estimate realistic completion timeline"""
```

### 3.2 Book Processor (AI Service)
```python
class BookProcessor:
    def __init__(self, nlp_model, text_extractor):
        self.nlp_model = nlp_model
        self.text_extractor = text_extractor
    
    def extract_content(self, file_path: str) -> ExtractedBookContent:
        """Extract and structure content from uploaded book"""
        
    def analyze_difficulty(self, content: str) -> str:
        """Determine content difficulty level"""
        
    def identify_prerequisites(self, content: ExtractedBookContent) -> List[str]:
        """Identify required background knowledge"""
        
    def chunk_content(self, content: ExtractedBookContent, daily_time: int) -> List[LearningSession]:
        """Divide content into time-appropriate learning sessions"""
        
    def extract_key_concepts(self, text: str) -> List[str]:
        """Extract key concepts for visual generation"""
```

### 3.3 Visual Content Generator (AI Service)
```python
class VisualContentGenerator:
    def __init__(self, text_to_image_model):
        self.text_to_image_model = text_to_image_model
    
    def generate_visual_content(self, concept: str, context: str) -> VisualContent:
        """Generate educational visual content for concepts"""
        
    def create_diagram(self, process_description: str) -> VisualContent:
        """Create process diagrams and flowcharts"""
        
    def generate_infographic(self, data_points: List[str]) -> VisualContent:
        """Create infographic-style visualizations"""
        
    def optimize_for_learning(self, image: VisualContent, target_audience: str) -> VisualContent:
        """Optimize images for educational effectiveness"""
        
    def generate_alt_text(self, image: VisualContent) -> str:
        """Generate accessibility-friendly descriptions"""
```

### 3.4 Adaptive Learning Engine
```typescript
class AdaptiveLearningEngine {
  analyzePerformance(userId: string): Promise<PerformanceMetrics>
  
  adjustLearningPath(pathId: string, performance: PerformanceMetrics): Promise<LearningPath>
  
  recommendNextSession(userId: string, pathId: string): Promise<LearningSession>
  
  identifyWeakAreas(quizResults: QuizResult[]): Promise<string[]>
  
  calculateLearningVelocity(userId: string): Promise<number>
}
```

### 3.5 Resource Recommendation Engine
```typescript
class ResourceRecommendationEngine {
  findCostEffectiveResources(topic: string, budget: number): Promise<Resource[]>
  
  rankResourcesByQuality(resources: Resource[]): Promise<Resource[]>
  
  filterBySkillLevel(resources: Resource[], skillLevel: string): Promise<Resource[]>
  
  getAlternativeResources(primaryResource: Resource): Promise<Resource[]>
}
```

## 4. API Design

### 4.1 Authentication Endpoints
```
POST /api/auth/register
POST /api/auth/login
POST /api/auth/refresh
POST /api/auth/logout
POST /api/auth/forgot-password
POST /api/auth/reset-password
```

### 4.2 User Management Endpoints
```
GET /api/users/profile
PUT /api/users/profile
POST /api/users/skill-assessment
PUT /api/users/preferences
GET /api/users/progress
```

### 4.3 Learning Path Endpoints
```
POST /api/learning-paths
GET /api/learning-paths
GET /api/learning-paths/:id
PUT /api/learning-paths/:id
DELETE /api/learning-paths/:id
POST /api/learning-paths/:id/adapt
GET /api/learning-paths/:id/progress
```

### 4.4 Book Processing Endpoints
```
POST /api/books/upload
GET /api/books/:id/status
GET /api/books/:id/content
POST /api/books/:id/generate-template
GET /api/books/:id/template
POST /api/books/:id/generate-visuals
```

### 4.5 Session and Quiz Endpoints
```
GET /api/sessions/:id
PUT /api/sessions/:id/complete
POST /api/quizzes/:id/submit
GET /api/quizzes/:id/results
GET /api/analytics/performance
```

## 5. Frontend Architecture

### 5.1 Component Structure
```
src/
├── components/
│   ├── common/
│   │   ├── Header.jsx
│   │   ├── Navigation.jsx
│   │   └── Footer.jsx
│   ├── auth/
│   │   ├── LoginForm.jsx
│   │   ├── RegisterForm.jsx
│   │   └── SkillAssessment.jsx
│   ├── learning/
│   │   ├── LearningPathCard.jsx
│   │   ├── SessionViewer.jsx
│   │   ├── ProgressTracker.jsx
│   │   └── QuizComponent.jsx
│   ├── books/
│   │   ├── BookUpload.jsx
│   │   ├── BookProcessor.jsx
│   │   └── VisualTemplate.jsx
│   └── dashboard/
│       ├── Dashboard.jsx
│       ├── Analytics.jsx
│       └── Settings.jsx
├── pages/
├── hooks/
├── services/
├── utils/
└── styles/
```

### 5.2 State Management
```typescript
// Using React Context + useReducer for state management
interface AppState {
  user: User | null;
  learningPaths: LearningPath[];
  currentSession: LearningSession | null;
  uploadedBooks: UploadedBook[];
  progress: UserProgress;
  loading: boolean;
  error: string | null;
}

// Actions for state updates
type AppAction = 
  | { type: 'SET_USER'; payload: User }
  | { type: 'ADD_LEARNING_PATH'; payload: LearningPath }
  | { type: 'UPDATE_PROGRESS'; payload: UserProgress }
  | { type: 'SET_LOADING'; payload: boolean }
  | { type: 'SET_ERROR'; payload: string };
```

### 5.3 Responsive Design System
```css
/* Tailwind CSS utility classes for responsive design */
.container {
  @apply mx-auto px-4 sm:px-6 lg:px-8;
}

.card {
  @apply bg-white rounded-lg shadow-md p-6 mb-4;
}

.btn-primary {
  @apply bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition-colors;
}

.progress-bar {
  @apply w-full bg-gray-200 rounded-full h-2.5;
}

.visual-content {
  @apply max-w-full h-auto rounded-lg shadow-sm;
}
```

## 6. AI Integration Architecture

### 6.1 Large Language Model Integration
```python
class LLMService:
    def __init__(self, model_name: str, api_key: str):
        self.model_name = model_name
        self.api_key = api_key
    
    async def analyze_content(self, text: str) -> ContentAnalysis:
        """Analyze text content for learning path generation"""
        
    async def generate_quiz_questions(self, content: str, difficulty: str) -> List[QuizQuestion]:
        """Generate quiz questions from content"""
        
    async def extract_key_concepts(self, text: str) -> List[str]:
        """Extract key learning concepts"""
        
    async def assess_difficulty(self, content: str) -> str:
        """Assess content difficulty level"""
```

### 6.2 Text-to-Image Generation
```python
class TextToImageService:
    def __init__(self, model_endpoint: str):
        self.model_endpoint = model_endpoint
    
    async def generate_educational_image(self, prompt: str, style: str = "educational") -> str:
        """Generate educational images from text prompts"""
        
    async def create_diagram(self, description: str) -> str:
        """Create diagrams and flowcharts"""
        
    async def generate_infographic(self, data: Dict) -> str:
        """Generate infographic-style visualizations"""
```

### 6.3 Natural Language Processing
```python
class NLPService:
    def __init__(self):
        self.tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
        self.model = AutoModel.from_pretrained("bert-base-uncased")
    
    def extract_text_from_pdf(self, file_path: str) -> str:
        """Extract text from PDF files"""
        
    def extract_text_from_epub(self, file_path: str) -> str:
        """Extract text from EPUB files"""
        
    def chunk_text(self, text: str, max_chunk_size: int) -> List[str]:
        """Chunk text into manageable sections"""
        
    def extract_prerequisites(self, text: str) -> List[str]:
        """Extract prerequisite knowledge from text"""
```

## 7. Database Schema

### 7.1 Core Tables
```sql
-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    skill_level VARCHAR(20) CHECK (skill_level IN ('beginner', 'intermediate', 'advanced')),
    preferences JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Learning paths table
CREATE TABLE learning_paths (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    goal TEXT NOT NULL,
    skill_level VARCHAR(20) NOT NULL,
    estimated_duration INTEGER, -- weeks
    status VARCHAR(20) DEFAULT 'active',
    progress DECIMAL(5,2) DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Learning modules table
CREATE TABLE learning_modules (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    learning_path_id UUID REFERENCES learning_paths(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    order_index INTEGER NOT NULL,
    estimated_time INTEGER, -- minutes
    prerequisites TEXT[],
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Learning sessions table
CREATE TABLE learning_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    module_id UUID REFERENCES learning_modules(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    session_type VARCHAR(20) CHECK (session_type IN ('reading', 'practice', 'quiz', 'project')),
    estimated_time INTEGER, -- minutes
    completed BOOLEAN DEFAULT FALSE,
    completed_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Uploaded books table
CREATE TABLE uploaded_books (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    original_name VARCHAR(255) NOT NULL,
    file_name VARCHAR(255) NOT NULL,
    file_size BIGINT NOT NULL,
    mime_type VARCHAR(100) NOT NULL,
    processing_status VARCHAR(20) DEFAULT 'pending',
    extracted_content JSONB,
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Visual content table
CREATE TABLE visual_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id UUID REFERENCES learning_sessions(id) ON DELETE CASCADE,
    type VARCHAR(20) CHECK (type IN ('diagram', 'infographic', 'illustration', 'chart')),
    url VARCHAR(500) NOT NULL,
    alt_text TEXT,
    caption TEXT,
    prompt TEXT,
    related_concept VARCHAR(255),
    optimized_urls JSONB,
    generated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 8. Security Architecture

### 8.1 Authentication and Authorization
```typescript
// JWT token structure
interface JWTPayload {
  userId: string;
  email: string;
  skillLevel: string;
  iat: number;
  exp: number;
}

// Middleware for route protection
const authenticateToken = (req: Request, res: Response, next: NextFunction) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.sendStatus(401);
  }
  
  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
};
```

### 8.2 File Upload Security
```typescript
// Multer configuration for secure file uploads
const upload = multer({
  storage: multer.memoryStorage(),
  limits: {
    fileSize: 100 * 1024 * 1024, // 100MB limit
  },
  fileFilter: (req, file, cb) => {
    const allowedTypes = ['application/pdf', 'application/epub+zip', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document', 'text/plain'];
    if (allowedTypes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Invalid file type'), false);
    }
  }
});

// Virus scanning integration
const scanFile = async (buffer: Buffer): Promise<boolean> => {
  // Integration with antivirus service
  return true; // Safe file
};
```

## 9. Performance Optimization

### 9.1 Caching Strategy
```typescript
// Redis caching for frequently accessed data
class CacheService {
  private redis: Redis;
  
  async cacheUserProgress(userId: string, progress: UserProgress): Promise<void> {
    await this.redis.setex(`user:${userId}:progress`, 3600, JSON.stringify(progress));
  }
  
  async getCachedLearningPath(pathId: string): Promise<LearningPath | null> {
    const cached = await this.redis.get(`path:${pathId}`);
    return cached ? JSON.parse(cached) : null;
  }
}
```

### 9.2 Database Optimization
```sql
-- Indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_learning_paths_user_id ON learning_paths(user_id);
CREATE INDEX idx_learning_sessions_module_id ON learning_sessions(module_id);
CREATE INDEX idx_visual_content_session_id ON visual_content(session_id);

-- Composite indexes
CREATE INDEX idx_learning_paths_user_status ON learning_paths(user_id, status);
CREATE INDEX idx_sessions_completed ON learning_sessions(completed, completed_at);
```

## 10. Deployment Architecture

### 10.1 Containerization
```dockerfile
# Frontend Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

# Backend Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 5000
CMD ["npm", "start"]

# AI Services Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 10.2 Docker Compose Configuration
```yaml
version: '3.8'
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://backend:5000
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/tutordb
      - JWT_SECRET=your-secret-key
      - AWS_S3_BUCKET=tutor-ai-files
    depends_on:
      - postgres
      - redis

  ai-services:
    build: ./ai-services
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=your-openai-key
      - HUGGINGFACE_API_KEY=your-hf-key
    volumes:
      - ./models:/app/models

  postgres:
    image: postgres:14
    environment:
      - POSTGRES_DB=tutordb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - frontend
      - backend

volumes:
  postgres_data:
```

### 10.3 CI/CD Pipeline
```yaml
# GitHub Actions workflow
name: Deploy Tutor AI
on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: |
          npm test
          python -m pytest

  build-and-deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build and push Docker images
        run: |
          docker build -t tutor-ai-frontend ./frontend
          docker build -t tutor-ai-backend ./backend
          docker build -t tutor-ai-ai-services ./ai-services
      - name: Deploy to production
        run: |
          # Deployment commands
```

This comprehensive design document provides the technical foundation for building the Tutor AI platform with all the specified features including AI-powered learning paths, visual content generation, and adaptive learning capabilities.