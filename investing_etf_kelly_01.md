
# Investing On ETFs Using The Kelly Formula Part 1

## How To Retrieve ETF Data Using Python 

## Introduction
The main problem of an investor is identifying profitable trading strategies.
To solve this, he uses fundamental and quantitative techniques to pick winning trading systems.
The next problem concerning the investor is how to optimally allocate his capital to different winning strategies.
A solution is to use the Kelly formula to calculate the amount of capital he will deploy to securities or trading systems before him.

In this article, I will source Vanguard ETFs list from the their website using Python package Selenium. 
I will use the list to pull historical Open-High-Low-Close-Volume (OHCLV) data using Python package pandas-datareader.
The ETF data collected in this article will be used in the next article to present a solution to the investor's capital allocation problem using the Kelly formula.

## Exchange Traded Fund (ETF)
ETFs are traded on major stock exchanges like the New York Stock Exchange and Nasdaq.
You can buy and sell them using online brokers such as TD Ameritrade or Interactive Brokers.
An ETF is a composed of tens, hundreds or sometimes even thousands of stocks (or bonds) in a single fund.

They have grown in popularity due to the following:
1. Low costs: ETFs have an average expense ratio of 0.27% per year.
2. Diversification: They provide a diversified basket of stocks instead of buying a single stock.
3. Alternative themes: ETFs enable investors to gain exposure into niche industries like water and solar tech firms.

## Vanguard
In this article, I will be using ETFs managed by Vanguard and present a brief overview of the firm.
It was established in 1975, and offers both actively and passively managed funds. 
Vanguard's founder is Jack Bogle, the father of index investing.
As of January 2019, it has USD 5.2 trillion of AUM.

## Vanguard ETFs
Vanguard ETFs are grouped by:
1. US Bond ETFs 
2. US Stock ETFs 
3. International Bond ETFs
4. International Stock ETFs
5. Sector ETFs

## Step By Step
1. Source Vanguard ETFs list of tickers.
2. Source Vanguard ETFs historical OHLCV data.

## Source Vanguard ETFs List Of Tickers
In this section, we retrieve tickers of every ETF on Vanguard's website.
We use Selenium, a web browser automation tool, to help us retrieve the ETF tickers.
We propose two approaches, direct and indirect, of sourcing ETF ticker data from website.

### Selenium
Selenium automates web browsers.
Selenium was conceived for automation of web application testing.
It allows you to open a browser and automate actions like clicking buttons and completing forms.

I propose two ways of using Selenium scrape of ETF ticker data:
1. An automated approach: We automate navigation of Vanguard's web site by going from the home page to the ETF list page containing the tickers.
2. A non-automated approach: We go directly to the ETF list page containing the tickers.

Import packages


```python
import pandas as pd
from pandas import Series, DataFrame
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from selenium.common.exceptions import TimeoutException
import pandas_datareader as web
import pickle
import matplotlib.pyplot as plt
%matplotlib inline
```

### Automated Approrach
Code for scraping ETF tickers from Vanguard's home page using the first approach


```python
# # Option
# option = webdriver.ChromeOptions()
# option.add_argument('--incognito')

# # Create a Chrome webdriver 
# driver = webdriver.Chrome('/Applications/chromedriver', options=option)

# # Get to the Vanguard home page
# driver.get('https://investor.vanguard.com/home/')

# # Set delay in seconds
# delay = 10 

# # Head to the next page by clicking on 'Investing'
# try:
#     WebDriverWait(driver, delay).until(EC.presence_of_element_located((By.LINK_TEXT, 'Investing')))
#     driver.find_element_by_link_text('Investing').click()
# except TimeoutException:
#     print("Timed out waiting for page to load.")
#     driver.quit()

# # Head to the next page by clicking on 'Vanguard ETFs'
# try:
#     WebDriverWait(driver, delay).until(EC.presence_of_element_located((By.LINK_TEXT, 'Vanguard ETFs')))
#     driver.find_element_by_link_text('Vanguard ETFs').click()
# except TimeoutException:
#     print("Timed out waiting for page to load.")
#     driver.quit()

# # Head to the next page by clicking on 'Browse a list of Vanguard ETFs'
# try:
#     WebDriverWait(driver, delay).until(EC.presence_of_element_located((By.LINK_TEXT, 'Browse a list of Vanguard ETFs')))
#     driver.find_element_by_partial_link_text('Browse a list of Vanguard ETFs').click()
# except TimeoutException:
#     print("Timed out waiting for page to load.")
#     driver.quit()

# # Source the Vanguard ETF names and tickers
# try:
#     WebDriverWait(driver, delay).until(EC.presence_of_element_located((By.CLASS_NAME, 'productEntry')))
#     content = driver.find_elements_by_class_name('productEntry')
# except TimeoutException:
#     print("Timed out waiting for page to load.")
#     driver.quit()
```

### Non-automated Approach
Code for scraping ETF tickers from Vanguard's ETF list page using the second approach


```python
# Option
option = webdriver.ChromeOptions()
option.add_argument('--incognito')

# Create a Chrome webdriver 
driver = webdriver.Chrome('/Applications/chromedriver', options=option)

# Get to the Vanguard ETF list page
driver.get('https://investor.vanguard.com/etf/list#/etf/asset-class/month-end-returns')

# Set delay in seconds
delay = 10

try:
    WebDriverWait(driver, delay).until(EC.presence_of_element_located((By.CLASS_NAME, 'productEntry')))
    # Source the Vanguard ETF names and tickers
    content = driver.find_elements_by_class_name('productEntry')
except TimeoutException:
    print("Timed out waiting for page to load.")
    driver.quit()
```

### Vanguard ETFs

Inspect the content collected


```python
len(content)
```




    118




