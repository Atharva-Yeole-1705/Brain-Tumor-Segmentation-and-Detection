# 🧠 Brain Tumor Segmentation and Detection

**A classical image-processing system for segmenting candidate tumor regions in brain MRI scans.**

## Overview

We developed a brain MRI segmentation pipeline using **Python, OpenCV, NumPy, and Matplotlib**. The system processes MRI images through grayscale conversion, noise reduction, intensity thresholding, connected-component labeling, and geometric filtering to generate a mask of selected candidate regions.

The generated mask can be compared with the reference mask provided with each MRI image. The pipeline also calculates segmentation metrics and visualizes intermediate processing stages, making it easier to understand how the final result is produced.

> **Scope:** This is an academic image-processing project, not a clinically validated diagnostic system. Its outputs are not intended to replace professional medical assessment.

## ✨ Key Features

- Converts MRI images to grayscale for intensity-based processing.
- Reduces isolated noise using a **3 × 3 median filter**.
- Applies constant thresholding to create a binary foreground image.
- Labels connected foreground regions using OpenCV's connected-component analysis.
- Extracts component **area, bounding box, and density**.
- Selects candidate regions using configurable area and density criteria.
- Compares predicted masks with the supplied reference masks using overlap-based metrics.
- Visualizes the MRI input, intermediate outputs, selected candidates, and reference mask.

## ⚙️ Image-Processing Pipeline

```text
MRI Input
   ↓
Grayscale Conversion
   ↓
3 × 3 Median Filtering
   ↓
Constant Thresholding
   ↓
Connected Component Labeling
   ↓
Area, Bounding Box & Density Calculation
   ↓
Candidate Region Selection
   ↓
Predicted Mask vs. Reference Mask
   ↓
Evaluation and Visualization
```

### 1. Grayscale Conversion

The input image is converted to a single intensity channel using the weighted grayscale formula:


Gray = 0.298R + 0.587G + 0.114B

For images that are already grayscale, a color-to-grayscale conversion is not necessary.

### 2. Median Filtering

A **3 × 3 median filter** reduces isolated noise while helping preserve important edges. For each pixel, the filter replaces its value with the median of the values in its local 3 × 3 neighborhood.

### 3. Constant Thresholding

A fixed intensity threshold converts the filtered grayscale image into a binary image. Pixels above the threshold are treated as foreground, while the remaining pixels are treated as background.

- **Threshold used in the project presentation:** `90`
- The threshold was selected empirically through trial and error.

The value in the notebook should match the value used to generate the reported outputs.

### 4. Connected Component Labeling

We use OpenCV's `connectedComponentsWithStats()` to identify separate connected regions of foreground pixels. Each component receives a label, and OpenCV calculates useful statistics for it, including its area, bounding box, and centroid. Components can be displayed in different colors to make them easier to inspect.

### 5. Region Metrics

For every connected component, the pipeline calculates:

- **Area:** Number of foreground pixels in the component.
- **Bounding box:** The enclosing rectangle represented by `(x, y, w, h)`.
- **Bounding-box area:** `w × h`.
- **Density:**

Density = Component Area/ Bounding-box Area

Density indicates how much of a component's bounding rectangle is occupied by foreground pixels.

### 6. Candidate Region Selection

The current candidate-selection criteria retain components satisfying both conditions:

```text
Area > 1000 AND Density > 0.4
```

These geometric rules help remove small or sparse regions. They identify **candidate regions**, not definitive tumor diagnoses; other bright structures can also satisfy the criteria.

## 📊 Evaluation

We compare the predicted mask with the reference output mask supplied with the MRI image. The evaluation includes overlap-based segmentation metrics to measure how closely the selected region matches the reference.

### Dice Score

The Dice Score measures the overlap between the predicted mask and the reference mask:

Dice = {2|P \cap G|}{|P| + |G|}

Where:

- `P` = predicted foreground/tumor mask.
- `G` = reference mask.
- `|P ∩ G|` = number of pixels classified as foreground in both masks.
- `|P|` and `|G|` = foreground pixel counts in the predicted and reference masks.

The Dice Score ranges from **0 to 1**. A score of `1` indicates perfect overlap; `0` indicates no overlap when the reference contains foreground pixels.

**Project criterion:** `Dice Score ≥ 0.8` is treated as an acceptable result for this project. This is a project-specific evaluation threshold, not a universal clinical standard.

Other useful metrics include **Intersection over Union (IoU), precision, recall, and pixel accuracy**. Pixel accuracy is best interpreted alongside overlap metrics because background pixels can greatly outnumber tumor pixels.

### Reported Performance

The project presentation reports **79% correct detection results across 100 MRI input samples**, compared with their supplied output masks. This figure should be interpreted according to the detection criterion implemented in the project; it is not a Dice Score unless calculated explicitly as one.

## 🚀 Getting Started

### Option A — Google Colab

1. Open the Jupyter notebook (`.ipynb`) in Google Colab.
2. Upload the MRI images and reference masks to the directory expected by the notebook. The current workflow uses `/content`.
3. Confirm that every image has a correctly named corresponding mask.
4. Run the notebook cells in order.
5. Review the intermediate images, selected candidate regions, predicted masks, and evaluation metrics.

### Option B — Run Locally

Install the main Python dependencies:

```bash
pip install opencv-python numpy matplotlib
```

Open the notebook in Jupyter Notebook or VS Code, update the dataset directory if necessary, and run the cells in order.

## 🧰 Tech Stack

- **Python** — pipeline implementation
- **OpenCV** — image processing, median filtering, thresholding, connected-component labeling
- **NumPy** — image-array operations and metric calculations
- **Matplotlib** — visualization of MRI images, components, and masks
- **Jupyter Notebook / Google Colab** — interactive execution and result inspection

## 🖼️ Outputs

The pipeline provides visual outputs for the main processing stages:

1. MRI Input
2. Gray Scale Image
3. After Median Filtering
4. After Constant Thresholding
5. After Connected Components Labeling
6. Selected Candidates
7. Given output mask

These outputs show how the input image changes at each stage and how the selected candidate regions compare with the supplied reference mask.

## ⚠️ Limitations

- A fixed intensity threshold may fail when tumor and normal-tissue intensities overlap.
- Tumors vary in size, shape, contrast, and internal appearance.
- Bright non-tumor structures may be selected as false positives.
- Tumor regions may be missed if they do not satisfy the chosen area or density thresholds.
- Results depend on image quality, dataset characteristics, reference-mask quality, and parameter selection.
- The approach relies on hand-designed rules rather than features learned from labeled training data.

## 🔮 Future Improvements

- Tune threshold, area, and density parameters systematically using a validation set.
- Incorporate adaptive thresholding, watershed, or contour-based techniques where appropriate.
- Report per-image Dice and IoU results, alongside precision, recall, and pixel accuracy.
- Compare the classical pipeline with a supervised segmentation architecture such as **U-Net**, using suitable training and validation splits.
- Evaluate the approach on held-out scans to assess how well it generalizes to images not used during parameter selection.

## 📌 Summary

We implemented a complete classical image-processing workflow for brain MRI candidate-region segmentation:

**Preprocess → Threshold → Label Components → Measure Regions → Select Candidates → Compare Masks**

The project demonstrates how standard image-processing operations can be combined into a reproducible segmentation pipeline, with visual outputs and quantitative evaluation against reference masks. It also provides a clear baseline for future improvements in medical-image segmentation.

---

*For academic use only. This project is not intended for diagnosis, treatment planning, or other clinical decision-making.*
