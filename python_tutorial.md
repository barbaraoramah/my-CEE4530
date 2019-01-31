# Python Tutorial for CEE 4530 - Barbara Oramah

# Assignment

1) Find a set of data that includes units (or make one up!) that could reasonably be fit with linear regression.
2) Save the data to a tab delimited file in your atom project workspace. --> save it as tsv
3) Load the data from the file into a Pandas dataframe.
4) Plot the data and the linear regression line.
5) Make sure to handle units carefully and to attach units to the linear regression line.
6) Add a figure in Markdown showing the graph you produced.
7) Show the linear regression equation that you obtained using latex.


```python

from aguaclara.core.units import unit_registry as u
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from scipy import stats
data_set = "https://raw.githubusercontent.com/barbaraoramah/my-CEE4530/master/dataset1.tsv"

df = pd.read_csv(data_set,delimiter='\t')
print(df)

list(df)
columns = df.columns
print(columns)

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
plt.savefig('linear')
plt.show()
print(intercept)
print(slope)

```

<p align="center"> <img src="https://github.com/barbaraoramah/my-CEE4530/blob/master/images/linear.png?raw=true" heights=310 width=927> </p>

**Figure 1**: Linear Regression plot of concentration vs. voltage

$$y=1.719 \frac{mg}{L}-0.07402 \times x$$
