====================================
**Data Input - Problem Description**
====================================

The **input file** for *Goma* contains the overall description of the problem to be solved together with
instructions on solution strategy. The file (cf. sample in Figure 3) is split into sixteen sections: (1)
File Specifications (Section 4.1) which directs I/O, (2) General Specifications (Section 4.2), (3)
Time Integration Specifications (Section 4.3), (4) Continuation Specifications (Section 4.4), (5)
Hunting Specifications (Section 4.5), (6) Augmenting conditions (Section 4.6), (7) Solver
Specifications (Section 4.7), (8) Eigensolver Specifications (Section 4.8), (9) Geometry
Specifications, (Section 4.9) (10) Boundary Condition Specifications (Section 4.10), (11)
Rotation Specifications (Section 4.11), (12) Problem Description (Section 4.12), (13) Post
Processing Specifications (Section 4.13), (14) Post Processing Fluxes and Data (Section 4.14),
(15) Post Processing Particle Traces (Section 4.15) and (16) Volumetric Integration (Section
4.16). Sections (1), (2), (3), (7), (10), (12), and (13) are required. The rest are optional, depending
on the problem type being solved.

Each section in this chapter discusses a separate part of the input file specification and it indicates
the data cards or input records that may be used, followed by the options available for each
individual record (or line in the file) and the necessary input data/parameters. All input data are
specified in a free field format with successive data items separated by blanks or tabs. In this
version of the user’s manual, a new format has been instituted in which each record is presented in
a template structure. This template has eight parts: 1) a title, which is also the card name, 2) a
syntax, which is enclosed in a framed box and shows the proper contents of the card, 3) a
Description/Usage section, which presents the user options and descriptions of proper input
records, 4) an Example, 5) a Technical Discussion to provide relevant information to help the user
understand how to select from among various options or how to properly determine the desired
parameters, 6) a Theory to provide an understanding of the physics and mechanics that have been
implemented or are being exercised, 7) a FAQs section to present important user experience, and
8) a Reference section to identify citations and/or provide background information to the user.
This is a more lengthy but a more complete form for documenting and instructing users of *Goma*.

The syntax entry denotes a unique string for each input record which *Goma* parses in the input
file. All words in these unique strings are separated by a single white space and because the code
parses for these exact strings, the parser becomes case sensitive. The identifying string for a
particular specification is followed by an ‘=’ character. Following this character will be all
additional data for that record, if any. In the syntax box, this additional data is symbolically
represented by one or more variables with some appropriate delimiters. Typically, the user will
find a variable called *model_name* enclosed in **curly braces** ‘{}’; this would then be followed by
a description of specific options for *model_name* in the Description/Usage section. The curly
braces indicate a required input and that the user must select one of the offered options for
*model_name*. Required parameters, if any, for the model option are enclosed in **angle brackets** ‘<
>’, while optional parameters for *model_name* are enclosed in **square brackets** ‘[ ]’. Following
the ‘=’ character, the user may use white space freely between and among the remaining
parameters on the command line.

