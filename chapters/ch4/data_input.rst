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

.. include:: /chapters/ch4/sections/4.1.1_fem_file.txt

.. include:: /chapters/ch4/sections/4.1.2_output_exodusII_file.txt

.. include:: /chapters/ch4/sections/4.1.3_guess_file.txt

.. include:: /chapters/ch4/sections/4.1.4_soln_file.txt

.. include:: /chapters/ch4/sections/4.1.5_write_intermediate_results.txt

.. include:: /chapters/ch4/sections/4.1.6_write_initial_solution.txt

**General Specifications**
##########################

This section of input records covers additional I/O requests and specifications, including parallel
file I/O information, initial-guess directives (viz., whether a restart comes from a neutral file or
another exoII file), individual field variable initialization, debugging options, developer
diagnostic options, etc. This section and several of its input records are required, as indicated
below.

.. include:: /chapters/ch4/sections/4.2.1_number_of_processors.txt

.. include:: /chapters/ch4/sections/4.2.2_output_level.txt

.. include:: /chapters/ch4/sections/4.2.3_debug.txt

.. include:: /chapters/ch4/sections/4.2.4_number_of_jacobian_file_dumps.txt

.. include:: /chapters/ch4/sections/4.2.5_initial_guess.txt

.. include:: /chapters/ch4/sections/4.2.7_initialize.txt

.. include:: /chapters/ch4/sections/4.2.9_external_field.txt

.. include:: /chapters/ch4/sections/4.2.10_export_field.txt

.. include:: /chapters/ch4/sections/4.2.11_external_pixel_field.txt

.. include:: /chapters/ch4/sections/4.2.12_pressure_datum.txt

.. include:: /chapters/ch4/sections/4.2.13_anneal_mesh_on_output.txt

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

.. include:: /chapters/ch4/sections/4.3.1_time_integration.txt

.. include:: /chapters/ch4/sections/4.3.2_delta_t.txt

.. include:: /chapters/ch4/sections/4.3.3_maximum_number_of_time_steps.txt

.. include:: /chapters/ch4/sections/4.3.4_maximum_time.txt

.. include:: /chapters/ch4/sections/4.3.5_minimum_time_step.txt

.. include:: /chapters/ch4/sections/4.3.6_maximum_time_step.txt

.. include:: /chapters/ch4/sections/4.3.7_minimum_resolved_time_step.txt

.. include:: /chapters/ch4/sections/4.3.8_courant_number_limit.txt

.. include:: /chapters/ch4/sections/4.3.9_time_step_parameter.txt

.. include:: /chapters/ch4/sections/4.3.10_time_step_error.txt

.. include:: /chapters/ch4/sections/4.3.11_printing_frequency.txt

.. include:: /chapters/ch4/sections/4.3.12_second_frequency_time.txt

.. include:: /chapters/ch4/sections/4.3.13_initial_time.txt

.. include:: /chapters/ch4/sections/4.3.14_fill_subcycle.txt

.. include:: /chapters/ch4/sections/4.3.15_fill_weight_function.txt

.. include:: /chapters/ch4/sections/4.3.16_level_set_interface_tracking.txt

.. include:: /chapters/ch4/sections/4.3.17_level_set_semi_lagrange.txt

.. include:: /chapters/ch4/sections/4.3.18_level_set_subgrid_integration_depth.txt

.. include:: /chapters/ch4/sections/4.3.20_level_set_subelement_integration.txt

.. include:: /chapters/ch4/sections/4.3.21_level_set_adaptive_integration.txt

.. include:: /chapters/ch4/sections/4.3.23_level_set_adaptive_order.txt

.. include:: /chapters/ch4/sections/4.3.25_overlap_quadrature_points.txt

.. include:: /chapters/ch4/sections/4.3.26_level_set_pspp_filtering.txt

.. include:: /chapters/ch4/sections/4.3.27_level_set_length_scale.txt