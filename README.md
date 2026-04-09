# RentFlow - Real-Time Room Financial Intelligence

> **Automated rent payment reconciliation platform powered by Equity Bank's Biller API**

RentFlow is a comprehensive property management platform that automates rent collection, payment tracking, and financial reconciliation for large-scale residential properties. Built on a proven foundation of real-time financial intelligence, RentFlow integrates directly with Equity Bank's Biller API to streamline payment processing and eliminate manual reconciliation.

---

## 🎯 Vision

Transform rent collection from a manual, error-prone process into an automated, real-time financial intelligence system that property managers can trust.

### Key Value Propositions

- **Real-Time Payment Reconciliation** - Instant validation and matching of payments via Equity Bank Biller API
- **Financial Intelligence Dashboard** - Comprehensive analytics with room-level granularity across 1,929+ units
- **Automated Collections** - No more chasing tenants or manual payment tracking
- **Compliance & Audit Trail** - Complete transaction history with bank-level verification
- **Scalable Architecture** - Designed to handle multi-property portfolios with thousands of units

---

## 🏗️ Architecture

RentFlow uses a modern, hybrid architecture that combines:

### Frontend Layer
- **React 18** with TypeScript for type-safe UI development
- **Tailwind CSS v4** for responsive, utility-first styling
- **React Router** for navigation and routing
- **Recharts** for data visualization and analytics
- **Lucide React** for consistent iconography

### Backend Layer
- **FastAPI Microservice** for Equity Bank Biller API integration
  - Payment validation endpoint
  - Bill number generation and mapping
  - Webhook handlers for real-time notifications
  - IP whitelisting for Equity Bank callbacks
  
- **Supabase** for core data management
  - PostgreSQL database with Row Level Security (RLS)
  - Real-time subscriptions for live updates
  - Authentication and authorization
  - Secure API layer with automatic JWT handling

### Integration Layer
- **Equity Bank Biller API**
  - Automated payment collection
  - Real-time payment notifications
  - Multi-channel support (PDQ, Mobile App, Branch)
  - Bank reference tracking
  - **Idempotency**: Ensures exactly-once processing of payment notifications by using the unique `bankReference` as an idempotency key. A database unique constraint on `bank_reference` prevents duplicate entries, and the `/notification` endpoint checks for existing records before insertion. Duplicate notifications are safely ignored and logged, ensuring that network retries or concurrent requests never create duplicate payment records.

---

## 📊 Data Model

### Core Entities

#### Properties
- Multi-property support with location tracking
- Owner management and access control

#### Blocks (A-H)
- 8 blocks with varying unit counts
- Total: 1,929 rooms across all blocks

| Block | Units | Bill Numbers |
|-------|-------|-------------|
| A | 224 | RM-A1 to RM-A224 |
| B | 231 | RM-B1 to RM-B231 |
| C | 224 | RM-C1 to RM-C224 |
| D | 358 | RM-D1 to RM-D358 |
| E | 350 | RM-E1 to RM-E350 |
| F | 234 | RM-F1 to RM-F234 |
| G | 234 | RM-G1 to RM-G234 |
| H | 74 | RM-H1 to RM-H74 |
| J | 193 | RM-J1 to RM-J193 |


#### Units
- Unique bill number per unit (RM-{BLOCK}{NUMBER})
- Rent amount tracking
- Tenant assignment
- Payment status tracking

#### Payment Statuses
- **PAID** - Fully paid for current period
- **PARTIAL** - Partially paid, balance remaining
- **OVERDUE** - Payment missed, past due date
- **ADVANCE_CREDIT** - Paid in advance, credit applied
- **UNMATCHED** - Payment received but needs reconciliation
- **VACANT** - No tenant assigned

#### Tenants
- Contact information (name, phone, email)
- Lease period tracking
- Unit assignment
- Payment history

#### Payment Transactions
- Bank reference numbers from Equity
- Payment channel tracking (PDQ, Mobile App, Branch)
- Timestamp and amount verification
- Bill number matching

---

## 🔐 Security & Compliance

### Authentication
- Secure JWT-based authentication via Supabase
- Role-based access control (Property Owners, Managers, Auditors)
- Session management with automatic token refresh

### API Security
- IP whitelisting for Equity Bank callbacks
- Request validation and sanitization
- Rate limiting on public endpoints
- Encrypted data transmission (TLS 1.3)

### Data Protection
- Row Level Security (RLS) in Supabase
- Encrypted sensitive data at rest
- Audit logging for all financial transactions
- GDPR-compliant data handling

---

## 🚀 Features

### Dashboard
- **Real-time financial metrics**
  - Total revenue with period-over-period comparison
  - Occupancy rate tracking
  - Overdue alerts with risk analysis
  - Reconciliation status

