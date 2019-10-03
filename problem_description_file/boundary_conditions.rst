Boundary Condition Specifications
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
types (called Category 1) is given below.

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

.. include:: boundary_conditions/number_of_bc.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 1: Any Equation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This category includes a set of cards that are used to provide all boundary condition information
for a generalized dirichlet (GD) boundary condition. The condition is applied as a pointwise
collocation along a given node set. The general syntax for the GD_cards is as follows:

::

	BC = <bc_name> <bc_type> <bc_id> <equation_name> <integer1> <variable_name> <integer2> {float_list}

The current allowable definitions and/or values for < bc_name>, <bc_type>, <bc_id>,
<integer1>, <integer2> and {float_list} are provided in the individual cards. As a general note,
<integer1> and <integer2> are the species number of the mass transport equation and
concentration variable, respectively; they should be zero for other equation and variable types.
Currently these conditions assume that the variable is defined at all the nodes at which the
equation is defined (no subparametric mapping).

However, the values for <equation_name> and <variable_name>, which apply generally to all
cards in this category (except as subsequently noted), are given here:

+------------------+---------------------------------------------------------------------------+
|<equation_name>   |   A character string indicating the equation to which this                |
|                  |   boundary condition is applied, which can be                             |
|                  |                                                                           |
|                  |    * **R_MOMENTUM1, R_MOMENTUM2,**                                        |  
|                  |    * **R_MOMENTUM3, R_MESH1, R_MESH2, R_MESH3,**                          |
|                  |    * **R_MASS, R_ENERGY, R_MASS_SURF,**                                   |
|                  |    * **R_PRESSURE, R_STRESS11, R_STRESS12,**                              |
|                  |    * **R_STRESS13, R_STRESS22, R_STRESS23,**                              |
|                  |    * **R_STRESS33, R_GRADIENT11, R_GRADIENT12,**                          |
|                  |    * **R_GRADIENT13, R_GRADIENT21,**                                      |
|                  |    * **R_GRADIENT22, R_GRADIENT23,**                                      |
|                  |    * **R_GRADIENT31, R_GRADIENT32,**                                      |
|                  |    * **R_GRADIENT33, R_POTENTIAL, R_FILL,**                               |
|                  |    * **R_SHEAR_RATE, R_MESH_NORMAL** (rotate mesh equations and apply     |
|                  |        this condition to normal component),                               |
|                  |    * **R_MESH_TANG1, R_MESH_TANG2,**                                      |  
|                  |    * **R_MOM_NORMAL** (rotate momentum equations and apply this condition |
|                  |        to normal component),                                              |
|                  |    * **R_MOM_TANG1, R_MOM_TANG2,**                                        |
|                  |    * **R_POR_LIQ_PRESS, R_POR_GAS_PRESS,**                                |
|                  |    * **R_POR_POROSITY, R_POR_SATURATION,**                                |
|                  |    * **R_POR_ENERGY, R_POR_LAST,**                                        |
|                  |    * **R_POR_SINK_MASS, R_VORT_DIR1,**                                    |
|                  |    * **R_VORT_DIR2, R_VORT_DIR3, R_VORT_LAMBDA,**                         |
|                  |    * **R_CURVATURE, R_LAGR_MULT1,**                                       |
|                  |    * **R_LAGR_MULT2, R_LAGR_MULT3,**                                      |
|                  |    * **R_BOND_EVOLUTION, R_SURF_CHARGE,**                                 |
|                  |    * **R_EXT_VELOCITY, R_EFIELD1, R_EFIELD2,**                            |
|                  |    * **R_EFIELD3, R_ENORM, R_NORMAL1,**                                   |
|                  |    * **R_NORMAL2, R_NORMAL3, R_ _CURVATURE,**                             |
|                  |    * **R_SHELL_TENSION, R_SHELL_X, R_SHELL_Y,**                           |
|                  |    * **R_SHELL_USER, R_PHASE1, R_PHASE2,**                                |
|                  |    * **R_PHASE3, R_PHASE4, R_PHASE5,**                                    |
|                  |    * **R_SHELL_ANGLE1, R_SHELL_ANGLE2,**                                  |
|                  |    * **R_SHELL_SURF_DIV_V, R_SHELL_SURF_CURV,**                           |
|                  |    * **R_N_DOT_CURL_V, R_GRAD_S_V_DOT_N1,**                               |
|                  |    * **R_GRAD_S_V_DOT_N2, R_GRAD_S_V_DOT_N3,**                            |
|                  |    * **R_ACOUS_PREAL, R_ACOUS_PIMAG,**                                    |
|                  |    * **R_SHELL_DIFF_FLUX,**                                               |
|                  |    * **R_SHELL_DIFF_CURVATURE,**                                          |
|                  |    * **R_SHELL_NORMAL1, R_SHELL_NORMAL2,**                                |
|                  |    * **R_ACOUS_REYN_STRESS, R_SHELL_BDYVELO,**                            |
|                  |    * **R_SHELL_LUBP, R_LUBP, R_SHELL_FILMP,**                             |
|                  |    * **R_SHELL_FILMH, R_SHELL_PARTC,**                                    |
|                  |    * **R_SHELL_SAT_CLOSED, R_SHELL_SAT_OPEN,**                            |
|                  |    * **R_SHELL_ENERGY, R_SHELL_DELTAH,**                                  |
|                  |    * **R_SHELL_LUB_CURV, R_SHELL_SAT_GASN,**                              |
|                  |    * **R_SHELL_SHEAR_TOP, R_SHELL_SHEAR_BOT,**                            |
|                  |    * **R_SHELL_CROSS_SHEAR, R_MAX_STRAIN,**                               |
|                  |    * **R_CUR_STRAIN , R_LUBP_2,**                                         |
|                  |    * **R_SHELL_SAT_OPEN_2, or**                                           |
|                  |    * **R_SHELL_LUB_CURV_2**                                               |
+------------------+---------------------------------------------------------------------------+
|<variable_name>   |   A character string indicating the variable which should be              |
|                  |   fixed, which can be                                                     |
|                  |                                                                           |
|                  |    * **VELOCITY1, VELOCITY2,**                                            |
|                  |    * **VELOCITY3, MESH_DISPLACEMENT1,**                                   |
|                  |    * **MESH_DISPLACEMENT2,**                                              |
|                  |    * **MESH_DISPLACEMENT3, MESH_POSITION1,**                              |
|                  |    * **MESH_POSITION2, MESH_POSITION3,**                                  |
|                  |    * **MASS_FRACTION, SURFACE, TEMPERATURE, or**                          |    
|                  |    * **PRESSURE** (pressure will have no effect if not using Q1 or Q2     |  
|                  |       basis functions),                                                   |
|                  |    * **POLYMER_STRESS11,**                                                |
|                  |    * **POLYMER_STRESS12, POLYMER_STRESS13,**                              |
|                  |    * **POLYMER_STRESS22, POLYMER_STRESS23,**                              |
|                  |    * **POLYMER_STRESS33, VOLTAGE, FILL,**                                 |
|                  |    * **SHEAR_RATE, VEL_NORM, D_VEL1_DT,**                                 |
|                  |    * **D_VEL2_DT, D_VEL3_DT, D_T_DT, D_C_DT,**                            |
|                  |    * **D_X1_DT, D_X2_DT, D_X3_DT, D_S_DT, D_P_DT,**                       |
|                  |    * **VELOCITY_GRADIENT11,**                                             |
|                  |    * **VELOCITY_GRADIENT12,**                                             |
|                  |    * **VELOCITY_GRADIENT13,**                                             |
|                  |    * **VELOCITY_GRADIENT21,**                                             |
|                  |    * **VELOCITY_GRADIENT22,**                                             |
|                  |    * **VELOCITY_GRADIENT23,**                                             |
|                  |    * **VELOCITY_GRADIENT31,**                                             |
|                  |    * **VELOCITY_GRADIENT32,**                                             |
|                  |    * **VELOCITY_GRADIENT33, POR_LIQ_PRESS,**                              |
|                  |    * **POR_GAS_PRESS, POR_POROSITY,**                                     |
|                  |    * **POR_POROSITY, POR_TEMP , POR_SATURATION,**                         |
|                  |    * **POR_LAST, MAX_POROUS_NUM,**                                        |
|                  |    * **POR_SINK_MASS, VORT_DIR1, VORT_DIR2,**                             |
|                  |    * **VORT_DIR3, VORT_LAMBDA, CURVATURE,**                               |
|                  |    * **LAGR_MULT1, LAGR_MULT2, LAGR_MULT3,**                              |
|                  |    * **BOND_EVOLUTION, SURF_CHARGE,**                                     |
|                  |    * **EXT_VELOCITY, EFIELD1, EFIELD2, EFIELD3,**                         |
|                  |    * **ENORM, NORMAL1, NORMAL2, NORMAL3,**                                |
|                  |    * **SHELL_CURVATURE, SHELL_TENSION,**                                  |
|                  |    * **SHELL_X, SHELL_Y, SHELL_USER, PHASE1,**                            |
|                  |    * **PHASE2, PHASE3, PHASE4, PHASE5,**                                  |
|                  |    * **SHELL_ANGLE1, SHELL_ANGLE2,**                                      |
|                  |    * **SHELL_SURF_DIV_V, SHELL_SURF_CURV,**                               |
|                  |    * **N_DOT_CURL_V, GRAD_S_V_DOT_N1,**                                   |
|                  |    * **GRAD_S_V_DOT_N2, GRAD_S_V_DOT_N3,**                                |
|                  |    * **ACOUS_PREAL, ACOUS_PIMAG,**                                        |
|                  |    * **SHELL_DIFF_FLUX, SHELL_DIFF_CURVATURE,**                           |
|                  |    * **SHELL_NORMAL1, SHELL_NORMAL2,**                                    |
|                  |    * **ACOUS_REYN_STRESS, SHELL_BDYVELO,**                                |
|                  |    * **SHELL_LUBP, LUBP, SHELL_FILMP,**                                   |
|                  |    * **SHELL_FILMH, SHELL_PARTC,**                                        |
|                  |    * **SHELL_SAT_CLOSED, SHELL_PRESS_OPEN,**                              |
|                  |    * **SHELL_TEMPERATURE, SHELL_DELTAH,**                                 |
|                  |    * **SHELL_LUB_CURV, SHELL_SAT_GASN,**                                  |
|                  |    * **SHELL_SHEAR_TOP, SHELL_SHEAR_BOT,**                                |
|                  |    * **SHELL_CROSS_SHEAR, MAX_STRAIN,**                                   |
|                  |    * **CUR_STRAIN, LUBP_2, SHELL_PRESS_OPEN2,**                           |
|                  |    * **SHELL_LUB_CURV_2**                                                 |
+------------------+---------------------------------------------------------------------------+

