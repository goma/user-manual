Post Processing Specifications
##################################

This section lists the post-processing options that are accessible within *Goma*. Each card below triggers calculations of the nodal values of a given function, which are then written to the EXODUS II output file. Normally these values are smoothed before writing them to the output file. For most of these cards a keyword is the only input; if the keyword is **yes**, the post-processing variable is calculated and written to the file; if the keyword is **no**, no output is generated for that variable. All of these cards are optional and can appear in any order.

The sections below list the post-processing options and a brief description of each. *Users are cautioned - for large, time-dependent runs, the output of many post-processing variables may lead to excessively large EXODUS II output files*.

.. include:: /chapters/ch4/section_13/4.13.1_stream_function.txt

.. include:: /chapters/ch4/section_13/4.13.2_streamwise_normal_stress.txt

.. include:: /chapters/ch4/section_13/4.13.3_cross_stream_shear_rate.txt

.. include:: /chapters/ch4/section_13/4.13.4_mean_shear_rate.txt

.. include:: /chapters/ch4/section_13/4.13.5_pressure_contours.txt

.. include:: /chapters/ch4/section_13/4.13.6_fill_contours.txt

.. include:: /chapters/ch4/section_13/4.13.7_concentration_contours.txt

.. include:: /chapters/ch4/section_13/4.13.8_stress_contours.txt

.. include:: /chapters/ch4/section_13/4.13.9_first_invariant_of_strain.txt

.. include:: /chapters/ch4/section_13/4.13.10_second_invariant_of_strain.txt

.. include:: /chapters/ch4/section_13/4.13.11_third_invariant_of_strain.txt

.. include:: /chapters/ch4/section_13/4.13.12_velocity_divergence.txt

.. include:: /chapters/ch4/section_13/4.13.13_particle_velocity_divergence.txt

.. include:: /chapters/ch4/section_13/4.13.14_total_velocity_divergence.txt

.. include:: /chapters/ch4/section_13/4.13.15_electric_field.txt

.. include:: /chapters/ch4/section_13/4.13.16_electric_field_magnitude.txt

.. include:: /chapters/ch4/section_13/4.13.17_enormsq_field.txt

.. include:: /chapters/ch4/section_13/4.13.18_enormsq_field_norm.txt

.. include:: /chapters/ch4/section_13/4.13.19_viscosity.txt

.. include:: /chapters/ch4/section_13/4.13.21_density.txt

.. include:: /chapters/ch4/section_13/4.13.22_lame_mu.txt

.. include:: /chapters/ch4/section_13/4.13.23_lame_lambda.txt

.. include:: /chapters/ch4/section_13/4.13.24_von_mises_strain.txt

.. include:: /chapters/ch4/section_13/4.13.25_von_mises_stress.txt

.. include:: /chapters/ch4/section_13/4.13.27_navier_stokes_residuals.txt

.. include:: /chapters/ch4/section_13/4.13.28_moving_mesh_residuals.txt

.. include:: /chapters/ch4/section_13/4.13.29_mass_diffusion_vectors.txt

.. include:: /chapters/ch4/section_13/4.13.30_diffusive_mass_flux_vectors.txt

.. include:: /chapters/ch4/section_13/4.13.31_mass_fluxlines.txt

.. include:: /chapters/ch4/section_13/4.13.32_energy_conduction_vectors.txt

.. include:: /chapters/ch4/section_13/4.13.33_energy_fluxlines.txt

.. include:: /chapters/ch4/section_13/4.13.34_time_derivatives.txt

.. include:: /chapters/ch4/section_13/4.13.35_mesh_stress_tensor.txt

.. include:: /chapters/ch4/section_13/4.13.36_real_solid_stress_tensor.txt

.. include:: /chapters/ch4/section_13/4.13.37_mesh_strain_tensor.txt

.. include:: /chapters/ch4/section_13/4.13.38_viscoplastic_def_grad_tensor.txt

.. include:: /chapters/ch4/section_13/4.13.39_lagrangian_convection.txt

.. include:: /chapters/ch4/section_13/4.13.40_normal_and_tangent_vectors.txt

.. include:: /chapters/ch4/section_13/4.13.41_error_zz_velocity.txt

.. include:: /chapters/ch4/section_13/4.13.42_error_zz_heat_flux.txt

.. include:: /chapters/ch4/section_13/4.13.43_error_zz_pressure.txt

.. include:: /chapters/ch4/section_13/4.13.44_user_defined_post_processing.txt

.. include:: /chapters/ch4/section_13/4.13.45_porous_saturation.txt

.. include:: /chapters/ch4/section_13/4.13.46_total_density_of_solvents_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.47_density_of_solvents_in_gas_phase_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.48_density_of_liquid_phase_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.49_gas_phase_darcy_velocity_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.50_liquid_phase_darcy_velocity_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.52_liquid_phase_darcy_velocity_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.54_capillary_pressure_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.56_grid_peclet_number_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.57_supg_velocity_in_porous_media.txt

.. include:: /chapters/ch4/section_13/4.13.58_vorticity_vector.txt
