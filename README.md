# Image-deduplication
This repository provides an end-to-end pipeline for detecting and removing duplicate or near-duplicate images from a dataset. Duplicate images can degrade the performance of computer vision models, waste storage, and introduce bias. This project helps identify such duplicates using efficient similarity techniques.


📁 Use Cases
- Clean image datasets before training ML models
- Remove redundant data from photo libraries
- Preprocess images for computer vision research

# Method 1
This method combines feature-based matching (using SIFT and ORB descriptors) with structural similarity index (SSIM) to detect and move duplicate images into a designated folder, while also counting the number of duplicates for each unique image. 

Here’s a breakdown of the method:
Step-by-Step Explanation
1.	Setup and Initialization:
o	Required libraries (cv2, numpy, os, shutil, defaultdict, ssim) are imported.
o	Folders for images and duplicates are set up, and the image files are gathered from the source folder.
2.	Image Loading and Downsampling:
o	Each image is loaded in grayscale (for simplicity and efficiency) and then downsampled by 50% (adjustable with scale=0.5) to reduce computational load. Downsampling helps speed up the matching process while retaining enough detail for feature extraction.
3.	Feature Extraction Using SIFT and ORB:
o	The SIFT (Scale-Invariant Feature Transform) and ORB (Oriented FAST and Rotated BRIEF) algorithms are used to extract descriptors for each image. These descriptors capture keypoints, which are unique features that remain consistent despite changes in orientation, scale, or lighting.
o	SIFT is used first for each image, storing the descriptors if features are detected. If SIFT features are not sufficient or absent, ORB is used as a fallback.
4.	Duplicate Detection Using Feature Matching:
o	Each image is compared against every other image using SIFT and, if necessary, ORB.
o	SIFT Matching: SIFT descriptors are matched using a brute-force matcher with L2 norm. If the number of good matches exceeds a certain threshold (0.1 * len(descriptors)) based on the number of detected features, the images are considered similar enough to proceed with SSIM verification.
o	ORB Matching: If SIFT doesn’t detect enough good matches, ORB descriptors are matched using the Hamming distance. ORB is typically less computationally intensive than SIFT and performs well with binary descriptors. A similar threshold is applied to check if the images are likely duplicates.
5.	Structural Similarity Index (SSIM) for Final Verification:
o	To reduce false positives from feature-based matching, SSIM is computed for pairs of potentially duplicate images. SSIM measures the perceptual similarity of images, focusing on luminance, contrast, and structural information.
o	If the SSIM score exceeds a threshold (set at 0.7, but adjustable), the images are considered duplicates.
6.	Counting and Moving Duplicates:
o	A duplicate_counts dictionary keeps track of how many times each image appears as a duplicate.
o	The duplicate_images set collects images identified as duplicates.
o	Once duplicate detection is complete, each duplicate image is moved to the duplicates_folder.
o	Finally, the code prints a count of duplicate instances for each unique image and the total number of duplicates moved.
Key Components
•	Feature Extraction and Matching: SIFT and ORB provide a robust mechanism for finding visually similar images based on keypoints, which is helpful for detecting duplicates even if the images are slightly rotated or scaled.
•	SSIM for Verification: SSIM helps confirm duplicate images based on perceptual similarity, reducing false positives that might arise from feature-based matching alone.
•	Efficient Storage and Moving: Duplicates are counted, moved to a separate folder, and a report on the counts of duplicates per image is generated, giving insight into the dataset's redundancy.
