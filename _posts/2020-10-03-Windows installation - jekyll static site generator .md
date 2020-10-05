---
title: "Windows Installation, Jekyll - Static Site Generator"
categories: Jekyll
tags:
  - static site

header:
  teaser: "/assets/images/2020-09-22-Connecting NodeMCU with Firebase/teaser.png"
---

![Jekyll logo](/assets/images/2020-10-03-Windows installation - jekyll static site generator/jekyll.png)

### Prerequisites Required

1. **Ruby version 2.5.0 or higher**
2. **RubyGems**
3. **GCC and Make**

### Step 1 : Download Ruby Installer

Go to the official site of Ruby and download **Ruby version 2.5.0 or higher**. And make sure that you download the ruby **"WITH DEVKIT"**.([Ruby](https://rubyinstaller.org/downloads/))
![ruby_installer](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step2.png)

### Step 2 : Run Ruby Installer

1. Browse the location of "Ruby Installer" and then run the setup by double clicking it.
   ![ruby_setup](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step8.png)
2. Accept the **Terms and condition** and click "Next".
   ![running_setup](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step3.png)

3. Select the Installation destination and then click "Install"
   ![selecting_destination](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step4.png)
4. Makes sure **"MSY2 development toolchain"** is checked and then click "Next"
   ![selecting_components](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step5.png)

5. By default **"Run ‘ridk install’ to setup MSYS2 and development toolchain"** is checked . Leave this checked and then click **"finish"**.
   ![rdk_install](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step6.png)

6. A black command line labelled as **"Ruby Installer 2 for windows "** provides three different option to be installed.
   Select the components to be installed and if unsure then install both 1 and 3.
   ![commandline_install](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step7.png)

7. Once you are done with all installation, please close the installer.

### Step 3 : Verify the Ruby version

Open the terminal and run command **ruby -v**.
If the system shows your installed ruby version then you are good to go.
![Ruby_version_check](/assets/images/2020-10-03-Windows installation - jekyll static site generator/step10.png)
