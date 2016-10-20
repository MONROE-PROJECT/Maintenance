# Maintenance

## Routines (suggested)
*italic text in [brackets] are deadlines and numbers to be adjusted*

### Actors

Each partner designates a maintenance responsible (role, not person) to perform the **monitoring**, **reporting** and **recovery** tasks for their nodes and their external partners nodes.

### Tools
  
  * The monroe inventory: https://monroe.celerway.no/index.php
  * The monroe scheduler: https://scheduler.monroe-system.eu
  * Maintenance SSH access [TODO/Celerway]
  * Database results monitor [TODO/IMDEA]

### Monitoring 

We define the HW maintenance procedures when the node is not reachable differently for stationary and
mobile nodes.
Stationary Nodes: When a stationary node is NOT online for 24 hours:

  * Power cycle the node by sending an SMS through the GSM socket
  * If the node is still not online after 36 hours, manual intervention is required.

Mobile Nodes: When a mobile node is NOT online for 3 days:

  * Check if bus/train/truck is still operating
  * If bus/train/truck is still operating, manual intervention is required.

It is the responsibility of the maintenance responsible to check *[daily]*:

  * The status of the node in the monroe inventory (see above)

  * The status of the node in the scheduler dashboard (REST https://scheduler.monroe-system.eu/v1/backend/activity)
    * If the node is in the list "maintenance", action is required.
    * If the node is in the list "dropouts 30d-1d", action is required.
  * TODO: The status of the node in the database results monitor [IMDEA]
  
### Reporting

  * In each maintenance case, the corresponding ticket in the issue list should be (re)opened and commented on. 
  * If the node or mifi is not recovered by the defined actions, the issue should be assigned to a developer, or the monroe-dev mailing list notified.  
  
### Recovery

  * If the node is **fully gray** in the inventory, the following actions should be taken:
    * power cycle the node leaving it unpowered for 2-5 minutes, via cable or text message. (3 attempts)
    * physical analysis - is it powered? does it react on text messages by powering off?
    * BIOS check via serial cable (if possible) - report on the BIOS output after boot.
    * Exchange hardware. 
    
  * If individual modems are **fully gray or continuously red** in the inventory, the following actions may be taken, in addition to reporting the issue in a maintenance ticket:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
    * report the output of the following commands, if suspicious
      * *cat /var/log/biteback.log* - system self-test should report 25/25 successful tests, or errors
      * *lsusb* - should show three (two for Norway) ZTE devices
      * *curl -s http://localhost:88/modems | jq .* - will indicate which modems are not working and why.
      * *mf910-info usb0* (or relevant interface) - shows all internal variables, if the modem is communicative. 
      * *dmesg|grep usb* - should not show any usb errors (-32, -72, etc.)
      * *tail -f /var/log/network-listener.log* - should not show any relevant [ERROR]s. 

  * If the node is in **maintenance mode** in the scheduler, the following should may be taken:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
    * investigate the output of the following commands:
      * *cat /var/log/biteback.log* - system self-test should report 25/25 successful tests, or errors
      * *cat /tmp/maintenance.reasons* - shows the last reasons to go into maintenance mode. 
    * If the node does not show any errors in the self-test, the file /.maintenance may be removed, but the event should be reported in a maintenance ticket.
    * If a node is in maintenance mode for reasons not detectable by the self-test, please set write protection on the file *chattr +i /.maintenance*
    
  * If the node is a **dropout** in the scheduler, the following actions should be taken, in addition to reporting the issue in a maintenance ticket:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
      * *cat /var/log/biteback.log* - system self-test should report 25/25 successful tests, or errors
      * *systemctl restart marvind* - restarts the scheduling client (currently not available, to be automated)

## Replacements

Nodes and components that are not recovered by software and power cycling actions, need to be replaced.

If the node cannot be recovered for a certain period of time, manual intervention is
required. Our approach to manual intervention is to replace the node (with the test nodes). To this end, the
representative will contact the host to arrange the replacement of the node. Once the node is in the lab, the
engineering team will perform the troubleshooting and identify the components that failed the most. We
will then buy backup parts for these components.

For the mobile nodes, since the installation requires more effort, before the replacement of the node, the
engineer on the site will access to the node over the management eth cable and will try to see whether there
is a SW issue that can be solved on site.
