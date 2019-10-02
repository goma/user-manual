Post Processing Fluxes and Data
###################################

By *Post-processing Fluxes* we mean area-integrated fluxes that can be calculated for any flux
quantity across any surface demarcated by a side set. The area-integrated flux is in fact a total
flow rate across the boundary. Examples include heat-flow, total force of a liquid on a surface, and species flow (both diffusive and convective). The integrated flux quantities are output to a
specified file at each time step, together with the time stamp and the convective and diffusive
components. This capability is useful for extracting engineering results from an analysis, and can
further be used to as an objective function evaluator for engineering optimization problems (cf.
*Post Processing Flux Sensitivities* card below).

*Post Processing Data* output can be used to produce spatial {*value, x, y, z*} sets on a specified side set of any primitive variable in the problem, viz. pressure, x-component of velocity, etc. The quantity *value* is the value of the variable at a node in the side set, and x, y, z are the coordinates of the node.

.. include:: /chapters/ch4/section_14/4.14.1_post_processing_fluxes.txt

.. include:: /chapters/ch4/section_14/4.14.2_flux.txt

.. include:: /chapters/ch4/section_14/4.14.3_end_of_flux.txt

.. include:: /chapters/ch4/section_14/4.14.4_post_processing_data.txt

.. include:: /chapters/ch4/section_14/4.14.5_data.txt

.. include:: /chapters/ch4/section_14/4.14.6_end_of_data.txt

.. include:: /chapters/ch4/section_14/4.14.7_post_processing_flux_sensitivities.txt

.. include:: /chapters/ch4/section_14/4.14.8_flux_sens.txt

.. include:: /chapters/ch4/section_14/4.14.9_end_of_flux_sens.txt

.. include:: /chapters/ch4/section_14/4.14.10_post_processing_date_sensitivities.txt

.. include:: /chapters/ch4/section_14/4.14.11_data_sens.txt

.. include:: /chapters/ch4/section_14/4.14.12_end_of_data_sens.txt