```python
for i in content:
    print(i.text)
```

    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    Large-Cap ETF
    Mega Cap ETF
    Mega Cap Growth ETF
    Mega Cap Value ETF
    Vanguard Select Funds
    S&P 500 ETF
    Vanguard Select Funds
    Total Stock Market ETF
    Value ETF
    Vanguard Select Funds
    Extended Market ETF
    Mid-Cap ETF
    Mid-Cap Growth ETF
    Mid-Cap Value ETF
    Small-Cap ETF
    Small-Cap Growth ETF
    Small-Cap Value ETF
    Total World Bond ETF
    Vanguard Select Funds
    Total International Bond ETF
    Emerging Markets Government Bond ETF
    Total World Stock ETF
    ESG International Stock ETF
    FTSE All-World ex-US ETF
    FTSE All-World ex-US Small-Cap ETF
    Vanguard Select Funds
    FTSE Developed Markets ETF
    FTSE Europe ETF
    FTSE Pacific ETF
    Global ex-U.S. Real Estate ETF
    International Dividend Appreciation ETF
    International High Dividend Yield ETF
    Vanguard Select Funds
    Total International Stock ETF
    Vanguard Select Funds
    FTSE Emerging Markets ETF
    Communication Services ETF
    Consumer Discretionary ETF
    Consumer Staples ETF
    Energy ETF
    Financials ETF
    Health Care ETF
    Industrials ETF
    Information Technology ETF
    Materials ETF
    Real Estate ETF
    Utilities ETF
    EDV Bond - Long-term Government 0.07% $125.50 $0.31
    increased
    0.24%
    increased
    2.62%A
    30 day
    6/10/2019 11.45% 14.61% 7.58% 8.82% 8.04% (12/06/2007)
    BIV Bond - Inter-term Investment 0.07% $85.39 -$0.01
    decreased
    -0.01%
    decreased
    2.69%A
    30 day
    6/10/2019 6.34% 8.34% 3.13% 4.97% 5.07% (04/03/2007)
    VGIT Bond - Inter-term Government 0.07% $65.37 -$0.02
    decreased
    -0.02%
    decreased
    2.09%A
    30 day
    6/10/2019 4.01% 6.75% 2.21% — 2.95% (11/19/2009)
    BLV Bond - Long-term Investment 0.07% $94.56 -$0.04
    decreased
    -0.04%
    decreased
    3.53%A
    30 day
    6/10/2019 9.82% 10.28% 5.13% 7.55% 6.86% (04/03/2007)
    VGLT Bond - Long-term Government 0.07% $80.28 $0.06
    increased
    0.07%
    increased
    2.59%A
    30 day
    6/10/2019 8.70% 11.83% 5.40% — 6.32% (11/19/2009)
    VMBS Bond - Inter-term Government 0.07% $52.52 -$0.01
    decreased
    -0.02%
    decreased
    2.91%J
    30 day
    6/10/2019 3.28% 5.16% 2.29% — 2.80% (11/19/2009)
    BSV Bond - Short-term Investment 0.07% $80.10 -$0.03
    decreased
    -0.04%
    decreased
    2.29%A
    30 day
    6/10/2019 2.92% 4.59% 1.58% 2.02% 2.77% (04/03/2007)
    VTIP Bond - Short-term Government 0.06% $49.23 -$0.03
    decreased
    -0.05%
    decreased
    0.44%E,G
    30 day
    6/6/2019 2.73% 2.76% 0.79% — 0.66% (10/12/2012)
    VGSH Bond - Short-term Government 0.07% $60.69 -$0.03
    decreased
    -0.04%
    decreased
    2.11%A
    30 day
    6/10/2019 1.96% 3.45% 1.05% — 0.99% (11/19/2009)
    BND Bond - Inter-term Investment 0.035% $82.11 -$0.04
    decreased
    -0.05%
    decreased
    2.73%A
    30 day
    6/10/2019 4.90% 6.56% 2.65% 3.68% 4.08% (04/03/2007)
    VCIT Bond - Inter-term Investment 0.07% $88.22 $1.28
    increased
    1.47%
    increased
    3.43%A
    30 day
    6/10/2019 8.10% 8.76% 3.73% — 5.46% (11/19/2009)
    VCLT Bond - Long-term Investment 0.07% $93.45 $0.08
    increased
    0.08%
    increased
    4.26%A
    30 day
    6/10/2019 11.63% 9.65% 5.06% — 7.16% (11/19/2009)
    VCSH Bond - Short-term Investment 0.07% $80.10 -$0.03
    decreased
    -0.04%
    decreased
    2.83%A
    30 day
    6/10/2019 4.00% 5.28% 2.21% — 2.98% (11/19/2009)
    VTC Bond - Inter-term Investment 0.07% $84.93 $0.01
    increased
    0.01%
    increased
    3.25%B
    30 day
    6/10/2019 7.73% 7.59% — — 3.22% (11/07/2017)
    VTEB Bond - Long-term National Muni 0.08% $52.91 $0.02
    increased
    0.04%
    increased
    2.02%A
    30 day
    6/10/2019 4.66% 6.31% — — 3.51% (08/21/2015)
    VIG Stock - Large-Cap Blend 0.06% $113.77 -$0.32
    decreased
    -0.28%
    decreased
    1.97%B
    30 day
    5/31/2019 16.78% 8.66% 9.32% 12.97% 8.29% (04/21/2006)
    ESGV Stock - Large-Cap Blend 0.12% $50.37 $0.01
    increased
    0.01%
    increased
    1.74%B
    30 day
    5/31/2019 17.51% — — — -3.52% (09/18/2018)
    VUG Stock - Large-Cap Growth 0.04% $160.77 -$0.05
    decreased
    -0.03%
    decreased
    1.23%B
    30 day
    5/31/2019 20.14% 4.63% 11.06% 15.07% 8.81% (01/26/2004)
    VYM Stock - Large-Cap Value 0.06% $86.72 $0.19
    increased
    0.22%
    increased
    3.50%B
    30 day
    5/31/2019 12.14% 1.63% 8.03% 13.36% 7.28% (11/10/2006)
    VV Stock - Large-Cap Blend 0.04% $132.71 -$0.04
    decreased
    -0.03%
    decreased
    2.01%B
    30 day
    5/31/2019 16.31% 3.75% 9.55% 13.92% 8.32% (01/27/2004)
    MGC Stock - Large-Cap Blend 0.07% $99.90 $0.02
    increased
    0.02%
    increased
    2.06%B
    30 day
    5/31/2019 15.69% 4.23% 9.86% 13.81% 8.10% (12/17/2007)
    MGK Stock - Large-Cap Growth 0.07% $127.45 $0.08
    increased
    0.06%
    increased
    1.34%B
    30 day
    5/31/2019 19.32% 3.98% 11.45% 15.15% 9.69% (12/17/2007)
    MGV Stock - Large-Cap Value 0.07% $79.59 -$0.04
    decreased
    -0.04%
    decreased
    2.82%B
    30 day
    5/31/2019 12.39% 4.03% 8.42% 12.60% 6.59% (12/17/2007)
    VOO Stock - Large-Cap Blend 0.03% $265.38 $0.00
    0.00%
    2.07%B
    30 day
    5/31/2019 16.20% 3.75% 9.63% — 13.49% (09/07/2010)
    VTI Stock - Large-Cap Blend 0.03% $147.50 -$0.08
    decreased
    -0.05%
    decreased
    1.97%B
    30 day
    5/31/2019 16.26% 2.57% 9.25% 13.98% 6.88% (05/24/2001)
    VTV Stock - Large-Cap Value 0.04% $109.49 -$0.03
    decreased
    -0.02%
    decreased
    2.83%B
    30 day
    5/31/2019 12.68% 2.76% 8.18% 12.85% 7.66% (01/26/2004)
    VXF Stock - Mid-Cap Blend 0.07% $115.94 -$0.75
    decreased
    -0.64%
    decreased
    1.46%B
    30 day
    5/31/2019 16.54% -3.64% 7.33% 14.00% 9.09% (12/27/2001)
    VO Stock - Mid-Cap Blend 0.04% $164.12 -$0.40
    decreased
    -0.24%
    decreased
    1.62%B
    30 day
    5/31/2019 19.30% 1.63% 7.98% 14.37% 9.18% (01/26/2004)
    VOT Stock - Mid-Cap Growth 0.07% $147.04 -$0.57
    decreased
    -0.38%
    decreased
    0.85%B
    30 day
    5/31/2019 23.19% 6.34% 9.48% 14.64% 8.88% (08/17/2006)
    VOE Stock - Mid-Cap Value 0.07% $109.17 -$0.04
    decreased
    -0.04%
    decreased
    2.40%B
    30 day
    5/31/2019 15.23% -3.12% 6.43% 13.95% 7.86% (08/17/2006)
    VB Stock - Small-Cap Blend 0.05% $153.12 -$0.32
    decreased
    -0.21%
    decreased
    1.65%B
    30 day
    5/31/2019 16.48% -3.78% 7.30% 14.15% 8.71% (01/26/2004)
    VBK Stock - Small-Cap Growth 0.07% $181.22 -$0.88
    decreased
    -0.48%
    decreased
    0.80%B
    30 day
    5/31/2019 20.62% 0.63% 8.67% 14.89% 9.09% (01/26/2004)
    VBR Stock - Small-Cap Value 0.07% $128.17 $0.03
    increased
    0.02%
    increased
    2.36%B
    30 day
    5/31/2019 12.91% -7.52% 6.11% 13.33% 8.18% (01/26/2004)
    BNDW International 0.09% $77.92 $0.05
    increased
    0.06%
    increased
    1.89%B
    30 day
    6/10/2019 5.02% — — — 6.12% (09/04/2018)
    BNDX International 0.09% $56.74 $0.02
    increased
    0.04%
    increased
    0.72%A
    30 day
    6/10/2019 5.10% 6.89% 4.09% — 4.05% (05/31/2013)
    VWOB International 0.30% $79.05 $0.17
    increased
    0.22%
    increased
    4.98%A
    30 day
    6/10/2019 8.16% 7.35% 3.97% — 4.26% (05/31/2013)
    VT International 0.09% $74.36 $0.19
    increased
    0.26%
    increased
    — 14.11% -1.93% 5.45% 9.54% 5.55% (06/24/2008)
    VSGX International 0.15% $49.59 $0.31
    increased
    0.63%
    increased
    — 11.69% — — — -4.05% (09/18/2018)
    VEU International 0.09% $50.67 $0.32
    increased
    0.63%
    increased
    — 11.57% -6.26% 1.59% 5.90% 2.47% (03/02/2007)
    VSS International 0.12% $104.19 $0.40
    increased
    0.39%
    increased
    — 10.08% -13.13% 1.15% 7.15% 9.61% (04/02/2009)
    VEA International 0.05% $41.46 $0.21
    increased
    0.51%
    increased
    — 12.23% -7.04% 1.57% 6.33% 1.08% (07/20/2007)
    VGK International 0.09% $54.98 $0.32
    increased
    0.59%
    increased
    — 13.75% -6.02% 0.26% 6.32% 4.01% (03/04/2005)
    VPL International 0.09% $65.75 $0.33
    increased
    0.50%
    increased
    — 8.68% -9.98% 3.47% 6.35% 4.27% (03/04/2005)
    VNQI International 0.12% $59.07 $0.22
    increased
    0.37%
    increased
    — 12.79% -0.60% 4.09% — 5.61% (11/01/2010)
    VIGI International 0.25% $66.98 $0.28
    increased
    0.41%
    increased
    — 16.48% -0.63% — — 9.75% (02/25/2016)
    VYMI International 0.32% $61.44 $0.40
    increased
    0.66%
    increased
    — 10.04% -4.25% — — 8.75% (02/25/2016)
    VXUS International 0.09% $52.41 $0.33
    increased
    0.63%
    increased
    — 11.44% -6.98% 1.54% — 3.03% (01/26/2011)
    VWO International 0.12% $41.69 $0.43
    increased
    1.04%
    increased
    — 9.52% -6.47% 1.91% 4.72% 5.92% (03/04/2005)
    VOX Stock - Sector 0.10% $84.57 $0.13
    increased
    0.15%
    increased
    1.07%B
    30 day
    5/31/2019 14.40% 2.94% 2.17% 8.89% 6.65% (09/23/2004)
    VCR Stock - Sector 0.10% $176.39 $0.59
    increased
    0.33%
    increased
    1.33%B
    30 day
    5/31/2019 17.53% 2.31% 10.96% 18.11% 9.44% (01/26/2004)
    VDC Stock - Sector 0.10% $150.74 $0.70
    increased
    0.47%
    increased
    2.77%B
    30 day
    5/31/2019 15.54% 13.12% 6.90% 12.32% 9.32% (01/26/2004)
    VDE Stock - Sector 0.10% $82.15 $0.11
    increased
    0.13%
    increased
    3.67%B
    30 day
    5/31/2019 7.35% -22.79% -7.86% 2.75% 5.18% (09/23/2004)
    VFH Stock - Sector 0.10% $68.42 $0.02
    increased
    0.03%
    increased
    2.35%B
    30 day
    5/31/2019 15.94% -3.53% 9.64% 12.42% 3.81% (01/26/2004)
    VHT Stock - Sector 0.10% $170.38 -$0.50
    decreased
    -0.29%
    decreased
    1.53%B
    30 day
    5/31/2019 6.86% 5.71% 10.06% 15.57% 9.43% (01/26/2004)
    VIS Stock - Sector 0.10% $141.38 -$1.10
    decreased
    -0.77%
    decreased
    1.73%B
    30 day
    5/31/2019 18.04% -1.58% 7.54% 14.44% 8.93% (09/23/2004)
    VGT Stock - Sector 0.10% $207.64 -$0.25
    decreased
    -0.12%
    decreased
    1.34%B
    30 day
    5/31/2019 24.89% 7.61% 17.18% 18.10% 10.10% (01/26/2004)
    VAW Stock - Sector 0.10% $125.34 $0.06
    increased
    0.05%
    increased
    2.13%B
    30 day
    5/31/2019 13.65% -11.39% 3.11% 9.92% 7.70% (01/26/2004)
    VNQ Stock - Sector 0.12% $88.94 $0.19
    increased
    0.21%
    increased
    note 20.08% 14.91% 7.58% 14.92% 8.72% (09/23/2004)
    VPU Stock - Sector 0.10% $131.92 -$0.85
    decreased
    -0.64%
    decreased
    3.21%B
    30 day
    5/31/2019 12.80% 17.11% 10.36% 12.69% 10.07% (01/26/2004)


