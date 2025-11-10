### 🧠 Project Title

**E-commerce Analytics Monitor (Swiggy / Zepto / Blinkit)**

---

### 📋 Goal

This project automates **price and availability monitoring** for selected grocery or product links from **Swiggy Instamart**, **Zepto**, and **Blinkit**.
It runs periodically (via cron) and stores structured results for analysis.

---

### 🎯 Objectives

1. Take a list of product URLs + location(s).
2. Automatically open each product page.
3. Select the correct delivery location.
4. Extract key information:

   * Product title
   * Price
   * Availability (In Stock / Out of Stock)
   * Platform
   * Timestamp
5. Store results in `output.csv` (or Supabase in future).
6. Optionally, take a screenshot for each product page for verification.

---

### 🧱 Tech Stack

* **Language:** Node.js (ESM)
* **Automation:** Playwright (headless mode for cron)
* **Data Input:** CSV or JSON (configurable)
* **Output:** CSV
* **Scheduling:** Cron job / PM2 task runner

---

### 📂 Folder Structure

```
project-root/
 ┣━ data/
 ┃   ┗━ products.csv              # Input file
 ┣━ output/
 ┃   ┣━ screenshots/              # Optional screenshots
 ┃   ┗━ results.csv               # Output data
 ┣━ platforms/
 ┃   ┣━ swiggy.js                 # Logic to fetch from Swiggy
 ┃   ┣━ zepto.js                  # Logic to fetch from Zepto
 ┃   ┗━ blinkit.js                # Logic to fetch from Blinkit
 ┣━ utils/
 ┃   ┗━ helpers.js                # Common utilities (CSV read/write, logger)
 ┣━ monitor.js                    # Main controller script
 ┗━ PROJECT_SPEC.md               # This file
```

---

### ⚙️ Execution Flow

1. Read product list from `data/products.csv`
2. For each product entry:

   * Open a new browser context (Playwright)
   * Go to the respective platform’s URL
   * If required, set the **delivery location** (based on pin code or area name)
   * Extract:

     ```js
     {
       product_name,
       price,
       availability,
       location,
       platform,
       timestamp: new Date().toISOString()
     }
     ```
   * Save to `output/results.csv`
3. Repeat for all products
4. Close browser

---

### 🧠 Example Input (`data/products.csv`)

| product_name    | platform | url                                                                                                                  | location        |
| --------------- | -------- | -------------------------------------------------------------------------------------------------------------------- | --------------- |
| Amul Milk       | Zepto    | [https://www.zeptonow.com/product/amul-milk](https://www.zeptonow.com/product/amul-milk)                             | Jalandhar       |
| Tropicana Juice | Swiggy   | [https://www.swiggy.com/instamart/product/tropicana-juice](https://www.swiggy.com/instamart/product/tropicana-juice) | Railway Station |
| Bread           | Blinkit  | [https://blinkit.com/prn/britannia-bread](https://blinkit.com/prn/britannia-bread)                                   | Jalandhar       |

---

### 🧩 Platform Logic Notes

Each platform will have its own `setLocation()` + `getProductInfo()` flow:

#### Zepto

* Step 1: Click location button (`div.a0Ppr button[aria-haspopup="dialog"]`)
* Step 2: Type in location (or pincode)
* Step 3: Select first search result
* Step 4: Confirm location
* Step 5: Extract price + availability

#### Swiggy

* Step 1: Go to Instamart
* Step 2: Open location modal
* Step 3: Search location
* Step 4: Confirm
* Step 5: Parse product price, availability

#### Blinkit

* Step 1: Detect address bar
* Step 2: Type location or pincode
* Step 3: Click confirm
* Step 4: Extract product price and availability

---

### 🧰 Helper Functions

**utils/helpers.js**

```js
export async function readCSV(filePath) { ... }
export async function writeCSV(data, filePath) { ... }
export async function delay(ms) { return new Promise(r => setTimeout(r, ms)); }
export function logStep(step, message) { console.log(`${step} ➤ ${message}`); }
```

---

### 🧾 Output Example

**output/results.csv**

| product_name    | price | availability | platform | location        | timestamp            |
| --------------- | ----- | ------------ | -------- | --------------- | -------------------- |
| Amul Milk       | ₹56   | In Stock     | Zepto    | Jalandhar       | 2025-11-10T11:15:00Z |
| Tropicana Juice | ₹125  | Out of Stock | Swiggy   | Railway Station | 2025-11-10T11:15:03Z |

---

### 🕒 Cron Configuration Example

```bash
# Every 6 hours
0 */6 * * * /usr/bin/node /home/user/project/monitor.js >> /home/user/project/logs/cron.log 2>&1
```

---

### 🧩 Future Enhancements

* Integrate **Supabase** or **MongoDB** for storage
* Add **Telegram alert** if price changes
* Add **email report** summary
* Web dashboard for analytics

---