EXCEPTIONS to the above parameter definitions: For the *GD_TIME* card, the <variable_names>
of **LINEAR, EXPONENTIAL**, or **SINUSOIDAL** are acceptable (see examples below). There
are also differences in the use of the *GD_TABLE* card, which are explained in the description of
that card below.

A GD boundary condition can be applied multiple times to the same side set and equation to build
up a general multiparameter condition. When this is done, the function is built by expanding the
equations sequentially in the order specified in the BC list.

Descriptions of the GD cards are given next. An insert entitled “Usage Notes on the GD Cards”
follows the descriptions, explaining how the cards are used together in various combinations.

.. include:: boundary_conditions/fix.rst

.. include:: boundary_conditions/gd_const.rst

.. include:: boundary_conditions/gd_linear.rst

.. include:: boundary_conditions/gd_parab.rst

.. include:: boundary_conditions/gd_polyn.rst

.. include:: boundary_conditions/gd_time.rst

.. include:: boundary_conditions/gd_circ.rst

.. include:: boundary_conditions/gd_table.rst

.. include:: boundary_conditions/table_wicv.rst

.. include:: boundary_conditions/table_wics.rst

.. include:: boundary_conditions/table.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 2: Mesh Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The boundary conditions in this section involve the mesh motion equations in LAGRANGIAN or
ARBITRARY form (cf. *Mesh Motion* card). These conditions can be used to pin the mesh, specify
its slope at some boundary intersection, apply a traction to a surface, etc. Several more boundary
conditions that are applied to the mesh motion equations but include other problem physics are
also available.

