---
layout: case
date-submitted:  2015-05-17
model: IBPSA.Fluid.Examples.FlowSystem.Simplified1
sources:
  - repository: git@github.com:ibpsa/modelica-ibpsa.git
    commit: dbd21fa5937b262ecd6a6b155a51e5f9edfe3f62
tool: Dymola
tool-version: 2022x
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
    Time: 12.97929584189059
    Tag: simulation.nonlinear[3]
    For debugging information enable
    Simulation/Setup/Debug/Nonlinear solver diagnostics/Detailed logging of failed nonlinear solutions.

  Warning: Failed to solve nonlinear system using Newton solver.
    Time: 14.0524358164975
    Tag: simulation.nonlinear[3]

  Warning: Failed to solve nonlinear system using Newton solver.
    Time: 17.85036254174598
    Tag: simulation.nonlinear[3]

  Warning: Failed to solve nonlinear system using Newton solver.
    Time: 516.3564299944559
    Tag: simulation.nonlinear[3]

  Warning: Failed to solve nonlinear system using Newton solver.
    Time: 516.0534294555639
    Tag: simulation.nonlinear[3]

  Warning: Failed to solve nonlinear system using Newton solver.
    Time: 516
    Tag: simulation.nonlinear[3]
  Previous problem occured when evaluating crossing function, reducing step-size
  cvode: CVODE cvRcheck3 At t = 515.978, the rootfinding routine failed in an unrecoverable manner.
  Unexpected value: -12 at time 514
  Unexpected CVode value

  Integration terminated unsuccesfully at T = 514
     CPU-time for integration                  : 0.0912421 seconds
     CPU-time for initialization               : 0.000753164 seconds
     Number of result points                   : 266
     Number of grid points                     : 258
     Number of accepted steps                  : 351
     Number of rejected steps                  : 4
     Number of f-evaluations (dynamics)        : 439
     Number of non-linear iteration            : 410
     Number of non-linear convergence failures : 5
     Number of Jacobian-evaluations            : 12
     Number of crossing function evaluations   : 619
     Number of model time events               : 4
     Number of state events                    : 1
     Number of step events                     : 0
     Maximum integration order                 : 5

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