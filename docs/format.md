---
layout: page
title: IBPSA Project 1
permalink: format.html
---

<div class="starter-template">
  <h1>File format</h1>
</div>


To submit a test case, use the following format (comments that start with // are not part of the specification).
Test cases are stored in the <code>_cases</code> directory.

<pre>
---
layout: case                                            // leave this entry as is
date-submitted:  2015-05-17                             // Date of submission
model: IBPSA.Fluid.Examples.FlowSystem.Basic            // Modelica model name
sources:                                                // List of required repositories
  - repository: git@github.com:ibpsa/modelica-ibpsa.git    // Git repository
    commit: 15259eeb2dcd4f5c60794a1cd11a55fcb4eb6cb9       // Commit hash
  - repository: git@github.com:myRepo/test-repo.git        // Git repository
    commit: 25259eeb2dcd4f5c60794a1cd11a55fcb4eb6cb9       // Commit hash


tool: Dymola|OPTIMICA|OpenModelica                      // Tool. Enter either Dymola, OPTIMICA, OpenModelica
tool-version: 2022                                      // Tool version
tool-issue-number: xxx                                  // Optional: The ticket number if there is
                                                        // a support request with the tool developer
tool-issue-link: xxx                                    // Optional: URL to the tool issue,
                                                        // such as link to a github issue
tool-status:                                            // Status of tool developer: either closed or open

library-issue-link: xxx                                 // Optional: URL to the library issue,
                                                        // such as link to a github issue
os: Linux Ubuntu 20.04                                  // Operating system

compiler: gcc version 9.4.0                             // Optional. Compiler version.

command: xxx                                            // Command to simulate the model
files:                                                // List any number of scripts needed to run the model
  - name: xxx                                           // File name of the script (as is used in the command)
    content: xxx                                        // Content of script

log: xxx                                                // Paste the log output of the simulator
---

Provide a brief one to two sentence description that will be displayed in the overview table.

&lt;!--excerpt-->

Provide a more detailed description.
</pre>

Note that entries with multiple lines can be entered as follows:
<pre>
log: |+
  This is a log entry
  that has two lines.
</pre>



