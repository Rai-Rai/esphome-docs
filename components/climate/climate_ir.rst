IR Remote Climate
=================

.. seo::
    :description: Controls a variety of compatible Climate devices via IR
    :image: air-conditioner-ir.svg

This climate component allows you to control compatible AC units by sending an infrared (IR)
control signal, just as the unit's handheld remote controller would.

.. figure:: images/climate-ui.png
    :align: center
    :width: 60.0%

There is a growing list of compatible units. If your unit is not listed below you should
submit a feature request (see FAQ).

+---------------------------------------+---------------------+----------------------+
| Name                                  | Platform name       |  Supports receiver   |
|                                       |                     |                      |
+=======================================+=====================+======================+
| :ref:`Arduino-HeatpumpIR<heatpumpir>` | ``heatpumpir``      |                      |
+---------------------------------------+---------------------+----------------------+
| Ballu                                 | ``ballu``           | yes                  |
+---------------------------------------+---------------------+----------------------+
| Coolix                                | ``coolix``          | yes                  |
+---------------------------------------+---------------------+----------------------+
| Daikin                                | ``daikin``          | yes                  |
+---------------------------------------+---------------------+----------------------+
| :ref:`Delonghi<delonghi_ir>`          | ``delonghi``        | yes                  |
+---------------------------------------+---------------------+----------------------+
| Fujitsu General                       | ``fujitsu_general`` | yes                  |
+---------------------------------------+---------------------+----------------------+
| Hitachi                               | ``hitachi_ac344``   | yes                  |
|                                       | ``hitachi_ac424``   |                      |
+---------------------------------------+---------------------+----------------------+
| :ref:`LG<climate_ir_lg>`              | ``climate_ir_lg``   | yes                  |
+---------------------------------------+---------------------+----------------------+
| Midea                                 | ``midea_ir``        | yes                  |
+---------------------------------------+---------------------+----------------------+
| Mitsubishi                            | ``mitsubishi``      |                      |
+---------------------------------------+---------------------+----------------------+
| TCL112, Fuego                         | ``tcl112``          | yes                  |
+---------------------------------------+---------------------+----------------------+
| :ref:`Toshiba<toshiba>`               | ``toshiba``         | yes                  |
+---------------------------------------+---------------------+----------------------+
| :ref:`Whirlpool<whirlpool>`           | ``whirlpool``       | yes                  |
+---------------------------------------+---------------------+----------------------+
| Yashima                               | ``yashima``         |                      |
+---------------------------------------+---------------------+----------------------+

This component requires that you have configured a :doc:`/components/remote_transmitter`.

Due to the unidirectional nature of IR remote controllers, this component cannot determine the
actual state of the device and will assume the state of the device is the latest state requested.

However, when receiver is supported, you can optionally add a :doc:`/components/remote_receiver`
component so the climate state will be tracked when it is operated with the original remote
controller unit.

.. code-block:: yaml

    # Example configuration entry
    remote_transmitter:
      pin: GPIO32
      carrier_duty_percent: 50%

    climate:
      - platform: coolix       # adjust to match your AC unit!
        name: "Living Room AC"

Configuration Variables:
------------------------

- **name** (**Required**, string): The name for the climate device.
- **sensor** (*Optional*, :ref:`config-id`): The sensor that is used to measure the ambient
  temperature. This is only for reporting the current temperature in the frontend.
- **supports_cool** (*Optional*, boolean): Enables setting cooling mode for this climate device. Defaults to ``true``.
- **supports_heat** (*Optional*, boolean): Enables setting heating mode for this climate device. Defaults to ``true``.
- **receiver_id** (*Optional*, :ref:`config-id`): The id of the remote_receiver if this platform supports
  receiver. see: :ref:`ir-receiver_id`.
- All other options from :ref:`Climate <config-climate>`.

Advanced Options
----------------

- **id** (*Optional*, :ref:`config-id`): Manually specify the ID used for code generation.
- **transmitter_id** (*Optional*, :ref:`config-id`): Manually specify the ID of the remote transmitter.

.. _heatpumpir:

Arduino-HeatpumpIR
------------------

The ``heatpumpir`` platform supports dozens of manufacturers and hundreds of AC units by utilising the `Arduino-HeatpumpIR library <https://github.com/ToniA/arduino-heatpumpir>`__.

This platform should only be used if your AC unit is not supported by any of the other (native) platforms. No support can be provided for Arduino-HeatpumpIR, because it is a third party library.

