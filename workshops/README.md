# systemd.conf 2016 workshop notes #

For starters see my [blogpost](http://maciej.lasyk.info/2016/Sep/29/systemdconf2-1st-day-workshop-summary/) about 
0 - day of systemd.conf

Also keep in mind that those are just scratchbook notes.

## Demystifying systemd - Hands on by Ben Breard ##

- `systemd-analyze`
- `systemd-analyze blame`
- default target (runlevel)?
    - `systemctl get-default`
    - `systemctl isolate [target]`
- general status?
    - `systemctl`
    - `systemctl status`
    - list only loaded unit files: `systemctl -t service`
    - all loaded: `systemctl list-unit-files`
- sets enable/disable according to vendor defaults: `systemctl preset mariadb` 
- list failed units: `systemctl --failed`
- display unit dependencies: `systemctl list-dependencies httpd`
- start && enable: `systemctl enable --now mariadb`
- stop && disable: `systemctl disable --now mariadb`
- display all unit config settings including defaults (not set per se): `systemctl show mariadb`

## Using the Journal Efficiently by Lennart Poettering ##

- **journald** is awesome e.g. for security concerns. Classical syslog based logs might be spoofed easily by 
  applications introducing as different applications. In systemd/journald it's impossible - there is an actual
  authentication in systemd-units
- No more **Disk /dev/sda1 is out of space**. journald knows if there's enough disk space where it could write logs. If
  there's very low number of free space it will start to rotate old logs.
- If any application is spamming journald the rate - limiting will block it by dropping this application's messages. So
  the journald will not be lagged
- Messages filtering is done durring accepting messages (so during READ) not during write.
- Message logtime is a time of when it was received. Thanks to that we have a common timezone (which wasn't a case in
  classical syslog)
- **journalctl**:
    - what happened recently? `-e`
    - show me last 40 entries: `-n 40`
    - in revers order: `-r`
    - kernel related (like dmesg, but not 1:1): `-k`
    - since last boot: `-b`
    - only errors or worse please: `-p err`
    - from info to errors: `-p info..err`
    - actually show everything (so like systemctl): `-p debug`
    - live version like tail -f please: `-f`
    - time filtering?
        - `man systemd.timer`
        - `--since="2016-08-01"`
        - `--until="2016-09-01"`
        - `today, yesterday, tomorrow, -1week, -1month, -20day`
    - specify unit: `-u [unit]`
    - entries related to specifig device: `journalctl /dev/sda`
        - but actually might be safer with UUID instead of device; UUID doesn't change between reboots!
    - same as above but for binary? `journalctl /bin/firefox`
    - show detailed metadata: `-o verbose`
    - specific PID: `_PID=1`
        - could provide more than one: `_PID=1 _PID=123`
    - which source code generated this entry? `CODE_FILE=src/core/job.c`
    - like -u but differently? `_SYSTEMD_UNIT=crond.daemon.service`
    - filter by hostname: `_HOSTNAME=somehost`
    - `_UID=x GID=y`
    - add more contextual info: `-x`
        - this might not show anything; depending if application provides this kind of information
- cursors: thanks to that journald knows where it stopped pulling data from specified journal and where to continue
  you can see the cursor state in **-o verbose** on top: **[s=...some-very-long-string]**
- moving logs around between hosts, centralized logging etc: **systemd-journal-gatewayd / systemd-journal-remote**
- testing journal? use **logger**: `logger test`
    - might even specify loglevel: `logger -p error some test errmessage`