## Numerical model inputs for Mondy et.al. 2018: 
# The role of asthenospheric flow during rift propagation and breakup

Please see the paper here: https://doi.org/10.1130/G39674.1

### Abstract:
Continental rifting precedes the breakup of continents, leading to the formation of passive margins and oceanic lithosphere. Although rifting dynamics is classically described in terms of either active rifting caused by active mantle upwelling, or passive rifting caused by far-field extensional stresses, it was proposed that a transition from passive to active rifting can result from changes in buoyancy forces due to localized thinning of the lithosphere. Three-dimensional numerical experiments of rifting near an Euler pole allow the quantification of these buoyancy forces and show that gravitational stresses are strong enough not only to sustain rifting and drive axis-parallel motion in the asthenosphere dome, but also to promote along-axis asthenospheric flow and to drive the propagation of the rift tip toward its rotation pole. We show that gradients of gravitational potential energy due to the presence of the dome of asthenosphere induce time-dependent phases of compressional and transcurrent stress regimes, despite an overall divergent plate setting. Our experiments predict overdeepened bathymetry at the tip of the propagating rift, as well as the variability of focal mechanisms of shallow seismic events similar to those observed in such a setting. We also explain the episodes of basin inversion documented in many rifted continental margins.

### How to read these files:
Both models (the rotational and orthogonal) are very similar, with only the boundary conditions changed. Pick one model to look at, and you should see a list of files like this:
```
LICENSE
LMR.xsd
lmrAuxiliary.xml
lmrInitials.xml
lmrMain.xml
lmrMaterials.xml
lmrNumerics.xml
lmrPassiveTracers.xml
lmrRheologyLibrary.xml
lmrRunModel.py
lmrSolvers.xml
lmrStart.xml
lmrThermalBoundaries.xml
lmrThermalEquilibration.xml
lmrVelocityBoundaries.xml
readme.rst
```
#### lmrMaterials.xml
This file is the best place to start. It defines how the materials in the model are laid out and behave. The first half shows the geometric layout of each layer of material, and the second half applies the rheologies (densities, flow laws, special behavious such as melting).

#### lmrVelocityBoundaries.xml
This file defines the velocity boundary conditions used to drive the model.

#### lmrThermalBoundaries.xml
This file defines the thermal boundary conditions.

#### lmrRheologyLibrary.xml
A file full of predefined rheologies that can be applied to materials.

#### The other XML files
For a beginner, there is almost no reason to look in the other XML files, unless out of interest. They mostly describe the more numerical aspects that Underworld needs to get going.


### Software requirements:
These models rely on a custom version of Underworld 1, which can be found here: https://github.com/OlympusMonds/EarthByte_Underworld


It's not the easiest thing to install, but there are some instructions here: https://github.com/OlympusMonds/lithospheric_modelling_recipe/wiki/Installing-Underworld-for-the-LMR

### How to run:
Note:
You need a big computer to run these models. For the paper, each one took about 2 weeks on 768 CPUs. Reducing the models to ~4 km resolution helps reduce the load a lot.

1) Download the input files (or git clone them) on your computer.
2) Go into either the `rotational` or `orthogonal` folder, and open up `lmrStart.xml`.
3) You will need to modify this structure:
    ```
    <Underworld_Execution>
       <Underworld_binary> /group/partner985/software/underworld2_intel/libUnderworld/build/bin/Underworld </Underworld_binary> <!-- Replace with your own path to UW -->
        <CPUs> 6 </CPUs>
        <supercomputer_mpi_format> true </supercomputer_mpi_format>
        <!--parallel_command> aprun -B </parallel_command-->
        <parallel_command> aprun -n 768 -N 24 </parallel_command>
        <verbose_run> true </verbose_run>
    </Underworld_Execution>
    ```
    `<Underworld_binary>` needs to point to your Underworld binary. 
    
    
    `<supercomputer_mpi_format>` when false: runs Underworld `mpirun -np $CPUs $Underworld_binary`; when true: runs Underworld `mpirun $Underworld_binary`
    
    
    `<parallel_command>` if defined, will replace `mpirun` with whatever you put in here.
    
4) Now run the model to produce a thermally equilibrated initial condition. To do this, run the command `python lmrRunModel.py` on a relatively small number of CPUs. The model will run for 1 Gyr.

5) Once completed, open `lmrStart.xml` again, and changed this line:
    ```
     <run_thermal_equilibration_phase> true </run_thermal_equilibration_phase>
    ```
    to
    ```
     <run_thermal_equilibration_phase> false </run_thermal_equilibration_phase>
    ```
6) Now run `python lmrRunModel.py` on a lot of CPUs, and wait for a while to see the results.
