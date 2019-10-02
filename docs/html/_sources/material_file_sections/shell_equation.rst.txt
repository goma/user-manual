Shell Equation Properties and Models
########################################

In this section we list all “material-region” specific models and properties associated with
GOMA’s extensive shell equation capability. Currently we have specialized shell equations for
Reynolds lubrication flow (lubp), open Reynolds film flow (shell_film_H), energy (shell_energy,
convection and diffusion, coupled with lubrication), thin porous media (closed cell and open cell),
melting and phase change and more. While many of these cards are actual material properties,
most are geometry and kinematic related. The most appropriate place for these cards are region/
material files because they are actually boundary conditions and related parameters which arise
from the reduction of order (integration through the thin film). For more information, please see
the shell-equation tutorial (GT-036).

.. include:: /chapters/ch5/section_7/5.7.1_upper_height_function_constants.txt

.. include:: /chapters/ch5/section_7/5.7.2_lower_height_function_constants.txt

.. include:: /chapters/ch5/section_7/5.7.3_upper_velocity_function_constants.txt

.. include:: /chapters/ch5/section_7/5.7.4_lower_velocity_function_constants.txt

.. include:: /chapters/ch5/section_7/5.7.5_upper_contact_angle.txt

.. include:: /chapters/ch5/section_7/5.7.6_lower_contact_angle.txt

.. include:: /chapters/ch5/section_7/5.7.7_lubrication_fluid_source.txt

.. include:: /chapters/ch5/section_7/5.7.8_lubrication_momentum_source.txt

.. include:: /chapters/ch5/section_7/5.7.9_turbulent_lubrication_model.txt

.. include:: /chapters/ch5/section_7/5.7.10_shell_energy_source_QCONV.txt

.. include:: /chapters/ch5/section_7/5.7.11_shell_energy_source_sliding_contact.txt

.. include:: /chapters/ch5/section_7/5.7.12_shell_energy_source_viscous_dissipation.txt

.. include:: /chapters/ch5/section_7/5.7.13_shell_energy_source_external.txt

.. include:: /chapters/ch5/section_7/5.7.14_FSI_deformation_model.txt

.. include:: /chapters/ch5/section_7/5.7.15_film_evaporation_model.txt

.. include:: /chapters/ch5/section_7/5.7.16_disjoining_pressure_model.txt

.. include:: /chapters/ch5/section_7/5.7.17_diffusion_coefficient_model.txt

.. include:: /chapters/ch5/section_7/5.7.18_porous_shell_radius.txt

.. include:: /chapters/ch5/section_7/5.7.19_porous_shell_height.txt

.. include:: /chapters/ch5/section_7/5.7.21_porous_shell_closed_porosity.txt

.. include:: /chapters/ch5/section_7/5.7.22_porous_shell_closed_gas_pressure.txt

.. include:: /chapters/ch5/section_7/5.7.23_porous_shell_atmospheric_pressure.rst

.. include:: /chapters/ch5/section_7/5.7.24_porous_shell_reference_pressure.txt

.. include:: /chapters/ch5/section_7/5.7.25_porous_shell_cross_permeability.txt

.. include:: /chapters/ch5/section_7/5.7.26_porous_shell_gas_diffusivity.txt

.. include:: /chapters/ch5/section_7/5.7.27_porous_shell_gas_temperature_constant.txt

.. include:: /chapters/ch5/section_7/5.7.28_porous_shell_henrys_law_constant.txt
