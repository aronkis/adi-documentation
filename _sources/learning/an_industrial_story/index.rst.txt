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
- Zeprhy: :git-zephyr:`ZEPHYR_PROJECT_NAME <ZEPHYR_PROJECT_NAME:>`
- NO-OS: :git-noos:`NO_OS_PROJECT_NAME <NO_OS_PROJECT_NAME:>`
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
and NO-OS. The demo showcases the seamless connectivity and data flow between 
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
runs Zephyr RTOS, while the :adi:`EVAL-ADIN1110 <eval-adin1110>` runs NO-OS. 
Both of the boards have an :adi:`EVAL-CN0391-ARDZ <cn0391>` connected to them, 
which is used to read the temperature using 4 thermocouples each.
The system can be interchaned, by simply compiling the Zephyr application for the 
:adi:`EVAL-ADIN1110 <eval-adin1110>` and the NO-OS application for the
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


Results
--------------------------------------------------------------------------------

.. figure:: scopy-result-placeholder.png
   :align: center

   Results on the AD-APARD32690-SL using Zephyr RTOS

.. TODO::
    - Build steps for both systems
    - Scopy results