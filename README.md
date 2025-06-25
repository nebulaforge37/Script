# ------------------------------------
# 1. System Prerequisites
# ------------------------------------
sudo apt update && sudo apt upgrade -y

# Install Node.js 20 (ARM-compatible)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Install PostgreSQL
sudo apt install -y postgresql postgresql-contrib

# Install Git
sudo apt install -y git

# Install global npm tools
npm install -g pnpm expo-cli

# ------------------------------------
# 2. Setup Frontend
# ------------------------------------
# Create frontend folder
mkdir frontend && cd frontend

# Initialize with Vite + React + TypeScript
pnpm create vite . --template react-ts

# Install Tailwind, shadcn/ui, TanStack Query, Wouter
pnpm install -D tailwindcss postcss autoprefixer
pnpm install @tanstack/react-query wouter class-variance-authority lucide-react tailwind-variants
npx tailwindcss init -p

# Optional: Install shadcn/ui generator
pnpm dlx shadcn-ui@latest init

# Tailwind config example (edit tailwind.config.ts accordingly):
# content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],

cd ..

# ------------------------------------
# 3. Setup Backend
# ------------------------------------
mkdir backend && cd backend
pnpm init -y

# Install backend packages
pnpm install express pg drizzle-orm dotenv jsonwebtoken passport passport-jwt ws cors

# Optional: For TypeScript
pnpm install -D typescript ts-node @types/node @types/express @types/ws @types/jsonwebtoken

# Create basic backend structure
mkdir src && touch src/index.ts
npx tsc --init

cd ..

# ------------------------------------
# 4. Setup Mobile App
# ------------------------------------
mkdir mobile && cd mobile
expo init --template expo-template-blank-typescript

# Install navigation + state
pnpm install react-native-wagmi react-query react-native-websocket react-native-paper

cd ..

# ------------------------------------
# 5. PostgreSQL Setup
# ------------------------------------
# Start and configure PostgreSQL
sudo systemctl enable postgresql
sudo systemctl start postgresql

# Setup default DB and user (replace with your own)
sudo -u postgres psql <<EOF
CREATE USER devuser WITH PASSWORD 'devpass';
CREATE DATABASE devdb OWNER devuser;
EOF

# ------------------------------------
# 6. Run Everything
# ------------------------------------
# Frontend
cd frontend && pnpm dev &

# Backend
cd ../backend && pnpm ts-node src/index.ts &

# Mobile
cd ../mobile && expo start
