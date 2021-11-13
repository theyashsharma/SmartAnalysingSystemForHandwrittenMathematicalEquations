Smart Analyzing System for Handwritten Mathematical Equations Using Convolutional Neural Networks

	This Project is a part of a Hackathon project for Reva hack
	Aim of this project is to digitize the steps of solving a mathematical equation written by freehand on a paper, 
	validating the steps and final answer of the recognized handwritten lines by maintaining the context.

Workflow
	The overall solution can be divided into the following two parts:
	  + Workspace Detection module
	  + Analysis Module

Workspace Detection module is responsible for detecting multiple workspaces in a given sheet of paper using pre-defined markers.

	Step 1: Finding Rectangular Boxes
	  - Rectangles are formed by two horizontal and vertical lines. So the first step is to find all the horizontal and vertical lines ignoring digits, 
	    symbols or anything that is written on the worksheet.
	  - Next, we have to add image “vertical_lines_img” with “horizontal_lines_img” to get the final image.
	  - OpenCV has findContour() function that helps in extracting the contours from the image. 
	    Each individual contour is a Numpy array of (x,y) coordinates of boundary points of the object. 
	    We will use that to find all the objects in the final image (Only objects in the final image are the rectangles).
  
	Step 2: Sorting the contours
	  - Now we have found all the rectangles, its time to sort them top-to-bottom based on their coordinates.
	  - sort_contours function will return contours and bounding boxes(top-left and bottom-right coordinates) sorted in the method we have given. 
	    In this case method is top to bottom.

	Step 3: Selection based on area
	  - There are many rectangles, but we only need the three largest ones. How can we select the three largest rectangles?….
	    One answer is to find the area of the rectangles, then choose the top 3 rectangles which have the maximum area.
	  - These selected rectangles are the work-spaces that are then extracted from the worksheet and sent to the Analysis Module.
  
Analysis module is responsible for detecting and localizing characters in lines in any given single workspace, 
and mathematically analyzing them and then drawing red, green lines depending upon their correctness. 

	Step 1: Line Detection
	    Our line detection module assumes that there is a sufficient gap between lines and there is some intersection between exponential characters and line. 
	    First, the detected work-spaces are converted to binary images then compressed in a single array to take the forward derivative. 
	    Wherever there is a line there will be a change in the derivative.

	Step 2: Character Segmentation and Exponential detection
	    After detecting all the lines we have to send the extracted line images to the text_segment function which will use 
	    openCV’s find contours to segment the characters and sort them using the function sort_contours described above where method is now set to left-to-right.

	Step 3: Optical Character Recognition
	    We can use MNIST dataset for digits (28*28 pixels) and Kaggle’s Handwritten Mathematical symbols dataset for symbols(45*45 pixels) to train the model.

	Step 4: Evaluation and drawing boxes
	    The eval method parses the expression passed to it and runs python expression(code) within the program 
	    Steps involved in evaluation process are,
	    - 1) Solve the given math question and save the answer
	    - 2) Solve each handwritten lines and compare its derived value with the answer stored.
	    - 3) Draw a green bounding box if the line correct and red if the line is wrong.

Example

Each line is corrected separately 
	1. Green Box represents - Line is correct
	2. Red Box represents - Line is incorrect

Target algebraic equation A * X<sup>2</sup> + B * Y
	Where A = 56, B = 7, X = 3, Y = 13

| Line No       | Equation written     | Expected Ans  |   Actual Ans    |   Status       |
| ------------- |:--------------------:| -------------:| --------------: | --------------:|
| 1      | 56 * 3<sup>2</sup> + 7 * 13 | 595 | 595 | Correct |
| 2      | 56 * 7 + 92 | 595 | 484 | Incorrect |
| 3      | 595 + 92 | 595 | 687 | Incorrect |
| 4      | 595 | 595 | 595 | Correct |
