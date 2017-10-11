# Project: Search and Sample Return

[//]: # (Image References)

[image1]: ./output/color_thresh.png
[image2]: ./output/video-frame.png
[image3]: ./output/rover-results.png


### Notebook Analysis
#### Color Threshold Function
Originally the color threshold function only acted as a lower bound, as any color value higher than the threshold would be allowed. After some initial testing I found that this would be sufficient for finding navigable terrain. This is shown in the image below. However, without an upper threshold it was very difficult to find the pixels of the rock without including the surronding ground and the sky. As shown below, adding an upper threshold allowed for the rock to be clearly thresholded from the original image.

![Color Threshold][image1]

#### process_image()
The process_image() function pulls all of the predefined functions into a single step. The two steps are the perspective transform and navigable terrain / rock color thresholding. Next these thresholded pixels are converted to rover-centric coordinates and then into the world coordinates. To convert from rover-centric coordinates to world coordinates the robots current X, Y, and yaw positions are needed. These are supplied by the databucket in the notebook.

After this the locations cooresponding to the navigable terrain and rocks are displayed on the world map. The obstacled terrain is also displayed as all the pixels that aren't the navigable terrain. The original image, warped image, world map, and the color threshold are all displayed after processing the image. This yeilds the results shown in the final.mp4, a frame of which is shown below.



![Process Image][image2]

### Autonomous Navigation and Mapping

#### Perception Step
The perception step is almost exactly like process_image() from the notebook. The main exceptions is that we don't return any images. Instead return add the coordinates of the navigable terrain, obstacled terrain, and the rock locations to the worldmap of the rover. We also add angles and distance to all the navigable terrain pixels to the rover. Other than that, everything is the same.

#### Decision Step
The decision step already contained logic that allowed the Rover to navigate the map quite successfully. However an obvious short coming was the fact that once it stopped it always turned the same direction to find a way out. I added a value in the Rover class in drive_rover.py so that I could retain the last steering value before the Rover switched from forward mode to stop mode. Then I added logic that whatever direction the Rover was heading before switching modes, it would turn in the opposite when leaving. I choose the opposite direction because if there was a Y intersection and the Rover happened to turn right, if it found a dead end you would want it to turn around by turning left. This would increase the chances that once it turned back to intersection it would try the previously unexplored route.


#### Autonomous Results
The Rover is able to find a rock and maintain a fidelity of around 75% once it has mapped atleast 40% of the environment as shown below.

![Rover Results][image3]

An area of improvement would be to log the X, Y locations the Rover has already explored and to add a bias to steering to steer away from locations already visited. Right now the Rover is just acting out behaviours and doesn't use any significant amount of prior knowledge when making decisions.


#### Simulator Settings
The settings I used for the simulator was 1024x768 resolution with good graphics quality. Using these settings I was averaging around 20 FPS of the according to the terminal printout.