
# Investing On ETFs Using The Kelly Formula Part 2

## How To Calculate The Kelly Formula Using Python

## Introduction
The main problem of an investor is identifying profitable trading strategies.
To solve this, he uses fundamental and quantitative techniques to pick winning trading systems.
The next problem concerning the investor is how to optimally allocate his capital to different winning strategies.
A solution is to use the Kelly formula to calculate the amount of capital he will deploy to securities or trading systems before him.

In this article, I will present the Kelly formula which is a popular system used in both sports betting and investing. 
I will use the ETFs dataset sourced in the previous article to calculate the Kelly leverages or fractions for each ETF on the list. 
I will analyze the performance metrics of each ETF group under the different leverages considered: "unconstrained", "full" and "half-Kelly".

## The Kelly Formula
The Kelly formula (or Kelly criterion) was introduced by Bell labs researcher John Kelly in his 1956 paper "A New Interpretation Of Information Rate". He proposed a betting system maximizing the expected logarithm of wealth, which is equivalent to maximizing expected compounded growth rate. John Kelly demonstrated the formula's application in horse racing where the bet size per horse is adjusted by the bettor's informed estimate of the horse's chance of winning.

The Kelly formula was popularized by mathematician, blackjack researcher and quantitative hedge fund pioneer Edward Thorp in his book "Beat the Dealer". In 1960s, he combined a card-counting system and the Kelly criterion to beat dealers in the blackjack tables of Las Vegas. Later, Edward Thorp used statistical analysis and the Kelly formula to beat other investment firms in Wall Street.

## The Kelly Formula In Investing
In this section, we follow Thorp's [paper](https://www.researchgate.net/publication/247922818_The_Kelly_Criterion_in_Blackjack_Sports_Betting_and_the_Stock_Market) presenting the Kelly formula's application in securities investing.   

### Case 1: Single Security
The objective is to maximize the growth rate $g$ of a portfolio composed of a single security
$$ \max_{f} g(f), $$

where the growth rate $g$ is
$$ g(f) = r_{Risk-free} + f m - \frac{1}{2} f^2 s^2, $$

and $f$ is the fraction of capital we allocate to the security.

Assuming that the security follows a Normal distribution with mean $m$ and variance $s^2$, the optimal allocation $f^*$ is 
$$ f^* = \frac{m}{s^2}, $$

and the optimum growth rate is 
$$ g(f^*) = r_{Risk-free} + \frac{1}{2} f^{*2} s^2. $$

### Case 2: Multiple Securities
We extend the previous case to maximize the growth rate $g$ of a portfolio composed of multiple securities
$$ \max_{F} g(F), $$

where the growth rate $g$ is
$$ g(F) = r_{Risk-free} + F^T M - \frac{1}{2} F^T C F, $$

and $F$ is a vector containing fractions of capital we allocate to each security
$$ F = \left( f_1, f_2, ..., f_n \right). $$

Assuming that each security $i$ follows a Normal distribution, $M$ is the vector containing average of excess returns of each security
$$ M = \left( m_1, m_2,..., m_n \right), $$

and $C$ is the covariance matrix of the returns between securities $i$ and $j$
$$
\begin{pmatrix} 
c_{11} & c_{12} & ... & c_{1n} \\ 
c_{21} & c_{22} & ... & c_{2n} \\ 
\vdots & \vdots & \ddots & \vdots \\ 
c_{n1} & c_{n2} & ... & c_{nn} \\ 
\end{pmatrix}
$$

the optimal allocation $F^*$ is
$$ F^* = C^{-1} M, $$

and the optimum growth rate is
$$ g(F^*) = r_{Risk-free} + \frac{1}{2} F^T C F. $$

## Step By Step
1. Calculate the Kelly leverages and performance metrics.
2. Analysis of the results.

## Calculate the Kelly Leverages And Performance Metrics
In this section, we calculate compounded growth rate and Sharpe ratio under the following:
1. "Unconstrained" Kelly leverages using the formula above.
2. "Full" Kelly fractions with the constraint of maximum leverage set to 4.
3. "Half" Kelly leverages with the same contraint as the full Kelly. 

Note that practitioners recommend using half Kelly over full Kelly to counter possible input errors.

Import packages


```python
import numpy as np
import pandas as pd
from pandas import Series, DataFrame
import pickle
import matplotlib.pyplot as plt
%matplotlib inline
```

Set the group keys


