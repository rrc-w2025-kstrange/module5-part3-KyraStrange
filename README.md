# Pixel River Financial Bank Services

This project is a small banking web app. It is split into separate services (microservices) that run in Docker containers. Everything is reached through one front door: NGINX on port 80.

Student: Kyra Strange (0425418)

## What the web app does

- A student portfolio page opens first. It has a "Banking App" link.
- The link takes you to the bank. In the bank you can:
  - **Register** a new user
  - **Log in**
  - See your **balance** on the dashboard
  - **Deposit** money (numbers only, like 500.50)
  - **Withdraw** money (numbers only)
  - **View Transactions**: a monthly report of your deposits and withdrawals
  - **Log out**
- User details and balances are saved in a MongoDB database. The data is kept even after a restart.

## The services

| Service | What it is | What it does |
| --- | --- | --- |
| `studentportfolio` | HTML + Bootstrap | Shows the portfolio page |
| `backend` | Python + Flask | Register, login, dashboard, deposit, withdraw, logout |
| `transactions` | JavaScript (Node.js + Express) | Builds the monthly transactions report |
| `mongo` | MongoDB | Saves the data |
| `nginx` | NGINX | The only entrance. It sends each request to the right service |

## How the traffic flows

1. You open `http://localhost` (port 80, no port number needed).
2. NGINX reads the address and sends the request:
   - Normal pages go to the portfolio files.
   - `/api/transactions/...` goes to the transactions service.
   - Other banking pages (`/api/login`, `/api/register`, `/api/dashboard`, and so on) go to the backend.
3. The backend and the transactions service read and write data in MongoDB.

Only NGINX has a port open on the computer (`80:80`). The backend, transactions service and database cannot be reached directly from outside. This gives one place to control and log all traffic.

## How to run it

1. Install Docker Desktop and wait until it says it is running.
2. Open PowerShell in this project folder (the one with `docker-compose.yml`).
3. Start everything:

   ```
   docker compose up --build -d
   ```

4. Open `http://localhost` in your browser and click "Banking App".
5. To see the NGINX log of all requests:

   ```
   docker compose logs -f nginx
   ```

   Press Ctrl+C to stop watching.
6. When you are finished, stop and clean up:

   ```
   docker compose down -v
   ```

## How to run the tests

The backend tests are in `backend/tests`. From the project folder:

```
cd backend
pip install -r requirements.txt
pip install pytest
pytest -q tests/
```

## Automatic testing (CI)

The file `.github/workflows/ci.yml` tells GitHub Actions to run the backend tests every time code is pushed (or a pull request is opened) on the `main` branch.

- A green check means all tests passed.
- A red X means something broke.
- GitHub saves who pushed, when, and the result. This is the audit trail.

## Project folders

```
backend/            Python + Flask banking service (and its tests)
transactions/       Node.js transactions service
studentportfolio/   Front-end portfolio page
nginx.conf          NGINX routing rules
docker-compose.yml  Starts all the services together
.github/workflows/  The CI pipeline (ci.yml)
```