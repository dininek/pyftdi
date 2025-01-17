========
 PyFtdi
========

Overview
~~~~~~~~

PyFtdi aims at providing a user-space driver for modern FTDI_ devices,
implemented in pure Python language.

Modern FTDI_ devices include:

* FT232R (single port, clock up to 6 MHz, 3Mbps)
* FT2232D (dual port, clock up to 6 MHz)
* FT232H (single port, clock up to 30 MHz)
* FT2232H (dual port, clock up to 30 MHz)
* FT4232H (quad port, clock up to 30 MHz)
* FT230X (single port, clock up to 48 Mhz, 3Mbps)

Other FTDI_ devices could also be supported (including FT232* devices),
although these devices are not a primary goal for PyFtdi, and therefore have
not been tested with PyFtdi.

Primary goals
~~~~~~~~~~~~~

It should support the following modes:

* UART/Serial USB converter, up to 12Mbps (depending on the FTDI device
  capability)
* SPI master
* JTAG master
* Bitbang/GPIO support (not a primary goal)

PyFtdi should provide a pyserial_ compliant API, to be used as a drop-in module
to access USB-serial converters based on FTDI_ devices.

.. _FTDI: http://www.ftdichip.com/
.. _pyserial: http://pyserial.sourceforge.net/


Requirements
~~~~~~~~~~~~

PyFtdi relies on PyUSB_, which itself depends on one of the following native
libraries:

* libusb-1.0 (recommended), tested with 1.0.20
* libusbx-1.0, tested with 1.0.17
* libusb-0.1 (deprecated), tested with 0.1.4
* openusb (not tested with pyftdi)

PyFtdi does not depend on any other native library, and only uses standard
Python modules.

PyFTDI has been tested with PyUSB_ 1.0.0b1 and PyUSB_ 1.0.0b2. PyFTDI deals
with the API break introduced with PyUSB_ 1.0.0b2.

To use the serial port feature of PyFtdi, pyserial_ 2.6+ module should be
installed. Previous versions of pyserial_ will NOT work.

Python_ 2.6 or above is required. Python_ 3.x is not yet supported.

.. _PyUSB: http://sourceforge.net/projects/pyusb/
.. _Python: http://python.org/


Status
~~~~~~

This project is still in beta development stage.

However, PyFtdi is being forked from a closed-source software implementation
that has been successfully used for over a year - including serial, spi and
jtag protocols, based on top of the libftdi_ open source library.

libftdi_ is now being phased out from this closed-source project and replaced
with PyFtdi, to ease maintenance and customization.

Meanwhile, PyFtdi is developed as an open-source solution.

Supported features
------------------

* All FTDI device ports (UART, MPSSE) can be used simultaneously.

* Serial port, up to 12 Mbps. PyFtdi includes a pyserial_ emulation layer that
  offers transparent access to the FTDI serial ports through a pyserial_-
  compliant API. The ``serialext`` directory contains a minimal serial terminal
  demonstrating the use of this extension, and a dispatcher automatically
  selecting the serial backend (pyserial_, PyFtdi), based on the serial port
  name.

* SPI master. For now, SPI Mode 0 (CPOL=0, CPHA=0) is the only supported
  mode. It should be easy to extend the SPI master to deal with less common
  modes. PyFtdi can be used with pyspiflash_ module that demonstrates how to 
  use the FTDI SPI master with a pure-Python serial flash device driver for 
  several common devices.

* JTAG is under development and is not fully supported yet.

.. _libftdi: http://www.intra2net.com/en/developer/libftdi/
.. _pyspiflash: https://github.com/eblot/pyspiflash

Installation
~~~~~~~~~~~~
* Download & install pyusb-1.0.0b1: ``pip install [--pre] pyusb==1.0.0b2``

  * ``--pre`` tag is required with latest release of pip, as pyusb is still
    in beta status

* Download & install pyserial: ``pip install pyserial``

* Install pyftdi

Troubleshooting
---------------

*"Error: No backend available"*
  libusb native library cannot be loaded. Try helping the dynamic loader:

  * On Linux: ``export LD_LIBRARY_PATH=<path>``

    where <path> is the directory containing the ``libusb-1.*.so`` library file

  * On OS X: ``export DYLD_LIBRARY_PATH=.../lib``

    where <path> is the directory containing the ``libusb-1.*.dylib`` library
    file

*"Error: Access denied (insufficient permissions)"*
  The system may already be using the device.

  * On OS X 10.9+: starting with Mavericks, OS X ships with a native FTDI
    driver that preempts access to the FTDI device.

    The driver can be unloaded this way:

      ``sudo kextunload [-v] -bundle com.apple.driver.AppleUSBFTDI``

    Please note that the system automatically reloads the driver, so it may be
    useful to move the kernel extension so that the system never loads it.

  * This error message may also be triggered whenever the communication port is
    already in use.

*"serial.serialutil.SerialException: Unable to open USB port"*
  May be caused by a conflict with the FTDI virtual COM port (VCOM). Try
  uninstalling the driver. On OS X, refer to this FTDI guide_:

.. _guide: http://www.ftdichip.com/Support/Documents/AppNotes/AN_134_FTDI_Drivers_Installation_Guide_for_MAC_OSX.pdf

Development
~~~~~~~~~~~

PyFtdi is developed on Mac OS X platforms (including 64-bit kernels), and is
validated on a regular basis on Linux hosts.

As it contains no native code, it should work on any PyUSB_ and libusb_
supported platforms. However, Ms Windows is a seamless source of issues and is
not supported. Your mileage may vary.

.. _libusb: http://www.libusb.org/

Examples
~~~~~~~~

See pyftdi/examples directory for JTAG examples.
See pyspiflash_ module for SPI examples.

.. include:: serialext/README.rst
