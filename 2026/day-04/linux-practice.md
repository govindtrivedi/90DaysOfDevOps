1. Process commands
ps aux | head
pgrep -a ssh
2. Service commands
systemctl status ssh
systemctl list-units --type=service --state=running | head -20

If your system uses sshd instead of ssh, use:
systemctl status sshd

3. Log commands
journalctl -u ssh -n 50 --no-pager
tail -n 50 /var/log/auth.log

On some distributions, SSH logs may be in /var/log/secure instead.

What to record

For your assignment, record the command + output for at least these 6 commands:

Type	Command
Process	ps aux | head
Process	pgrep -a ssh
Service	systemctl status ssh
Service	systemctl list-units --type=service --state=running
Log	journalctl -u ssh -n 50 --no-pager
Log	tail -n 50 /var/log/auth.log
