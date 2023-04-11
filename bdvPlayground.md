# Inspecting multiple Tissue Cores using the BigDataViewer and BigDataViewer Playground in Fiji

Instructions by Anna Klemm, BioImage Informatics Facility, SciLifeLab, Sweden, March 2023.

**Background:**  
Images from tumor samples often show high variability in stain intensities. Further artefacts can be out-of-focus regions, air bubbles, edge effect of staining. 
Before starting analysis it is important to visually inspect the data.

**Aim:**  
Inspect image quality and staining intensities in the different channels in different cores.

**Tools:**  
We are using the BigDataViewer and BigDataViewer (BDV) Playground in Fiji.  
Information BigDataViewer: https://imagej.net/plugins/bdv/  
Information BDV Playground: [https://imagej.github.io/plugins/bdv/playground/bdv-playground](https://imagej.github.io/plugins/bdv/playground/bdv-playground)  
Introduction to both: [NEUBIAS video](https://www.youtube.com/watch?v=LHI7vXiUUms)

**Literature:**  
Pietzsch, T., Saalfeld, S., Preibisch, S., & Tomancak, P. (2015).   
BigDataViewer: visualization and processing for large image data sets.   
[Nature Methods, 12(6), 481–483. doi:10.1038/nmeth.3392](https://www.nature.com/articles/nmeth.3392)

## Example data set
The example data are 20 samples from lower grade glioma tumor patients ("tumor cores"). 
| Channel | Stain | Marker |  Main Cell Type |
|--------|:--------:|:--------:|:--------:|
| 1 | DAPI |  Nuclei | all |
| 2 | Opal 480 |    IBA1   | macrophages |
| 3 | Opal 520 | Ki67 | dividing cells |
| 4 | Opal 540 |    TMEM119   | microglia |
| 5 | Opal 570 | NeuroC | neurons |
| 6 | Opal 620 |    MBP   | (oligodendrocyte)|
| 7 | Opal 650 | mutIDH1 | glioma |
| 8 | Opal 690 |    CD34   | endothelial |
| 9 | Opal 780 | GFAP | astrocyte |
| 10 | Autofluorescence |    -   |    -   |


All procedures involving human specimens were 
approved by the regional ethical committee in Gothenburg (Ep Dnr: 1067–16), and informed consent was given by the patients. You can read more about the samples and the staining procedure in Solorzano *et al.*. 

**Literature:**  
Solorzano L, Wik L, Olsson Bontell T, Wang Y, Klemm AH, Öfverstedt J, Jakola AS, Östman A, Wählby C. (2021)  
Machine learning for cell classification and neighborhood analysis in glioma tissue.   
[Cytometry A. 99(12), 1176-1186. doi: 10.1002/cyto.a.24467](https://onlinelibrary.wiley.com/doi/10.1002/cyto.a.24467)

## Install BDV-Playground
Enable the `PTBIOP` Fiji update site via `Help>Update>Manage Update Sites`.  
A more detailled description on how to enable an update site can be found here: https://imagej.net/update-sites/following.

## Loading data into the BDV Playground
* Run `Plugins › BigDataViewer-Playground › BDVDataset › Open [BioFormats Bdv Bridge (Basic) (Legacy)]` to load images into the BDV-Playground.
* Choose the right physical unit of the dataset. Here: `MICROMETER`
* Add the files to inspect, e.g. by using `Add folder content...` to add all images in a folder.
* Press `OK`.  

![](img_bdv/Screenshot_bdvpg_OpenBridge.png?raw=true "Screenshot")


## Navigation to single images ('sources').
BDV-Playgrounds builds a tree of the sources, which makes it easy to inspect the images. For example we can easily inspect a certain channel.

Let's try this out:
* Navigate to `dataset > Channel > All Sources`. 
* Right-click on the first image with the name `resolution #1- Opal 480` and choose `BDV - Show Sources`
* Activate `Auto Contrast` and click `OK`.
The Opal 480 channel of the first image core is loaded into the BigDataViewer.
Close the window again, since this was just a first test.

![](img_bdv/Screenshot_bdv_single_channel.png?raw=true "Screenshot")

## Displaying several core images in a grid
Our aim is to visualize all the core images in a grid, visualizing one single channel for inspection. Once displayed on a grid, we can visually compare e.g. staining intensities, artefacts etc.. between the individual tumor cores.
Steps:
* In the Fiji toolbar, choose ` Plugins › BigDataViewer-Playground › Sources › Display Sources On Grid`. 
* In the Sacs window, navigate to  `dataset > Channel`. Click on `Opal 480` to select the Opal 480 channel of all the data sets (20 images in the example data set).
* Let's choose `NColumns = 5` to display the 20 images in a 4x5 grid. 
* Press `OK`.

![](img_bdv/Screenshot_bdvpg_DisplaySourcesOnGrid.png?raw=true "Screenshot")

## Adjustments and Navigation within the BDV
Please have a look on the BDV documentation to learn about navigation and displaying data: https://imagej.net/plugins/bdv/

For this exercise:
* hover over the right side of the BDV window to see two white arrows on blue background - click to expand the menu.
* select all sources using `SHIFT+click`
* select white as a display color for all the sources and the contrast limits `[0,10]`

The channel Opal 480 is now visible for all the 20 cores and we can inspect how well the different cores were stained. 

Basic Navigation:
* pan while holding the right click
* zoom: hold `CTRL+SHIFT` while zooming with the mouse wheel.

![](img_bdv/BDV_navigation.gif?raw=true "Screenshot")

## Acknowledgements
We thank Nicolas Chiaruttini, EPFL, Lausanne, Switzerland, for tipps in using the BDV-Playground for this dataset.







