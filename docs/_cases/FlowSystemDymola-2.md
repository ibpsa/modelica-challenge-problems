---
layout: case
date-submitted:  2024-02-16
model: IBPSA.Fluid.Examples.FlowSystem.Simplified1
sources:
  - repository: git@github.com:ibpsa/modelica-ibpsa.git
    commit: e70093d8a8fac05427d43f2f008a7e731fadd64e
tool: Dymola
tool-version: 2024x
status: open
os: Linux Ubuntu 20.04
command: |+
  cd modelica-ibpsa
  echo "openModel(\"IBPSA/package.mo\", changeDirectory = false);" > run.mos
  echo "simulateModel(\"IBPSA.Fluid.Examples.FlowSystem.Simplified1\", stopTime=1000, method=\"Cvode\", tolerance=1e-06, resultFile=\"Simplified1\");" >> run.mos
  echo "exit();" >> run.mos
  dymola run.mos
  cat dslog.txt

log: |+
Model: IBPSA.Fluid.Examples.FlowSystem.Simplified1
Integration started at 0 using integration method:
cvode from sundials


Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.344385256591128
  Tag: simulation.nonlinear[3]
  For debugging information enable
  Simulation/Setup/Debug/Nonlinear solver diagnostics/Detailed logging of failed nonlinear solutions.

Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.344385256591128
  Tag: simulation.nonlinear[3]

Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.390616987683465
  Tag: simulation.nonlinear[3]

Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.390616987683465
  Tag: simulation.nonlinear[3]

Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.370401854505952
  Tag: simulation.nonlinear[3]

Warning: Failed to solve nonlinear system using Newton solver.
  Time: 5.362821179564386
  Tag: simulation.nonlinear[3]
Previous problem occured when evaluating crossing function, reducing step-size
SUNDIALS: CVODE cvRcheck3 At t = 5.36029, the rootfinding routine failed in an unrecoverable manner.
Cannot recover from failed crossing function evaluation at time 5
CVode simulation failed

Integration terminated unsuccesfully at T = 5
   CPU-time for integration                  : 0.0367098 seconds
   CPU-time for initialization               : 0.00101209 seconds
   Number of result points                   : 5
   Number of grid points                     : 3
   Number of accepted steps                  : 43
   Number of rejected steps                  : 1
   Number of f-evaluations (dynamics)        : 71
   Number of non-linear iteration            : 58
   Number of non-linear convergence failures : 3
   Number of Jacobian-evaluations            : 5
   Number of crossing function evaluations   : 46
   Number of model time events               : 1
   Number of state events                    : 0
   Number of step events                     : 0
   Maximum integration order                 : 4

ERROR: The simulation of IBPSA.Fluid.Examples.FlowSystem.Simplified1 FAILED

---

Flow system in which the nonlinear system of equation cannot be solved during the time integration.

<!--excerpt-->

In Dymola, the model fails to simulate with CVOde 1E-6, but it works if changed to 1E-8.
For example, the following command leads to a successful simulation:
<pre>
  simulateModel("IBPSA.Fluid.Examples.FlowSystem.Simplified1",
    stopTime=1000, method="Cvode", tolerance=1e-08, resultFile="Simplified1");
</pre>
See also [`FlowSystemDymola`](FlowSystemDymola), which uses an earlier version of the `IBPSA` library, with a different pump
model, which works with Dymola 2024x. It appears that the numerics of the model changed to the point
where the solver now fails to converge, also if the tolerance is changed to `1E-8`.