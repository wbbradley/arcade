# Arcade

## Get image

- Locate your USB or SD drive with `diskutil list` on macOS or `fdisk-l` on
  Linux.
- Unmount is with `diskutil unmountDisk /dev/disk1` or `sudo umount /dev/sdb1`
- Download raspi image from https://www.raspberrypi.com/software/operating-systems/
- Decompress with gunzip -d <filename>
- Blast the ISO onto the drive.

```
sudo dd bs=1m if=2017-04-10-raspbian-jessie-lite.img of=/dev/disk<N>
```

- Unmount it again.

- Make sure raspi-config is set up to boot from your chosen format (SD or USB).
- Reboot.

## Console

### Setup SSH

```
sudo systemctl enable ssh
sudo systemctl start ssh
```

From Personal machine

```
scp ~/.ssh/$PRIVATE_KEY_FILENAME* your-raspberry-pi:~/.ssh/
```


### Fix virtual console fonts

```
sudo dpkg-reconfigure console-setup
```

### Fix caps-lock -> control mapping

Remap Caps-lock to control. In /etc/default/keyboard, add or append using comma
separator.

```
echo 'XKBOPTIONS="ctrl:nocaps"' | sudo tee -a /etc/default/keyboard
```

### Install tools


```
# Add 
cat >>"$HOME"/.ssh/config <<EOF
Host github.com
	User git
	IdentityFile $PRIVATE_KEY_FILENAME
EOF
cat $PRIVATE_KEY_FILENAME.pub >>authorized_keys

# Install git
sudo apt update -y
sudo apt install -y git

# Make src dir
mkdir "$HOME"/src
cd; cd src

# Install dotfiles
git clone git@github.com:wbbradley/dotfiles.git
cd dotfiles
./install.sh
exec -l bash
clean-vim.sh

# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### Build DevX

```
# Get raylib sources
git clone git@github.com:raysan5/raylib.git $HOME/src/raylib
cd $HOME/src/raylib

# Install raylib build dependencies
sudo apt-get install cmake

# Install raylib runtime dependencies
# From https://github.com/raysan5/raylib/wiki/Working-on-Raspberry-Pi
sudo apt-get install libdrm-dev libegl1-mesa-dev libgles2-mesa-dev libgbm-dev

mkdir build
cd build
cmake ..
```
