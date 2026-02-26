## 🎯 Project Objective

Creation of a consolidated report to analyze the relationship between user registrations and their activity in email campaigns. The query identifies the **TOP-10 key markets (countries)** generating the highest volume of traffic and user interactions.

---

## ⚙️ Query Logic (CTE Breakdown)

*   **ACC_METR (Account Aggregation):** 
    Gathering data on new accounts. Through a join chain (`ACCOUNT` -> `SESSION`), the registration country and technical attributes (send interval, verification status, and unsubscribe status) are determined.

*   **EM_METR (Email Performance):** 
    **Date Calculation:** Since the send date is stored as an offset, the `DATE_ADD` function is used to calculate the actual event day.
    **Metrics:** Calculation of unique messages across three funnel stages: `SENT`, `OPEN`, and `VISIT` (click-through to site).

*   **DATA_ALL (Consolidation):** 
    Merging two data streams using `UNION ALL`. Zero values are used as placeholders where data is missing to maintain metric integrity.

*   **DATA_TOTAL (Global Aggregation):** 
    Final grouping of all indicators by date, country, and account attributes. This eliminates any duplication that might have occurred during table merging.

*   **DATA_COUNTRY (Window Functions):** 
    Utilizing window functions `SUM(column) OVER (PARTITION BY country)` to calculate total volumes for each country over the entire period without losing daily granularity.

*   **DATA_SEND_INTERVAL & FINAL SELECT (Ranking):** 
    Applying `DENSE_RANK()` to create two rankings. The final result filters the data to retain only those countries that rank in the **TOP-10** either by account volume or by the number of emails sent.

---

## 🛠 Key Technical Features

*   **Fan-out Resilience:** By separating account and email aggregations into different CTEs, the query avoids row multiplication during joins.
*   **Filtering Flexibility:** Rankings are calculated based on total values, while the final result preserves the ability to view daily dynamics.
*   **Time Offset Handling:** Correct processing of `SENT_DATE` allows for viewing the actual date of user interaction with the email.

---


