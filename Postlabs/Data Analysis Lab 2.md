### Questions: Lab 2, Group 6
#### Ian Starnes and Barbara Oramah

$$K1=10^{−6.3} , K2=10^{−10.3}, KH=10^{−1.5\frac{mol}{L*atm}}, PCO2=10^{−3.5}atm,  and Kw=10^{−14}$$

1. Plot measured pH of the lake versus dimensionless hydraulic residence time (t/θ).
```python
from aguaclara.core.units import unit_registry as u
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats
import aguaclara.research.environmental_processes_analysis as epa
data_set = "https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/Lab%202%20-%20Acid%20Rain%20(1).txt"
from aguaclara import *

df = pd.read_csv(data_set,delimiter='\t')
print(df)

mass_total = 4.563 * u.kg
mass_bucket = 0.592* u.kg
mass_water = mass_total-mass_bucket
print(mass_water)
volume_water = (mass_water/(1*u.kg/u.L))
print (volume_water)
flow_rate = 0.074/15*u.L/u.s
print(flow_rate)
theta = volume_water/flow_rate
print(theta)

list(df)
columns = df.columns
print(columns)

# use the .loc method to call data of x values
x = df.loc[:, list(df)[0]].values * u.day
x
# use .iloc for calling y values
y = df.iloc[:,1].values


# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()

# plot the data as red circles
ax.plot(((x - (0.607747546*u.day))/theta)*1000000*u.s, y, 'ro')

#plot the linear regression as a black line


# Add axis labels using the column labels from the dataframe
ax.set(xlabel= 'Minutes')
ax.set(ylabel= 'pH')

ax.set(title = "Measured pH vs Hydraulic residence time")
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('phplot')
plt.show()

```

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/phplot.png?raw=true" heights=310 width=927> </p>

2. Assuming that the lake can be modeled as a completely mixed flow reactor and that ANC is a conservative parameter, equation (25) can be used to calculate the expected ANC in the lake effluent as the experiment proceeds. Graph the expected ANC in the lake effluent versus the hydraulic residence time (t/θ) based on the completely mixed flow reactor equation with the plot labeled (in the legend) as conservative ANC.

$$ANC_0 = [ANC_{out} - ANC_{in} (1-e^{-t/\theta})]e^{t/\theta}$$

$$ANC_{out}= \frac {ANC_0}{e^{t/\theta}} + ANC_{in} (1-e^{-t/\theta}) $$

```python
# Part 2: CMFR graph
from aguaclara.core.units import unit_registry as u
u.define("equivalent = mole = eq")
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats
data_set = "https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/Lab%202%20-%20Acid%20Rain%20(1).txt"
import aguaclara.research.environmental_processes_analysis as epa

from aguaclara import *

df = pd.read_csv(data_set,delimiter='\t')
print(df)


mass_total = 4.563 * u.kg
mass_bucket = 0.592* u.kg
mass_water = mass_total-mass_bucket
print(mass_water)
volume_water = (mass_water/(1*u.kg/u.L))
print (volume_water)
ANC_in = -0.001
pH_0 = 7.77
mass_nahco3 = 0.623 * u.g
mwt_nahco3 = 84* u.g/u.eq
conc_nahco3 = mass_nahco3/mwt_nahco3/volume_water
print(conc_nahco3)
ANC_0 = conc_nahco3
print(ANC_0)

flow_rate = 0.074/15*u.L/u.s
print(flow_rate)
theta = volume_water/flow_rate
print(theta)

t = epa.column_of_time(data_set,1,-1)
print(t)
#t - time/theta

ANC_out = epa.CMFR(theta, ANC_0, ANC_in)
print(ANC_out)


list(df)
columns = df.columns
print(columns)

# use the .loc method to call data of x values
x = df.loc[:, list(df)[0]].values * u.day
x
# use .iloc for calling y values
y = df.iloc[:,1].values


# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()

# plot the data as red circles
ax.plot(((x - (0.607747546*u.day))/theta)*1000000*u.s, y, 'ro')

#plot the linear regression as a black line


# Add axis labels using the column labels from the dataframe
ax.set(xlabel= 'Minutes')
ax.set(ylabel= 'pH')

ax.set(title = "Measured pH vs Hydraulic residence time")
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('phplot')
plt.show()

```

3. If we assume that there are no carbonates exchanged with the atmosphere during the experiment, then we can calculate ANC in the lake effluent by using equation (14) describing the ANC of a closed system. Calculate the ANC under the assumption of a closed system and plot it on the same graph produced in answering question #3 with the plot labeled (in the legend) as closed ANC.
4. If we assume that there is exchange with the atmosphere and that carbonates are at equilibrium with the atmosphere, then we can calculate ANC in the lake effluent by using equation (18) describing the ANC of an open system. Calculate the ANC under the assumption of an open system and plot it on the same graph produced in answering question #3 with the plot labeled (in the legend) as open ANC.
5. Analyze the data from the second experiment and graph the data appropriately. What did you learn from the second experiment?


##### Questions
1. What do you think would happen if enough NaHCO3 were added to the lake to maintain an ANC greater than 50μeq/L for 3 residence times with the stirrer turned off?


2. What are some of the complicating factors you might find in attempting to remediate a lake using CaCO3? Below is a list of issues to consider.
extent of mixing
solubility of CaCO3 (find the solubility and compare with NaHCO3)
density of CaCO3 slurry (find the density of CaCO3)
