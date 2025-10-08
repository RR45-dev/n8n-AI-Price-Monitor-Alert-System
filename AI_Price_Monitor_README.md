# 🤖 AI Price Monitor & Alert System (n8n + PostgreSQL)

A no-code/low-code **AI-powered price tracking and alert system** built with [n8n](https://n8n.io).  
Easily monitor product prices across e-commerce platforms — ideal for **buyers** looking for deals and **sellers** tracking competitors.

---

## 🖼️ Workflow Overview

Below is the main n8n workflow implementing automatic price tracking and alerts:

![n8n Workflow Screenshot](fe016e0e-c228-4dbd-9c87-448fc2932143.png)

> 💡 *This workflow automates product ingestion, scraping, price storage, and real-time alerting via Slack or Email.*

---

## ⚙️ Features

| Buyers | Sellers |
|--------|----------|
| Track any product via URL | Track competitors’ prices |
| Get alerts when price drops | Detect competitor delta price changes |
| Store historical prices | Suggest repricing with AI (optional) |
| Alerts via Slack / Email | Easy integration with APIs |
| 100% visual automation (n8n) | Extendable with AI or LangChain |

---

## 🧠 Architecture

```
Webhook → Insert Product → Insert Source
     ↓
Cron Trigger → Fetch Source URLs → Scrape → Parse Price → Store Price → Check Alerts → Notify (Slack/Email)
```

---

## 🧩 Workflow Node Descriptions

### 1️⃣ Webhook: Add Watch
Receives HTTP POST requests to add a new product to monitor.

### 2️⃣ PG: Insert Product
Adds or updates the product in the database.

### 3️⃣ PG: Insert Source
Links the product with its retailer and tracking URL.

### 4️⃣ Cron: Poll Sources
Scheduled trigger (default: every 15 minutes).

### 5️⃣ PG: List Sources
Queries all active sources that need price updates.

### 6️⃣ HTTP: Get Page
Downloads the HTML of each product page.

### 7️⃣ FN: Parse Price
Extracts the product price using regex and simple parsing rules.

### 8️⃣ PG: Insert Price
Saves the scraped price and timestamp to the database.

### 9️⃣ PG: Fetch Alerts
Retrieves alert rules linked to the product.

### 🔟 FN: Eval Alerts
Compares the new price against alert thresholds.

### 11️⃣ IF: Alert Hit?
Checks if an alert should fire.

### 12️⃣ Slack Notify / Email Notify
Sends formatted alerts to Slack or Email.

### 13️⃣ Done
Marks the workflow completion.

---

## 🗄️ Database Schema

Database: PostgreSQL  
SQL file: `price_monitor_schema.sql`

Tables include:
- `products`
- `product_sources`
- `prices`
- `alerts`
- `events`

---

## 🧰 Installation

### 1. Clone the repository
```bash
git clone https://github.com/your-username/ai-price-monitor.git
cd ai-price-monitor
```

### 2. Import Workflow into n8n
- Go to your n8n dashboard
- Click **Workflows → Import from File**
- Choose `n8n_price_monitor.json`
- Click **Import**

### 3. Setup Database
```bash
psql -U postgres -d price_monitor -f price_monitor_schema.sql
```

### 4. Add Credentials
- PostgreSQL connection
- Slack API key (optional)
- SMTP email config

---

## 🧪 Testing the Workflow

```bash
curl -X POST http://localhost:5678/webhook/add-watch   -H "Content-Type: application/json"   -d '{
    "org_id": "00000000-0000-0000-0000-000000000000",
    "title": "Sony WH-1000XM5",
    "brand": "Sony",
    "sku": "WH1000XM5",
    "canonical_url": "https://store.example.com/p/sony-wh1000xm5",
    "source_url": "https://store.example.com/p/sony-wh1000xm5",
    "retailer": "Example",
    "currency": "USD"
  }'
```

---

## 🧠 Optional AI Enhancements

Add a node after `FN: Eval Alerts`:

**OpenAI Node Prompt Example:**
> Check if this price drop is significant (more than 15% compared to last 30 days). Only alert if meaningful.

---

## 🧩 Extending the System

| Feature | How to Add |
|----------|-------------|
| Competitor price tracking | Create a 2nd cron workflow for delta comparison |
| Repricing engine | Add Python/AI node to compute new recommended prices |
| Dashboard | Connect Postgres to Grafana or Metabase |
| Multi-channel alerts | Duplicate Slack/Email nodes for SMS, Discord, etc. |

---

## 💡 Tips

- Use rotating proxies for anti-bot protection on retailers.
- Use n8n environment variables for credentials (`{{ $env.DB_PASS }}`).
- Run in Docker for 24/7 uptime.

---

## 📜 License
MIT © 2025 — Your Name

---

## 🌟 Star this repo if you like it!
