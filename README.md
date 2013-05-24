vagrant-audio
=============

A minimal Vagrantfile that makes sound work on Mac (host) and Ubuntu/Precise (guest).
Uses the OSS sound driver system (I could not get ALSA to work), and verified with Mac 10.8.3, VirtualBox 4.2.12

- Do "vagrant up" to start everthing, then "vagrant ssh" to login to the VM.
- Do "osstest" and you should hear sound.