This platform utilises the library's generic one-size-fits-all API, which might not line up perfectly with all of the supported AC units. For example, some AC units have more fan speed options than what the generic API supports.

Additional configuration must be specified for this platform:

- **protocol** (**Required**, string): Choose one of Arduino-HeatpumpIR's supported protcols: ``aux``, ``ballu``, ``carrier_mca``, ``carrier_nqv``, ``daikin_arc417``, ``daikin_arc480``, ``daikin``, ``electroluxyal``, ``fuego``, ``fujitsu_awyz``, ``gree``, ``greeya``, ``greeyac``, ``greeyan``, ``hisense_aud``, ``hitachi``, ``hyundai``, ``ivt``, ``midea``, ``mitsubishi_fa``, ``mitsubishi_fd``, ``mitsubishi_fe``, ``mitsubishi_heavy_fdtc``, ``mitsubishi_heavy_zj``, ``mitsubishi_heavy_zm``, ``mitsubishi_heavy_zmp``, ``mitsubishi_heavy_kj``, ``mitsubishi_msc``, ``mitsubishi_msy``, ``mitsubishi_sez``, ``panasonic_ckp``, ``panasonic_dke``, ``panasonic_jke``, ``panasonic_lke``, ``panasonic_nke``, ``samsung_aqv``, ``samsung_fjm``, ``sharp``, ``toshiba_daiseikai``, ``toshiba``
- **horizontal_default** (**Required**, string): What to default to when the AC unit's horizontal direction is *not* set to swing. Options are: ``left``, ``mleft``, ``middle``, ``mright``, ``right``, ``auto``
- **vertical_default** (**Required**, string): What to default to when the AC unit's vertical direction is *not* set to swing. Options are: ``down``, ``mdown``, ``middle``, ``mup``, ``up``, ``auto``
- **max_temperature** (**Required**, float): The maximum temperature that the AC unit supports being set to.
- **min_temperature** (**Required**, float): The minimum temperature that the AC unit supports being set to.
- **sensor** (*Optional*, :ref:`config-id`): The sensor that is used to measure the ambient temperature.

.. note::

    - The ``greeyac`` protocol supports a feature Gree calls "I-Feel". The handheld remote control
      has a built-in temperature sensor and it will periodically transmit the temperature from this sensor to the
      AC unit. If a ``sensor`` is provided in the configuration with this model, the sensor's temperature will be
      transmitted to the ``greeyac`` device in the same manner as the original remote controller. How often the
      temperature is transmitted is determined by the ``update_interval`` assigned to the ``sensor``. Note that
      ``update_interval`` must be less than 10 minutes or the ``greeyac`` device will revert to using its own
      internal temperature sensor; a value of 2 minutes seems to work well. See :doc:`/components/sensor/index`
      for more information.

.. _ir-receiver_id:

Using a Receiver
----------------

.. note::

    This is only supported with select climate devices, see "Supports receiver" in the table at the top of the page.

Optionally, some platforms can listen to data the climate device sends over infrared to update their state (
for example what mode the device is in). By setting up a :doc:`remote_receiver </components/remote_receiver>`
and passing its ID to the climate platform you can enable this mode.

When using a receiver it is recommended to put the IR receiver as close as possible to the equipment's
IR receiver.

.. code-block:: yaml

    # Example configuration entry
    remote_receiver:
      id: rcvr
      pin:
        number: GPIO14
        inverted: true
        mode:
          input: true
          pullup: true
      # high 55% tolerance is recommended for some remote control units
      tolerance: 55%

    climate:
      - platform: coolix
        name: "Living Room AC"
        receiver_id: rcvr

.. _midea_ir:

``midea_ir`` Climate
-------------------------

These air conditioners support two protocols: Midea and Coolix. Therefore, when using an IR receiver, it considers both protocols and publishes the received states.

Additional configuration is available for this platform


Configuration variables:

- **use_fahrenheit** (*Optional*, boolean): Allows you to transfer the temperature to the air conditioner in degrees Fahrenheit. The air conditioner display also shows the temperature in Fahrenheit. Defaults to ``false``.

.. code-block:: yaml

    # Example configuration entry
    climate:
      - platform: midea_ir
        name: "AC"
        sensor: room_temperature
        use_fahrenheit: true

.. note::

    - See :ref:`Toshiba<toshiba>` below if you are looking for compatibility with Midea model MAP14HS1TBL or similar.


