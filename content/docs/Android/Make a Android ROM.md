# How to build Android for a device
## Pre-requisites

Make sure the machine you'll be building on has the recommended requirements; the bare minimum is 32GB RAM, if you are going to use zram during the build. Decreasing the job count will help with RAM usage, and could even possibly get it to build on 8GB RAM, but I recommend 48GB or more of RAM.

Create the directory you'll do your build in with ```mkdir crDroid && cd crDroid``` (substitute crDroid for whatever you want the folder to be called, it doesn't need to be called anything specific)

For Debian 12 and newer (as of Feb 2025) (or any distro based on Debian 12 or newer, with these packages in the repos) run this:
```sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev```
After this is installed, you should have everything you need.

You'll need repo for this, so run these:
```mkdir ~/bin```

```PATH=~/bin:$PATH``` **Sidenote: ```PATH=~/bin:$PATH``` will need to be ran each reboot on your build machine in order for repo to be recognized.**

The following doesn't need to be ran each reboot.
```curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo```
```chmod a+x ~/bin/repo```

## Initialize the repo and download the code
Now, let's init the repo!
To init the repo, run the following (make sure you're in the folder you chose to build this in!)
```repo init -b branch-name-here --git-lfs --depth=1 -u link-here```
Make sure to replace `branch-name-here` with the branch corresponding to the Android version you intend to build. For example, for crDroid 11 (based on Android 15.0) I would put `15.0` there, or for LineageOS 22.1, for example, I would put `lineage-22.1`. You also have to replace link-here with the link to the git repository you're using for this. It will *always* end with .git, and is usually a repository named `android` inside of the organization. Here, I used `https://github.com/crdroidandroid/android.git` (for crDroid), and while it's pretty similar for Lineage, it can depend a lot.
An example of the command - this initializes repo and makes it know that this is where we'll be downloading from, essentially:
```repo init -b 15.0 --git-lfs --depth=1 -u https://github.com/crdroidandroid/android.git``` This command will initialize Repo, and have it know that I'm working on something that's related to crDroid 11.

At this point, put your local manifest in .repo/local_manifests/.
## TODO for me: Add section on how to find or create a local manifest for your device

Now, time to download it all! Make sure you have at least 300GB of space available where your build folder is; the more the better (After building Android 15 on a machine with 300GB total disk space, there's usually about 10-15GB left of free space. This is workable, if you know how to manage it, but that's a topic for another day for me to put into this guide.)
```repo sync```

## Actually building it
### Putting the build keys in and working with them
Now, I highly recommend you put build keys into your build once you've verified that the build works. Making a build key for something like crDroid is something I can't cover right now, but I will add that here in the future.

For crDroid and LineageOS, they go in `<build folder>/vendor/lineage-priv`. Your build keys should be in a folder named lineage-priv; this is where it goes.

Now, run:
```. build/envsetup.sh```. Even if this looks like it wouldn't work, it does.

Now, to actually build it! Make sure to set aside 3-4 hours for this if you are using the recommended specs, since that's how long it typically takes on recommended specs in my experience (I build my ROMs on a 8c8t VM (I have a Ryzen 7 7700X and 64GB RAM, but I can't main Linux on my main PC since I require some Windows only apps, so I use a VM for Android building for the time being.) with 48GB RAM and 300GB NVMe storage on a Crucial T500 SSD.) Lower specs will require more time, often several hours more, so be patient! You shouldn't use your computer while it's building, as it can disrupt the build process.

Here's what to run for each version (you don't need them all!):
Android 12L and earlier:
```brunch lineage_[devicecodename]-userdebug```
Android 13:
```lunch lineage_[devicecodename]-userdebug```
```m bacon```
Android 14:
```brunch [devicecodename]``` OR ```lunch lineage_[devicecodename]-userdebug```
```m bacon```. Either works.

Some phones, such as my Xperia XZ2 Premium, will fail the build without allowing missing dependencies. In those edge cases, you should use ```export ALLOW_MISSING_DEPENDENCIES=true``` to fix it. The build should complete without issue.

## Finding and testing the build
Your ROM will be in `<build folder>/out/target/product/[devicecodename]/`. What you need from this folder depends for every phone, so I won't go into that at the moment, but possibly in the future for a few phones.
Install the build, and keep in mind **you will need to factory reset the device in order to do this!**
If it's successful, then continue on (last part will be written eventually)
