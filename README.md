# Hi there 👋, I'm Jason!

My name is Jason Lunz, and I'm a software and systems engineer living in Seattle, Washington, USA. You can find me on [LinkedIn](https://www.linkedin.com/in/jasonlunz/) if you'd like to get in touch.

One day I'd like to fill this profile out more, but for now I'm going to use this space to showcase some open source work I've done over the years that I'm proud of.

## Linux

I've been a Linux user both personally and in my work since [Debian hamm](https://en.wikipedia.org/wiki/Debian_version_history#Debian_2.0_(Hamm)), which isn't without occasional frustrations. Sometimes those got under my skin enough that I chased them down into the kernel and came up with a patch.

### Fixed Reference Counting for Packet Sockets

When I was working at a [Network Intrusion Prevention System startup](https://web.archive.org/web/20031216042458/http://reflexsecurity.com/), I often used tcpdump to look at raw packets. Sometimes, the wrong sequence of bringing an interface up and down while using tcpdump would put it in an annoying state where the interface was in promiscuous mode whenever tcpdump _wasn't_ attached, and not in promiscuous mode when it _was_. It turns out this was a [refcounting bug in the kernel](https://git.kernel.org/pub/scm/linux/kernel/git/tglx/history.git/commit/?id=438fb92c9a999e86dea8fb591abf355a4620e8c1).

### JFFS2 Filesystem Deadlock

At the startup, the systems we shipped had to be highly reliable, so they used [JFFS2](https://en.wikipedia.org/wiki/JFFS2) for data storage. One day I upgraded the kernel and it began locking up, so [I fixed it](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fc0e01974ccccc7530b7634a63ee3fcc57b845ea).

### LVM crash on UML

[User Mode Linux](http://user-mode-linux.sourceforge.net/) is a little known port of linux to its own system call interface as its "hardware" architecture. This handy, lightweight form of virtualization allowed my team to develop our system far more efficiently, back before more modern forms of virtualization (like kvm or qemu) were widely available.

Unfortunately, UML's memory layout was set up in such a way that the system would crash if [LVM](http://www.sourceware.org/lvm2/) were initialized. This turned out to be one of those issues where [the description of the fix was longer to write than the code](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=af84eab208916acad91f6342bbd57bc865caf150).

### Suspend-to-RAM Crash in the IDE Disk Driver

My first linux laptop crashed hard whenever I tried using suspend-to-RAM. Needless to say, having a laptop that can't sleep is pretty annoying. It took me weeks to track this down because the crash turned out to occur before the console was enabled (meaning no log output was visible on the screen), and it was in the disk driver, which prevented any debug output from being logged to disk. I had to set up UDP logging to another machine over the network to debug it.

[The fix](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8c2c0118b86183bf4826db990cae5c8a8d6c6746) was to add a state to the IDE power management state machine to reprogram PIO timings on resume.

## Protocol Buffers

I once designed a distributed stateful packet filter for VMWare that ran partially in-vmkernel. It used Google Protocol Buffers to encode the filter rulesets, and https://github.com/protobuf-c/protobuf-c to decode those rulesets. Unfortunately, there was an edge case when memory was constrained that would crash the entire host, and all the virtual machines running on it (perhaps my most expensive bug of all time 🤑).

The [issue](https://github.com/protobuf-c/protobuf-c/issues/15) was [fixed based on a patch I submitted](https://github.com/protobuf-c/protobuf-c/commit/ad4442f388b6ebeb517358af4c993567607e219d).
