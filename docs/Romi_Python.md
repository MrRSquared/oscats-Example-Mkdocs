# Programming the Romi with Python
The WPILib Robotpy page has a wonderful sample project [here](https://robotpy.readthedocs.io/en/latest/guide/anatomy.html#putting-it-all-together).



However, because the Romi uses network tables to communicate, this will not work out of the box. So, we need to make a few changes.

There is a wonderful sample that uses the Romi [here](https://github.com/robotpy/examples/blob/main/commands-v2/romi/robot.py)

1) We need to follow the directions in the command example above to install the dependencies.
   The Romi code uses the old install so we install it with the following command `py -3 -m pip install robotpy-romi`
2) We may also want to install the simulator libraries `py -3 -m pip install robotpy-romi`
3) We can add the ROmi specific code to the simple sample...
```
#!/usr/bin/env python3
"""
    This is a good foundation to build your robot code on
"""

import wpilib
import wpilib.drive


class MyRobot(wpilib.TimedRobot):

    def robotInit(self):
        """
        This function is called upon program startup and
        should be used for any initialization code.
        """
        self.left_motor = wpilib.Spark(0)
        self.right_motor = wpilib.Spark(1)
        self.drive = wpilib.drive.DifferentialDrive(self.left_motor, self.right_motor)
        self.stick = wpilib.Joystick(1)
        self.timer = wpilib.Timer()

    def autonomousInit(self):
        """This function is run once each time the robot enters autonomous mode."""
        self.timer.reset()
        self.timer.start()

    def autonomousPeriodic(self):
        """This function is called periodically during autonomous."""

        # Drive for two seconds
        if self.timer.get() < 2.0:
            self.drive.arcadeDrive(-0.5, 0)  # Drive forwards at half speed
        else:
            self.drive.arcadeDrive(0, 0)  # Stop robot

    def teleopPeriodic(self):
        """This function is called periodically during operator control."""
        self.drive.arcadeDrive(self.stick.getY(), self.stick.getX())


if __name__ == "__main__":
    wpilib.run(MyRobot)
```
