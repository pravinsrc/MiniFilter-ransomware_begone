
# Ransomware BEGONE	

## What it is



> This is POC of a possible solution for blocking zero-day (or any other) ransomware in its early stage of encryption to prevent data lose.

The idea is to use a mini filter driver which will monitor specif I/O requests, and a Honeypot fodler/files that will triger for infection activity in case a process is trying to modify its content.

**What is a filter driver?**

Windows applications use Win32 API calls such as ReadFile to perform file I/O operations.  the Win32 subsystem in turn invokes the corresponding native system calls, which are handled by the I/O manager. The I/O manager uses a message-passing architecture for all I/O-related activity in the kernel .

The majority of I/O requests are represented by a data structure called the I/O Request Packet (IRP) that describes an I/O request completely. The I/O manager sends the IRP from one I/O system component to another. For example, when the Win32 operation CreateFile is called to open a file, the I/O manager creates an IRP describing the operation and sends it to the file system driver. After the driver receives the IRP, it performs the specified operation, and passes the IRP back to the I/O manager.

A mini-filter provides optional pre-operation and post-operation routines for each type of I/O request. A new structure that describes the operation is passed to these routines instead of IRPs. Mini-filter drivers can focus on request processing instead of IRP maintenance. In the pre-operation routine, the mini-filter may complete the operation (bypassing lower layers); pass it to lower layers; or redirect the request to another instance of itself on another volume's driver stack.

you can read more at: [filesystems.org](https://www.filesystems.org/docs/zen/zen.html) or [MSDN](https://msdn.microsoft.com/en-us/library/windows/hardware/ff540402(v=vs.85).aspx)

To ease the development, I used working example of mini filter by microstf, named "mini-spy" and modified/added code as needed.
it also has a user program, which let us to start and stop the monitoring at any time, which was comfterble for writing a POC, but it
is also possible to laod the mini-filter automatically.

I'm not a drivers developer, and its probably not the best code you'll see, but it is working and that was the main propose - to show a POC.

## Build

The project was compiled using Visual Studio 2013.

For WDK you can use the latest available.

It was tested on WIN7 x32, but should probably work on other OS as well (not tested thou).

If you are lazy to compile, the binaries can be found in the folder "READY_TO_USE"

Copy the folder **0Honeypot** to c:\  driver (and any other location you want if you think the malware will encrypt other folder first)

## Installing the driver 
> right click on minispy.inf and press install
>to activate:
>
>open command line and goto the minispy.exe path
>
> minispy.exe ?
>
>press another "Enter" ?
>
>( now to activate driver monitoring: )
>
> /a c: ?

You can watch a [DEMO](https://youtu.be/Fi1BsJ4lxec)

Notice that the malware will not neccesarly stop running, since it could be injected and executed from different procceses, however, even if it keeps running until
a ransomware message is shown, none of the files will be encrypted since the moment it was detected, and in that case, gives the user the possibility to reboot to other OS to remove the malicious infection with minimal lose of data.


