# Creating and setting up a SSH key

## 1 - Create a RSA key pair

Run the following command to generate a key pair on your machine:

```bash
ssh-keygen
```

!!! Note
    You can optionally use the command `ssh-keygen -b 4096` to use a higher security key.

You will be prompted for a location. You can use the default location (just press `enter`) or specify your own.

!!! Warning
    Make sure you don't overwrite any existing file, because you may break an existing connection if you do.

You will then be prompted to enter a passphrase. This step is optional, but recommended, especially if you share your machine with other people. If you specify a passphrase, you will be prompted for it every time you use the key to login using SSH.

You should then have two files at your specified location: `id_rsa`, which is your **private** key (do NOT share with anyone) and `id_rsa.pub`, which is your **public** key (that you will put on your server).

## 2 - Copying your key to the server

Now you need to copy your new key to your server. You will need either a password-based SSH account or physical access to the machine for this step. Here are two ways of doing this:

### Using `ssh-copy-id`

If you have `ssh-copy-id` available on your system this step is easy, just run the following command:

```bash
ssh-copy-id yourusername@yourserver
```

`yourusername` should be a password-based SSH account on your server.

If you don't have the `ssh-copy-id` utility but have ssh access to your server, you can use this one-liner to copy your key (from [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1804)): 

```bash
cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### Copying the key manually

You will need to copy the `id_rsa.pub` you have generated earlier to your server and add the contents to `~/.ssh/authorized_keys`.

First, you need to check if the .ssh directory exists in your home directory. You can create it using the following command:

```bash
mkdir -p ~/.ssh
```

!!! Tip
    The `mkdir -p` command is perfectly harmless if the directory already exists.

Then, you need to copy the contents of the id_rsa.pub file into a file called `authorized_keys`:

```bash
cat id_rsa.pub >> ~/.ssh/authorized_keys
```

Finally, you need to make sure the permissions are right for these files:

```bash
chmod -R go= ~/.ssh
chown -R yourusername:yourusername ~/.ssh
```

The first command removes the permissions for anyone other than `yourusername` and the second command ensures that `yourusername` is the owner the the `.ssh` directory and all its contents. Make sure to substitute `yourusername` with the name of the actual account on your server.

## 3 - Testing the password-less authentication

Now that everything is set-up, try to log on to your server using SSH:

```bash
ssh yourusername@yourserver
```

If your server still asks for a password (other than the private key passphrase, if using) or if it refuses the connection, please verify that you have followed all the steps above.

## 4 - Disabling password-based authentication

Once you have tested that you can log on using your key, you can increase your server security by disabling password-based authentication.

!!! Danger
    Make sure that your key is working and that it is safely stored. Disabling password-based authentication will effectively lock you out of your server should you lose that key! Always make sure that you have a contingency plan!

Once you have tested and are able to login using only the key, you will have to edit the SSHd configuration to disable password authentication. On most linux servers, this will allow you to edit the config file:

```bash
sudo nano /etc/ssh/sshd_config
```

If that file, find and uncomment (remove the `#`) the line that says:

```
#PasswordAuthentication yes
```

and change it to:

```
PasswordAuthentication no
```

!!! Tip
    While you are in that configuration file, you should change the default port for SSH. Look for the `Port 22` directive and change it to a port > 1024 for increased security. Make sure that the chosen port is not occupied by another service and that it is enabled in your firewall.

Save the file and restart your server to enable the changes.

## 5 - Creating an alias to accelerate the connection

Now that you can login using a key, why stop there? If you have change the port on your server, the command would now be something like:

```bash
ssh -p 2022 yourusername@yourserver.com
```

What if I told you that it could only be:

```bash
ssh yourserver
```

Neat, no? All you need to do is create an alias. Here's how. On your machine, run the following command:

```bash
nano ~/.ssh/config
```

In that file, type:

```bash
Host yourserver
    HostName yourserver.com
    User yourusername
    Port yourport
```

`yourserver` after the *Host* directive is the name of the shortcut that your want. The *HostName* is the real domain name your your server. The *User* is the username you use to connect and the *Port* is the port you have set in step 4 (default is 22). Once you have saved the file, you should be able to connect using only `ssh yourserver`.

