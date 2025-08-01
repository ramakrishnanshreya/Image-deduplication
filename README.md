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

# Method 2 
Step-by-Step Explanation
1.	Setup and Initialization:
o	Import Required Libraries: The necessary libraries are imported, including os for file operations, shutil for moving files, and face_recognition for face detection and recognition.
o	Folder Setup: The paths for the source image folder and the folder for storing duplicates are defined. This ensures that the function knows where to look for images and where to place duplicates.
o	Gather Image Files: The function lists all image files in the source folder, filtering for common image formats like PNG, JPG, and JPEG. This provides a clear list of images to process.
2.	Image Processing and Face Detection:
o	Iterate Through Each Image: For each image in the gathered list, the function constructs the full file path and loads the image using the face_recognition library.
o	Face Encoding Extraction: The function attempts to detect faces in the loaded image and extract face encodings, which are numerical representations of facial features. If no face is detected, a message is printed, and the function skips to the next image.
3.	Duplicate Detection:
o	Comparison of Face Encodings: Each image's face encoding is compared against previously stored encodings in a dictionary. The comparison is performed using a distance metric, which quantifies how similar two encodings are.
o	Threshold for Similarity: If the distance between the new encoding and an existing one is below a defined threshold (e.g., 0.6), the images are flagged as duplicates. This step is crucial for identifying visually similar faces that might appear in different images.
4.	Storing Unique Encodings:
o	Track Unique Faces: If the current image is not found to be a duplicate, its encoding is stored in the dictionary for future comparisons. This ensures that all unique faces are recorded for efficient duplicate detection as the function processes more images.
5.	Moving Duplicate Images:
o	Organizing Duplicates: After processing all images, the function collects all identified duplicates and moves them from the source folder to the specified duplicates folder. This helps keep the original folder organized and free from redundancy.
6.	Outputting Results:
o	Final Report: Finally, the function prints a summary indicating how many duplicate images were found and successfully moved to the duplicates folder. This gives the user an overview of the results from the duplicate detection process.

Key Components
•	Face Recognition: The method utilizes face recognition technology to identify duplicates, focusing on facial features that are less affected by changes in lighting or background. This makes it particularly effective for datasets with multiple images of the same individuals.
•	Distance Metric for Similarity: The use of a distance metric to compare face encodings allows for a robust means of determining similarity between images, accommodating variations in pose and expression.
•	Efficient Organization of Results: The method efficiently counts and moves duplicate images, ensuring that the workspace remains organized. The final report provides valuable insights into the dataset's redundancy, making it easier for users to manage their image collections.

# Method 3
Step-by-Step Explanation
1.	Setup and Initialization:
o	Import Required Libraries: The script begins by importing essential libraries, including os for handling file and directory operations, shutil for moving files, and cv2 from OpenCV for image processing tasks. These libraries provide the necessary tools for managing files and performing image analysis.
o	Folder Setup: The user specifies the paths for the source folder containing the images and the folder designated for storing duplicate images. This setup is crucial for organizing the results of the duplicate detection process.
o	Gathering Image Files: The script retrieves a list of all image files in the specified source folder, filtering for common image formats such as PNG, JPG, and JPEG. This allows the method to focus solely on relevant files for processing.
2.	Defining Similarity Through ORB:
o	Feature Detection Initialization: The method employs the ORB (Oriented FAST and Rotated BRIEF) algorithm, which is designed to detect keypoints and extract descriptors from images. Keypoints are distinctive features in an image, while descriptors provide a numerical representation of these features.
o	Comparison of Images: For each image in the source folder, the script compares it with previously identified unique images. This comparison is conducted using the orb_similarity function, which assesses how similar two images are based on their detected features.
3.	Keypoint and Descriptor Extraction:
o	Detecting Keypoints: The ORB algorithm processes each image to identify keypoints and compute their descriptors. These descriptors are unique to each keypoint and serve as a basis for comparing images.
o	Matching Descriptors: The method employs a brute-force matching strategy to find corresponding descriptors between the two images. Using Hamming distance, the algorithm assesses the similarity of the matched descriptors.
4.	Assessing Similarity:
o	Threshold for Similar Matches: The method establishes a threshold for determining whether two images are similar. If the number of good matches (where the distance between descriptors is below the threshold) exceeds a specified count, the images are considered duplicates. This approach helps filter out insignificant matches and focuses on more relevant comparisons.
5.	Collecting Duplicates:
o	Identifying Duplicates: As the method processes each image, it tracks duplicates by adding the paths of images that are found to be similar to an existing unique image. This helps compile a list of duplicate images that will be moved to the designated duplicates folder.
o	Storing Unique Images: Images that are not identified as duplicates are added to a list of unique images. This list serves as a reference for future comparisons with other images in the dataset.
6.	Moving Duplicate Images:
o	Organizing Results: After processing all images, the method moves identified duplicate images from the source folder to the specified duplicates folder. This organization helps maintain a clean workspace and reduces redundancy in the image dataset.
o	Final Reporting: The method concludes by printing a summary report indicating how many duplicate images were found and successfully moved. This provides the user with a clear overview of the duplicate detection results.