.. include:: boundary_conditions/distng.rst

.. include:: boundary_conditions/dxdydz.rst

.. include:: boundary_conditions/dxuser_dyuser_dzuser.rst

.. include:: boundary_conditions/dxyzdistng.rst

.. include:: boundary_conditions/splinexyz_geomxyz.rst

.. include:: boundary_conditions/spline_geom.rst

.. include:: boundary_conditions/planexyz.rst

.. include:: boundary_conditions/plane.rst

.. include:: boundary_conditions/moving_plane.rst

.. include:: boundary_conditions/slopexyz.rst

.. include:: boundary_conditions/slope.rst

.. include:: boundary_conditions/kinematic.rst

.. include:: boundary_conditions/kinematic_petrov.rst

.. include:: boundary_conditions/kinematic_colloc.rst

.. include:: boundary_conditions/kinematic_disc.rst

.. include:: boundary_conditions/kinematic_edge.rst

.. include:: boundary_conditions/kinematic_species.rst

.. include:: boundary_conditions/kin_displacement_petrov.rst

.. include:: boundary_conditions/kin_displacement_colloc.rst

.. include:: boundary_conditions/kin_displacement.rst

.. include:: boundary_conditions/kin_leak.rst

.. include:: boundary_conditions/kin_chem.rst

