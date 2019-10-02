Solver Specifications
#########################

This required section directs the nonlinear iteration strategy with associated parameters (e.g.,
Newton’s method options), matrix solution strategy and parameters, and other sundry options and
toggles for the pressure stabilization approach and linear stability analysis capability. With regard
to the parameters associated with matrix solution methods, it is important to understand that there
are two major classes of solvers - direct and iterative solvers. Direct solvers are the most robust,
but can be computationally impractical for some larger systems. Iterative solvers and associated
preconditioners are the only practical options for large-scale problems (viz., very large twodimensional
problems and virtually all three-dimensional problems). Choosing the solver settings
for good convergence of iterative matrix solvers can be an artful task for Navier-Stokes problems
and other poorly conditioned systems. It is recommended that the user consult the comprehensive
report by Schunk, et al. (2002) for an overview and further usage tips.

.. include:: /chapters/ch4/section_7/4.7.1_solution_algorithm.txt

.. include:: /chapters/ch4/section_7/4.7.2_matrix_storage_format.txt

.. include:: /chapters/ch4/section_7/4.7.3_preconditioner.txt

.. include:: /chapters/ch4/section_7/4.7.4_matrix_subdomain_solver.txt

.. include:: /chapters/ch4/section_7/4.7.5_matrix_scaling.txt

.. include:: /chapters/ch4/section_7/4.7.6_matrix_residual_norm_type.txt

.. include:: /chapters/ch4/section_7/4.7.7_matrix_output_type.txt

.. include:: /chapters/ch4/section_7/4.7.8_matrix_factorization_reuse.txt

.. include:: /chapters/ch4/section_7/4.7.9_matrix_graph_fillin.txt

.. include:: /chapters/ch4/section_7/4.7.10_matrix_factorization_overlap.txt

.. include:: /chapters/ch4/section_7/4.7.11_matrix_overlap_type.txt

.. include:: /chapters/ch4/section_7/4.7.12_matrix_auxiliary_vector.txt

.. include:: /chapters/ch4/section_7/4.7.13_matrix_drop_tolerance.txt

.. include:: /chapters/ch4/section_7/4.7.14_matrix_polynomial_order.txt

.. include:: /chapters/ch4/section_7/4.7.15_matrix_reorder.txt

.. include:: /chapters/ch4/section_7/4.7.16_matrix_factorization_save.txt

.. include:: /chapters/ch4/section_7/4.7.17_matrix_ilut_fill_factor.txt

.. include:: /chapters/ch4/section_7/4.7.18_matrix_rilu_relax_factor.txt

.. include:: /chapters/ch4/section_7/4.7.19_matrix_bilu_threshold.txt

.. include:: /chapters/ch4/section_7/4.7.20_matrix_relative_threshold.txt

.. include:: /chapters/ch4/section_7/4.7.21_matrix_absolute_threshold.txt

.. include:: /chapters/ch4/section_7/4.7.22_size_of_krylov_subspace.txt

.. include:: /chapters/ch4/section_7/4.7.23_orthogonalization.txt

.. include:: /chapters/ch4/section_7/4.7.24_maximum_linear_solve_iterations.txt

.. include:: /chapters/ch4/section_7/4.7.25_number_of_newton_iterations.txt

.. include:: /chapters/ch4/section_7/4.7.26_modified_newton_tolerance.txt

.. include:: /chapters/ch4/section_7/4.7.27_jacobian_reform_time_stride.txt

.. include:: /chapters/ch4/section_7/4.7.28_newton_correction_factor.txt

.. include:: /chapters/ch4/section_7/4.7.29_normalized_residual_tolerance.txt

.. include:: /chapters/ch4/section_7/4.7.30_normalized_correction_tolerance.txt

.. include:: /chapters/ch4/section_7/4.7.31_residual_ratio_tolerance.txt

.. include:: /chapters/ch4/section_7/4.7.32_pressure_stabilization.txt

.. include:: /chapters/ch4/section_7/4.7.33_pressure_stabilization_scaling.txt

.. include:: /chapters/ch4/section_7/4.7.34_linear_stability.txt

.. include:: /chapters/ch4/section_7/4.7.35_filter_concentration.txt

.. include:: /chapters/ch4/section_7/4.7.36_disable_viscosity_sensitivities.txt

