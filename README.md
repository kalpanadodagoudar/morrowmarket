# Morrow Market

A responsive, full-stack storefront with original Morrow Market branding, a React/Vite frontend, and a FastAPI API backed by MongoDB. The API seeds a starter catalog on first startup. Users can search/filter products, register and sign in, keep a bag and wishlist, place demo orders, and chat with a catalog-aware shopping assistant.

## Requirements

- Node.js 18+
- Python 3.10+
- MongoDB 6+ (local instance or hosted MongoDB URI)

## Run the API

In PowerShell:

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```

Set a unique `JWT_SECRET` in `backend/.env`. Set `MONGODB_URI` and `DATABASE_NAME` for your MongoDB instance. `AI_API_KEY` is optional; without it, the assistant searches the local catalog, and with it, the API uses the configured OpenAI-compatible chat completion model. Then start MongoDB and run:

```powershell
uvicorn main:app --reload
```

The API entry point is also available as `app.main:app`.

The API is available at `http://localhost:8000`; interactive API documentation is at `http://localhost:8000/docs`. Catalog seed data is inserted only when the products collection is empty.

## Run the storefront

From the project root:

```powershell
npm install
npm run dev
```

The Vite app defaults to `http://localhost:5173` and calls `http://localhost:8000`. To use a different API origin, create a root `.env` with `VITE_API_URL=https://your-api-host`.

## API overview

- `GET /products`, `GET /products/search`, `GET /products/category/{category}`, `GET /products/{product_id}`
- Authenticated catalog writes: `POST /products`, `PUT /products/{product_id}`, `DELETE /products/{product_id}`
- `POST /auth/register`, `POST /auth/login`, `GET /auth/me`
- Authenticated bag and wishlist routes under `/cart` and `/wishlist`
- Authenticated order history and demo checkout under `/orders`
- `POST /assistant/chat`

The API validates request bodies and query ranges, hashes passwords, signs expiring JWTs, scopes customer data by user, and serializes MongoDB ObjectIds as strings. Checkout records orders and reserves available stock; it does not collect payment details.
