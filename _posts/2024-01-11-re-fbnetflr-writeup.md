---
layout: post
title: "[RE] What's inside a Windows driver? Reversing FBNetFlt.sys writeup"
categories: hacking writeup re windows driver lenovo
---

## Introduction
If you own a Lenovo gaming laptop, there is a high chance that you have Lenovo Vantage software installed. Although I don't like how bloaty they made it, it has some nice features. They have a feature called Network Boost. How does it work?

![Image from NotebookCheck](/assets/images/fbnetflt/Lenovo_vantage_quick_options.jpg)

FBNetFlt.sys is a kernel module to support that functionality. I don't know exactly what it does, but as the name suggests, it probably is a network filter. Recently it also caused a BSOD so I decided to take a look at it to see what's inside this driver.

## Initial research
A quick search on Google to make sure that this haven't been done by anybody. That's even better since I am the first one to do this. Let's dive into the challenge.

## WFP basics
WFP (Windows Filtering Platform) is a framework for filtering network packets in Microsoft Windows. It's been around since Windows Vista. WFP can be used in two ways: Kernel mode and User mode. While WFP User mode powers some very good piece of software like [simplewall](https://github.com/henrypp/simplewall) by Henry++, WFP Kernel Mode allows more advanced features like modifying and injecting packets into the TCP/IP stack. 

This [blog post](https://scorpiosoftware.net/2022/12/25/introduction-to-the-windows-filtering-platform/) by Pavel Yosifovich is a great introduction.

FBNetFlt.sys obviously uses WFP Kernel Mode.

## Driver structure
But before we get into the interesting part, let's take a look at what a Windows driver look like. Windows provides vendors with different ways to build a driver. 
* WDM: The low-level API to write driver, more grain-controls, harder to use.
* WDF (Windows Driver Framework): An abstraction layer atop of WDM. It provides KMDF and UMDF.
    - KMDF (Kernel Mode Driver Framework): WDF functions in kernel mode.
    - UMDF (User Mode Driver Framework)L WDF functions in user mode.

[Microsoft's documentation](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/) has loads of information about Windows driver.

## TBD