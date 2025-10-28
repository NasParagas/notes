

[Creating an action — ROS 2 Documentation: Jazzy documentation](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Creating-an-Action.html)
[Writing an action server and client (C++) — ROS 2 Documentation: Jazzy documentation](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Writing-an-Action-Server-Client/Cpp.html)

実装
---
`.action`ファイルを次のように定義
```.action
# Request
---
# Result
---
# Feedback
```
- A _request_ message is sent from an action client to an action server initiating a new goal.
- A _result_ message is sent from an action server to an action client when a goal is done.
- _Feedback_ messages are periodically sent from an action server to an action client with updates about a goal.

例えばフィボナッチ数列を計算するactionを作りたい時は
```
int32 order
---
int32[] sequence
---
int32[] partial_sequence
```
のように定義する
order: どこまで計算するか(階数?)
sequence: 
