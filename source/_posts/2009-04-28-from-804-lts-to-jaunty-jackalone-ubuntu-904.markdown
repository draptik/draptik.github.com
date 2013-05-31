---
author: draptik
comments: true
date: 2009-04-28 22:26:03
layout: post
slug: from-804-lts-to-jaunty-jackalone-ubuntu-904
title: 'From 8.04 LTS to Jaunty Jackalone (Ubuntu 9.04): Software'
wordpress_id: 79
categories:
- linux
---

The upgrade from the Ubuntu 8.04 LTS version to 9.04 (codename "Jaunty Jackalone" = JJ) was very smooth. It did NOT screw up the X-Server, SMB, Cups, NFS, SSH, USB, etc.  For me it was about a 3GB download.

The first thing I noticed was that a nasty gdm-login-screen-resolution bug was fixed by upgrading from 8.04 to 8.10.

Since each upgrade changes the default version of ones most used/favorite software, I will list those products I was concerned about most:

**Sun Java ** ([http://java.sun.com/](http://java.sun.com/))**
**



	
  * _OK_: JJ comes with the current Java JRE and SDK version (1.6_13)


**Eclipse **([http://www.eclipse.org/](http://www.eclipse.org/))



	
  * _Out-of-date_: JJ comes with version 3.2 (2 years old). The current version is 3.4 (Ganymede). But since you can run Eclipse directly without compiling, this is no big deal.


**GNU Emacs **[(http://www.gnu.org/software/emacs/)](http://www.gnu.org/software/emacs/)**
**



	
  * **_GNU Emacs snapshot_** (=bleeding edge/developer version): _OK_: installing emacs-snapshot-gtk gives a current developer version  (build from 2009-04-05)

	
  * **_Emacs stable_**: _Out-of-date_: The current Emacs version is 22.3. JJ has Emacs version 22.2, although Emacs 22.3 was released 2008/09.


**AUCTeX **([http://www.gnu.org/software/auctex/](http://www.gnu.org/software/auctex/))**
**



	
  * _Out-of-date_: JJ comes with AUCTeX version 11.84. Version 11.85 was released 2008/02. I'll have to install this manually.


**R ** ([http://www.r-project.org/](http://www.r-project.org/))**
**



	
  * _OK_: JJ has 2.9.0. The current version is 2.9.0.


**TexLive** ([http://www.tug.org/texlive/](http://www.tug.org/texlive/))



	
  * _Out-of-date_: JJ comes with texlive-2007... Just download the current texlive-2008 from the texlive homepage and install it beside the Ubuntu texlive version.


**Skype-Plugin for Pidgin's Instant Messenger**



	
  * I've already written a [blog](http://draptik.wordpress.com/2009/04/09/pidgin-the-multi-plattform-multi-protocol-instant-messenger/) on some of the nice Pidgin features. After upgrading to JJ and seeing that Pidgin is used for internal messaging within Ubuntu, I am even more convinced that this is currently THE multi-protocol multi-plattform instant messenger to go with. Get the skype-plugin from [http://eion.robbmob.com/](http://eion.robbmob.com/) and install the Debian/Ubuntu skype4pidgin.deb file by executing "sudo dpkg -i skype4pidgin.deb"


**GTKtalog **([http://www.nongnu.org/gtktalog/](http://www.nongnu.org/gtktalog/))**
**



	
  * Not present in JJ's repositories. I'm very sad about this, because to my knowledge this is the only software for managing a CD/DVD collection which does not require a database. Although its user interface (Tck/Tk) is not-so-stylish-up-to-date, it has many features which I have grown accostumed to and which are lacking even in current high-end Disk-Manager programs.


