### Would a Grid Battery Have Paid for Itself in Great Britain (2019-2025)?

### 

A learning project based a simple question: if a grid-scale battery has charged when electricity was cheap and discharged when it was expensive, how much could it have earned, and what would it have been worth paying for it? It uses real half-hourly GB wholesale prices, a daily dispatch optimisation, and a break-even cost calculation, and it tests how sensitive the answer is to the assumptions. 



Status: the data download and the dispatch model are done. The cost and financial assumptions are still placeholders, so the break-even figures below are illustrative until each one has a source to back the corresponding data.



###### The question

How much revenue could a 2-hour battery earn from price arbitrage each year, and what is the most it could cost (per kWh) and still break even?



##### Data

* Prices: half-hourly Market Index Data (MID) from Elexon's public Insights API, downloaded with fetch\_prices.py. I kept the APXMIDP provider and rows with a positive volume, because the other provider (N2EXMIDP) returned zero price and zero volume in the sample I inspected.
* Period and size: 2019 to 2025, 120,513 half-hours.
* Coverage: 2022 to 2025 are close to complete (about 17,500 half-hours a year). 2019 to 2021 have gaps (17,166, 16,501 and 16,775 half-hours). I have not yet checked whether the gaps are missing data or rows removed by my zero-volume filter.
* Prices seen: mean about 91, median about 75, minimum about -103, maximum about 1,984. The unit is assumed to be pounds per MWh and still needs confirming against Elexon's documentation.





##### Method

1. Dispatch. 
Each day, a linear programme chooses when to charge and discharge to maximise revenue, subject to the power limit, the energy limit, round-trip efficiency and a limit of one full cycle per day. The battery starts each day empty, so it must charge before it discharges. Days without all 48 half-hours are excluded.
2. Two versions. 
Perfect foresight schedules using that day's actual prices, which is an upper bound. Naive forecast schedules using the average price of each half-hour over the previous 7 days, but is paid at actual prices. The naive version is the more realistic one.
3. Annual revenue. 
Average daily revenue for each year, multiplied by 365 so years with gaps can be compared.
4. Break-even capital cost. 
The present value of the annual net cash flows, which is the most you could pay up front and still have NPV of zero. This avoids guessing a capital cost: you compare it with a sourced cost.
5. Sensitivity.

&#x20;   Duration (1, 2, 4 hours), round-trip efficiency (80, 85, 90%) and discount rate (5, 7, 10%).



##### Assumptions

All values below are placeholders. None has a source yet, so none should be quoted as a finding. 



|Assumption|Value used|Source and year|
|-|-|-|
|Battery power|1 MW|placeholder|
|Duration|2 hours (1 and 4 in the sensitivity)|placeholder|
|Round-trip efficiency|85% (80 and 90 in the sensitivity)|placeholder, source needed|
|Cycles per day|1|placeholder|
|Annual capacity degradation|2%|placeholder, source needed|
|Lifetime|15 years|placeholder, source needed|
|Discount rate|7% (5 and 10 in the sensitivity)|placeholder (candidate source: the government's cost of capital update, 2020, dated)|
|Operating cost|0, so break-even is before running costs|not yet included|





##### Results

2-hour battery, 85% round-trip efficiency, one cycle per day, placeholder assumptions above.



|Year|Days used|Revenue, perfect foresight (£/MW/year)|Revenue, naive forecast (£/MW/year)|Naive as share of perfect|Break-even capex, perfect (£/kWh) |Break-even capex, naive (£/kWh)|
|-|-|-|-|-|-|-|
|2019|194|15,697|10,341|0.66|63.86|42.07|
|2020|226|19,814|12,762|0.64|80.61|51.92|
|2021|252|68,105|45,857|0.67|277.08|186.56|
|2022|307|80,129|48,626|0.61|326.00|197.83|
|2023|284|44,522|28,370|0.64|181.13|115.42|
|2024|350|32,454|21,837|0.67|132.04|88.84|
|2025|339|35,245|25,531|0.72|143.39|103.87|



###### What the results show

* Revenue depended heavily on the year. 

The 2021 and 2022 energy-crisis years earned several times more than 2019 or 2020. In 2024 and 2025, the revenue was well below the 2022 peak.

* Forecasting costs revenue. 

The simple 7-day forecast captured 61% to 72% of the perfect-foresight revenue, so a realistic operator would earn well below the upper bound.

* Break-even cost moved with revenue. 

With the naive forecast, it ranged from about £42/kWh (2019) to about £198/kWh (2022), and was about £104/kWh in 2025, before running costs and under the placeholder assumptions.

* Sensitivity. 

Break-even cost rose with efficiency and fell with the discount rate. For a 2-hour battery with the naive forecast and a 7% rate, it was about £97, £116 and £133 per kWh at 80%, 85% and 90% efficiency. The sensitivity averages all years, so the 2021 and 2022 spike lifts it. Longer batteries earned more per MW but had a lower break-even cost per kWh, because one cycle a day limits how much extra energy capacity can earn. The full table is available in sensitivity.csv.



##### Limitations

* Placeholder assumptions. 

The cost, efficiency, degradation, lifetime and discount rate are unsourced stand-ins, so the break-even figures are illustrative.

* Running costs excluded. 

Break-even cost would be lower once operating costs are included.

* Revenue streams left out. 

Balancing services, capacity market payments, grid charges and tax are not modelled.

* Price impact ignored. 

The model assumes the battery does not move the prices it trades against.

* A simple forecast. 

The 7-day average is a basic benchmark, not a good price forecast.

* A learning project. 

It uses public assumptions and is not investment advice.



##### Running it

pip install requests pandas numpy scipy matplotlib

python prices.py

python battery\_model.py



##### Extensions

* Source every assumption and add operating costs.
* Let the forecast use whichever of the previous 7 days are available, to recover days lost to gaps.
* Compare against a better forecast, such as one built from your UK demand forecast.
* Add imbalance prices or other revenue streams.

