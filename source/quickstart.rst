Quickstart: creating your first robot
=====================================

Any robot program is really just a function that *accepts* the state of the game and *returns* the robot's action. Let's take another look at the default robot:

.. code-block:: python

    def _robot(state, unit, debug):
        if state.turn % 2 == 0:
            return move(Direction.East)
        else:
            return attack(Direction.South)





.. _debugging:
Debugging your robot
--------------------
