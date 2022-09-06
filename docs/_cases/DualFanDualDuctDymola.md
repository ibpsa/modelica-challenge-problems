---
layout: case
date-submitted:  2022-03-16
model: Buildings.Examples.DualFanDualDuct.ClosedLoop
sources:
  - repository: git@github.com:lbl-srg/modelica-buildings.git
    commit: d538de534a6f6c19311d70efb664ffdf6f5ca6a6
tool: Dymola
tool-version: 2022x
tool-status: work-around
discussion: https://github.com/ibpsa/modelica-challenge-problems/discussions/3
os: Linux Ubuntu 20.04
command: |+
  cd modelica-buildings
  echo "openModel(\"Buildings/package.mo\", changeDirectory = false);" > run.mos
  echo "simulateModel(\"Buildings.Examples.DualFanDualDuct.ClosedLoop\", stopTime=172800, method=\"dassl\", tolerance=1e-06, resultFile=\"ClosedLoop\");" >> run.mos
  echo "exit();" >> run.mos
  dymola run.mos
log: |+
  None as simulation hangs. The dslog.txt file is empty, even after manually terminating the simulation.
  After terminating the simulation, the command log has the following entries:

  ...
  Stopping...
  Failed to wait for simulation to stop.
  previous process failed.
  Simulating: Time= 2.039e+04
   Simulation is not responding to stop request.
  WARNINGS have been issued.
  ERRORS have been issued.

status: open
---

Simulation that hangs. The dslog.txt file is empty and the simulation window does not show any diagnostics.

<!--excerpt-->
For comparison, with Radau, 1E-6, the simulation takes 4 seconds;
if changed to cvode, 1E-6, the simulation fails at time = 19873.6;
if changed to dassl, 1E-6, the simulation hangs around time = 20000.
