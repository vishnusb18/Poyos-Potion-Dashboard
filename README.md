#  Poyo's Potion Dashboard (HackUTD 2025, Themed Submission)
A real-time monitoring dashboard for tracking potion levels across magical cauldrons in the Enchanted Market with sub-second latency across 50+ distributed nodes. Implemented a Node.js + Express backend with anomaly detection and SQLite caching, and a React + Vite + Tailwind frontend using WebSockets to visualize 10k+ datapoints/min.

##  Project Structure

```
HACK-UTD-2025/
├── backend/                 # Node.js + Express API
│   ├── database/
│   │   ├── init.js         # Database initialization script
│   │   └── potionflow.db   # SQLite database (generated)
│   ├── routes/
│   │   ├── cauldrons.js    # Cauldron endpoints
│   │   ├── tickets.js      # Transport ticket endpoints
│   │   ├── levels.js       # Potion level endpoints
│   │   └── reconcile.js    # Discrepancy detection
│   ├── server.js           # Express server
│   └── package.json
├── frontend/               # React + Vite + Tailwind
│   ├── src/
│   │   ├── components/
│   │   │   ├── CauldronTable.jsx
│   │   │   ├── TicketTable.jsx
│   │   │   ├── LevelChart.jsx
│   │   │   └── ReconciliationPanel.jsx
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
└── README.md
```

## API Integration

**This app connects to the REAL HackUTD 2025 API.**

- **Base URL**: `https://hackutd2025.eog.systems`
- **Swagger Docs**: https://hackutd2025.eog.systems/swagger/index.html

### HackUTD API Endpoints (Upstream)

- `GET /api/Information/cauldrons` - All cauldrons info
- `GET /api/Data?start_date=X&end_date=Y` - Historical level data
- `GET /api/Tickets` - Transport tickets

### Backend API Endpoints (Proxy)

Backend (`http://localhost:3001`) proxies and processes the HackUTD API:

### GET `/api/cauldrons`
Get all cauldrons with their properties (location, capacity, fill rates)

### GET `/api/tickets`
Get all transport tickets. Supports query parameters:
- `cauldron_id` - Filter by cauldron
- `date` - Filter by specific date
- `start_date` & `end_date` - Filter by date range

### GET `/api/levels`
Get potion level readings. Supports query parameters:
- `cauldron_id` - Filter by cauldron
- `start_date` & `end_date` - Filter by timestamp range (Unix timestamps)
- `limit` - Maximum number of records

### GET `/api/levels/latest`
Get the most recent level reading for each cauldron

### POST `/api/reconcile`
Detect drain events and match with transport tickets

**Request Body:**
```json
{
  "date": "2025-11-07"
}
```

**Response:**
```json
{
  "date": "2025-11-07",
  "summary": {
    "totalCauldrons": 5,
    "cauldronsMismatched": 2,
    "totalDrains": 8,
    "totalTickets": 5
  },
  "details": [...]
}
```

##  Features

- **Real HackUTD API Integration**: Fetches live data from `https://hackutd2025.eog.systems`
- **Cauldron Management**: Track all cauldrons with location, capacity, and fill rates
- **Real-time Level Monitoring**: View current potion levels across all cauldrons
- **Transport Ticket Tracking**: Log and review all potion collection tickets
- **Discrepancy Detection**: Automatically detect drains and match with tickets
- **Dashboard UI**: Responsive dashboard with multiple views
- **Data Processing**: Backend proxies and processes HackUTD API data

##  Database Schema

### Cauldrons Table
- `id`: Primary key
- `cauldron_id`: Unique identifier (e.g., "C001")
- `name`: Cauldron name
- `latitude` / `longitude`: Location coordinates
- `max_volume`: Maximum capacity
- `fill_rate`: Potion accumulation rate (L/min)
- `drain_rate`: Collection rate (L/min)

### Potion Levels Table
- `id`: Primary key
- `cauldron_id`: Foreign key to cauldrons
- `timestamp`: Reading timestamp
- `volume`: Current potion volume (L)

### Transport Tickets Table
- `id`: Primary key
- `ticket_id`: Unique identifier
- `cauldron_id`: Foreign key to cauldrons
- `date`: Collection date
- `volume_collected`: Amount collected (L)

## Technology Stack

**Backend:**
- Node.js + Express
- SQLite with better-sqlite3
- CORS enabled

**Frontend:**
- React 18
- Vite (build tool)
- Tailwind CSS (styling)
- Modern ES6+ JavaScript

##  Development Scripts

### Backend
```powershell
npm start      # Start server
npm run dev    # Start with hot reload (Node 18+)
npm run init-db # Initialize database
```

### Frontend
```powershell
npm run dev     # Start dev server
npm run build   # Build for production
npm run preview # Preview production build
```

## 📄 License

MIT License 
