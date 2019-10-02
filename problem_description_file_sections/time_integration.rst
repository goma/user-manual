Time Integration Specifications
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

