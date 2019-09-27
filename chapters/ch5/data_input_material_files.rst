===============================
**Data Input-- Material Files**
===============================

The material (“mat”) file for *Goma* contains a description or specification of all the properties
required for the multi-physics capabilities of *Goma*. A separate *.mat* file must be developed for
each material present in each simulation. The *mat* file (see Figure 5) is split into seven sections:
(1) Physical Properties (Section 5.1), (2) Mechanical Properties and Constitutive Equations
(Section 5.2), (3) Thermal Properties (Section 5.3), (4) Electrical Properties (Section 5.4), (5)
Microstructure Properties (Section 5.5), (6) Species Properties (Section 5.6), and (7) Source
Terms (Section 5.7).

Each section in this chapter discusses a separate part of the material file specification and it
indicates the data cards or input records that may be used, followed by the options available for
each individual record (or line in the file) and the necessary input data/parameters. All input data
are specified in a free field format with successive data items separated by blanks or tabs. In this
version of the user’s manual, a new format has been instituted in which each record is presented in
a template structure. This template has eight parts: 1) a title, which is also the card name, 2) a
syntax, which is enclosed in a framed box and shows the proper contents of the card, 3) a
Description/Usage section, which presents the user options and descriptions of proper input
records, 4) an Example, 5) a Technical Discussion to provide relevant information to help the user
understand how to select from among various options or how to properly determine the desired
parameters, 6) a Theory to provide an understanding of the physics and mechanics that have been
implemented or are being exercised, 7) a FAQs section to present important user experience, and
8) a Reference section to identify citations and/or provide background information to the user.
This is a more lengthly but a more complete form for documenting and instructing users of *Goma*.

The syntax entry denotes a unique string for each input record which *Goma* parses in the input
file. All words in these unique strings are separated by a single white space and because the code
parses for these exact strings, the parser becomes case sensitive. The identifying string for a
particular specification is followed by an ‘=’ character. Following this character will be all
additional data for that record, if any. In the syntax box, this additional data is symbolically
represented by one or more variables with some appropriate delimiters. Typically, the user will
find a variable called *model_name* enclosed in **curly braces** ‘{}’; this would then be followed by
a description of specific options for *model_name* in the Description/Usage section. The curly
braces indicate a required input and that the user must select one of the offered options for
*model_name*. **Required parameters**, if any, for the model option are enclosed in **angle brackets** ‘<
>’, while **optional parameters** for model_name are enclosed in **square brackets** ‘[ ]’. Following
the ‘=’ character, the user may use white space freely between and among the remaining
parameters on the command line.

Figure 4 illustrates a typical material file. The section *headers*, e.g., “--- Physical Properties”, are
user comments that are not processed by the input parser. In all sections of this chapter,
*model_name* is a character string and *floating_point_const_list* is a list of floating point numbers
of arbitrary length separated by a comma or one or more white spaces. The remainder of this
chapter covers each card (line) of the material-description file in detail. For each parameter that is
not dimensionless, base units are indicated in square brackets ( [ ] ) at the end of the syntax line;
the base units are those indicated in the Nomenclature section of this document. Empty brackets (
[ ] ) denote dimensionless parameters, while those without units or brackets are simply model
names, other strings, or integers. Several model parameters, e.g., Diffusivity, where the model
options include other than the **CONSTANT** type with a single input value, identify the units as
[**varied**]. In these cases, the parameter units will be listed for the **CONSTANT** model option and
the units for individual input parameters will be identified in the parameter description.


.. figure:: /figures/340_goma_physics.png
	:align: center
	:width: 90%

	Sample material-description file format. Lines highlighted in bold-face type are required.

All property models will eventually have a **USER** and a **USER_GEN** option. When the former is
selected, the user must add the user model to the appropriate routine in the file *user_mp.c.* This
file contains a template to simplify the implementation of a model in a full-Newton context, but
has the restriction that none of the models can contain a dependence on gradients of variables. For
more complex models, which contain such dependencies, the user must resort to the more
sophisticated mechanism that comprise the routines in *user_mp_gen.c*