We notice the following structure:
1. Elements 0-58 are the names of the ETFs.
2. Elements 59-118 are the tickers, asset class, etc. of the ETFs.

Create lists to hold ETF names and tickers


```python
# Initialize the lists
etf_names = []
etf_tickers = []

for i in range(0, 59):
    # Get the names
    try:
        etf_names.append(content[i].text.split('\n')[1])
    except IndexError:
        etf_names.append(content[i].text)
    
    # Get the tickers
    etf_tickers.append(content[i + 59].text.split()[0])
```

Create a DataFrame mapping an ETF's name to it's ticker


```python
etf_map = pd.DataFrame({'Name': etf_names,
                        'Ticker': etf_tickers})
```

### Vanguard ETFs By Group
Create a DataFrame mapping an ETF's name to it's ticker by group.


```python
etf_us_bonds_map = etf_map.iloc[0:15]
etf_us_stocks_map = etf_map.iloc[15:33]
etf_intl_bonds_map = etf_map.iloc[33:36]
etf_intl_stocks_map = etf_map.iloc[36:48]
etf_sectors_map = etf_map.iloc[48:]
```

## Source Vanguard ETFs Historical OHLCV Data

Set the parameters
1. Source is set to the Investors Exchange.
2. Start and end date are set to span 5 years from today.


```python
source = 'iex'
start = '2014-01-01'
end = '2020-01-01'
```

### Vanguard ETFs

Pull historical OHLCV data from the Investors Exchange


```python
# List of tickers
etf_list = etf_map['Ticker'].tolist()

# Initialize data
etf_data = web.DataReader(etf_list[0], source, start, end)
# Conver index to datetime
etf_data.index = pd.to_datetime(etf_data.index)

for i in range(1, len(etf_list)):
    # Pull new data
    temp_data = web.DataReader(etf_list[i], source, start, end)
    
    # Update data
    etf_data = pd.concat([etf_data, temp_data], axis=1, sort=True)
    
# Set multi-level columns
etf_data.columns = pd.MultiIndex.from_product([etf_list, temp_data.columns])
```

Inspect