.. include:: boundary_conditions/force.rst

.. include:: boundary_conditions/norm_force.rst

.. include:: boundary_conditions/rep_force.rst

.. include:: boundary_conditions/force_user.rst

.. include:: boundary_conditions/ca.rst

.. include:: boundary_conditions/ca_or_fix.rst

.. include:: boundary_conditions/ca_edge.rst

.. include:: boundary_conditions/ca_edge_int.rst

.. include:: boundary_conditions/ca_edge_or_fix.rst

.. include:: boundary_conditions/ca_edge_curve.rst

.. include:: boundary_conditions/ca_edge_curve_int.rst

.. include:: boundary_conditions/var_ca_edge.rst

.. include:: boundary_conditions/var_ca_user.rst

.. include:: boundary_conditions/friction.rst

.. include:: boundary_conditions/solid_fluid.rst

.. include:: boundary_conditions/penetration.rst

.. include:: boundary_conditions/porous_kin.rst

.. include:: boundary_conditions/sdc_kin_sf.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 3: Real Solid Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The reader is referred to a report by Schunk (2000) for a complete description of this equation
type. Briefly, these boundary conditions pertain to the *TOTAL_ALE* mesh motion type (see *Mesh
Motion* card), and are applied to the real solid only, viz. the boundary conditions applied to the
companion mesh motion equations are still needed to control the mesh, independent of the realsolid
material.


.. include:: boundary_conditions/dxdydz_rs.rst

.. include:: boundary_conditions/force_rs.rst

.. include:: boundary_conditions/norm_force_rs.rst

.. include:: boundary_conditions/rep_force_rs.rst

.. include:: boundary_conditions/force_user_rs.rst

.. include:: boundary_conditions/solid_fluid_rs.rst

.. include:: boundary_conditions/splinexyz_rs.rst

.. include:: boundary_conditions/spline_rs.rst

.. include:: boundary_conditions/friction_rs.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 4: Fluid Momentum Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The fluid-momentum equations, e.g., the momentum equations in the Navier-Stokes system for
incompressible flows, require many boundary conditions mainly because they are formulated in
an arbitrary frame of reference. The plethora of boundary conditions here contain Dirichlet, finiteelement
weak form, finite-element strong form, and many other boundary condition types.

