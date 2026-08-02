# Microsoft's Steady Margin Hides a 21 Point Gap Between Its Segments
Microsoft reports one operating margin. In FY2024 it was 44.6%, making it look stable. Look closer, the fastest growing segment is not the most profitable one, which makes the next place to allocate money become unclear. That is why this ends in three different recommendations rather than one.

**View Dashboard:** 
[**Placeholder**](https::)

**Stack:** Power BI (DAX, Power Query) · Python · pandas · matplotlib · Microsoft 10-K filings

---

## Overview
This is a decision focused BI dashboard built on six years of Microsoft 10-K data, FY2019 to FY2024. The analysis is displayed through Python while the delivery runs in Power BI. The output is not a set of charts but an allocation call: one segment to fund, one to protect, one to question.

| Segment                             | FY2024 Revenue | FY2024 Margin | Growth 3-yr Avg |
|-------------------------------------|----------------|---------------|-----------------|
| Intelligent Cloud                   | 105,362m       | 47.1%         | 20.6%           |
| Productivity and Business Processes | 77,728m        | 52.2%         | 13.0%           |
| More Personal Computing             | 62,032m        | 31.1%         | 5.2%            |

Three segments, three different answers, since the leader on each measure is a different business.

---

## The Recommendation 
**Invest: Intelligence Cloud:** The only segment above average on both measures. Incremental investment should be allocated here.

**Defend: Productivity** The highest margin in the company and continuing to rise yearly. This is a strong profit base which should be protected and optimized rather than blindly push growth.

**Scrutinize: More Personal Computing** Last on both measures and having shown signs of decline. This segment needs a further look before spending continues.

---

## The Evidence

These are the four pieces of evidence in the data that directly produced these calls.

**1. The Segments are 21 Points Apart on Profitability**
Productivity converted 52.2% of revenue into operating income in FY2024 while Personal Computing managed 31.1%. This 21% gap is the reason why a single company level margin is not the correct number to allocate against.  

**2. The Gap is Widening**
In FY2019 the gap was 11.4 points. Cloud and Productivity improved their margins in almost every year of the series. Compared to Personal Computing which only rose 3.1 points across six years and fell in two of them. Both stronger segments are noticeably pulling ahead.

**3. Growth and Profitability Sit in Different Segments**
Cloud great at a 20.6% average across FY2022 to 2024, half as fast as Productivity but earns 5.1 less on each dollar. On its own both segments are not the obvious answer, which is why the recommendation splits.

**4. One Segments is Last on Both Measures**
Personal Computing has been comparatively underperforming, with the lowest margins and slowest growth, including an 8.7% revenue decline in FY2023. It sits alone in the lower left quadrant, signaling not how much to spend but whether to spend further at all. 

![Segment quadrant: growth against margin](images/dashboard_quadrant.png)

*Chart above: Average revenue growth FY2022 to FY2024 against FY2024 operating margin with reference lines at the group average.*

*Note: Productivity sits almost exactly on the average growth line at 13% against an average of 12.9%. The quadrant cannot separate it on growth so margin is what distinguishes it.* 

---

## The Data

Microsoft 10-K filings on SEC EDGAR: FY2019 to FY2021 from the FY2021 10-K, FY2022 to FY2024 from the FY2024 10-K. Figures hand-transcribed into a single Excel file, which records the source filing for each row, then loaded and reshaped in the notebook.

*Note: Margin throughout refers to operating margin. Segment operating income is divided by segment revenue. These are the only segment level profitability measures Microsoft publishes as the rest are company wide.*

---

## Methodology 
**Two Tools, One Job**
The notebook is designed to showcase the analysis, loading and reshaping the figures, computing margin and growth, running the reconciliation and produces the quadrants that establishes the findings. It exports a clean CSV ready to be turned into a stakeholder ready visual via Power BI. Legibility in the notebook means it follows through in the dashboard.

**Why Growth is Averaged and Margin is Not**
The quadrant uses a three year average on its growth axis and a single year on its margin axis. Margin tends to move slowly, reasonably allowing a one year representation to be a fair call. Whilst growth is more volatile, averaging a three year span represents a segment more honestly. Personal Computing fell 8.7% in FY2023 and rebounded 13.3% in FY2024, if an average would've not been taken into account it would've mislead the findings and shown it performing better than Productivity on growth. 

**Why the Reference Lines Sit at the Group Average** 
Lines at zero would put all three segments in the same quadrant since every one is growing and profitable. The question is not whether a segment is healthy but where the next dollar should go.

**Reconciliation** 
Segment revenue and segment operating income sums total company values exactly in all six years. Both checks are visible in the notebook output. There is a limitation on this reconciliation and it is mentioned below.

