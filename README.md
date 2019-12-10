## sudo-security
*Good Practises for using sudo in a secure manner*

The use of sudo needs some security issues to be considered.
The following requirements should help to prevent the whole system from being compromised.
Never forget: The only way to use sudo in a secure way is not to use sudo ;-) because of its suid-root-bit.

This document is intended for (Linux) Servers only, not for Desktop systems.

### Assumptions
(Linux in general, not only subject to sudo:
- Login to the system is always done via SSH key
- There may exist “individual accounts” for every user.
- There may exist “functional accounts” for groups of operators and/or admins where personal identification is done with the log entry of the used ssh key fingerprint.
- There always exist “system accounts”, e.g. www-data, daemon, bin, …
- Root is the only account which needs a real password (for console login)
- Avoiding passwords simplifies password management and tools.
- With ssh keys, managed by a reliable key distribution system (and properly configured passphrases), there is no need for another authentication factor when performing administrative tasks. (!this means, there is no additional password needed!)
- Users, Groups & Processes on the system are configured in a way that most work can be done with suitable user accounts directly without the need of escalated privileges.
- Capabilities on needed executables were checked and properly configured to achieve the principle of least privileges instead of running the executable via sudo as root.
- If an audit trail of personell activity is needed, this must be done by a jumphost in front of the system (because local logging cannot be trusted if the user has full root privileges).
- Additional local activity logging can be done.
- Logging via sudo is incomplete and cannot be trusted if the user is permitted to execute further processes (sudo only logs the allowed command, not the content of a, e.g., shell started from an allowed command).
- There may be a so called “rootsh” – a shell that logs all commands typed in. Remember: If another shell is started from the rootsh, there are no logs anymore.

### Remember
- sudo needs suid-root (evil!)

### Goals
* Minimizing the risk of compromising the entire system:
  * give only least privileges to all user accounts to execute their tasks
  * no way to gain full root privileges via sudo for system or application accounts
* Avoid passwords.
* Logging is not a goal of sudo.
* Keep it simple.

And yes, of course, there are a lot of other possibilities to gain root privileges. But if the system has a current patch-level (no kernel exploits, no vulnerabilities to suid-binaries), most common way to compromise a system is done via misconfiguration. Perhaps by a bad sudoers file.

## Requirements ##

#### Req 1   Users and groups, which can run commands via sudo, must explicitly be named in the sudoers file.
[or in other words: `ALL` as username is prohibited] 

#### Req 2   System accounts, machine-to-machine users and functional accounts may only have a dedicated list of commands allowed.
[or in other words: `ALL` as command is prohibited]

#### Req 3   Commands must be given fixed parameters if these parameters are able to change system settings or files in an unwanted manner.
[no read/write of arbitrary files, e.g. tcpdump, awk, sed. Use “” to prohibit from using parameters]

#### Req 4   Commands that a user can execute must not be writable by this user, and must be stored in a directory, the user has no write permissions to.
[no commands in own home or other directory with write permissions]

#### Req 5   Execution of further processes from within an allowed command must not be possible.
[use `noexec` for all these commands, e.g. scp or paginators like more, less]

#### Req 6   For editing files, only `sudoedit` may be allowed with dedicated listed files.
[avoid exec from editors, e.g. vi; no editing of arbitrary files]

#### Req 7   The flag `authenticate` can be set to `off`  - or -   the tag `NOPASSWD:` can be used.
[assumption: passwords for user accounts are not available as a 2nd authenticator]

#### Req 8   If direct root login via ssh is possible, there should be no way to gain a root-shell via sudo.
[therefore no need of `sudo bash`, `sudo -i`, `sudo -s`, `sudo su`, `sudo sudo`, …]

#### Req 9   If direct root login via ssh is not possible, only members of a named group may get a root-shell via sudo. Usage of rootsh is preferred to log activities.
[Use a group, e.g. `%admin`, and permit commands like `bash`, `ALL` or similar. Logging is not bulletproof!]

#### Req 10   The flag `env_reset` must not be set to off.
[Use `Default_Entry`, to avoid attacks by environment variables]

#### Req 11   The flag `secure_path` must be set to `/bin:/sbin:/usr/bin:/usr/sbin`.
[Use `Default_Entry`, to ensure proper `PATH` when executing command]

#### Req 12   Including files within sudoers by `#include` and `#includedir` must be handled carefully.
[Take care of the content of all files in `/etc/sudoers.d` and test all permissions with `sudo -l <user>`]

#### Req 13   If logging of sudo activities is required, the logs should immediately be forwarded to another system.
[local logs cannot be trusted]

#### Req 14   Changes of the sudoers file should be tracked.
[to detect unauthorized changes: sudoers controlled by puppet, chef, ansible, … versioning with svn, git, … comparison of current state vs. target state]


sudo - the devil at your side!
