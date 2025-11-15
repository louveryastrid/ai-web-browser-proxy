# 🚀 COMPLETE SOURCE CODE - AI Web Browser

This file contains ALL source code for the AI Web Browser project. Simply copy each file's content and create the corresponding file in your project.

## 📂 Project Structure

```
ai-web-browser-proxy/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Browser.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   ├── ChatPanel.jsx
│   │   │   ├── ToolsPanel.jsx
│   │   │   ├── HistoryPanel.jsx
│   │   │   └── URLBar.jsx
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   ├── Dockerfile
│   ├── nginx.conf
│   └── .dockerignore
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js
│   │   ├── services/
│   │   │   ├── ai.service.js
│   │   │   ├── scraper.service.js
│   │   │   └── screenshot.service.js
│   │   ├── routes/
│   │   │   ├── browser.routes.js
│   │   │   ├── ai.routes.js
│   │   │   └── webhook.routes.js
│   │   ├── middleware/
│   │   │   └── logger.js
│   │   └── server.js
│   ├── package.json
│   ├── Dockerfile
│   └── .dockerignore
├── docker-compose.yml ✅ (already created)
├── .env.example ✅ (already created)
├── n8n-workflow.json
├── setup.sh
└── README.md ✅ (already created)
```

---

## 🎨 FRONTEND FILES

### `frontend/package.json`

```json
{
  "name": "ai-web-browser-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "axios": "^1.7.2",
    "lucide-react": "^0.400.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.1",
    "vite": "^5.3.3",
    "tailwindcss": "^3.4.4",
    "postcss": "^8.4.39",
    "autoprefixer": "^10.4.19"
  }
}
```

### `frontend/vite.config.js`

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    host: '0.0.0.0',
    port: 3000,
    proxy: {
      '/api': {
        target: process.env.VITE_API_URL || 'http://localhost:5000',
        changeOrigin: true
      }
    }
  },
  build: {
    outDir: 'dist',
    sourcemap: false
  }
})
```

### `frontend/tailwind.config.js`

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: '#3b82f6',
        secondary: '#8b5cf6',
        dark: '#1e293b',
        darker: '#0f172a'
      }
    },
  },
  plugins: [],
}
```

### `frontend/postcss.config.js`

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### `frontend/index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>AI Web Browser - ProProxy Clone</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

### `frontend/src/main.jsx`

```javascript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

### `frontend/src/index.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply bg-darker text-gray-100;
  }
}

@layer components {
  .btn-primary {
    @apply bg-primary hover:bg-blue-600 text-white px-4 py-2 rounded-lg transition-colors;
  }
  
  .btn-secondary {
    @apply bg-gray-700 hover:bg-gray-600 text-white px-4 py-2 rounded-lg transition-colors;
  }
  
  .input-field {
    @apply bg-dark border border-gray-700 rounded-lg px-4 py-2 text-gray-100 focus:outline-none focus:border-primary transition-colors;
  }
  
  .card {
    @apply bg-dark border border-gray-800 rounded-lg p-4;
  }
}
```

### `frontend/src/App.jsx`

```javascript
import { useState } from 'react'
import URLBar from './components/URLBar'
import Browser from './components/Browser'
import Sidebar from './components/Sidebar'

function App() {
  const [currentUrl, setCurrentUrl] = useState('')
  const [iframeUrl, setIframeUrl] = useState('')
  const [sidebarOpen, setSidebarOpen] = useState(true)
  const [activeTab, setActiveTab] = useState('chat')

  const handleNavigate = (url) => {
    setIframeUrl(url)
    setCurrentUrl(url)
  }

  return (
    <div className="h-screen w-screen flex flex-col bg-darker overflow-hidden">
      {/* Header */}
      <div className="bg-dark border-b border-gray-800 px-4 py-3">
        <div className="flex items-center space-x-4">
          <h1 className="text-xl font-bold text-primary">AI Web Browser</h1>
          <div className="flex-1">
            <URLBar onNavigate={handleNavigate} currentUrl={currentUrl} />
          </div>
        </div>
      </div>

      {/* Main Content */}
      <div className="flex-1 flex overflow-hidden">
        {/* Browser Window */}
        <div className={`flex-1 transition-all $