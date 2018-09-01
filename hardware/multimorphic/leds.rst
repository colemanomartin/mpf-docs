How to configure LEDs on the PD-LED (P-ROC/P3-ROC)
==================================================

+------------------------------------------------------------------------------+
| Related Config File Sections                                                 |
+==============================================================================+
| :doc:`/config/lights`                                                        |
+------------------------------------------------------------------------------+

This guide explains how to configure MPF to use LEDs attached to a Multimorphic
PD-LED board with either a P-ROC or P3-ROC.

Note that if you're using a P-ROC/P3-ROC and you want to use serial-controlled
LEDs (NeoPixels, etc.), then you can do that with a P-ROC/P3-ROC by using a
:doc:`FadeCandy </hardware/fadecandy/index>` instead of a PD-LED. You can also
mix-and-match PD-LEDs and FadeCandy LEDs.
If you are using a PD-8x8 or a local matrix on the P-Roc see the instructions
about :doc:`Matrix lights for P/P3-Roc <leds>`.

Understanding the PD-LED board
------------------------------

The PD-LED controls up to 84 individual LED elements, which can be used to
control individual single color LEDs, or (more likely), combined into groups to
control RGB LEDs.

.. image:: /hardware/images/multimorphic_PD-LED.png

The PD-LED uses a "direct" connection method for LEDs, where each LED
has connections for each color element running back to the PD-LED. This is a
different architecture than the serial-controlled "Neo Pixel" type LEDs that
other hardware uses.

LED number:
-----------

Since the PD-LED board directly drives single color LED outputs, when you use
it with RGB LEDs, you combine three outputs into a single RGB LED. The PD-LED
supports both common cathode (common ground) and common anode (common 3.3v)
LEDs, so each LED you buy has four pins (red, green, blue, and
common). When you configure the hardware number for a PD-LED RGB LED, you
specify four parts, separated by dashes:

1. The address of the PD-LED board on the serial chain (as configured via the
   DIP switches on the PD-LED.
2. The output number of the red element.
3. The output number of the green element.
4. The output number of the blue element.

You separate those with dashes, so an example PD-LED configuration might look
like this:

.. code-block:: mpf-config

   lights:
      l_led0:
         number: 8-0-1-2
         subtype: led

The example above configures "l_led0” as the LED connected to PD-LED board at
address 8, using outputs 0, 1, and 2 as its red, green, and blue connections.

``subtype: led`` is only needed on the P-Roc since ``subtype`` defaults to ``led``
on the P3-Roc defaults. The P-Roc defaults to ``matrix``.

You might connect different color channels to your PD-LED.
For instance you might have only a red channel:

.. code-block:: mpf-config

  lights:
      my_red_only_insert:
        channels:
           red:
              - number: 8-0   # board 8 and first channel


Alternatively, you can use the same syntax as for `l_led0` above:

.. code-block:: mpf-config

  lights:
      l_led0:
        channels:
           red:
              - number: 8-0
           green:
              - number: 8-1
           blue:
              - number: 8-2


Or you can configure a white LED:

.. code-block:: mpf-config

  lights:
      my_white_light:
        channels:
           white:
              - number: 8-4

You can also configure two red channel, green plus white or any other
combination.
See :doc:`/mechs/lights/leds` for more details about how to configure channels
for different types of LEDs.

polarity
--------

The PD-LED allows you to use either common anode or common cathode LEDs. (See
the PD-LED documentation for details. The type of LED would dictate whether you
hook it up between the PD-LED’s output and ground, or between the output and
3.3v.) You can then use the config file to specify which type of LED you have,
such as:

.. code-block:: mpf-config

   lights:
      l_shoot_again:
         number: 8-60-61-62
         platform_settings:
            polarity: True

**True** = common cathode (or common ground),
**False** = common anode (or common 3.3V)

Note that DIP Switch 6 on the PD-LED board controls whether the “default” state
of the LEDs after a reset is high or low. Basically it’s whether all the LEDs
turn on or turn off when the board is reset. Which position does what is
dependent on whether you’re controlling the anode or the cathode with your
outputs, so basically if you turn on your PD-LED and all your LEDs turn on,
then flip DIP switch 6 on the PD-LED to the opposite position and power cycle
the board.

Color Correction
----------------

If you are using RGB LEDs, they might not be perfectly white when you turn
them on. They might be pinkish or blueish instead depending on the brand of
the LED. To a certain extend this is normal/expected and you can compensate
for it by configuring
:doc:`color_correction profiles in light_settings </config/light_settings>`.


Serial LEDs on the PD-LED
-------------------------

The PD-LED supports serial LEDs.
Let us know in the forum if you want to use this feature and we will
add a config option for it.

Amplifying PD-LED channels with FETs
------------------------------------

The PD-LED drives LEDs with a current of 22mA at 3.3 volts. With this current
limitation, it an only drive (at most) a few LEDs per channel. If you want to 
drive flashers, GIs or long LED strips, the PD-LED can switch a seperate 
voltage with more power through a FET (as stated in the manual). 
This is an example of such a circuit:

.. image:: /hardware/images/FET-LEDs.png

Please make sure to connect your PD-LED and the sourse pin of the FET to the same
:doc:`common ground </hardware/voltages_and_power/voltages_and_power>` or
your FET will smoke when connecting power.

