# sudo-security
*Security requirments for using sudo*

The use of sudo needs some security issues to be considered.
The following requirements should help to prevent the whole system from being compromised.
Never forget: The only way to use sudo in a secure way is not to use sudo ;-) because of its suid-root-bit.

This document is intended for (Linux) Servers only, not for Desktop systems.

 Assumptions (requirements on Linux in general, not only subject to sudo:
- Login to the system is always done via SSH key
- There may exist “individual accounts” for every user.
- There may exist “functional accounts” for groups of operators and/or admins where personal identification is done with the log entry of the used ssh key fingerprint.
- There always exist “system accounts”, e.g. www-data, daemon, bin, …
- Root is the only account which needs a real password (for console login)
- Avoiding passwords simplifies password management and tools.
- With ssh keys, managed by a reliable key distribution system (and properly configured passphrases), there is no need for another authentication factor when performing administrative tasks.
- Users, Groups & Processes on the system are configured in a way that most work can be done with suitable user accounts directly without the need of escalated privileges.
- Capabilities on needed executables were checked and properly configured to achieve the principle of least privileges instead of running the executable via sudo as root.
- If an audit trail of activities is needed, this must be done by a jumphost in front of the system (because local logging cannot be trusted if the user has full root privileges). See Req. “Architecture of systems”.
- Additional local activity logging can be done (workers council approval needed).
- Logging via sudo is incomplete and cannot be trusted if the execution of further processes is possible for the user (sudo only logs the allowed command, not the content of a, e.g., shell started from an allowed command).
- There may be used a so called “rootsh” – a shell that logs all commands typed in. Remember: If another shell is started from the rootsh, there are no logs anymore.

Goals
- Minimizing the risk of compromising the entire system,
   - give only least privileges to all user accounts to execute their tasks,
   - there should be no way to gain full root privileges via sudo for system or application accounts.
- Avoid passwords.
- Logging is not a goal of sudo.
- Keep it simple.

And yes, of course, there are a lot of other possibilities to gain root privileges. But if the system has a current patch-level (no kernel exploits, no vulnerabilities to suid-binaries), most common way to compromise a system is done via misconfiguration. Perhaps by a bad sudoers file.
