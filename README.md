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

It is the responsibility of the maintenance responsible to check *[daily]*:

  * The status of the node in the monroe inventory 
    * If the node is gray (>*[24h]* for mobile nodes), action is required 
    * If individual modems are gray, or red for more than *[24]* hours, action is required.
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
      * *biteback -f* - system self-test should report 24/24 successful tests, or errors
      * *lsusb* - should show three (two for Norway) ZTE devices
      * *curl -s http://localhost:88/modems | jq .* - will indicate which modems are not working and why.
      * *mf910-info usb0* (or relevant interface) - shows all internal variables, if the modem is communicative. 
      * *dmesg|grep usb* - should not show any usb errors (-32, -72, etc.)
      * *tail -f /var/log/network-listener.log* - should not show any relevant [ERROR]s. 

  * If the node is in **maintenance mode** in the scheduler, the following should may be taken:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
    * investigate the output of the following commands:
      * *biteback -f* - system self-test should report 24/24 successful tests, or errors
      * *cat /tmp/maintenance.reasons* - shows the last reasons to go into maintenance mode. 
    * If the node does not show any errors in the self-test, the file /.maintenance may be removed, but the event should be reported in a maintenance ticket.
    * If a node is in maintenance mode for reasons not detectable by the self-test, please set write protection on the file *chattr +i /.maintenance*
    
  * If the node is a **dropout** in the scheduler, the following actions should be taken, in addition to reporting the issue in a maintenance ticket:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
      * *systemctl restart marvind* - restarts the scheduling client 
      * *biteback -f* - system self-test should report 24/24 successful tests, or errors