```python
etf_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">EDV</th>
      <th colspan="5" halign="left">BIV</th>
      <th>...</th>
      <th colspan="5" halign="left">VNQ</th>
      <th colspan="5" halign="left">VPU</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>...</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>85.2790</td>
      <td>86.4896</td>
      <td>84.7732</td>
      <td>86.2077</td>
      <td>34454</td>
      <td>72.1485</td>
      <td>72.3372</td>
      <td>72.0884</td>
      <td>72.3372</td>
      <td>325751</td>
      <td>...</td>
      <td>60.0805</td>
      <td>60.1289</td>
      <td>59.4764</td>
      <td>59.9351</td>
      <td>2249588</td>
      <td>78.0776</td>
      <td>78.5698</td>
      <td>77.2797</td>
      <td>78.4256</td>
      <td>96214</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>85.6853</td>
      <td>86.9375</td>
      <td>85.6853</td>
      <td>86.2824</td>
      <td>36060</td>
      <td>72.0456</td>
      <td>72.2427</td>
      <td>72.0371</td>
      <td>72.1999</td>
      <td>156542</td>
      <td>...</td>
      <td>59.9351</td>
      <td>60.1855</td>
      <td>59.5394</td>
      <td>60.0885</td>
      <td>1853138</td>
      <td>78.2643</td>
      <td>78.9518</td>
      <td>78.0521</td>
      <td>78.8499</td>
      <td>99422</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>86.4233</td>
      <td>86.7762</td>
      <td>86.0253</td>
      <td>86.5809</td>
      <td>72137</td>
      <td>72.2428</td>
      <td>72.2600</td>
      <td>72.1571</td>
      <td>72.2257</td>
      <td>390893</td>
      <td>...</td>
      <td>60.0401</td>
      <td>60.2920</td>
      <td>59.6929</td>
      <td>59.7817</td>
      <td>2643862</td>
      <td>78.9348</td>
      <td>79.9788</td>
      <td>78.7803</td>
      <td>79.4101</td>
      <td>1006261</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>86.3404</td>
      <td>86.3404</td>
      <td>85.5029</td>
      <td>85.7336</td>
      <td>55781</td>
      <td>72.1313</td>
      <td>72.1313</td>
      <td>71.9426</td>
      <td>71.9426</td>
      <td>306016</td>
      <td>...</td>
      <td>59.7413</td>
      <td>59.9997</td>
      <td>59.5171</td>
      <td>59.9351</td>
      <td>1942368</td>
      <td>79.3168</td>
      <td>79.5544</td>
      <td>78.9857</td>
      <td>79.3592</td>
      <td>265682</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>85.9258</td>
      <td>86.6058</td>
      <td>85.6771</td>
      <td>86.3902</td>
      <td>15027</td>
      <td>72.0884</td>
      <td>72.2857</td>
      <td>71.9855</td>
      <td>72.2857</td>
      <td>300049</td>
      <td>...</td>
      <td>59.9432</td>
      <td>60.4883</td>
      <td>59.6929</td>
      <td>60.3550</td>
      <td>2734503</td>
      <td>79.3507</td>
      <td>80.9549</td>
      <td>79.3507</td>
      <td>80.9549</td>
      <td>151926</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 295 columns</p>
</div>




```python
etf_data.EDV.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>85.2790</td>
      <td>86.4896</td>
      <td>84.7732</td>
      <td>86.2077</td>
      <td>34454</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>85.6853</td>
      <td>86.9375</td>
      <td>85.6853</td>
      <td>86.2824</td>
      <td>36060</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>86.4233</td>
      <td>86.7762</td>
      <td>86.0253</td>
      <td>86.5809</td>
      <td>72137</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>86.3404</td>
      <td>86.3404</td>
      <td>85.5029</td>
      <td>85.7336</td>
      <td>55781</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>85.9258</td>
      <td>86.6058</td>
      <td>85.6771</td>
      <td>86.3902</td>
      <td>15027</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_data.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">EDV</th>
      <th colspan="5" halign="left">BIV</th>
      <th>...</th>
      <th colspan="5" halign="left">VNQ</th>
      <th colspan="5" halign="left">VPU</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>...</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>125.13</td>
      <td>125.5000</td>
      <td>124.4900</td>
      <td>124.60</td>
      <td>301695</td>
      <td>85.51</td>
      <td>85.640</td>
      <td>85.380</td>
      <td>85.47</td>
      <td>606893</td>
      <td>...</td>
      <td>87.21</td>
      <td>88.620</td>
      <td>86.99</td>
      <td>88.52</td>
      <td>6775425</td>
      <td>131.67</td>
      <td>134.1402</td>
      <td>131.2348</td>
      <td>133.75</td>
      <td>243189</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>125.85</td>
      <td>126.5848</td>
      <td>124.8075</td>
      <td>125.26</td>
      <td>137576</td>
      <td>85.46</td>
      <td>85.535</td>
      <td>85.320</td>
      <td>85.40</td>
      <td>572499</td>
      <td>...</td>
      <td>88.80</td>
      <td>88.890</td>
      <td>88.05</td>
      <td>88.69</td>
      <td>4077190</td>
      <td>133.91</td>
      <td>134.7300</td>
      <td>133.6399</td>
      <td>134.52</td>
      <td>1183343</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>126.65</td>
      <td>126.9993</td>
      <td>126.1300</td>
      <td>126.56</td>
      <td>88292</td>
      <td>85.76</td>
      <td>85.830</td>
      <td>85.595</td>
      <td>85.68</td>
      <td>381772</td>
      <td>...</td>
      <td>89.13</td>
      <td>89.569</td>
      <td>88.92</td>
      <td>89.02</td>
      <td>3525699</td>
      <td>135.29</td>
      <td>135.9600</td>
      <td>133.6300</td>
      <td>133.66</td>
      <td>235898</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>125.42</td>
      <td>125.4200</td>
      <td>124.8400</td>
      <td>125.20</td>
      <td>153210</td>
      <td>85.51</td>
      <td>85.550</td>
      <td>85.390</td>
      <td>85.40</td>
      <td>350319</td>
      <td>...</td>
      <td>89.19</td>
      <td>89.290</td>
      <td>88.40</td>
      <td>88.73</td>
      <td>2945455</td>
      <td>133.33</td>
      <td>133.4100</td>
      <td>132.1900</td>
      <td>132.80</td>
      <td>237848</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>125.12</td>
      <td>125.8000</td>
      <td>125.0900</td>
      <td>125.48</td>
      <td>187176</td>
      <td>85.37</td>
      <td>85.430</td>
      <td>85.330</td>
      <td>85.39</td>
      <td>471072</td>
      <td>...</td>
      <td>88.99</td>
      <td>89.190</td>
      <td>88.31</td>
      <td>88.97</td>
      <td>3834010</td>
      <td>132.80</td>
      <td>132.9950</td>
      <td>131.2100</td>
      <td>131.97</td>
      <td>105742</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 295 columns</p>
</div>




```python
etf_data.EDV.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>125.13</td>
      <td>125.5000</td>
      <td>124.4900</td>
      <td>124.60</td>
      <td>301695</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>125.85</td>
      <td>126.5848</td>
      <td>124.8075</td>
      <td>125.26</td>
      <td>137576</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>126.65</td>
      <td>126.9993</td>
      <td>126.1300</td>
      <td>126.56</td>
      <td>88292</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>125.42</td>
      <td>125.4200</td>
      <td>124.8400</td>
      <td>125.20</td>
      <td>153210</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>125.12</td>
      <td>125.8000</td>
      <td>125.0900</td>
      <td>125.48</td>
      <td>187176</td>
    </tr>
  </tbody>
</table>
</div>



### Vanguard ETFs By Group

Create DataFrame objects containing close prices of ETFs by group.


```python
# Create list of Vanguard ETF groups
groups = ['us bonds',
          'us stocks',
          'intl bonds',
          'intl stocks',
          'sectors']

# Create a dictionary of Vanguard ETF maps
maps = {'us bonds': etf_us_bonds_map,
        'us stocks': etf_us_stocks_map,
        'intl bonds': etf_intl_bonds_map,
        'intl stocks': etf_intl_stocks_map,
        'sectors': etf_sectors_map}

# Initialize Vanguard ETF close dictionary
close = {'us bonds': pd.DataFrame(),
         'us stocks': pd.DataFrame(),
         'intl bonds': pd.DataFrame(),
         'intl stocks': pd.DataFrame(),
         'sectors': pd.DataFrame()}

