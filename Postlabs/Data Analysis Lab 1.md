### Data Analysis and Questions: Lab 1, Group 6
#### Ian Starnes and Barbara Oramah


```python

from aguaclara.core.units import unit_registry as u
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats
data_set = "https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/data%20lab%201.tsv"

df = pd.read_csv(data_set,delimiter='\t')
print(df)

list(df)
columns = df.columns
print(columns)


x = df[list(df)[0]].values * u.mg/u.L
x
# 3) We can use the iloc command and select all of the rows in column 0.
y = df.iloc[:,0].values * u.mg/u.L
y
# use the .loc method to call data of x values
y = df.loc[:, list(df)[0]].values * u.mg/u.L
y
# use .iloc for calling y values
x = df.iloc[:,1].values * u.mg/u.L

slope, intercept, r_value, p_value, std_err = stats.linregress(x,y)

#We can add the units to intercept by giving it the same units as the y values.
intercept = intercept * y.units
# Note that slope is dimensionless for this case, but not in general!
# For the general case we can attach the correct units to slope.
slope = slope * y.units/x.units

# Now create a figure and plot the data and the line from the linear regression.
fig, ax = plt.subplots()

# plot the data as red circles
ax.plot(x, y, 'ro', )

#plot the linear regression as a black line
ax.plot(x, slope * x + intercept, 'k-', )

# Add axis labels using the column labels from the dataframe
ax.set(xlabel=list(df)[1])
ax.set(ylabel=list(df)[0])
ax.legend(['Measured', 'Linear regression'])
ax.set(title = "Concentration vs. absorbance with skewed data")
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('skewed pts')
plt.show()
print(intercept)
print(slope)
```



* Create a graph of absorbance vs. concentration of red dye \#40 in Atom/Markdown using the exported data file. Does absorbance increase linearly with concentration of the red dye? Remove data points from the graph that are outside of the linear region.

When the data included concentrations including the 100 mg/L and 200 mg/L the data did not increase linearly.

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/skewed%20pts.png?raw=true" heights=310 width=927> </p>

**Figure 1**: Linear Regression plot of concentration vs. absorbance with skewed data

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/linear%20without%20skewed%20pts.png?raw=true" heights=310 width=927> </p>

**Figure 2**: Linear Regression plot of concentration vs. absorbance without skewed data

$$y = 0.08374 \frac{mg}{L} + 0.04643x$$

* What is the value of the extinction coefficient, ε?

$$ A = εbc $$

where:
b - path length  19 mm
The photometer is a flow cell with an optical path length of 19 mm.
c - concentration
ε - extinction coefficient (fctn of wavelength and molecule)

In order to find ε, use the linear regression formula. We can neglect the intercept.

A = y
m = εb
c = x

$$ m = εb $$
$$ ε = \frac{m}{b} $$
$$ε = \frac{0.04643}{(0.019 m)}$$
$$ε = 2.44 \frac{m^2}{mg}$$


slope =  m3/mg
absorbance g/m^2

The extinction coefficient was determined by solving ε for each concentration. We then assumed that ε was the highest ε we obtained.

* Did you use interpolation or extrapolation to get the concentration of the unknown?

We used interpolation to find the concentration of the unknown as the voltage

* What measurement controls the accuracy of the density measurement for the NaCl solution?

The volumetric flask was the controlling factor of our accuracy with an accuracy of 0.16 mL/ 100 mL (0.16%). The percent error in the mass error of NaCl is lower at 0.017%.

* What density did you expect (see prelab 1)?

Based on our pre-lab, the calculated value of NaCl was 1037.8 g/L.

* Approximately what should the accuracy be for the density measurement?

We would expect our experimental density to be within the 0.16% accuracy.

* Don’t forget to write a brief paragraph on conclusions and on suggestions using Markdown.

In this lab we learned how to use several instruments that we will continue to use for the rest of the labs. This included how to calibrate and use the electronic balances. We also used the proper technique for the pipette to measure the masses of reverse osmosis water. We diluted stock solution into various designated concentrations of red dye. Finally we learned how to calibrate and use the photometer in order to interpolate the unknown concentration in a dilute sample of red dye.

We don't have any suggestions on improving Markdown.

* Verify that your report and graphs meet the requirements as outlined on the course website.