**Two Dax Measures**
```dax
Operating Margin = DIVIDE(SUM('microsoft_segments_analysed'[operating_income_musd]),
                          SUM('microsoft_segments_analysed'[revenue_musd]))
```
Total income over total revenue not averaged, doing so would over weight the smallest segment. The KPI card is filtered to FY2024, if it was unfiltered it would return to 40.94% (the six year average).

```dax
Total Revenue Growth =
VAR CurrentYear = MAX('microsoft_company'[fiscal_year])
VAR CurrentRev = CALCULATE(SUM('microsoft_company'[total_revenue_musd]), 'microsoft_company'[fiscal_year] = CurrentYear)
VAR PriorRev = CALCULATE(SUM('microsoft_company'[total_revenue_musd]), 'microsoft_company'[fiscal_year] = CurrentYear - 1)
RETURN DIVIDE(CurrentRev - PriorRev, PriorRev)
```
Written with variables and `CALCULATE` rather than time intelligence because `fiscal_year` is a whole number rather than a true Date column. Functions like `SAMEPERIODLASTYEAR` have nothing to work with. Returns 15.7% for FY2024.

---

## The Scope Decision

The series stops at FY2024 as in August 2024 Microsoft announced changes to the composition of its reported segments. They moved commercial components of Microsoft 365 into Productivity and Business Processes starting FY2025. The reallocation is large enough to change which segment is the largest and this depends on which filing is read. Continuing the series past 2025 would have meant either mixing two incompatible definitions into one trend or restarting the series to a smaller degree. A six year series under one definition is worth more than a distorted two year series.

**Further Recasting in the Series**
Microsoft similarly recast in its 2023 10-K for FY2021, moving 352 Million USD of revenue from Intelligence Cloud into More Personal Computing. This series reports 2021 as originally filed, regardless the effects were measured rather than assumed:

|                                          | As built | On recast FY2021 |
|------------------------------------------|----------|------------------|
| Intelligent Cloud, 3-yr avg growth       | 20.63%   | 20.88%           |
| More Personal Computing, 3-yr avg growth | 5.15%    | 4.91%            |

Around 0.6% of one segment's revenue in one year. Ultimately the impact is marginal to the ranking and quadrant positions, therefore the recommendations remain unchanged. Two recasts, two different answers measured rather than assumed.

**What the Reconciliation Can Not Catch** 
The company totals do not change when revenue is reallocated between segments. The change was identified by comparing Microsoft’s filings across years, not through arithmetic, because only the segment allocations changed while the company wide totals remained the same.

---

## Limitations 

**No Segment Level Return**
Microsoft reports R&D and Capital Expenditures at company level only. This analysis ranks segments on growth and margin not measuring return on investments.

**Segment Margins are Not Standalone Economics**
Segment operating income is stated after allocating corporate costs. A 47.1% segment margin is what that segment earns after carrying its assigned share of central overhead. The three are comparable to each other because the same logic applies to all of them, based on Microsoft's chosen allocation. 

**Six Years is a Short Series**
This series spans an unusual period through pandemic era demand shifts and a following rise in industry wide capital investment. Trends that derive from this series are directional not precise. 

**The FY2021 Recast**
Following the FY2025 recast, Productivity becomes larger than Intelligence Cloud. This is a change rather than a discrepancy.

---

## The Next Steps 
**A Refresh on the Recast Basis**
A continuation of this project falls on what happens to the data once FY2025 AND FY2026 are included. Assumptions of how ranking fall have been made but running the numbers and having concrete results via an additional multi year series is out of scope and deserved separate analysis. 

**A Company Level View Immune to Recast**
Revenue, operating income, R&D and Capital Expenditures are unaffected by segment reallocations. This allows a look into a company wide question, is revenue growing faster than spending?

--- 

## Files
| File                               | What It Is                                                  |
|------------------------------------|-------------------------------------------------------------|
| `microsoft_segment_analysis.ipynb` | The analysis: load, reshape, compute, reconcile, plot       |
| `microsoft_10-K_source_data.xlsx`  | Hand-transcribed 10-K figures, with source filing per row   |
| `microsoft_segments_analysed.csv`  | 18 rows, six years by three segments, the dashboard's input |
| `microsoft_company.csv`            | Six rows, company totals per year                           |
| `microsoft_segment_dashboard.pbix` | Power BI source file                                        |
| `microsoft_segment_dashboard.pdf`  | Both dashboard pages, no Power BI needed                    |
| `charts/`                          | Chart exports used above                                    |

--- 

## Attributions 

All figures from Microsoft Corporation 10-K filings, filed with the U.S. Securities and Exchange Commission and publicly available on EDGAR: FY2019 to FY2024 for the data (FY2023 and FY2025 for the recast checks). Educational portfolio project. Not investment advice.









