# CarND-Path-Planning-Project

## Code Walkthrough

### Planning Process and Logic

#### 1. Get the car moving inside a lane

For getting the car to move inside the track, there are a series of steps that were done. Code block from line 347 all the way to 446 provide the process of calculating the list of points that define the path.

Lines 351 to 378 pull 2 points from the previous path. This is to make sure a continuity is maintained and the car doesn't experience sudden changes to steering. If the car is starting from stop position, previous points are interpolated by using the car's current steering angle and position.

Lines 385 to 395 add additional 3 points that are spaced 30m apart. This is calculated by using the car's current position. 

Lines 398 to 404 transform the car's rotation to zero to reset the car's axis to where the car is facing so we are always facing front where the car is headed.

Once this set of 5 points are calculated, we generate our path using a spline function. Spline function ensures that the curve passes through all the points and there is a smooth transition between each point.

Using the calculated Spline function, we then calculate a list of points that we need the car to go through.

Before the list of points are calculated, we need to check how many points from the previous path the car has already traversed. We provide the simulator a list of 50 points but the car doesn't necessarily traverses all those points when the code is run (every 0.2s). We take whatever points were remaining from the previous path and add them to our current path. So if 30 points remain from the previous path, then we calculate only the next 20 points and give a list of 50 points again to the simulator.

Lines 411 to 414 add the previous path points, the car couldn't traverse in time, to our current list of points. Lines 425 to 444 calculate the remaining set of points using the Spline function keeping in mind the reference velocity. 'x_add_on' makes sure we are going only to those points we need for the path. 

#### 2. Eliminate the Jerk warnings by slowly starting the car from 0 velocity



#### 3. Take previous path points and combine to current path plan (for next 30m) to create a continuous path
#### 4. Implement Spline to smooth the transition from previous path to current path keeping in mind the previous velocity so there are no jerks
#### 5. Once the car moves without jerk, use Sensor fusion to look for cars in front of you and slow down when approaching a car
#### 6. Write logic to change lanes - plan path so you are first avoiding the car in front of you (even if you crash onto car beside you when changing lane)
#### 7. Using Sensor fusion to check for cars beside you or within a distance from you so you don't crash when changing lanes
#### 8. Allow for "Keep Lane" logic and only change lanes when required