.. include:: boundary_conditions/uvw.rst

.. include:: boundary_conditions/puvw.rst

.. include:: boundary_conditions/uvwvary.rst

.. include:: boundary_conditions/uvwuser.rst

.. include:: boundary_conditions/no_slip_no_slip_rs.rst

.. include:: boundary_conditions/velo_normal.rst

.. include:: boundary_conditions/velo_normal_ls.rst

.. include:: boundary_conditions/velo_norm_colloc.rst

.. include:: boundary_conditions/velo_normal_disc.rst

.. include:: boundary_conditions/velo_normal_edge.rst

.. include:: boundary_conditions/velo_normal_edge_int.rst

.. include:: boundary_conditions/velo_tangent.rst

.. include:: boundary_conditions/velo_tangent_edge.rst

.. include:: boundary_conditions/velo_tangent_edge_int.rst

.. include:: boundary_conditions/velo_tangent_3D.rst

.. include:: boundary_conditions/velo_slip.rst

.. include:: boundary_conditions/velo_slip_rot.rst

.. include:: boundary_conditions/velo_slip_fill.rst

.. include:: boundary_conditions/velo_slip_electrokinetic.rst

.. include:: boundary_conditions/velo_slip_electrokinetic3D.rst

.. include:: boundary_conditions/velo_tangent_solid.rst

.. include:: boundary_conditions/velo_slip_solid.rst

.. include:: boundary_conditions/discontinuous_velo.rst

.. include:: boundary_conditions/hydrostatic_symm.rst

.. include:: boundary_conditions/flow_pressure.rst

.. include:: boundary_conditions/flow_stressnobc.rst

.. include:: boundary_conditions/flow_gradv.rst

.. include:: boundary_conditions/flow_press_user.rst

.. include:: boundary_conditions/flow_hydrostatic.rst

.. include:: boundary_conditions/flowrate.rst

.. include:: boundary_conditions/pressure_user.rst

.. include:: boundary_conditions/cont_tang_vel.rst

.. include:: boundary_conditions/cont_norm_vel.rst

.. include:: boundary_conditions/vnorm_leak.rst

.. include:: boundary_conditions/capillary.rst

.. include:: boundary_conditions/cap_repulse.rst

.. include:: boundary_conditions/cap_recoil_press.rst

.. include:: boundary_conditions/elec_traction.rst

.. include:: boundary_conditions/cap_endforce.rst

.. include:: boundary_conditions/surftang_edge.rst

.. include:: boundary_conditions/cap_endforce_scalar.rst

.. include:: boundary_conditions/surftang_scalar_edge.rst

.. include:: boundary_conditions/fill_ca.rst

.. include:: boundary_conditions/moving_ca.rst

.. include:: boundary_conditions/sdc_stefanflow.rst

.. include:: boundary_conditions/fluid_solid.rst

.. include:: boundary_conditions/fluid_solid_rs.rst

.. include:: boundary_conditions/darcy_continuous.rst

.. include:: boundary_conditions/vn_porous.rst

.. include:: boundary_conditions/capillary_shear_visc.rst

.. include:: boundary_conditions/velo_theta_cox.rst

.. include:: boundary_conditions/velo_theta_hoffman.rst

.. include:: boundary_conditions/velo_theta_tpl.rst

.. include:: boundary_conditions/sheet_endslop.rst

.. include:: boundary_conditions/tension_sheet.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 5: Energy Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following conditions are applied as boundary conditions to the energy equation. These
conditions include strong Dirichlet conditions, such as hard sets on temperature on a boundary as
a constant or function of position, weak-form conditions, such as a specified heat flux from a
convective heat transfer model or a constant flux, and a host of interfacial conditions for phase
change (viz. latent heat effects), etc. The energy equation is of course a scalar equation. Some with a molten metal surface. These conditions will also apply in general to the porous energy
equation (see Porous Energy).
highly specialized equations are also available, such as a heat flux model for a laser interaction

.. include:: boundary_conditions/t.rst

.. include:: boundary_conditions/t_user.rst

