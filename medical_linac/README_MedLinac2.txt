+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        MedLinac2						   |
+                                                          +
|                        README                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

The MedLinac2 advanced example (ML2) is a renewed version of the MedLinac advanced example developed by Michela Piergentili with the support of M.G.Pia and Franca Foppiano in 2004.


This application has been developed by the Geant4 users:
^Claudio Andenna,*Barbara Caccia with the support of Pablo Cirrone (LNS-INFN Catania, Italy) and with the contribute of Alessandro Occhigrossi*


^INAIL DIPIA - ex ISPESL and INFN Roma, gruppo collegato Sanita', Italy
*Istituto Superiore di Sanita' and INFN Roma, gruppo collegato Sanita', Italy
Viale Regina Elena 299, 00161 Roma (Italy)
tel (39) 06 49902246
fax (39) 06 49387075



---> A brief description

The example is based on a typical structure of a medical linear accelerator for Intensity Modulated Radiation Therapy (IMRT), such as Varian Clinac 2100 accelerator.
The user may choice a cubic phantom filled with water, a phantom filled with an equivalent lung-tissue with a inhomogeneity (a 6 cm sided PMMA cube) located in the centre of the phantom, or a DICOM phantom.
Two type of particle sources may be chosen, a random generator of electrons gun shooting the target or particles loaded from a phase space.
The program allows the generation of a plane phase space. However since the mother volume of this plane phase space is the accelerator volume it can be positioned only inside the accelerator main volume. Furthermore, the phase space can be calculated only if the accelerator is not rotated.

	The example package contains:
- source files (src, include, ml2.cc)
- GNUmakefile
- README_MedLinac2.txt
- ml2.mac and vis.mac macro files
- a launches directory as an example to use the package (pay attention to properly set the path inside the macro files). It contains also an example of dicom phantom as taken from the extended/medical/DICOM example.
- two log files (macLoop.log and macExp.log) obtained launching the ml2.mac (with the proper directories) with the /convergence/bCompareExp false and /convergence/bCompareExp true respectively. 


-----------------------------------------------------------------------
----> 1. Experimental set-up.

The elements simulated are:

1-A source of electrons (the distribution of the electron energy and the electron radial intensity was assumed Gaussian in direction and energy). The beam direction is along the z axis.
2-A target
3-A primary collimator
4-A vacuum window
5-A flattening filter
6-A ion chamber
7-A mirror
8-A light field reticle
9-Secondary movable collimators (jaws)
10-A simple Multi Leaf Collimator
11-Phantom1 ("fullWater") filled with water (cube of 30 cm sided)
12-Phantom2 ("BoxInBox") filled with G4_ICRP lung tissue (cube of 30 cm sided) with a inhomogeneity (PMMA 6 cm sided cube) in the centre and a PLEXIGLASS slab 1 cm thick on the surface of the phantom.
13-Phantom3 ("Dicom1") it is a dicom phantom generated starting from *.dcm files. It uses part of the code given in the extended/medical/DICOM example.

-----------------------------------------------------------------------
----> 2. Some main features

	Dicom phantom
The dicom *.dcm files can be read and used as input data to build a parameterized phantom according to the extended/medical/DICOM example. Only some of the extended example classes have been used and they have been adapted to the architecture of the ML2 program avoiding a deep change in the code. Consequently the dicom part still reads the *.dcm files and translate them into *g4dcm and *g4dcmb files to be used later to build the phantom (for more information on the dicom format, see the readme file in the extended/medical/DICOM example). However new materials have been added and a different way to check if the material defined in the data.dat file are implemented has been realized.
As an example three contiguous dicom images are supplied as an example to use. You can find the three dicom images reproduced as a sequence in the movie CT_abdomen.mov.



	Accelerator rotation:
it is possible to arbitrary rotate the accelerator around the X axis (/accelerator/rotationX 20 deg)
it is possible to rotate the accelerator along the y axis of 90 deg (/accelerator/rotation90Y true/false)

The last possibility has been implemented because there was an experimental conflict between the dicom phantom and the accelerator world. Both have the z direction as the main directions, i.e. the dicom slices grows along the z direction and the accelerator gun shoots along the z axis. Consequently it is impossible to irradiate transversally to the phantom (unless the accelerator is rotated along the Y axis of 90 deg).

	Phantom translation:
This feature has been added to allow the possibility to irradiate a particular area of the phantom. The accelerator rotates around the isocentre set at coordinates (/phantom/centre  0,0,0) of the world, consequently to irradiate a selected area of the phantom this has to be located at the world centre. 


A check of the physical volumes overlap is performed each time the geometry is changed, but not in the case of the dicom phantom because of the very high number of physical volumes.

Several phantom centres and accelerator rotations (around the X axis) can be independently assigned by the user through the macro file.
The /accelerator/rotation90Y, the /accelerator/rotationX and the /phantom/centre are the only interactive commands to change the geometry during the run. The first doesn't refresh the screen.

-----------------------------------------------------------------------
----> 3. How to run the example. 
 
The example runs with the ml2.mac macro file.
This file contains some setup information and the name of other macro files (the accelerator macro file -"acc1.mac"- and, if required by the phantom chosen the phantom macro file -"dicomPhantom.mac"-) describing the chosen accelerator and phantom. If the dicom phantom is chosen other data files are necessary (typically data.dat, CT2Denstity.dat, ColourMap.dat) according to the extended example. 

Inside the ml2.mac file there is a flag "/OnlyVisio" that has to be set to "true" to switch on the visualization mode calling the "vis.mac" file ("false" for the modality without visualization). Generally the visualization should be used to check the set up of the simulation while to compute the dose is faster to switch it to false.

The example needs as input data the macro file (like "ml2.mac") and a seed number.
Medlinac2 can be launched using a macro file:
$G4WORKDIR/bin/Linux-g++/ml2
that is equivalent to
$G4WORKDIR/bin/Linux-g++/ml2 ml2.mac 1

while the string
$G4WORKDIR/bin/Linux-g++/ml2 user_macro_file
is equivalent to
$G4WORKDIR/bin/Linux-g++/ml2 user_macro_file 1


-----------------------------------------------------------------------
----> 4. The physics

The ML2Physicslist class allows the activation of all the physic models either using the physics lists 
or the physics package lists. The standard electromagnetic option3 model is the default model.

------------------------------------------------------------------------
----> 5. Simulation output 

The output of the ML2 example is:

1) A phase space file containing the data of the particles hitting the plane phase space. 
The file contains:
a progressive number, position, direction, kinetic energy, PDGE code of the particle, PDG code of the primary particle, progressive number of the primary particle generating the event in the phase space

2) An output file written in matlab format generated from an experimental data file (if provided). The coordinates of the voxels don't follow any phantom translation, in case the user has to properly modify the code. 
The file contains:
position of the voxels and experimental dose values as given in the experimental data file,
cumulative dose, cumulative square dose, number of events in the voxels, cumulative dose normalized to the experimental data (if provided), cumulative square dose normalized to the experimental data (if provided)

3) Some output files containing the ROG results, one for each simulated geometry and one collecting all the simulated geometries results.
Each file contains (among the others):
index of the material of the physical volume, position, indexes of the voxel according to the voxelization of the ROG, cumulative dose, cumulative square dose, number of events in the voxel

------------------------------------------------------------------------
----> 6. Main differences with the previous ML2 release.

This release of the example has been deeply modified and it is hard to detailed enumerate the differences with the previous one. What it will be described here are just the main features modified or added.

The main mac file has been modified. Some new parameters have been added and others have been modified to have more intuitive names.

The SSD parameter has been deleted.
The environmental variables ML2FILEIN and ML2FILEOUT are not been used any more and each file has to be given with its full path.

The output file containing the ROG results has been changed. The first data now is the id of the material name of the physical volume and not its name to allow an easier way to filter different materials (and hopefully the physical volumes).

------------------------------------------------------------------------
----> 7. Contacts

If you have any questions or wish to notify of updates and/or modification
please contact:
 	
Barbara Caccia at barbara.caccia@iss.it
 
Claudio Andenna at claudio.andenna@ispesl.it, claudio.andenna@iss.infn.it


Istituto Superiore di Sanita' and INFN Roma, gruppo collegato Sanita', Italy
Viale Regina Elena 299, 00161 Roma (Italy)
tel (39) 06 49902246
fax (39) 06 49387075

