PRACTICAL SYSTEMS LABS

Linux System Administration

Five guided labs in shell evidence handling, access control, packages, services, and SSH

REPORT METADATA

DETAIL

Learner

[Enter name]

Course / programme

[Enter course or programme]

Institution

[Enter institution]

Lab environment

[Distribution, version, VM or host]

Lab dates

[Enter dates]

Instructor / assessor

[Enter name]

Report status

Practical report template; enter observed results and attach evidence

Purpose

This report documents five foundational Linux administration exercises. Together, the labs practice organizing system evidence, managing identities and permissions, handling packages, inspecting processes and services, and reviewing SSH authentication activity. The report provides a repeatable workflow and evidence checklist; measured outcomes must be completed from the learner’s own lab session.


Training report  •  Complete evidence fields after execution

LINUX SYSTEM ADMINISTRATION  |  PRACTICAL LAB REPORT

## 2. Executive summary

The five-lab sequence develops practical familiarity with routine Linux administration tasks and the evidence needed to verify them. The work progresses from read-only inspection and file organization to privileged account, package, service, and remote-access operations. Successful completion should be demonstrated with command output, screenshots or log excerpts recorded in the appendices.

No execution transcript, host details, or measured results accompanied the lab instructions. Accordingly, this report records the planned procedures and expected verification points but does not claim that commands were run or that any system state changed. Complete the result fields after running the labs in an authorized, isolated environment.

LAB

FOCUS

PRIMARY VERIFICATION

1

Structured evidence workspace

Directory tree and captured system information

2

Users, groups, ownership, modes

Identity/group membership and permission listing

3

Package lifecycle

Installed package/version and removal state

4

Processes and systemd service

Process observations and service state

5

SSH and authentication logs

Connection outcome and matching log/history records

## 3. Objectives

The overall objective is to perform and verify common Linux administration tasks from the shell. By the end of the sequence, the learner should be able to:

Create a structured evidence directory and capture command output using redirection.

Create and inspect users and groups, assign ownership, and apply least-privilege permission modes.

Refresh package metadata, install a utility, confirm it is available, and remove it safely.

Inspect processes and use systemd to check and manage a service.

Connect to a private lab host over SSH, transfer a file, and correlate authentication activity with system logs.

Record outcomes with enough context for another person to review and reproduce them.

## 4. Scope and limitations

Scope includes the five supplied introductory labs and the core commands listed in the source material. The examples assume a Linux host with a shell, sudo access for administrative tasks, and, for Lab 5, a private lab network with an authorized SSH server and test account.

Commands and service names vary by distribution and configuration. Ubuntu/Debian examples use apt and commonly use the ssh service name and /var/log/auth.log. RHEL-family systems use dnf and commonly write authentication events to /var/log/secure; the service may be named sshd. systemd is assumed. Some environments use journald only, managed package policies, or different service names.

This is a training workflow, not a production change plan or security audit. No actual system, network address, package version, login event, result, or risk rating was supplied. Do not run account, package, service, or SSH operations on systems without authorization. Stopping or restarting SSH on a remote machine may disconnect access; maintain console or alternate access in a lab.

## 5. Environment and tools

ITEM

RECORD BEFORE EXECUTION

Distribution and release

[e.g., Ubuntu 24.04 / Rocky Linux 9]

Kernel and architecture

[Capture with uname -a]

Hostname

[Capture with hostname]

Lab topology

[VMs, private subnet, host roles]

Shell and privilege model

[Bash, sudo account, access method]

Package manager

[apt / dnf / other]

Service manager

[systemd / other]

Evidence location

[Absolute path; restrict access if logs contain personal data]

Tools referenced include a terminal, mkdir, pwd, ls, hostname, uname, df, tree, useradd, passwd, groupadd, usermod, id, chown, chmod, apt or dnf, which, ps, top, pgrep, systemctl, ssh, scp, grep, tail, and last. The tree utility may need to be installed; if unavailable, use ls -R as a documented alternative.

## 6. Methodology

Perform each lab on a disposable or explicitly assigned training host. Record the date, host, account used, commands entered, exit status where useful, and observed output. Redact passwords, private keys, tokens, and unrelated personal data before storing or sharing evidence. Preserve original log timestamps and avoid editing captured outputs.