.. include:: boundary_conditions/qconv.rst

.. include:: boundary_conditions/qrad.rst

.. include:: boundary_conditions/qside.rst

.. include:: boundary_conditions/t_contact_resis_t_contact_resis_2.rst

.. include:: boundary_conditions/qnobc.rst

.. include:: boundary_conditions/quser.rst

.. include:: boundary_conditions/q_velo_slip.rst

.. include:: boundary_conditions/q_laser_weld.rst

.. include:: boundary_conditions/q_vapor_bc.rst

.. include:: boundary_conditions/vp_equil.rst

.. include:: boundary_conditions/latent_heat.rst

.. include:: boundary_conditions/latent_heat_internal.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 6: Mass Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The collection of boundary conditions in this category are applied to the mass balance equations,
specifically the species component balance equations. Most boundary conditions are weakly
integrated conditions defining fluxes at internal or external surfaces, although strongly integrated
and Dirichlet conditions are also available to control known values of dependent variables or
integrated quantities. Boundary conditions are available for chemical species as well as charged
species, suspensions and liquid metals. An important capability in *Goma* is represented by the
discontinuous variable boundary conditions, for which users are referred to Schunk and Rao
(1994) and Moffat (2001). Care must be taken if the species concentration is high enough to be
outside of the *dilute species* assumption, in which case transport of species through boundaries
will affect the volume of the bounding fluids. In these cases, users are referred to the
*VNORM_LEAK* condition for the fluid momentum equations and to *KIN_LEAK* for the solid
momentum (mesh) equations. And finally, users are cautioned about different bases for
concentration (volume, mass, molar) and several discussions on or references to units.

.. include:: boundary_conditions/y.rst

.. include:: boundary_conditions/yuser.rst

.. include:: boundary_conditions/y_discontinuous.rst

.. include:: boundary_conditions/yflux.rst

.. include:: boundary_conditions/yflux_const.rst

.. include:: boundary_conditions/yflux_equil.rst

.. include:: boundary_conditions/yflux_sulfidation.rst

.. include:: boundary_conditions/yflux_sus.rst

.. include:: boundary_conditions/yflux_bv.rst

.. include:: boundary_conditions/yflux_hor.rst

.. include:: boundary_conditions/yflux_orr.rst

.. include:: boundary_conditions/yflux_user.rst

.. include:: boundary_conditions/yflux_alloy.rst

.. include:: boundary_conditions/ytotalflux_const.rst

.. include:: boundary_conditions/vl_equil.rst

.. include:: boundary_conditions/vl_poly.rst

.. include:: boundary_conditions/vl_equil_pseudorxn.rst

.. include:: boundary_conditions/is_equil_pseudorxn.rst

.. include:: boundary_conditions/surface_charge.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 7: Continuity Equation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The continuity equation rarely requires a boundary condition as it represents an overall mass
balance constraint on the velocity field for the fluid, viz. normally it is used to enforce
incompressibility. Boundary conditions for pressure are most often put on the fluid-momentum
equations as a part of the stress condition at an inflow or outflow plane (see for example boundary
condition cards *FLOW_PRESSURE, FLOW_HYDROSTATIC*, etc. ). On occasion, however, we
can use a pressure condition as a pressure datum, as the Dirichlet pressure condition below
allows, though the user must keep in mind that it is a condition on continuity and not momentum.
When using pressure stabilization, viz. PSPG techniques, then also there is an occasional need for
a boundary condition on this equation.

.. include:: boundary_conditions/p.rst

.. include:: boundary_conditions/pspg.rst

