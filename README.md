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
| Acetate | 0 / -94.4 (rxn05488)  | 0 / 94.4 (rxn05488)  | 
| Fe2+ |  0 / -29.0 (rxn37614_2)  | 0 / 23.2 (FERCYT) | 
| Fe3+  |  0 / 29.0 (rxn37614_2)  | 0 / -23.2 (FERCYT) |
| Citrate  |  -0.0020 / 52.7 (rxn05211)  |  0.0029 / -52.7 (rxn05211)  |
| CO2 |  0.016 / -128.3 (rxn05467) |  -0.018 / 88.23 (rxn05467)  |
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
# Uncomment one of the below lines to set media bounds
# model.medium = mediumupper
# model.medium = mediumlower
sol = model.optimize()


def add_dynamic_bounds(model, y):
   """Use external concentrations to bound the uptake flux of N2"""
   biomass, N2 = y  # expand the boundary species
   N2_max_import = -13 * N2/(6.5 + N2) ### MONOD EQUATION
   model.reactions.EX_cpd10515_e0.lower_bound = N2_max_import

def dynamic_system(t, y):
   """Calculate the time derivative of external species."""

   biomass, N2 = y  # expand the boundary species

   # Calculate the specific exchanges fluxes at the given external concentration
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

ts = np.linspace(0, 1, 100)  # Desired integration resolution and interval
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
# Set N2 
model.reactions.get_by_id("EX_cpd00528_e0").lower_bound = -1
model.reactions.get_by_id("EX_cpd00528_e0").upper_bound = 1

# Set model objective for max prediction
model.objective = {model.reactions.rxn01466_c0: 1}

sol = model.optimize()
#print("farnesyl diphosphate precurosor")
print(sol)