# Update Vanguard ETF close dictionary
for i in groups:
    # Set tickers
    tickers = maps[i]['Ticker'].tolist()
     
    res = {}
    for j in range(len(tickers)):
        res[tickers[j]] = etf_data[tickers[j]].close
        
    # Update close prices 
    close[i] = pd.DataFrame(res)
```

### Vanguard US Bond ETFs


```python
etf_us_bonds_close = close['us bonds']
```

Inspect


```python
etf_us_bonds_close.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EDV</th>
      <th>BIV</th>
      <th>VGIT</th>
      <th>BLV</th>
      <th>VGLT</th>
      <th>VMBS</th>
      <th>BSV</th>
      <th>VTIP</th>
      <th>VGSH</th>
      <th>BND</th>
      <th>VCIT</th>
      <th>VCLT</th>
      <th>VCSH</th>
      <th>VTC</th>
      <th>VTEB</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>86.2077</td>
      <td>72.3372</td>
      <td>58.3027</td>
      <td>72.9509</td>
      <td>61.2526</td>
      <td>46.7804</td>
      <td>73.7095</td>
      <td>47.2736</td>
      <td>57.5397</td>
      <td>71.6522</td>
      <td>72.7439</td>
      <td>71.9479</td>
      <td>71.7766</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>86.2824</td>
      <td>72.1999</td>
      <td>58.2387</td>
      <td>72.9960</td>
      <td>61.3021</td>
      <td>46.7268</td>
      <td>73.6636</td>
      <td>47.2263</td>
      <td>57.5113</td>
      <td>71.6347</td>
      <td>72.6340</td>
      <td>71.9076</td>
      <td>71.7676</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>86.5809</td>
      <td>72.2257</td>
      <td>58.1847</td>
      <td>73.0862</td>
      <td>61.3876</td>
      <td>46.7358</td>
      <td>73.6360</td>
      <td>47.2168</td>
      <td>57.4925</td>
      <td>71.6172</td>
      <td>72.6847</td>
      <td>71.9882</td>
      <td>71.6871</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>85.7336</td>
      <td>71.9426</td>
      <td>58.0467</td>
      <td>72.6678</td>
      <td>61.0080</td>
      <td>46.6733</td>
      <td>73.5809</td>
      <td>47.2168</td>
      <td>57.4641</td>
      <td>71.4508</td>
      <td>72.3635</td>
      <td>71.6739</td>
      <td>71.6692</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>86.3902</td>
      <td>72.2857</td>
      <td>58.2103</td>
      <td>73.0698</td>
      <td>61.2963</td>
      <td>46.8250</td>
      <td>73.6452</td>
      <td>47.3493</td>
      <td>57.5019</td>
      <td>71.6347</td>
      <td>72.7439</td>
      <td>72.0849</td>
      <td>71.7766</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_bonds_close[['EDV', 'BIV', 'VGIT', 'BLV', 'VGLT']].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EDV</th>
      <th>BIV</th>
      <th>VGIT</th>
      <th>BLV</th>
      <th>VGLT</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>86.2077</td>
      <td>72.3372</td>
      <td>58.3027</td>
      <td>72.9509</td>
      <td>61.2526</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>86.2824</td>
      <td>72.1999</td>
      <td>58.2387</td>
      <td>72.9960</td>
      <td>61.3021</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>86.5809</td>
      <td>72.2257</td>
      <td>58.1847</td>
      <td>73.0862</td>
      <td>61.3876</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>85.7336</td>
      <td>71.9426</td>
      <td>58.0467</td>
      <td>72.6678</td>
      <td>61.0080</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>86.3902</td>
      <td>72.2857</td>
      <td>58.2103</td>
      <td>73.0698</td>
      <td>61.2963</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_bonds_close.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EDV</th>
      <th>BIV</th>
      <th>VGIT</th>
      <th>BLV</th>
      <th>VGLT</th>
      <th>VMBS</th>
      <th>BSV</th>
      <th>VTIP</th>
      <th>VGSH</th>
      <th>BND</th>
      <th>VCIT</th>
      <th>VCLT</th>
      <th>VCSH</th>
      <th>VTC</th>
      <th>VTEB</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>124.60</td>
      <td>85.47</td>
      <td>65.50</td>
      <td>94.15</td>
      <td>80.02</td>
      <td>52.52</td>
      <td>80.21</td>
      <td>49.30</td>
      <td>60.77</td>
      <td>82.14</td>
      <td>88.14</td>
      <td>92.77</td>
      <td>80.13</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>125.26</td>
      <td>85.40</td>
      <td>65.44</td>
      <td>94.50</td>
      <td>80.27</td>
      <td>52.53</td>
      <td>80.12</td>
      <td>49.28</td>
      <td>60.72</td>
      <td>82.12</td>
      <td>88.13</td>
      <td>93.04</td>
      <td>80.09</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>126.56</td>
      <td>85.68</td>
      <td>65.61</td>
      <td>95.18</td>
      <td>80.92</td>
      <td>52.62</td>
      <td>80.23</td>
      <td>49.37</td>
      <td>60.78</td>
      <td>82.34</td>
      <td>88.46</td>
      <td>93.78</td>
      <td>80.20</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>125.20</td>
      <td>85.40</td>
      <td>65.40</td>
      <td>94.63</td>
      <td>80.20</td>
      <td>52.54</td>
      <td>80.13</td>
      <td>49.26</td>
      <td>60.72</td>
      <td>82.17</td>
      <td>88.21</td>
      <td>93.50</td>
      <td>80.14</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>125.48</td>
      <td>85.39</td>
      <td>65.36</td>
      <td>94.57</td>
      <td>80.29</td>
      <td>52.51</td>
      <td>80.11</td>
      <td>49.22</td>
      <td>60.68</td>
      <td>82.11</td>
      <td>88.21</td>
      <td>93.53</td>
      <td>80.10</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_bonds_close[['EDV', 'BIV', 'VGIT', 'BLV', 'VGLT']].tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EDV</th>
      <th>BIV</th>
      <th>VGIT</th>
      <th>BLV</th>
      <th>VGLT</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>124.60</td>
      <td>85.47</td>
      <td>65.50</td>
      <td>94.15</td>
      <td>80.02</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>125.26</td>
      <td>85.40</td>
      <td>65.44</td>
      <td>94.50</td>
      <td>80.27</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>126.56</td>
      <td>85.68</td>
      <td>65.61</td>
      <td>95.18</td>
      <td>80.92</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>125.20</td>
      <td>85.40</td>
      <td>65.40</td>
      <td>94.63</td>
      <td>80.20</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>125.48</td>
      <td>85.39</td>
      <td>65.36</td>
      <td>94.57</td>
      <td>80.29</td>
    </tr>
  </tbody>
</table>
</div>



VTC and VTEB close prices are NaNs, we drop these funds.


```python
etf_us_bonds_close.drop(['VTC', 'VTEB'], axis='columns', inplace=True)
```

### Vanguard US Stock ETFs


```python
etf_us_stocks_close = close['us stocks']
```

Inspect