```python
groups = ['us bonds',
          'us stocks',
          'intl bonds',
          'intl stocks',
          'sectors']
```

Set the input files


```python
input = {'us bonds': 'etf_us_bonds.pickle', 
         'us stocks': 'etf_us_stocks.pickle',
         'intl bonds': 'etf_intl_bonds.pickle',
         'intl stocks': 'etf_intl_stocks.pickle',
         'sectors': 'etf_sectors.pickle'}
```

Create the output dictionary


```python
output = {'us bonds': {},
          'us stocks': {},
          'intl bonds': {},
          'intl stocks': {},
          'sectors': {}}
```

Set the following parameters:
1. Risk-free rate is assumed to be 2.5%.
2. Maximum leverage is set to 4 which is consistent with US-approved brokers.


```python
risk_free = 0.025
max_leverage = 4.00
```

Calculate the Kelly leverages for each portfolio


```python
for i in groups:    

    # Load file
    with open(input[i], 'rb') as f:
        close = pickle.load(f)
    f.close()
    
    # Daily returns for the past 6 months
    returns = close[-120:].pct_change()
    
    # Excess daily returns
    excess_returns = returns - risk_free / 250
    
    # Mean excess daily returns annualized
    M = excess_returns.mean() * 250
    
    # Covariance of daily returns
    C = returns.cov() * 250
    
    # Kelly leverage
    F = np.matmul(np.linalg.inv(C), M)
    
    # Adjust leverage
    adj_leverage = max_leverage / np.sum(np.abs(F))
    
    # Leverages for unconstrained, full and half Kelly
    Kelly = [F, 
             adj_leverage * F, 
             0.5 * adj_leverage * F] 
    
    Kelly_id = ['unconstrained',
                'full',
                'half']
    
    for k in range(len(Kelly)):
        # Growth rate 
        g = risk_free + np.matmul(np.transpose(Kelly[k]), M) - 0.5 * np.matmul(np.matmul(np.transpose(Kelly[k]), C), Kelly[k])
        # Sharpe ratio
        sharpe = np.sqrt(np.matmul(np.matmul(np.transpose(Kelly[k]), C), Kelly[k]))
        
        # Updated outputs: F, g and sharpe
        output[i][Kelly_id[k]] = Kelly[k]
        output[i][Kelly_id[k] + ' growth'] = g
        output[i][Kelly_id[k] + ' sharpe'] = sharpe
        
    # Update output
    output[i]['tickers'] = list(close.columns)
```

## Analysis Of The Results

### Vanguard US Bond ETFs

The Kelly leverage for US bond ETFs are


