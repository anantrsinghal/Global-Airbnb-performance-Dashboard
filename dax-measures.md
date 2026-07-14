# DAX Measures Used in This Project

This file lists the key DAX measures I built for this dashboard, along with 
a simple explanation of what each one does and why I used it.

---

## 1. Cumulative Listings

```dax
Cumulative Listings = 
VAR CurrentRank =
    MAXX (
        VALUES ( Listings[city] ),
        [City Rank]
    )
RETURN
CALCULATE (
    [Total Listing],
    FILTER (
        ALL ( Listings[city] ),
        [City Rank] <= CurrentRank
    )
)
```

**What it does:** Calculates a running total of listings, ordered by city rank. 
For each city on the chart, it adds up listings for that city plus every city 
ranked above it — this is what creates the "staircase" pattern in a Pareto chart.

**Key functions:** `VAR`/`RETURN`, `MAXX`, `CALCULATE`, `FILTER`, `ALL`

---

## 2. Cumulative %

```dax
Cumulative % =
DIVIDE (
    [Cumulative Listings],
    CALCULATE ( [Total Listing], ALL ( Listings[city] ) )
)
```

**What it does:** Takes the running total from above and divides it by the 
grand total across all cities, giving a cumulative percentage (e.g. "top 5 
cities = 77% of all listings"). This is the classic 80/20 / Pareto pattern.

**Key functions:** `DIVIDE`, `CALCULATE`, `ALL`

---

## 3. % of Monthly Reviews

```dax
% of Monthly Reviews =
DIVIDE (
    [Total Reviews],
    CALCULATE (
        [Total Reviews],
        ALLSELECTED ( Listings[city] )
    )
)
```

**What it does:** Calculates a city's share of total reviews, but the total 
respects whatever the user has filtered/selected (via slicers), instead of 
always comparing against every city in the entire dataset. This keeps the 
percentages meaningful and "add up to 100%" within whatever selection the 
user is currently viewing.

**Key functions:** `DIVIDE`, `CALCULATE`, `ALLSELECTED`

---

## Summary of DAX Concepts Practiced

| Concept | Used For |
|---|---|
| `CALCULATE` | Changing filter context to recompute values |
| `FILTER` | Building custom row subsets based on conditions |
| `ALL` | Removing filters (column or table level) |
| `ALLEXCEPT` | Removing all filters except one specific column |
| `ALLSELECTED` | Respecting user-applied slicers instead of the whole dataset |
| `MAXX` | Finding max values, including row-by-row iteration |
| `VAR` / `RETURN` | Storing temporary values for cleaner, reusable logic |
| `DIVIDE` | Safe division without divide-by-zero errors |
| `ALLSELECTED` | Making percentages dynamic based on user filters/slicers |

This project was a good way to move beyond basic SUM/AVERAGE measures and 
actually understand how filter context works in DAX — especially the 
difference between `ALL`, `ALLEXCEPT`, and `ALLSELECTED`, which took me a 
while to fully wrap my head around.
