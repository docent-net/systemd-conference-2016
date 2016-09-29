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