- **Operational Matrix**
  - Visual heatmap of all 1,929 rooms
  - Color-coded payment statuses
  - Block-level filtering (A-H)
  - Floor-by-floor visualization
  - Click-to-view room details

- **Status Breakdown**
  - Payment distribution by status
  - Revenue calculation per category
  - Percentage analytics

- **Aging Buckets**
  - 0-30 days: Recent activity
  - 30-60 days: Attention needed
  - 60+ days: Critical overdue

### Payment Management
- Real-time payment notifications from Equity Bank
- Automatic bill number matching
- Multi-channel payment tracking
- Payment history and receipts
- Dispute resolution workflow

### Analytics & Reporting
- Monthly revenue trends
- Collection rate analysis
- Payment channel distribution
- Occupancy analytics
- Exportable reports (PDF, CSV, Excel)

### Tenant Management
- Tenant profiles and contact management
- Lease tracking and renewals
- Communication history
- Payment reminders (SMS/Email)

---

## 🛠️ Development Setup

### Prerequisites
- Node.js 18+ and npm/pnpm
- Python 3.11+ (for FastAPI backend)
- Supabase account and project
- Equity Bank Biller API credentials

### Frontend Setup

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

### Backend Setup (FastAPI Microservice)

```bash
# Navigate to backend directory
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set environment variables
cp .env.example .env
# Edit .env with your Equity Bank and Supabase credentials

# Run development server
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Environment Variables

**Frontend (.env)**
```
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
VITE_API_URL=http://localhost:8000
```

**Backend (.env)**
```
# Equity Bank Biller API
EQUITY_API_URL=https://api.equitybankgroup.com/biller/v1
EQUITY_API_KEY=your_equity_api_key
EQUITY_SECRET_KEY=your_equity_secret_key
EQUITY_BILLER_CODE=your_biller_code

# Supabase
SUPABASE_URL=your_supabase_url
SUPABASE_SERVICE_KEY=your_supabase_service_role_key

# Security
ALLOWED_IPS=196.201.214.0/24  # Equity Bank IP range
JWT_SECRET=your_jwt_secret

