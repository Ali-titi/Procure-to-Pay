# Procure-to-Pay Full-Stack Application

A complete Procure-to-Pay system with Django REST Framework backend and React frontend, featuring multi-level approval workflows, document processing, and role-based access control.

## 🚀 Quick Start

### Prerequisites
- Python 3.11+
- Node.js 18+
- npm or yarn

### Installation & Setup

1. **One-command complete setup (recommended):**
   ```bash
   npm run setup
   ```

   This automatically:
   - Installs all dependencies
   - Runs database migrations
   - Creates test users
   - Populates database with demo data

2. **Or step-by-step setup:**
   ```bash
   # Install dependencies
   npm run install:all

   # Set up database
   npm run migrate

   # Create test users
   npm run createusers

   # Add demo data (optional)
   npm run createdemo
   ```

3. **Run the application:**
   ```bash
   npm run dev
   ```

This will start both the Django backend (http://localhost:8000) and React frontend (http://localhost:5173) simultaneously.

## 📁 Project Structure

```
procure-to-pay/
├── backend/                 # Django REST API
│   ├── accounts/           # User management & authentication
│   ├── requests/           # Purchase requests & approvals
│   ├── finance/            # Finance operations & receipts
│   ├── documents/          # Document processing services
│   ├── backend/            # Django settings & configuration
│   ├── manage.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── docker-compose.yml
├── frontend/               # React application
│   ├── src/
│   ├── package.json
│   └── vite.config.ts
├── package.json            # Root scripts for running both services
└── README.md
```

## 🔐 User Roles & Permissions

| Role | Permissions |
|------|-------------|
| **Staff** | Create purchase requests, view own requests, upload attachments |
| **Approver Level 1** | View pending L1 requests, approve/reject with comments |
| **Approver Level 2** | View pending L2 requests, approve/reject with comments |
| **Finance** | Validate goods received, upload receipt documents, mark completion |
| **Admin** | Full CRUD access, manage users, view analytics |

## 🔄 Workflow Process

1. **Staff** creates purchase request → Status: `pending_l1`
2. **Approver 1** approves → Status: `pending_l2` or rejects → Status: `rejected_l1`
3. **Approver 2** approves → Status: `approved` or rejects → Status: `rejected_l2`
4. **Finance** validates delivery → Status: `completed`

## 📊 Data Models

### User Model
- `name`: Full name
- `email`: Unique email address
- `role`: User role (staff, approver1, approver2, finance, admin)
- `created_at`: Account creation timestamp

### PurchaseRequest Model
- `title`: Request title
- `description`: Detailed description
- `amount`: Total amount
- `quantity`: Item quantity
- `department`: Requesting department
- `status`: Workflow status
- `created_by`: Request creator (FK User)
- `created_at/updated_at`: Timestamps

### Approval Model
- `purchase_request`: Related request (FK)
- `approver`: Approving user (FK User)
- `level`: Approval level (1 or 2)
- `status`: approved/rejected
- `comment`: Approval comment
- `date`: Approval timestamp

### Attachment Model
- `purchase_request`: Related request (FK)
- `file`: Uploaded file
- `uploaded_at`: Upload timestamp

### ReceiptValidation Model
- `purchase_request`: Related request (FK)
- `finance_user`: Validating user (FK User)
- `status`: received/partially_received/not_received
- `comment`: Validation comment
- `date`: Validation timestamp

## 🔑 Login Credentials

After running `npm run setup`, you can login with these test accounts:

**Note:** Approvers now correctly redirect to their dashboard after login! 🎉

| Role | Username/Email | Password | Permissions |
|------|----------------|----------|-------------|
| **Staff** | `staff@example.com` | `staff123` | Create purchase requests |
| **Approver 1** | `approver1@example.com` | `approver123` | Approve level 1 requests |
| **Approver 2** | `approver2@example.com` | `approver123` | Approve level 2 requests |
| **Finance** | `finance@example.com` | `finance123` | Validate receipts |
| **Admin** | `admin@example.com` | `admin123` | Full access (Django admin) |

## 📊 Demo Data

After running `npm run setup`, your database will contain:

- **13+ Sample Purchase Requests** with different statuses:
  - Office Equipment Purchase (Approved) - $2,900
  - Software Licenses (Approved) - $899.97
  - Conference Room Setup (Pending) - $2,420
  - IT Infrastructure (Approved) - $164.99
  - Office Supplies (Rejected) - $395
  - Consulting Services (Pending) - $2,500
  - And more demo requests...

- **Purchase Items** for each request with pricing
- **Multi-level Approval Workflows** with proper timestamps
- **Complete Audit Trail** for testing all user roles
- **Real-time Data Updates** - No more page refreshes needed!
- **Sequential Indexing** - Clean numbered lists instead of database IDs

## 🛠️ Available Scripts

- `npm run dev` - Run both frontend and backend
- `npm run dev:backend` - Run only Django backend
- `npm run dev:frontend` - Run only React frontend
- `npm run install:all` - Install all dependencies
- `npm run migrate` - Run Django migrations
- `npm run createusers` - Create test users
- `npm run createdemo` - Create demo data
- `npm run setup` - Complete setup (install + migrate + users + demo data)

## 🌐 API Endpoints

### Authentication
- `POST /api/auth/login/` - User login
- `POST /api/auth/register/` - User registration (admin only)
- `POST /api/auth/logout/` - User logout
- `POST /api/auth/refresh/` - Refresh JWT token
- `GET /api/auth/profile/` - Get user profile

### Purchase Requests
- `GET /api/purchase-requests/` - List user's requests (filtered by role)
- `POST /api/purchase-requests/` - Create new request (staff only)
- `GET /api/purchase-requests/{id}/` - Get request details
- `PUT /api/purchase-requests/{id}/` - Update pending request (creator only)
- `DELETE /api/purchase-requests/{id}/` - Delete request (staff only)

### Approvals
- `GET /api/approvals/pending/` - List pending approvals for current user
- `POST /api/purchase-requests/{id}/approve/` - Approve request (approvers only)
- `POST /api/purchase-requests/{id}/reject/` - Reject request (approvers only)

### Attachments
- `POST /api/purchase-requests/{id}/upload_attachment/` - Upload attachment
- `GET /api/purchase-requests/{id}/attachments/` - List request attachments

### Finance Validation
- `GET /api/finance/` - List approved requests for finance
- `POST /api/finance/{id}/validate_receipt/` - Validate receipt (finance only)
- `GET /api/finance/purchase_orders/` - List purchase orders with files

## 🐳 Docker Deployment

### Complete Application with Docker Compose

For production deployment with Docker Compose:

```bash
# Build and run all services
docker-compose up --build

# Or run in background
docker-compose up -d --build
```

This starts:
- **Frontend**: http://localhost (port 80)
- **Backend**: http://localhost:8000
- **PostgreSQL**: Database service

### Individual Services

```bash
# Backend only
cd backend
docker build -t procure-backend .
docker run -p 8000:8000 procure-backend

# Frontend only
cd frontend
docker build -t procure-frontend .
docker run -p 80:80 procure-frontend
```

### Environment Variables

Create a `.env` file in the root directory:

```env
SECRET_KEY=your-secret-key-here
DEBUG=False
ALLOWED_HOSTS=localhost,yourdomain.com
DATABASE_URL=postgresql://user:password@db:5432/procuretopay
```

## 📄 Features

### ✅ Core Features
- **JWT Authentication** with role-based permissions
- **Multi-level approval workflow** (Staff → Approver 1 → Approver 2 → Finance)
- **Document management** with file uploads (PDF, images)
- **OCR text extraction** from uploaded documents
- **Automatic Purchase Order generation**
- **Receipt validation** against purchase orders
- **Real-time status updates** and notifications

### ✅ New Features (Latest Update)
- **Enhanced User Model** with name, email uniqueness, and admin role
- **Complete Workflow Logic** with 8 status states
- **Attachment System** for request documentation
- **Finance Validation** with receipt status tracking
- **Role-based API Filtering** - users see only relevant data
- **Comprehensive Audit Trail** with approval history
- **Docker Containerization** for easy deployment
- **PostgreSQL Support** for production databases

### ✅ Technical Features
- **RESTful API Design** with Django REST Framework
- **Responsive React Frontend** with TypeScript
- **File Upload Handling** with secure storage
- **Database Migrations** with proper relationships
- **CORS Configuration** for cross-origin requests
- **Environment-based Configuration**
- **Comprehensive Error Handling**
- **Input Validation** and sanitization

## 🔧 Development

### Backend Setup
```bash
cd backend
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

### Frontend Setup
```bash
cd frontend
npm install
npm run dev
```

## 📝 License

MIT License - see LICENSE file for details.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

**Happy coding! 🎉**