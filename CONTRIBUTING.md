# Contributing to the CRI group repositories

:+1::tada: First off, thanks for taking the time to contribute! :tada::+1:

The following is a set of guidelines for contributing to [CRI Group repositories](https://github.com/crigroup) on GitHub.

## What should I know before I get started?

### Packages and Modules
These are all the packages currently being maintained:

* [`bcap`](https://github.com/fsuarez6/bcap): bCAP library as a ROS package
* [`behavior_trees`](https://github.com/fsuarez6/behavior_trees): ROS metapackage for Behavior Trees. Writen in Python
* [`cribags`](https://github.com/crigroup/cribags): ROS bags
* [`criros`](https://github.com/crigroup/criros): ROS utilities used by the CRI group
* [`ensenso`](https://github.com/crigroup/ensenso): Code for using Ensenso camera
* [`netft`](https://github.com/fsuarez6/netft): ROS metapackage for the ATI NET/FT box + ATI F/T sensor.
* [`optitrack`](https://github.com/crigroup/optitrack): ROS package for working with the NaturalPoint Optitrack motion capture setup
* [`robotiq`](https://github.com/crigroup/robotiq): ROS Metapackage for Robotiq 85 Gripper, Modbus TCP/IP

### Design Decisions
When we make a significant decision in how we maintain our projects and what we can or cannot support, we will be documenting them in the corresponding repository wiki. If you have a question around how we do things, check to see if it is documented there. If it is *not* documented there, please open a new issue.

## How Can I Contribute?
We use the [Github Flow](https://guides.github.com/introduction/flow/) workflow. Therefore, you will have to: 

* Create a branch
* Add commits to it
* Open pull request 
* Now, we can merge it into the `master` branch.

### Pull Requests

* Include screenshots and animated GIFs in your pull request whenever possible.
* Follow the [Python](#python-styleguide) and [CPP](#cpp-styleguide) styleguides.
* Document new code based on the [Documentation Styleguide](#documentation-styleguide)

## Styleguides

### Git Commit Messages

* Use the present tense ("Add feature" not "Added feature")
* Use the imperative mood ("Move function to..." not "Moves function to...")
* Limit the first line to 72 characters or less
* Reference issues and pull requests liberally
* When only changing documentation, include `[ci skip]` in the commit description
* Consider starting the commit message with an applicable emoji:
    * :art: `:art:` when improving the format/structure of the code
    * :racehorse: `:racehorse:` when improving performance
    * :non-potable_water: `:non-potable_water:` when plugging memory leaks
    * :memo: `:memo:` when writing docs
    * :bug: `:bug:` when fixing a bug
    * :fire: `:fire:` when removing code or files
    * :white_check_mark: `:white_check_mark:` when adding tests
    * :arrow_up: `:arrow_up:` when upgrading dependencies
    * :arrow_down: `:arrow_down:` when downgrading dependencies

### ROS Packages Styleguide
TODO

### Python Styleguide
The main styleguide for coding in Python is [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#naming-conventions). These are the things to keep in mind:

* **Indentation**: Use 2 spaces per indentation level. This is different from PEP 8 but we prefer this way so that the code can be better read in Github.
* **Naming Style**: There are a lot of different naming styles. We follow `lower_case_with_underscores` style.
* **Tabs or Spaces?**: Use **spaces** as indentation method.

#### Example
```{python}
class Action(object):
  def __init__(self, identifier, title, properties):
    self.category = 'Action'
    self.identifier = identifier
    self.title = title
    self.properties = collections.defaultdict(lambda:None, properties)
    self.state = ActionState.NONE
  
  def get_state(self):
    return self.state
  
  def tick(self):
    pass
``` 

### CPP Styleguide
The main styleguide for coding in CPP is the [ROS C++ Style Guide](http://wiki.ros.org/CppStyleGuide). These are the things to keep in mind:

* **Indentation**: Use 2 spaces to indent code blocks.
* **Naming Style**: There are a lot of different naming styles. We follow `lower_case_with_underscores` style.
* **Tabs or Spaces?**: Use **spaces** as indentation method.
* **Files naming**: Please pay particular attention to the [naming of files](http://wiki.ros.org/CppStyleGuide#Files).

#### Example
**Filename:** `rc8_ros_driver.cpp`
```{c++}
class RC8RosDriver : public hardware_interface::RobotHW
{
public:
  RC8RosDriver() : nh_private_("~")
  {
    // Load configuration parameters
    nh_private_.param("ip", ip_address_, std::string(DEFAULT_IP_ADDRESS));
    if (!nh_private_.hasParam("ip"))
      ROS_WARN_STREAM("Parameter [~ip] not found, using default: " << ip_address_);
    nh_private_.param("rate", rate_, 125.0);
    if (!nh_private_.hasParam("rate"))
      ROS_WARN("Parameter [~rate] not found, using default: %.2f", rate_);
  }
private:
  ros::NodeHandle   nh_, nh_private_;
  std::string       ip_address_;
  double            rate_;
``` 

### Documentation Styleguide

* We use [epydoc](http://epydoc.sourceforge.net/epydoc.html) for Python and [doxygen](https://www.stack.nl/~dimitri/doxygen/manual/docblocks.html) for CPP documentation.
* Document all your classes and methods in your libraries so that others can understand what is going on.

#### Python example
```{python}
class JointControllerBase(object):
  """
  Base class for the Joint Position Controllers. It subscribes to the C{joint_states} topic by default.
  """
  def __init__(self, namespace='', log_level=rospy.INFO):
    """
    JointControllerBase constructor. It subscribes to the C{joint_states} topic and informs after 
    successfully reading a message from the topic.
    @type namespace: string
    @param namespace: Override ROS namespace manually. Useful when controlling several robots from the 
    same node.
    @type log_level: int
    @param log_level: Change the log level. Basically avoid info messages when C{rospy.ERROR} or 
    C{rospy.FATAL} is specified.
    """
    
  def joint_states_cb(self, msg):
    """
    Callback executed every time a message is publish in the C{joint_states} topic.
    @type  msg: sensor_msgs/JointState
    @param msg: The JointState message published by the RT hardware interface.
    """
``` 
Full example [here](https://github.com/quangounet/denso_common/blob/master/denso_control/src/denso_control/controllers.py).

#### CPP example
```{c++}
namespace denso_control
{
/**
 * \brief This class serves as interface with the <strong>DENSO RC8 Controller</strong>.
 * 
 * It uses the b-Cap library provided by DENSO to perform the 
 * communication with the robot.
 * <b>IMPORTANT:</b> Be careful using bcap functions such as SysFreeString.
 * If you do twice SysFreeString to the same variable it will generate a 
 * core segmentation. Difficult to debug and annoying!
 */
class RC8ControllerInterface
{
  public:
    /**
     * The constructor of RC8ControllerInterface.
     * 
     * @param address The IP address of the RC8 Controller
     * @param read_only During READ-ONLY mode, the motors are OFF
     * communication
     */
    RC8ControllerInterface(const std::string &address, bool read_only);
``` 
Full example [here](https://github.com/quangounet/denso_common/blob/master/denso_control/src/rc8_ros_driver.cpp).
