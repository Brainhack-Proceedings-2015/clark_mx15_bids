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
- initials: KJG
  surname: Gorgolewski
  firstname: Krzysztof J.
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

contrib: RCC and KJG provided supervision and reference, DJC and KJG wrote the software, DJC and KJG performed tests, and DJC wrote the report.
  
bibliography: brainhack-report

gigascience-ref: REFXXX
...

#Introduction
Data acquired during neuroimaging experiments can be organized in many different ways. This stems from inhomogeneity in scanner software, various DICOM and NIFTI tools, and custom data organizing scripts within diffierent laboratories. The Brain Imaging Data Structure (BIDS) specification\cite{Gorgolewski15-2} provides a simple, straightforward solution to this problem by introducing an intuitive standard for neuroimaging data organization. The widespread adoption of BIDS can be facilitated through incorporating this standard into sofware projects used for neuroimaging analysis. The goal of this Brainhack project is to demonstrate the integration of BIDS into a popular neuroimaging tool, the Configurable Pipeline for the Analysis of Connectomes (C-PAC).

#Approach
C-PAC performs anatomical and functional MRI data pre-processing by gathering a list of subject-indexed input images, or \emph{data bundles}, and sending the data through a user-configurable processing pipline to produced the desired outputs. Incorporating the BIDS standard into C-PAC requires allowing users to specify if their data is in a custom format (as C-PAC has traditionally worked) or BIDS.

##### \texttt{C-PAC subject list builder}:
Currently, the GUI in C-PAC allows users to specify anatomical and functional filepath templates that correspond to how the input data is organized on their computer. C-PAC will then gather any images that match the template patterns and organize the information into C-PAC-compatible data bundles for later processing. The GUI also allows for further customizations in the data bundles with site and participant-specific exclusion options. This has been a fairly reliable system thus far, as it allows for user-specific data organization.

\begin{figure}[h!]
  \includegraphics[width=.47\textwidth]{old_gui}
  \caption{\label{custom_gui} C-PAC subject list builder using custom template}
\end{figure}

However, this system is prone to errors. It can be seen in figure \ref{custom_gui} that the bracketed keywords along with glob-compatible wildcard characters (e.g. "*") are used to specify how the data is organized so C-PAC can traverse the image directories to build the data bundles. These file patterns can sometimes grow to be quite large and prone to typos; users can also misunderstand the layout of their images or what C-PAC expects to see at the various keywords present in their data structure.

##### \texttt{C-PAC with BIDS}:
Using BIDS, C-PAC only requires a single base directory in order to build a complete subject list for processing. This eliminates the user tediously typing in the file pattern template of their input data with risk of error. Additionally, BIDS enforces metadata being present in the form of json files stored alongside the imaging data. C-PAC can utilize the metadata from these files to build more informative data bundles - for example, encoding scan acquisition parameters or task-based information. This data can then be used later during the pipeline run to further enhance the way the data is processed.

To do this, a \texttt{bids\_flag} parameter was added to the current subject list builder module. This flag builds the anatomical and functional file pattern templates by walking through the BIDS file hierarchy. These templates are then returned to the current subject list builder, which can then build the data bundles in the same as way before. This approach minimized modifications to the data gathering algorithm while providing for a robust way to ensure all data is found and returned properly.

The new implementation takes advantage of one of the many BIDS utilities openly available: the BIDS meta-data tool\cite{Gorgolewski15}. This tool provides the site, participant, session, and series-level indicators to the builder without needing the user to manually enter any keywords. This tool takes advantage of the fixed organization scheme and the presence of JSON files to deliver all of this information reliably and efficiently.

#Results
The updated C-PAC GUI reflects the "BIDS" and "Custom" options avilable for the user to specify for their data - as seen in figure \ref{bids_gui}. This feature was added to demonstrate the option to the user, however, in the future this option would be more elegantly displayed via a radio button with the input fields dynamically changing to reflect the type of input desired.

\begin{figure}[h!]
  \includegraphics[width=.47\textwidth]{new_gui}
  \caption{\label{bids_gui} C-PAC subject list builder using BIDS directory}
\end{figure}

The code changes were fairly straightforward and were cleanly inserted into the current builder module at \url{https://github.com/FCP-INDI/C-PAC/blob/test_dev/CPAC/utils/build_sublist.py}. Throughout the Brainhack, this code was fully implemented but still needs further testing and debugging to work on any given BIDS dataset.

# Conclusions
Incorporating the BIDS subject list builder into C-PAC is a great step forward in bringing the standard to a broader audience. Throughout the integration process, other technologies were discovered that could further enable input data gathering across a wide range of file system types, including FTP, SFTP, Zip, S3, and an array of virtual filesystems. With further development, the overhead of preprocessing one's own neuroimaging data for discovery science can be minized so scientists can focus on the results.
