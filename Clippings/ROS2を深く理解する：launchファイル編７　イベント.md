---
title: "ROS2を深く理解する：launchファイル編７　イベント"
source: "https://zenn.dev/uedake/articles/ros2_launch7_event"
author:
  - "[[Zenn]]"
published: 2024-03-16
created: 2025-08-26
description:
tags:
  - "clippings"
---
2[tech](https://zenn.dev/tech-or-idea)

## 解説対象

本記事では、ROS2のlaunch機能が提供するイベントの仕組みを解説します。

## 前提

- ROS2 humble時の実装に基づいています。
- launchファイルの記述は、python形式・xml形式・yaml形式の３形式のどれでも可能ですが、本記事はpython形式について解説しています。
	- ※launchファイルは特段の理由ない限りpython形式で書くべきです。シンプルな構成であればどの形式でも記述可能ですが、複雑なことをする場合xml形式・yaml形式では行き詰まります。最初は良くてもプロジェクトの進展によって後から複雑なことをしたくなるのが常ですので、launchファイルは最初からpython形式で書き始めることを推奨します。

## 公式ドキュメント

TBD

## 解説

## launchシステムにおけるイベント・イベントハンドラとは？

launchシステムでは「〇〇が発生した（＝イベントが起きた）」時に「〇〇をする（=イベントハンドラを実行する）」という処理が可能です。  
このトリガーを表す概念が「イベント」であり、イベントが起きたときに何の処理をするのかを記載したのが「イベントハンドラ」です。

イベントハンドラは任意のpython関数が使えますが、主に行うことは「実行したいlaunchアクションを戻り値として返す」ことです。  
イベントハンドラの実行結果として返されたlaunchアクションはlaunchシステム（正確には `LaunchService` ）によって実行されていくので、結果的に「〇〇が発生した（＝イベントが起きた）」時に「〇〇をする（＝望むlaunchアクションを実行する）」ということが可能になります。

例えば標準で用意されている `OnIncludeLaunchDescription` イベントハンドラは、 `IncludeLaunchDescription` イベント（〇〇というlaunch fileを読み込めという指示を受けたときに発生するイベント）をトリガーとし、そのイベント中で指定されているlaunch fileから得られるlaunchアクションを返します。その結果、launch fileに記載されているlaunchアクションが実行できます。（これがlaunch fileのincludeの仕組みです）

## launchシステムにおけるイベントキューとは？

イベントキューとは、イベントが処理すべき順に積まれたキューです。実体は `LaunchService` が管理する `LaunchContext` のインスタンス変数 `_event_queue` にあります

## launchシステムにおけるイベントハンドラリストとは？

イベントハンドラリストとは、イベントを処理するための関数のリストです。実体は `LaunchService` が管理する `LaunchContext` のインスタンス変数 `_event_handlers` にあります。実体はlistでなくdequeですが、概念的にはリストのように使用されているので便宜上リストと呼びます。

## 標準で用意されているイベントハンドラ

- launchレポジトリでイベントハンドラとして用意されているのは、下記6つです
	- [`OnIncludeLaunchDescription` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_include_launch_description.py)
	- [`OnExecutionComplete` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_execution_complete.py)
	- [`OnProcessStart` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_process_start.py)
	- [`OnProcessIO` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_process_io.py)
	- [`OnProcessExit` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_process_exit.py)
	- [`OnShutdown` イベントハンドラ](https://github.com/ros2/launch/blob/humble/launch/launch/event_handlers/on_shutdown.py)
- launch\_rosレポジトリでイベントハンドラとして用意されているのは、下記1つです
	- [`OnStateTransition` イベントハンドラ](https://github.com/ros2/launch_ros/blob/humble/launch_ros/launch_ros/event_handlers/on_state_transition.py)

## （参考）ソースの確認

## LaunchServiceを理解する

`LaunchService` を見てみましょう。

- `LaunchService` において、イベントキューとイベントハンドラリストが実装されています
- `LaunchService` のコンストラクタが実行されると、イベントハンドラリストに２つのイベントハンドラが登録されます
	- `OnIncludeLaunchDescription` イベントハンドラ
	- `OnShutdown` イベントハンドラ

これ以外のイベントハンドラはユーザが必要に応じて登録することで使用可能になります

[launch\_service.py](https://github.com/ros2/launch/blob/humble/launch/launch/launch_service.py)

launch\_service.py抜粋

```
class LaunchService:
    """Service that manages the event loop and runtime for launched system."""
    
    def __init__(
        self,
        *,
        argv: Optional[Iterable[Text]] = None,
        noninteractive: bool = False,
        debug: bool = False
    ) -> None:
        """
        Create a LaunchService.

        :param: argv stored in the context for access by the entities, None results in []
        :param: noninteractive if True (not default), this service will assume it has
            no terminal associated e.g. it is being executed from a non interactive script
        :param: debug if True (not default), asyncio the logger are seutp for debug
        """
        # Setup logging and debugging.
        launch.logging.launch_config.level = logging.DEBUG if debug else logging.INFO
        self.__debug = debug
        self.__argv = argv if argv is not None else []

        # Setup logging
        self.__logger = launch.logging.get_logger('launch')

        # Setup context and register a built-in event handler for bootstrapping.
        self.__context = LaunchContext(argv=self.__argv, noninteractive=noninteractive)
        self.__context.register_event_handler(OnIncludeLaunchDescription())
        self.__context.register_event_handler(OnShutdown(on_shutdown=self.__on_shutdown))
```

## include\_launch\_description()を理解する

`LaunchService` の `include_launch_description()` が実行されると、イベントキューに `IncludeLaunchDescription` イベントがイベントキューに追加されます。

- 非常に混乱しやすくて注意なのが、 `IncludeLaunchDescription` という名前のクラスは、「launchアクション」と「イベント」の両方が存在すること
	- イベントとしての `IncludeLaunchDescription`
		- [launch.events.IncludeLaunchDescription](https://github.com/ros2/launch/blob/humble/launch/launch/events/include_launch_description.py)
		- `LaunchService` の `include_launch_description()` メソッド内で生成される
		- プロパティとして `launch_description` （ `LaunchDescription` 型）をもつ
			- この `LaunchDescription` は `IncludeLaunchDescription` アクションをもつ
	- launchアクションとしての `IncludeLaunchDescription`
		- [launch.actions.IncludeLaunchDescription](https://github.com/ros2/launch/blob/humble/launch/launch/actions/include_launch_description.py)
		- ルートとなるlaunch fileの読み込みについては、 `launch_a_launch_file()` メソッド内で生成される
		- launch fileからincludeされるlaunch fileの読み込みについては、親のlaunch file内でlaunchアクションとして定義される

launch\_service.py抜粋

```
class LaunchService:

    #略

    def emit_event(self, event: Event) -> None:
        """
        Emit an event synchronously and thread-safely.

        If the LaunchService is not running, the event is queued until it is.
        """
        future = None
        with self.__loop_from_run_thread_lock:
            if self.__loop_from_run_thread is not None:
                # loop is in use, asynchronously emit the event
                future = asyncio.run_coroutine_threadsafe(
                    self.__context.emit_event(event),
                    self.__loop_from_run_thread
                )
            else:
                # loop is not in use, synchronously emit the event, and it will be processed later
                self.__context.emit_event_sync(event)

        if future is not None:
            # Block until asynchronously emitted event is emitted by loop
            future.result()

    def include_launch_description(self, launch_description: LaunchDescription) -> None:
        """
        Evaluate a given LaunchDescription and visits all of its entities.

        This method is thread-safe.
        """
        self.emit_event(IncludeLaunchDescription(launch_description))
```

## イベントのハンドリング処理を理解する

- `launch_service.run()` が実行されると、launch処理のloopが開始されイベントキューに積まれたイベントを１つ１つ順番に実行していきます
	- イベントを１つ１つ順番に実行しているのが `_process_one_event()` メソッドであり、実体はそこから呼ばれる `__process_event()` メソッドです
	- 実行すべきイベントを実行可能なイベントハンドラをイベントハンドラリストから見つけ、そのイベントハンドラの `handle()` メソッドの第一引数にイベントを渡すことによってイベント実行します
	- イベントハンドラは戻り値としてlaunchアクションもしくは `LaunchDescription` のリストを返します
		- 正しくは `LaunchDescriptionEntity` のリストが戻り値と定義されていますが、 `LaunchDescriptionEntity` クラスを継承しているのがlaunchアクションもしくは `LaunchDescription` です
	- イベントハンドラの戻り値（launchアクションもしくは `LaunchDescription` のリスト）は `visit_all_entities_and_collect_futures()` に渡されて実行されます
- つまりこの仕組みにより「〇〇が発生したら（＝イベントが起きたら）」「〇〇をする（=launchアクションを実行する）」という処理が実現されています

[launch\_service.py](https://github.com/ros2/launch/blob/humble/launch/launch/launch_service.py)

launch\_service.py抜粋

```
class LaunchService:
    #略

    async def _process_one_event(self) -> None:
        next_event = await self.__context._event_queue.get()
        await self.__process_event(next_event)

    async def __process_event(self, event: Event) -> None:
        self.__logger.debug("processing event: '{}'".format(event))
        for event_handler in tuple(self.__context._event_handlers):
            if event_handler.matches(event):
                self.__logger.debug(
                    "processing event: '{}' ✓ '{}'".format(event, event_handler))
                self.__context._push_locals()
                entities = event_handler.handle(event, self.__context)
                entities = \
                    entities if isinstance(entities, collections.abc.Iterable) else (entities,)
                for entity in [e for e in entities if e is not None]:
                    from .utilities import is_a_subclass
                    if not is_a_subclass(entity, LaunchDescriptionEntity):
                        raise RuntimeError(
                            "expected a LaunchDescriptionEntity from event_handler, got '{}'"
                            .format(entity)
                        )
                    self._entity_future_pairs.extend(
                        visit_all_entities_and_collect_futures(entity, self.__context))
                self.__context._pop_locals()
            else:
                pass
```

[GitHubで編集を提案](https://github.com/uedake/zenn/blob/main/articles/ros2_launch7_event.md)

2

2