# Use of MicroPython uasyncio library

This GitHub repository consists of the following parts:
 * [Asynchronous device drivers](./DRIVERS.md). A module providing drivers for
 devices such as switches and pushbuttons.
 * [Synchronisation primitives](./PRIMITIVES.md). Provides commonly used
 synchronisation primitives plus an API for task cancellation and monitoring.
 * [A tutorial](./TUTORIAL.md) An introductory tutorial on asynchronous
 programming and the use of the uasyncio library is offered. This is a work in
 progress, not least because uasyncio is not yet complete.
 * [A driver for an IR remote control](./nec_ir/README.md) This is intended as
 an example of an asynchronous device driver. It decodes signals received from
 infra red remote controls using the popular NEC protocol.
 * [A modified uasyncio](./FASTPOLL.md) This incorporates a simple priority
 mechanism. With suitable application design this improves the rate at which
 devices can be polled and improves the accuracy of time delays. Also provides
 for low priority tasks which are only scheduled when normal tasks are paused.
 * [Communication between devices](./syncom_as/README.md) Enables MicroPython
 boards to communicate without using a UART. Primarily intended to enable a
 a Pyboard-like device to achieve bidirectional communication with an ESP8266.

# Installation of uasyncio

Firstly install the latest version of ``micropython-uasyncio``. To use queues,
also install the ``micropython-uasyncio.queues`` module. A ``Lock``
synchronisation primitive is provided by ``micropython-uasyncio.synchro``.

Instructions on installing library modules may be found [here](https://github.com/micropython/micropython-lib).

On networked hardware, upip may be run locally. The [tutorial](./TUTORIAL.md#installing-uasyncio-on-bare-metal)
has instructions for a method of installation on non-networked baremetal
targets.

# Current development state

For those familiar with asyncio under CPython 3.5, uasyncio supports the
following Python 3.5 features:

 * ``async def`` and ``await`` syntax.
 * Awaitable classes (using ``__iter__`` rather than ``__await__``).
 * Asynchronous context managers.
 * Asynchronous iterators.
 * Event loop methods ``call_soon`` and ``call_later``.
 * uasyncio ``sleep(seconds)``.

It supports millisecond level timing with the following:

 * Event loop method ``call_later_ms``
 * uasyncio ``sleep_ms(time)``

As of `uasyncio.core` V1.6 (16th Dec 2017) it supports coroutine timeouts and
cancellation. This is done via a "micro" implementation rather than by
supporting objects of type ``Future`` and ``Task``. Routines to run
concurrently are defined as coroutines instantiated with ``async def`` and
yield execution with ``await <awaitable>``.

## Asynchronous I/O

At the time of writing this was under development. Asynchronous I/O works with
devices whose drivers support streaming, such as the UART.

## Time values

For timing asyncio uses floating point values of seconds. The uasyncio ``sleep``
method accepts floats (including sub-second values) or integers. Note that in
MicroPython the use of floats implies RAM allocation which incurs a performance
penalty. The design of uasyncio enables allocation-free scheduling. In
applications where performance is an issue, integers should be used and the
millisecond level functions (with integer arguments) employed where necessary.

The ``loop.time`` method returns an integer number of milliseconds whereas
CPython returns a floating point number of seconds. ``call_at`` follows the
same convention.
