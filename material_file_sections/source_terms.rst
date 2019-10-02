Source Terms
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

