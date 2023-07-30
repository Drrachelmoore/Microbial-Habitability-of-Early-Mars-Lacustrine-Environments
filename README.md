# Companion repository for the manuscript entitled _Microbial habitability of early Mars lacustrine environments sustained by iron redox cycling_

The purpose of this repository is to allow for the reproduction of our results utilizing the community genome-scale models.

## Requirements
* [Python](https://www.python.org/downloads/) (version 3.10 was used in the manuscript + the [PyCharm 2021.2.3 IDE](https://www.jetbrains.com/pycharm/))
* [COBRApy](https://github.com/opencobra/cobrapy) (version 0.25.0 was used in the manuscript)
* [Gurobi Optimizer](http://www.gurobi.com/registration/download-reg) for any simulations
* The model(s)


## General reading and simulating a model
The models provided in the manuscript both end in `.xml` and are in Systems Biology Markup Language ([SBML](https://sbml.org/)) format. 

**To read in the model and run a basic simulation:**
```
# Note for all simulations:
# c1 is the cytoplasm of G. sulfurreducens 
# c0 is the cytoplasm of R. palustris 
# e0 is the external environment

import cobra

model = cobra.io.read_sbml_model("path\\to\\model.xml")
solution = model.optimize()

# Will print only the solution to the objective function
print(solution)

# Will print more of a summary including the solution
print(model.summary(solution = solution))
```
## Investigating the reverse Krebs cycle in _G. sulfurreducens_
### The following code will allow you to simulate the growth of the community model on upper bound media. It will print out the production and consumption reactions for all of the key metabolites involved in the Krebs cycle for _G. sulfurreducens_.
```
import cobra
model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")
sol = model.optimize()

print("oxaloacetate")
print(model.metabolites.get_by_id("cpd00032_c1").summary(solution = sol))

print("malate")
print(model.metabolites.get_by_id("cpd00130_c1").summary(solution = sol))

print("fumarate")
print(model.metabolites.get_by_id("cpd00106_c1").summary(solution = sol))
# Note that reaction FRD5 is a synonym for rxn10619

print("succinate")
print(model.metabolites.get_by_id("cpd00036_c1").summary(solution = sol))

print("succinyl-coa")
print(model.metabolites.get_by_id("cpd00078_c1").summary(solution = sol))

print("acetyl-coa")
print(model.metabolites.get_by_id("cpd00022_c1").summary(solution = sol))

print("pyruvate")
print(model.metabolites.get_by_id("cpd00020_c1").summary(solution = sol))

print("Acetate")
print(model.metabolites.get_by_id("cpd00029_c1").summary(solution = sol))

print("2-oxoglutarate")
print(model.metabolites.get_by_id("cpd00024_c1").summary(solution = sol))

print("isocitrate")
print(model.metabolites.get_by_id("cpd00260_c1").summary(solution = sol))
```

## Table 2 from the manuscript
Table 2. Specific growth rates of the genome-based models under the specified conditions and constrained to either the low or high bounds of medium metabolite flux.
|Electron donor/acceptors | Input carbon source |Light?  | Model | Specific growth Rate (h-1) |
| ------------- | ------------- |------------- | ------------- |------------- |
| H2 / Fe | CO2  | NA  | _G. sulfurreducens_  | 0.001–0.005  |
| Fe / CO2 |  CO2  | - | _R. palustris_ | 0.00–0.00 |
| Fe / CO2  |  CO2  | +  | _R. palustris_  | 0.001–0.032  |
| H2, Fe / Fe, CO2  |  CO2  | +  | Community  | 0.001–1.031  |
| H2, Fe / Fe, CO2 |  CO2 | - | Community | 0.00–1.012 |

### To generate the community model growth results in Table 2:
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")

# Set media to upper bounds of Gale Lake with light
mediumupper = {
    'EX_cpd29674_e0': 45,
    'EX_cpd00063_e0': 45,
    'EX_cpd00099_e0': 250,
    'EX_cpd00149_e0': 0.0059,
    'EX_cpd00011_e0': 41,
    'EX_cpd00058_e0': 0.0001,
    'EX_cpd10515_e0': 5.8,
    'EX_cpd00067_e0': 0.001,
    'EX_cpd11640_e0': 12,
    'EX_cpd00001_e0': 1000,
    'EX_cpd00205_e0': 4.4,
    'EX_cpd00254_e0': 60,
    'EX_cpd00030_e0': 0.01,
    'EX_cpd00528_e0': 13,
    'EX_cpd00971_e0': 120,
    'EX_cpd00009_e0': 1.8,
    'EX_cpd00048_e0': 72,
    'EX_cpd00034_e0': 0.0001,
    'EX_photon_e': 100.0,
    'EX_cpd00023_e0: 0.1
}


model.medium = mediumupper
sol = model.optimize()
print(sol)
# prints <Solution 1.031 at 0x2106b888f10>
# 1.031 h^-1

# Set media to lower bounds of Gale Lake with light
mediumlower = {
    'EX_cpd29674_e0': 24,
    'EX_cpd00063_e0': 24,
    'EX_cpd00099_e0': 110,
    'EX_cpd00149_e0': 0.001,
    'EX_cpd00011_e0': 2.3,
    'EX_cpd00058_e0': 0.0001,
    'EX_cpd10515_e0': 0.12,
    'EX_cpd00067_e0': 0.001,
    'EX_cpd11640_e0': 12,
    'EX_cpd00001_e0': 1000,
    'EX_cpd00205_e0': 1.4,
    'EX_cpd00254_e0': 35,
    'EX_cpd00030_e0': 0.00053,
    'EX_cpd00528_e0': 13,
    'EX_cpd00971_e0': 94,
    'EX_cpd00009_e0': 0.00052,
    'EX_cpd00048_e0': 44,
    'EX_cpd00034_e0': 0.0001,
    'EX_photon_e': 100.0,
    'EX_cpd00023_e0: 0.1
}
model.medium = mediumlower
sol = model.optimize()
print(sol)
# prints <Solution 0.001 at 0x17fd603ce80>
# 0.001 h^-1


# Now simulate without light
#First, with upper bounds

mediumupper["EX_photon_e"] = 0.0
model.medium = mediumupper
sol = model.optimize()
print(sol)

# Then, with lower bounds
mediumlower["EX_photon_e"] = 0.0
model.medium = mediumlower
sol = model.optimize()
print(sol)

# prints 
#<Solution 1.012 at 0x1da9a099780>
#<Solution 0.000 at 0x1da99d90ca0>
```
## Table 3 from the manuscript
Table 3. Modeled interactions between community members. 
|Metabolite| _R. palustris_ (reaction ID) |_G. sulfurreducens_ (reaction ID) | 
| ------------- | ------------- |------------- | 
| Acetate | -6.83 / -144.4 (rxn05488)  | 6.83 / 144.4 (rxn05488)  | 
| Fe2+ |  -6.99/ -0.052 (rxn37614_2)  | 6.99 / 0.052 (FERCYT) | 
| Fe3+  |  6.99 / 0.052 (rxn37614_2)  | 6.99 / -0.052 (FERCYT) |
| Citrate  |  -2.81 / 63.88 (rxn05211)  |  2.81 / -63.88 (rxn05211)  |
| CO2 |  -3.21 / -95.74 (rxn05467) |  0.91 / 55.21 (rxn05467)  |
* Fluxes are shown in mmol gDW-1 h-1 for low / high bounds of media inputs with light. Negative values indicate metabolite consumption, whereas positive values indicate production.


### To reproduce the modeled interactions between community members in Table 3:
```
# Follow the same instructions above to read in model and media bounds

model.medium = mediumupper #setting media to upper bounds
sol = model.optimize()

#print(sol)
#print(model.summary(solution = sol))
print("Acetate upper")
#print(model.metabolites.get_by_id("cpd00029_e0").summary(solution = sol))
print("Fe2+  upper")
print(model.metabolites.get_by_id("cpd10515_e0").summary(solution = sol))
print("Fe3+  upper")
print(model.metabolites.get_by_id("cpd10516_e0").summary(solution = sol))
print("Citrate  upper")
print(model.metabolites.get_by_id("cpd00137_e0").summary(solution = sol))
print("CO2  upper")
print(model.metabolites.get_by_id("cpd00011_e0").summary(solution = sol))

model.medium = mediumlower #setting media to lower bounds
sol = model.optimize()

#print(sol)
#print(model.summary(solution = sol))
print("Acetate lower")
#print(model.metabolites.get_by_id("cpd00029_e0").summary(solution = sol))
print("Fe2+ lower")
print(model.metabolites.get_by_id("cpd10515_e0").summary(solution = sol))
print("Fe3+ lower")
print(model.metabolites.get_by_id("cpd10516_e0").summary(solution = sol))
print("Citrate lower")
print(model.metabolites.get_by_id("cpd00137_e0").summary(solution = sol))
print("CO2 lower")
print(model.metabolites.get_by_id("cpd00011_e0").summary(solution = sol))

```

## Dynamic flux balance analysis 
```
import numpy as np
from scipy.integrate import solve_ivp
import matplotlib.pyplot as plt
from tqdm import tqdm
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")
#Set media to upper or lower as above

def add_dynamic_bounds(model3, y):
   """Use external concentrations to bound the uptake flux of N2"""
   biomass, Fe = y  # expand the boundary species
   N2_max_import = -0.2 * Fe/(0.1 + Fe) ### MONOD EQUATION
   model3.reactions.EX_cpd10515_e0.lower_bound = N2_max_import

def dynamic_system(t, y):
   """Calculate the time derivative of external species."""

   biomass, Fe = y  # expand the boundary species

   # Calculate the specific exchanges fluxes at the given external concen
   with model:
       add_dynamic_bounds(model, y)

       cobra.util.add_lp_feasibility(model)
       feasibility = cobra.util.fix_objective_as_constraint(model)
       lex_constraints = cobra.util.add_lexicographic_constraints(
           model, ['bio1_biomass', 'EX_cpd00528_e0'], ['max', 'max'])

   # Since the calculated fluxes are specific rates, we multiply them by
   # biomass concentration to get the bulk exchange rates.
   fluxes = lex_constraints.values
   fluxes *= biomass

   # This implementation is **not** efficient, so I display the current
   # simulation time using a progress bar.
   if dynamic_system.pbar is not None:
       dynamic_system.pbar.update(1)
       dynamic_system.pbar.set_description('t = {:.3f}'.format(t))

   return fluxes

dynamic_system.pbar = None


def infeasible_event(t, y):
   with model:

       add_dynamic_bounds(model, y)

       cobra.util.add_lp_feasibility(model)
       feasibility = cobra.util.fix_objective_as_constraint(model)

   return feasibility - infeasible_event.epsilon

infeasible_event.epsilon = 1E-6
infeasible_event.direction = 1
infeasible_event.terminal = True

ts = np.linspace(0, 1, 100)  # Desired integration resolution and interva
y0 = [0.1, 13]

with tqdm() as pbar:
   dynamic_system.pbar = pbar
   sol = solve_ivp(
       fun=dynamic_system,
       events=[infeasible_event],
       t_span=(ts.min(), ts.max()),
       y0=y0,
       t_eval=ts,
       rtol=1e-6,
       atol=1e-8,
       method='BDF'
   )
print(sol)

ax = plt.subplot(111)
ax.plot(sol.t, sol.y.T[:, 0], 'k-')
ax2 = plt.twinx(ax)
ax2.plot(sol.t, sol.y.T[:, 1], 'r--')
plt.show()
```

## Lipid biomarker production
```

```

## Diurnal community model
```

```
