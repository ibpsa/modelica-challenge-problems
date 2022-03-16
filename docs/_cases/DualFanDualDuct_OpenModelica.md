---
layout: case
date-submitted:  2022-03-16
model: Buildings.Examples.DualFanDualDuct.ClosedLoop
sources:
  - repository: git@github.com:lbl-srg/modelica-buildings.git
    commit: d538de534a6f6c19311d70efb664ffdf6f5ca6a6
tool: OpenModelica
tool-version: OpenModelica 1.19.0~dev-613-gd6e04c0
tool-status: open
os: Linux Ubuntu 20.04
command: |+
  cd modelica-buildings
  omc openmod.mos
files:
  - name: openmod.mos
    content: |+
      setCommandLineOptions("-d=nogen");
      setCommandLineOptions("-d=initialization");
      setCommandLineOptions("-d=backenddaeinfo");
      setCommandLineOptions("-d=discreteinfo");
      setCommandLineOptions("-d=stateselection");
      setMatchingAlgorithm("PFPlusExt");
      setIndexReductionMethod("dynamicStateSelection");
      loadFile("Buildings/package.mo");
      translateModel(Buildings.Examples.DualFanDualDuct.ClosedLoop, method="cvode", tolerance=1e-06, numberOfIntervals=500);
      getErrorString();
      system("make -f Buildings.Examples.DualFanDualDuct.ClosedLoop.makefile");
      system("./Buildings.Examples.DualFanDualDuct.ClosedLoop -s cvode -steps -cpu -lv LOG_STATS");

log: |+
  [CVODE ERROR]  CVode
    At t = 19894.3, mxstep steps taken before reaching tout.

  stdout            | info    | ##CVODE## -1 error occurred at time = 19894.3009912866
  stdout            | info    | model terminate | Integrator failed. | Simulation terminated at time 19894.3
  LOG_STATS         | info    | ### STATISTICS ###
  |                 | |       | | timer
  |                 | |       | | |     0.797688s          reading init.xml
  |                 | |       | | |    0.0651169s          reading info.xml
  |                 | |       | | |    0.0349142s [  1.8%] pre-initialization
  |                 | |       | | |    0.0996235s [  5.2%] initialization
  |                 | |       | | |      1.13512s [ 59.0%] steps
  |                 | |       | | |     0.494772s [ 25.7%] solver (excl. callbacks)
  |                 | |       | | |       0.0318s [  1.7%] creating output-file
  |                 | |       | | |   0.00117857s [  0.1%] event-handling
  |                 | |       | | |   0.00172706s [  0.1%] overhead
  |                 | |       | | |       0.1258s [  6.5%] simulation
  |                 | |       | | |      1.92494s [100.0%] total
  |                 | |       | | events
  |                 | |       | | |     3 state events
  |                 | |       | | |     0 time events
  |                 | |       | | solver: cvode
  |                 | |       | | |  1424 steps taken
  |                 | |       | | |  1859 calls of functionODE
  |                 | |       | | |    37 evaluations of jacobian
  |                 | |       | | |    30 error test failures
  |                 | |       | | |     5 convergence test failures
  |                 | |       | | | 0s time of jacobian evaluation

status: open
---

The simulation fails with the cvode solver.

<!--excerpt-->
If the solver is changed to dassl, the simulation succeeds, but takes
about 380 seconds.

For comparison, with Dymola 2022x and Radau, 1E-6, simulation takes 4 seconds;
if changed to cvode, the simulation fails at time = 19873.6;
if changed to dassl, the simulation hangs around time = 19800.
