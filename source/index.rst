Welcome to the RR docs!
========================================

.. toctree::
   :hidden:

   quickstart
   api
   rumblebot
   technical-details

In Robot Rumble, you develop robots that fight in one-vs-one arena battles. We currently support Python and Javascript, but more languages are planned. A very basic robot program looks like this:

.. code-block:: python

  def _robot(state, unit, debug):
      if state.turn % 2 == 0:
          return move(Direction.East)
      else:
          return attack(Direction.South)

You code a program for one robot, and then, every turn, that program is run for every robot on your team. Global state is allowed, so how you make your robots work together is entirely up to you.

Robots fight in a 19x19 circular arena. In the picture below, dark squares are the wall, and green squares are spawn points. Every **10** turns, a batch of **5** robots randomly spawns for each team.

.. image:: _static/arena.png

Every turn, a robot can either **move** or **attack** in one of four directions.

.. image:: _static/grid.png

Every robot starts out with **5 HP**, and attacks do **1 damage** (even in the case of friendly fire!). If a robot moves into a wall, into another robot, or into the same location as another robot, it will pass. There is no collateral damage in this game.

If you have more robots in the arena after **100** turns, you win!


 * Jump into the game by :doc:`creating your first robot <quickstart>`
 * Browse the :doc:`API <api>`
 * Learn about using the :doc:`CLI tool <rumblebot>`
 * Geek out over the :doc:`technical details <technical-details>`
