# pconvert

Located here:

M:\\Agricultural Machinery Mechanics\\Instruction\\SL\\DLG\\pconvert

It is started with pconvert.exe

Due to the slow Wi-Fi connection, it is advisable to copy it to your own PC beforehand.

Here are two more points:

Licensing:

We received the following information from the DLG (www.DLG.org) DLG TestService GmbH, (<https://de.wikipedia.org/wiki/Deutsche_Landwirtschafts-Gesellschaft>)

As discussed by phone, the buyer is additionally authorized to copy the software for teaching purposes at the master craftsman school. The software may only be used at the master craftsman school and not for commercial purposes.

I would like to point out that this software is not technically protected (license key, dongle). YOU are therefore responsible for NOT making any copies of the software for commercial purposes. If you wish to use the software commercially, you must purchase a license from the DLG for €1995.



``` If you practice with the software at home after the lesson next week or in preparation, I consider that part of the learning process.

If a customer comes to you and you repair their machine with it, and you issue an invoice, that's clearly commercial.

So,

I'll describe this using the PEAK USB adapter, <https://www.peak-system.com/PCAN-USB.199.0.html>.

Other adapters are also supported:

See the list at the very end of this email.

First, we need the basic PEAK driver:

Download the device driver setup for Windows from the website mentioned above.

In the options, select at least PCAN Basic and Device Driver.

Now you should check if the PEAK is working using PCAN-View.


Other adapters are also supported:

See the list at the very end of this email. Now, install the extended driver (Pconvert works without it, but the extended driver is better if you plan to use PCAN-View or Busmaster with Pconvert later).

M:\\Landmaschinenmechanik\\Unterricht\\SL\_\*\*\\DLG\\pconvert\\progs\\can\\CANdriver\\peak\\OEM\\Redistributable

Then create a network in NetCFG32:

M:\\Landmaschinenmechanik\\Unterricht\\SL\_\*\*\\DLG\\pconvert\\progs\\can\\CANdriver\\peak

Name this network dlg_net

<img src="https://user-images.githubusercontent.com/69573151/94334981-67efa980-ffd8-11ea-9fd8-113bc6955ae7.jpeg" />

Now start pconvert:

M:\\Landmaschinenmechanik\\Unterricht\\SL\_\*\*\\DLG\\pconvert Run pconvert.exe

Then select CAN – CAN Tools – Net Client Check the box for Mode

In the window that appears, select "TRACE," and it will show what's happening on the bus:

<img src="https://user-images.githubusercontent.com/69573151/94334983-67efa980-ffd8-11ea-8ee0-ed068a57ed0d.png" />

If this doesn't work, select CAN – CAN Tools – Net Client Table – Close and Set Net Client again.

This is especially important if the network isn't named DLG_net. \*\*

<img src="https://user-images.githubusercontent.com/69573151/94334982-67efa980-ffd8-11ea-8b0f-3a9e9a771d35.png" />

<img src="https://user-images.githubusercontent.com/69573151/93021356-73a79d00-f5e2-11ea-8593-d1aa723ad8e5.png" />

<img src="https://user-images.githubusercontent.com/69573151/93021359-7e623200-f5e2-11ea-9546-445c9bef2f61.png" />

<img src="https://user-images.githubusercontent.com/69573151/93021370-891cc700-f5e2-11ea-81d8-33800bf482d4.png" />

<img src="https://user-images.githubusercontent.com/69573151/93021494-2677fb00-f5e3-11ea-8136-af3307e95905.png" />