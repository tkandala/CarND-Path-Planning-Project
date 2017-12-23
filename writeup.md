# CarND-Path-Planning-Project

## Code Walkthrough

### Planning Process and Logic

#### -> Get the car moving inside a lane. Eliminate the Jerk warnings by slowly starting the car from 0 velocity. Take previous path points and combine to current path plan (for next 30m) to create a continuous path. Implement Spline to smooth the transition from previous path to current path keeping in mind the previous velocity so there are no jerks

For getting the car to move inside the track, there are a series of steps that were done. Code block from line 347 all the way to 446 provide the process of calculating the list of points that define the path.

Lines 351 to 378 pull 2 points from the previous path. This is to make sure a continuity is maintained and the car doesn't experience sudden changes to steering. If the car is starting from stop position, previous points are interpolated by using the car's current steering angle and position.

Lines 385 to 395 add additional 3 points that are spaced 30m apart. This is calculated by using the car's current position. 

Lines 398 to 404 transform the car's rotation to zero to reset the car's axis to where the car is facing so we are always facing front where the car is headed.

Once this set of 5 points are calculated, we generate our path using a spline function. Spline function ensures that the curve passes through all the points and there is a smooth transition between each point.

Using the calculated Spline function, we then calculate a list of points that we need the car to go through.

Before the list of points are calculated, we need to check how many points from the previous path the car has already traversed. We provide the simulator a list of 50 points but the car doesn't necessarily traverses all those points when the code is run (every 0.2s). We take whatever points were remaining from the previous path and add them to our current path. So if 30 points remain from the previous path, then we calculate only the next 20 points and give a list of 50 points again to the simulator.

Lines 411 to 414 add the previous path points, the car couldn't traverse in time, to our current list of points. Lines 425 to 444 calculate the remaining set of points using the Spline function keeping in mind the reference velocity. `x_add_on` makes sure we are going only to those points we need for the path. 

Lines 351 to 414 make sure that the car takes into consideration the previous path's points so we maintain a smooth transition to the next calculated path. Also, Spline function makes sure we are following a smooth curve.

#### -> Once the car moves without jerk, use Sensor fusion to look for cars in front of you and slow down when approaching a car. Also use Sensor fusion to check for cars beside you or within a distance from you so you don't crash when changing lanes

Lines 281 to 315 use the sensor fusion data to get the list of cars and their position, velocity, etc to enable us to determine a list of cars around us. 

Lines 288 to 302 check for cars in the current lane we are travelling in. If we have a car is in our lane, we check to see if it is in front of us in the next 0.02s using it's current position and it's speed. Usinf a `ref_dist` of 30m, we check if the car is close to us. If so, the boolean variable `too_close` is set to true.

Lines 304 to 313 check for cars in the lanes beside us. Using similar logic as the car in the same lane, the code snippet calculates cars which are 30m ahead or behind us. Boolean variables `too_close_left` and `too_close_right` are set to true if any cars satisfy the above conditions. 

#### -> Write logic to change lanes - plan path so you are first avoiding the car in front of you and at the same time avoid crashing into other cars in the lanes beside which you are planning to move into.

Lines 317 to 333 provide the logic to basically keep the car in it's lane. If a car is moving too slowly in front of us, our car decides to change lanes based on the boolean variables we calculated above for cars in the other lanes. The car doesn't prefer a lane that it wants to travel but rather maintains the lane it is in, if possible, if not changes lanes.

Lines 335 to 344 provides the logic to control the speed of the car. The velocity is gradually decreased when approaching a slower vehicle. When no cars are in front, the car accelerates to it's reference velocity which in our case is 49.5MPH.

## Project Rubrics

### The car is able to drive at least 4.32 miles without incident..

The YouTube video below shows the car driving around the track without any incident.

[![Video of Car inside simulator successfully going through the highway](http://img.youtube.com/vi/hRj7UyIEueA/0.jpg)](http://www.youtube.com/watch?v=hRj7UyIEueA "SDCND Path Planning video")

### The car drives according to the speed limit.

The code block (lines 335 to 344) adds logic to control the speed of the car and maintain it just under 50MPH to around 49.5MPH when there is no car in front of it. In case of slowdown, the car speed goes down until there is enough space for the car to speed up.

### Max Acceleration and Jerk are not Exceeded.

The spline functionality mentioned above along with previous path points and a reference velocity of 49.5MPH, the car's acceleration is maintained. The video also shows no warnings of Jerk throughout.

### Car does not have collisions.

The car's path planning and lane change logic ensures that no collisions occur. Also, speed is reduced when approaching a car and when no lane changes are permissible.

### The car stays in its lane, except for the time between changing lanes.

As mentioned earlier, the speed of the car is reduced when no lane changes are permissible and there is a car in front of it going slower.

### The car is able to change lanes

The attached video shows the car successfully managing lanes changes when required.

## Reflection

The Path Planning algorithm presented here does a pretty good job of planning a path that is Jerk free, collision free and drives safely for the conditions present.

What the algorithm doesn't implement is the Machine learning piece, which basically uses Naive Bayes to estimate position of cars based on their speeds, position in the future and make decisions based on what is going to happen in the future. As we can see in the video, at one point, the car gets stuck in the rightmost lane and it is not able to get out of that lane because of another car in the left lane. If planned properly, the car could slow down enough to allow the car in the left lane to pass and slowly move to the leftmost lane and overtake the cars.

Another aspect we can notice here is that our car whne approaching a slower moving vehicle, it slows down significantly (when it is not able to switch lane), slower than the car in front of it. It then accelerates again to realize that it has to reduce again. This keeps repeating until a lane change is safe to be executed. In situations like these, we can implement a follow car feature where our car just blindly follows the car in the same speed. And when it is safe to change lanes, make the lane change.



