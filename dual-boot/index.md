# Dual Boot Tips

# Tips About Dual Boot

If you are using MBR for dual boot...

When you are installing the Linux sys, git the make a partition with a size of 1 or 2 GB to mount /boot, where saves several kernels and the booting info. This action ensures the booting of your Linux don't affect that of your Windows sys.

After you install the  Linux sys, in my case, the Pop OS, don't forget to install os-prober
`sudo os-prober`
`sudo update-grub`

