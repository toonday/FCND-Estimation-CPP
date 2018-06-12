## Project: Building an Estimator

---

# Required Steps for a Passing Submission:
1. Determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data.
2. Implement all of the elements of the prediction step for the estimator.
3. Implement the magnetometer update.
4. Implement the GPS update.
5. Write it up.
6. Congratulations!  You're Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1807/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data.

I copied the data into google sheets, then used google sheet formulas to get the mean and standard deviation from the data.
The formulas are pretty standard.
mean = sum(data) / count(data)
std_dev = sqrt(sum((data(i) - mean)^2)/count(data))
std_dev-GPS-X = 0.71279695
std_dev-IMU-AX = 0.5101711345

### Explain your Code

#### 1. Implement a better rate gyro attitude integration scheme in the UpdateFromIMU() function.
I implemented the UpdateFromIMU function in lines 96 - 105 of the QuadEstimatorEKF.cpp file.
I used the gyro readings and the current estimates of the vehicle's roll, pitch and yaw angles to predict the new estimates for the vehicle's roll, pitch and yaw angles.
I converted the roll, pitch and yaw angles into a quaternion before performing the integration with the gyro readings over a small time of dtIMU.


#### 2. Implement all of the elements of the prediction step for the estimator.
I implemented all the elements to enable the predict step for the EKF estimator.
This included:
- predicting the new state of the vehicle after a time step of dt by updating the position using the current velocity and updating the velocity using the global acceleration (which was gotten by translating the specific accleration values read from the accelerometer readings into world/global space). [lines 168 - 175 of QuadEstimatorEKF.cpp file]
- calculating the derivative of rotation matrix RbgPrime. [lines 203 - 217 of the QuadEstimatorEKF.cpp file]
- the RbgPrime value was then used (along with the accelerometer readings) to calculate the state transition function gPrime. [lines 264 - 270 of the QuadEstimatorEKF.cpp file]
- Finally, gPrime was used to update the covariance matrix ekfCov. [line 272 of the QuadEstimatorEKF.cpp file]


#### 3. Implement the magnetometer update.
I used the magnetometer readings to update the estimator's state in lines 324 - 332 of the QuadEstimatorEKF.cpp file.
To do this, I first needed to calculate the error/difference between the measured/observed yaw angle (gotten from the magnetometer readings) and the predicted yaw angle.
I then used the error to normalize the predicted value to ensure that the error is short angle and not the longer angular value.


#### 4. Implement the GPS update.
I used the GPS readings to update the estimator's state in lines 298 - 305 of the QuadEstimatorEKF.cpp file.
This was quite straight forward.
I needed to pass the current state and the derivative of the measurement model matrix hPrime (which was almost an identity matrix, oh well ...) to the update function which performs the update step of the EKF estimator.


# Others
Finally, I copied over my controller from the previous project and had to de-tune some values of the controller to enable the drone to fly using a reference path.
However, the drone looked like it was flying drunk (although I passed the simulation test).
This last 2 projects actually showed me the huge amount of parameter tuning that goes into developing intelligent vehicle systems. It would be really helpful if this parameters could be auto-tuned by some smart algorithm(s), etc.