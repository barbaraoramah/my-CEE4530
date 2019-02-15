### Data Analysis and Questions: Lab 2, Group 6
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
ax.plot(((x - (0.607747546*u.day))/theta)*100000*u.s, y, 'r')

#plot the linear regression as a black line


# Add axis labels using the column labels from the dataframe
ax.set(xlabel= 'hydraulic residence time (unitless)')
ax.set(ylabel= 'pH')

ax.set(title = "Measured pH vs Hydraulic residence time")
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('phplot0')
plt.show()

```

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/phplot0.png?raw=true" heights=310 width=927> </p>

**Figure 1**

2. Assuming that the lake can be modeled as a completely mixed flow reactor and that ANC is a conservative parameter, equation (25) can be used to calculate the expected ANC in the lake effluent as the experiment proceeds. Graph the expected ANC in the lake effluent versus the hydraulic residence time (t/θ) based on the completely mixed flow reactor equation with the plot labeled (in the legend) as conservative ANC.
3. If we assume that there are no carbonates exchanged with the atmosphere during the experiment, then we can calculate ANC in the lake effluent by using equation (14) describing the ANC of a closed system. Calculate the ANC under the assumption of a closed system and plot it on the same graph produced in answering question #3 with the plot labeled (in the legend) as closed ANC.
4. If we assume that there is exchange with the atmosphere and that carbonates are at equilibrium with the atmosphere, then we can calculate ANC in the lake effluent by using equation (18) describing the ANC of an open system. Calculate the ANC under the assumption of an open system and plot it on the same graph produced in answering question #3 with the plot labeled (in the legend) as open ANC.

$$ANC_0 = [ANC_{out} - ANC_{in} (1-e^{-t/\theta})]e^{t/\theta}$$

$$ANC_{out}= \frac {ANC_0}{e^{t/\theta}} + ANC_{in} (1-e^{-t/\theta}) $$


<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/ANCplots2.png?raw=true" heights=310 width=927> </p>

**Figure 2** 

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
import math
from aguaclara import *

df = pd.read_csv(data_set,delimiter='\t')
print(df)


mass_total = 4.563 * u.kg
mass_bucket = 0.592* u.kg
mass_water = mass_total-mass_bucket
print(mass_water)
volume_water = (mass_water/(1*u.kg/u.L))
print (volume_water)
ANC_in = -0.001*u.eq/u.L
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

 # Q2
time = epa.column_of_time(data_set,1,-1)
time
# note: you cannot print a list that has units, you must call it on its own. refer to 't' variable.
res_time = (time/theta).to('dimensionless')
res_time
ANC_out = ANC_0*(np.exp(-res_time))+ANC_in*(1-(np.exp(-res_time)))
ANC_out

# Q3

lake_pH = df.iloc[:,1].values
ANC_cl=epa.ANC_closed(lake_pH,ANC_0)
ANC_cl = ANC_cl[0:1496]
ANC_cl

# Q4
ANC_o = epa.ANC_open(lake_pH)
ANC_o = ANC_o[0:1496]
# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()


ax.plot(res_time, ANC_out.to(u.meq/u.L), 'r', res_time, ANC_cl.to(u.meq/u.L), 'b', res_time, ANC_o.to(u.meq/u.L), 'g')

# Add axis labels using the column labels from the dataframe
ax.set(xlabel= 'Hydraulic residence time (unitless)')
ax.set(ylabel= 'ANC (meq/L)')
ax.set(title = "Hydraulic residence time vs Various ANC scenarios")
ax.legend([ 'Expected ANC','Closed ANC', 'Open ANC'])
ax.grid(True)
plt.ylim((-1,2))
plt.xlim(0,2)

# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('ANCplots2')
plt.show()
```



5. Analyze the data from the second experiment and graph the data appropriately. What did you learn from the second experiment?


<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/phplot1.png?raw=true" heights=310 width=927> </p>

**Figure 3:** Double ANC

We learned that by doubling the ANC added, the pH took a longer time to drop. This is because there was a higher buffer capacity, causing the lake to acidify at a slower rate.

```python
from aguaclara.core.units import unit_registry as u
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats
import aguaclara.research.environmental_processes_analysis as epa
data_set = "https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/lab2%20-%20double%20ANC.xls"
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
y

# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()


# plot the data as red circles
ax.plot(((x - (0.6350743*u.day))/theta)*100000*u.s, y, 'r')

#plot the linear regression as a black line


# Add axis labels using the column labels from the dataframe
ax.set(xlabel= 'hydraulic residence time (unitless)')
ax.set(ylabel= 'pH')

ax.set(title = "Measured pH vs Hydraulic residence time")
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('phplot1')
plt.show()
```

##### Questions
1. What do you think would happen if enough NaHCO3 were added to the lake to maintain an ANC greater than 50μeq/L for 3 residence times with the stirrer turned off?

Due to the stirrer being off, when the NaHCO3 is added, some of it will dissolve the water while some of it will sink tot eh bottom due to the greater density of NaHCO3 than water. The water in which some of the NaHCO3 dissolved in more dense than the rest of the water and it will also sink. Therefore, as the acid is added the pH will be different throughout the lake. The pH will be lower at the source of the acid, at the top of the lake, and higher farther away, at the bottom of the lake.

2. What are some of the complicating factors you might find in attempting to remediate a lake using CaCO3? Below is a list of issues to consider.
- extent of mixing solubility of CaCO3 (find the solubility and compare with NaHCO3)
- density of CaCO3 slurry (find the density of CaCO3)

The solubility of the CaCO3 is very low in water. Therefore, it might not work to just add in a lot of it at the same time. Instead if a steady amount was added during the during of the acid rain, the solubility might not be a problem. If all of the CaCO3 is added at the same time, a lot of it will not go into solution but rather settle at the bottom. The effect of this is that the ANC will not use the full potential of the CaCO3 added.