.. _climate_ir_lg:

``climate_ir_lg`` Climate
-------------------------

Additional configuration is available for this platform


Configuration variables:

- **header_high** (*Optional*, :ref:`config-time`): time for the high part of the header for the LG protocol. Defaults to ``8000us``
- **header_low** (*Optional*, :ref:`config-time`): time for the low part of the header for the LG protocol. Defaults to ``4000us``
- **bit_high** (*Optional*, :ref:`config-time`): time for the high part of any bit in the LG protocol. Defaults to ``600us``
- **bit_one_low** (*Optional*, :ref:`config-time`): time for the low part of a '1' bit in the LG protocol. Defaults to ``1600us``
- **bit_zero_low** (*Optional*, :ref:`config-time`): time for the low part of a '0' bit in the LG protocol. Defaults to ``550us``

.. code-block:: yaml

    # Example configuration entry
    climate:
      - platform: climate_ir_lg
        name: "AC"
        sensor: room_temperature
        header_high: 3265us # AC Units from LG in Brazil, for example use these timings
        header_low: 9856us

.. _delonghi_ir:

``delonghi`` Climate
-------------------------

Currently supports the protocol used by some Delonghi portable units

Known working with:

- Delonghi PAC WE 120HP


.. _toshiba:

``toshiba`` Climate
-------------------

Additional configuration is available for this model.


Configuration variables:

- **model** (*Optional*, string): There are two valid models

  - ``GENERIC``: Temperature range is from 17 to 30 (default)
  - ``RAC-PT1411HWRU-C``: Temperature range is from 16 to 30; unit displays temperature in degrees Celsius
  - ``RAC-PT1411HWRU-F``: Temperature range is from 16 to 30; unit displays temperature in degrees Fahrenheit

.. note::

    - While they are identified as separate models here, the ``RAC-PT1411HWRU-C`` and ``RAC-PT1411HWRU-C`` are
      in fact the same physical model/unit. They are separated here only because different IR codes are used
      depending on the desired unit of measurement. This only affects how temperature is displayed on the unit itself.

    - The ``RAC-PT1411HWRU`` model supports a feature Toshiba calls "Comfort Sense". The handheld remote control
      has a built-in temperature sensor and it will periodically transmit the temperature from this sensor to the
      AC unit. If a ``sensor`` is provided in the configuration with this model, the sensor's temperature will be
      transmitted to the ``RAC-PT1411HWRU`` in the same manner as the original remote controller. How often the
      temperature is transmitted is determined by the ``update_interval`` assigned to the ``sensor``. Note that
      ``update_interval`` must be less than seven minutes or the ``RAC-PT1411HWRU`` will revert to using its own
      internal temperature sensor; a value of 30 seconds seems to work well. See :doc:`/components/sensor/index`
      for more information.
    
    - This climate IR component is also known to work with Midea model MAP14HS1TBL and may work with other similar
      models, as well. (Midea acquired Toshiba's product line and re-branded it.)


.. _whirlpool:


``whirlpool`` Climate
---------------------

Additional configuration is available for this model.


Configuration variables:

- **model** (*Optional*, string): There are two valid models

  - ``DG11J1-3A``: Temperature range is from 18 to 32 (default)
  - ``DG11J1-91``: Temperature range is from 16 to 30


See Also
--------

- :doc:`/components/climate/index`
- :doc:`/components/remote_receiver`
- :doc:`/components/remote_transmitter`
- :doc:`/components/sensor/index`
- :apiref:`ballu.h <ballu/ballu.h>`,
- :apiref:`coolix.h <coolix/coolix.h>`,
  :apiref:`daikin.h <daikin/daikin.h>`
  :apiref:`fujitsu_general.h <fujitsu_general/fujitsu_general.h>`,
  :apiref:`hitachi_ac344.h <hitachi_ac344/hitachi_ac344.h>`,
  :apiref:`midea_ir.h <midea_ir/midea_ir.h>`,
  :apiref:`mitsubishi.h <mitsubishi/mitsubishi.h>`,
  :apiref:`tcl112.h <tcl112/tcl112.h>`,
  :apiref:`yashima.h <yashima/yashima.h>`
  :apiref:`whirlpool.h <whirlpool/whirlpool.h>`
  :apiref:`climate_ir_lg.h <climate_ir_lg/climate_ir_lg.h>`
- :ghedit:`Edit`
