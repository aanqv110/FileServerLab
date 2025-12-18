# Overview

This project documents the design and implementation of a production-style on-premises Windows infrastructure.
The lab simulates how Active Directory, file services, automation, and disaster recovery are implemented in a real enterprise environment.

The focus of this project is correct architecture, security best practices, role separation, and migration readiness, rather than simply making services work.

## Lab Objectives

-Deploy Active Directory Domain Services and DNS

-Implement a dedicated file server (no file shares on the domain controller)

-Secure file access using NTFS permissions and security groups

-Abstract file access using DFS Namespace

-Automate drive mapping using Group Policy

-Implement backup and disaster recovery for AD and file services

-Validate access control and recovery scenarios

## Environment Summary

Domain name: LAB.local

Servers:

DC01 – Domain Controller, DNS, DFS Namespace

FS01 – File Server

WIN10 – Domain-joined client

Primary user access path:

## Logical Architecture

LAB.local domain contains a Domain Controller (DC01) and a dedicated File Server (FS01).
Users authenticate to Active Directory on DC01 and access files through a DFS Namespace hosted on the domain.

The DFS Namespace abstracts the file server so users never connect directly to FS01.

WIN10 is used to validate authentication, authorization, and automated drive mapping.

## Server Roles and Responsibilities

DC01 – Domain Controller

Active Directory Domain Services

DNS

DFS Namespace hosting

Group Policy Management

No file shares are hosted on the domain controller.

FS01 – File Server

Dedicated file server role

NTFS permissions enforced at the file system level

SMB shares for departmental data

Backend target for DFS Namespace

WIN10 – Client

Domain-joined Windows 10 workstation

Used to test user access, drive mapping, and permissions

## Identity and Access Management

All access is managed using Active Directory security groups.

Security groups created:

HR-Share-Users

IT-Share-Users

Finance-Share-Users

Users are added to groups based on department.
No permissions are assigned directly to individual users.

This enforces a least-privilege, scalable access model.

## File Services Design
Physical File Structure (FS01)

Z:\FileShares\HR

Z:\FileShares\IT

Z:\FileShares\Finance

NTFS Permission Model

Z:\FileShares

Domain Admins – Full Control

SYSTEM – Full Control

Inheritance disabled

Department folders

HR – HR-Share-Users – Modify

IT – IT-Share-Users – Modify

Finance – Finance-Share-Users – Modify

NTFS permissions are treated as the primary security boundary.

## SMB Shares

SMB shares are created on FS01 for each department:

HR

IT

Finance

Share permissions:

Domain Admins – Full

Department security group – Change

Combined with NTFS permissions, this ensures secure and controlled access.

## DFS Namespace

A domain-based DFS Namespace is implemented to abstract file server dependencies.

DFS Namespace path:

DFS folder targets:

Benefits of DFS:

No hard-coded server names for users

Seamless file server replacement

Improved scalability and migration readiness

## Group Policy Automation

Drive mapping is automated using Group Policy Preferences.

Drive mappings:

HR users → H: mapped to \LAB\Shares\HR

IT users → I: mapped to \LAB\Shares\IT

Finance users → F: mapped to \LAB\Shares\Finance

Each department has its own GPO with:

User Configuration → Preferences → Drive Maps

Security filtering applied to the corresponding security group

Authenticated Users removed

No item-level targeting used

## Validation and Testing

Validation was performed using real domain user accounts.

Domain join verified

DFS namespace resolution confirmed

NTFS permissions enforced correctly

Unauthorized access denied

Drives mapped automatically at logon

## Disaster Recovery Implementation
File Server Backup

Windows Server Backup installed on FS01

Scheduled backup configured for Z:\FileShares

Backup destination stored on a separate disk

File Restore Testing

Test files deleted from departmental shares

Files restored using Windows Server Backup

File integrity and permissions verified after restore

## Active Directory Backup

Windows Server Backup installed on DC01

System State backup performed

System State includes:

Active Directory database

SYSVOL

Registry

Boot configuration

## Active Directory Restore (Simulation)

Recovery scenarios tested:

Accidental deletion of user accounts

Accidental deletion of organizational units

Restore methods:

Non-authoritative restore

Authoritative restore (conceptual, single-DC lab)

Post-restore validation:

Objects restored successfully

Directory services operational

## Business Continuity Considerations

DFS allows file server replacement without user impact

Group-based permissions simplify recovery and auditing

Backup strategy protects against accidental deletion and ransomware

Architecture supports future Azure Backup or hybrid integration

## Tools and Technologies Used

Windows Server 2019

Active Directory Domain Services

DNS

DFS Namespace

NTFS and SMB

Group Policy Management

Windows Server Backup

PowerShell

Windows 10

## Skills Demonstrated

Enterprise Active Directory design

File server architecture and security

NTFS vs SMB permission modeling

DFS Namespace implementation

Group Policy automation

Disaster recovery planning and testing

Infrastructure troubleshooting

Migration-ready system design

## Summary

This lab demonstrates how a real enterprise Windows environment is designed, secured, automated, and protected.
It reflects practical system administration and infrastructure engineering skills rather than isolated configuration tasks.
