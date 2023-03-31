# QuPath instructions for workshop

By Fredrik Nysjö, Anna Klemm

## Data

- Images: `LGG TMA 5_2_Core[1,10,B]...data.tif` and `LGG TMA 7_2_Core[1,1,E]...data.tif`
- Signature matrix: `signature_matrix.png`

The example data are samples from lower grade glioma tumor patients ("tumor cores"). 
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

## Creating a new QuPath project

Go to `Menu->File->Project->Create project`. This will ask you to create a new folder for the project, where any non-image data (annotations, trained classifiers, detections, etc.) will be stored.

For the new project, in the `Project` tab in the left panel, click on `Add images` and select the two .tif images from Dataset 1. You can also just drag-and-drop the images onto the QuPath window. Under `Set image type` select `Fluorescence`. Afterward, the project with the two images should look like this:

![](img/screenshot_project.png?raw=true "Screenshot")

## Core annotation

### Displaying individual image channels

Each image in the dataset contains image data for a single tissue-micro array (TMA) core with 10 channels (8 Opal marker channels + DAPI + an autofluorescent channel). Open the image for the first core (5_10_B), and use the Brightness & contrast tool from the toolbar (looks like a half-moon) to make only the DAPI channel visible in the viewport:

![](img/screenshot_brightness_contrast1.png?raw=true "Screenshot")

![](img/screenshot_brightness_contrast2.png?raw=true "Screenshot")

### Basic annotation drawing

To annotate which region in the core image that should be used for the cell segmentation and classification, use the Ellipse tool from the toolbar to draw a circle, like in the screenshot:

![](img/screenshot_annotation1.png?raw=true "Screenshot")

![](img/screenshot_annotation2.png?raw=true "Screenshot")

### Using the Brush tool for refinement

The Brush tool in QuPath can be used to further refine an annotation to, for instance, mask out regions in the core that contain imaging/staining artifacts or tissue that should be excluded.

![](img/screenshot_freehand1.png?raw=true "Screenshot")

To try this, make sure the annotation for the core is selected (is highlighted in yellow); you can select an annotation by `Left double-click` on it in the viewport, or via the `Annotations` tab in the left panel. Then select the Brush tool from the toolbar, and use `Alt+Left click` (on Windows and macOS) or `Alt+Win+Left click` (on Linux) to subtract from the annotation. Using `Left click` will simply add to the annotation instead.

When performing this step, you may want to switch to the Autofluorescence channel (or toggle some of the marker channels) to better see structures in the image.

![](img/screenshot_freehand2.png?raw=true "Screenshot")

![](img/screenshot_freehand3.png?raw=true "Screenshot")

## Cell segmentation

### Using the built-in QuPath cell detection

To segment the cells in the image, switch back to the DAPI channel, and make sure the annotation you created for the core is selected. Go to `Menu->Analyze->Cell detection->Cell detection`, and try first to segment the cells with the default settings from the first screenshot below. Then change the settings (threshold value and cell expansion) to the ones in the second screenshot, before pressing "Run" again to repeat the segmentation.

![](img/screenshot_cell_seg1.png?raw=true "Screenshot")

![](img/screenshot_cell_seg2.png?raw=true "Screenshot")


## Cell classification

### Training an object classifier

According to the signature matrix (see the file `signature_matrix.png` included in the dataset), the Glioma cell type should express mutIDH1, visible in the Opal 650 channel. To create a classifier for this cell type, first go to the `Annotations` tab in the left panel, and remove the existing default classes. Then add two new classes, one called Glioma and the other Not-Glioma, that should indicate if a cell is positive or negative for the cell type.

![](img/screenshot_classes1.png?raw=true "Screenshot")

![](img/screenshot_classes2.png?raw=true "Screenshot")

Next step is to add annotation points for training, on top of segmented cells. First switch to the Opal 650 channel in the viewport, to see where the marker is expressed. Select the Points tool from the toolbar, and press the `Add` button to start adding a few points for cells where the marker is expressed (have higher intensity). Assign these points the class Glioma. Now repeat this step for cells where the marker is not expressed, that should have the Not-Glioma class. To better see the intensities inside the cells, you may want to toggle showing the cell boundaries without the nuclei via `Menu->View->Cell display->Cell boundaries only`.

![](img/screenshot_singleclass1.png?raw=true "Screenshot")

![](img/screenshot_singleclass2.png?raw=true "Screenshot")

