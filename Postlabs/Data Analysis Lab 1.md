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
x = df.iloc[:,0].values * u.mg/u.L
x
# use the .loc method to call data of x values
x = df.loc[:, list(df)[0]].values * u.mg/u.L
x
# use .iloc for calling y values
y = df.iloc[:,1].values * u.mg/u.L

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
ax.set(xlabel=list(df)[0])
ax.set(ylabel=list(df)[1])
ax.legend(['Measured', 'Linear regression'])
ax.grid(True)
# Here I save the file to my local harddrive. You will need to change this to work on your computer.
# We don't need the file type (png) here.
plt.savefig('linear without skewed points')
plt.show()
print(intercept)
print(slope)

```
* Create a graph of absorbance vs. concentration of red dye \#40 in Atom/Markdown using the exported data file. Does absorbance increase linearly with concentration of the red dye? Remove data points from the graph that are outside of the linear region.

When the data included concentrations including the 100 mg/L and 200 mg/L the data did not increase linearly.

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/skewed%20post%20lab%201.png?raw=true" heights=310 width=927> </p>

**Figure 1**: Linear Regression plot of concentration vs. absorbance with skewed data



* What is the value of the extinction coefficient, ε?

A = εbc

where:
b - path length (1 cm)
c -  concentration
ε - extinction coefficient (fct of wavelength and molecule)

* Did you use interpolation or extrapolation to get the concentration of the unknown?
* What measurement controls the accuracy of the density measurement for the NaCl solution?
* What density did you expect (see prelab 2)?
* Approximately what should the accuracy be for the density measurement?
* Don’t forget to write a brief paragraph on conclusions and on suggestions using Markdown.
* Verify that your report and graphs meet the requirements as outlined on the course website.
