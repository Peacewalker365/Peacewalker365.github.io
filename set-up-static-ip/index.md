# Static IP Setup

#Solve the Prob: No Internet After Static IP Setup

The reason is that we didn't manually set up the DNS.
So you may Ping 8.8.8.8 successfully, but not a regular website.

`sudo nano /etc/resolvconf/resolv.conf.d/head`

Write `nameserver x.x.x.x y.y.y.y z.z.z.z`

Then reboot.

This should solve your problem.