The last step is to now train a new classifier. Go to `Menu->Classify->Object classification->Train object classifier`. Follow the screenshots below to select a classifier method and which features and classes that should be used. Also select to use the points you created for the training. After pressing `Live update` and inspecting the result, give the classifier the name Glioma and press `Save`. 

![](img/screenshot_singleclass3.png?raw=true "Screenshot")

![](img/screenshot_singleclass4.png?raw=true "Screenshot")

![](img/screenshot_singleclass5.png?raw=true "Screenshot")

![](img/screenshot_singleclass6.png?raw=true "Screenshot")

### Training multiple object classifiers

After training the Glioma classifier in the previous step, you should now create and train a second classifier for a different cell type. According to the signature matrix, the macrophages are characterized by the expression of IBA1 (Opal 480). We can classify cells expressing IBA1 in a class called TAMM (tumor-associated macrophages and microglia). Make two new classes named TAMM and Not-TAMM, and repeat the steps of creating annotation points for training for each class. Now train a classifier for the new cell type on the Opal 480 (IBA1) feature measurements.

Apply the new classifier on the cells. Note how this will overwrite the previous Glioma/Not-Glioma classification! To combine multiple classifiers in QuPath, go to `Menu->Classify->Object classification->Load object classifier`, then select (using `Ctrl+Left click`) both classifiers in the list and click `Apply classifier sequentially`. Each cell should now be assigned both a Glioma/Not-Glioma class and TAMM/Not-TAMM class. In the `Annotations` tab in the left panel, press the `three dots` next to `Auto set`, choose `Populate from existing objects > All classes (including subclasses)`. You can change the class color by double-clicking on a class.

![](img/screenshot_list_classes.png?raw=true "Screenshot")
![](img/screenshot_multiclass2.png?raw=true "Screenshot")

### Applying a trained classifier on a different image

In the `Project` tab in the left panel, open the second image (the one you did not use for training) for the project. Repeat the steps above for core annotation and cell segmentation, and then try each of the trained classifiers on the cells in this image and compare with the results you got on the first image. If the classifier does not fit the second image, reset the cell classes with `Classify > Object classsification > Reset detection classifications` and create a new classifier following the instructions above (#training-an-object-classifier). Save the classifier using a new name, e.g. Glioma_7_1_E.

## Strategies for creating classifiers
Aim: we now want to build a classifier trained on the cell objects of several cores. With this we aim for a more generalizable classifier.
We will exemplify this for the TAMM/not-TAMM classifier.
* Both images should contain TAMM/not-TAMM point-annotations.

We now have two images with a few point annotations for training a combined TAMM/not-TAMM classifier. For this:  

* go to `Menu->Classify->Object classification->Train object classifier` 
* as before select the features (all intensity measurements on `480`) and classes (TAMM/not-TAMM)
* within the `Train object classifier` GUI press `Load training` and move both images from `Available` to `Selected` using the two arrows, then press `Apply`.
* Save the classifier under the name `TAMM_combined`.

![](img/screenshot_train_loadTraining.png?raw=true "Screenshot")

### Visual inspection of the classifier results
* go to image 5_10_B
* load - one by one - the three classifiers: the classifier you originally created (TAMM_5_10_B), the classifier adjusted to the second image (TAMM_7_1_E) and the combined classifier. How do the three classifiers compare?

Note: This visual inspection is only to get a first impression. Quantitatively, the adjusted classifier could be taken as the ground truth (eventually after manually correcting the classes of single, misclassified cells) for the image and quantitative measures like accuracy, precision, and a confusion matrix can be generated to better compare the combined classifier. Consider also that different experts in most cases will not agree 100% on the "correct" class of a cell and that the ground truth is dependend on the person annotating the data set.

## Exporting results

### As CSV table
Use `Measure->Show detection measurements` to get a list of all cells, their class and other properties. Press `Save` to export this table as a .txt file.
You can also use  `Menu->Measure->Export measurements` to fuse the measurements of several cores into one csv-file.

### As GeoJSON (with cells exported as polygonal regions)

Go to `Menu->Objects->Select->Select detections->Select cells` to first select all the cells in the open image, and then go to `Menu->File->Object data->Export as GeoJSON` and make sure you are using the options in the screenshot below to export only selected objects (i.e., the cells). Repeat this step for each image, and name the exported files 5_10_B.geojson and 7_1_E.geojson (or similar).

![](img/screenshot_export_geojson.png?raw=true "Screenshot")




