# MediConnect Backend (NestJS + MongoDB)

This is a NestJS/Mongoose implementation of the MediConnect backend, using the modular NestJS structure and conventions from the supplied Mana Health backend.
## Requirements
- Node.js 20+ (Node 22 recommended
- npm
- MongoDB 7/8, local or hosted

## Setup
```bash
npm install
cp .env.example .env
# Set a long random JWT_SECRET and your MongoDB connection string
npm run start:dev
```
API base URL: `http://localhost:3000/api/v1`  
Swagger: `http://localhost:3000/api-docs`  
Health: `http://localhost:3000/api/v1/health`

## Run locally (without Docker)

1. Install Node.js 20+ (Node 22 recommended) and MongoDB Community Server, or use a hosted MongoDB connection string.
2. Run `npm install`.
3. Copy `.env.example` to `.env` and set `MONGO_URI` and a strong `JWT_SECRET`.
4. Start MongoDB locally if using a local database.
5. Run `npm run start:dev`.

The API runs at `http://localhost:3000/api/v1`; Swagger is at `http://localhost:3000/api-docs`.

## Main modules
- `auth`: patient/doctor registration, login, JWT
- `users`: shared account model
- `patients`: patient profile read/update
- `doctors`: public directory and doctor profile read/update
- `specializations`: specialization directory and admin management
- `doctor-services`: doctor-owned consultation services and pricing
- `availability`: weekly doctor availability
- `blocked-dates`: doctor unavailable dates
- `appointments`: booking, listing, cancellation and overlap checks
- `payments`: Stripe Checkout and webhook confirmation
- `notifications`: user notifications
- `health`: API/database health status

## API routes
All routes are prefixed with `/api/v1`.

| Method | Route | Access |
|---|---|---|
| POST | `/auth/register` | Public |
| POST | `/auth/login` | Public |
| GET | `/health` | Public |
| GET | `/doctors` | Public |
| GET | `/doctors/:id` | Public |
| GET | `/specializations` | Public |
| GET/PATCH | `/patients/me` | Patient JWT |
| GET/PATCH | `/doctors/me` | Doctor JWT |
| GET/POST/PATCH/DELETE | `/doctor-services` | Public list / Doctor manage |
| GET/POST/DELETE | `/availability` | Public list / Doctor manage |
| GET/POST/DELETE | `/blocked-dates` | Public list / Doctor manage |
| POST/GET | `/appointments`, `/appointments/mine` | JWT |
| PATCH | `/appointments/:id/cancel` | Appointment owner |
| POST | `/payments/checkout` | JWT |
| POST | `/payments/stripe/webhook` | Stripe signature |
| GET | `/notifications` | JWT |
| PATCH | `/notifications/:id/read` | JWT |

## Registration payloads
Patient:
```json
{"email":"patient@example.com","password":"StrongPass123","firstName":"Ada","lastName":"Okafor","phone":"+2348000000000"}
```
Doctor:
```json
{"email":"doctor@example.com","password":"StrongPass123","firstName":"John","lastName":"Doe","role":"DOCTOR","qualification":"MBBS","bio":"Family physician"}
```
Doctor profiles are initially unpublished. The doctor can update their profile; an admin workflow should review and publish profiles before public listing.

## Important implementation notes
- Currency defaults to `NGN`; consultation types are `VIDEO`, `PHONE`, and `IN_PERSON`.
- Appointment `startsAt` is expected as an ISO-8601 UTC timestamp. Availability uses JavaScript day-of-week values (Sunday `0` through Saturday `6`) and `HH:mm` strings.
- Stripe webhook verification uses the raw request body. Configure `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, and `APP_BASE_URL`.
- Configure production CORS origins, HTTPS, secrets, database access controls and backups before deployment.
- Admin registration is intentionally disabled. Create admin users through a secure seed/administrative process before enabling admin-only actions.
- This project is a backend foundation matching the supplied MediConnect domain model; adapt any frontend-specific field names and payment requirements during integration.