```python
etf_us_stocks_close.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VIG</th>
      <th>ESGV</th>
      <th>VUG</th>
      <th>VYM</th>
      <th>VV</th>
      <th>MGC</th>
      <th>MGK</th>
      <th>MGV</th>
      <th>VOO</th>
      <th>VTI</th>
      <th>VTV</th>
      <th>VXF</th>
      <th>VO</th>
      <th>VOT</th>
      <th>VOE</th>
      <th>VB</th>
      <th>VBK</th>
      <th>VBR</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>69.5502</td>
      <td>NaN</td>
      <td>91.2609</td>
      <td>56.2715</td>
      <td>80.5827</td>
      <td>59.7627</td>
      <td>70.6379</td>
      <td>50.7774</td>
      <td>160.3744</td>
      <td>91.0774</td>
      <td>70.6714</td>
      <td>79.4990</td>
      <td>107.5091</td>
      <td>90.5219</td>
      <td>76.4035</td>
      <td>105.3637</td>
      <td>117.2411</td>
      <td>93.1253</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>69.6492</td>
      <td>NaN</td>
      <td>91.4579</td>
      <td>56.5027</td>
      <td>80.8184</td>
      <td>59.9253</td>
      <td>70.7875</td>
      <td>51.0587</td>
      <td>160.8808</td>
      <td>91.3771</td>
      <td>70.9886</td>
      <td>79.7502</td>
      <td>107.9332</td>
      <td>90.7654</td>
      <td>76.7002</td>
      <td>105.7062</td>
      <td>117.6700</td>
      <td>93.3694</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>69.6852</td>
      <td>NaN</td>
      <td>91.5235</td>
      <td>56.6654</td>
      <td>80.9272</td>
      <td>60.0065</td>
      <td>70.8623</td>
      <td>51.0499</td>
      <td>161.0164</td>
      <td>91.4498</td>
      <td>71.0591</td>
      <td>79.8898</td>
      <td>108.0167</td>
      <td>90.8770</td>
      <td>76.7722</td>
      <td>105.9931</td>
      <td>118.2894</td>
      <td>93.4056</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>69.8382</td>
      <td>NaN</td>
      <td>91.7017</td>
      <td>56.7596</td>
      <td>81.1175</td>
      <td>60.1510</td>
      <td>70.9464</td>
      <td>51.2082</td>
      <td>161.4414</td>
      <td>91.7767</td>
      <td>71.2706</td>
      <td>80.5038</td>
      <td>108.4983</td>
      <td>91.2417</td>
      <td>77.1947</td>
      <td>106.7613</td>
      <td>119.2423</td>
      <td>94.1741</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>70.5041</td>
      <td>NaN</td>
      <td>92.4053</td>
      <td>57.1450</td>
      <td>81.7520</td>
      <td>60.6205</td>
      <td>71.5070</td>
      <td>51.6566</td>
      <td>162.6440</td>
      <td>92.4578</td>
      <td>71.8258</td>
      <td>80.9318</td>
      <td>109.1837</td>
      <td>91.7695</td>
      <td>77.6892</td>
      <td>107.4184</td>
      <td>119.9952</td>
      <td>94.6171</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_stocks_close[['VIG', 'ESGV', 'VUG', 'VYM', 'VV']].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VIG</th>
      <th>ESGV</th>
      <th>VUG</th>
      <th>VYM</th>
      <th>VV</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>69.5502</td>
      <td>NaN</td>
      <td>91.2609</td>
      <td>56.2715</td>
      <td>80.5827</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>69.6492</td>
      <td>NaN</td>
      <td>91.4579</td>
      <td>56.5027</td>
      <td>80.8184</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>69.6852</td>
      <td>NaN</td>
      <td>91.5235</td>
      <td>56.6654</td>
      <td>80.9272</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>69.8382</td>
      <td>NaN</td>
      <td>91.7017</td>
      <td>56.7596</td>
      <td>81.1175</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>70.5041</td>
      <td>NaN</td>
      <td>92.4053</td>
      <td>57.1450</td>
      <td>81.7520</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_stocks_close.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VIG</th>
      <th>ESGV</th>
      <th>VUG</th>
      <th>VYM</th>
      <th>VV</th>
      <th>MGC</th>
      <th>MGK</th>
      <th>MGV</th>
      <th>VOO</th>
      <th>VTI</th>
      <th>VTV</th>
      <th>VXF</th>
      <th>VO</th>
      <th>VOT</th>
      <th>VOE</th>
      <th>VB</th>
      <th>VBK</th>
      <th>VBR</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>112.07</td>
      <td>NaN</td>
      <td>156.30</td>
      <td>85.44</td>
      <td>129.98</td>
      <td>97.6700</td>
      <td>123.66</td>
      <td>78.47</td>
      <td>259.85</td>
      <td>144.66</td>
      <td>107.96</td>
      <td>114.62</td>
      <td>161.93</td>
      <td>144.68</td>
      <td>108.01</td>
      <td>151.40</td>
      <td>178.53</td>
      <td>127.16</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>112.72</td>
      <td>NaN</td>
      <td>157.51</td>
      <td>86.00</td>
      <td>130.77</td>
      <td>98.3100</td>
      <td>124.52</td>
      <td>78.90</td>
      <td>261.54</td>
      <td>145.47</td>
      <td>108.63</td>
      <td>114.78</td>
      <td>162.81</td>
      <td>145.49</td>
      <td>108.60</td>
      <td>151.57</td>
      <td>178.89</td>
      <td>127.16</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>113.85</td>
      <td>NaN</td>
      <td>159.85</td>
      <td>86.37</td>
      <td>132.09</td>
      <td>99.4358</td>
      <td>126.65</td>
      <td>79.31</td>
      <td>264.15</td>
      <td>146.79</td>
      <td>109.17</td>
      <td>115.48</td>
      <td>163.73</td>
      <td>146.65</td>
      <td>108.93</td>
      <td>152.51</td>
      <td>180.71</td>
      <td>127.58</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>114.09</td>
      <td>NaN</td>
      <td>160.82</td>
      <td>86.55</td>
      <td>132.76</td>
      <td>99.8900</td>
      <td>127.40</td>
      <td>79.60</td>
      <td>265.39</td>
      <td>147.61</td>
      <td>109.52</td>
      <td>116.27</td>
      <td>164.53</td>
      <td>147.65</td>
      <td>109.24</td>
      <td>153.47</td>
      <td>182.04</td>
      <td>128.09</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>113.77</td>
      <td>NaN</td>
      <td>160.72</td>
      <td>86.72</td>
      <td>132.70</td>
      <td>99.9000</td>
      <td>127.44</td>
      <td>79.60</td>
      <td>265.34</td>
      <td>147.47</td>
      <td>109.47</td>
      <td>115.89</td>
      <td>164.12</td>
      <td>147.04</td>
      <td>109.14</td>
      <td>153.09</td>
      <td>181.21</td>
      <td>128.19</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_us_stocks_close[['VIG', 'ESGV', 'VUG', 'VYM', 'VV']].tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VIG</th>
      <th>ESGV</th>
      <th>VUG</th>
      <th>VYM</th>
      <th>VV</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>112.07</td>
      <td>NaN</td>
      <td>156.30</td>
      <td>85.44</td>
      <td>129.98</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>112.72</td>
      <td>NaN</td>
      <td>157.51</td>
      <td>86.00</td>
      <td>130.77</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>113.85</td>
      <td>NaN</td>
      <td>159.85</td>
      <td>86.37</td>
      <td>132.09</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>114.09</td>
      <td>NaN</td>
      <td>160.82</td>
      <td>86.55</td>
      <td>132.76</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>113.77</td>
      <td>NaN</td>
      <td>160.72</td>
      <td>86.72</td>
      <td>132.70</td>
    </tr>
  </tbody>
</table>
</div>



ESGV close prices are NaNs, we drop this fund.


```python
etf_us_stocks_close.drop('ESGV', axis='columns', inplace=True)
```

### Vanguard International Bond ETFs


```python
etf_intl_bonds_close = close['intl bonds']
```

Inspect


```python
etf_intl_bonds_close.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BNDW</th>
      <th>BNDX</th>
      <th>VWOB</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>NaN</td>
      <td>46.3357</td>
      <td>63.9766</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>NaN</td>
      <td>46.3538</td>
      <td>63.9528</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>NaN</td>
      <td>46.3357</td>
      <td>63.8257</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>NaN</td>
      <td>46.2995</td>
      <td>63.7462</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>NaN</td>
      <td>46.3448</td>
      <td>63.7383</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_intl_bonds_close.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BNDW</th>
      <th>BNDX</th>
      <th>VWOB</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>NaN</td>
      <td>56.61</td>
      <td>78.370</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>NaN</td>
      <td>56.67</td>
      <td>78.640</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>NaN</td>
      <td>56.82</td>
      <td>78.870</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>NaN</td>
      <td>56.71</td>
      <td>78.960</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>NaN</td>
      <td>56.73</td>
      <td>79.075</td>
    </tr>
  </tbody>
