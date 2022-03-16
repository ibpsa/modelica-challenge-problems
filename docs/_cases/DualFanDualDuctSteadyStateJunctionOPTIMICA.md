---
layout: case
date-submitted:  2022-03-16
model: DualFanDualDuctSteadyStateJunction
sources:
  - repository: git@github.com:lbl-srg/modelica-buildings.git
    commit: d538de534a6f6c19311d70efb664ffdf6f5ca6a6
tool: OPTIMICA
tool-version: OCT-dev-r26446_JM-r14295
tool-status: open
os: Linux Ubuntu 20.04
command: |+
  cd modelica-buildings
  echo "model DualFanDualDuctSteadyStateJunction
  extends Buildings.Examples.DualFanDualDuct.ClosedLoop(
    splRetRoo1(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splRetSou(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splRetEas(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splRetNor(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupNorCol(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupEasCol(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupSouCol(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupRoo1Col(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupRoo1Hot(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupSouHot(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupEasHot(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splSupNorHot(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splHotColDec(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splCol2(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState),
    splCol1(energyDynamics=Modelica.Fluid.Types.Dynamics.SteadyState));
  annotation (experiment(
      StopTime=172800,
      Tolerance=1e-06));
  end DualFanDualDuctSteadyStateJunction;" > DualFanDualDuctSteadyStateJunction.mo
  jm_ipython.sh run.py DualFanDualDuctSteadyStateJunction.mo
files:
  - name: run.py
    content: |+
      #!/usr/bin/env python3
      ##########################################################################
      # Script to simulate Modelica models with JModelica.
      #
      ##########################################################################
      # Import the function for compilation of models and the load_fmu method

      from pymodelica import compile_fmu
      import traceback
      import logging
      import math

      from pyfmi import load_fmu
      import pymodelica

      import os
      import shutil
      import sys
      import matplotlib.pyplot as plt

      import pprint

      debug_solver = False
      model="Buildings.Utilities.Psychrometrics.Examples.DewPointTemperature"
      generate_plot = False
      final_time = float("NaN") # Set to  float("NaN") to use stopTime from .mo file
      #final_time = 10E4
      # Overwrite model with command line argument if specified
      if len(sys.argv) > 1:
        # If the argument is a file, then parse it to a model name
        if os.path.isfile(sys.argv[1]):
          model = sys.argv[1].replace(os.path.sep, '.')[:-3]
        else:
          model=sys.argv[1]


      print("*** Compiling {}".format(model))
      # Increase memory
      pymodelica.environ['JVM_ARGS'] = '-Xmx4096m'


      sys.stdout.flush()

      ######################################################################
      # Compile fmu
      import time

      start = time.time()
      fmu_name = compile_fmu(model,
                             version="2.0",
                             compiler_log_level='warning', #'info', 'warning',
                             compiler_options = {"generate_html_diagnostics" : False,
                                                 "event_output_vars": True,
                                                 "generate_ode_jacobian": False, # default is False
                                                 "nle_solver_tol_factor": 1e-2}) # 1e-2 is the default

      print(f"Compiled {fmu_name}")
      end = time.time()
      print(f"Translation time: {end - start}")

      #sys.exit(0)
      ######################################################################
      # Copy style sheets.
      # This is a hack to get the css and js files to render the html diagnostics.
      htm_dir = os.path.splitext(os.path.basename(fmu_name))[0] + "_html_diagnostics"
      if os.path.exists(htm_dir):
          for fil in ["scripts.js", "style.css", "zepto.min.js"]:
              src = os.path.join(".jmodelica_html", fil)
              if os.path.exists(src):
                  des = os.path.join(htm_dir, fil)
                  shutil.copyfile(src, des)

      ######################################################################
      # Load model
      mod = load_fmu(fmu_name, log_level=4) # default setting is 3
      mod.set_max_log_size(2073741824) # = 2*1024^3 (about 2GB)
      ######################################################################
      # Print derivatives
      import re
      print("Derivatives:")
      for d in mod.get_derivatives_list().keys():
        print(f"       {d}")
      print("")

      ######################################################################
      # Retrieve and set solver options
      x_nominal = mod.nominal_continuous_states
      opts = mod.simulate_options() #Retrieve the default options

      opts['solver'] = 'CVode' #'Radau5ODE' #CVode
      opts['ncp'] = 500

      # Set user-specified tolerance if it is smaller than the tolerance in the .mo file
      rtol = 1.0e-6
      x_nominal = mod.nominal_continuous_states

      if len(x_nominal) > 0:
        atol = rtol*x_nominal
      else:
        atol = rtol
      opts[f"{opts['solver']}_options"]['rtol'] = rtol
      opts[f"{opts['solver']}_options"]['atol'] = atol

      if opts['solver'].lower() == 'cvode':

        opts['CVode_options']['external_event_detection'] = False
        opts['CVode_options']['maxh'] = (mod.get_default_experiment_stop_time()-mod.get_default_experiment_start_time())/float  (opts['ncp'])
        opts['CVode_options']['iter'] = 'Newton'
        opts['CVode_options']['discr'] = 'BDF'
        opts['CVode_options']['store_event_points'] = True # True is default, set to false if many events

        if debug_solver:
          opts['CVode_options']['clock_step'] = True


      if debug_solver:
        opts["logging"] = True #<- Turn on solver debug logging
        mod.set("_log_level", 4)

      ######################################################################
      # Simulate
      if opts['solver'].lower() != 'cvode' or abs(rtol-1E-6) > 1E-12:
        print(f"Solver is {opts['solver']}, {rtol}")

      if math.isnan(final_time):
        res = mod.simulate(options=opts)
      else:
        res = mod.simulate(options=opts, final_time=final_time)

log: |+
  ...
        der(pumPreHea.filter.s[2])
        der(conFanSupCol.con.I.y)
        der(conPreHeatCoi.preHeaCoiCon.I.y)
        der(conTMix.I.y)

  Integrator time: 2.020292e+04

status: open
---

HVAC and building model that stalls during the simulation.

<!--excerpt-->
The original model `Buildings.Examples.DualFanDualDuct.ClosedLoop` works
with OPTIMICA, but the parameter assignments that remove the mixing volume at the flow
junctions cause the solver to stall.
Therefore, we recommend our users to add these mixing volumes, but the drawback is
that they introduce fast dynamics and additional state variables.