A relatively new capability/model available on many of the properties is a table-lookup feature.
That is, if the model is of type **TABLE**, then a linear or bilinear interpolation is used to extract the
material property value from a table of numbers representing the dependence. The best way to
explain this is with an example. Often times a property is dependent on temperature, or related
dependent variable. If discrete data is available of the property value at various temperatures, as
from a spreadsheet, then such a table can be read and with appropriate interpolation operations the
property value is determined. Throughout the material property options, the reader might see aat
**TABLE** option. The syntax for the input of that option is as follows:

<**Property name**> = **TABLE** *<integer1> <character_string1> [character_string2] {LINEAR |
BILINEAR} [integer2] [FILE = filenm]*

Here, the integers, character strings and floats are defined as follows:

<integer1> - the number N of columns in the table. The first N-1 columns are the values of the
independent variables (e.g. temperature, concentration, etc.) and the final Nth column is the
property value. This number is usually 2.

<character_string1> - Required variable name for first column. Valid variable names are
**TEMPERATURE, MASS_FRACTION, SPECIES, CAP_PRES, FAUX_PLASTIC,** and
**LOWER_DISTANCE**. The last three are specific to the Saturation model of porous flow,
the LAME Mu model, and the Lubrication Height function model, respectively.
Temperature and mass fraction dependence are available in all properties with a **TABLE** option
which make sense.

[character_string2] - Optional second variable name for bi-linear lookup dependence. This is
exploratory.

{*LINEAR | BILINEAR*} - type of interpolation

[*integer2*] - species number required only for MASS_FRACTION, SPECIES, and
FAUX_PLASTICITY variables.

[*FILE = <filenm>*] - The optional keyword ‘FILE=’ indicates that the table data is to be read
from a separate file identified by <filenm>. Each row of the table corresponds to one variable
value, and is input in free form CSV or space separated values. Note that if this ‘FILE=’ option is
not present then the data will be read from the input material file itself following the TABLE
model card. The end of the table is signaled by the keyword “END TABLE” (see example below).

Some examples are in order:

::

   Lame MU = TABLE 2 FAUX_PLASTIC 0 LINEAR FILE=stress_strain_comp.txt
   ...
   Lame MU = TABLE 2 TEMPERATURE LINEAR
   1. 293
   2. 300
   3. 425.
   END TABLE


Finally, before we get started, the following is an option added to allow existing Chemkin
material property databases to be read in, basically obviating the need to even read the material
(mat) file. The detailed description of input records provided in this chapter thus applies to the
case when the Default Database is set to GOMA_MAT.

**Physical Properties**
#######################

The intrinsic property of materials essential to *Goma* is the density. As *Goma* presumes that all
materials are incompressible, density is a constant in the governing differential equations.
However, several options for models of density are present in the code because numerous
processes lead to density changes, though during any analysis cycle, the density is constant.

.. include:: /chapters/ch5/section_1/5.0.1_default_database.txt

.. include:: /chapters/ch5/section_1/5.1.1_density.txt

**Mechanical Properties and Constitutive Equations**
####################################################

This section of the material property input specifies the type of model, for both solids and fluids,
that relates stress and strain (or strain-rate) as well as the various parameters for these models.
Models for solids are relatively simple compared to solid mechanics codes but cover the primary
needs in fluid-solid problems. The models for fluids are quite extensive, covering Newtonian,
generalized-Newtonian, rate-dependent models, thermally-dependent models, curing and particleladen
models and combinations of these. These properties are used in the solid and fluid
momentum conservation equations.

.. include:: /chapters/ch5/section_2/5.2.1_solid_constitutive_equation.txt

.. include:: /chapters/ch5/section_2/5.2.2_plasticity_equation.txt

.. include:: /chapters/ch5/section_2/5.2.3_convective_lagrangian_velocity.txt

.. include:: /chapters/ch5/section_2/5.2.4_lame_mu.txt

.. include:: /chapters/ch5/section_2/5.2.5_lame_lambda.txt