# prints
# <Solution 0.001 at 0x15cdf24ffd0>
```

## Diurnal community model
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_2.xml") # Important to use diurnal model here

mediumhigh = {
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
    'EX_cpd00034_e0': 0.0001
}
model.reactions.get_by_id("EX_photon_e0").lower_bound = -100
model.reactions.get_by_id("EX_photon_e0").upper_bound = 100

sol = model.optimize()

# print overall community specific growth rate 
print(sol)
# prints
# <Solution 2.062 at 0x1c0f3df2e00>

# print growth rate of just R. palustris in community
print(model.reactions.get_by_id("bio11_biomass").summary(solution = sol))
# prints
# bio11_biomass
#=============
#35.5403092430435 cpd00001_c0 + 40.1101757365074 cpd00002_c0 + 0.00309646685192537 cpd00003_c0 + 0.00309646685192537 cpd00006_c0 + 0.00309646685192537 cpd00010_c0 + 
#0.00309646685192537 cpd00015_c0 + 0.00309646685192537 cpd00016_c0 + 0.00309646685192537 cpd00017_c0 + 0.00309646685192537 cpd00021_c0 + 0.219088153012743 cpd00023_c0 
#+ 0.00309646685192537 cpd00028_c0 + 0.00309646685192537 cpd00030_c0 + 0.509869786991038 cpd00033_c0 + 0.00309646685192537 cpd00034_c0 + 0.427934380173264 cpd00035_c0 
#+ 0.135406821203723 cpd00038_c0 + 0.285438020490179 cpd00039_c0 + 0.200830806928348 cpd00041_c0 + 0.00309646685192537 cpd00042_c0 + 0.00309646685192537 cpd00048_c0 + 
#0.246696822701341 cpd00051_c0 + 0.0841036156544863 cpd00052_c0 + 0.219088153012743 cpd00053_c0 + 0.179456352975885 cpd00054_c0 + 0.00309646685192537 cpd00056_c0 + 
#0.00309646685192537 cpd00058_c0 + 0.127801422590767 cpd00060_c0 + 0.0908319049068452 cpd00062_c0 + 0.00309646685192537 cpd00063_c0 + 0.0472019191450218 cpd00065_c0 + 
#0.154519490031345 cpd00066_c0 + 0.120676604606612 cpd00069_c0 + 0.0761464922056484 cpd00084_c0 + 0.00309646685192537 cpd00087_c0 + 0.00309646685192537 cpd00099_c0 + 
#0.375388847540127 cpd00107_c0 + 0.0112027733796597 cpd00115_c0 + 0.00309646685192537 cpd00118_c0 + 0.0792636000737159 cpd00119_c0 + 0.184354665339991 cpd00129_c0 + 
#0.200830806928348 cpd00132_c0 + 0.00309646685192537 cpd00149_c0 + 0.352233189091625 cpd00156_c0 + 0.211072732780569 cpd00161_c0 + 0.00309646685192537 cpd00166_c0 + 
#0.00309646685192537 cpd00201_c0 + 0.00309646685192537 cpd00205_c0 + 0.00309646685192537 cpd00220_c0 + 0.0208335931217656 cpd00241_c0 + 0.00309646685192537 cpd00254_c0 
#+ 0.00309646685192537 cpd00264_c0 + 0.241798510337235 cpd00322_c0 + 0.00309646685192537 cpd00345_c0 + 0.0208335931217656 cpd00356_c0 + 0.0112027733796597 cpd00357_c0 
#+ 0.00309646685192537 cpd00557_c0 + 0.0250105977108944 cpd02229_c0 + 0.00309646685192537 cpd10516_c0 + 0.00309646685192537 cpd11493_c0 + 0.00309646685192537 
#cpd15352_c0 + 0.0250105977108944 cpd15432_c0 + 0.00309646685192537 cpd15500_c0 + 0.0106480421341882 cpd15533_c0 + 0.0106480421341882 cpd15540_c0 + 0.00309646685192537 
#cpd15560_c0 + 0.0250105977108944 cpd15665_c0 + 0.0106480421341882 cpd15695_c0 + 0.0106480421341882 cpd15696_c0 + 0.0106480421341882 cpd15722_c0 + 0.0106480421341882 
#cpd15723_c0 + cpd17041_c0 + cpd17042_c0 + cpd17043_c0 --> 40.0 cpd00008_c0 + 39.9969035331481 cpd00009_c0 + 0.484590811275282 cpd00012_c0 + 40.0 cpd00067_c0 + 
#0.00309646685192537 cpd01997_c0 + 0.00309646685192537 cpd03422_c0 + cpd11416_c0 + 0.00309646685192537 cpd12370_c0 + 0.0250105977108944 cpd15666_c0
#Bounds: 0.0, 100000.0
#Flux: 0.06459

# print growth rate of just G. sulfurreducins in community
print(model.reactions.get_by_id("bio1_biomass").summary(solution = sol))

# prints
#bio1_biomass
#============
#13.07392627 cpd00001_c11 + 18.218804 cpd00002_c11 + 0.0177014 cpd00003_c11 + 0.056687258 cpd00005_c11 + 0.28381802 cpd00012_c11 + 0.17933863 cpd00023_c11 + 
#0.021845986 cpd00024_c11 + 0.011800933 cpd00026_c11 + 0.417500331 cpd00033_c11 + 0.372353 cpd00035_c11 + 0.034085056 cpd00037_c11 + 0.064652564 cpd00038_c11 + 
#0.233857574 cpd00039_c11 + 0.164274 cpd00041_c11 + 0.20157662 cpd00051_c11 + 0.040129178 cpd00052_c11 + 0.17933863 cpd00053_c11 + 0.471859373 cpd00054_c11 + 
#0.10473376 cpd00060_c11 + 0.0177014 cpd00061_c11 + 0.043314033 cpd00062_c11 + 0.038737144 cpd00065_c11 + 0.126254396 cpd00066_c11 + 0.093973442 cpd00069_c11 + 
#0.026552099 cpd00079_c11 + 0.062409843 cpd00084_c11 + 0.191964286 cpd00102_c11 + 0.307027735 cpd00107_c11 + 0.020714 cpd00115_c11 + 0.022284123 cpd00117_c11 + 
#0.064561907 cpd00119_c11 + 0.150644449 cpd00129_c11 + 0.164274185 cpd00132_c11 + 0.925925926 cpd00155_c11 + 0.288376518 cpd00156_c11 + 0.17288244 cpd00161_c11 + 
#0.0177014 cpd00169_c11 + 0.022284123 cpd00186_c11 + 0.221964448 cpd00205_c11 + 0.225997835 cpd00214_c11 + 0.020714656 cpd00241_c11 + 0.031977929 cpd00254_c11 + 
#0.197989848 cpd00322_c11 + 0.020714656 cpd00356_c11 + 0.020714656 cpd00357_c11 + 0.022284123 cpd00516_c11 + 0.022284123 cpd00773_c11 + 0.003862304 cpd01080_c11 + 
#0.021391222 cpd03847_c11 + 0.001307 cpd11430_c11 + 0.062391063 cpd11436_c11 + 0.002733323 cpd11438_c11 + 0.276897481 cpd15237_c11 + 0.023114403 cpd15269_c11 + 
#0.011170971 cpd15601_c11 + 0.061393371 cpd19013_c11 + 0.0177014 cpd19028_c11 + 0.000940527 cpd29674_c11 --> 0.0177014 cpd00004_c11 + 0.056687 cpd00006_c11 + 18.166254 
#cpd00008_c11 + 18.214415 cpd00009_c11 + 18.20524 cpd00067_c11
#Bounds: 0.0, 100000.0
#Flux: 1.998


```
### The code below will allow you to save a lists of all metabolites and reactions to examine transfer between light and dark (or vice versa)

