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

Now you need to copy your new key to your server. You will need either a password-based SSH account or physical access to the machine for this step. Here are two ways of doing this :

### Using `ssh-copy-id``

If you have `ssh-copy-id` available on your system this step is easy, just run the following command :

```bash
ssh-copy-id yourusername@yourserver
```

`yourusername` should be a password-based SSH account on your server.