.. include:: /chapters/ch5/section_2/5.2.6_stress_free_solvent_vol_frac.txt

.. include:: /chapters/ch5/section_2/5.2.7_solid_thermal_expansion.txt

.. include:: /chapters/ch5/section_2/5.2.8_solid_reference_temperature.txt

.. include:: /chapters/ch5/section_2/5.2.9_plastic_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.10_evp_yield_stress.txt

.. include:: /chapters/ch5/section_2/5.2.11_pseudo_solid_constitutive_equation.txt

.. include:: /chapters/ch5/section_2/5.2.12_pseudo_solid_lame_mu.txt

.. include:: /chapters/ch5/section_2/5.2.13_pseudo_solid_lame_lambda.txt

.. include:: /chapters/ch5/section_2/5.2.14_liquid_constitutive_equation.txt

.. include:: /chapters/ch5/section_2/5.2.15_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.16_low_rate_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.17_power_law_exponent.txt

.. include:: /chapters/ch5/section_2/5.2.18_high_rate_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.19_time_constant.txt

.. include:: /chapters/ch5/section_2/5.2.20_aexp.txt

.. include:: /chapters/ch5/section_2/5.2.21_thermal_exponent.txt

.. include:: /chapters/ch5/section_2/5.2.22_thermal_wlf_constant2.txt

.. include:: /chapters/ch5/section_2/5.2.23_yield_stress.txt

.. include:: /chapters/ch5/section_2/5.2.24_yield_exponent.txt

.. include:: /chapters/ch5/section_2/5.2.25_suspension_maximum_packing.txt

.. include:: /chapters/ch5/section_2/5.2.26_suspension_species_number.txt

.. include:: /chapters/ch5/section_2/5.2.27_cure_gel_point.txt

.. include:: /chapters/ch5/section_2/5.2.28_cure_a_exponent.txt

.. include:: /chapters/ch5/section_2/5.2.29_cure_b_exponent.txt

.. include:: /chapters/ch5/section_2/5.2.30_cure_species_number.txt

.. include:: /chapters/ch5/section_2/5.2.31_unreacted_gel_temperature.txt

.. include:: /chapters/ch5/section_2/5.2.32_polymer_constitutive_equation.txt

.. include:: /chapters/ch5/section_2/5.2.33_polymer_stress_formulation.txt

.. include:: /chapters/ch5/section_2/5.2.34_polymer_weight_function.txt

.. include:: /chapters/ch5/section_2/5.2.35_polymer_shift_function.txt

.. include:: /chapters/ch5/section_2/5.2.36_discontinuous_jacobian_formulation.txt

.. include:: /chapters/ch5/section_2/5.2.38_polymer_weighting.txt

.. include:: /chapters/ch5/section_2/5.2.39_adaptive_viscosity_scaling.txt

.. include:: /chapters/ch5/section_2/5.2.40_polymer_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.41_polymer_time_constant.txt

.. include:: /chapters/ch5/section_2/5.2.42_mobility_parameter.txt

.. include:: /chapters/ch5/section_2/5.2.43_ptt_xi_parameter.txt

.. include:: /chapters/ch5/section_2/5.2.44_ptt_epsilon_parameter.txt

.. include:: /chapters/ch5/section_2/5.2.45_surface_tension.txt

.. include:: /chapters/ch5/section_2/5.2.46_second_level_set_conductivity.txt

.. include:: /chapters/ch5/section_2/5.2.48_second_level_set_density.txt

.. include:: /chapters/ch5/section_2/5.2.50_second_level_set_heat_capacity.txt

.. include:: /chapters/ch5/section_2/5.2.52_second_level_set_momentum_source.txt

.. include:: /chapters/ch5/section_2/5.2.54_second_level_set_viscosity.txt

.. include:: /chapters/ch5/section_2/5.2.56_shell_bending_stiffness.txt

**Thermal Properties**
######################

In this section of material properties, the user specifies the parameters of models for Fourier heat
conduction and thermally-induced density changes (by volume expansion) and parameters
controlling the onset of phase changes. Properties governing energy transport by convection,
radiation and diffusion are specified elsewhere.