</table>
</div>



BNDW close prices are NaNs, we drop this fund.


```python
etf_intl_bonds_close.drop('BNDW', axis='columns', inplace=True)
```

### Vanguard International Stock ETFs


```python
etf_intl_stocks_close = close['intl stocks']
```

Inspect


```python
etf_intl_stocks_close.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VT</th>
      <th>VSGX</th>
      <th>VEU</th>
      <th>VSS</th>
      <th>VEA</th>
      <th>VGK</th>
      <th>VPL</th>
      <th>VNQI</th>
      <th>VIGI</th>
      <th>VYMI</th>
      <th>VXUS</th>
      <th>VWO</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>55.1091</td>
      <td>NaN</td>
      <td>45.4997</td>
      <td>96.7475</td>
      <td>37.0462</td>
      <td>52.0153</td>
      <td>53.8583</td>
      <td>47.0823</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>47.1400</td>
      <td>38.0651</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>55.2604</td>
      <td>NaN</td>
      <td>45.5516</td>
      <td>96.6253</td>
      <td>37.0376</td>
      <td>51.9387</td>
      <td>54.0336</td>
      <td>47.1395</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>47.2005</td>
      <td>38.2053</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>55.2248</td>
      <td>NaN</td>
      <td>45.5256</td>
      <td>96.3723</td>
      <td>37.0635</td>
      <td>51.9727</td>
      <td>54.0161</td>
      <td>47.0332</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>47.1573</td>
      <td>37.9600</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>55.3317</td>
      <td>NaN</td>
      <td>45.4217</td>
      <td>96.1280</td>
      <td>37.0203</td>
      <td>51.9727</td>
      <td>53.8671</td>
      <td>46.9269</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>47.0967</td>
      <td>37.9863</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>55.6521</td>
      <td>NaN</td>
      <td>45.8633</td>
      <td>96.9656</td>
      <td>37.3402</td>
      <td>52.4154</td>
      <td>54.4544</td>
      <td>47.2949</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>47.5379</td>
      <td>38.4243</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_intl_stocks_close[['VT', 'VSGX', 'VEU', 'VSS', 'VEA']].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VT</th>
      <th>VSGX</th>
      <th>VEU</th>
      <th>VSS</th>
      <th>VEA</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>55.1091</td>
      <td>NaN</td>
      <td>45.4997</td>
      <td>96.7475</td>
      <td>37.0462</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>55.2604</td>
      <td>NaN</td>
      <td>45.5516</td>
      <td>96.6253</td>
      <td>37.0376</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>55.2248</td>
      <td>NaN</td>
      <td>45.5256</td>
      <td>96.3723</td>
      <td>37.0635</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>55.3317</td>
      <td>NaN</td>
      <td>45.4217</td>
      <td>96.1280</td>
      <td>37.0203</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>55.6521</td>
      <td>NaN</td>
      <td>45.8633</td>
      <td>96.9656</td>
      <td>37.3402</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_intl_stocks_close.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VT</th>
      <th>VSGX</th>
      <th>VEU</th>
      <th>VSS</th>
      <th>VEA</th>
      <th>VGK</th>
      <th>VPL</th>
      <th>VNQI</th>
      <th>VIGI</th>
      <th>VYMI</th>
      <th>VXUS</th>
      <th>VWO</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>72.77</td>
      <td>NaN</td>
      <td>49.43</td>
      <td>102.31</td>
      <td>40.50</td>
      <td>53.57</td>
      <td>64.20</td>
      <td>57.91</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51.14</td>
      <td>40.59</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>73.11</td>
      <td>NaN</td>
      <td>49.61</td>
      <td>102.40</td>
      <td>40.67</td>
      <td>53.85</td>
      <td>64.38</td>
      <td>58.20</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51.26</td>
      <td>40.58</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>73.85</td>
      <td>NaN</td>
      <td>50.13</td>
      <td>103.50</td>
      <td>41.15</td>
      <td>54.56</td>
      <td>65.05</td>
      <td>58.50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51.85</td>
      <td>40.87</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>74.16</td>
      <td>NaN</td>
      <td>50.34</td>
      <td>103.83</td>
      <td>41.26</td>
      <td>54.64</td>
      <td>65.40</td>
      <td>58.90</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>52.06</td>
      <td>41.25</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>74.37</td>
      <td>NaN</td>
      <td>50.68</td>
      <td>104.21</td>
      <td>41.46</td>
      <td>54.99</td>
      <td>65.77</td>
      <td>59.07</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>52.40</td>
      <td>41.68</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_intl_stocks_close[['VT', 'VSGX', 'VEU', 'VSS', 'VEA']].tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VT</th>
      <th>VSGX</th>
      <th>VEU</th>
      <th>VSS</th>
      <th>VEA</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>72.77</td>
      <td>NaN</td>
      <td>49.43</td>
      <td>102.31</td>
      <td>40.50</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>73.11</td>
      <td>NaN</td>
      <td>49.61</td>
      <td>102.40</td>
      <td>40.67</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>73.85</td>
      <td>NaN</td>
      <td>50.13</td>
      <td>103.50</td>
      <td>41.15</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>74.16</td>
      <td>NaN</td>
      <td>50.34</td>
      <td>103.83</td>
      <td>41.26</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>74.37</td>
      <td>NaN</td>
      <td>50.68</td>
      <td>104.21</td>
      <td>41.46</td>
    </tr>
  </tbody>
</table>
</div>



VSGX, VIGI, and VYMI close prices are NaNs, we drop these funds.


```python
etf_intl_stocks_close.drop(['VSGX', 'VIGI', 'VYMI'], axis='columns', inplace=True)
```

### Vanguard Sector ETFs


```python
etf_sectors_close = close['sectors']
```

Inspect


```python
etf_sectors_close.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VOX</th>
      <th>VCR</th>
      <th>VDC</th>
      <th>VDE</th>
      <th>VFH</th>
      <th>VHT</th>
      <th>VIS</th>
      <th>VGT</th>
      <th>VAW</th>
      <th>VNQ</th>
      <th>VPU</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>73.2093</td>
      <td>99.4362</td>
      <td>101.4614</td>
      <td>121.4008</td>
      <td>41.7458</td>
      <td>101.6054</td>
      <td>94.4731</td>
      <td>88.8409</td>
      <td>99.0038</td>
      <td>59.9351</td>
      <td>78.4256</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>73.8089</td>
      <td>99.5073</td>
      <td>101.4878</td>
      <td>122.6118</td>
      <td>41.7458</td>
      <td>101.5308</td>
      <td>94.7460</td>
      <td>89.4035</td>
      <td>99.4385</td>
      <td>60.0885</td>
      <td>78.8499</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>73.8688</td>
      <td>99.8709</td>
      <td>101.8401</td>
      <td>123.2038</td>
      <td>41.5917</td>
      <td>101.8105</td>
      <td>94.6732</td>
      <td>89.5254</td>
      <td>99.3027</td>
      <td>59.7817</td>
      <td>79.4101</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>74.1943</td>
      <td>100.2812</td>
      <td>101.9634</td>
      <td>123.0545</td>
      <td>42.0086</td>
      <td>101.8851</td>
      <td>95.0098</td>
      <td>89.8630</td>
      <td>99.6558</td>
      <td>59.9351</td>
      <td>79.3592</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>74.8623</td>
      <td>100.9804</td>
      <td>103.0908</td>
      <td>123.9834</td>
      <td>42.2352</td>
      <td>102.6588</td>
      <td>95.5193</td>
      <td>90.2849</td>
      <td>100.6248</td>
      <td>60.3550</td>
      <td>80.9549</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_sectors_close[['VOX', 'VCR', 'VDC', 'VDE', 'VFH']].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VOX</th>
      <th>VCR</th>
      <th>VDC</th>
      <th>VDE</th>
      <th>VFH</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-06-12</th>
      <td>73.2093</td>
      <td>99.4362</td>
      <td>101.4614</td>
      <td>121.4008</td>
      <td>41.7458</td>
    </tr>
    <tr>
      <th>2014-06-13</th>
      <td>73.8089</td>
      <td>99.5073</td>
      <td>101.4878</td>
      <td>122.6118</td>
      <td>41.7458</td>
    </tr>
    <tr>
      <th>2014-06-16</th>
      <td>73.8688</td>
      <td>99.8709</td>
      <td>101.8401</td>
      <td>123.2038</td>
      <td>41.5917</td>
    </tr>
    <tr>
      <th>2014-06-17</th>
      <td>74.1943</td>
      <td>100.2812</td>
      <td>101.9634</td>
      <td>123.0545</td>
      <td>42.0086</td>
    </tr>
    <tr>
      <th>2014-06-18</th>
      <td>74.8623</td>
      <td>100.9804</td>
      <td>103.0908</td>
      <td>123.9834</td>
      <td>42.2352</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_sectors_close.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VOX</th>
      <th>VCR</th>
      <th>VDC</th>
      <th>VDE</th>
      <th>VFH</th>
      <th>VHT</th>
      <th>VIS</th>
      <th>VGT</th>
      <th>VAW</th>
      <th>VNQ</th>
      <th>VPU</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>83.15</td>
      <td>171.09</td>
      <td>147.27</td>
      <td>80.19</td>
      <td>67.76</td>
      <td>168.02</td>
      <td>140.89</td>
      <td>199.81</td>
      <td>122.77</td>
      <td>88.52</td>
      <td>133.75</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>83.43</td>
      <td>171.70</td>
      <td>148.60</td>
      <td>81.49</td>
      <td>67.98</td>
      <td>168.21</td>
      <td>140.98</td>
      <td>202.02</td>
      <td>124.10</td>
      <td>88.69</td>
      <td>134.52</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>84.47</td>
      <td>174.18</td>
      <td>149.93</td>
      <td>81.82</td>
      <td>67.81</td>
      <td>170.42</td>
      <td>142.08</td>
      <td>205.55</td>
      <td>125.00</td>
      <td>89.02</td>
      <td>133.66</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>84.47</td>
      <td>175.76</td>
      <td>149.98</td>
      <td>81.99</td>
      <td>68.37</td>
      <td>170.90</td>
      <td>142.43</td>
      <td>207.80</td>
      <td>125.28</td>
      <td>88.73</td>
      <td>132.80</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>84.61</td>
      <td>176.42</td>
      <td>150.73</td>
      <td>82.12</td>
      <td>68.40</td>
      <td>170.42</td>
      <td>141.42</td>
      <td>207.55</td>
      <td>125.38</td>
      <td>88.97</td>
      <td>131.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
