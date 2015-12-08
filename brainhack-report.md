---
event: '2015 Brainhack Americas (MX)'

title:  'Integrating the Brain Imaging Data Structure (BIDS) standard into C-PAC'

author:

- initials: DJC
  surname: Clark
  firstname: Daniel
  email: daniel.clark@childmind.org
  affiliation: aff1
  corref: aff1
- initials: CJG
  surname: Gorgolewski
  firstname: Chris
  email: krzysztof.gorgolewski@gmail.com
  affiliation: aff2
- initials: RCC
  surname: Craddock
  firstname : R. Cameron
  email: ccraddock@nki.rfmh.org
  affiliation: aff1, aff3


affiliations: 

- id: aff1
  orgname: 'Center for the Developing Brain, Child Mind Institute'
  street: 445 Park Ave
  postcode: 10022
  city: New York
  state: New York
  country: USA
- id: aff2
  orgname: 'Poldrack Lab, Department of Psychology, Stanford University'
  street: 450 Serra Mall
  city: Stanford
  state: California
  postcode: 94305
  country: USA
- id: aff3
  orgname: 'Computational Neuroimaging Lab, Center for Biomedical Imaging and Neuromodulation, Nathan Kline Institute for Psychiatric Research'
  street: 140 Old Orangeburg Rd
  postcode: 10962
  city: Orangeburg
  state: New York
  country: USA

url: https://bids.neuroimaging.io

coi: None

acknow: The authors would like to thank the organizers and attendees of Brainhack MX and the developers of C-PAC. This project was funded in part by a Educational Research Grant from Amazon Web Services.

contrib: RCC and CJG provided supervision and reference, DJC and CJG wrote the software, DJC and CJG performed tests, and DJC wrote the report.
  
bibliography: brainhack-report

gigascience-ref: REFXXX
...

#Introduction
Data acquired during neuroimaging experiments can be organized in many different ways. This stems from inhomogeneity in scanner software, various DICOM and NIFTI tools, and custom data organizing scripts within diffierent laboratories. The Brain Imaging Data Structure (BIDS) specification\cite{Gorgolewski15-2} provides a simple, straightforward solution to this problem by introducing an intuitive standard for neuroimaging data organization. The widespread adoption of BIDS can be facilitated through incorporating this standard into sofware projects used for neuroimaging analysis. The goal of this Brainhack project is to demonstrate the integration of BIDS into a popular neuroimaging tool, the Configurable Pipeline for the Analysis of Connectomes (C-PAC).

#Approach
C-PAC performs anatomical and functional MRI data pre-processing by gathering a list of subject-indexed input images, or \emph{data bundles}, and sending the data through a user-configurable processing pipline to produced the desired outputs. Incorporating the BIDS standard into C-PAC requires allowing users to specify how their data is organized and thus adding a new feature to the current subject list builder.

##### \texttt{C-PAC subject list builder}:
Currently, the GUI in C-PAC allows users to specify anatomical and functional filepath templates that correspond to how the input data is organized on their computer or on Amazon Web Services (AWS) Standard Storage Service (S3) cloud-based service. C-PAC will then gather any images that match the template patterns and organize the information into C-PAC-compatible data bundles for later processing. The GUI also allows for further customizations in the data bundles with site and participant-specific exclusion options. This has been a fairly reliable system thus far, as it allows for user-specific data organization.

\begin{figure}[h!]
  \includegraphics[width=.47\textwidth]{gui_setup}
  \caption{\label{databundle_gather} C-PAC subject list builder window with custom template specified}
\end{figure}

However, this system is prone to errors. It can be seen in figure \ref{databundle_gather} that the "\{site\}", "\{participant\}", "\{session\}", and "\{series\}" keywords (only "\{participant\}" and "\{series\}" keywords are mandatory) along with glob-compatible wildcard characters (e.g. "*") are used to specify how the data is organized so C-PAC can traverse the image directories to build the data bundles. These file patterns can sometimes grow to be quite large and prone to typos; users can also misunderstand the layout of their images or what C-PAC expects to see at the various keywords present in their data structure.

##### \texttt{C-PAC with BIDS}:
The BIDS specification allows for users to specify the base directory of their input data as the only requirement for C-PAC to build a complete subject list for processing. This eliminates the user tediously typing in the file pattern template of their input data with risk of error. Additionally, BIDS enforces metadata being present in the form of json files stored alongside the imaging data. C-PAC can utilize the metadata from these files to build more informative data bundles - for example, encoding scan acquisition parameters or task-based information. This data can then be used later during the pipeline run to further enhance the way the data is processed.

#Results
There are many utilities openly available for re-organizing data from non-BIDS to BIDS format; the BIDS meta-data tool\cite{Gorgolewski15} is able to directly provide subject-specific metadata from a given filepath, which is used in the C-PAC data bundle. This tools also utilizes the Python packages \texttt{fs}

# Conclusions
Optimized versions of lFCD and DC achieved 4$\times$ to 30$\times$ decreases in computation time compared to C-PAC's Python implementation and decreased the memory footprint to less than 1 gigabyte. These improvements will dramatically increase the size of Connectomes analyses that can be performed using conventional workstations. Making this implementation available through AFNI ensures that it will be available to a wide range of neuroimaging researchers who do not have the wherewithal to implement these algorithms themselves.
