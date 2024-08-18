[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > POSIX and the Single UNIX Specification

# POSIX and the Single UNIX Specification



## X/Open Portability Guide (XPG)

* UNIX fragmentation and the lack of a single conforming standard affected the development of portable applications.
* To address this issue, AT&T created *System V Interface Definition* (SVID).
* Later, X/Open (now The Open Group), created the *X/Open Portability Guide* (XPG).
    - e.g., UNIX95, UNIX98, UNIX03 (depending on the version of the specification)



## Portable Operating System Interface for Computer Environments (POSIX)

* **POSIX**, the *Portable Operating System Interface for Computer Environments*, was developed at the behest of the Institution of Electrical and Electronics Engineers (IEEE).
* POSIX is a set of standard interfaces based on the UNIX operating system. POSIX compliance ensures that a set of programs developed on one machine can be moved to another without recoding.
    - POSIX.1
      - Specifies the *C application program interface*.
    - POSIX.2
      - Deals with the *shell* and *utilities*.
* POSIX refers to operating systems in general, but was based on UNIX.



## Single UNIX Specification, Version 3 (SUSV3)

* **SUSV3** is the unification of two standards; *XPG* and *POSIX*.
  - a.k.a. *IEEE 1003.1:2001*
  - We will make references to POSIX, but they should be interpreted to mean
    the SUSV3 as well.
* Once software has been developed on any POSIX-compliant UNIX system, it can be easily ported to another POSIX-compliant UNIX machine with minimum modifications. ("wrtie once, adopt everywhere" approach)
* Link to SUSV3: [http://www.unix.org/unix03.html](http://www.unix.org/unix03.html)
