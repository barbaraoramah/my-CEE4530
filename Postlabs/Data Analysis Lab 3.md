### Lab 3 Report, Group 6
#### Ian Starnes and Barbara Oramah

####Introduction
Why did you decide to do this experiment? Introduce your approach by explaining what needs to be done to meet your goal for your real world project. Explain what you hoped to learn through this research. How did you expect this experiment to guide your decisions about the real world project that you are working on?

This is the section where you can present the equations that you will be using. Format the equations using Latex to create a beautiful report.
##### Objective

#### Procedure
Provide an overview of the methods that you used in your investigation. The best procedures give an overview of the method with an explanation of why you used those methods. There is no need to restate the step-by-step procedures as outlined in the lab manual: it is sufficient to cite the lab manual and include information on any deviations from the manual procedures. When method development is part of the laboratory exercise, a detailed description of the methods should be included. Methods and procedures need to be detailed enough so that one of your classmates could duplicate your work.
#### Results and Discussion


1. Plot the titration curve of the t=0 sample with 0.05 N HCl (plot pH as a function of titrant volume). Label the equivalent volume of titrant. Label the 2 regions of the graph where pH changes slowly with the dominant reaction that is occurring. (Place labels with the chemical reactions on the graph in the pH regions where each reaction is occurring.) Note that in a third region of slow pH change no significant reactions are occurring (added hydrogen ions contribute directly to change in pH).


```Python
from aguaclara.core.units import unit_registry as u
import aguaclara.research.environmental_processes_analysis as epa
import aguaclara.core.utility as ut
from scipy import optimize
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats

Gran_data = 'https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/Lab%203%20Data/time_equals_0_Gran_Plot.xls'
# The epa.Gran function imports data from your Gran data file as saved by ProCoDA.
# The epa.Gran function assigns all of the outputs in one statement
V_titrant, pH, V_Sample, Normality_Titrant, V_equivalent, ANC = epa.Gran(Gran_data)

# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()


# plot the data as red circles
ax.plot(V_titrant, pH, 'r')
ax.set(xlabel= 'Titrant Volume (mL)')
ax.set(ylabel= 'pH')

ax.set(title = "Measured pH vs Titrant Volume")
ax.grid(True)
plt.ylim(2.5,6.5)
plt.xlim(0.2,1.5)

plt.text(0.45, 6, '<-- bicarbonate to')
plt.text(0.55, 5.75, 'carbonic acid')
plt.text(1.1, 3.25, '<-- approaching')
plt.text(1.2, 3.05, 'input pH')
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('gran_t=0')
plt.show()

```

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/gran_t=0.png?raw=true" heights=310 width=927> </p>

**Figure 1:** Titration curve of the t=0 sample with 0.05 N HCl



$${F_1} = \frac{{{V_S} + {V_T}}}{{{V_S}}}{\text{[}}{{\text{H}}^ + }{\text{]}}$$

The ANC can be calculated from the equivalent volume from $$ANC=\frac{V_e N_t }{V_s }$$


2. Prepare a Gran plot using the data from the titration curve of the t=0 sample. Use linear regression on the linear region or simply draw a straight line through the linear region of the curve to identify the equivalent volume. Compare your calculation of Ve with that was calculated by ProCoDA.
```python
from aguaclara.core.units import unit_registry as u
import aguaclara.research.environmental_processes_analysis as epa
import aguaclara.core.utility as ut
from scipy import optimize
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats

Gran_data = 'https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/Lab%203%20Data/time_equals_0_Gran_Plot.xls'
# The epa.Gran function imports data from your Gran data file as saved by ProCoDA.
# The epa.Gran function assigns all of the outputs in one statement
V_titrant, pH, V_Sample, Normality_Titrant, V_equivalent, ANC = epa.Gran(Gran_data)

V_equivalent

#Define the gran function.
def F1(V_sample,V_titrant,pH):
  return (V_sample + V_titrant)/V_sample * epa.invpH(pH)
#Create an array of the F1 values.
F1_data = F1(V_Sample,V_titrant,pH)
#By inspection I guess that there are 4 good data points in the linear region.
N_good_points = 3
#use scipy linear regression. Note that the we can extract the last n points from an array using the notation [-N:]
slope, intercept, r_value, p_value, std_err = stats.linregress(V_titrant[-N_good_points:],F1_data[-N_good_points:])
#reattach the correct units to the slope and intercept.
intercept = intercept*u.mole/u.L
slope = slope*(u.mole/u.L)/u.mL
V_eq = -intercept/slope
ANC_sample = V_eq*Normality_Titrant/V_Sample
print('The r value for this curve fit is', ut.round_sf(r_value,5))
print('The equivalent volume was', ut.round_sf(V_eq,2))
print('The acid neutralizing capacity was',ut.round_sf(ANC_sample.to(u.meq/u.L),2))

#The equivalent volume agrees well with the value calculated by ProCoDA.
#create an array of points to draw the linear regression line
x=[V_eq.magnitude,V_titrant[-1].magnitude ]
y=[0,(V_titrant[-1]*slope+intercept).magnitude]
#Now plot the data and the linear regression
plt.plot(V_titrant, F1_data,'o')
plt.plot(x, y,'r')
plt.xlabel('Titrant Volume (mL)')
plt.ylabel('Gran function (mole/L)')
plt.legend(['data'])

plt.savefig('GranQ2.png')
plt.show()
```
The calulated Ve (0.79 mL) is approximately the same as the ProCoDa Ve (0.790561 mL).

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/GranQ2.png?raw=true" heights=310 width=927> </p>

3. Plot the measured ANC of the lake on the same graph as was used to plot the conservative, volatile, and nonvolatile ANC models (see questions 2 to 5 of the Acid Precipitation and Remediation of an Acid Lake lab). Did the measured ANC values agree with the conservative ANC model?

```python
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
ax.legend([ 'Conservative ANC','Nonvolatile ANC', 'Volatile ANC'])
ax.grid(True)
plt.ylim((-1,2))
plt.xlim(0,2)

# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('ANCplots3')
plt.show()
```
