---
layout: post
title: Convert DIMACS-CNF format to SMTLIBv2 format 
---

{{ page.title }}
================

<p class="meta">25 May 2014</p>

The [propositional satisfiability](http://en.wikipedia.org/wiki/Boolean_satisfiability_problem) (SAT) problem is the first known NP-complete problem. It has been applied to scheduling, model checking, to name just a few applications.

There are various formats to describe a SAT problem, including the [DIMACS-CNF](http://people.sc.fsu.edu/~jburkardt/data/cnf/cnf.html) format, which is widely accepted as the standard format for SAT. For example, this format is used in the benchmarks on [satlib.org](satlib.org) as well as the benchmarks in the [SAT competition](http://www.satcompetition.org/).

The DIMACS-CNF format is very simple, and easy to parse. However, like other formats for SAT, its expressiveness is very limited. For example, most state-of-the-art SAT solvers can be used in an incremental and backtrackable manner, and in many cases it is desirable to solve the problem incrementally. However, it is not possible to express incremental solving in DIMACS-CNF format.

For this reason, I propose to use the [SMTLIBv2](http://smtlib.cs.uiowa.edu/language.shtml) format to present a SAT problem. SMTLIBv2 is the standard format for the [SAT Modulo Theories](http://en.wikipedia.org/wiki/Satisfiability_Modulo_Theories) (SMT) problem, of which SAT is a proper subset.

A SAT problem can be viewed as a special case of an SMT problem in which all Boolean variables are viewed as uninterpreted functions. Hence, I have implemented a simple tool to convert a propositional formula in DIMACS-CNF format to a formula in QF_UF in SMTLIBv2 format. The source code can be downloaded from this repository:  
[https://github.com/qsphan/smt-utils](https://github.com/qsphan/smt-utils)

Note that all SMT solvers have a SAT solver inside, and many SMT solvers, e.g. [z3](http://z3.codeplex.com/), even accept the DIMACS-CNF format.

Here is an example of a problem in DIMACS-CNF format:  

        c  simple_v3_c2.cnf
        c
        p cnf 3 2
        1 -3 0
        2 3 -1 0


Using the tool I implemented, the problem is converted into a QF_UF formula as follows:


        ( set-option :produce-models true )
        ( set-logic QF_UF )
        
        ( declare-fun p1 () Bool )
        ( declare-fun p2 () Bool )
        ( declare-fun p3 () Bool )
        
        ( assert ( and 
         ( or p1 ( not p3 )  )
         ( or p2 p3 ( not p1 )  )
        ))
        
        ( check-sat )
        ( get-model )


This formula then can be extended with **push** and **pop** as in SMTLIBv2 standard for incremental solving.
