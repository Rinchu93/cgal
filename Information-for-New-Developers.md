<!--TOC-->

# Table of Contents
* [Creating a GitHub account](#creating-a-github-account)
* [Setting up your development environment](#setting-up-your-development-environment)
* [Important Resources](#important-resources)
* [Stay in contact](#stay-in-contact)

<!--TOC-->

Once your mentor has introduced you on the [CGAL Editorial Board mailing list](Mailing-Lists), and once the [CGAL Editorial Board](http://www.cgal.org/people.html) has approved your participation in the CGAL project as a [CGAL developer](http://www.cgal.org/project_rules.html#developers), then you should follow the following guidelines. 

## Creating a GitHub account

-   Visit <https://github.com/join> and then, choose **Free** as the
    personal plan (while you are free to pay if you want).
-   You should receive an email to *verify* that the email address
    provided is valid.
-   Add your first name and last name and complete your profile at
    <https://github.com/settings/profile>
-   Add all email addresses you have ever used (for statistics) at
    <https://github.com/settings/emails> and, optionally, check the box
    *Keep my email address private*.
-   Set up your SSH keys: <https://github.com/settings/ssh> (see
    [here](http://help.github.com/key-setup-redirect) for help)
-   **Recommended** Set up the 2-factor authentication
    <https://github.com/settings/security> (if you don't set it you'll
    be contacted by an admin of CGAL to ask you why).
-   Configure notifications at
    <https://github.com/settings/notifications>

Once done, send an email with your Github ID to [@sloriot](https://github.com/sloriot) who will
add you to the list of members of the CGAL organization, so that you can
see the private repositories.

**<span style="color:red">Note that each developer with SCM access has
the power to modify all source files for the time being. This does not
entitle you to exploit this. You are expected to be very cautious if you
change anything under the version control system.</span>**

## Setting up your development environment


**<span style="color: red; ">BEFORE YOU START ANY CODING BE SURE TO SET
UP CGAL IN EXACTLY THIS WAY:</span>**

- Uninstall any old CGAL Installation (if you installed CGAL through a package manager, use it to uninstall it again, if you installed CGAL from a tarball, you need to remove it manually)
- Install all dependencies
  - on Arch Linux: `pacman -S cmake qt5 gmp mpfi mpfr boost zlib mesa glu eigen`, you can get `qglviewer` from AUR
  - on Mac OS X, use brew (`brew install --only-dependencies cgal`) or macports
  - on Windows things are more [delicate](https://www.cgal.org/windows_installation.html) (TODO other link?)
- Clone your repository (if you're CGAL *student* clone `cgal-public-dev`)
  - cloning cgal-dev: ` git clone git@github.com:CGAL/cgal-dev.git`
  - or cloning cgal-public-dev: ` git clone git@github.com:CGAL/cgal-public-dev.git`
  - add read-only branches: ` git remote add cgal git@github.com:CGAL/cgal.git`
- Set `CGAL_DIR` persistently to a directory where you intend to build CGAL
  - UNIX environment: Add `export CGAL_DIR=path/to/cgal/build` to `~/.profile` or your shell configuration file. Refer to your systems documentation for more specific information.
- [Build CGAL from the repository](Branch-Build#using-a-single-version-of-cgal)

## Important Resources


There is a lot of information in the wiki. Some pages that every
developer should read are:

-   The [public Developers' Manual](http://doc.cgal.org/latest/Manual/dev_manual.html)
    (especially the [*Getting Started* section](http://doc.cgal.org/latest/Manual/general_intro.html))
-   Our [Guidelines](Guidelines) and in particular
    - [Source Code Management](Source-Code-Management-with-Git) and
    its subpages 
    - [Package directory structure](Directory-Structure-for-Packages)
    - [Package documentation](Documentation-Guidelines)

## Stay in contact

-   Please introduce yourself on your **user page** of the *internal* wiki (click on your user
    name at the top of every wiki page when logged in), and provide your
    e-mail address there as well (remember that that wiki is not
    publicly accessible).
-   Have a look at our [mailing lists](Mailing-Lists). The
    developers mainly communicate via *cgal-develop* (with restricted
    access only for [CGAL developers](http://www.cgal.org/project_rules.html#developers)), but you might want to also
    subscribe to the public (high-traffic) *cgal-discuss* and/or
    (low-traffic) *cgal-announce*.
-   We meet in person on *CGAL developer meetings*, which take place roughly each six
    months. We try to distribute the meetings between several institutes
    affiliated to CGAL. They are announced via *cgal-develop* and on the internal
    wiki.