# Application
ENVIRONMENT=development
LOG_LEVEL=INFO
```

---

## 📡 API Endpoints

### FastAPI Microservice Endpoints

#### Payment Validation
```
POST /api/v1/payments/validate
```
Validates incoming payment notifications from Equity Bank.

**Request Body:**
```json
{
  "billNumber": "RM-A123",
  "amount": 4500,
  "bankReference": "EQ-29348234",
  "paymentChannel": "PDQ",
  "timestamp": "2026-03-18T10:30:00Z",
  "customerName": "John Mwangi",
  "phoneNumber": "+254712345678"
}
```

**Response:**
```json
{
  "status": "success",
  "matchedUnit": "unit-a123",
  "tenant": "John Mwangi",
  "reconciliationStatus": "FULLY_PAID",
  "transactionId": "txn_abc123"
}
```

#### Bill Number Generation
```
POST /api/v1/billing/generate
```
Generates bill numbers for new units or billing periods.

#### Webhook Handler
```
POST /api/v1/webhooks/equity
```
Receives real-time payment notifications from Equity Bank (IP whitelisted).

#### Payment Status Query
```
GET /api/v1/payments/status/{billNumber}
```
Retrieves current payment status for a specific bill number.

---

## 🗄️ Database Schema

### Supabase Tables

#### `properties`
```sql
CREATE TABLE properties (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  location TEXT NOT NULL,
  owner_id UUID REFERENCES auth.users(id),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `blocks`
```sql
CREATE TABLE blocks (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  property_id UUID REFERENCES properties(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  unit_count INTEGER NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `units`
```sql
CREATE TABLE units (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  block_id UUID REFERENCES blocks(id) ON DELETE CASCADE,
  unit_number TEXT NOT NULL,
  bill_number TEXT UNIQUE NOT NULL,
  rent_amount DECIMAL(10, 2) NOT NULL,
  tenant_id UUID REFERENCES tenants(id),
  status TEXT CHECK (status IN ('PAID', 'PARTIAL', 'OVERDUE', 'VACANT', 'ADVANCE_CREDIT', 'UNMATCHED')),
  block_name TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_units_bill_number ON units(bill_number);
CREATE INDEX idx_units_status ON units(status);
CREATE INDEX idx_units_block_id ON units(block_id);
```

#### `tenants`
```sql
CREATE TABLE tenants (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  phone TEXT NOT NULL,
  email TEXT,
  unit_id UUID REFERENCES units(id),
  lease_start DATE NOT NULL,
  lease_end DATE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `payment_transactions`
```sql
CREATE TABLE payment_transactions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  bill_number TEXT NOT NULL,
  tenant_id UUID REFERENCES tenants(id),
  unit_id UUID REFERENCES units(id),
  amount DECIMAL(10, 2) NOT NULL,
  bank_reference TEXT UNIQUE NOT NULL,
  payment_channel TEXT NOT NULL,
  timestamp TIMESTAMPTZ NOT NULL,
  status TEXT DEFAULT 'PENDING',
  reconciled BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_payments_bill_number ON payment_transactions(bill_number);
CREATE INDEX idx_payments_bank_reference ON payment_transactions(bank_reference);
CREATE INDEX idx_payments_timestamp ON payment_transactions(timestamp DESC);
```

#### `audit_logs`
```sql
CREATE TABLE audit_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users(id),
  action TEXT NOT NULL,
  resource_type TEXT NOT NULL,
  resource_id UUID,
  metadata JSONB,
  ip_address INET,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at DESC);
CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
```

---

## 🔄 Payment Flow

### Standard Payment Process

1. **Tenant Makes Payment**
   - Visits Equity Bank branch, PDQ, or Mobile App
   - Enters bill number (e.g., RM-A123)
   - Completes payment

2. **Equity Bank Notification**
   - Equity sends webhook to RentFlow API
   - Includes: bill number, amount, bank reference, channel, timestamp

3. **RentFlow Validation**
   - FastAPI endpoint receives webhook
   - Validates IP address (Equity Bank only)
   - Verifies bill number exists
   - Matches amount against expected rent

4. **Database Update**
   - Creates payment transaction record
   - Updates unit status (PAID, PARTIAL, etc.)
   - Logs audit trail
   - Triggers real-time update to frontend

5. **Notification**
   - Property manager sees live update on dashboard
   - Tenant receives confirmation SMS
   - Receipt generated and stored

### Exception Handling

- **Unmatched Bill Number** → Status: UNMATCHED, manual review required
- **Partial Payment** → Status: PARTIAL, tracks balance remaining
- **Overpayment** → Status: ADVANCE_CREDIT, applied to next period
- **Duplicate Payment** → Flagged for reconciliation, not double-counted

---

## 🧪 Testing

### Frontend Testing
```bash
# Run unit tests
npm run test

# Run E2E tests
npm run test:e2e
```

### Backend Testing
```bash
# Run pytest suite
pytest tests/ -v

# Test with Equity sandbox
pytest tests/integration/test_equity_integration.py
```

### Integration Testing
- Equity Bank sandbox environment
- Mock payment scenarios
- End-to-end payment flow validation
- Error handling and retry logic

---

## 📦 Deployment

### Frontend (Vercel/Netlify)
```bash
npm run build
# Deploy dist/ folder
```

### Backend (Railway/Render/AWS)
```bash
# Build Docker image
docker build -t rentflow-api .

# Run container
docker run -p 8000:8000 --env-file .env rentflow-api
```

### Supabase
- Database migrations via Supabase CLI
- Row Level Security (RLS) policies enabled
- Real-time subscriptions configured

---

## 🗺️ Roadmap

### Phase 1: Foundation ✅
- [x] Frontend dashboard with 1,929 rooms
- [x] Mock data and UI components
- [x] Dark theme and responsive design
- [x] Real-time metrics and analytics

### Phase 2: Equity Integration 🚧
- [ ] FastAPI microservice setup
- [ ] Equity Bank Biller API integration
- [ ] Bill number mapping system
- [ ] Webhook handlers and validation
- [ ] Supabase database schema
- [ ] UAT with pilot landlords

### Phase 3: Enhanced Features 📋
- [ ] SMS notifications (Africa's Talking API)
- [ ] Email receipts and reminders
- [ ] Bulk bill number generation
- [ ] Advanced reporting (PDF exports)
- [ ] Mobile app (React Native)

### Phase 4: AI & Intelligence 🤖
- [ ] Payment prediction models
- [ ] Tenant risk scoring
- [ ] Automated collections strategies
- [ ] Occupancy forecasting
- [ ] Revenue optimization recommendations

### Phase 5: Scale & Enterprise 🏢
- [ ] Multi-property management
- [ ] White-label solution for property managers
- [ ] API marketplace for third-party integrations
- [ ] Advanced analytics and BI dashboards
- [ ] Enterprise SLA and support

---

## 🤝 Contributing

We welcome contributions! Please see our contributing guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🙏 Acknowledgments

- **Equity Bank Kenya** - For the Biller API integration
- **Supabase** - For the real-time database platform
- **FastAPI** - For the high-performance Python backend
- **React & Tailwind** - For the modern frontend stack

---

## 📞 Support

For technical support or business inquiries:

- **Email**: support@rentflow.co.ke
- **Documentation**: https://docs.rentflow.co.ke
- **Status Page**: https://status.rentflow.co.ke

---

Built with ❤️ for property managers across Kenya
