# PC Builder Application Setup Guide

## 1. Project Structure

```
pc-builder/
├── backend/                 # Laravel Backend
│   ├── app/
│   │   ├── Console/
│   │   ├── Exceptions/
│   │   ├── Http/
│   │   │   ├── Controllers/
│   │   │   │   ├── API/
│   │   │   │   │   ├── AuthController.php
│   │   │   │   │   ├── BuildController.php
│   │   │   │   │   ├── CategoryController.php
│   │   │   │   │   ├── ComponentController.php
│   │   │   │   │   └── UserController.php
│   │   │   ├── Middleware/
│   │   │   └── Requests/
│   │   │       ├── BuildRequest.php
│   │   │       ├── ComponentRequest.php
│   │   │       └── UserRequest.php
│   │   ├── Models/
│   │   │   ├── Build.php
│   │   │   ├── BuildComponent.php
│   │   │   ├── Category.php
│   │   │   ├── Component.php
│   │   │   └── User.php
│   │   └── Services/
│   │       ├── CompatibilityService.php
│   │       ├── PriceService.php
│   │       └── BuildService.php
│   ├── config/
│   ├── database/
│   │   ├── factories/
│   │   ├── migrations/
│   │   └── seeders/
│   ├── resources/
│   ├── routes/
│   │   ├── api.php
│   │   └── web.php
│   ├── storage/
│   └── tests/
│
├── frontend/                # React Frontend
│   ├── public/
│   │   ├── assets/
│   │   │   ├── images/
│   │   │   └── models/    # 3D models
│   ├── src/
│   │   ├── components/
│   │   │   ├── common/
│   │   │   │   ├── Button.jsx
│   │   │   │   ├── Card.jsx
│   │   │   │   ├── Input.jsx
│   │   │   │   └── Modal.jsx
│   │   │   ├── builder/
│   │   │   │   ├── BuilderCanvas.jsx
│   │   │   │   ├── ComponentList.jsx
│   │   │   │   ├── ComponentSelector.jsx
│   │   │   │   ├── CompatibilityChecker.jsx
│   │   │   │   └── PriceCalculator.jsx
│   │   │   ├── layout/
│   │   │   │   ├── Header.jsx
│   │   │   │   ├── Footer.jsx
│   │   │   │   └── Sidebar.jsx
│   │   │   └── user/
│   │   │       ├── Login.jsx
│   │   │       ├── Register.jsx
│   │   │       └── Profile.jsx
│   │   ├── contexts/
│   │   │   ├── AuthContext.jsx
│   │   │   └── BuildContext.jsx
│   │   ├── hooks/
│   │   │   ├── useAuth.js
│   │   │   ├── useBuild.js
│   │   │   └── useComponents.js
│   │   ├── services/
│   │   │   ├── api.js
│   │   │   ├── auth.js
│   │   │   └── storage.js
│   │   ├── utils/
│   │   │   ├── compatibility.js
│   │   │   ├── price.js
│   │   │   └── validation.js
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── .env
│   └── package.json
│
└── docker/                  # Docker Configuration
    ├── nginx/
    ├── php/
    └── docker-compose.yml
```

## 2. Setup Steps

### Step 1: Initialize Backend (Laravel)

```bash
# Create new Laravel project
composer create-project laravel/laravel backend
cd backend

# Install required packages
composer require laravel/sanctum
composer require spatie/laravel-permission
composer require intervention/image

# Set up environment
cp .env.example .env
php artisan key:generate

# Set up database migrations
php artisan migrate:fresh --seed

# Install Laravel Sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

### Step 2: Initialize Frontend (React)

```bash
# Create Vite React project
npm create vite@latest frontend -- --template react
cd frontend

# Install dependencies
npm install @tanstack/react-query
npm install axios
npm install react-router-dom
npm install @headlessui/react
npm install tailwindcss postcss autoprefixer
npm install three @react-three/fiber @react-three/drei
npm install @heroicons/react
npm install shadcn/ui

# Initialize Tailwind CSS
npx tailwindcss init -p
```

### Step 3: Configure Docker Environment

```bash
# Create docker-compose.yml and Dockerfiles
mkdir docker
cd docker

# Create necessary configuration files
touch docker-compose.yml
mkdir nginx && touch nginx/nginx.conf
mkdir php && touch php/Dockerfile
```

### Step 4: Set Up Database Tables

Create all necessary migrations in `backend/database/migrations/`:

1. Categories
2. Components
3. Builds
4. Build Components
5. Users
6. Reviews
7. Compatibility Rules

### Step 5: Create Backend API Structure

1. Set up controllers in `app/Http/Controllers/API/`
2. Create services in `app/Services/`
3. Define models in `app/Models/`
4. Configure routes in `routes/api.php`

### Step 6: Set Up Frontend Structure

1. Create components in `src/components/`
2. Set up routing in `src/App.jsx`
3. Configure API services in `src/services/`
4. Initialize contexts in `src/contexts/`

## 3. Running the Application

### Development Environment

```bash
# Start Docker containers
docker-compose up -d

# Backend setup
cd backend
php artisan migrate
php artisan db:seed
php artisan serve

# Frontend setup
cd frontend
npm install
npm run dev
```

### Production Environment

```bash
# Build frontend
cd frontend
npm run build

# Configure nginx
# Set up SSL certificates
# Configure environment variables
```

## 4. Key Configuration Files

### Backend `.env`

```env
APP_NAME=PCBuilder
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=pc_builder
DB_USERNAME=root
DB_PASSWORD=

SANCTUM_STATEFUL_DOMAINS=localhost:5173
SESSION_DOMAIN=localhost
```

### Frontend `.env`

```env
VITE_API_URL=http://localhost:8000
VITE_APP_NAME=PC Builder
```

### Docker Compose

```yaml
version: '3.8'
services:
  app:
    build:
      context: ./docker/php
      dockerfile: Dockerfile
    volumes:
      - ./backend:/var/www/html
    networks:
      - pc-builder

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./backend:/var/www/html
      - ./docker/nginx:/etc/nginx/conf.d
    networks:
      - pc-builder

  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: pc_builder
      MYSQL_ROOT_PASSWORD: root
    networks:
      - pc-builder

networks:
  pc-builder:
    driver: bridge
```