.. include:: boundary_conditions/pressure_datum.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 8: Porous Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following conditions are applied as boundary conditions to the porous-flow equations. These
conditions include strong Dirichlet conditions, such as hard sets on porous phase pressure on a
boundary as a constant or function of position, weak-form conditions, such as a specified phase
flux from a convective mass transfer model or a constant flux, and a host of interfacial conditions
for impregnation, etc. The porous flow equations are actually scalar equations that represent
component mass balances. Specifically, there is one component mass balance for the liquid phase,
one for the gas phase, and one for the solid phase. The corresponding three dependent variables in
these balances are the liquid phase pressure, the gas phase pressure, and the porosity, respectively.
These variables are related to the flow through a boundary by their normal gradients (Darcy’s law
formulation) and to the local inventory of liquid and gas through the saturation function. These
implicit terms can often lead to some confusion in setting the boundary conditions so it is
recommended that the user consult the supplementary documentation referenced in the following
porous boundary condition cards.

.. include:: boundary_conditions/porous_liq_pressure.rst

.. include:: boundary_conditions/porous_liq_flux_const.rst

.. include:: boundary_conditions/porous_gas_pressure.rst

.. include:: boundary_conditions/porous_gas.rst

.. include:: boundary_conditions/porous_gas_flux_const.rst

.. include:: boundary_conditions/porous_conv.rst

.. include:: boundary_conditions/porous_flux.rst

.. include:: boundary_conditions/porous_pressure.rst

.. include:: boundary_conditions/p_liq_user.rst

.. include:: boundary_conditions/porous_temperature.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 9: Stress Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following conditions provide a means to set boundary conditions for the hyperbolic
viscoelastic stress equations

.. include:: boundary_conditions/s11.rst

.. include:: boundary_conditions/s12.rst

.. include:: boundary_conditions/s13.rst

.. include:: boundary_conditions/s22.rst

.. include:: boundary_conditions/s23.rst

.. include:: boundary_conditions/s33.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 10: Gradient Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As companion equations to the viscoelastic stress equations, a continuous velocity gradient is determined through the so-called Velocity Gradient Equations. These boundary conditions are of the Dirichlet type and can be used to put conditions on this class of equations.

.. include:: boundary_conditions/g11.rst

.. include:: boundary_conditions/g12.rst

.. include:: boundary_conditions/g13.rst

.. include:: boundary_conditions/g21.rst

.. include:: boundary_conditions/g22.rst

.. include:: boundary_conditions/g23.rst

.. include:: boundary_conditions/g31.rst

.. include:: boundary_conditions/g32.rst

.. include:: boundary_conditions/g33.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 11: Shear Rate Equation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The single boundary condition in this category is used to set a Dirichlet condition for the scalar
shear rate equation. This differential equation is employed by the Phillips model for a suspension
constitutive equation.

.. include:: boundary_conditions/sh.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 12: Fill Equation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The so-called *Fill* equation is used by the volume-of-fluid and level-set Eulerian interface
tracking in *Goma*. Basically it is a statement of Lagrangian invariance and is hence a hyperbolic
statement of the so-called kinematic equation. Given a velocity field, this equation advances the
fill function as a set of material points; hence material surfaces remain ostensibly intact. The
boundary conditions in this section are used to specify the level-of-fill at a boundary at which a
fluid of a specific phase is flowing into the problem domain.

.. include:: boundary_conditions/f.rst

.. include:: boundary_conditions/fill_inlet.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 13: Potential Equation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The *Potential* equation is a Laplace equation for the voltage (potential) given a charge distribution
in a dielectric medium or a voltage or current boundary condition in an electrically conductive
medium. The following boundary conditions allow the current or voltage to be set on a boundary.

.. include:: boundary_conditions/current.rst

.. include:: boundary_conditions/current_user.rst

.. include:: boundary_conditions/volt.rst

.. include:: boundary_conditions/current_bv.rst

.. include:: boundary_conditions/current_hor.rst

.. include:: boundary_conditions/current_orr.rst

.. include:: boundary_conditions/volt_user.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 14: Fluid-Solid Interaction
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is a special group of boundary conditions for problems in which there are two distinct
material phases (fluid and solid) with relative motion between them. These BC’s provide a means
to apply conditions to a moving boundary with sensitivities to variables in both phases. These
problems are formulated in Goma as overset-grid or phase function problems.

.. include:: boundary_conditions/lagrange_no_slip.rst

.. include:: boundary_conditions/overset_fluid_solid_baaijens_fluid_solid.rst

