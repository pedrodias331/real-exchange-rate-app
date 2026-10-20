IMF Real Exchange Rate Explorer
A Streamlit dashboard for bilateral monthly real and nominal exchange rates using IMF SDMX 3.0 data, designed for Python 3.12.
Features
- Searchable Country 1 / Country 2 selectors (ISO 3166-1 alpha-3 codes, plus IMF Euro area G163). Not every listed country/territory has IMF data.
- Requests monthly CPI and exchange-rate data from January 1976 onward, where available.
- Monthly nominal exchange rate in Country 2 currency per one unit of Country 1 currency.
- Real exchange rate and sample-rebased real index.
- Month-range selectors that recompute the sample mean and index.
- Interactive Real / Nominal / Both chart; Both uses independent vertical axes.
- CSV export with monthly source values, derived values, missing rows and sample mean.
- Cached IMF requests and explicit handling of missing or unavailable data.
Calculation
Let FX_i be IMF-reported units of local currency i per USD, and P_i the CPI index:
S(t)          = FX_2(t) / FX_1(t)
RER(t)        = S(t) * P_1(t) / P_2(t)
RER*          = mean of valid RER(t) within the selected date window
Real Index(t) = 100 * RER(t) / RER*
The nominal series S(t) is plotted in actual currency units, not rebased. The real index averages 100 over valid months in the chosen period. Months with missing CPI or FX inputs are not filled or imputed.
IMF API
Base URL: https://api.imf.org/external/sdmx/3.0

The app requests CSV from the IMF.STA dataflows:
- CPI: /data/dataflow/IMF.STA/CPI/~/BRA.CPI._T.IX.M
- FX: /data/dataflow/IMF.STA/ER/~/BRA.XDC_USD.PA_RT.M
with the filter c[TIME_PERIOD]=ge:1976-M01 and Accept: text/csv. The API, dataflow IDs, keys, and available country coverage are controlled by IMF and may change. The app does not invent data when the service is unavailable. USA FX is defined as 1 USD per USD.
Run locally
1. Install Python 3.12.
2. From this directory, create and activate a virtual environment (optional):
   python -m venv .venv
   # macOS/Linux: source .venv/bin/activate
   # Windows PowerShell: .venv\Scripts\Activate.ps1
3. Install dependencies and start:
   pip install -r requirements.txt
   streamlit run app.py
Deploy on Streamlit Community Cloud
1. Create a public GitHub repository.
2. Add app.py, requirements.txt, and README.md to its root.
3. At https://share.streamlit.io, choose Create app, select your repository and main branch, and set the entrypoint to app.py.
4. In Advanced settings, select Python 3.12 if available, then deploy.
Limitations and troubleshooting
- Coverage: 1976 is the requested lower bound, not a promise of observations for every country. The euro area and many economies have shorter or incomplete histories.
- Empty result: Choose a different country pair. The app requires overlapping positive CPI and FX data for a valid RER.
- IMF error / timeout: Retry later. IMF may rate-limit requests or change its API and dataset conventions; inspect the current IMF dataflow metadata and adjust fetch_imf() accordingly.
- FX definition: XDC_USD.PA_RT.M is assumed to mean local currency per USD, as specified in the project requirements. Check IMF series metadata before using the results for research or publication.
- Index base: Cross-country CPI index levels need not share a reference base. The plotted real index is rebased to the sample mean; its level is not an absolute PPP measure.
- Charts: Two independent axes make it possible to see both series but can be visually misleading when comparing slopes. Use the axis labels and tooltips.
- Validation: The source endpoint could not be live-tested in this build environment; verify IMF API responses for a known country pair after deployment.
Data attribution
Source: International Monetary Fund (IMF), CPI and exchange-rate datasets accessed through the IMF SDMX API. See https://data.imf.org/en/Resource-Pages/IMF-API.
