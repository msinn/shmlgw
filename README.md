# Bang & Olufsen Masterlink Gateway #

This plugin can send commands to all Bang & Olufsen audio- and video systems which are connected to a Masterlink Gateway. Supported commands are the commands, a B&O remote can produce.

This plugin can receive telegrams, which are send by an B&O audio- or video system. These commands are the **LIGHT** and **CONTROL** commands, which originate from a B&O remote control (e.g. Beo4).


# Requirements

This plugin need a Bang & Olufsen Masterlink Gateway and can connect to it via TCPIP. Connecting via RS232 are not supported.

## Supported Hardware

* B&O Masterlink Gateway v2 with firmware v2.24a or later
* B&O Beolink Gateway with firmware v1.1.0 or later


# Configuration

## plugin.conf

<pre>
[mlgw]
    class_name = mlgw
    class_path = plugins.mlgw
#    host = 'mlgw.local'
#    port = 9000
#    username = mlgw
#    password = mlgw
#    rooms = []
#    Mlns = []
#    log_telegrams = 1        # parameter not implemented yet
</pre>

This plugins is looking for a masterlink gateway. By default it tries to connect to the host 'mlgw.local' on port 9000. You could change this in your plugin.conf.

If the masterlink gateway requires a login, you can specify username and password.

To make logging output more readable, you can specify a list of rooms and MLNs. 

With **log_telegrams** you can control if decoded mlgw telegrams should be logged in the smarthome.log fie.

	- 0 no telegrams are written to the log
	- 1 received telegrams that are not handled by the plugin are logged
	- 2 received telegrams are logged
	- 3 sent and received telegrams are logged
	- 4 send and received telegrams are logged, including keep alive traffic


## items.conf

The following attributes are used to **send commands** to a B&O device:

### mlgw_send
**mlgw_send** has to be specified to send commands to a B&O device. To send a b&o command (like a beo4 key-press), you have to set **mlgw_send** = *cmd*. Alternatively you could set **mlgw_send** = *ch*. In this case, you send aprogram/channel number to the B&O device.

When setting **mlgw_send** = *cmd*, you have two options for the datatype. You could set **type** = *str*. In this case the name of the command has to be passed to the item (e.g. 'DVD'). 

As second option you could set **type** = *bool*. This way you have to specify the command to send with **mlgw_cmd**. (e.g.: **mlgw_cmd** = *'DVD'*). In this case you have to pass *True* to the item to send the preconfigured command.

**enforce_updates** = *true* has to be set in conjunction with **mlgw_send**. Otherwise the command will be send only the first time.

### mlgw_cmd
**mlgw_cmd** has to be specified, if you set **mlgw_send** = *cmd* and define the item's datatype as *bool*. In conjunction with **mlgw_send**, the attribute **mlgw_cmd** specifies the command to send (e.g.: **mlgw_cmd** = *'DVD'*). 


### mlgw_mln
**mlgw_mln** specifies the destination (B&O device) to which the command is being sent. The *Masterlink Node* (MLN) numbers of the B&O devices have been specified in the Masterlink Gateway configuration.

---

The following attributes are used to **receive triggers** from a B&O device:

### mlgw_listen
... 

### mlgw_room
... 

### mlgw_cmd
... 

### Example

Please provide an item configuration with every attribute and usefull settings.

<pre>
# items/my.conf
        
    [Someroom]
    
        [[bv10]]
            name = BeoVision 10
            type = str
            enforce_updates = true
            mlgw_send = cmd
            mlgw_mln = 3
        
            [[[channel]]]
                name = BeoVision 10: Channel
                type = num
                enforce_updates = true
                mlgw_send = ch
                mlgw_mln = 3
                
            [[[digit_1]]]
                name = BeoVision 10: Digit "1"
                type = bool
                enforce_updates = true
                mlgw_send = cmd
                mlgw_mln = 3
                mlgw_cmd = 'Digit-1'
                
            [[living_light0]]
                name = living room: Light "0"
                type = bool
                mlgw_listen = light
                mlgw_room = 6
                mlgw_cmd = 'Digit-0'
                
            [[[living_control0]]]
                name = living room: Control "0"
                type = bool
                mlgw_listen = control
                mlgw_room = 6
                mlgw_cmd = 'Digit-0'

</pre>

## logic.conf
If your plugin support item triggers as well, please describe the attributes like the item attributes.


# Methodes
If your plugin provides methods for logics. List and describe them here...

## method1(param1, param2)
This method enables the logic to send param1 and param2 to the device. You could call it with `sh.my.method1('String', 2)`.

## method2()
This method does nothing.
