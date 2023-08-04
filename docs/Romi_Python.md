# Programming the Romi with Python
The WPILib Robotpy page has a wonderful sample project [here](https://robotpy.readthedocs.io/en/latest/guide/anatomy.html#putting-it-all-together).



However, because the Romi uses network tables to communicate, this will not work out of the box. So, we need to make a few changes.

There is a wonderful sample that uses the Romi [here](https://github.com/robotpy/examples/blob/main/commands-v2/romi/robot.py)

1) We need to follow the directions in the command example above to install the dependencies.\

   The Romi code uses the old install so we install it with the following command `py -3 -m pip install robotpy-romi`
   
2) We may also want to install the simulator libraries `py -3 -m pip install robotpy-romi`

4) We can add the Romi specific code to the functional sample...

```
#!/usr/bin/env python3
"""
    This is a good foundation to build your robot code on.
    This version is for the Romi. 
    To run it, do not forget to use the correct flags
    
#    # Windows
#    py -3 robot.py sim --ws-client
#
#    # Linux/macOS
#    python robot.py sim --ws-client
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
    # If your ROMI isn't at the default address, set that here
    os.environ["HALSIMWS_HOST"] = "10.0.0.2"
    os.environ["HALSIMWS_PORT"] = "3300"
    wpilib.run(MyRobot)
```

5) Use the correct flags in the terminal to run your software.
```

# Windows
    py -3 robot.py sim --ws-client

# Linux/macOS
    python robot.py sim --ws-client
```
This can be found on my [Github](https://github.com/MrRSquared/RomiPythonFoundation) as well.
To add other objects from the Romi, use the [built-in API](https://robotpy.readthedocs.io/projects/romi/en/latest/api.html)

# Adding some complexity...
We can use the [built-in API](https://robotpy.readthedocs.io/projects/romi/en/latest/api.html) to get some important information about the robot for instance how fast it is going, how far it moved, etc.
Here is an example that leverages the Romi API.
```
#!/usr/bin/env python3
"""
    This is adds the Romi IO to the foundation program.
    It can be found here. 
    https://robotpy.readthedocs.io/en/latest/guide/anatomy.html

"""

# I merged many aspcets of this example https://github.com/robotpy/examples/tree/main/commands-v2/romi on Github to get this running...
# The Following Header is also helpful...

# Example that shows how to connect to a ROMI from RobotPy
#
# Requirements
# ------------
#
# You must have the robotpy-halsim-ws package installed. This is best done via:
#
#    # Windows
#    py -3 -m pip install robotpy[commands2,sim]
#
#    # Linux/macOS
#    pip3 install robotpy[commands2,sim]
#
# Run the program
# ---------------
#
# To run the program you will need to explicitly use the ws-client option:
#
#    # Windows
#    py -3 robot.py sim --ws-client
#
#    # Linux/macOS
#    python robot.py sim --ws-client
#
# By default the WPILib simulation GUI will be displayed. To disable the display
# you can add the --nogui option
#

import wpilib
import wpilib.drive
# This is required for the simulator to work.
import os
# This is required for the Accellerometer and Gyro.
import romi
# we also need to import Math to, you know, do Maths
import math


class MyRobot(wpilib.TimedRobot):
    # We like to start constants with a K
    kCountsPerRevolution = 1440.0 
    # Counts per rev is what it sounds like. How many signals do we get in one revolution.
    kWheelDiameterInch = 2.75591
    # We want the diameter to calculate how far the robot is going and how fast it is moving.
    # Mathmatically, we are (almost) peeling the wheel onto the floor as we drive to calcualte the value.


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

        # The Romi has onboard encoders that are hardcoded
        # to use DIO pins 4/5 and 6/7 for the left and right
        self.leftEncoder = wpilib.Encoder(4, 5)
        self.rightEncoder = wpilib.Encoder(6, 7)

        # Set up the RomiGyro
        self.gyro = romi.RomiGyro()

        # Set up the BuiltInAccelerometer
        self.accelerometer = wpilib.BuiltInAccelerometer()

        # Use inches as unit for encoder distances
        self.leftEncoder.setDistancePerPulse(
            (math.pi * self.kWheelDiameterInch) / self.kCountsPerRevolution
        )
        self.rightEncoder.setDistancePerPulse(
            (math.pi * self.kWheelDiameterInch) / self.kCountsPerRevolution
        )
        self.resetEncoders()
    def robotPeriodic(self):
        """
        This function is called once upon each loop.
        """

        # Put data on the smart dashboard
        
        # Encoder Data
        wpilib.SmartDashboard.putNumber("Left Encoder Count", self.leftEncoder.get())
        wpilib.SmartDashboard.putNumber("Right Encoder Count", self.rightEncoder.get())
        wpilib.SmartDashboard.putNumber("Left Encoder Distance", self.leftEncoder.getDistance())
        wpilib.SmartDashboard.putNumber("Right Encoder Distance", self.rightEncoder.getDistance())
        
        # Accelerometer Data
        wpilib.SmartDashboard.putNumber("Acc. X", self.accelerometer.getX())
        wpilib.SmartDashboard.putNumber("Acc. Y", self.accelerometer.getY())
        wpilib.SmartDashboard.putNumber("Acc. Z", self.accelerometer.getZ()) 
        
        # Gyro Data
        wpilib.SmartDashboard.putNumber("Gyro X", self.gyro.getAngleX())
        wpilib.SmartDashboard.putNumber("Gyro Y", self.gyro.getAngleY())
        wpilib.SmartDashboard.putNumber("Gyro Z", self.gyro.getAngleZ()) 

    def autonomousInit(self):
        """This function is run once each time the robot enters autonomous mode."""
        self.timer.reset()
        self.timer.start()

    def autonomousPeriodic(self):
        """This function is called periodically during autonomous."""

        # Drive for two seconds
        if self.timer.get() < 2.0:
            self.drive.arcadeDrive(-0.4, 0)  # Drive forwards at half speed
        else:
            self.drive.arcadeDrive(0, 0)  # Stop robot

    def teleopPeriodic(self):
        """This function is called periodically during operator control."""
        self.drive.arcadeDrive(self.stick.getY(), self.stick.getX())

    def resetEncoders(self) -> None:
        """Resets the drive encoders to currently read a position of 0."""
        self.leftEncoder.reset()
        self.rightEncoder.reset()

if __name__ == "__main__":
    # These are the two lines required to connect to the Romi through Python.
    # If your ROMI isn't at the default address, set that here
    os.environ["HALSIMWS_HOST"] = "10.0.0.2"
    os.environ["HALSIMWS_PORT"] = "3300"


    wpilib.run(MyRobot)
```
There is a copy of this on my [Github](https://github.com/MrRSquared/RomiPythonIO) as well.

Next Steps...

- Create an autonomous routine that drives forward and stops at a specific distance (we need to add this to our current competition bot).
- Learn to use the [Command Framework](https://docs.wpilib.org/en/stable/docs/software/commandbased/index.html)
     -[This is the Python Specific API](https://robotpy.readthedocs.io/projects/commands-v2/en/latest/api.html)
- RobotPy has a complete example on their [Commands Git](https://github.com/robotpy/examples/tree/main/commands-v2/romi)
- There are a few directions we can go from here. We will discuss them and then find some more resources.