.. include:: boundary_conditions/overset_solid_fluid_baaijens_solid_fluid.rst

.. include:: boundary_conditions/f1_f2_f3_f4_f5.rst

.. include:: boundary_conditions/pf_capillary.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 15: Level Set Interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These boundary conditions are designed to apply conditions in materials along surfaces whose
position is monitored using Level Set Interface Tracking.

.. include:: boundary_conditions/ls_adc.rst

.. include:: boundary_conditions/ls_ca_h.rst

.. include:: boundary_conditions/ls_capillary.rst

.. include:: boundary_conditions/ls_flow_pressure.rst

.. include:: boundary_conditions/ls_fluid_solid_contact.rst

.. include:: boundary_conditions/ls_inlet.rst

.. include:: boundary_conditions/ls_no_slip.rst

.. include:: boundary_conditions/ls_q.rst

.. include:: boundary_conditions/ls_qrad.rst

.. include:: boundary_conditions/ls_qlaser.rst

.. include:: boundary_conditions/ls_recoil_pressure.rst

.. include:: boundary_conditions/ls_vapor_ls_qvapor.rst

.. include:: boundary_conditions/ls_yflux.rst

.. include:: boundary_conditions/sharp_blake_velocity.rst

.. include:: boundary_conditions/sharp_ca_2d.rst

.. include:: boundary_conditions/sharp_cox_velocity.rst

.. include:: boundary_conditions/sharp_hoffman_velocity.rst

.. include:: boundary_conditions/sharp_wetlin_velocity.rst

.. include:: boundary_conditions/wetting_speed_blake.rst

.. include:: boundary_conditions/wetting_speed_cox.rst

.. include:: boundary_conditions/wetting_speed_hoffman.rst

.. include:: boundary_conditions/wetting_speed_linear.rst

.. include:: boundary_conditions/linear_wetting_sic.rst

.. include:: boundary_conditions/blake_dirichlet.rst

.. include:: boundary_conditions/cox_dirichlet.rst

.. include:: boundary_conditions/hoffman_dirichlet.rst

.. include:: boundary_conditions/velo_slip_ls.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 16: Shell Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These boundary conditions are applied to shell equations, a special category of equations
applied on 1D boundaries of 2D surfaces in Goma.

.. include:: boundary_conditions/shell_surface_charge.rst

.. include:: boundary_conditions/shell_surface_charge_sic.rst

.. include:: boundary_conditions/surface_electric_field.rst

.. include:: boundary_conditions/sh_tens.rst

.. include:: boundary_conditions/sh_k.rst

.. include:: boundary_conditions/sh_fluid_stress.rst

.. include:: boundary_conditions/lub_press.rst

.. include:: boundary_conditions/grad_lub_press.rst

.. include:: boundary_conditions/shell_filmp.rst

.. include:: boundary_conditions/shell_filmh.rst

.. include:: boundary_conditions/shell_partc.rst

.. include:: boundary_conditions/shell_grad_fp.rst

.. include:: boundary_conditions/shell_grad_fp_nobc.rst

.. include:: boundary_conditions/shell_grad_fh.rst

.. include:: boundary_conditions/shell_grad_fh_nobc.rst

.. include:: boundary_conditions/shell_grad_pc.rst

.. include:: boundary_conditions/shell_lubp_solid.rst

.. include:: boundary_conditions/shell_temp.rst

.. include:: boundary_conditions/shell_open_press_shell_open_press_2.rst

.. include:: boundary_conditions/shell_sh_fp_flux.rst

.. include:: boundary_conditions/lubp_sh_fp_match.rst

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Category 17: Acoustic Equations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These boundary conditions are applied to acoustic equations (Preal, Pimag, Reyn_stress).

.. include:: boundary_conditions/apr.rst

.. include:: boundary_conditions/apr_plane_tran.rst

.. include:: boundary_conditions/api.rst

.. include:: boundary_conditions/api_plane_tran.rst

.. include:: boundary_conditions/end_of_bc.rst
