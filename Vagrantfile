def local_cache(box_name, purpose)
  cache_dir = File.join(File.expand_path(Vagrant::Environment::DEFAULT_HOME),
                        'cache',
                        purpose,
                        box_name)
  partial_dir = File.join(cache_dir, 'partial')
  FileUtils.mkdir_p(partial_dir) unless File.exists? partial_dir
  cache_dir
end

Vagrant.configure("2") do |config|
  config.ssh.forward_x11 = true # useful since some audio testing programs use x11
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.provision :shell, :inline => $BOOTSTRAP_SCRIPT # see below

  # enable audio drivers on VM settings
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, '--audio', 'coreaudio', '--audiocontroller', 'hda'] # choices: hda sb16 ac97
  end

  # apt package caching
  config.vm.synced_folder (local_cache config.vm.box, 'apt'), "/var/cache/apt/archives/"

end

$BOOTSTRAP_SCRIPT = <<EOF
	set -e # Stop on any error

	# --------------- SETTINGS ----------------
	# Other settings
	export DEBIAN_FRONTEND=noninteractive

  sudo apt-get update

  # ---- OSS AUDIO
  sudo usermod -a -G audio vagrant
  sudo apt-get install -y oss4-base oss4-dkms oss4-source oss4-gtk linux-headers-3.2.0-23 debconf-utils
  sudo ln -s /usr/src/linux-headers-$(uname -r)/ /lib/modules/$(uname -r)/source || echo ALREADY SYMLINKED
  sudo module-assistant prepare
  sudo module-assistant auto-install -i oss4 # this can take 2 minutes
  sudo debconf-set-selections <<< "linux-sound-base linux-sound-base/sound_system select  OSS"
  echo READY.

  # have to reboot for drivers to kick in, but only the first time of course
  if [ ! -f ~/runonce ]
  then
    sudo reboot
    touch ~/runonce
  fi
EOF
