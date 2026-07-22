# Portfolio Website — Run Guide

Commands to set up and run the project on an Ubuntu server (e.g. EC2), with a one-liner explaining each command.

## Step 1 — Arrange files into proper folders

```bash
mkdir -p backend database frontend
```
Creates the three folders the code expects (backend, database, frontend).

```bash
mv backend_server.js backend/server.js
```
Moves the backend script into `backend/` and renames it to `server.js`.

```bash
mv backend_package.json backend/package.json
```
Moves the backend's dependency file into `backend/` as `package.json`.

```bash
mv database_service.py database/db_service.py
```
Moves the DB script into `database/` and renames it to `db_service.py` (backend imports it by this name).

```bash
mv portfolio_frontend.html frontend/index.html
```
Moves the frontend HTML into `frontend/` as `index.html`.

## Step 2 — Update system & install Node.js

```bash
sudo apt update && sudo apt upgrade -y
```
Refreshes package lists and upgrades installed packages.

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```
Adds the NodeSource repository for Node.js 20.

```bash
sudo apt install -y nodejs
```
Installs Node.js (and npm) from that repository.

```bash
node -v && npm -v
```
Confirms Node.js and npm installed correctly.

## Step 3 — Install Python

```bash
sudo apt install -y python3 python3-pip python3-venv
```
Installs Python 3, pip, and the venv module for virtual environments.

## Step 4 — Install & start PostgreSQL

```bash
sudo apt install -y postgresql postgresql-contrib
```
Installs the PostgreSQL database server.

```bash
sudo systemctl enable postgresql
```
Sets PostgreSQL to start automatically on boot.

```bash
sudo systemctl start postgresql
```
Starts the PostgreSQL service now.

## Step 5 — Create database & user

```bash
sudo -u postgres psql
```
Opens a PostgreSQL prompt as the default `postgres` superuser.

```sql
CREATE USER portfolio_user WITH PASSWORD 'portfolio_pass';
```
Creates the database user the app connects with.

```sql
CREATE DATABASE portfolio_db;
```
Creates the database used by the app.

```sql
GRANT ALL PRIVILEGES ON DATABASE portfolio_db TO portfolio_user;
```
Gives that user full rights on the database.

```sql
\c portfolio_db
```
Switches the psql session into the new database.

```sql
GRANT ALL ON SCHEMA public TO portfolio_user;
```
Grants the user full rights on the `public` schema.

```sql
GRANT CREATE ON SCHEMA public TO portfolio_user;
```
Allows the user to create new tables in that schema.

```sql
ALTER SCHEMA public OWNER TO portfolio_user;
```
Makes the user the owner of the `public` schema.

```sql
ALTER DATABASE portfolio_db OWNER TO portfolio_user;
```
Makes the user the owner of the database itself.

```sql
\q
```
Exits the psql prompt.

## Step 6 — Python virtual environment

```bash
python3 -m venv venv
```
Creates an isolated Python environment in a `venv/` folder.

```bash
source venv/bin/activate
```
Activates that virtual environment for the current shell.

```bash
pip install psycopg2-binary
```
Installs the PostgreSQL driver Python needs to connect to the DB.

## Step 7 — Initialize the database

```bash
cd database
```
Moves into the database folder.

```bash
python db_service.py
```
Runs the script once to create tables and seed sample data.

```bash
cd ..
```
Returns to the project root.

## Step 8 — Install backend dependencies

```bash
cd backend
```
Moves into the backend folder.

```bash
npm install
```
Installs the Node.js packages (Express, cors, etc.) listed in `package.json`.

## Step 9 — Free up ports before starting (if reused)

```bash
sudo lsof -i :5000
```
Shows which process (if any) is currently using port 5000 (backend).

```bash
sudo lsof -i :3000
```
Shows which process (if any) is currently using port 3000 (frontend).

```bash
sudo kill -9 <PID>
```
Force-kills a specific process by its PID shown above.

```bash
pkill -f "node server.js"
```
Kills any running backend `node server.js` process by name.

```bash
pkill -f "http-server"
```
Kills any running `http-server` (frontend) process by name.

## Step 10 — Start backend (in background)

```bash
cd ~/Portfolio-Website/backend
```
Moves into the backend folder from anywhere.

```bash
nohup node server.js > backend.log 2>&1 &
```
Starts the backend in the background, logging output to `backend.log`, so it keeps running after the terminal closes.

```bash
curl http://localhost:5000/api/health
```
Checks that the backend is responding.

## Step 11 — Start frontend (in background)

```bash
cd ~/Portfolio-Website/frontend
```
Moves into the frontend folder.

```bash
nohup npx http-server -p 3000 -a 0.0.0.0 > frontend.log 2>&1 &
```
Serves the frontend on port 3000, bound to all network interfaces (not just localhost) so it's reachable externally, running in the background.

```bash
curl http://localhost:3000
```
Checks that the frontend is responding locally.

## Step 12 — Open in browser
