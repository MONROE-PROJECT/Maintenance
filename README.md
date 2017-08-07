# Maintenance

## Routines (suggested)

### Actors

Each partner designates a maintenance responsible (role, not person) to perform the **monitoring**, **reporting** and **recovery** tasks for their nodes and their external partners nodes.

### Tools

  * The monroe inventory: https://monroe.celerway.no/index.php
  * The monroe scheduler: https://scheduler.monroe-system.eu
  * Maintenance SSH access: ssh://sshp-monroe.celerway.no
  * Database results monitor [TODO/IMDEA]
  * Weekly status emails sent through the maintenance mailer.

### Monitoring

We define the HW maintenance procedures when the node is not reachable differently for stationary and
mobile nodes.
Stationary Nodes: When a stationary node is NOT online for 24 hours:

  * Power cycle the node by sending an SMS through the GSM socket
  * If the node is still not online after 36 hours, manual intervention is required.

Mobile Nodes: When a mobile node is NOT online for 3 days:

  * Check if bus/train/truck is still operating
  * If bus/train/truck is still operating, manual intervention is required.

It is the responsibility of the maintenance responsible to check *weekly*:

  * The status of the node in the maintenance mailer. If an issue is reported, refer to
    * The status of the node in the monroe inventory (see above)
    * The status of the node in the scheduler dashboard (https://scheduler.monroe-system.eu/Resources.html)
  * TODO: The status of the node in the database results monitor [IMDEA]

Action is required for the following issues:

  * No interfaces / not all interfaces live
  * Last seen > 6h., especially if the last seen timestamp is gerater than 24 hours/3 days.
  * In maintenance
  * Last heartbeat > 6h

### Reporting

  * In each maintenance case, the corresponding ticket in the issue list should be (re)opened and commented on.
  * If the node or mifi is not recovered by the defined actions, the issue should be assigned to a developer, or the monroe-dev mailing list notified.

### Recovery

  * If the reported node issue is **last seen > ?h**, the following actions should be taken:
    * power cycle the node leaving it unpowered for 2-5 minutes, via cable or text message. (3 attempts)
    * if a power cycle is not possible, but the nodes head/tail counterpart is online, log in via ssh to 
      * into head node: 172.16.253.1 or 172.16.254.1
      * into tail node: 172.16.253.2 or 172.16.254.2
    * physical analysis - is it powered? does it react on text messages by powering off?
    * BIOS check via serial cable (if possible) - report on the BIOS output after boot.
    * Exchange hardware.

  * If the reported node issue is **Not all interfaces live**, the following actions may be taken, in addition to reporting the issue in a maintenance ticket:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
    * report the output of the following commands, if suspicious
      * *cat /var/log/biteback.log* - system self-test should report all successful tests, or errors
      * *lsusb* - should show all installed modems.
      * *modems* - will indicate which modems are not working and why.
      * *tail -f /var/log/network-listener.log* - should not show any relevant [ERROR]s.
    * If indicated, the SIM or hardware may need to be exchanged, or the node power cycled (e.g. Sierra Wireless in mode 9071 in lsusb).

  * If the reported node issue is **In maintenance**, the following should may be taken:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
    * investigate the output of the following commands:
      * *cat /var/log/biteback.log* - system self-test should report 25/25 successful tests, or errors
      * *cat /monroe/maintenance/reasons* - shows the last reasons to go into maintenance mode.
    * If the node does not show any errors in the self-test, the file /monroe/maintenance/reasons may be removed, but the event should be reported in a maintenance ticket.
    * If a node is in maintenance mode for reasons not detectable by the self-test, please set write protection on the file *chattr +i /monroe/maintenance/enabled

  * If the reported node issue is **Last heartbeat > 6h**, the following actions should be taken, in addition to reporting the issue in a maintenance ticket:
    * login via SSH maintenance access (a node that does not reply on SSH should be treated as fully gray)
      * *cat /var/log/biteback.log* - system self-test should report 25/25 successful tests, or errors

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
