Enabling Flexible Data Acquisition Across Mixed Vendor Compute Platforms
================================================================================

ADI DataX™ unifies application portability across heterogeneous
compute environments, enabling reusable workflows for data acquisition and
processing. Developers can scale complexity as needed, from rapid prototyping to
advanced system refinement. The solution highlights modularity, consistency, and
efficiency in building multi‑vendor data pipelines. This approach significantly 
reduces integration overhead and accelerates product development.

Resources
--------------------------------------------------------------------------------
- Zephyr: :git-zephyr:`ZEPHYR_PROJECT_NAME <ZEPHYR_PROJECT_NAME:>`
- no-OS: :git-noos:`NO_OS_PROJECT_NAME <NO_OS_PROJECT_NAME:>`
- Linux: :git-linux:`LINUX_PROJECT_NAME <LINUX_PROJECT_NAME:>`
- Hardware: 
  
  - :adi:`AD-APARD32690-SL Rev. E <ad-apard32690-sl>`
  - :adi:`AD-APARDPFW-SL <ad-apardpfw-sl>`
  - :adi:`EVAL-ADIN1110 <eval-adin1110>`
  - :adi:`EVAL-CN0391-ARDZ <cn0391>`
  - :adi:`AD-RPI-T1LPSE-SL <ad-rpi-t1lpse-sl>`

Block diagram
--------------------------------------------------------------------------------

.. figure:: demo_block_diagram.svg
   :align: center


Demo description
--------------------------------------------------------------------------------
This demo illustrates the flexibility of ADI DataX™ in enabling data acquisition
across mixed vendor compute platforms. The system integrates ADI's data 
acquisition hardware with a variety of compute platforms, including Zephyr RTOS 
and no-OS. The demo showcases the seamless connectivity and data flow between 
the hardware and software components, demonstrating how developers can easily 
build and deploy data acquisition workflows across different environments. By 
leveraging ADI DataX™, developers can focus on application development without 
worrying about the complexities of integration, enabling faster time-to-market 
for their products.

In this demo setup, the Raspberry Pi, running :adi:`Kuiper 2 <kuiper>`, serves
as the central interogation unit, communicating with the ADI hardware components
to acquire data using the  :adi:`AD-RPI-T1LPSE-SL <ad-rpi-t1lpse-sl>` via T1L
connections.

The :adi:`AD-RPI-T1LPSE-SL <ad-rpi-t1lpse-sl>` powers the :adi:`AD-APARDPFW-SL <ad-apardpfw-sl>`
which powers the :adi:`AD-APARD32690-SL <ad-apard32690-sl>` and forwards power 
to the :adi:`EVAL-ADIN1110 <eval-adin1110>`.

To demonstrate the flexibility of DataX™, the :adi:`AD-APARD32690-SL <ad-apard32690-sl>`
runs Zephyr RTOS, while the :adi:`EVAL-ADIN1110 <eval-adin1110>` runs no-OS. 
Both of the boards have an :adi:`EVAL-CN0391-ARDZ <cn0391>` connected to them, 
which is used to read the temperature using 4 thermocouples each.
The system can be interchaned, by simply compiling the Zephyr application for the 
:adi:`EVAL-ADIN1110 <eval-adin1110>` and the no-OS application for the
:adi:`AD-APARD32690-SL <ad-apard32690-sl>`, demonstrating the ease of using
DataX™ in enabling data acquisition across mixed vendor compute platforms.


Required Hardware
--------------------------------------------------------------------------------

.. csv-table::
    :file: hardware_table.csv

Building steps
--------------------------------------------------------------------------------

Raspberry Pi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuring the Micro-SD Card
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Linux kernel requires a matching device tree overlay to identify the
devices on the AD-RPI-T1LPSE-SL. The overlay table is included with the
:ref:`kuiper` and simply needs to be enabled.

To do this, follow the Hardware Configuration procedure under **Configuring
the SD Card for Raspberry Pi Projects** in the :ref:`kuiper` page.
Enable the AD-RPI-T1LPSE-SL overlay by adding the following line to *config.txt*
for class 12:

::

   dtoverlay=rpi-t1lpse-class12

or for class 14:

::

   dtoverlay=rpi-t1lpse-class14

Save the changes and reboot the system by entering the following command in the
console:

.. shell::
   :user: analog
   :group: analog
   :show-user:

   $sudo reboot

Setting Up a Static IP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To set up a static IP address for the AD-RPI-T1LPSE-SL, the user has to modify
the IPv4 address of the chosen network interface. This can be done by
right-clicking in the top right corner the network icon and selecting Wireless
& Wired Network Settings.

.. figure:: ad-rpi-t1lpse-sl-static-ip-location.png
   :width: 400 px

   Network Settings Location

Next to the **interface** field select the wanted interface (e.g. **eth1 /
eth2**) and type in the chosen IP address as shown below:

.. figure:: ad-rpi-t1lpse-sl-static-ip-set-ip.png
   :width: 400 px

   Static IP Address Configuration

The next set is to reset the ip link, which can be done by entering the
following command in a terminal:

.. shell::
   :user: analog
   :group: analog
   :show-user:

   $sudo ip link set eth0 down

.. figure:: ad-rpi-t1lpse-sl-static-ip-set-eth0-down.png
   :width: 400 px

   Setting eth0 down

Next, set the interface up again by entering the following command:

.. shell::
   :user: analog
   :group: analog
   :show-user:

   $sudo ip link set eth0 up

.. figure:: ad-rpi-t1lpse-sl-static-ip-set-eth0-up.png
   :width: 400 px

   Setting eth0 up

If everything was done correctly the interface should be up and running with
the static IP address set. To verify this, enter the following command in the
console, the next to the **inet** field the static IP address should be shown:

.. shell::
   :user: analog
   :group: analog
   :show-user:

   $ip a

.. figure:: ad-rpi-t1lpse-sl-static-ip-result.png
   :width: 400 px

   Static IP Address Result


Zephyr RTOS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This project builds an Industrial I/O Daemon (iiod) with network support on the 
APARD32690 platform. It enables remote access to industrial I/O devices over the 
network using the Libiio v.1.0 library run on Zephyr RTOS.
The monitored device here is an ad7124 which exposes 4 virtual channels for 
reading the temperature from 4 different Type K thermocouples. 
The data can be visualized using Scopy, which connects to the iiod running on the 
APARD32690 board.

Setting Up the Zephyr Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Getting Libiio
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Get the latest version of Libiio into the Zephyr project by updating *west.yml*. 
Add the following lines under `remotes`:

.. code-block:: yaml

   manifest:
    - name: libiio
      url-base: https://github.com/analogdevicesinc

Add the following lines under `projects`:

.. code-block:: yaml

    - name: libiio                                                                                    
      path: modules/lib/libiio
      revision: main

Run this command to update the Zephyr project with the new manifest:

.. shell::
   :user: analog
   :group: analog
   :show-user:

   ~/zephyrproject/zephyr
   $west update

You should now have Libiio in the Zephyr project under *modules/lib/libiio*.

Build and Run
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To further emphasize the way ADI DataX™ allows software to efortlessly accomodate 
hardware changes, there are 2 possible setups for this Zephyr project. The only 
software and hardware difference between the two is the inclusion or exclusion of the 
AD-APARDPFW-SL shield from the build command and from the physical setup, as 
explained below:

 1) Both the AD-APARDPFW-SL shield and the EVAL-CN0391-ARDZ shield connected to the AD-APARD32690-SL board.
    AD-APARDPFW-SL powers the AD-APARD32690-SL and establishes the ethernet connection over SPI0.

      Build the application for this setup using the following command:

      .. shell::
         :user: analog
         :group: analog
         :show-user:

         ~/zephyrproject/zephyr   
         $west build -p always -b apard32690/max32690/m4 ../modules/lib/libiio/zephyr/samples/iiod/ -S iiod-network --shield eval_cn0391_ardz --shield ad_apardpfw_sl


 2) Only EVAL-CN0391-ARDZ shield connected to the AD-APARD32690-SL. 
    The AD-APARD32690-SL is externally powered through the USB and uses SPI4 to communicate via ethernet.

      Build the application:

      .. shell::
         :user: analog
         :group: analog
         :show-user:

         ~/zephyrproject/zephyr   
         $west build -p always -b apard32690/max32690/m4 ../modules/lib/libiio/zephyr/samples/iiod/ -S iiod-network --shield eval_cn0391_ardz

Then flash:

.. shell::
   :user: analog
   :group: analog
   :show-user:
   
   ~/zephyrproject/zephyr
   $west flash --runner openocd

.. Note::
   This flashing process requires the ADI distribution of OpenOCD to be installed. Details on how to 
   install it can be found on `ADI OpenOCD GitHub <https://github.com/analogdevicesinc/openocd>`_.

   If you have problems with the flashing process, please use ``-DOPENOCD=<path_to_your_adi_openocd>`` with the build command. 
   You should see *<path_to_your_adi_openocd>* in *./build/zephyr/runners.yaml*, under *config -> openocd*.

By connecting to the serial communication of the board (e.g.: ``minicom -D /dev/ttyACM0 -b 115200``) and resetting, the following output should be observed for each configuration:

.. list-table::
   :widths: 50 50
   :header-rows: 1

   * - Configuration 1 (with AD-APARDPFW-SL)
     - Configuration 2 (without AD-APARDPFW-SL)
   * - .. code-block::

          Hello World! apard32690/max32690/m4
          [00:00:00.176,000] <inf> phy_adin: PHY 1 ID 283BCA1
          [00:00:00.178,000] <inf> phy_adin: PHY 1 2.4V mode supported
          [00:00:00.180,000] <inf> phy_adin: PHY 2 ID 283BCA1
          [00:00:00.182,000] <inf> phy_adin: PHY 2 2.4V mode supported
          *** Booting Zephyr OS build v4.3.0-5400-g0c770e917768 ***

     - .. code-block::

          Hello World! apard32690/max32690/m4
          [00:00:00.150,000] <inf> phy_adin: PHY 1 ID 283BCA1
          [00:00:00.152,000] <inf> phy_adin: PHY 1 2.4V mode supported
          *** Booting Zephyr OS build v4.3.0-5400-g0c770e917768 ***

You are now ready to connect to the board using Scopy and start acquiring data from the thermocouples.
Open Scopy and enter the URI of the APARD32690-SL board (``ip:192.168.97.100``), then click **Verify**. 

.. figure:: scopy-zephyr-apard32690-verify.png
   :width: 400 px

   Enter URI and Verify

Click **Add Device** with both *DataLogger* and *Debugger* selected.

.. figure:: scopy-zephyr-apard32690-add-device.png
   :width: 400 px

   Add Device

Then click **Connect**.

.. figure:: scopy-zephyr-apard32690-connect.png
   :width: 400 px

   Connect to the Device

Go to the **Data Logger** tab, select the channels you want to display and then click **Start** to start acquiring data from the thermocouples.

.. figure:: scopy-zephyr-apard32690-plot.png
   :width: 400 px

   Plot Data from the Thermocouples

no-OS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Results
--------------------------------------------------------------------------------

.. figure:: scopy-result-placeholder.png
   :align: center

   Results on the AD-APARD32690-SL using Zephyr RTOS

.. TODO::
    - Build steps for no-OS
    - Setting Up the Zephyr Environment
    - Scopy connect video
    - Scopy results