Lab 1  Build a structured evidence workspace

Create the directory structure, confirm the working location, and capture basic host and storage information. Use append redirection for subsequent command output.

mkdir -p ~/evidence/{logs,configs,reports}
cd ~/evidence
pwd
ls -la
hostname > reports/system_info.txt
uname -a >> reports/system_info.txt
df -h >> reports/system_info.txt
tree ~/evidence

Expected verification: the logs, configs, and reports directories exist; system_info.txt contains output from hostname, uname, and df. Record the tree output or ls -R equivalent.

Lab 2  Manage users, groups, ownership, and modes

Use an authorized lab account with sudo. Confirm whether the analyst user or investigators group already exists before creating them. passwd is interactive and must not be captured in evidence.

sudo groupadd investigators
sudo useradd -m analyst
sudo passwd analyst
sudo usermod -aG investigators analyst
sudo chown analyst:investigators report.txt
chmod 640 report.txt
chmod 750 evidence/
id analyst
ls -l report.txt evidence/

If the account or group already exists, inspect it and adapt the procedure rather than repeating creation commands. The example chown target must exist. Verify supplementary group membership in a fresh analyst login session when required.

Lab 3  Install, update, verify, and remove a package

Choose the command set matching the distribution. Review proposed package changes before confirming them. A full upgrade can change many packages, so in a constrained lab install and remove only the requested utility unless the exercise specifically requires a system upgrade.

Ubuntu/Debian
sudo apt update
sudo apt install tree
which tree
tree --version
sudo apt remove tree

RHEL/CentOS/Fedora family
sudo dnf install tree
tree --version
sudo dnf remove tree

If performing an upgrade exercise, use the approved command for the distribution and capture the package manager’s transaction summary. Avoid running apt upgrade or dnf update on an uncontrolled production host.

Lab 4  Inspect processes and manage one service

Observe process state, identify the configured SSH service unit, then check and manage only the service assigned by the instructor. Service actions require sudo and may affect active users.

ps aux
top
pgrep -a sshd
systemctl status ssh
sudo systemctl stop ssh
sudo systemctl start ssh
sudo systemctl restart ssh
systemctl is-active ssh
systemctl status ssh

On systems where the unit is sshd, substitute sshd consistently. Enable at boot only if the lab requires persistence: sudo systemctl enable ssh. Capture status before and after each requested action, and record failures verbatim.

Lab 5  Connect over SSH and review authentication records

Use the private lab address and credentials or key issued for the exercise. Verify the server identity using the lab’s trusted host-key process. Do not disable host-key checking as a shortcut.

ssh analyst@192.168.1.10
scp report.txt analyst@192.168.1.10:/home/analyst/
sudo grep sshd /var/log/auth.log
sudo tail -n 50 /var/log/auth.log
last

On RHEL-family systems, inspect /var/log/secure. If files are unavailable, use journalctl for the SSH service, for example: sudo journalctl -u ssh --since today. For failed-login evidence, use only a controlled test account and a deliberately incorrect test password if explicitly assigned; do not repeatedly guess credentials.

## 7. Results and evidence

Enter observed results after execution. A successful result means the command completed and its state was verified using the checks below; command output alone may not prove persistence or remote effect.

LAB

OBSERVED RESULT

EVIDENCE TO ATTACH

STATUS

1

[Directory path and captured files]

Tree/ls output; system_info.txt

[Pass / needs review]

2

[User, group, owner and modes observed]

id output; ls -l; relevant config-free evidence

[Pass / needs review]

3

[Package installed/version/removal]

Package transaction and version output

[Pass / needs review]

4

[Process and service states]

ps/pgrep and systemctl status output

[Pass / needs review]

5

[SSH/scp outcome and log correlation]

Sanitized client result and matching server log lines

[Pass / needs review]

Lab execution record: date/time [ ]; host [ ]; operator [ ]; errors or deviations [ ]; evidence filenames [ ]. Do not include passwords, private keys, or unredacted secrets.

## 8. Analysis

