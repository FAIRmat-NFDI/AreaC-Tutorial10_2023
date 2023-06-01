# Part I: How to explore the NOMAD Archive and Repository.

In this part, we are going to learn how to find and explore the NOMAD Archive and Repository (in short, NOMAD-lab) for electronic structure data. A more extensive information can be found in the [FAIRmat tutorial 1](https://www.fairmat-nfdi.eu/events/fairmat-tutorial-1/tutorial-1-home) web.

## Exploring NOMAD

Go to the [NOMAD-lab website](https://nomad-lab.eu/nomad-lab/). 

There are two main versions when exploring NOMAD-lab: the stable version and the beta or staging version. As the current NOMAD infrastructure is constantly being updated, we recommend you to go to the [Beta version](https://nomad-lab.eu/prod/v1/staging/gui/search/entries). The link can be also found when scrolling down in the main landing page of NOMAD-lab or when clicking on the tab menu "Solutions".

<p align="center">
    <img src="../assets/part1_explore/beta1.png" alt="Finding the Beta in the website, option 1" width="45%" title="NOMAD Beta option 1">
    <img src="../assets/part1_explore/beta2.png" alt="Finding the Beta in the website, option 2" width="41.5%" title="NOMAD Beta option 2">
</p>


We land on the NOMAD Entries page, a very intuitive and easy-to-use Graphical User Interface (GUI). Here we can explore data according to our preferences. We can select the atoms that form the material, which crystal symmetry, etc., as well as selecting the methodology done for a calculation or experiment. We can also select the output properties, which for us is going to be mainly the menu “Electronic”. 



<p align="center">
    <img src="../assets/part1_explore/explore.png" alt="Explore GUI page" width="90%" title="Explore GUI page">
</p>
<p align="center">
    <img src="../assets/part1_explore/materials.png" alt="Materials menu" width="90%" title="Materials menu">
</p>

There are several options to filter from data in NOMAD. As for this tutorial, we will focus on electronic structure calculations, i.e., data obtained when solving Density Functional Theory (`DFT`), `GW` approximation, Bethe-Salpeter equation (`BSE`), projected tight-binding models (`Projection`), and Dynamical Mean-Field Theory (`DMFT`). 

All of these menus contain (meta)information important for these calculations, and we are aiming for users and experts on these fields to help us shaping these menus according to their preferences. If you feel some important quantity is missing, or maybe you want to extend to other electronic structure techniques, please contact us!

## Expert functionalities: FASTAPI calls and the NOMAD metainfo

Once you decide searching for a set of materials with certain properties derived from your prefered methodology, NOMAD gives you another tool to perform a query via API calls. This can be found when clicking the symbol `<>` on the GUI. In [Part II](part2.md), we will show you how to use this API query to get data and work with it in a Jupyter Notebook for analysis.

<p align="center">
    <img src="../assets/part1_explore/api.png" alt="API query" width="90%" title="API query">
</p>

Another functionality we will explore more in detail is the fact that not all the defined NOMAD metainfo is shown in the filter menus in the GUI. We decide to select a few quantities we think might be important, nevertheless, the full information of a given system, methodology, or property can be found under the Metainfo tab. NOMAD also has a web with all the definitions that you can find under the tab menu "Analyze > The NOMAD Metainfo".

<p align="center">
    <img src="../assets/part1_explore/nomadmetainfo.png" alt="The NOMAD metainfo" width="90%" title="The NOMAD metainfo">
</p>