```python
group = 'us bonds'

pd.DataFrame({'Unconstrained': output[group]['unconstrained'],
              'Full Kelly': output[group]['full'],
              'Half Kelly': output[group]['half']},
             index=output[group]['tickers'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>EDV</th>
      <td>12.156232</td>
      <td>0.033435</td>
      <td>0.016717</td>
    </tr>
    <tr>
      <th>BIV</th>
      <td>116.324525</td>
      <td>0.319941</td>
      <td>0.159971</td>
    </tr>
    <tr>
      <th>VGIT</th>
      <td>-94.226520</td>
      <td>-0.259162</td>
      <td>-0.129581</td>
    </tr>
    <tr>
      <th>BLV</th>
      <td>46.831168</td>
      <td>0.128805</td>
      <td>0.064403</td>
    </tr>
    <tr>
      <th>VGLT</th>
      <td>-21.563687</td>
      <td>-0.059309</td>
      <td>-0.029655</td>
    </tr>
    <tr>
      <th>VMBS</th>
      <td>-63.459954</td>
      <td>-0.174542</td>
      <td>-0.087271</td>
    </tr>
    <tr>
      <th>BSV</th>
      <td>-204.894055</td>
      <td>-0.563545</td>
      <td>-0.281772</td>
    </tr>
    <tr>
      <th>VTIP</th>
      <td>-90.945714</td>
      <td>-0.250139</td>
      <td>-0.125069</td>
    </tr>
    <tr>
      <th>VGSH</th>
      <td>-85.628605</td>
      <td>-0.235515</td>
      <td>-0.117757</td>
    </tr>
    <tr>
      <th>BND</th>
      <td>-46.758134</td>
      <td>-0.128604</td>
      <td>-0.064302</td>
    </tr>
    <tr>
      <th>VCIT</th>
      <td>212.425357</td>
      <td>0.584259</td>
      <td>0.292129</td>
    </tr>
    <tr>
      <th>VCLT</th>
      <td>-71.501762</td>
      <td>-0.196660</td>
      <td>-0.098330</td>
    </tr>
    <tr>
      <th>VCSH</th>
      <td>387.607771</td>
      <td>1.066084</td>
      <td>0.533042</td>
    </tr>
  </tbody>
</table>
</div>



The growth rate and Sharpe ratio are


```python
pd.DataFrame({'Unconstrained': [output[group]['unconstrained growth'], output[group]['unconstrained sharpe']],
              'Full Kelly': [output[group]['full growth'], output[group]['full sharpe']],
              'Half Kelly': [output[group]['half growth'], output[group]['half sharpe']]},
             index=['Growth Rate', 'Sharpe Ratio'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Growth Rate</th>
      <td>17.121054</td>
      <td>0.118913</td>
      <td>0.071989</td>
    </tr>
    <tr>
      <th>Sharpe Ratio</th>
      <td>5.847402</td>
      <td>0.016083</td>
      <td>0.008041</td>
    </tr>
  </tbody>
</table>
</div>



Applying the Kelly formula to US bond ETFs yield following remarks:
1. Unconstrained Kelly proposes significant leverages e.g. 388 for VCSH and -205 for BSV.
2. Considering the max leverage of 4, the full and half Kelly produces unsatisfactory Sharpe ratios (<1).

### Vanguard US Stock ETFs

The Kelly leverage for US stock ETFs are


```python
group = 'us stocks'

pd.DataFrame({'Unconstrained': output[group]['unconstrained'],
              'Full Kelly': output[group]['full'],
              'Half Kelly': output[group]['half']},
             index=output[group]['tickers'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>VIG</th>
      <td>11.583435</td>
      <td>0.041141</td>
      <td>0.020571</td>
    </tr>
    <tr>
      <th>VUG</th>
      <td>282.321606</td>
      <td>1.002729</td>
      <td>0.501365</td>
    </tr>
    <tr>
      <th>VYM</th>
      <td>-20.176372</td>
      <td>-0.071661</td>
      <td>-0.035830</td>
    </tr>
    <tr>
      <th>VV</th>
      <td>-89.070723</td>
      <td>-0.316355</td>
      <td>-0.158177</td>
    </tr>
    <tr>
      <th>MGC</th>
      <td>45.122143</td>
      <td>0.160262</td>
      <td>0.080131</td>
    </tr>
    <tr>
      <th>MGK</th>
      <td>-215.370435</td>
      <td>-0.764937</td>
      <td>-0.382468</td>
    </tr>
    <tr>
      <th>MGV</th>
      <td>39.437699</td>
      <td>0.140072</td>
      <td>0.070036</td>
    </tr>
    <tr>
      <th>VOO</th>
      <td>-147.455077</td>
      <td>-0.523720</td>
      <td>-0.261860</td>
    </tr>
    <tr>
      <th>VTI</th>
      <td>104.591684</td>
      <td>0.371481</td>
      <td>0.185741</td>
    </tr>
    <tr>
      <th>VTV</th>
      <td>-22.634360</td>
      <td>-0.080391</td>
      <td>-0.040196</td>
    </tr>
    <tr>
      <th>VXF</th>
      <td>-0.051763</td>
      <td>-0.000184</td>
      <td>-0.000092</td>
    </tr>
    <tr>
      <th>VO</th>
      <td>64.870150</td>
      <td>0.230401</td>
      <td>0.115201</td>
    </tr>
    <tr>
      <th>VOT</th>
      <td>10.176984</td>
      <td>0.036146</td>
      <td>0.018073</td>
    </tr>
    <tr>
      <th>VOE</th>
      <td>-0.257829</td>
      <td>-0.000916</td>
      <td>-0.000458</td>
    </tr>
    <tr>
      <th>VB</th>
      <td>7.060804</td>
      <td>0.025078</td>
      <td>0.012539</td>
    </tr>
    <tr>
      <th>VBK</th>
      <td>-34.794622</td>
      <td>-0.123581</td>
      <td>-0.061790</td>
    </tr>
    <tr>
      <th>VBR</th>
      <td>-31.236998</td>
      <td>-0.110945</td>
      <td>-0.055473</td>
    </tr>
  </tbody>
</table>
</div>



The growth rate and Sharpe ratio are


```python
pd.DataFrame({'Unconstrained': [output[group]['unconstrained growth'], output[group]['unconstrained sharpe']],
              'Full Kelly': [output[group]['full growth'], output[group]['full sharpe']],
              'Half Kelly': [output[group]['half growth'], output[group]['half sharpe']]},
             index=['Growth Rate', 'Sharpe Ratio'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Growth Rate</th>
      <td>7.125607</td>
      <td>0.075349</td>
      <td>0.050197</td>
    </tr>
    <tr>
      <th>Sharpe Ratio</th>
      <td>3.768450</td>
      <td>0.013385</td>
      <td>0.006692</td>
    </tr>
  </tbody>
</table>
</div>



Applying the Kelly formula to US stock ETFs yield following remarks:
1. Unconstrained Kelly propose significant leverages e.g. 282 for VUG and -215 for MGK.
2. Considering the max leverage of 4, the full and half Kelly produces unsatisfactory Sharpe ratios (<1).

### Vanguard International Bond ETFs

The Kelly leverage for international bond ETFs are


```python
group = 'intl bonds'

pd.DataFrame({'Unconstrained': output[group]['unconstrained'],
              'Full Kelly': output[group]['full'],
              'Half Kelly': output[group]['half']},
             index=output[group]['tickers'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>BNDX</th>
      <td>210.493973</td>
      <td>2.705469</td>
      <td>1.352734</td>
    </tr>
    <tr>
      <th>VWOB</th>
      <td>100.718599</td>
      <td>1.294531</td>
      <td>0.647266</td>
    </tr>
  </tbody>
</table>
</div>



The growth rate and Sharpe ratio are


```python
pd.DataFrame({'Unconstrained': [output[group]['unconstrained growth'], output[group]['unconstrained sharpe']],
              'Full Kelly': [output[group]['full growth'], output[group]['full sharpe']],
              'Half Kelly': [output[group]['half growth'], output[group]['half sharpe']]},
             index=['Growth Rate', 'Sharpe Ratio'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Growth Rate</th>
      <td>16.462221</td>
      <td>0.444818</td>
      <td>0.235588</td>
    </tr>
    <tr>
      <th>Sharpe Ratio</th>
      <td>5.733624</td>
      <td>0.073694</td>
      <td>0.036847</td>
    </tr>
  </tbody>
</table>
</div>



Applying the Kelly formula to international bond ETFs yields the following remarks:
1. Unconstrained Kelly propose significant leverages e.g. 210 for BNDX and 101 for VWOB.
2. Considering the max leverage of 4, the full and half Kelly produces unsatisfactory Sharpe ratios (<1).

### Vanguard International Stock ETFs

The Kelly leverage for international stock ETFs are


```python
group = 'intl stocks'

pd.DataFrame({'Unconstrained': output[group]['unconstrained'],
              'Full Kelly': output[group]['full'],
              'Half Kelly': output[group]['half']},
             index=output[group]['tickers'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>VT</th>
      <td>11.873599</td>
      <td>0.173985</td>
      <td>0.086992</td>
    </tr>
    <tr>
      <th>VEU</th>
      <td>56.446795</td>
      <td>0.827119</td>
      <td>0.413560</td>
    </tr>
    <tr>
      <th>VSS</th>
      <td>-6.710062</td>
      <td>-0.098323</td>
      <td>-0.049162</td>
    </tr>
    <tr>
      <th>VEA</th>
      <td>47.170226</td>
      <td>0.691189</td>
      <td>0.345595</td>
    </tr>
    <tr>
      <th>VGK</th>
      <td>-10.031345</td>
      <td>-0.146990</td>
      <td>-0.073495</td>
    </tr>
    <tr>
      <th>VPL</th>
      <td>-52.156336</td>
      <td>-0.764251</td>
      <td>-0.382125</td>
    </tr>
    <tr>
      <th>VNQI</th>
      <td>26.673947</td>
      <td>0.390855</td>
      <td>0.195428</td>
    </tr>
    <tr>
      <th>VXUS</th>
      <td>-59.953430</td>
      <td>-0.878502</td>
      <td>-0.439251</td>
    </tr>
    <tr>
      <th>VWO</th>
      <td>1.964445</td>
      <td>0.028785</td>
      <td>0.014393</td>
    </tr>
  </tbody>
</table>
</div>



The growth rate and Sharpe ratio are


```python
pd.DataFrame({'Unconstrained': [output[group]['unconstrained growth'], output[group]['unconstrained sharpe']],
              'Full Kelly': [output[group]['full growth'], output[group]['full sharpe']],
              'Half Kelly': [output[group]['half growth'], output[group]['half sharpe']]},
             index=['Growth Rate', 'Sharpe Ratio'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Growth Rate</th>
      <td>3.502137</td>
      <td>0.126155</td>
      <td>0.075764</td>
    </tr>
    <tr>
      <th>Sharpe Ratio</th>
      <td>2.637096</td>
      <td>0.038642</td>
      <td>0.019321</td>
    </tr>
  </tbody>
</table>
</div>



Applying the Kelly formula to international stock ETFs yields the following remarks:
1. Unconstrained Kelly propose relatively smaller leverages (compared to US bond and US stock ETFs) and Sharpe ratio of 2.64.
2. Considering the max leverage of 4, the full and half Kelly produces unsatisfactory Sharpe ratios (<1).

### Vanguard Sector ETFs

The Kelly leverage for sector ETFs are


```python
group = 'sectors'

pd.DataFrame({'Unconstrained': output[group]['unconstrained'],
              'Full Kelly': output[group]['full'],
              'Half Kelly': output[group]['half']},
             index=output[group]['tickers'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>VOX</th>
      <td>1.219117</td>
      <td>0.050629</td>
      <td>0.025314</td>
    </tr>
    <tr>
      <th>VCR</th>
      <td>-8.332042</td>
      <td>-0.346022</td>
      <td>-0.173011</td>
    </tr>
    <tr>
      <th>VDC</th>
      <td>-2.505247</td>
      <td>-0.104041</td>
      <td>-0.052020</td>
    </tr>
    <tr>
      <th>VDE</th>
      <td>-11.760600</td>
      <td>-0.488407</td>
      <td>-0.244203</td>
    </tr>
    <tr>
      <th>VFH</th>
      <td>17.209040</td>
      <td>0.714675</td>
      <td>0.357338</td>
    </tr>
    <tr>
      <th>VHT</th>
      <td>-19.652473</td>
      <td>-0.816149</td>
      <td>-0.408074</td>
    </tr>
    <tr>
      <th>VIS</th>
      <td>3.842607</td>
      <td>0.159580</td>
      <td>0.079790</td>
    </tr>
    <tr>
      <th>VGT</th>
      <td>14.855284</td>
      <td>0.616926</td>
      <td>0.308463</td>
    </tr>
    <tr>
      <th>VAW</th>
      <td>0.396723</td>
      <td>0.016476</td>
      <td>0.008238</td>
    </tr>
    <tr>
      <th>VNQ</th>
      <td>12.386444</td>
      <td>0.514397</td>
      <td>0.257199</td>
    </tr>
    <tr>
      <th>VPU</th>
      <td>4.158522</td>
      <td>0.172700</td>
      <td>0.086350</td>
    </tr>
  </tbody>
</table>
</div>



The growth rate and Sharpe ratio are


```python
pd.DataFrame({'Unconstrained': [output[group]['unconstrained growth'], output[group]['unconstrained sharpe']],
              'Full Kelly': [output[group]['full growth'], output[group]['full sharpe']],
              'Half Kelly': [output[group]['half growth'], output[group]['half sharpe']]},
             index=['Growth Rate', 'Sharpe Ratio'])
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
      <th>Unconstrained</th>
      <th>Full Kelly</th>
      <th>Half Kelly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Growth Rate</th>
      <td>6.271559</td>
      <td>0.533054</td>
      <td>0.281720</td>
    </tr>
    <tr>
      <th>Sharpe Ratio</th>
      <td>3.534560</td>
      <td>0.146787</td>
      <td>0.073393</td>
    </tr>
  </tbody>
</table>
</div>



Applying the Kelly formula to sector ETFs yields the following remarks:
1. Unconstrained Kelly propose relatively small leverages (compared to US bond and US stock ETFs) and Sharpe ratio of 3.53.
2. Considering the max leverage of 4, the full and half Kelly produces unsatisfactory Sharpe ratios (<1).

## Conclusion
In this article, I reviewed the Kelly formula which is a solution to the investor's second problem.
I demonstrated how we can use the Kelly criterion to calculate ETF fractions per Vanguard fund group. 
The unconstrained Kelly generates impressive performance but requires significant amounts of leverage.
The full and half Kelly suggest reasonable levels of leverage but produces unsatisfactory performance.
