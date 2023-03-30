# Pixel Classification

In this exercise we will detect the blood vessels and measure the distance of cells to the blood vessels.
With this we demonstrate the usage of pixel classification in QuPath.

Image: LGG TMA 5_2_Core[1,10,I]_[19581,50232]_component_data.tif

Important channels for this exercise
| Channel | Stain | Marker |  Main Cell Type |
|--------|:--------:|:--------:|:--------:|
| 1 | DAPI |  Nuclei | all |
| 2 | Opal 480 |    IBA1   | macrophages |
| 8 | Opal 690 |    CD34   | endothelial |

## Vessel detection

Start by drawing an annotation for the tissue area.

