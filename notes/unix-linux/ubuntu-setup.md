[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Ubuntu Setup

# Ubuntu Setup



## Software Update

* Check and perform software update using `apt` package manager.

  ```plain
  sudo apt update
  sudo apt upgrade
  ```




## Installing Package (`.deb`)

* Use `dpkg`, a tool to install, build, remove and manage Debian packages.

  ```plain
  dpkg -i <.deb_file_path>
  ```




## Development Environment

* Install necessary utilities

  ```plain
  sudo apt install gcc g++ make vim 
  ```

* Create `.vimrc` under your home directory




## Git

* Install git

  ```plain
  sudo apt install git
  ```

* Update user credentials

  ```plain
  git config --global user.name "Kyungjae Lee"
  git config --global user.email kjxlee@gmail.com
                                 ----------------
                                 Double quote not necessary if there is no space
  ```

  Check `~/.gitconfig` to see the updated information.

* Clone remote repositories to your local machine.

* Save git personal access token on your system

  ```plain
  git config --global credential.helper store
  git push https://github.com/kyungjae-lee/kyungjae-lee.github.io.git
           ----------------------------------------------------------
           Repository address
  (Enter username)
  (Enter personal access token)
  ```

  Check `~/.git-credentials` to see if the token has been successfully stored on your system.



## Jekyll

* Installation guide

  - [https://jekyllrb.com/docs/installation/#requirements](https://jekyllrb.com/docs/installation/#requirements)

* From `Ruby 3.0.0`, `webrick` library is no longer a bundled gem or standard library. 
  To use its features, go to your repository, install webrick.

  ```plain
  bundle install webrick    // Linux
  gem install webrick       // Windows
  ```

  This will fix the issue that comes with running `jekyll serve` out of the box.




## gTile Extension (Window Tiling Manager)

* Install Extension Manager

  ```plain
  sudo apt install gnome-shell-extension-manager
  ```

* Run Extension Manager and search for **gTile**. Toggle it on to install.




## Install Microsoft Fonts

* To begin the installation

  ```plain
  sudo apt install ttf-mscorefonts-installer
  ```

* When the installer pops up, hit Ok, Yes then wait until the Microsoft fonts installation
  is complete.

* Scan the font directories on the system to build fresh font information cache files.

  ```plain
  sudo fc-cache -vr
  ```




## Language

* Install **Korean**.
* Reboot.
* Add Korean to keyboard input method.




## Miscellaneous

* Show batter percentage.
* Configure dock.
* Add bluetooth devices.