Key Components
•	Feature Detection and Matching: The ORB algorithm provides a robust mechanism for detecting and comparing visually similar images based on keypoints. This is particularly useful for identifying duplicates that may differ in terms of lighting, orientation, or minor alterations.
•	Efficient Duplicate Management: By moving duplicate images to a separate folder, the method streamlines image organization, making it easier for users to manage their datasets and avoid clutter.
•	Flexibility and Threshold Adjustment: The ability to adjust parameters such as the similarity threshold and the number of good matches required for duplication allows for customization based on the specific requirements of the user's image collection.

# Method 4
Step-by-Step Explanation
1.	Setup and Initialization:
o	The method begins by importing the necessary libraries, which include:
	os: for handling file and directory operations.
	shutil: for moving files between directories.
	Image from PIL: for opening and manipulating image files.
	imagehash: for generating perceptual hashes of images.
o	Users need to specify the paths for two folders: one containing the images to be checked for duplicates and another where the identified duplicates will be moved. This organization is essential for managing the results of the duplicate detection process.
o	The method collects all image files from the specified source folder, filtering them to include only common image formats like PNG, JPG, and JPEG. This ensures that only relevant image files are processed.
2.	Defining the Duplicate Detection Function:
o	The core function of the method is designed to identify duplicate images based on their perceptual hashes. It takes three parameters:
	image_folder: the folder containing the images to check for duplicates.
	duplicates_folder: the folder where duplicates will be stored.
	hash_size: a parameter that defines the size of the hash, with a default value set to 8. This hash size determines the resolution of the perceptual hash, where a smaller size results in a more compact hash that may sacrifice some accuracy.
3.	Hash Computation:
o	The method initializes an empty dictionary to store computed image hashes along with their corresponding file names, as well as a set to keep track of images identified as duplicates.
o	For each image in the source folder, the method opens the image and computes its perceptual hash. This hash provides a unique representation of the image based on its visual content, allowing for effective detection of duplicates even if the images differ slightly.
4.	Duplicate Detection:
o	After computing the hash for an image, the method checks if that hash already exists in the previously created dictionary.
o	If the hash is found in the dictionary, it indicates that the image is a duplicate, and the method adds the filename of that image to a set of duplicates.
o	If the hash is not found, the method stores the new hash along with its corresponding filename in the dictionary for future comparisons.
5.	Moving Duplicate Images:
o	Once all images have been processed, the method moves the identified duplicate images from the source folder to the specified duplicates folder. This helps maintain a clean workspace by removing duplicates from the original folder.
o	The method concludes by printing a summary report, indicating how many duplicate images were detected and moved to the duplicates folder. This provides users with clear feedback on the results of the operation.

Key Components
•	Perceptual Hashing: The method employs perceptual hashing, which generates a compact representation of an image’s visual characteristics. Unlike traditional hashing methods that are based on pixel values, perceptual hashing produces similar hashes for visually similar images. This feature makes it effective for identifying duplicates even when there are minor differences in the images.
•	Efficient Duplicate Management: This approach efficiently detects and organizes duplicate images, making it easier for users to manage large collections. By using hashes to track images, the method minimizes the need for more complex and time-consuming image comparison techniques, resulting in a faster detection process.
•	Flexibility with Hash Size: The option to customize the hash size allows users to tailor the method based on their specific requirements. A smaller hash size may speed up processing but could decrease accuracy in duplicate detection.
