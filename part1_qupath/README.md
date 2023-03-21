# QuPath instructions for workshop

By Fredrik Nysjö

## Data

- Folder: `dataset_1_tma_cores/`
- Images: `LGG TMA 5_2_Core[1,10,B]...data.tif` and `LGG TMA 7_2_Core[1,1,E]...data.tif`
- Signature matrix: `signature_matrix.png`

## Creating a new QuPath project

Go to `Menu->File->Project->Create project`. This will ask you to create a new folder for the project, where any non-image data (annotations, trained classifiers, detections, etc.) will be stored.

For the new project, in the `Project` tab in the left panel, click on `Add images` and select the two .tif images from Dataset 1. You can also just drag-and-drop the images onto the QuPath window. Afterward, the project with the two images should look like this:

![](images/screenshot_project.png?raw=true "Screenshot")

## Core annotation

### Displaying individual image channels

Each image in the dataset contains image data for a single tissue-micro array (TMA) core with 10 channels (8 Opal marker channels + DAPI + an autofluorescent channel). Open the image for the first core (5_10_B), and use the Brightness & contrast tool from the toolbar (looks like a half-moon) to make only the DAPI channel visible in the viewport:

![](images/screenshot_brightness_contrast1.png?raw=true "Screenshot")

![](images/screenshot_brightness_contrast2.png?raw=true "Screenshot")

### Basic annotation drawing

To annotate which region in the core image that should be used for the cell segmentation and classification, use the Ellipse tool from the toolbar to draw a circle, like in the screenshot:

![](images/screenshot_annotation1.png?raw=true "Screenshot")

![](images/screenshot_annotation2.png?raw=true "Screenshot")

### Using the Brush tool for refinement

The Brush tool in QuPath can be used to further refine an annotation to, for instance, mask out regions in the core that contain imaging/staining artifacts or tissue that should be excluded.

![](images/screenshot_freehand1.png?raw=true "Screenshot")

To try this, make sure the annotation for the core is selected (is highlighted in yellow); you can select an annotation by `Left double-click` on it in the viewport, or via the `Annotations` tab in the left panel. Then select the Brush tool from the toolbar, and use `Alt+Left click` (on Windows and macOS) or `Alt+Win+Left click` (on Linux) to subtract from the annotation. Using `Left click` will simply add to the annotation instead.

When performing this step, you may want to switch to the Autofluorescence channel (or toggle some of the marker channels) to better see structures in the image.

![](images/screenshot_freehand2.png?raw=true "Screenshot")

![](images/screenshot_freehand3.png?raw=true "Screenshot")

## Cell segmentation

### Using the built-in QuPath cell detection

To segment the cells in the image, switch back to the DAPI channel, and make sure the annotation you created for the core is selected. Go to `Menu->Analyze->Cell detection->Cell detection`, and try first to segment the cells with the default settings from the first screenshot below. Then change the settings (threshold value and cell expansion) to the ones in the second screenshot, before pressing "Run" again to repeat the segmentation.

![](images/screenshot_cell_seg1.png?raw=true "Screenshot")

![](images/screenshot_cell_seg2.png?raw=true "Screenshot")

### Using the StarDist QuPath extension (optional)

TODO

Note: if you did not install the StarDist extension prior to creating your project, you need to save the project (`Menu->File->Save`), install the extension (by downloading it from [here](https://github.com/qupath/qupath-extension-stardist/releases) and then dragging-and-dropping the .jar file over the QuPath window), and then restart QuPath.

## Cell classification

### Training an object classifier

According to the signature matrix (see the file `signature_matrix.png` included in the dataset), the Glioma cell type should be expressed in the Opal 650 (mutIDH1) channel. To create a classifier for this cell type, first go to the `Annotations` tab in the left panel, and remove the existing default classes. Then add two new classes, one called Glioma and the other Not-Glioma, that should indicate if a cell is positive or negative for the cell type.

![](images/screenshot_classes1.png?raw=true "Screenshot")

![](images/screenshot_classes2.png?raw=true "Screenshot")

Next step is to add annotation points for training, on top of segmented cells. First switch to the Opal 650 channel in the viewport, to see where the marker is expressed. Select the Points tool from the toolbar, and press the `Add` button to start adding a few points for cells where the marker is expressed (have higher intensity). Assign these points the class Glioma. Now repeat this step for cells where the marker is not expressed, that should have the Not-Glioma class. To better see the intensities inside the cells, you may want to toggle showing the cell boundaries without the nuclei via `Menu->View->Cell display->Cell boundaries only`.

![](images/screenshot_singleclass1.png?raw=true "Screenshot")

![](images/screenshot_singleclass2.png?raw=true "Screenshot")

The last step is to now train a new classifier. Go to `Menu->Classify->Object classification->Train object classifier`. Follow the screenshots below to select a classifier method and which features and classes that should be used. Also select to use the points you created for the training. After pressing `Live update` and inspecting the result, give the classifier the name Glioma and press `Save`. 

![](images/screenshot_singleclass3.png?raw=true "Screenshot")

![](images/screenshot_singleclass4.png?raw=true "Screenshot")

![](images/screenshot_singleclass5.png?raw=true "Screenshot")

![](images/screenshot_singleclass6.png?raw=true "Screenshot")

### Training multiple object classifiers

After training the Glioma classifier in the previous step, you should now create and train a second classifier for a different cell type. According to the signature matrix, the Oligodendrocyte cell type should be expressed in the Opal 620 (MBP) channel. Make two new classes named Oligo and Not-Oligo, and repeat the steps of creating annotation points for training for each class. Now train a classifier for the new cell type on the Opal 620 feature measurements.

Apply the new classifier on the cells. Note how this will overwrite the previous Glioma/Not-Glioma classification! To combine multiple classifiers in QuPath, go to `Menu->Classify->Object classification->Load object classifier`, then select (using `Ctrl+Left click`) both classifiers in the list and click `Apply classifier sequentially`. Each cell should now be assigned both a Glioma/Not-Glioma class and an Oligo/Not-Oligo class.

![](images/screenshot_multiclass1.png?raw=true "Screenshot")

![](images/screenshot_multiclass2.png?raw=true "Screenshot")

### Applying a trained classifier on a different image

In the `Project` tab in the left panel, open the second image (the one you did not use for training) for the project. Repeat the steps above for core annotation and cell segmentation, and then try each of the trained classifiers on the cells in this image and compare with the results you got on the first image.

### Feature normalization (optional)

TODO

## Exporting results

### As GeoJSON (with cells exported as polygonal regions)

Go to `Menu->Objects->Select->Select detections->Select cells` to first select all the cells in the open image, and then go to `Menu->File->Object data->Export as GeoJSON` and make sure you are using the options in the screenshot below to export only selected objects (i.e., the cells). Repeat this step for each image, and name the exported files 5_10_B.geojson and 7_1_E.geojson (or similar).

![](images/screenshot_export_geojson.png?raw=true "Screenshot")

### As CSV table (with cells exported as points)

Go to `Menu->Measure->Export measurements` and select which image you want to export the cells from. Make sure you are using the options in the screenshot below to export in a CSV format that for example TissUUmaps can read. Repeat this step for each image, and name the exported files 5_10_B.csv and 7_1_E.csv (or similar).

Note: if you see the warning "A selected image is open in the viewer..." displayed like in the screenshot, you can just save the project before opening the "Export measurements" dialog again.

![](images/screenshot_export_csv.png?raw=true "Screenshot")
