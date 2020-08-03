Quickstart: creating your first robot
=====================================

Any robot program is really just a function that *accepts* the state of the game and *returns* the robot's action. Let's take another look at the default robot:

.. content-tabs::

    .. tab-container:: tab1
        :title: Python

        .. code-block:: python

            def robot(state, unit):
                if state.turn % 2 == 0:
                    return Action.move(Direction.East)
                else:
                    return Action.attack(Direction.South)

    .. tab-container:: tab2
        :title: Javascript

        .. code-block:: javascript

            function robot(state, unit) {
              if (state.turn % 2 === 0) {
                return Action.move(Direction.East)
              } else {
                return Action.attack(Direction.South)
              }
            }

The state of the arena is passed as the first argument, and information about this specific unit is passed as the second.

The body of this function must decide this robot's specific move. Recall that you have three options: move, attack, or pass (by returning ``None/null``). In this case, the robot alternates between moving east and attacking south.

During a battle simulation, every turn, this function is run for every one of your robots. After the same is done for the opponent's robots, the RR logic core resolves any movement conflicts, updates the arena state, and the cycle begins over again.

Reading the state
-----------------

Before any robot is run, its code is combined with one of our standard libraries (more info in :doc:`api`). These stdlib's provide several useful classes for dealing with the game state:



.. class:: State
    :noindex:

    This is the root class for all game state. Go here for arena-wide information, like:

    .. attribute:: turn
        :noindex:
    .. attribute:: our_team
        :noindex:
    .. attribute:: other_team
        :noindex:
    .. method:: objs_by_team(team)
        :noindex:
    .. method:: obj_by_coords(coords)
        :noindex:

These last two methods are particularly useful, because they return information about the units, contained in the ``Obj`` class:

.. class:: Obj
    :noindex:

    Anything that can exist within a grid tile is considered an ``Obj``. Right now, there are two types: :attr:`ObjType.Unit` and :attr:`ObjType.Terrain`.

    .. attribute:: id
        :noindex:
    .. attribute:: coords
        :noindex:
    .. attribute:: obj_type
        :noindex:
    .. attribute:: team
        :noindex:
    .. attribute:: health
        :noindex:

The stdlib also provides a special class for working with coordinates:

.. class:: Coords
    :noindex:

    .. attribute:: x, y
        :noindex:
    .. method:: distance_to(other)
        :noindex:
    .. method:: direction_to(other)
        :noindex:

...and several useful enums, like :class:`Direction`.

Armed with these new tools, we can drastically improve our robot program. Let's use the :func:`Coords.distance_to` method to find the closest enemy to the current robot and attack them:


.. content-tabs::

    .. tab-container:: tab1
        :title: Python

        .. code-block:: python

            def robot(state, unit):
                enemies = state.objs_by_team(state.other_team)
                closest_enemy = min(enemies,
                    key=lambda e: e.coords.distance_to(unit.coords)
                )
                direction = unit.coords.direction_to(closest_enemy.coords)

                if unit.coords.distance_to(closest_enemy.coords) == 1:
                    # we're right next to them
                    return Action.attack(direction)
                else:
                    return Action.move(direction)

    .. tab-container:: tab2
        :title: Javascript

        .. code-block:: javascript

            function robot(state, unit) {
              enemies = state.objsByTeam(state.otherTeam)
              closestEnemy = _.minBy(enemies,
                e => e.coords.distanceTo(unit.coords)
              )
              direction = unit.coords.directionTo(closestEnemy.coords)

              if (unit.coords.distanceTo(closestEnemy.coords) === 1) {
                // we're right next to them
                return Action.attack(direction)
              } else {
                return Action.move(direction)
              }
            }

Coordinating your army
----------------------

If you've ever played Starcraft, you're probably familiar with the terms *micro* and *macro*. *Micro* refers to the local decisions of your units, like how to maneuver between enemies and deliver a well-timed attack. *Macro*, on the other hand, refers to your high-level strategy, like when and where to move your armies.

Just like in Starcraft, any good RR player needs a combination of *micro* and *macro* to win. As you just saw, coding *micro* is relatively straightforward, since it comes down to simple logical decisions. But *macro* is much more difficult — it involves long-term planning, creativity, adaptability! And no, you can't cheat by importing a neural network and letting it do the work for you. Coding *macro* is really like coding anything else: you just need good abstraction, and a hell of a lot of patience.

A good place to start is with implementing coordination. Although the :func:`robot` function runs individually for every robot, you can use the global scope to share information and strategize. Let's improve our program by asking all of our robots to focus on one target (and we'll use :func:`Debug.inspect` to highlight that target in the map):

