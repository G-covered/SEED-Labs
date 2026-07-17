# Introduction

## Lab Overview

In this lab, we will study **Cross-Site Request Forgery (CSRF)** attacks using a social networking website as the target application. The objective is to understand how an attacker can exploit a victim's authenticated session to perform unauthorized actions without the victim's knowledge.

Throughout the lab, we will perform attacks such as:

- Adding the attacker as the victim's friend without the victim's consent.
- Modifying the victim's profile information.

These attacks demonstrate how a web application that lacks proper CSRF protections can be manipulated by malicious websites.

![Lab Overview](https://github.com/user-attachments/assets/760fc5b6-1351-4bdb-968d-eeb6fab33fc9)

## Lab Environment

The lab consists of three different websites, each serving a specific purpose:

### Target Website

The target website is a **SEED social networking application** running inside a Docker container. This intentionally vulnerable application has certain security protections disabled, allowing us to perform CSRF attacks and observe their effects.

### Attacker Website

The attacker website hosts a malicious webpage containing the CSRF attack code. When a logged-in victim visits this page, the browser automatically sends authenticated requests to the target website, causing unauthorized actions to be executed on the victim's behalf.

### Countermeasure Website

The countermeasure website demonstrates defenses against CSRF attacks. It allows us to compare a vulnerable application with one that has proper security protections enabled.

![Lab Architecture](https://github.com/user-attachments/assets/c5462045-0178-439c-bbb7-f6c4cd736748)

## Network Configuration

The target website and the countermeasure website are hosted inside the same Docker container (IP address **10.9.0.5**). After downloading the lab setup files and starting the containers, these websites become available through hostnames that must be mapped in the system's `/etc/hosts` file.

The attacker website runs on a separate machine. Its hostname must also be mapped to the appropriate IP address in the `/etc/hosts` file so that all three websites can communicate correctly during the lab.

Configuring these hostname mappings ensures that the browser can resolve each website by name, allowing the CSRF attacks and countermeasures to function as intended.