.. include:: /chapters/ch5/section_3/5.3.1_heat_flux_model.txt

.. include:: /chapters/ch5/section_3/5.3.2_conductivity.txt

.. include:: /chapters/ch5/section_3/5.3.3_heat_capacity.txt

.. include:: /chapters/ch5/section_3/5.3.4_volume_expansion.txt

.. include:: /chapters/ch5/section_3/5.3.5_reference_temperature.txt

.. include:: /chapters/ch5/section_3/5.3.6_liquidus_temperature.txt

.. include:: /chapters/ch5/section_3/5.3.7_solidus_temperature.txt

.. include:: /chapters/ch5/section_3/5.3.8_energy_weight_function.txt

**Electrical Properties**
#########################

Models for material electrical properties are simple or specialized, being very applicationoriented.
The primary need for modeling electrical potential effects are to activate mass transport
mechanisms that are charge-dependent.

.. include:: /chapters/ch5/section_4/5.4.1_electrical_conductivity.txt

.. include:: /chapters/ch5/section_4/5.4.2_electrical_permittivity.txt

.. include:: /chapters/ch5/section_4/5.4.3_microstructure_properties.txt

.. include:: /chapters/ch5/section_4/5.4.4_media_type.txt

.. include:: /chapters/ch5/section_4/5.4.6_porosity.txt

.. include:: /chapters/ch5/section_4/5.4.7_permeability.txt

.. include:: /chapters/ch5/section_4/5.4.8_liquid_phase_compressibility.txt

.. include:: /chapters/ch5/section_4/5.4.9_liquid_phase_reference_pressure.txt

.. include:: /chapters/ch5/section_4/5.4.10_flowing_liquid_viscosity.txt

.. include:: /chapters/ch5/section_4/5.4.11_inertia_coefficient.txt

.. include:: /chapters/ch5/section_4/5.4.12_capillary_network_stress.txt

.. include:: /chapters/ch5/section_4/5.4.13_rel_gas_permeability.txt

.. include:: /chapters/ch5/section_4/5.4.14_rel_liq_permeability.txt

.. include:: /chapters/ch5/section_4/5.4.15_saturation.txt

.. include:: /chapters/ch5/section_4/5.4.16_porous_weight_function.txt

.. include:: /chapters/ch5/section_4/5.4.17_porous_mass_lumping.txt

.. include:: /chapters/ch5/section_4/5.4.19_porous_diffusion_constitutive_equation.txt

.. include:: /chapters/ch5/section_4/5.4.20_porous_gas_diffusivity.txt

.. include:: /chapters/ch5/section_4/5.4.21_porous_latent_heat_vaporization.txt

.. include:: /chapters/ch5/section_4/5.4.22_porous_latent_heat_fusion.txt

.. include:: /chapters/ch5/section_4/5.4.23_porous_vapor_pressure.txt

.. include:: /chapters/ch5/section_4/5.4.24_porous_liquid_volume_expansion.txt

.. include:: /chapters/ch5/section_4/5.4.25_porous_gas_constants.txt

**Species Properties**
######################

The section of material properties defines the models and parameters governing diffusive mass
transport, whether it be ordinary, forced or thermal diffusion of species. Included in those
generalizations are electrical potential-driven species movements. Models include those for single
species, especially particle-laden suspensions, binary species and multi-component systems.
Models for various equations of mass transport are included, various models of diffusion
properties, different representations of species by means of molar, mass or volume concentrations,
various models of vapor pressure for multiphase flow and on material boundaries for lumped
parameter analyses, and properties for charged species.

.. include:: /chapters/ch5/section_5/5.5.1_number_of_species.txt

.. include:: /chapters/ch5/section_5/5.5.2_diffusion_constitutive_equation.txt

.. include:: /chapters/ch5/section_5/5.5.3_species_weight_function.txt

.. include:: /chapters/ch5/section_5/5.5.4_number_of_chemical_reactions.txt

.. include:: /chapters/ch5/section_5/5.5.5_reaction_rate.txt

