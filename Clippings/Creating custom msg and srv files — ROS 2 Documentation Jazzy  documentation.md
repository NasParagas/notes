---
title: "Creating custom msg and srv files — ROS 2 Documentation: Jazzy  documentation"
source: "https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html"
author:
published:
created: 2025-08-21
description:
tags:
  - "clippings"
---
**You're reading the documentation for an older, but still supported, version of ROS 2. For information on the latest version, please have a look at [Kilted](https://docs.ros.org/en/kilted/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html).**

## Creating custom msg and srv files

**Goal:** Define custom interface files (`.msg` and `.srv`) and use them with Python and C++ nodes.

**Tutorial level:** Beginner

**Time:** 20 minutes

## 

In previous tutorials you utilized message and service interfaces to learn about [topics](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Topics/Understanding-ROS2-Topics.html), [services](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Services/Understanding-ROS2-Services.html), and simple publisher/subscriber ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html) / [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Publisher-And-Subscriber.html)) and service/client ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Service-And-Client.html) / [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Service-And-Client.html)) nodes. The interfaces you used were predefined in those cases.

While it’s good practice to use predefined interface definitions, you will probably need to define your own messages and services sometimes as well. This tutorial will introduce you to the simplest method of creating custom interface definitions.

## 

You should have a [ROS 2 workspace](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html).

This tutorial also uses the packages created in the publisher/subscriber ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html) and [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Publisher-And-Subscriber.html)) and service/client ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Service-And-Client.html) and [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Service-And-Client.html)) tutorials to try out the new custom messages.

## 

### 

For this tutorial you will be creating custom `.msg` and `.srv` files in their own package, and then utilizing them in a separate package. Both packages should be in the same workspace.

Since we will use the pub/sub and service/client packages created in earlier tutorials, make sure you are in the same workspace as those packages (`ros2_ws/src`), and then run the following command to create a new package:

```
$ ros2 pkg create --build-type ament_cmake --license Apache-2.0 tutorial_interfaces
```

`tutorial_interfaces` is the name of the new package. Note that it is, and can only be, an ament\_cmake package, but this doesn’t restrict in which type of packages you can use your messages and services. You can create your own custom interfaces in an ament\_cmake package, and then use it in a C++ or Python node, which will be covered in the last section.

The `.msg` and `.srv` files are required to be placed in directories called `msg` and `srv` respectively. Create the directories in `ros2_ws/src/tutorial_interfaces`:

```
$ mkdir msg srv
```

### 

#### 2.1 msg definition

In the `tutorial_interfaces/msg` directory you just created, make a new file called `Num.msg` with one line of code declaring its data structure:

```bash
int64 num
```

This is a custom message that transfers a single 64-bit integer called `num`.

Also in the `tutorial_interfaces/msg` directory you just created, make a new file called `Sphere.msg` with the following content:

```bash
geometry_msgs/Point center
float64 radius
```

This custom message uses a message from another message package (`geometry_msgs/Point` in this case).

#### 2.2 srv definition

Back in the `tutorial_interfaces/srv` directory you just created, make a new file called `AddThreeInts.srv` with the following request and response structure:

```bash
int64 a
int64 b
int64 c
---
int64 sum
```

This is your custom service that requests three integers named `a`, `b`, and `c`, and responds with an integer called `sum`.

### 

To convert the interfaces you defined into language-specific code (like C++ and Python) so that they can be used in those languages, add the following lines to `CMakeLists.txt`:

```cmake
find_package(geometry_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Num.msg"
  "msg/Sphere.msg"
  "srv/AddThreeInts.srv"
  DEPENDENCIES geometry_msgs # Add packages that above messages depend on, in this case geometry_msgs for Sphere.msg
)
```

Note

