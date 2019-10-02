File Specification
######################
 
In general, this first section of the main input file is used to direct *Goma* I/O through a series of
named external files that contain information about the finite element mesh, the initial guess of a
solution vector, and output options for saving solutions for continuation, remesh, etc. The
required and optional input records are as follows:
 
.. include:: /chapters/ch4/section_1/4.1.1_fem_file.txt

.. include:: /chapters/ch4/section_1/4.1.2_output_exodusII_file.txt

.. include:: /chapters/ch4/section_1/4.1.3_guess_file.txt

.. include:: /chapters/ch4/section_1/4.1.4_soln_file.txt

.. include:: /chapters/ch4/section_1/4.1.5_write_intermediate_results.txt

.. include:: /chapters/ch4/section_1/4.1.6_write_initial_solution.txt
