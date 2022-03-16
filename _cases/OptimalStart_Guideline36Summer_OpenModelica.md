---
layout: case
date-submitted:  2022-03-11
model: Buildings.Air.Systems.SingleZone.VAV.Examples.OptimalStart.Guideline36Summer
sources:
  - repository: git@github.com:lbl-srg/modelica-buildings.git
    commit: 60595a70f9830422198abff9190c84ead456b20c
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
      translateModel(Buildings.Air.Systems.SingleZone.VAV.Examples.OptimalStart.Guideline36Summer, method="dassl", tolerance=1e-06, numberOfIntervals=500);
      getErrorString();
      system("make -f Buildings.Air.Systems.SingleZone.VAV.Examples.OptimalStart.Guideline36Summer.makefile");
      system("./Buildings.Air.Systems.SingleZone.VAV.Examples.OptimalStart.Guideline36Summer -s dassl -steps -cpu -lv LOG_STATS");
log: |+
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[1] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[2] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[3] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[4] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[5] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[6] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[7] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[8] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[9] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[10] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[11] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[12] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[13] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Opt.sinZonFlo.flo.irRadExc.J[14] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[1] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[2] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[3] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[4] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[5] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[6] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[7] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[8] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[9] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[10] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[11] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[12] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[13] <= 0.0, has value: 3350.13
   assert            | warning | The following assertion has been violated at time 15984000.000000
   |                 | |       | false
   assert            | warning | Nominal variable violating max constraint: zonAHUG36Con.sinZonFlo.flo.irRadExc.J[14] <= 0.0, has value: 3350.13
   assert            | debug   | division leads to inf or nan at time 1.5984e+07, (a=-nan) / (b=-nan), where divisor b is: 1006.0 * zonAHUG36Con.hvac.heaCoi.sta_a.X[2] + 1860.0 * zonAHUG36Con.hvac.heaCoi.sta_a.X[1]
   assert            | error   | Failed to solve the initialization problem with global homotopy with equidistant step size.
   assert            | debug   | Unable to solve initialization problem.
   assert            | info    | simulation terminated by an assertion at initialization
   255

status: open
---

The simulation fails.

<!--excerpt-->
The simulation fails with the message shown in the log.
