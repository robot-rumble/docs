The complete API reference
==========================

The Standard Library
--------------------

.. class:: Direction(enum)

    .. attribute:: North, South, East, West
    .. attribute:: opposite
    .. attribute:: to_coords

        Convert to a coordinate pair like ``(1, 0)`` or ``(0, -1)``

.. class:: Coords

    .. attribute:: x, y

    .. method:: __init__(x, y)

    .. method:: distance_to(other)
    .. method:: walking_distance_to(other)
    .. method:: direction_to(other)

    .. method:: __add__(other)
    .. method:: __sub__(other)
    .. method:: __mul__(other)

        Languages without operator overloading have similarly named functions: ``add``, ``sub``, ``mul``.

.. class:: Team(enum)

    .. attribute:: Red, Blue
    .. attribute:: opposite

.. class:: ObjType(enum)

    .. attribute:: Unit, Terrain

.. class:: Obj

    Anything that can exist within a grid tile.

    .. attribute:: id
    .. attribute:: coords
    .. attribute:: obj_type
    .. attribute:: team
    .. attribute:: health

.. class:: State

    Root class for all game state.

    .. attribute:: turn_num
    .. attribute:: our_team
    .. attribute:: other_team

    .. method:: ids_by_team(team)
    .. method:: objs_by_team(team)

    .. method:: id_by_coords(coords)
    .. method:: obj_by_coords(coords)

    .. method:: obj_by_id(id)

.. class:: ActionType(enum)

    You should never have to worry about this class if you use the :class:`Action` static methods.

    .. attribute:: Attack, Move

.. class:: Action

    .. method:: __init__(type, direction)

    .. staticmethod:: move(direction)
    .. staticmethod:: attack(direction)

User-defined functions
----------------------


.. function:: robot(state, unit, debug)

    The main robot function. You must define it.

    :param State state: The State instance for this battle.
    :param Obj unit: The Obj instance for this specific unit.
    :param debug: A function that accepts a string key and any value and it to the robot-specific debug table.
    :type debug: function(str, str)
    :return: An action, obtained using one of the static methods of the :class:`Action` class.

.. function:: init_turn(state)

    An optional initialization function called at the beginning of every turn. Use it to initialize global state.

    :param State state: The State instance.
    :return: Nothing.

Python Details
--------------
Stdlib source here__.

__ https://github.com/robot-rumble/logic/blob/master/langs/python/stdlib.py

Javascript Details
------------------
Stdlib source here__. Every identifier is in camelCase.

__ https://github.com/robot-rumble/logic/blob/master/langs/javascript/stdlib.js

Javascript "enums" are implemented using the method proposed by 2ality__. In a nutshell, this method leverages static class members to create a Java-like Enum superclass. In practice, the result should feel no different from the Python enums.

__ https://2ality.com/2020/01/enum-pattern.html

