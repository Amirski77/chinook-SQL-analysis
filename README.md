# chinook-SQL-analysis
SQL-based analysis of the Chinook digital media store using Google BigQuery. Focus on revenue optimization, customer segmentation, and inventory insights.

# 🎵 Chinook Music Store Data Analysis

## 📌 Project Overview
This project involves a comprehensive analysis of the **Chinook Database**, which represents a digital media store (similar to iTunes). The goal was to act as a Data Analyst helping the Chinook team understand their market performance, customer behavior, and inventory composition.

The analysis was performed using **SQL** in **Google BigQuery**, utilizing advanced techniques to extract actionable business insights.

## 🛠️ Tech Stack & Techniques
* **Database:** Chinook (Relational Model: Customers, Invoices, Artists, Albums, Tracks).
* **Platform:** Google BigQuery.
* **SQL Skills Demonstrated:**
    * **Aggregations:** `SUM`, `COUNT`, `AVG` to calculate revenue and statistics.
    * **Joins:** Complex `JOIN` (Inner/Left) across 4+ tables to link sales to specific genres and artists.
    * **Window Functions:** `RANK()` and `PARTITION BY` for category-specific rankings.
    * **Subqueries & CTEs:** For multi-step logical filtering.

## 📊 Key Business Insights

### 1. 🌍 Market & Geography Analysis
* **Top Countries by Activity:** The **USA** is the largest market by volume (91 invoices), followed by **Canada** (56) and **Brazil** (35).
* **Top Revenue City:** **Prague** is the highest-grossing city ($90.24 revenue), making it the ideal location for the proposed promotional music festival.
* **Global Revenue Leaders:** While the USA leads in volume, **France** and **Brazil** remain critical secondary markets based on total revenue generation.

### 2. 👥 Customer Analysis
* **Top Global Customer:** The customer with the highest lifetime value is **Helena Holý**, with a total spend of **$49.62**.
* **Super-Fan Analysis:** By analyzing artist-specific spending, we identified **Mark Taylor** as the top supporter of the store's leading band (*Iron Maiden*), having spent **$17.82** on their tracks alone.
* **Customer Segmentation:** We successfully generated targeted lists of Rock music listeners for email marketing campaigns.

### 3.  Inventory & Product Analysis
* **Dominant Genre:** **Rock** is the primary revenue driver ($826.65), significantly outperforming the second-place genre, Latin ($382.14).
* **Artist Inventory:** **Iron Maiden** has the largest catalog presence with **204 tracks**, followed by Led Zeppelin (114) and Metallica (112).
* **Content Outliers:** The analysis identified tracks significantly longer than the average, such as *"Occupation / Precipice"* (approx. 88 minutes), which helps in curating specialized "Long-Form" playlists.

##  SQL Code Example
*Below is an example of an advanced query used in this project to find the most popular genre for each country using Window Functions:*

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