.. include:: /chapters/ch5/section_5/5.5.6_thermodynamic_potential.txt

.. include:: /chapters/ch5/section_5/5.5.7_interfacial_area.txt

.. include:: /chapters/ch5/section_5/5.5.8_butler_volmer_j.txt

.. include:: /chapters/ch5/section_5/5.5.9_butler_volmer_ij.txt

.. include:: /chapters/ch5/section_5/5.5.10_solution_temperature.txt

.. include:: /chapters/ch5/section_5/5.5.11_porosity.txt

.. include:: /chapters/ch5/section_5/5.5.12_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.13_shear_rate_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.14_viscosity_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.15_curvature_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.16_fickian_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.17_gravity_based_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.18_q_tensor_diffusivity.txt

.. include:: /chapters/ch5/section_5/5.5.19_species_time_integration.txt

.. include:: /chapters/ch5/section_5/5.5.20_advective_scaling.txt

.. include:: /chapters/ch5/section_5/5.5.21_latent_heat_vaporization.txt

.. include:: /chapters/ch5/section_5/5.5.22_latent_heat_fusion.txt

.. include:: /chapters/ch5/section_5/5.5.23_vapor_pressure.txt

.. include:: /chapters/ch5/section_5/5.5.24_species_volume_expansion.txt

.. include:: /chapters/ch5/section_5/5.5.25_standard_state_chemical_potential.txt

.. include:: /chapters/ch5/section_5/5.5.26_pure_species_chemical_potential.txt

.. include:: /chapters/ch5/section_5/5.5.27_chemical_potential.txt

.. include:: /chapters/ch5/section_5/5.5.28_reference_concentration.txt

.. include:: /chapters/ch5/section_5/5.5.29_molecular_weight.txt

.. include:: /chapters/ch5/section_5/5.5.30_specific_volume.txt

.. include:: /chapters/ch5/section_5/5.5.31_molar_volume.txt

.. include:: /chapters/ch5/section_5/5.5.32_charge_number.txt

.. include:: /chapters/ch5/section_5/5.5.33_non_condensable_molecular_weight.txt

.. include:: /chapters/ch5/section_5/5.5.34_non_volatile_molar_volume.txt

.. include:: /chapters/ch5/section_5/5.5.35_non_volatile_specific_volume.txt

.. include:: /chapters/ch5/section_5/5.5.36_flory_huggins_parameters.txt

**Source Terms**
################

Source term models cover the internal generation of pressure (in fluids and solids), energy,
species component mass and electrical potential for each of the main differential equations.
Several representations are available for fluids, and the user should be aware of some
consistencies required with density models (see details below). For all of the source models, the
user must heed the following warning:

**Make sure the equation term multipliers for the source terms being used are set to unity**
*(Section 4.12 - Problem Description and Equation specification in Volume 1).*

.. include:: /chapters/ch5/section_6/5.6.1_navier_stokes_source.txt

.. include:: /chapters/ch5/section_6/5.6.2_solid_body_source.txt

.. include:: /chapters/ch5/section_6/5.6.3_mass_source.txt

.. include:: /chapters/ch5/section_6/5.6.4_heat_source.txt

.. include:: /chapters/ch5/section_6/5.6.5_species_source.txt

.. include:: /chapters/ch5/section_6/5.6.6_current_source.txt

.. include:: /chapters/ch5/section_6/5.6.7_initialize.txt

**Shell Equation Properties and Models**
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

References
##########

.. include:: /chapters/ch5/section_7/5.7.31_references.txt

Appendix 1: Goma Documentation Lists
####################################

The documents identified in this appendix constitute a current list of instructional, technical and
reference material for Goma and the CRMPC Consortium of Companies.

.. include:: /chapters/ch5/section_7/5.7.32_appendix_1.txt

Appendix 2: Using Goma in Library Mode
######################################

.. include:: /chapters/ch5/section_7/5.7.33_appendix_2.txt

Index
#####

.. include:: /chapters/ch5/section_7/5.7.34_index.txt



