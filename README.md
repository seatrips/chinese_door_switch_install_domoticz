# chinese_door_switch_install_domoticz
chinese doorswitch


Hi,

So I don't know if this is special or not but I found these cheap Door sensor that look great, are really cheap (<€ 5,-) and send an open and close signal. 
https://www.aliexpress.com/item/433MHz- ... 91010.html
IMG_4198.JPG
IMG_4198.JPG (32.29 KiB) Viewed 13628 times

I thought I would share how to set them up in Domoticz.

So first this sensor has to be picked up by Domoticz. If you have an RFXCOM you need to enable the Lightning 4 mode.
As soon as this is enabled your sensor will show up in your list of (unused) devices as 2 devices with the same ID.
One device will be triggered when the door is opened, one will be triggered when the door is closed.

You need to find out which device is triggered when. You can find out by triggering each event. First move the contact away from the base, this will simulate opening the door. The "last seen" time of that device will change. 
Rename that device. Let's call it "Front door open (event)".
Now move the contact back to the base. If you did it correct "last seen" time of the other device will change. 
Rename that device. Let's call that "Front door close (event)".

And add them both as switches in Domoticz.
Both devices will be always on, you can't switch them off. To know the state of the door we need to add a new dummy switch.
Add a new dummy door contact and call it "Front door (Virtual)".

To know the state of the door we need to add 2 small scripts.
One when the door is opened and one when closed. 
Here they are:
Spoiler: hide
## Door open :
CODE:
```
return {
	active = true, -- set to false to disable this script
	on = {
		devices = {
			'Front door open (event)',
		},
	},
	-- in case of a timer event or security event, device == nil
	execute = function(domoticz, device)
		if (device.changed) then
			domoticz.devices('Front door (Virtual)').open()
		end
	end
}
```
## Door close :
CODE: 
```
return {
	active = true, -- set to false to disable this script
	on = {
		devices = {
			'Front door close (event)' 
		},
	},
       -- in case of a timer event or security event, device == nil
	execute = function(domoticz, device)
		if (device.changed) then
			domoticz.devices('Front door (Virtual)').close()
		end
	end
}
```
