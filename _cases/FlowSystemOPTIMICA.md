---
layout: case
date-submitted:  2020-03-11
model: IBPSA.Fluid.Examples.FlowSystem.Basic
sources:
  - repository: git@github.com:ibpsa/modelica-ibpsa.git
    commit: dbd21fa5937b262ecd6a6b155a51e5f9edfe3f62
tool: OPTIMICA
tool-version: OCT-dev-r26446_JM-r14295
tool-status: open
os: Linux Ubuntu 20.04
command: |+
  cd modelica-ibpsa
  jm_ipython.sh run.py IBPSA.Fluid.Examples.FlowSystem.Basic
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
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   Failed to find a solution for nonlinear <value name="block">"2"</value> at <value name="time">        4.5547110015712224E+02</value>.
  FMIL: module = Model, log level = 2: [INFO][FMU status:Error]   Could not compute next set of values for the iteration variables that would get us closer to a solution.
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   The <value name="function">KINSol</value> outputs:
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="msg">"The line search algorithm was unable to find an iterate sufficiently distinct from the current iterate."</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="functionL2Norm">        4.1508352471939716E-08</value>, <value name="scaledStepLength">        8.1781233873004737E+00</value>, <value name="tolerance">        1.0000000000000000E-08</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] </NonlinearBlockConvergenceError>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] <Error category="error">The equations with initial scaling solved fine, re-scaled equations failed in <value name="block">"2"</value></Error>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <Warning category="warning">Evaluating the derivatives failed while evaluating the event indicators at <value name="t">        4.5547110015712224E+02</value>, retrying with restored values.</Warning>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">0</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.4543980541939865E+11</value> (<value name="dRes_dIter_scaled">       -1.4543980541939866E+12</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">18</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">        1.5966465854843893E+10</value> (<value name="dRes_dIter_scaled">        1.5966465854843893E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">26</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603391159E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603391159E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">27</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603391647E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603391647E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">28</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603389008E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603389008E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 2: [INFO][FMU status:Error] <NonlinearBlockConvergenceError category="error">
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   Failed to find a solution for nonlinear <value name="block">"2"</value> at <value name="time">        4.5547110015712224E+02</value>.
  FMIL: module = Model, log level = 2: [INFO][FMU status:Error]   Could not compute next set of values for the iteration variables that would get us closer to a solution.
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   The <value name="function">KINSol</value> outputs:
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="msg">"The line search algorithm was unable to find an iterate sufficiently distinct from the current iterate."</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="functionL2Norm">        4.1508352471939716E-08</value>, <value name="scaledStepLength">        8.1781233873004737E+00</value>, <value name="tolerance">        1.0000000000000000E-08</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] </NonlinearBlockConvergenceError>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] <Error category="error">The equations with initial scaling solved fine, re-scaled equations failed in <value name="block">"2"</value></Error>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] <Error category="error">Evaluating the derivatives failed while evaluating the event indicators at <value name="t">        4.5547110015712224E+02</value></Error>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">0</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.4543980541939865E+11</value> (<value name="dRes_dIter_scaled">       -1.4543980541939866E+12</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">18</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">        1.5966465854843893E+10</value> (<value name="dRes_dIter_scaled">        1.5966465854843893E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">26</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603391159E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603391159E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">27</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603391647E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603391647E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 3: [WARNING][FMU status:Warning] <MinScalingExceeded category="warning">Poor scaling in <value name="block">"2"</value> but will allow it based on structure of block. Consider changes in the model. Partial derivative of <value name="equation" flags="index">28</value> with respect to <value name="Iter">"spl.port_3.m_flow"</value> is <value name="dRes_dIter">       -1.9058323603389008E+10</value> (<value name="dRes_dIter_scaled">       -1.9058323603389008E+11</value>).</MinScalingExceeded>
  FMIL: module = Model, log level = 2: [INFO][FMU status:Error] <NonlinearBlockConvergenceError category="error">
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   Failed to find a solution for nonlinear <value name="block">"2"</value> at <value name="time">        4.5547110015712224E+02</value>.
  FMIL: module = Model, log level = 2: [INFO][FMU status:Error]   Could not compute next set of values for the iteration variables that would get us closer to a solution.
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   The <value name="function">KINSol</value> outputs:
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="msg">"The line search algorithm was unable to find an iterate sufficiently distinct from the current iterate."</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error]   <value name="functionL2Norm">        4.1508352471939716E-08</value>, <value name="scaledStepLength">        8.1781233873004737E+00</value>, <value name="tolerance">        1.0000000000000000E-08</value>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] </NonlinearBlockConvergenceError>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] <Error category="error">The equations with initial scaling solved fine, re-scaled equations failed in <value name="block">"2"</value></Error>
  FMIL: module = Model, log level = 2: [ERROR][FMU status:Error] <DerivativeCalculationFailure category="error">Evaluating the ode derivatives failed.</DerivativeCalculationFailure>

status: open
---

Flow system in which the time integration stalls.

<!--excerpt-->
