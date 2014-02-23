bbb-xenomai-patches
===================

Patches for xenomai &amp; Robert C Nelson Kernel for Xenomai

Installation steps:
1) Get Robert C Nelson Kernel build environment first - tag v3.13.4-bone5 from
	git://github.com/RobertCNelson/linux-dev.git

2) Get Xenomai master from git://git.xenomai.org/xenomai-2.6.git

3) Build default Robert C Nelson kernel running the script build\_kernel.sh 
	(this will download all the required tools, sources, patches etc.).
	This step is not strictly necessary, because it is sufficient to get the
	tools and patch the kernel, but building default Robert's kernel guarantees
	that the all necessary patches will be aplied

4) After the patches were applied in previous step you can give tag to the
	patched Robert's kernel tree - cd KERNEL; git tag 3.13.4-bone5

5) in directory KERNEL apply patch  	v3.13.4-bone5\_ipipe.patch.gz 
	zcat v3.13.4-bone5\_ipipe.patch.gz | patch -p1

6) copy/overwrite right config from repo stored in directory configs/ to
directory KERNEL overwriting files .config &amp; .config.old - 
	the config includes only basic drivers & has all debug enabled I wanted.
	When somebody wishes to change it please change the settings concerning
	debugging. Warning!!! Ipipe debugging/tracing options seem to be _very_
	expensive in CPU time.

7) go to your Xenomai repo from step (2)

8) apply patch xenomai-2.6.3\_master\_3.13.patch.gz to xenomai 
	cd ${XENO_DIR}; zcat xenomai-2.6.3\_master\_3.13.patch.gz | patch -p1
	
9) installa xenomai as usual: ./scripts/prepare-kernel.sh --kernel=${RCNKernelPath}, etc...

10) rebuild &amp; install the kernel in BBB (there are various places where it
	is described - eewiki is one of those ).

11) test & eventually enjoy (surely moderately, because there are some serious
	flaws, mosty concerning mmc read / writes with external SDcard).

The flaws:
1) the version is completely unsupported by Xenomai or IPIPE teams! Please
don't bother the guys with any problems concerning the patches.
2) xeno\_rtdmtest &amp; xeno\_klat seem to introduce plenty of trouble concerning
system stability. I had to rewrite rootfs because of FS errors caused by MMC
subsystem malfunction. So !!!BE CAREFUL!!! and make a backup before playing
with the xenomai version.
I've seen something like "[  240.244050] omap\_hsmmc 48060000.mmc: MMC start
dma failure" and later it was not funny anymore!

3) only BBB is handled with the patch - the rest of the platforms at least
partially. I've join'ed not applied ipipe-3.10 patches in
ipipe-3.10-patches\_skipped.patch.gz for somebody who wishes to apply the rest
or apply the patches for some other platforms similar to BBB.

4) during heavy IO load I've seen "Unable to handle kernel NULL pointer
dereference at virtual address 000004fc" in dmesg. the dereference was in
function cleanup\_event(). I've seen it only once, but haven't performed the
testing well.

5) test xenomai/bin/regression/native/sigdebug gives error 
"relaxed mutex owner
FAILURE rt\_task\_body:102: rt\_mutex\_acquire returned 0 instead of -4 - Success"
therefore I'm not sure about rt\_mutex handling quality at all.

6) loadig xeno\_klat can cause immidiate system freeze

In directory configs/ are stored config files for kernel rebuild. The ones
don't include all possible driver configurations, but include some debugging
options for ipipe / xenomai, also for some am335x peripherals.

In directory tests/ xenomai test results are stored.
