
Problem Description
#######################

This section directs all input specifications required for differential equations, material type, mesh motion type, coordinate system, finite element basis function type, and several other input tasks. This section of input records, with the exception of the *Number of Materials* card (the first one
below), must be repeated for each material region in the problem. Within that region of the problem domain (and the corresponding section of the input file) there are no restrictions as to which differential or constraint equations can be specified, which is a unique capability of *Goma*.

However, some combinations or specifications do not make much sense, e.g., a cylindrical coordinate region combined with a cartesian one. It is recommended that the user consult the usage tutorials and example problems to get a feel for how this section is constructed.

.. include:: /chapters/ch4/section_12/4.12.1_number_of_materials.txt

.. include:: /chapters/ch4/section_12/4.12.2_mat.txt

.. include:: /chapters/ch4/section_12/4.12.3_coordinate_system.txt

.. include:: /chapters/ch4/section_12/4.12.4_element_mapping.txt

.. include:: /chapters/ch4/section_12/4.12.5_mesh_motion.txt

.. include:: /chapters/ch4/section_12/4.12.6_number_of_bulk_species.txt

.. include:: /chapters/ch4/section_12/4.12.7_material_is_nondilute.txt

.. include:: /chapters/ch4/section_12/4.12.8_number_of_bulk_species_equations.txt

.. include:: /chapters/ch4/section_12/4.12.9_default_material_species_type.txt

.. include:: /chapters/ch4/section_12/4.12.10_number_of_viscoelastic_modes.txt

.. include:: /chapters/ch4/section_12/4.12.11_number_of_eq.txt

.. include:: /chapters/ch4/section_12/4.12.12_energy.txt

.. include:: /chapters/ch4/section_12/4.12.13_momentum.txt

.. include:: /chapters/ch4/section_12/4.12.14_pmomentum.txt

.. include:: /chapters/ch4/section_12/4.12.15_stress.txt

.. include:: /chapters/ch4/section_12/4.12.16_species_bulk.txt

.. include:: /chapters/ch4/section_12/4.12.17_mesh.txt

.. include:: /chapters/ch4/section_12/4.12.18_mom_solid.txt

.. include:: /chapters/ch4/section_12/4.12.19_continuity.txt

.. include:: /chapters/ch4/section_12/4.12.20_fill.txt

.. include:: /chapters/ch4/section_12/4.12.21_lagr_mult_1, lagr_mult_2, lagr_mult_3.txt

.. include:: /chapters/ch4/section_12/4.12.22_level set.txt

.. include:: /chapters/ch4/section_12/4.12.23_voltage.txt

.. include:: /chapters/ch4/section_12/4.12.25_efield.txt

.. include:: /chapters/ch4/section_12/4.12.26_enorm.txt

.. include:: /chapters/ch4/section_12/4.12.27_shear_rate.txt

.. include:: /chapters/ch4/section_12/4.12.28_vort_dir.txt

.. include:: /chapters/ch4/section_12/4.12.29_vort_lambda.txt

.. include:: /chapters/ch4/section_12/4.12.30_porous_sat.txt

.. include:: /chapters/ch4/section_12/4.12.31_porous_unsat.txt

.. include:: /chapters/ch4/section_12/4.12.32_porous_liq.txt

.. include:: /chapters/ch4/section_12/4.12.33_porous_gas.txt

.. include:: /chapters/ch4/section_12/4.12.34_porous_deform.txt

.. include:: /chapters/ch4/section_12/4.12.35_porous_energy.txt

.. include:: /chapters/ch4/section_12/4.12.36_surf_charge.txt

.. include:: /chapters/ch4/section_12/4.12.37_shell_tension.txt

.. include:: /chapters/ch4/section_12/4.12.38_shell_curvature.txt

.. include:: /chapters/ch4/section_12/4.12.39_shell_angle.txt

.. include:: /chapters/ch4/section_12/4.12.40_shell_diff_flux.txt

.. include:: /chapters/ch4/section_12/4.12.41_shell_diff_curv.txt

.. include:: /chapters/ch4/section_12/4.12.42_shell_normal.txt

.. include:: /chapters/ch4/section_12/4.12.43_shell_surf_curv.txt

.. include:: /chapters/ch4/section_12/4.12.44_shell_surf_div_v.txt

.. include:: /chapters/ch4/section_12/4.12.45_grad_v_dot_n1, grad_v_dot_n2, grad_v_dot_n3.txt

.. include:: /chapters/ch4/section_12/4.12.46_n_dot_curl_v.txt

.. include:: /chapters/ch4/section_12/4.12.47_acous_preal.txt

.. include:: /chapters/ch4/section_12/4.12.48_acous_pimag.txt

.. include:: /chapters/ch4/section_12/4.12.49_acous_reyn_stress.txt

.. include:: /chapters/ch4/section_12/4.12.50_potential1.txt

.. include:: /chapters/ch4/section_12/4.12.51_potential2.txt

.. include:: /chapters/ch4/section_12/4.12.52_lubp.txt

.. include:: /chapters/ch4/section_12/4.12.53_lubp_2.txt

.. include:: /chapters/ch4/section_12/4.12.55_shell_energy.txt

.. include:: /chapters/ch4/section_12/4.12.56_shell_filmp.txt

.. include:: /chapters/ch4/section_12/4.12.57_shell_filmh.txt

.. include:: /chapters/ch4/section_12/4.12.58_shell_partc.txt

.. include:: /chapters/ch4/section_12/4.12.59_shell_sat_closed.txt

.. include:: /chapters/ch4/section_12/4.12.61_shell_sat_gasn.txt

.. include:: /chapters/ch4/section_12/4.12.63_shell_sat_open.txt

.. include:: /chapters/ch4/section_12/4.12.64_shell_sat_open_2.txt

.. include:: /chapters/ch4/section_12/4.12.66_shell_deltah.txt

.. include:: /chapters/ch4/section_12/4.12.68_end_of_eq.txt

.. include:: /chapters/ch4/section_12/4.12.69_end_of_mat.txt