The exercises connect administration actions to observable system state. Lab 1 establishes a repeatable evidence location and demonstrates the difference between replacing output with > and appending with >>. Lab 2 links identity, group membership, ownership, and mode bits; these controls should be checked together because ownership and permissions jointly determine access.

Lab 3 highlights the package manager as the source of installation and removal state, while version checks confirm that a command resolves to an executable. Lab 4 distinguishes process inspection from service management: a process may exist without the expected service being enabled, and a service unit can be active while an application-level check still fails. Lab 5 correlates client-side connection evidence with server-side authentication records and login history.

Complete this section with evidence-based interpretation: [Which checks succeeded? Which differed from the expected state? What explains any discrepancy? What additional command would confirm the finding?] Do not infer successful authentication solely from a client command being issued; correlate timestamps and the server-side record.

## 9. Risk and impact

AREA

POTENTIAL IMPACT

CONTROL

Account and group changes

Unintended access or duplicate account state

Check existing identities; use approved names; verify membership and scope.

Permissions and ownership

Excessive access or accidental lockout

Use least privilege; inspect target path and resulting mode/owner.

Package update/removal

Dependency changes or removal of needed software

Review transaction preview; use disposable lab host; record package state.

Service stop/restart

Loss of service or remote session

Confirm target unit; use console/alternate access; act only in assigned lab.

SSH and log review

Credential exposure or sensitive log disclosure

Use authorized credentials; protect evidence; redact secrets and unrelated identities.

Risk ratings depend on the host’s role, exposure, and recovery options. Assign likelihood and impact using the course or organization’s rubric after reviewing the actual environment.

## 10. Mitigation and recommendations

Run the exercises in snapshots or disposable VMs and revert the environment after assessment when appropriate.

Use a dedicated, non-production analyst account and grant only the group membership required by the exercise.

Capture command, timestamp, host, and verification output together; keep an original copy and a redacted sharing copy when logs contain sensitive data.

Check package manager transaction details before applying updates or removals.

Confirm the exact systemd unit name and maintain a recovery path before changing remote-access services.

Use SSH host-key verification and key-based authentication where the lab supports it; protect private keys and avoid placing secrets in report evidence.

After each lab, compare the observed state with the expected state and document any deviations and corrective action.

## 11. Conclusion

These five labs provide a coherent introduction to Linux command-line administration, access control, package handling, service operations, and SSH log review. The workflow emphasizes verification and evidence alongside execution. The practical conclusions for this run should be finalized only after the learner records the actual environment, command outcomes, and supporting evidence.

## 12. References

The procedures and objectives in this report are based on the lab instructions supplied for this assignment. Consult the installed distribution’s documentation and local man pages for version-specific behavior.

man mkdir, man useradd, man usermod, man chmod, man chown, man apt, man dnf, man systemctl, man ssh, man scp, man journalctl, man last.

System documentation available on the target distribution, including package manager and systemd documentation for the installed release.

## 13. Appendices

Appendix A  Evidence inventory

ID

FILE / SCREENSHOT

DESCRIPTION

REDACTED

A1

[path or filename]

Lab 1 directory tree and system info

[Yes/No]

A2

[path or filename]

Lab 2 user/group and permission verification

[Yes/No]

A3

[path or filename]

Lab 3 package verification

[Yes/No]

A4

[path or filename]

Lab 4 process/service verification

[Yes/No]

A5

[path or filename]

Lab 5 connection/log evidence

[Yes/No]

Appendix B  Command assessment checklist

COMMAND GROUP

PRACTICE CHECK

Navigation and files

mkdir, cd, pwd, ls, cp, mv, rm; confirm paths before modifying files.

Permissions and identity

chmod, chown, useradd, passwd, usermod, groupadd, id.

Packages

apt or dnf; inspect transaction and verify executable/version.

Processes and services

ps, top, pgrep, systemctl; verify status after requested action.

Remote access and logs

ssh, scp, grep, tail, last; correlate event time and host.

Appendix C  Result completion prompts

What command output proves each objective was met?

Were any commands unavailable, denied, or different on this distribution?

Did the observed state persist after a new login or service check where relevant?

Which evidence items were redacted, and why?

What would you change in a repeat run to make the evidence clearer?

Train