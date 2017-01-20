.. highlight:: rst

Introduction
============

Overview
--------

ASAP focuses on (i) innovative methods and technologies and (ii) tools and applications. 

The Web Analytics and Telecommunications Applications provides useful workflows. They have alse served as a pilot for conducting a set of operators required for the workflow execution. These operators are implemented in Cilk and Swan or in Apache Spark, etc and are uploaded (via REST) to the IReS which is able to profile and execute them without  having  knowledge  of  their  implementation  details.   Therefore  a  JSON-based  metadata language is employed as a general abstraction. Finally, whenever a new materialized operator gets inserted in the IReS platform the system estimates its cost in terms of performance and money using several configurations and saves each plan in a database.  The later is queried by the Decision Making Module in order to choose the optimal execution plan for a workflow uploaded by the user for execution.


An ASAP user, usually follows the following steps:

1) The Developer implements a new analytics computation as an operator.
2) The Developer builds the operators and creates the metadata for describe the operator's semantics.
3) The Developer adds the operator to a running installation of the ASAP platform into the library of IReS operators.
4) The IReS runs the operator implementation.
5) The Workflow Designer creates a workflow in the Workflow Management Tool.
6) The User initiates the computation of that workflow.
7) The IReS platform runs that workflow.
8) The User can monitor at any time the workflow execution.


The figure below shows the separate components of the ASAP system along with their interactions. For more details read the corresponding chapter.


.. image:: figures/integration.png




