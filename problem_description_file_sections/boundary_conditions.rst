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

.. include:: /chapters/ch4/section_10/4.10.251_ls_recoil_pressure.txt

.. include:: /chapters/ch4/section_10/4.10.253_ls_vapor_ls_qvapor.txt

.. include:: /chapters/ch4/section_10/4.10.255_ls_yflux.txt

.. include:: /chapters/ch4/section_10/4.10.257_sharp_blake_velocity.txt

.. include:: /chapters/ch4/section_10/4.10.259_sharp_ca_2d.txt

.. include:: /chapters/ch4/section_10/4.10.261_sharp_cox_velocity.txt

.. include:: /chapters/ch4/section_10/4.10.263_sharp_hoffman_velocity.txt

.. include:: /chapters/ch4/section_10/4.10.265_sharp_wetlin_velocity.txt

.. include:: /chapters/ch4/section_10/4.10.267_wetting_speed_blake.txt

.. include:: /chapters/ch4/section_10/4.10.269_wetting_speed_cox.txt

.. include:: /chapters/ch4/section_10/4.10.270_wetting_speed_hoffman.txt

.. include:: /chapters/ch4/section_10/4.10.271_wetting_speed_linear.txt

.. include:: /chapters/ch4/section_10/4.10.273_linear_wetting_sic.txt

.. include:: /chapters/ch4/section_10/4.10.275_blake_dirichlet.txt

.. include:: /chapters/ch4/section_10/4.10.276_cox_dirichlet.txt

.. include:: /chapters/ch4/section_10/4.10.278_hoffman_dirichlet.txt

.. include:: /chapters/ch4/section_10/4.10.280_velo_slip_ls.txt

.. include:: /chapters/ch4/section_10/4.10.281_category_16.txt

.. include:: /chapters/ch4/section_10/4.10.282_shell_surface_charge.txt

.. include:: /chapters/ch4/section_10/4.10.284_shell_surface_charge_sic.txt

.. include:: /chapters/ch4/section_10/4.10.286_surface_electric_field.txt

.. include:: /chapters/ch4/section_10/4.10.288_sh_tens.txt

.. include:: /chapters/ch4/section_10/4.10.290_sh_k.txt

.. include:: /chapters/ch4/section_10/4.10.292_sh_fluid_stress.txt

.. include:: /chapters/ch4/section_10/4.10.294_lub_press.txt

.. include:: /chapters/ch4/section_10/4.10.295_grad_lub_press.txt

.. include:: /chapters/ch4/section_10/4.10.297_shell_filmp.txt

.. include:: /chapters/ch4/section_10/4.10.298_shell_filmh.txt

.. include:: /chapters/ch4/section_10/4.10.299_shell_partc.txt

.. include:: /chapters/ch4/section_10/4.10.300_shell_grad_fp.txt

.. include:: /chapters/ch4/section_10/4.10.301_shell_grad_fp_nobc.txt

.. include:: /chapters/ch4/section_10/4.10.302_shell_grad_fh.txt

.. include:: /chapters/ch4/section_10/4.10.303_shell_grad_fh_nobc.txt

.. include:: /chapters/ch4/section_10/4.10.304_shell_grad_pc.txt

.. include:: /chapters/ch4/section_10/4.10.305_shell_lubp_solid.txt

.. include:: /chapters/ch4/section_10/4.10.306_shell_temp.txt

.. include:: /chapters/ch4/section_10/4.10.308_shell_open_press_shell_open_press_2.txt

.. include:: /chapters/ch4/section_10/4.10.309_shell_sh_fp_flux.txt

.. include:: /chapters/ch4/section_10/4.10.310_lupb_sh_fp_match.txt

.. include:: /chapters/ch4/section_10/4.10.311_apr.txt

.. include:: /chapters/ch4/section_10/4.10.313_apr_plane_tran.txt

.. include:: /chapters/ch4/section_10/4.10.315_api.txt

.. include:: /chapters/ch4/section_10/4.10.317_api_plane_tran.txt

.. include:: /chapters/ch4/section_10/4.10.318_end_of_bc.txt
