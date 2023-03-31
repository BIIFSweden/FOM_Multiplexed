# Pixel Classification

In this exercise we will detect the blood vessels and measure the distance of cells to the blood vessels.
With this we demonstrate the usage of pixel classification in QuPath.

Image: LGG TMA 5_2_Core[1,10,I]_[19581,50232]_component_data.tif

Important channels for this exercise:
| Channel | Stain | Marker |  Main Cell Type |
|--------|:--------:|:--------:|:--------:|
| 1 | DAPI |  Nuclei | all |
| 2 | Opal 480 |    IBA1   | macrophages |
| 8 | Opal 690 |    CD34   | endothelial |

![](img/screenshot_pixelclassification.png?raw=true "Screenshot")


## Cell Detection and Classification
We start as before with drawing an annotation of the tissue, then detect and classify cells.
* start by drawing an annotation for the tissue area.
* select the annotation
* `Analyze->Cell detection->Cell detection`: use a `Threshold : 0.5`and `Cell epansion : 2.5µm`
* open `Classify -> Object classification -> Load object classifier`
* Apply the classifier `TAMM_combined` generated in the [previous exercise](multiplexed_celldetection_cellclassification.md#strategies-for-creating-classifiers)

## Vessel detection
In this example we will create selections for the blood vessels based on a single marker (Opal690/CD34).  
`Classify -> Pixel classification -> Create thresholder` could be used as well and is demonstrated in this [YouTube video](https://www.youtube.com/watch?v=WTAgXpuuqNY&t=1443s).  
Instead here we are using `Classify -> Pixel classification -> Train pixel classifier`. Knowing how to train a classifier is very useful in case of more complex structures that are characterized e.g. by a combination of marker channels or by a defined texture.

### Training a pixel classifier

* in the `Annotations` tab, on the three dots add a class and call it 'Vessel' 
* using the brush tool, draw small areas above pixels belonging to vessels (Opal690 positive).
* right-click on the annotation to set the class to 'Vessel'
* using the brush tool, draw small areas above pixels belonging to background (Opal690 negative).
* right-click on the annotation to set the class to 'Ignore*'

#### Setting up the pixel classifier
A pixel classifier usually calculates the effect of different filters (e.g. edge-enhancing, smoothening) and classifies each pixel according to its intensities in the original and the filtered images. The classifier can also take into account several channels and filtered version of those.
For demonstration purposes we just generate a very simple classifier based on only one channel and one filter.

* open `Classify -> Pixel classification -> Train pixel classifier`
* Follow the first screenshot for the settings: we are using a high resolution version of our image to create the classifier.
* under `Features` press `Edit` to select which channels and features (=filters) to use for the classification. In this simple example it is enough to take only the Opal 690 channel and only one filter: `Gaussian`. Press `OK`
* in the pixel classifier window one can inspect the `Live prediction`, and under `Show classification` also the effect of the gaussian filtering.
* save the classifier under the name 'vessel_pixel_5_10_I'

![](img/screenshot_pixelclassifier.png?raw=true "Screenshot")
![](img/screenshot_features.png?raw=true "Screenshot")


#### Loading the pixel classifier
* make sure to select the annotation of the entire core
* open `Classify -> Pixel classification -> Load pixel classifier`
* Load 'vessel_pixel_5_10_I'
* press `Create objects` choosing `Current selection`
* For the parameters of the `Create objects` command, please follow the screenshot below. We want to create annotations for single vessel objects (= `split objects`) and exclude those particles that might be positive for the CD34-marker, but too small to be considered a vessel (=`Minimum object size`). We also don't want to delete the cell detection that we have created before (= unticked `Delete existing objects`).

![](img/screenshot_createObjects.png?raw=true "Screenshot")

Result:

![](img/screenshot_pixelclassifier_result.png?raw=true "Screenshot")

# Spatial Analysis
We now want to analyse the distance of tumor-associated macrophages (class *TAMM*) to the next blood vessel.


### Adding Spatial Features
* run  `Analyze->Spatial analysis->Distance to annotations 2D`
* `Split multi-part classifications : no`

For each cell we now get the distance in µm to the closest vessel annotation. In the measurement table these will be listed in an additional column.

