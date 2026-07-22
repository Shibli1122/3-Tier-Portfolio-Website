## STEP 1 — Clone the Repository

Clone the project from GitHub:

```bash
git clone https://github.com/Shibli1122/Portfolio-Website.git
cd Portfolio-Website
```

---

## STEP 2 — Update Your System

Update Ubuntu packages:

```bash
sudo apt update
sudo apt upgrade -y
```

---

## STEP 3 — Install Node.js

Install Node.js 20 LTS:

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

---

## STEP 4 — Install Python

Install Python, pip and virtual environment:

```bash
sudo apt install -y python3 python3-pip python3-venv

# Verify installation
python3 --version
pip3 --version
```

---

## STEP 5 — Install PostgreSQL

Install PostgreSQL server:

```bash
sudo apt install -y postgresql postgresql-contrib

sudo systemctl enable postgresql
sudo systemctl start postgresql

sudo systemctl status postgresql
```

---

## STEP 6 — Create Database

Open PostgreSQL:

```bash
sudo -u postgres psql
```

Run:

```sql
CREATE USER portfolio_user WITH PASSWORD 'portfolio_pass';

CREATE DATABASE portfolio_db;

GRANT ALL PRIVILEGES ON DATABASE portfolio_db TO portfolio_user;

\c portfolio_db

GRANT ALL ON SCHEMA public TO portfolio_user;
GRANT CREATE ON SCHEMA public TO portfolio_user;
ALTER SCHEMA public OWNER TO portfolio_user;
ALTER DATABASE portfolio_db OWNER TO portfolio_user;

\q
```

---

## STEP 7 — Create Python Virtual Environment

```bash
python3 -m venv venv

source venv/bin/activate
```

---

## STEP 8 — Install Python Dependencies

```bash
pip install psycopg2-binary
```

---

## STEP 9 — Install Backend Dependencies

```bash
cd backend

npm install

cd ..
```

---

## STEP 10 — Initialize Database

```bash
source venv/bin/activate

cd database

python db_service.py

cd ..
```

---

## STEP 11 — Start Backend

```bash
cd backend

node server.js
```

Open another terminal:

```bash
curl http://localhost:5000/api/health
```

---

## STEP 12 — Start Frontend

```bash
cd frontend

npx http-server -p 3000
```

---

## STEP 13 — Configure AWS Security Group

Open the following inbound ports:

```
3000
5000
```

---

## STEP 14 — Open the Website

```
http://<EC2-PUBLIC-IP>:3000
```