The nature of the input parser allows the user to freely comment the input deck in any way, so
long as the character strings in the comments do not contain the exact strings described in this
section of the manual at the beginning of the comment line. Simply for the sake of uniformity, it is
recommended that a comment card convention be adopted, i.e., placing some delimiting symbol
(e.g., $, #, \, etc.) at the *beginning* of each comment line. Moreover, employing some of the basic
text processing capabilities provided in the ACCESS system (Sjaardema, 1993) makes it possible
to connect both the model generation input file, e.g., FASTQ or CUBIT, with the input deck for
*Goma*. For example, the *“include”* statement in APREPRO (Sjaardema, 1992) makes it
convenient to include geometrical information contained in a FASTQ input file into the *Goma*
input file for use with commands like *PLANE* and *SPLINE* that make use of global problem
geometry. APREPRO also enables a user to generate customized model parameterizations with
the algebraic preprocessing capability (demonstrated in *Goma* Tutorials). Finally, employing a
text preprocessor like APREPRO enables the analyst to attach more meaningful labels to entities
such as side sets, node sets and element blocks than the internal names (which are simple integer
identifiers).

**The order of the input cards is significant**; omitting a required card will often result in an error
message from *Goma*. To avoid such errors, a good strategy is to copy a current version of a
working input file and then make changes to it. However, as noted below, some cards are optional.
Some file sections, such as boundary condition specification section and equation specification
section, are not order dependent, but number dependent, as only the number of boundary
conditions or equations which are specified by the “Number of BC” and “Number of EQ” cards
will be read (regardless of the number of cards in the file). That is, after the specified number of
individual equation or boundary condition cards is read, any remaining cards are ignored. Figure
3 shows a sample problem description input deck, indicating some optional and required cards
(lines). All possible cards and card sections are not identified in this figure as they are too
numerous. The remainder of this chapter describes each card in detail.

A final note to the user (and reader) of this manual pertains to backward compatibility and
obsolescence. There are several input records that have been superseded or have simply been
dropped from usage as the software has evolved. Rather than eliminate all of these inputs cards
immediately and cause some head-scratching about input cards which exist in users old input
decks, the decision was made to leave these cards in the current manual and simply document the
fact that they are no longer used (and in some cases why this is so). In the CD version of the
*Goma* Document System, these cards will be removed at a future date and no reference made to
them again.

.. figure:: /figures/003_goma_physics.png
	:align: center
	:width: 90%

	Sample problem description input deck. Italic type denotes required data cards (lines) and plain type denotes optional cards or cards that in number correspond to the designation above them, e.g., “Number of BC” or “Number of EQ”. (a) These cards are optional if the “steady” option is chosen on the Time Integration card. (b) This group of cards is repeated for each different material block in the EXODUS II database file. (c) These cards are all optional and can appear in any order. Please check this manual for
	numerous new post processing options.

**File Specification**
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

**General Specifications**
##########################

This section of input records covers additional I/O requests and specifications, including parallel
file I/O information, initial-guess directives (viz., whether a restart comes from a neutral file or
another exoII file), individual field variable initialization, debugging options, developer
diagnostic options, etc. This section and several of its input records are required, as indicated
below.

.. include:: /chapters/ch4/section_2/4.2.1_number_of_processors.txt

.. include:: /chapters/ch4/section_2/4.2.2_output_level.txt

.. include:: /chapters/ch4/section_2/4.2.3_debug.txt

.. include:: /chapters/ch4/section_2/4.2.4_number_of_jacobian_file_dumps.txt

.. include:: /chapters/ch4/section_2/4.2.5_initial_guess.txt

.. include:: /chapters/ch4/section_2/4.2.7_initialize.txt

.. include:: /chapters/ch4/section_2/4.2.9_external_field.txt

.. include:: /chapters/ch4/section_2/4.2.10_export_field.txt

.. include:: /chapters/ch4/section_2/4.2.11_external_pixel_field.txt

.. include:: /chapters/ch4/section_2/4.2.12_pressure_datum.txt

.. include:: /chapters/ch4/section_2/4.2.13_anneal_mesh_on_output.txt

**Time Integration Specifications**
###################################

The first card in this section dictates whether the problem is a steady state or transient simulation.
This card is required. If the *steady state* option is chosen, then the remaining input records are not
required, as the rest of the records are used to set parameters for transient simulations, e.g., time
step size, time step error control, etc. Some records are optional even for a transient simulation, as
indicated below. It should be noted that the mass-matrix term multiplier in the *Problem
Description section* (see, for example, the *EQ=* cards), must be set to one (1) for the transient run
to evolve the fields in time. The only equations that are taken as purely quasi static are the
*EQ=mesh* equations for the situation in which the *Mesh Motion* type is *Arbitrary*.

In addition to the transient parameter information, some Level-Set function information is also
supplied to *Goma* in this section. The method of Level-Sets is used to track fluid-fluid or fluidsolid
interfaces in an Eulerian fashion, making the problem inherently transient.

.. include:: /chapters/ch4/section_3/4.3.1_time_integration.txt

.. include:: /chapters/ch4/section_3/4.3.2_delta_t.txt

.. include:: /chapters/ch4/section_3/4.3.3_maximum_number_of_time_steps.txt

.. include:: /chapters/ch4/section_3/4.3.4_maximum_time.txt

.. include:: /chapters/ch4/section_3/4.3.5_minimum_time_step.txt

.. include:: /chapters/ch4/section_3/4.3.6_maximum_time_step.txt

.. include:: /chapters/ch4/section_3/4.3.7_minimum_resolved_time_step.txt

.. include:: /chapters/ch4/section_3/4.3.8_courant_number_limit.txt

.. include:: /chapters/ch4/section_3/4.3.9_time_step_parameter.txt

.. include:: /chapters/ch4/section_3/4.3.10_time_step_error.txt

.. include:: /chapters/ch4/section_3/4.3.11_printing_frequency.txt

.. include:: /chapters/ch4/section_3/4.3.12_second_frequency_time.txt

.. include:: /chapters/ch4/section_3/4.3.13_initial_time.txt

.. include:: /chapters/ch4/section_3/4.3.14_fill_subcycle.txt

.. include:: /chapters/ch4/section_3/4.3.15_fill_weight_function.txt

.. include:: /chapters/ch4/section_3/4.3.16_level_set_interface_tracking.txt

.. include:: /chapters/ch4/section_3/4.3.17_level_set_semi_lagrange.txt

.. include:: /chapters/ch4/section_3/4.3.18_level_set_subgrid_integration_depth.txt

.. include:: /chapters/ch4/section_3/4.3.20_level_set_subelement_integration.txt

.. include:: /chapters/ch4/section_3/4.3.21_level_set_adaptive_integration.txt

.. include:: /chapters/ch4/section_3/4.3.23_level_set_adaptive_order.txt

.. include:: /chapters/ch4/section_3/4.3.25_overlap_quadrature_points.txt

.. include:: /chapters/ch4/section_3/4.3.26_level_set_pspp_filtering.txt

.. include:: /chapters/ch4/section_3/4.3.27_level_set_length_scale.txt

.. include:: /chapters/ch4/section_3/4.3.28_level_set_initialize.txt

.. include:: /chapters/ch4/section_3/4.3.30_level_set_initialization_method.txt

.. include:: /chapters/ch4/section_3/4.3.31_level_set_periodic_planes.txt

.. include:: /chapters/ch4/section_3/4.3.32_level_set_control_width.txt

.. include:: /chapters/ch4/section_3/4.3.33_level_set_timestep_control.txt

.. include:: /chapters/ch4/section_3/4.3.34_level_set_renormalization_tolerance.txt

.. include:: /chapters/ch4/section_3/4.3.35_level_set_renormalization_method.txt

.. include:: /chapters/ch4/section_3/4.3.36_level_set_renormalization_frequency.txt

.. include:: /chapters/ch4/section_3/4.3.37_restart_time_integration_after_renormalization.txt

.. include:: /chapters/ch4/section_3/4.3.38_level_set_reconstruction_method.txt

.. include:: /chapters/ch4/section_3/4.3.39_level_set_contact_extension.txt

.. include:: /chapters/ch4/section_3/4.3.40_level_set_slave_surface.txt

.. include:: /chapters/ch4/section_3/4.3.42_ignore_level_set_dependencies.txt

.. include:: /chapters/ch4/section_3/4.3.43_force_initial_level_set_renormalization.txt

.. include:: /chapters/ch4/section_3/4.3.44_number_of_phase_functions.txt

.. include:: /chapters/ch4/section_3/4.3.45_phase_function_slave_surface.txt

.. include:: /chapters/ch4/section_3/4.3.46_phase_function_initialization_method.txt

.. include:: /chapters/ch4/section_3/4.3.47_phase_function_renormalization_tolerance.txt

.. include:: /chapters/ch4/section_3/4.3.49_phase_function_renormalization_method.txt

**Continuation Specifications**
###############################

This section of input records is used to direct all automatic continuation procedures. The entire
section is completely optional. Basically, automatic continuation can be accomplished in steady
state simulations (see *Time Integration* card) through any one or combination of parameters.
These parameters can be any one or combination of the input floats required on the boundary
condition cards (see Section 4.10) or material property cards (see Chapter 5). The cards in this
section are used to specify the parameters that will be marched automatically, the method of
marching (e.g. zero-order, first-order, multiparameter first-order, etc.), the limits of parameter
values, and other sundry options. Much of this capability can now be managed from the LOCA
library package (Library of Continuation Algorithms - Salinger et al. 2002).

Input specifications for this section of input records is discussed in a separate, comprehensive
manual (Gates, et. al., 2000); an update to this manual has been completed during the summer of
2006 (Labreche, et. al., 2006).

**Hunting Specifications**
##########################

The cards in this section are used to direct multiparameter continuation with the hunting
technique, which is a linear, multiparameter capability. The user is referred to discussions for the
*Continuation Specifications* for the important details of Continuation. As is true for the
*Continuation Specifications*, this entire section is completely optional. *Hunting Specification*
cards are used in conjunction with *Continuation Specifications.*

Input specifications for this section of input records is discussed in a separate, comprehensive
manual (Gates, et. al., 2000); an update to this manual has been completed during the summer of
2006 (Labreche, et. al., 2006).

**Augmenting Conditions Specifications**
########################################

Input records in this section are used to direct the solution of augmenting constraints on the base
system of differential equations. Addition of these conditions may require some programming in
the file user_ac.c. This entire section of the input deck is optional.

Input specifications for this section of input records is discussed in a separate, comprehensive
manual (Gates, et. al., 2000); an update to this manual has been completed during the summer of
2006 (Labreche, et. al., 2006).

**Solver Specifications**
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

**Eigensolver Specifications**
##############################

The ability to solve for the stability of a base flow is a very powerful tool. Often, the important
characteristics of a flow can be summarized in the answer to the question “is the flow stable?”.
Although the following cards are in active use at the time of this writing, sweeping changes are
coming to the eigensolver sections of *Goma*. In particular, the old code (called “eggroll”) is being
replaced with newer methods (in the ARPACK library), as well as being coupled to the
continuation and tracking algorithms (in the LOCA library).

Input specifications for this section of input records is discussed in a separate, comprehensive
manual (Gates, et. al., 2000); an update to this manual will be completed during the summer of
2006 (Labreche, et. al., 2006). Either of these manuals contains a thorough discussion of how to
successfully compute the stability and interesting modes of an underlying base flow.

**Geometry Specifications**
###########################

Geometry commands allow the user to import geometry from a pre-existing file in the ACIS
format (files with the “.sat” extension) and to generate geometry through primitive commands
within the *Goma* input file. This geometry is usually of an analytic nature which helps
convergence. It is used with the *MESH_CONSTRAINT* boundary condition (and soon to initialize
a level set). The main advantage in using geometry is one of practicality - once the geometry is
created to generate a mesh within CUBIT, that same geometry can be exported and used within
*Goma* without a laborious reconstruction of the geometry through other BC commands. At the
time of this writing, only 2D geometry had been verified.

The geometry capability is only available when the CGM library is linked in. A tutorial has been
written to assist in defining input at the present time. The user is referred to that document at the present time (GT-021.2, Common Geometry Model (CGM) Usage for GOMA, August 22, 2002,
M. M. Hopkins).

.. include:: /chapters/ch4/section_9/4.9.1_acis_file.txt

.. include:: /chapters/ch4/section_9/4.9.2_vertex.txt

.. include:: /chapters/ch4/section_9/4.9.3_end_of_vertex.txt

.. include:: /chapters/ch4/section_9/4.9.4_edge.txt

.. include:: /chapters/ch4/section_9/4.9.5_end_of_edge.txt

.. include:: /chapters/ch4/section_9/4.9.6_face.txt

.. include:: /chapters/ch4/section_9/4.9.7_end_of_face.txt

.. include:: /chapters/ch4/section_9/4.9.8_end_of_body.txt

.. include:: /chapters/ch4/section_9/4.9.9_exported_geometry_file.txt

**Boundary Condition Specifications**
#####################################

The broad range of mechanics capabilities that has been built into *Goma* necessitates an equally
broad range of boundary conditions (BCs) to provide all boundary condition information that the
differential equations specified in the *Problem Description* section will require for a well-posed
system. The BCs for *Goma* have been categorized according to the differential equation set to
which they apply. First are listed those boundary conditions which can be applied to any equation
followed by BCs for mesh, real solid, fluid momentum, energy, mass, continuity, porous, stress,
gradient, shear rate, fill and potential equations. Each boundary condition (BC) card follows a
general syntax as follows:

::

	BC = <bc_name> <bc_type> <bc_id> {integer_list}/{float_list}

The <bc_name> identifies the desired control of the physics/mechanics at the boundary as
identified by the <bc_type> and its associated <bc_id>. The <bc_type> is either nodeset, NS
(NODEBC or POINBC in EXODUS II) or sideset, SS (ELEMBC in EXODUS II) depending on
the <bc_name> and can be located in the problem domain by means of its flag or <bc_id> number
(set in EXODUS II). The {integer_list} and/or {float_list} specify parameters of the boundary
condition. Within each equation category are Dirichlet nodeset boundary conditions (i.e. T, U, V,
W, DX, DY, DZ, Y, S11, S12, S13, S22, S23, S33, G11, G12, G13, G21, G22, G23, G31, G32,
G33) that can be handled (i.e., processed) in two ways in *Goma*. The first way is application of the
BC as a “hard-set” on the primitive variable, and the second as a residual equation; differences in
these methods are discussed below. The cards belonging to this category have the following
general syntax:

::

	BC = <bc_name> <bc_type> <bc_id> <float1> <float2>

where <float2> flags whether a hard-set or residual equation is to be used.

Prior to introducing individual boundary conditions and their parameters, some general comments
regarding the first category of BCs, boundary condition types and the resolution of boundary
condition conflicts will be made.

**Any Equation Boundary Conditions** - There are several boundary condition types that are
not necessarily best binned with a specific equation type. The FIX, GD_* and TABLE boundary
condition types are general and can be applied to any equation type. A general description of these
types (called Category 1 conditions) is given below.

**Boundary Condition Types** - Beyond the generalized boundary conditions types and the Dirichlet
types, *Goma* has strong-collocated, weak form, and several others that are intrinsic to the
Galerkin finite element method; these are applied in a variety of ways. Because of this, boundary
conditions at a given node might interact in ways that produce unexpected results. For this reason,
it is important to understand the differing methods of application that occur in *Goma* and how
each affects the other. In addition, by cleverly mixing boundary conditions, the analyst is often
able to achieve a desired result, but only if the nature of each boundary condition is understood.
Toward this end, the user will find a *special label* assigned to each boundary condition, which,
with the ensuing explanation below, will provide each user with an understanding of how that BC
is applied within *Goma*.

On each boundary condition card, the boundary condition type appears in the **Description/Usage**
section. These are the following boundary condition types that will be found here:

* **DIRICHLET (DC)**
* **STRONGLY INTEGRATED (SIC)**
* **STRONGLY INTEGRATED EDGE (SIC_EDGE)**
* **COLLOCATED (PCC)**
* **COLLOCATED EDGE (PCC_EDGE)**
* **WEAKLY INTEGRATED (WIC)**

The following sections discuss the method of application of each boundary condition type along
with the implications of using each.

**DIRICHLET (DC):**

In the hierarchy of boundary conditions, Dirichlet conditions are at the top.
Nothing trumps a Dirichlet conditions. A Dirichlet condition is applied by
discarding all mechanics information related to a particular field variable that has
been accumulated at a given node and replacing it with a direct assignment of the
nodal unknown of that field with a fixed *a priori* value. Algorithmically, applying
a Dirichlet condition on a degree of freedom at a node involves zeroing the entire equation row, inserting a unity value on the diagonal element of the Jacobian
matrix, inserting a zero value at the appropriate place in the residual vector, and
inserting the known boundary condition value at the appropriate place in the
solution vector. This is referred to in many places as the “*hard set*” method. An
alternate formulation imposes the boundary condition by replacing the mechanics
equation at a node with the simple *residual equation*, EQUATION , where φ and φ0
are the nodal unknown field and its assigned value, respectively.The sensitivities
of this residual equation are entered into the Jacobian appropriately and solution
takes place normally.

Dirichlet conditions are strictly node-based. Neighbor nodes and shared elements
have no influence on them. For this reason, all Dirichlet conditions are applied to
nodesets. Furthermore, Dirichlet conditions are assigned the highest precedence in
terms of boundary conditions. If a Dirichlet condition appears at a node, it will be
applied. Any other boundary condition that could be applied will be discarded (at
that node).

Dirichlet conditions are limited, however in that they can only affect the nodal
value of a degree of freedom. Derived quantities cannot be set with a Dirichlet
condition. You will never see a Dirichlet condition being applied to a heat flux for
example.

**STRONGLY INTEGRATED (SIC):**

The next class of boundary condition is referred to within *Goma* as the strongly
integrated boundary conditions. These boundary conditions replace the mechanics
equation at the i\ :sup:`th` node with a surface integral of some derived quantity. The
general form of these conditions is:

.. math::

   \int_{S} \phi_i \, g(\mathbf{x}) \, \mathrm{d}S = 0

where in this case :math:`g(\mathbf{x})` is not a residual equation but some derived quantity. Unlike
strong constraints, this term is not multiplied by a penalizing factor before it is
added to the accumulated mechanics equation at node *i*. Consequently, it
represents boundary contributions to the mechanics at that node. Note also that
since these conditions only make additions to the boundary mechanics, if a
strongly enforced condition (SIC or PCC) is also present at the node, the weakly
integrated constraint will be clobbered along with the rest of the mechanics. As an
example, a CAPILLARY boundary condition that is applied to the same sideset as
a VELO_NORMAL condition will have no effect in the final answer.

Weakly integrated boundary conditions are also very much a consequence of the
“natural” boundary conditions that emerge from the finite element formulation. As
anyone familiar with the finite element method knows, these are the ghostly
boundary terms that enforce zero boundary fluxes or forces as a convenient
default. The weakly integrated boundary condition step into the space afforded by
the natural boundary conditions and allow the user to specify values for these
boundary fluxes or forces as functions of conditions on those boundaries.

In addition, to the various classes of boundary conditions detailed above, there are special cases
that arise when applying boundary conditions to the “vector” degrees of freedom. Currently, the
only “vector” degrees of freedom are the mesh displacement and fluid velocity unknowns. When
a boundary condition is applied to these degrees of freedom, it may be ROTATED, VECTOR or
SCALAR. These labels appear in the boundary condition documentation along with the class of
the condition.

**ROTATED:**

When a boundary condition is designated as “ROTATED,” the vector components
of the appropriate equations for the surface nodes are projected into a new
coordinate system that is locally based on the surface normal vector and tangent
vectors. It is the presence of the “ROTATED” boundary condition that prompts
this process. Usually, only one of these rotated components is then affected by the
boundary condition constraint and in this sense ROTATED conditions are
SCALAR conditions (see below). Also generally speaking, ROTATED boundary
conditions are strongly enforced as described above.

**VECTOR:**

When a boundary condition is designated as a “VECTOR” condition, the
implication is that a vector quantity will be added to the vector components of the
original mechanics equations. “VECTOR” boundary conditions are generally
always applied weakly.

**SCALAR:**

When a boundary condition is designated a “SCALAR” condition, only a single
mechanics equation is going to be influenced by the boundary condition. In the
case of the vector degrees of freedom, only a single component would be affected
by the boundary condition. Boundary conditions that apply to degrees of freedom
that are naturally scalars, for instance temperature and species, are by default
SCALAR conditions.

An example of these special labels for the *VELO_NORMAL_EDGE* condition (found on the line
with the **Description/Usage** section header) is **PCC-EDGE/ROTATED MOMENTUM** indicating
a rotated collocated edge condition applied to the fluid momentum equation. Given this labeling
convention, boundary conditions which are not specified to be rotated or vector conditions can
be presumed to be unrotated scalar conditions. Boundary conditions that may be applied to any
equation are labeled “varied.”

The user will not find “periodic boundary conditions” discussed in this manual. Those interested
in such conditions should consult the Advanced Capabilities Manual (SAND2006-7304).

**Resolving Conflicts between Boundary Conditions** - In *Goma*, the bulk equations and
boundary conditions are evaluated on an element-by-element basis. After the residual and Jacobian
entries for the bulk equations have been calculated, the boundary conditions are used to modify
or replace the bulk entries where necessary. Often the selection of boundary conditions from the
input deck may cause two boundary conditions to be applied to the same equation (equation associated
with a nodal point); this is especially true at junction points. Frequently the multiple boundary
conditions perform the same function (i.e. duplicates) but in some important instances they
are different (i.e. conflicts). In *Goma*, a decision making process was developed for determining
which boundary conditions have priority. The flow chart for this decision-making is shown in Figure
4. While this process resolves boundary-condition conflicts, it **does not** eliminate the possibility
of setting boundary conditions that are incompatible and lead to errors in solving the problem.
However, this method should clarify how BC’s are chosen from the input deck and should enable
the user to determine why a given combination of boundary conditions does not work.

The flow chart in Figure 3 shows the procedure for resolving what boundary conditions get applied
to a given equation at a given node. The starting point assumes that a list of all the potential
boundary conditions for the equation are known. Boundary conditions in *Goma* fall into several
classes: *Dirichlet, Pointwise Collocation, Strong Integrated, Weak Integrated and Special conditions*,
in order of priority. For boundary conditions applied to vector equations (mesh or momentum),
a boundary condition can cause the bulk equations to be rotated prior to applying the
boundary condition; in conflicts between boundary conditions, conditions which do not rotate the
bulk equations (*unrotated conditions*) have priority over conditions which rotate the bulk equations
(*rotated conditions*). In certain cases (e.g. two PLANE conditions which intersect at a point),
conflicting boundary conditions can be checked to determine if they are duplicates, in which case
only the first of the duplicates in the input deck is applied. Most boundary conditions are designed
to apply by themselves, but a special class of boundary conditions, the generalized dirichlet (GD) conditions, are designed so that multiple GD conditions can apply along the same boundary and to
the same equation.

While running, *Goma* prints the results of conflict resolution for every node at which it found at
least two boundary conditions being applied to the same equation. The results indicate the node
number, equation type, boundary conditions chosen by *Goma*, and the side-set or node-set numbers
to which the boundary conditions apply. Thus to determine what boundary conditions are actually
used by *Goma*, carefully check the output from conflict resolution. Setting the *Debug_Flag*
= 1 causes *Goma* to print out more information regarding which boundary conditions apply and
which do not. Despite the complexity of the logic built into *Goma* to resolve conflicts between
boundary conditions, there are several combinations of boundary conditions that do not have a
clear resolution. It is up to the user to resolve the final conflicts.

And finally, the first (*Number of BC*) and last (*END OF BC*) boundary condition cards are a pair
and stand alone; the remaining cards belong to the categories of conditions discussed above. The
ordering of input cards within this collection of BC input records (i.e., section) is sequential and
some sections of interspersed comments accompany each boundary condition category.

.. include:: /chapters/ch4/section_10/4.10.1_number_of_bc.txt

.. include:: /chapters/ch4/section_10/4.10.2_fix.txt

.. include:: /chapters/ch4/section_10/4.10.3_gd_const.txt

.. include:: /chapters/ch4/section_10/4.10.4_gd_linear.txt

.. include:: /chapters/ch4/section_10/4.10.5_gd_parab.txt

.. include:: /chapters/ch4/section_10/4.10.6_gd_polyn.txt

.. include:: /chapters/ch4/section_10/4.10.7_gd_time.txt

.. include:: /chapters/ch4/section_10/4.10.8_gd_circ.txt

.. include:: /chapters/ch4/section_10/4.10.9_gd_table.txt

.. include:: /chapters/ch4/section_10/4.10.10_table_wicv.txt

.. include:: /chapters/ch4/section_10/4.10.11_table_wics.txt

.. include:: /chapters/ch4/section_10/4.10.12_table.txt

.. include:: /chapters/ch4/section_10/4.10.13_distng.txt

.. include:: /chapters/ch4/section_10/4.10.14_dxdydz.txt

.. include:: /chapters/ch4/section_10/4.10.15_dxuser_dyuser_dzuser.txt

.. include:: /chapters/ch4/section_10/4.10.17_dxyzdistng.txt

.. include:: /chapters/ch4/section_10/4.10.18_splinexyz_geomxyz.txt

.. include:: /chapters/ch4/section_10/4.10.19_spline_geom.txt

.. include:: /chapters/ch4/section_10/4.10.21_planexyz.txt

.. include:: /chapters/ch4/section_10/4.10.22_plane.txt

.. include:: /chapters/ch4/section_10/4.10.23_moving_plane.txt

.. include:: /chapters/ch4/section_10/4.10.24_slopexyz.txt

.. include:: /chapters/ch4/section_10/4.10.25_slope.txt

.. include:: /chapters/ch4/section_10/4.10.26_kinematic.txt

.. include:: /chapters/ch4/section_10/4.10.27_kinematic_petrov.txt

.. include:: /chapters/ch4/section_10/4.10.28_kinematic_colloc.txt

.. include:: /chapters/ch4/section_10/4.10.29_kinematic_disc.txt

.. include:: /chapters/ch4/section_10/4.10.30_kinematic_edge.txt

.. include:: /chapters/ch4/section_10/4.10.31_kinematic_species.txt

.. include:: /chapters/ch4/section_10/4.10.32_kin_displacement_petrov.txt

.. include:: /chapters/ch4/section_10/4.10.33_kin_displacement_colloc.txt

.. include:: /chapters/ch4/section_10/4.10.34_kin_displacement.txt

.. include:: /chapters/ch4/section_10/4.10.35_kin_leak.txt

.. include:: /chapters/ch4/section_10/4.10.36_kin_chem.txt

.. include:: /chapters/ch4/section_10/4.10.37_force.txt

.. include:: /chapters/ch4/section_10/4.10.38_norm_force.txt

.. include:: /chapters/ch4/section_10/4.10.39_rep_force.txt

.. include:: /chapters/ch4/section_10/4.10.40_force_user.txt

.. include:: /chapters/ch4/section_10/4.10.41_ca.txt

.. include:: /chapters/ch4/section_10/4.10.42_ca_or_fix.txt

.. include:: /chapters/ch4/section_10/4.10.43_ca_edge.txt

.. include:: /chapters/ch4/section_10/4.10.44_ca_edge_int.txt

.. include:: /chapters/ch4/section_10/4.10.46_ca_edge_or_fix.txt

.. include:: /chapters/ch4/section_10/4.10.47_ca_edge_curve.txt

.. include:: /chapters/ch4/section_10/4.10.48_ca_edge_curve_int.txt

.. include:: /chapters/ch4/section_10/4.10.49_var_ca_edge.txt

.. include:: /chapters/ch4/section_10/4.10.50_var_ca_user.txt

.. include:: /chapters/ch4/section_10/4.10.51_friction.txt

.. include:: /chapters/ch4/section_10/4.10.52_solid_fluid.txt

.. include:: /chapters/ch4/section_10/4.10.53_penetration.txt

.. include:: /chapters/ch4/section_10/4.10.55_porous_kin.txt

.. include:: /chapters/ch4/section_10/4.10.56_sdc_kin_sf.txt

.. include:: /chapters/ch4/section_10/4.10.57_dxdydz_rs.txt

.. include:: /chapters/ch4/section_10/4.10.58_force_rs.txt

.. include:: /chapters/ch4/section_10/4.10.59_norm_force_rs.txt

.. include:: /chapters/ch4/section_10/4.10.60_rep_force_rs.txt

.. include:: /chapters/ch4/section_10/4.10.61_force_user_rs.txt

.. include:: /chapters/ch4/section_10/4.10.62_solid_fluid_rs.txt

.. include:: /chapters/ch4/section_10/4.10.63_splinexyz_rs.txt

.. include:: /chapters/ch4/section_10/4.10.64_spline_rs.txt

.. include:: /chapters/ch4/section_10/4.10.65_friction_rs.txt

.. include:: /chapters/ch4/section_10/4.10.67_uvw.txt

.. include:: /chapters/ch4/section_10/4.10.68_puvw.txt

.. include:: /chapters/ch4/section_10/4.10.69_uvwvary.txt

.. include:: /chapters/ch4/section_10/4.10.70_uvwuser.txt

.. include:: /chapters/ch4/section_10/4.10.71_no_slip_no_slip_rs.txt

.. include:: /chapters/ch4/section_10/4.10.72_velo_normal.txt

.. include:: /chapters/ch4/section_10/4.10.73_velo_normal_ls.txt

.. include:: /chapters/ch4/section_10/4.10.75_velo_norm_colloc.txt

.. include:: /chapters/ch4/section_10/4.10.77_velo_normal_disc.txt

.. include:: /chapters/ch4/section_10/4.10.78_velo_normal_edge.txt

.. include:: /chapters/ch4/section_10/4.10.79_velo_normal_edge_int.txt

.. include:: /chapters/ch4/section_10/4.10.80_velo_tangent.txt

.. include:: /chapters/ch4/section_10/4.10.81_velo_tangent_edge.txt

.. include:: /chapters/ch4/section_10/4.10.82_velo_tangent_edge_int.txt

.. include:: /chapters/ch4/section_10/4.10.83_velo_tangent_3D.txt

.. include:: /chapters/ch4/section_10/4.10.84_velo_slip.txt

.. include:: /chapters/ch4/section_10/4.10.85_velo_slip_rot.txt

.. include:: /chapters/ch4/section_10/4.10.86_velo_slip_fill.txt

.. include:: /chapters/ch4/section_10/4.10.87_velo_slip_electrokinetic.txt

.. include:: /chapters/ch4/section_10/4.10.88_velo_slip_electrokinetic3D.txt

.. include:: /chapters/ch4/section_10/4.10.89_velo_tangent_solid.txt

.. include:: /chapters/ch4/section_10/4.10.91_velo_slip_solid.txt

.. include:: /chapters/ch4/section_10/4.10.92_discontinuous_velo.txt

.. include:: /chapters/ch4/section_10/4.10.93_hydrostatic_symm.txt

.. include:: /chapters/ch4/section_10/4.10.94_flow_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.95_flow_stressnobc.txt

.. include:: /chapters/ch4/section_10/4.10.96_flow_gradv.txt

.. include:: /chapters/ch4/section_10/4.10.97_flow_press_user.txt

.. include:: /chapters/ch4/section_10/4.10.98_flow_hydrostatic.txt

.. include:: /chapters/ch4/section_10/4.10.100_flowrate.txt

.. include:: /chapters/ch4/section_10/4.10.101_pressure_user.txt

.. include:: /chapters/ch4/section_10/4.10.102_cont_tang_vel.txt

.. include:: /chapters/ch4/section_10/4.10.103_cont_norm_vel.txt

.. include:: /chapters/ch4/section_10/4.10.104_vnorm_leak.txt

.. include:: /chapters/ch4/section_10/4.10.105_capillary.txt

.. include:: /chapters/ch4/section_10/4.10.106_cap_repulse.txt

.. include:: /chapters/ch4/section_10/4.10.107_cap_recoil_press.txt

.. include:: /chapters/ch4/section_10/4.10.108_elec_traction.txt

.. include:: /chapters/ch4/section_10/4.10.109_cap_endforce.txt

.. include:: /chapters/ch4/section_10/4.10.110_surftang_edge.txt

.. include:: /chapters/ch4/section_10/4.10.111_cap_endforce_scalar.txt

.. include:: /chapters/ch4/section_10/4.10.112_surftang_scalar_edge.txt

.. include:: /chapters/ch4/section_10/4.10.113_fill_ca.txt

.. include:: /chapters/ch4/section_10/4.10.114_moving_ca.txt

.. include:: /chapters/ch4/section_10/4.10.116_sdc_stefanflow.txt

.. include:: /chapters/ch4/section_10/4.10.118_fluid_solid.txt

.. include:: /chapters/ch4/section_10/4.10.120_fluid_solid_rs.txt

.. include:: /chapters/ch4/section_10/4.10.121_darcy_continuous.txt

.. include:: /chapters/ch4/section_10/4.10.122_vn_porous.txt

.. include:: /chapters/ch4/section_10/4.10.123_capillary_shear_visc.txt

.. include:: /chapters/ch4/section_10/4.10.124_velo_theta_cox.txt

.. include:: /chapters/ch4/section_10/4.10.125_velo_theta_hoffman.txt

.. include:: /chapters/ch4/section_10/4.10.126_velo_theta_tpl.txt

.. include:: /chapters/ch4/section_10/4.10.128_sheet_endslop.txt

.. include:: /chapters/ch4/section_10/4.10.129_tension_sheet.txt

.. include:: /chapters/ch4/section_10/4.10.130_t.txt

.. include:: /chapters/ch4/section_10/4.10.131_t_user.txt

.. include:: /chapters/ch4/section_10/4.10.133_qconv.txt

.. include:: /chapters/ch4/section_10/4.10.134_qrad.txt

.. include:: /chapters/ch4/section_10/4.10.135_qside.txt

.. include:: /chapters/ch4/section_10/4.10.136_t_contact_resis_t_contact_resis_2.txt

.. include:: /chapters/ch4/section_10/4.10.137_quser.txt

.. include:: /chapters/ch4/section_10/4.10.139_q_velo_slip.txt

.. include:: /chapters/ch4/section_10/4.10.140_q_laser_weld.txt

.. include:: /chapters/ch4/section_10/4.10.141_q_vapor_bc.txt

.. include:: /chapters/ch4/section_10/4.10.143_vp_equil.txt

.. include:: /chapters/ch4/section_10/4.10.144_latent_heat.txt

.. include:: /chapters/ch4/section_10/4.10.145_latent_heat_internal.txt

.. include:: /chapters/ch4/section_10/4.10.146_y.txt

.. include:: /chapters/ch4/section_10/4.10.147_yuser.txt

.. include:: /chapters/ch4/section_10/4.10.149_y_discontinuous.txt

.. include:: /chapters/ch4/section_10/4.10.150_yflux.txt

.. include:: /chapters/ch4/section_10/4.10.151_yflux_const.txt

.. include:: /chapters/ch4/section_10/4.10.152_yflux_equil.txt

.. include:: /chapters/ch4/section_10/4.10.154_yflux_sulfidation.txt

.. include:: /chapters/ch4/section_10/4.10.155_yflux_sus.txt

.. include:: /chapters/ch4/section_10/4.10.157_yflux_bv.txt

.. include:: /chapters/ch4/section_10/4.10.158_yflux_hor.txt

.. include:: /chapters/ch4/section_10/4.10.160_yflux_orr.txt

.. include:: /chapters/ch4/section_10/4.10.162_yflux_user.txt

.. include:: /chapters/ch4/section_10/4.10.164_yflux_alloy.txt

.. include:: /chapters/ch4/section_10/4.10.165_ytotalflux_const.txt

.. include:: /chapters/ch4/section_10/4.10.166_vl_equil.txt

.. include:: /chapters/ch4/section_10/4.10.167_vl_poly.txt

.. include:: /chapters/ch4/section_10/4.10.169_vl_equil_pseudorxn.txt

.. include:: /chapters/ch4/section_10/4.10.170_is_equil_pseudorxn.txt

.. include:: /chapters/ch4/section_10/4.10.171_surface_charge.txt

.. include:: /chapters/ch4/section_10/4.10.172_p.txt

.. include:: /chapters/ch4/section_10/4.10.174_pspg.txt

.. include:: /chapters/ch4/section_10/4.10.176_pressure_datum.txt

.. include:: /chapters/ch4/section_10/4.10.177_category_8.txt

.. include:: /chapters/ch4/section_10/4.10.178_porous_liq_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.179_porous_liq_flux_const.txt

.. include:: /chapters/ch4/section_10/4.10.180_porous_gas_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.182_porous_gas.txt

.. include:: /chapters/ch4/section_10/4.10.184_porous_gas_flux_const.txt

.. include:: /chapters/ch4/section_10/4.10.185_porous_conv.txt

.. include:: /chapters/ch4/section_10/4.10.186_porous_flux.txt

.. include:: /chapters/ch4/section_10/4.10.187_porous_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.189_p_liq_user.txt

.. include:: /chapters/ch4/section_10/4.10.191_porous_temperature.txt

.. include:: /chapters/ch4/section_10/4.10.192_category_9.txt

.. include:: /chapters/ch4/section_10/4.10.193_s11.txt

.. include:: /chapters/ch4/section_10/4.10.194_s12.txt

.. include:: /chapters/ch4/section_10/4.10.195_s13.txt

.. include:: /chapters/ch4/section_10/4.10.196_s22.txt

.. include:: /chapters/ch4/section_10/4.10.197_s23.txt

.. include:: /chapters/ch4/section_10/4.10.198_s33.txt

.. include:: /chapters/ch4/section_10/4.10.199_g11.txt

.. include:: /chapters/ch4/section_10/4.10.200_g12.txt

.. include:: /chapters/ch4/section_10/4.10.201_g13.txt

.. include:: /chapters/ch4/section_10/4.10.202_g21.txt

.. include:: /chapters/ch4/section_10/4.10.203_g22.txt

.. include:: /chapters/ch4/section_10/4.10.204_g23.txt

.. include:: /chapters/ch4/section_10/4.10.205_g31.txt

.. include:: /chapters/ch4/section_10/4.10.206_g32.txt

.. include:: /chapters/ch4/section_10/4.10.207_g33.txt

.. include:: /chapters/ch4/section_10/4.10.208_sh.txt

.. include:: /chapters/ch4/section_10/4.10.209_f.txt

.. include:: /chapters/ch4/section_10/4.10.210_fill_inlet.txt

.. include:: /chapters/ch4/section_10/4.10.211_current.txt

.. include:: /chapters/ch4/section_10/4.10.212_current_user.txt

.. include:: /chapters/ch4/section_10/4.10.213_volt.txt

.. include:: /chapters/ch4/section_10/4.10.214_current_bv.txt

.. include:: /chapters/ch4/section_10/4.10.216_current_hor.txt

.. include:: /chapters/ch4/section_10/4.10.218_current_orr.txt

.. include:: /chapters/ch4/section_10/4.10.220_volt_user.txt

.. include:: /chapters/ch4/section_10/4.10.221_category_14.txt

.. include:: /chapters/ch4/section_10/4.10.222_lagrange_no_slip.txt

.. include:: /chapters/ch4/section_10/4.10.224_overset_fluid_solid_baaijens_fluid_solid.txt

.. include:: /chapters/ch4/section_10/4.10.225_overset_solid_fluid_baaijens_solid_fluid.txt

.. include:: /chapters/ch4/section_10/4.10.227_f1_f2_f3_f4_f5.txt

.. include:: /chapters/ch4/section_10/4.10.229_pf_capillary.txt

.. include:: /chapters/ch4/section_10/4.10.230_category_15.txt

.. include:: /chapters/ch4/section_10/4.10.231_ls_adc.txt

.. include:: /chapters/ch4/section_10/4.10.233_ls_ca_h.txt

.. include:: /chapters/ch4/section_10/4.10.235_ls_capillary.txt

.. include:: /chapters/ch4/section_10/4.10.237_ls_flow_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.239_ls_fluid_solid_contact.txt

.. include:: /chapters/ch4/section_10/4.10.241_ls_inlet.txt

.. include:: /chapters/ch4/section_10/4.10.243_ls_no_slip.txt

.. include:: /chapters/ch4/section_10/4.10.245_ls_q.txt

.. include:: /chapters/ch4/section_10/4.10.247_ls_qrad.txt

.. include:: /chapters/ch4/section_10/4.10.249_ls_qlaser.txt

.. 
	TODO - In line 486 where the word "EQUATION" is, it needs to be replaced with the correct equation fo this paragraph. In line 510 there is a picture in the place in which an equation needs to be written. 

