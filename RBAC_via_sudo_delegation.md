The Scenario

In a growing infrastructure, giving every user full root access is a high-risk security vulnerability. This project demonstrates how to delegate specific system privileges to different organizational roles (Interns vs. Seniors) using the "Principle of Least Privilege."
The Architecture (The "How-To")

Instead of modifying the core /etc/sudoers file, I used a modular approach by creating a dedicated policy file in /etc/sudoers.d/dev_team. This ensures the configuration is easy to audit and survives system updates.
Step 1: Defining the Roles & Scopes

I used Aliases to make the security policy scalable.

The Policy Logic:

    INTERNS: Can only manage the web server (status, restart, reload).

    SENIORS: Can perform all administrative tasks but are required to authenticate with their own password for accountability.



Execution: 

As above stated, I created a file inside /etc/sudoers.d/ called dev_team, to include granular permissions for the different teams that are to be managed under LPA.

using sudo visudo /etc/sudoers.d/dev_team, i created the following permissions : 

<img width="1100" height="580" alt="Captura de ecrã 2026-02-17 111539" src="https://github.com/user-attachments/assets/c294e94e-fe8c-4a8c-b6ca-46fcea3ce0f5" />

A few notes here. While this is an exercise, I'd like to point out that in a scenario where one might have 10~50 senior developers with relaxed permissions (such as what I've made here) this permission setting is a major security risk.

For example : by setting rules (even with password request) that they can basically perform any task using sudo, let's perform a small scenario : 

Let's say for instance that I'm the senior dev.

I'm not particularly happy with the company that i'm at, and I know full well my way around a linux machine. since I have privileged rights (all commands can be runned, I just need to provide my password) let's create chaos:

<img width="1094" height="212" alt="Captura de ecrã 2026-02-17 112117" src="https://github.com/user-attachments/assets/a3208f5d-c093-4b4b-aa65-18ce5d867f06" />

since I'm a dev, I can run vi. It can be normal. but wait...

<img width="1103" height="579" alt="Captura de ecrã 2026-02-17 112232" src="https://github.com/user-attachments/assets/7f83939f-e82b-4846-912d-248322a63fc8" />

running this command - !/bin/bash - creates a very funny situation.

<img width="342" height="39" alt="Captura de ecrã 2026-02-17 112323" src="https://github.com/user-attachments/assets/cc2107d6-19bc-4761-8be1-4c1061b0e923" />

dev_senior is now root. Using vi and running the command !/bin/bash creates an escape shell without asking any question (! forces vi to not ask any questions.)

So if he's root....

<img width="1094" height="559" alt="Captura de ecrã 2026-02-17 112510" src="https://github.com/user-attachments/assets/8e3f97c4-f0b3-4fb8-8fba-01f2eb5b93ee" />

There goes everyone's secrets.

This doesn't just apply to a discontent employee who wants to burn the company down. He could actually be a very responsible person - the same scenario can be applied to a threat actor that somehow managed to either crack a password from ANY of the senior developers. The permissions are already relaxed enough for the threat actor to have a direct ladder towards exploiting, exfiltrating any anything else that he/she wants to have /destroy.

So what did I learn?

It's not what you're immediately thinking - "don't give users sudo". sudo is there to be used, and while it can definitely cause some problems, it still is a mode to be used.

The best way to get around this is not only thinking about the user permissions, but how hardened actually sudo is. While there isn't a definite failsafe method, there is logging to create accountability in case things go south (make sure the logging is on a remote server, as away as possible from the hands of threat actors).

There's NOEXEC which can be nailed in the sudoers file, but it's still difficult to keep a proper leash tight enough.

You can also try to set some boundaries on even what root can do (even though if anyone is root eventually if they find changes, they can revert it back because you know...root).


So in the end, this is how important Sudo delegation needs to be really well thought out, as well as the commands that we are to allow to specific persons /departments.

