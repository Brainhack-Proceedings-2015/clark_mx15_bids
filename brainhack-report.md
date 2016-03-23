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
Data acquired during neuroimaging experiments can be organized in many ways. This stems from differences in scanner software, various DICOM and NIFTI tools, and custom data organizing scripts within different laboratories. The Brain Imaging Data Structure (BIDS) specification\cite{Gorgolewski15-2} provides a simple, straightforward solution to this problem by introducing an intuitive standard for neuroimaging data organization. The widespread adoption of BIDS can be facilitated through incorporating this standard into software projects used for neuroimaging analysis. These software packages will in turn benefit from the homogenous data structure and ease of specifying data acquisition parameters afforded by BIDS. The goal of this Brainhack project was to integrate BIDS into the Configurable Pipeline for the Analysis of Connectomes (C-PAC)\cite{CraddockCPAC}, a python software built on Nipype\cite{Gorgolewski2011} for the high-throughput analysis of resting state fMRI data. 

#Approach

Processing data with C-PAC begins with specifying the paths of the anatomical and functional files to be processed, along with their corresponding acquisition parameters. This is accomplished in a semi-automatic procedure in which the user specifies templates that describe the file organization and then a script walks this structure to find the data. The resulting subject list can then be partnered with a pipeline configuration and submitted to C-PAC for processing. We extended this functionality to natively understand BIDS, so that data that conforms to this standard can be configured to run through C-PAC with minimal user intervention. 

##### \texttt{C-PAC with BIDS}:

A BIDS flag was added to the subject list builder along with a text box for the user to specify the base directory of the data file structure. The BIDS file hierarchy is then traversed to build anatomical and functional file pattern templates. These templates are returned to the main subject list builder function, which runs the same way as if using user specified file path templates. This approach minimized modifications to the data gathering algorithm while providing for a robust way to ensure all data is found and returned properly. Additional scanning parameters that are required to complete the processing (repetition time, slice timing information, etc.) are read from BIDS specified JSON files that are stored alongside the imaging data. 

The new implementation takes advantage of one of many BIDS utilities openly available: the BIDS meta-data tool\cite{Gorgolewski15} (\url{https://github.com/INCF/bidsutils}). This tool provides the subject, session, and run-level indicators to the builder without needing the user to manually enter any keywords; it takes advantage of the fixed organization scheme and the presence of JSON files to deliver all of this information reliably and efficiently. The tool is written in Python, which provided for easy integration into the C-PAC source code. It works for BIDS datasets stored locally as well as those available through remotely through Amazon S3.

#Results
The updated C-PAC GUI reflects the "BIDS" and "Custom" options - as seen in figure \ref{bids_gui} - with the "Custom" option allowing users to specify their data structure as in previous versions of C-PAC. In the future this option would be more elegantly displayed via a radio button with the input fields dynamically changing to reflect the type of input desired.

\begin{figure}[ht!]
  \includegraphics[width=.47\textwidth]{new_gui}
  \caption{\label{bids_gui} C-PAC subject list builder using BIDS directory}
\end{figure}

The code changes were fairly straightforward and were cleanly inserted into the current builder module at \url{https://github.com/FCP-INDI/C-PAC/blob/test_dev/CPAC/utils/build_sublist.py}. The implementation developed during Brainhack is feature full, but will require more testing in the future.

# Conclusions
Incorporating the BIDS subject list builder into C-PAC is a great step forward in bringing the standard to a broader audience. Throughout the integration process, other technologies were discovered that could further enable input data gathering across a wide range of file system types, including FTP, SFTP, Zip, S3, and an array of virtual filesystems. With further development, the overhead of preprocessing ones own neuroimaging data for discovery science can be minimized so scientists can focus on the results.
