# Bitespeed Identity Reconciliation Service

A robust backend service that identifies and links customer contacts across multiple purchases, helping businesses maintain a unified customer identity even when customers use different email addresses and phone numbers.

## 🎯 Problem Statement

FluxKart.com needs to track customer identity across multiple purchases where customers might use different contact information. This service links different orders made with different contact information to the same person using intelligent contact reconciliation.

## 🚀 Features

- **Contact Linking**: Automatically links contacts with common email or phone numbers
- **Primary/Secondary Hierarchy**: Maintains contact relationships with the oldest contact as primary
- **Smart Consolidation**: Merges separate contact chains when new information connects them
- **RESTful API**: Clean `/identify` endpoint for easy integration
- **Database Optimization**: Efficient queries with proper indexing
- **Type Safety**: Full TypeScript implementation
- **Error Handling**: Comprehensive error handling and validation

## 🛠 Tech Stack

- **Backend**: Node.js + Express.js + TypeScript
- **Database**: PostgreSQL (via Supabase)
- **ORM**: Supabase Client
- **Deployment**: Ready for platforms like Render, Railway, or Heroku

## 📋 Prerequisites

Before running this application, you need:

1. **Supabase Account**: [Create a free Supabase account](https://supabase.com)
2. **Node.js**: Version 18 or higher

## ⚙️ Setup Instructions

### 1. Clone and Install Dependencies

```bash
git clone <your-repo-url>
cd bitespeed-identity-reconciliation
npm install
```

### 2. Database Setup

1. Create a new Supabase project
2. Run the migration file in your Supabase SQL editor:
   - Copy the contents of `supabase/migrations/create_contacts_table.sql`
   - Paste and execute in Supabase SQL Editor

### 3. Environment Configuration

1. Copy `.env.example` to `.env`
2. Add your Supabase credentials:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
PORT=3000
NODE_ENV=development
```

### 4. Run the Application

```bash
# Development mode
npm run dev

# Production build
npm run build
npm start
```

The service will be available at `http://localhost:3000`

## 📡 API Documentation

### POST `/identify`

Identifies and consolidates customer contact information.

**Request Body:**
```json
{
  "email": "customer@example.com",    // optional
  "phoneNumber": "1234567890"         // optional
}
```

**Response:**
```json
{
  "contact": {
    "primaryContactId": 1,
    "emails": ["customer@example.com", "customer2@example.com"],
    "phoneNumbers": ["1234567890", "0987654321"],
    "secondaryContactIds": [2, 3]
  }
}
```

### GET `/health`

Health check endpoint to verify service status.

**Response:**
```json
{
  "status": "OK",
  "message": "Bitespeed Identity Reconciliation Service is running",
  "timestamp": "2023-12-07T10:30:00.000Z"
}
```

## 🔄 Business Logic Flow

1. **New Customer**: If no matching email or phone number exists, creates a new primary contact
2. **Exact Match**: If exact combination exists, returns existing contact chain
3. **Partial Match**: If email OR phone matches existing contact, creates secondary contact
4. **Chain Linking**: If request links two separate contact chains, merges them (oldest primary wins)

## 🏗 Database Schema

```sql
CREATE TABLE contacts (
  id SERIAL PRIMARY KEY,
  phone_number TEXT,
  email TEXT,
  linked_id INTEGER REFERENCES contacts(id),
  link_precedence TEXT CHECK (link_precedence IN ('primary', 'secondary')),
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  deleted_at TIMESTAMPTZ
);
```

## 🧪 Testing Examples

### Example 1: New Customer
```bash
curl -X POST http://localhost:3000/identify \
  -H "Content-Type: application/json" \
  -d '{"email":"lorraine@hillvalley.edu","phoneNumber":"123456"}'
```

### Example 2: Adding Secondary Contact
```bash
curl -X POST http://localhost:3000/identify \
  -H "Content-Type: application/json" \
  -d '{"email":"mcfly@hillvalley.edu","phoneNumber":"123456"}'
```

### Example 3: Linking Separate Chains
```bash
curl -X POST http://localhost:3000/identify \
  -H "Content-Type: application/json" \
  -d '{"email":"george@hillvalley.edu","phoneNumber":"717171"}'
```

## 🚀 Deployment

### Deploy to Render

1. Connect your GitHub repository to Render
2. Set environment variables in Render dashboard
3. Deploy with build command: `npm run build`
4. Start command: `npm start`

### Deploy to Railway

1. Connect repository to Railway
2. Add environment variables
3. Railway will auto-deploy on git push

## 🔧 Development

### Project Structure
```
src/
├── config/         # Database configuration
├── controllers/    # Request handlers
├── middleware/     # Custom middleware
├── routes/         # Route definitions
├── services/       # Business logic
├── types/          # TypeScript type definitions
└── index.ts        # Application entry point
```

### Available Scripts
- `npm run dev`: Start development server with hot reload
- `npm run build`: Build for production
- `npm start`: Start production server
- `npm test`: Run tests

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

**Live API Endpoint**: [Will be updated after deployment]

Built with ❤️ for Bitespeed Backend Task