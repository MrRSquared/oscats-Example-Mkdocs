# Programming an FRC Robot

There are many resources you need for programming a robot.
Most of the setup is covered by WPILib.

One of the best tutorials is the [0-autonomous](https://www.youtube.com/channel/UCmJAoN-yI6AJDv7JJ3372yg) tutorial that Team 6814 created.

In order to use any third-party libraries, you need to set them up in VSCode.

When following the tutorial, there will be some differences. We use Rev hardware instead of CTRE. We are also using the internal encoders on the Neos rather than external.

To figure out how to use the encoders, you can use the [Rev API](https://codedocs.revrobotics.com/java/com/revrobotics/package-summary.html).

Rev uses the `getRelativeEncoder()` method from the motor to identify the encoder.
Here is a sample...
```
/**
This sample code is taken from Rev's Spark Max Examples located at...
https://github.com/REVrobotics/SPARK-MAX-Examples/blob/master/Java/Encoder%20Feedback%20Device/src/main/java/frc/robot/Robot.java
*/
@Override
public void robotInit() {
...
// initialize SPARK MAX with CAN ID
    m_motor = new CANSparkMax(deviceID, MotorType.kBrushed);
    m_encoder = m_motor.getEncoder(SparkMaxRelativeEncoder.Type.kQuadrature, 4096); //This sets the internal motor up with 4096 ppr
...
}
...
@Override
  public void teleopPeriodic() {
  ...
  /**
     * Encoder position is read from a RelativeEncoder object by calling the
     * GetPosition() method.
     * 
     * GetPosition() returns the position of the encoder in units of revolutions
     */
    SmartDashboard.putNumber("Encoder Position", m_encoder.getPosition()); //Use this method to get how far the wheel has travelled.

    /**
     * Encoder velocity is read from a RelativeEncoder object by calling the
     * GetVelocity() method.
     * 
     * GetVelocity() returns the velocity of the encoder in units of RPM
     */
    SmartDashboard.putNumber("Encoder Velocity", m_encoder.getVelocity()); //Use this method to determine how fast the wheel is turing.
  }

How do we figure out how far we have gone in feet?
  
  