.. content-tabs::

    .. tab-container:: tab1
        :title: Python

        .. code-block:: python

            target_id = None

            def robot(state, unit):
                global target_id

                if target_id:
                    if not state.obj_by_id(target_id):
                        # target has died
                        target_id = None

                if not target_id:
                    allies = state.objs_by_team(state.our_team)

                    def total_distance_for_team(enemy):
                        return sum([ally.coords.distance_to(enemy.coords) for ally in allies])

                    enemies = state.objs_by_team(state.other_team)
                    closest_enemy_for_team = min(enemies,
                        key=total_distance_for_team
                    )
                    target_id = closest_enemy_for_team.id

                target = state.obj_by_id(target_id)
                debug.inspect(target)
                direction = unit.coords.direction_to(target.coords)

                if unit.coords.distance_to(target.coords) == 1:
                    # we're right next to them
                    return Action.attack(direction)
                else:
                    return Action.move(direction)

    .. tab-container:: tab2
        :title: Javascript

        .. code-block:: javascript

            let targetId = null

            function robot(state, unit) {
              if (targetId) {
                if (!state.objById(targetId)) {
                  // target has died
                  targetId = null
                }
              }

              if (!targetId) {
                allies = state.objsByTeam(state.ourTeam)

                const totalDistanceForTeam = (enemy) =>
                  _.sum(allies.map(ally => ally.coords.distanceTo(enemy.coords)))

                enemies = state.objsByTeam(state.otherTeam)
                closestEnemyForTeam = _.minBy(enemies, totalDistanceForTeam)
                targetId = closestEnemyForTeam.id
              }

              const target = state.objById(targetId)
              debug.inspect(target)

              direction = unit.coords.directionTo(target.coords)

              if (unit.coords.distanceTo(target.coords) === 1) {
                // we're right next to them
                return Action.attack(direction)
              } else {
                return Action.move(direction)
              }
            }

We can improve this code by taking advantage of :func:`init_turn`, which allows us to separate out the initialization code that needs to be called once every turn:

.. content-tabs::

    .. tab-container:: tab1
        :title: Python

        .. code-block:: python

            target_id = None

            def init_turn(state):
                global target_id

                if target_id:
                    if not state.obj_by_id(target_id):
                        # target has died
                        target_id = None

                if not target_id:
                    allies = state.objs_by_team(state.our_team)

                    def total_distance_for_team(enemy):
                        return sum([ally.coords.distance_to(enemy.coords) for ally in allies])

                    enemies = state.objs_by_team(state.other_team)
                    closest_enemy_for_team = min(enemies,
                        key=total_distance_for_team
                    )
                    target_id = closest_enemy_for_team.id

            def robot(state, unit):
                target = state.obj_by_id(target_id)
                debug.inspect(target)
                direction = unit.coords.direction_to(target.coords)

                if unit.coords.distance_to(target.coords) == 1:
                    # we're right next to them
                    return Action.attack(direction)
                else:
                    return Action.move(direction)


    .. tab-container:: tab2
        :title: Javascript

        .. code-block:: javascript

            let targetId = null

            function initTurn(state) {
              if (targetId) {
                if (!state.objById(targetId)) {
                  // target has died
                  targetId = null
                }
              }

              if (!targetId) {
                allies = state.objsByTeam(state.ourTeam)

                const totalDistanceForTeam = (enemy) =>
                  _.sum(allies.map(ally => ally.coords.distanceTo(enemy.coords)))

                enemies = state.objsByTeam(state.otherTeam)
                closestEnemyForTeam = _.minBy(enemies, totalDistanceForTeam)
                targetId = closestEnemyForTeam.id
              }
            }

            function robot(state, unit) {
              const target = state.objById(targetId)
              debug.inspect(target)

              direction = unit.coords.directionTo(target.coords)

              if (unit.coords.distanceTo(target.coords) === 1) {
                // we're right next to them
                return Action.attack(direction)
              } else {
                return Action.move(direction)
              }
            }

As you can probably tell, there are about a million ways to further improve this program. The stdlib may seem somewhat minimalist, but it actually contains more than enough tools for you to create arena-wrecking champions. Go create!


Publishing your robot
---------------------

When you create a robot under your profile, it will initially be saved as a draft. In order for others to see your robot, and for the matchmaker to queue your robot for ranked battles, you need to **publish** it by clicking the link available in the robot editor. You're only allowed to publish new code every 24 hours, so make sure to only do so when your robot is ready to meet the competition.


Debugging your robot
--------------------

Logging in RR occurs on two levels.

1. **Turn-wide info**: Calling the standard ``print``/``console.log`` function will immediately spit out your logs regardless of which robot they came from. You can view them in the bottom-right panel of the webview, or in the terminal output of rumblebot.
2. **Robot-specific info**: If you need to view information specific to individual units, you have a better option: :class:`Debug`. This class is dedicated to giving the programmer easy access to the webapp GUI, which is currently the only way to inspect robot-specific information. :func:`Debug.log` allows you to create a table with custom values, and :func:`Debug.inspect` gives you an easy way to locate a robot in the map. You can access these methods through a global ``debug`` variable.

Errors behave in a somewhat similar way. Initialization-level errors (like syntax errors) are fatal to your program, and so their output is placed in the same place as turn-wide logs. Runtime errors, on the other hand, occur locally to each robot function invocation, so they are placed in a similar space as the robot-specific info.
