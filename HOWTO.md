# How to replicate our setup?

**Step 1. Buy a drone and assemble it.**

In our case, it’s this particular [unit](https://www.amazon.de/HAWKS-WORK-Transmitter-Accessories-Instructions/dp/B0GG8P2651/ref=mp_s_a_1_2?crid=27SPGYD5JRCPM&dib=eyJ2IjoiMSJ9.TcycDxHe43l5tKQPnIWq_yQOab3UCwww4RKjO6JZfOZ6v-1MyLgsypJN8YjbuE0dfEoqaaeRMITKdFKxnyX6Qd6PHnQa0DfUzWHuDh5cheF1WMCujKsznzqee5qnXlqX6uOpWfwMSEVfF3c5XkoXZON1HP1Iaq_0ECrohFGEFcg2gOXFbNETl7f4pf2z20b3.ZBHQSoA0QYPlXzEhLteCmG-Jl9GwsiA01pPk7cERosg&dib_tag=se&keywords=hawk+s+work+f450+kit&qid=1778867420&sprefix=hawk+wor%2Caps%2C171&sr=8-2).

You can purchase other components at your own discretion. The main thing is that the flight controller must be compatible with PX4 firmware. Please note that we haven't tested other components for compatibility, but everything should work. Keep in mind that the firmware version and configuration parameters may differ from ours, depending on the specific components you choose.

**Step 2. Configure the flight controller.**

First, install [QGroundControl](https://qgroundcontrol.com/). Then, install the [firmware](https://github.com/arkadii888/Finch/blob/main/DroneSetup/px4_fmu-v3_default.px4) and upload the [parameters](https://github.com/arkadii888/Finch/blob/main/DroneSetup/onboard.params).

Important note: It is best to reconfigure the sensors in the area where you plan to fly. Also, make sure that the GPS cable does not touch the body of the flight controller. Wrap the cable around the mast on which the GPS is mounted.

**Step 3. Connect and attach the Raspberry Pi to the frame.**

In our case, it is a Raspberry Pi 5 with 8GB RAM. We leave the method of attaching the Raspberry Pi to the drone frame up to you. The main thing is to take the cable from the kit that fits the Telem2 port on the flight controller, which has 6 bare wires on the other end. Connect one end to Telem2, and the other ends to the pins on the Raspberry Pi: black to pin 6, blue to pin 8, and white to pin 10. Double-check that Telem2 is enabled in the flight controller parameters.

Regarding power, we initially used a power bank, but it made the drone too heavy. Because of this, we connected the drone's main battery to both the drone itself and the Raspberry Pi using [this](https://www.amazon.de/-/en/gp/aw/d/B0F9FLF6QB?psc=1&ref=ppx_pop_mob_b_asin_title) adapter. This requires some soldering (although it is not complicated), so we will leave this decision up to you. 

**Step 4. Configuring the Raspberry Pi.**

First, let's make the Raspberry Pi act as a Wi-Fi access point on startup so that we can connect to it.

```
sudo nmcli con add type wifi ifname wlan0 mode ap con-name FinchAP ssid Finch
sudo nmcli con modify FinchAP wifi-sec.key-mgmt wpa-psk
sudo nmcli con modify FinchAP wifi-sec.psk "12345678"
sudo nmcli con modify FinchAP ipv4.method shared ipv4.addresses 192.168.4.1/24
sudo nmcli con modify FinchAP connection.autoconnect yes
sudo nmcli con up FinchAP
```

Also, let's set up MAVProxy to get our Telem2 connection working. MAVProxy will route the telemetry into two streams: one for Executor, and another to allow you to use QGroundControl wirelessly by connecting to the access point. Make sure that the baudrate in the Telem2 parameter matches the value below.

```
sudo pip3 install mavproxy
```

```
sudo nano /etc/systemd/system/mavproxy.service
```

```
[Unit]
Description=MAVProxy Telemetry Router
After=network-online.target

[Service]
Type=simple
User=root
WorkingDirectory=/tmp
ExecStartPre=/bin/sleep 15
ExecStart=/usr/local/bin/mavproxy.py --master=/dev/ttyAMA0 --baudrate=921600 --out=udp:127.0.0.1:14540 --out=udpbcast:192.168.4.255:14550 --daemon
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl daemon-reload
sudo systemctl enable mavproxy.service
sudo systemctl start mavproxy.service
```

Reboot the system.

**Step 5. Install our software stack on the Raspberry Pi.**

Install and run [Executor](https://github.com/arkadii888/Executor).

Install and run [Agent](https://github.com/arkadii888/Agent).

**Step 6. Configure the ground base.**

Install and run [GroundBase](https://github.com/arkadii888/GroundBase). Connect to the Raspberry Pi's Wi-Fi access point from your computer.  

You can use any implementation in accordance with the Executor requirements.

**Step 6. Try the system.**

Please note: For safety, test everything at home first with the propellers removed. Reset all flight controller settings to default, reboot, switch it to SIH (Simulation In Hardware) mode, and enable Telem2. You may need to restart Executor and Agent. You will be able to monitor the flight directly in QGroundControl. For field tests, you can reset all settings to default again, reboot, and upload the parameters from Step 2.

Also, keep in mind that the actual flight must be conducted in accordance with your local regulations.

Try sending a prompt, for example: ```take off to 10m, fly north 20m, return to the starting point.```