etf_sectors_close[['VOX', 'VCR', 'VDC', 'VDE', 'VFH']].tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>VOX</th>
      <th>VCR</th>
      <th>VDC</th>
      <th>VDE</th>
      <th>VFH</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-06-05</th>
      <td>83.15</td>
      <td>171.09</td>
      <td>147.27</td>
      <td>80.19</td>
      <td>67.76</td>
    </tr>
    <tr>
      <th>2019-06-06</th>
      <td>83.43</td>
      <td>171.70</td>
      <td>148.60</td>
      <td>81.49</td>
      <td>67.98</td>
    </tr>
    <tr>
      <th>2019-06-07</th>
      <td>84.47</td>
      <td>174.18</td>
      <td>149.93</td>
      <td>81.82</td>
      <td>67.81</td>
    </tr>
    <tr>
      <th>2019-06-10</th>
      <td>84.47</td>
      <td>175.76</td>
      <td>149.98</td>
      <td>81.99</td>
      <td>68.37</td>
    </tr>
    <tr>
      <th>2019-06-11</th>
      <td>84.61</td>
      <td>176.42</td>
      <td>150.73</td>
      <td>82.12</td>
      <td>68.40</td>
    </tr>
  </tbody>
</table>
</div>



### Plot Of Vanguard ETFs By Group


```python
plt.figure(figsize=[20, 40])

# US Bonds
plt.subplot(5, 1, 1)
for i in etf_us_bonds_close.columns.tolist():
    plt.plot(etf_us_bonds_close[i])    
plt.title('Vanguard US Bonds ETFs - Close')
plt.legend(etf_us_bonds_close.columns.tolist(), loc=2)
plt.xlim(etf_data.index[0], etf_data.index[-1])

# US Stocks
plt.subplot(5, 1, 2)
for i in etf_us_stocks_close.columns.tolist():
    plt.plot(etf_us_stocks_close[i])    
plt.title('Vanguard US Stocks ETFs - Close')
plt.legend(etf_us_stocks_close.columns.tolist(), loc=2)
plt.xlim(etf_data.index[0], etf_data.index[-1])

# Intl Bonds
plt.subplot(5, 1, 3)
for i in etf_intl_bonds_close.columns.tolist():
    plt.plot(etf_intl_bonds_close[i])    
plt.title('Vanguard International Bond ETFs - Close')
plt.legend(etf_intl_bonds_close.columns.tolist(), loc=2)
plt.xlim(etf_data.index[0], etf_data.index[-1])

# Intl Stocks
plt.subplot(5, 1, 4)
for i in etf_intl_stocks_close.columns.tolist():
    plt.plot(etf_intl_stocks_close[i])    
plt.title('Vanguard International Stock ETFs - Close')
plt.legend(etf_intl_stocks_close.columns.tolist(), loc=2)
plt.xlim(etf_data.index[0], etf_data.index[-1])

# Sectors
plt.subplot(5, 1, 5)
for i in etf_sectors_close.columns.tolist():
    plt.plot(etf_sectors_close[i])
plt.title('Vanguard Sector ETFs - Close')
plt.legend(etf_sectors_close.columns.tolist(), loc=2)
plt.xlim(etf_data.index[0], etf_data.index[-1])
```




    (735396.0, 737221.0)




![png](output_78_1.png)


## Save The Data

Create a list of output files


```python
output = ['etf_us_bonds.pickle', 
          'etf_us_stocks.pickle', 
          'etf_intl_bonds.pickle', 
          'etf_intl_stocks.pickle', 
          'etf_sectors.pickle']
```

Create a list of ETF data


```python
data = [etf_us_bonds_close, 
        etf_us_stocks_close,
        etf_intl_bonds_close,
        etf_intl_stocks_close,
        etf_sectors_close]
```

Pickle


```python
for i in range(len(output)):
    with open(output[i], 'wb') as f:
        pickle.dump(data[i], f)
    
    f.close()
```

## Conclusion
In this article, I showed how to scrape ETFs list from Vanguard web page, and pull historical OHLCV data from IEX.
I used Python packages Selenium and pandas-datareader for web scraping and sourcing datasets, respectively.
In the next article, I use the data to demonstrate the application of Kelly formula in investing.
