# SSHD Setup

# SSHD Setup

1. Install openssh-server `sudo apt-get install openssh-server`
2. Check if the sshd service is running `ps -e | grep sshd`
3. If not, `sudo /etc/init.d/ssh start`
   - If it's for your LAN, then you may want to setup the static IP for your server
      - Check your current ip `ifconfig en0`
      - Check current gateway
        - For macOS user: `netstat -nr | grep default`
        - For Linux user: `ip route | grep default`
   - Notice that the sshd will start automatically on boot. If this is not what you want, you can remove this feature and start it manually. `sudo systemctl diable ssh`
   - For security, you may want to change the default port # 22 to some other #. `sudo vim /etc/ssh/sshd_config`
4. Now we can establish connection from the client `ssh -p port# username@ip-addr`
   - If you use the default port #, then just use `ssh username@ip-addr`
   - You can generate ssh key pair to avoid typing password on connection.
     - `ssh-keygen -t rsa` then give the path to store it
     - `ssh-copy-id -u client-username -i name.pub -p port# username@ip` copy the public key to the server, -u let the client user login without password.
     - `ssh -i private-key -p port# username@ip`
     - You can add the keys to ssh agent.
       - `ssh-add`
       - `ssh-add -L` to check the keys added.
     - After logged in, go to `.ssh/authorized_keys` and make sure we haven't added extra keys we don't want to add.
     - Now we can just use `ssh -p port# ip` to log in
     - You can also add alias for the server in ~/.ssh/config
       - `vim ~/.ssh/config`

        ```bash
        Host Alias
            HostName IP-addr
            User the username you want to log in as
            Port the port# you want to use
        ```

       - then you can just use `ssh alias` for login
5. You can use scp to upload or download files.
   - `scp local-file username@ip:path-on-server`
   - `scp -r local-dir username@ip:path-on-server`
   - `scp username@ip:path-on-server local-path`
   - `scp -r username@ip:path-on-server local-path`

### Notice

```
If you want to log in as root, the you may want to edit `/etc/ssh/sshd_config` and change `#PermitRootLogin no` to `PermitRootLogin yes`

Then `service ssh restart`

You may need to use `ssh -T username@ip` for the purpose of verification
```