The first argument (library name) in the `rosidl_generate_interfaces` must start with the name of the package, e.g., simply `${PROJECT_NAME}` or `${PROJECT_NAME}_suffix`. See [https://github.com/ros2/rosidl/issues/441#issuecomment-591025515](https://github.com/ros2/rosidl/issues/441#issuecomment-591025515).

### 

Because the interfaces rely on `rosidl_default_generators` for generating language-specific code, you need to declare a build tool dependency on it.`rosidl_default_runtime` is a runtime or execution-stage dependency, needed to be able to use the interfaces later. The `rosidl_interface_packages` is the name of the dependency group that your package, `tutorial_interfaces`, should be associated with, declared using the `<member_of_group>` tag.

Add the following lines within the `<package>` element of `package.xml`:

```xml
<depend>geometry_msgs</depend>
<buildtool_depend>rosidl_default_generators</buildtool_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

### 

Now that all the parts of your custom interfaces package are in place, you can build the package. In the root of your workspace (`~/ros2_ws`), run the following command:

```
$ colcon build --packages-select tutorial_interfaces
```

Now the interfaces will be discoverable by other ROS 2 packages.

### 

In a new terminal, run the following command from within your workspace (`ros2_ws`) to source it:

```
$ source install/setup.bash
```

Now you can confirm that your interface creation worked by using the `ros2 interface show` command. The output you see in your terminal should look similar to the following:

```
$ ros2 interface show tutorial_interfaces/msg/Num
int64 num
```

```
$ ros2 interface show tutorial_interfaces/msg/Sphere
geometry_msgs/Point center
        float64 x
        float64 y
        float64 z
float64 radius
```

```
$ ros2 interface show tutorial_interfaces/srv/AddThreeInts
int64 a
int64 b
int64 c
---
int64 sum
```

### 

For this step you can use the packages you created in previous tutorials. A few simple modifications to the nodes, `CMakeLists.txt` and `package.xml` files will allow you to use your new interfaces.

#### 7.1 Testing Num.msg with pub/sub

With a few modifications to the publisher/subscriber package created in a previous tutorial ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html) or [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Publisher-And-Subscriber.html)), you can see `Num.msg` in action. Since you’ll be changing the standard string msg to a numerical one, the output will be slightly different.

**Publisher**

**Subscriber**

**CMakeLists.txt**

Add the following lines (C++ only):

**package.xml**

Add the following line:

```
<depend>tutorial_interfaces</depend>
```

After making the above edits and saving all the changes, build the package:

On Linux/macOS:

```
$ colcon build --packages-select cpp_pubsub
```

On Windows:

```
$ colcon build --merge-install --packages-select cpp_pubsub
```

Then open two new terminals, source `ros2_ws` in each, and run:

```
$ ros2 run cpp_pubsub talker
```

```
$ ros2 run cpp_pubsub listener
```

Since `Num.msg` relays only an integer, the talker should only be publishing integer values, as opposed to the string it published previously:

```
[INFO] [minimal_publisher]: Publishing: '0'
[INFO] [minimal_publisher]: Publishing: '1'
[INFO] [minimal_publisher]: Publishing: '2'
```

#### 7.2 Testing AddThreeInts.srv with service/client

With a few modifications to the service/client package created in a previous tutorial ([C++](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Service-And-Client.html) or [Python](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Service-And-Client.html)), you can see `AddThreeInts.srv` in action. Since you’ll be changing the original two integer request srv to a three integer request srv, the output will be slightly different.

**Service**

**Client**

```
#include "rclcpp/rclcpp.hpp"
#include "tutorial_interfaces/srv/add_three_ints.hpp"                                       // CHANGE

#include <chrono>
#include <cstdlib>
#include <memory>

using namespace std::chrono_literals;

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  if (argc != 4) { // CHANGE
      RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "usage: add_three_ints_client X Y Z");      // CHANGE
      return 1;
  }

  std::shared_ptr<rclcpp::Node> node = rclcpp::Node::make_shared("add_three_ints_client");  // CHANGE
  rclcpp::Client<tutorial_interfaces::srv::AddThreeInts>::SharedPtr client =                // CHANGE
    node->create_client<tutorial_interfaces::srv::AddThreeInts>("add_three_ints");          // CHANGE

  auto request = std::make_shared<tutorial_interfaces::srv::AddThreeInts::Request>();       // CHANGE
  request->a = atoll(argv[1]);
  request->b = atoll(argv[2]);
  request->c = atoll(argv[3]);                                                              // CHANGE

  while (!client->wait_for_service(1s)) {
    if (!rclcpp::ok()) {
      RCLCPP_ERROR(rclcpp::get_logger("rclcpp"), "Interrupted while waiting for the service. Exiting.");
      return 0;
    }
    RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "service not available, waiting again...");
  }

  auto result = client->async_send_request(request);
  // Wait for the result.
  if (rclcpp::spin_until_future_complete(node, result) ==
    rclcpp::FutureReturnCode::SUCCESS)
  {
    RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "Sum: %ld", result.get()->sum);
  } else {
    RCLCPP_ERROR(rclcpp::get_logger("rclcpp"), "Failed to call service add_three_ints");    // CHANGE
  }

  rclcpp::shutdown();
  return 0;
}
```

**CMakeLists.txt**

Add the following lines (C++ only):

```cmake
#...

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(tutorial_interfaces REQUIRED)         # CHANGE

add_executable(server src/add_two_ints_server.cpp)
ament_target_dependencies(server
  rclcpp tutorial_interfaces)                      # CHANGE

add_executable(client src/add_two_ints_client.cpp)
ament_target_dependencies(client
  rclcpp tutorial_interfaces)                      # CHANGE

install(TARGETS
  server
  client
  DESTINATION lib/${PROJECT_NAME})

ament_package()
```

**package.xml**

Add the following line:

```
<depend>tutorial_interfaces</depend>
```

After making the above edits and saving all the changes, build the package:

On Linux/macOS:

```
$ colcon build --packages-select cpp_srvcli
```

On Windows:

```
$ colcon build --merge-install --packages-select cpp_srvcli
```

Then open two new terminals, source `ros2_ws` in each, and run:

```
$ ros2 run cpp_srvcli server
```

```
$ ros2 run cpp_srvcli client 2 3 1
```

## 

In this tutorial, you learned how to create custom interfaces in their own package and how to utilize those interfaces in other packages.

This tutorial only scratches the surface about defining custom interfaces. You can learn more about it in [About ROS 2 interfaces](https://docs.ros.org/en/jazzy/Concepts/Basic/About-Interfaces.html).

## 

The [next tutorial](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Single-Package-Define-And-Use-Interface.html) covers more ways to use interfaces in ROS 2.