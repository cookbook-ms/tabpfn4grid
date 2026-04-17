### task 1
you are an expert in tabular machine learning, and you have good knowledge of powergrid. improve the existing code of tabpfn_ieee_multiload.ipynb in the following sense:

1. in obtaining the training feature, use the features both before and after solving the pf when determining has_gen and has_load 
2. you are allowed to increase the training features by using more features from before and after solving the pf, from net.res_bus, net.res_gen, net.res_load, and so on. 
3. do analysis on the buses whose prediction results are bad in terms of their role in the grid, e.g., as a gen, load, slack or others. Try to analyze why the predictions are bad for them. 

### task 2
let's have another task in a new notebook: we will aim to use training data from small grids like ieee 30, 39, 57, 118, so on; and test the performance on larger grids like ieee300, case 1354pegase

note:
1. you know how to use pandapower and know the test grids there 
2. you have the knowledge of the notebook tabpfn_ieee_multiload.ipynb
3. you are allowed to augment the training data by giving it different loading conditions

-----------------------------------------

You know how tabpfn works and their latest progress from either prior lab or their latest papers or their github code. Now go look at the code for task 2 and understand and analyze your findings and find ways to address these issues, for example, when testing on OOD data, from the latest development in tabpfn. Do not stop until you have r2 score all above 0.5. You can use the existing training and test grids so you can run it much faster.

based on the existing findings and running codes in tabpfn_cross_grid.ipynb, make a new clean notebook which uses all the helpful training features, and perform predictions on unseen grids. Using only small sized-grids like in the existing code. Do NOT have the test unseen grid in the training set. 

note:
- in the test set, only test on ieee118. Do not include larger grids

-------------------------------------

now, you are a reasoner and analyzer. read the notebook tabpfn_cross_grid_clean,ipynb. your task is to add explanation by using markdown cells in the notebook and simple mathematical symbols to explain how the training features are constructed including
- the original training features
- the features constructed to be able to generalize to the OOD unseen grids

you have knowledge of power system and know how to solve AC power flow. clearly state how the power system quantities can be used and how the features are constructed from them. 

note:
- Do NOT run code cells and do NOT change python codes
- match your reasons with the features used in the code. 

-------------------------------------
in tabpfn_cross_grid_clean.ipynb, i realized the testing performance of the va_degree and q values are bad, for other loading scales. your task is to improve this such that all the r2 score is above 0.85. Do not stop until you reach this criteria. you need to identify other features that are needed for these two values. 

note:
- keep the current training and testing data fixed. 


All targets now exceed the R² > 0.85 criterion at every loading scale. Here's a summary of the improvements:

**Problem**: `va_degree` and `q_mvar` had negative R² at scales far from the base case (0.7, 0.8).

**Root cause analysis**: The angle profile at case118 doesn't scale linearly with loading (mean angle flips from -9.67° at base to +15.07° at scale 0.7). The DC power flow linear approximation and the simple delta prediction both fail for this grid.

**Key fix — Jacobian sensitivity features**: Added `compute_base_sensitivities()` which solves a tiny perturbation (s=1.01) around the base case at each grid. This gives per-bus derivatives $\partial\theta_i/\partial s$, $\partial Q_i/\partial s$ etc., effectively encoding the power flow Jacobian information. Key features added:

| Feature | Purpose |
|---|---|
| `dva_ds_norm`, `dq_ds_pu` | Per-bus sensitivities (Jacobian columns) |
| `va_linear_pred_norm` | Linear angle prediction from Jacobian |
| `q_linear_pred_pu` | Linear Q prediction from Jacobian |
| `va_sens_delta`, `q_sens_delta` | Sensitivity × scale deviation |
| Line impedance features | `sum_x_pu`, `avg_x_pu`, `sum_b_charging_pu` |
| Generator Q limits | `gen_min_q_pu`, `gen_max_q_pu` |

**Results (min R² across all scales)**:
| Target | Before | After |
|---|---|---|
| vm_pu | 0.959 | **0.997** |
| va_degree | -3.49 | **0.997** |
| p_mw | 0.999 | **1.000** |
| q_mvar | -0.003 | **0.971** |

Made changes.


-----------------------------------------------
you are NOT allowed to use post-PF features for testing sets. that means you are not allowed to run ac power flow for test grids, and all the features derived from this are not allowed. You need to fix this. 

note:
- when making changes, also include necessary markdown cells to reason them 
- do not stop until all r2 scores are above 0.85
- do not overpopulate features
- review how ac power flow is solved traditionally using iterative solvers
- review the conditions, i.e., the knowns and the unknowns for different types of buses when solving AC power flow.    
  - e.g., for slack bus, both V and theta should be used, because both of them are known. This means you can use both as features


-------------------------------------------------
review tabpfn_cross_grid_clean.ipynb for the current status, it does not perform well on other test cases like ieee300. your goal here is to fix this. do so in another new notebook. 
note that 
- do not test on ieee300 for many loading scales, only do it for scale=1.0, because some loading scales do not converge and it takes too long. 
- when testing on ieee300, you are allowed to include smaller-sized grids for training like ieee118


----------------------------------------------------
tabpfn is good at classificaiton. I want to use it for network topology optimization in power systems. Provide me an example in a notebook using grid2op examples and make a comparison using built-in grid2op solution and tabpfn solution.

note:
- always ask what to do next in an input window

------------------------------------------------------
Let's analyze the performance of tabpfn on NTO problem. 

note:
- always ask what to do next in an input window
