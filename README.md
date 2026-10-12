# Purchase Tracking and Attribution Audit

FormWise is a made-up online legal-document subscription service, and the data is simulated generate_data.py

The channel report showed mobile purchases falling, but finance said revenue was flat. This audit works out which
number is right, what's wrong with the tracking, and how much channel credit depends on the attribution model.

## What I did
- Reconciled front-end purchase events against billing records, week by week, for web and the mobile app.
- Checked for duplicate events, missing IDs, and orders with no event at all.
- Compared four attribution models: last touch, first touch, linear, and position-based.
- Wrote a tracking plan and a set of SQL checks that can run daily before dashboards refresh.

## Findings
- Mobile app version 3.2.0 sent **44%** of purchase events without a user ID for 11 weeks. Any report that joined
  purchases to users or channels undercounted mobile, which explains the "drop".
- Web totals looked fine, but only because two problems cancelled out: duplicate events from client retries
  (~200 orders) and orders whose event never arrived (~3%).
- Last touch gives social **7%** of revenue credit. Position-based gives it **14%**. Social tends to start
  journeys that end in a search or direct visit.
- The daily checks would have flagged the app bug the day after release.

![Events vs billing] reconciliation.png

## Recommendations
1. Use billing data as the source of truth for purchases in all reporting.
2. Send the purchase event server-side from the billing webhook.
3. Run the data quality checks daily and as part of mobile release QA.
4. Show two attribution models side by side, and test social with a regional holdout before moving budget.

## Run it
```
pip install -r requirements.txt
python generate_data.py
jupyter notebook analysis.ipynb
```

Tools: SQL (SQLite), Python, pandas, Matplotlib
