.. highlight:: rst

Introduction
============

IReS(Intelligent  Multi-Engine  Resource  Scheduler) is an integrated,  open  source platform for managing, executing and monitoring complex analytics workflows. IReS is a core  component  of  the  ASAP  system  architecture  and its  main  task  is  to  "mix-and-match" diverse execution engines and data stores in order to optimize a workflow with respect to multiple, user-defined criteria. 

To that end, IReS incorporates a modeling framework that constantly evaluates the cost and  performance  of  data  and  computational  resources  under  various  configuration setups  in  order  to  decide  on  the  most  advantageous store,  indexing  and  execution pattern. A tree-based metadata language that describes operators in abstract and instantiated forms enables the search and matching of operators that perform a similar task in the planning  phase.  Afterwards,  a  decision  making  module  chooses  among  the  different equivalent execution plans (i.e., on different engines, resulting in equivalent output) the one that best fits the given policy based on cost and performance models. The chosen plan is scheduled and enforced, taking into account the available resources.