# Trajectory Constraints

In the [previous article](trajectory-generation.md), you might have noticed that no custom constraints were added when generating the trajectories. Custom constraints allow users to impose more restrictions on the velocity and acceleration at points along the trajectory based on location and curvature.

For example, a custom constraint can keep the velocity of the trajectory under a certain threshold in a certain region or slow down the robot near turns for stability purposes.

## Provided Constraints

FTCLib includes a set of predefined constraints that users can utilize when generating trajectories. The list of FTCLib-provided constraints is as follows:

* `CentripetalAccelerationConstraint`: Limits the centripetal acceleration of the robot as it traverses along the trajectory. This can help slow down the robot around tight turns.
* `DifferentialDriveKinematicsConstraint`: Limits the velocity of the robot around turns such that no wheel of a differential-drive robot goes over a specified maximum velocity.
* `DifferentialDriveVoltageConstraint`: Limits the acceleration of a differential drive robot such that no commanded voltage goes over a specified maximum.
* `MecanumDriveKinematicsConstraint`: Limits the velocity of the robot around turns such that no wheel of a [mecanum](../../features/drivebases.md#mecanum)-drive robot goes over a specified maximum velocity.
* `SwerveDriveKinematicsConstraint`: Limits the velocity of the robot around turns such that no wheel of a swerve-drive robot goes over a specified maximum velocity.

### Note

The `DifferentialDriveVoltageConstraint` only ensures that theoretical voltage commands do not go over the specified maximum using a [feedforward model](../../features/controllers.md#feedforward-control). If the robot were to deviate from the reference while tracking, the commanded voltage may be higher than the specified maximum.

## Creating a Custom Constraint

Users can create their own constraint by implementing the `TrajectoryConstraint` [interface](https://github.com/FTCLib/FTCLib/blob/v2.1.1/core/src/main/java/com/arcrobotics/ftclib/trajectory/constraint/TrajectoryConstraint.java).

```java
@Override
public double getMaxVelocityMetersPerSecond(
  Pose2d poseMeters,
  double curvatureRadPerMeter,
  double velocityMetersPerSecond) {
  // code here
}

@Override
public MinMax getMinMaxAccelerationMetersPerSecondSq(
  Pose2d poseMeters,
  double curvatureRadPerMeter,
  double velocityMetersPerSecond) {
  // code here
}
```

The `MaxVelocity` method should return the maximum allowed velocity for the given pose, curvature, and original velocity of the trajectory without any constraints. The `MinMaxAcceleration` method should return the minimum and maximum allowed acceleration for the given pose, curvature, and constrained velocity.

