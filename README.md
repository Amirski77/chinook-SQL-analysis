# chinook-SQL-analysis
SQL-based analysis of the Chinook digital media store using Google BigQuery. Focus on revenue optimization, customer segmentation, and inventory insights.

# 🎵 Chinook Music Store Data Analysis

## 📌 Project Overview
This project involves a comprehensive analysis of the **Chinook Database**, representing a digital media store. The goal was to act as a Data Analyst helping the Chinook team understand their market performance, customer behavior, and inventory composition.

The analysis was performed using **SQL** in **Google BigQuery**, utilizing advanced techniques to extract actionable business insights.

## 🛠️ Tech Stack & Techniques
* **Database:** Chinook (Relational Model: Customers, Invoices, Artists, Albums, Tracks).
* **Platform:** Google BigQuery.
* **SQL Skills Demonstrated:**
    * **Aggregations:** `SUM`, `COUNT`, `AVG` to calculate revenue and statistics.
    * **Joins:** Complex `JOIN` (Inner/Left) across 4+ tables.
    * **Window Functions:** `RANK()` and `PARTITION BY` for category-specific rankings.
    * **Date Functions:** `EXTRACT(YEAR from ...)` for time-series analysis.

## 📊 Key Business Insights

### 1. 📈 Sales & Employee Performance
* **Top Salesperson:** **Jane Peacock** is the leading employee, generating **$833.04** in revenue across 146 separate sales. She outperforms the runner-up, Margaret Park ($775.40), by approximately 7.4%.
* **Sales Dynamics:** We analyzed the yearly revenue trends to determine if the business is growing or contracting. *(See visualization in `TotalRevenue by YEAR.png`)*.
* **Team Structure:** The sales team is highly competitive, with all top 3 agents generating over $700 each in revenue.

### 2. 🌍 Market & Geography Analysis
* **Top Countries by Activity:** The **USA** is the largest market by volume (91 invoices), followed by **Canada** (56) and **Brazil** (35).
* **Top Revenue City:** **Prague** is the highest-grossing city ($90.24 revenue), identifying it as a prime location for promotional events.
* **Global Revenue Leaders:** While the USA leads in volume, **France** and **Brazil** remain critical secondary markets based on total revenue generation.

### 3. 👥 Customer Analysis
* **Top Global Customer:** The customer with the highest lifetime value is **Helena Holý**, with a total spend of **$49.62**.
* **Super-Fan Analysis:** By analyzing artist-specific spending, we identified **Mark Taylor** as the top supporter of *Iron Maiden*, having spent **$17.82** on their tracks alone.

### 4. 🎸 Inventory & Product Analysis
* **Dominant Genres (Top 5 by Revenue):**
    1.  **Rock:** $826.65 (Market Leader)
    2.  **Latin:** $382.14
    3.  **Metal:** $261.36
    4.  **Alternative & Punk:** $241.56
    5.  **TV Shows:** $93.53
* **Inventory Insight:** Rock and Latin music account for the vast majority of store revenue, suggesting that marketing efforts should focus heavily on these two genres.

## 💻 SQL Code Example
*Below is an example of an advanced query used to find the most popular genre for each country using Window Functions:*

```sql
/* Goal: Return each country along with its top-selling Genre.
Technique: Using RANK() over partitions to handle ties and ordering.
*/

SELECT
  BillingCountry,
  GenreName,
  Purchases
FROM (
  SELECT
    i.BillingCountry,
    g.Name AS GenreName,
    COUNT(il.InvoiceLineId) AS Purchases,
    RANK() OVER (PARTITION BY i.BillingCountry ORDER BY COUNT(il.InvoiceLineId) DESC) AS Genre_Rank
  FROM `da-nfactorial.chinook.invoice` AS i
  JOIN `da-nfactorial.chinook.invoiceline` AS il ON i.InvoiceId = il.InvoiceId
  JOIN `da-nfactorial.chinook.track` AS t ON il.TrackId = t.TrackId
  JOIN `da-nfactorial.chinook.genre` AS g ON t.GenreId = g.GenreId
  GROUP BY i.BillingCountry, g.Name
)
WHERE Genre_Rank = 1
ORDER BY Purchases DESC;
