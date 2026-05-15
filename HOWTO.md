# How to replicate our setup?

**Step 1. Buy a drone and assemble it.**

In our case, it’s this particular [unit](https://www.amazon.de/HAWKS-WORK-Transmitter-Accessories-Instructions/dp/B0GG8P2651/ref=mp_s_a_1_2?crid=27SPGYD5JRCPM&dib=eyJ2IjoiMSJ9.TcycDxHe43l5tKQPnIWq_yQOab3UCwww4RKjO6JZfOZ6v-1MyLgsypJN8YjbuE0dfEoqaaeRMITKdFKxnyX6Qd6PHnQa0DfUzWHuDh5cheF1WMCujKsznzqee5qnXlqX6uOpWfwMSEVfF3c5XkoXZON1HP1Iaq_0ECrohFGEFcg2gOXFbNETl7f4pf2z20b3.ZBHQSoA0QYPlXzEhLteCmG-Jl9GwsiA01pPk7cERosg&dib_tag=se&keywords=hawk+s+work+f450+kit&qid=1778867420&sprefix=hawk+wor%2Caps%2C171&sr=8-2).

You can purchase other components at your own discretion. The main thing is that the flight controller must be compatible with PX4 firmware. Please note that we haven't tested other components for compatibility, but everything should work. Keep in mind that the firmware version and configuration parameters may differ from ours, depending on the specific components you choose.

**Step 2. Configure the flight controller.**

First, install [QGroundControl](https://qgroundcontrol.com/). Then, install the [firmware](https://github.com/arkadii888/Finch/blob/main/DroneSetup/px4_fmu-v3_default.px4) and upload the [parameters](https://github.com/arkadii888/Finch/blob/main/DroneSetup/onboard.params).

Important note: It is best to reconfigure the sensors in the area where you plan to fly. Also, make sure that the GPS cable does not touch the body of the flight controller. Wrap the cable around the mast on which the GPS is mounted.

**Step 3. Connect and attach the Raspberry Pi to the frame.**

In our case, it is a Raspberry Pi 5 with 8GB RAM. We leave the method of attaching the Raspberry Pi to the drone frame up to you. The main thing is to take the cable from the kit that fits the Telem2 port on the flight controller, which has 6 bare wires on the other end. Connect one end to Telem2, and the other ends to the pins on the Raspberry Pi: black to pin 6, blue to pin 8, and white to pin 10. Double-check that Telem2 is enabled in the flight controller parameters.

Regarding power, we initially used a power bank, but it made the drone too heavy. Because of this, we connected the drone's main battery to both the drone itself and the Raspberry Pi. This requires some soldering (although it is not complicated), so we will leave this decision up to you.
