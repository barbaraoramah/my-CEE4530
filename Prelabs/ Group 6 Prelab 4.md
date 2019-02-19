### Ian Starnes and Barbara Oramah
#### Pre-Lab 3, Group 6

1. Calculate the mass of sodium sulfite needed to reduce all the dissolved oxygen in 750 mL of pure water in equilibrium with the atmosphere and at 22∘C.

The calculated mass of sodium sulfite is 52.53 mg.
Calculations can be found in the python code below.

```Python

import aguaclara.research.environmental_processes_analysis as epa
from aguaclara.core.units import unit_registry as u
import matplotlib.pyplot as plt
import numpy as np

P_air = 101.3*u.kPa
temp = 22*u.degC
C_Oxygen = epa.O2_sat(P_air,temp)
print(C_Oxygen)

mass_O2 = 0.75 *u.L * C_Oxygen
print(mass_O2)
mass_naso3= (mass_O2 * u.mol)/(32000*u.mg)*(2)*(126000*u.mg/u.mol)
print(mass_naso3)
```


2. Describe your expectations for dissolved oxygen concentration as a function of time during a reaeration experiment. Assume you have added enough sodium sulfite to consume all of the oxygen at the start of the experiment. What would the shape of the curve look like

The rate of change of dissolved oxygen will be at its highest in the beginning of the experiment and its rate will decrease to zero as time approaches infinity.

3. Why is k̂v,l not zero when the gas flow rate is zero? How can oxygen transfer into the reactor even when no air is pumped into the diffuser?

k̂v,l is not zero when gas flow rate because oxygen is going across the liquid-atmosphere boundary at the surface of the solution.

4. Describe your expectations for k̂ v,l as a function of gas flow rate. Do you expect a straight line? Why?

The overall volumetric gas transfer coefficient, k̂v,l would increase linearly as a function of gas flow rate because the surface area of the bubbles will increase linearly with gas flow rate. This occurs at the outer surface of the bubbles is where the oxygen is transferred into the solution. However, most systems have a very low OTE (Oxygen Transfer Efficiency). We meet the condition of the water column being shallow so the simple gas transfer model is applicable.

5. A dissolved oxygen probe was placed in a small vial in such a way that the vial was sealed. The water in the vial was sterile. Over a period of several hours the dissolved oxygen concentration gradually decreased to zero. Why? (You need to know how dissolved oxygen probes work!)

The dissolved oxygen in the probe decreases because the oxygen goes through the probe's gas permeable membrane and is reduced to water at the silver cathode.