### The method in which these data were compiled is archived within the Diurnal_community_model_results folder on Github (Diurnal_fluxes_all_metabolites_and_transfer_reactions.xlsx)
```
from pandas import Series
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_2.xml") # Important to use diurnal model here

mediumhigh = {
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
    'EX_cpd00034_e0': 0.0001
}

sol = model.optimize()

#### The for loop below will save a list of all reactions, reaction IDs,
#### and their fluxes to a csv. There you can find transfer reaction fluxes.
a = []
for reaction in model.reactions[:]:
    model.medium = mediumhigh
    model.reactions.get_by_id("EX_photon_e0").lower_bound = -100
    model.reactions.get_by_id("EX_photon_e0").upper_bound = 100
    model.optimize()
    rxn = (reaction.id, reaction.flux, reaction)
    a.append(rxn)
df = pandas.DataFrame(a)

## File is archived as reactions_GALE_DIURNAL_2023 on GitHub under Diurnal_community_model_results
df.to_csv(r"C:\\where\\you\\wish\\to\\save\\\\all_reactions.csv", index=False)

#### The for loop below will save a csv of all metabolites and their fluxes in light or dark
#### c0 is R. pal in light
#### c00 is R. pal in dark
#### c1 is G. sulf in light
#### c11 is G. sulf in dark

def metabolite_flux_balance(metabolite, solution):
    rxn_ids = list()
    adj_flux = list()
    for rxn in metabolite.reactions:
        coeff = rxn.get_coefficient(metabolite)
        rxn_ids.append(rxn.id)
        adj_flux.append(coeff * solution.fluxes[rxn.id])
    return Series(data=adj_flux, index=rxn_ids, dtype=float, name="reaction")
    
a = []
for met in model.metabolites:
    sol = metabolite_flux_balance(met, solution)
    flux = sol[sol > 0.0].sum()
    met = (met.id, flux)
    a.append(met)
df = pandas.DataFrame(a)

## File is archived as metabolites_GALE_DIURNAL_2023 on GitHub under Diurnal_community_model_results
df.to_csv(r"C:\\where\\you\\wish\\to\\save\\all_metabolites.csv", index=False)

```
