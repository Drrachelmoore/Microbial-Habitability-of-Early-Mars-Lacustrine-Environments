# Companion repository for the manuscript entitled _Microbial habitability of early Mars lacustrine environments sustained by iron redox cycling_

The purpose of this repository is to allow for the reproduction of our results utilizing the community genome-scale models.

## Requirements
* [Python](https://www.python.org/downloads/) (version 3.10 was used in the manuscript + the [PyCharm 2021.2.3 IDE](https://www.jetbrains.com/pycharm/))
* [COBRApy](https://github.com/opencobra/cobrapy) (version 0.25.0 was used in the manuscript)
* [Gurobi Optimizer](http://www.gurobi.com/registration/download-reg) for any simulations
* The model(s)


## Reading and simulating a model
The models provided in the manuscript both end in `.xml` and are in Systems Biology Markup Language ([SBML](https://sbml.org/)) format. 

**To read in the model and run a basic simulation:**
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model.xml")
solution = model.optimize()

# Will print only the solution to the objective function
print(solution)

# Will print more of a summary including the solution
print(model.summary(solution = solution))
```

**To generate the community model grwoth results in Table 2:**
```
import cobra

model = cobra.io.read_sbml_model("path\\to\\model\\supplemental_file_1.xml")

# Set media to upper bounds of Gale Lake with light
mediumupper = {
    'EX_cpd29674_e0': 45,
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
    'EX_photon_e': 100.0
}

model.medium = mediumupper
sol = model.optimize()
print(sol)
# prints <Solution 1.031 at 0x2106b888f10>
# 1.031 h^-1

# Set media to lower bounds of Gale Lake with light
mediumlower = {
    'EX_cpd29674_e0': 45,
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
    'EX_photon_e': 100.0
}

model.medium = mediumlower
sol = model.optimize()
print(sol)
# prints <Solution 0.001 at 0x17fd603ce80>
# 0.001 h^-1
```


