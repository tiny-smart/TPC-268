# 🌳🌻 Plant Taxonomy Meets Plant Counting :🍀🍒<br>A Fine-Grained, Taxonomic Dataset for Counting Hundreds of Plant Species
<p align="center">
    <a href="https://arxiv.org/abs/2603.21229"><img src="https://img.shields.io/badge/arXiv-2603.21229-b31b1b?style=flat-square&logo=arxiv&logoColor=white" alt="arXiv"></a>
    <a href="https://huggingface.co/datasets/jinyu-xu/TPC-268"><img src="https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Dataset-FFD21E?style=flat-square" alt="Hugging Face"></a>
    <a href="https://tiny-smart.github.io/tpc268-project-page/"><img src="https://img.shields.io/badge/%F0%9F%8C%90%20Project%20Page-TPC--268-blue?style=flat-square" alt="Project Page"></a>
    <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/"><img src="https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey?style=flat-square&logo=creativecommons&logoColor=white" alt="License"></a>
</p>

**[CVPR 2026 Oral]** Official repository for the paper **"Plant Taxonomy Meets Plant Counting: A Fine-Grained, Taxonomic Dataset for Counting Hundreds of Plant Species"**.

**Authors:** Jinyu Xu, Tianqi Hu, Xiaonan Hu, Letian Zhou, Songliang Cao, Meng Zhang, Hao Lu*

*TPC-268 is a large-scale dataset for class-agnostic counting (CAC) that explicitly integrates plant taxonomy. It is designed to tackle the unique challenges of visual counting in the natural world, featuring highly diverse and nonrigid plant morphologies.*

## Dataset Overview
* **Images:** 10,000 images spanning extreme observation scales, ranging from canopy-level remote sensing to tissue-level microscopy.
* **Annotations:** 678,050 instance-level points and 30,000 exemplar bounding boxes.
* **Taxonomy:** A complete 7-level Linnaean hierarchy encompassing 2 Kingdoms (Plantae and Fungi), 2 Phyla, 4 Classes, 35 Orders, 83 Families, 192 Genera, and 242 Species.
* **Categories:** 268 fine-grained "Species-Organization" targets, providing precise semantics for counting (e.g., flower, fruit, leaf, stem, stoma, resin).

<p align="center">
  <img src="./assets/overview.jpg" width="100%" alt="Sample images from the TPC-268 dataset">
  <br>
  Sample images from the TPC-268 dataset.
</p>

## Dataset Download

You can download TPC-268 from either of the following links: [Hugging Face](https://huggingface.co/datasets/jinyu-xu/TPC-268), [Google Drive](https://drive.google.com/file/d/1MNS03y5e_skC_CPm0Qz2BwLWxTTZeFSf/view?usp=sharing). Extract the downloaded file into a `TPC-268/` directory. The images are strictly organized by their taxonomy and organization:
`TPC-268/[Genus_Species]/[Organization]/[Genus_Species]_[Organization]_[index].jpg`

```text
TPC-268/
├── Abelmoschus_esculentus/
│   └─ fruit/
│      ├─ Abelmoschus_esculentus_fruit_1.jpg
│      └─ ...
├── Zea_mays/
│   └─ fruit/
│      ├─ Zea_mays_fruit_1.jpg
│      └─ ...
└── ...
```

## Annotations and Splits

Core data files are located in the `annotations/` and `splits/` directories:

* `annotations/tpc268_annotations.json`: Instance-level point annotations and 4-point coordinates for exemplars.
* `annotations/tpc268_taxonomy_ids.json`: Mapping of hierarchical taxonomic levels (Kingdom to Species) to unique numerical IDs.
* `annotations/tpc268_taxonomy_vectors.json`: 7-dimensional taxonomic feature vectors for each plant species.
* `splits/tpc268_[train|val|test].txt`: Lists of relative image paths used for data loading.
* `splits/tpc268_split.json`: Lists of specific species-organization categories partitioned into the train, val, and test sets.

## Scripts

The `tools/` directory provides essential utilities:

**`tpc268_dataset.py`**: A standardized PyTorch `Dataset` class to load images and annotations.
```python
from tools.tpc268_dataset import TPC268Dataset
dataset = TPC268Dataset(data_dir='TPC-268/', split_txt='splits/tpc268_train.txt', anno_json='annotations/tpc268_annotations.json')
```
**`tpc268_visualize_dataset.py`**: Script to overlay annotations on images.
```bash
python tools/tpc268_visualize_dataset.py --img_path TPC-268/Zea_mays/fruit/Zea_mays_fruit_1.jpg --anno_json annotations/tpc268_annotations.json
```
**`TPC268_Annotator.html`**: An HTML tool for browsing and editing annotations directly in the browser.
**`tpc268_generate_benchmark_split.py`**: Script to generate the dataset partitioning lists.

## Benchmark Results

The following table reports the 3-shot counting performance on TPC-268, best performance is highlighted in bold. For other experimental results, please refer to the main paper.

| Method      | Backbone | Val MAE   | Val RMSE  | Val $R^2$ | Test MAE  | Test RMSE | Test $R^2$ |
| :---------- | :------- | :-------- | :-------- | :-------- | :-------- | :-------- | :--------- |
| FamNet      | R50      | 28.87     | 52.51     | 0.58      | 30.43     | 65.62     | 0.62       |
| BMNet+      | R50      | 29.33     | 77.78     | 0.47      | 27.78     | 57.25     | 0.74       |
| C-DETR      | R50      | 22.66     | 77.51     | 0.75      | 22.68     | 57.97     | 0.74       |
| SPDCNet     | R18      | 25.66     | 72.49     | 0.52      | 23.70     | 47.53     | 0.64       |
| CountTR     | Hybrid   | 20.21     | 55.82     | 0.73      | 25.19     | 49.94     | 0.62       |
| SAFECount   | R18      | 22.57     | 63.65     | 0.64      | 25.70     | 52.30     | 0.58       |
| LOCA        | R50      | 17.26     | 53.19     | 0.75      | **17.51** | **38.37** | **0.78**   |
| DAVE        | R50      | 16.47     | 52.87     | 0.76      | 17.61     | 40.06     | 0.75       |
| CACVIT      | ViT-B    | 16.63     | **42.49** | 0.82      | 22.04     | 41.79     | 0.73       |
| CountGD     | Swin-B   | 18.32     | 54.55     | 0.74      | 19.52     | 50.51     | 0.61       |
| TasselNetV4 | ViT-B    | **13.20** | 43.93     | **0.83**  | 22.95     | 51.36     | 0.60       |

## Citation

If you find TPC-268 useful for your research, please cite:
```bibtex
@inproceedings{xu2026plant,
  title={Plant Taxonomy Meets Plant Counting: A Fine-Grained, Taxonomic Dataset for Counting Hundreds of Plant Species},
  author={Xu, Jinyu and Hu, Tianqi and Hu, Xiaonan and Zhou, Letian and Cao, Songliang and Zhang, Meng and Lu, Hao},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
  year={2026}
}
```

## License

The TPC-268 dataset is released exclusively for academic research purposes. It is licensed under the [Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0)](LICENSE). Any commercial use, reproduction, or distribution of this dataset without explicit prior consent is strictly prohibited. 
