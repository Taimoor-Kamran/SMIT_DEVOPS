# Class 4: Service Users, Directory Ownership & Secure Permissions

---

## 1. What is a Service User?

A **service user** is a system account created specifically to run one application or service.  
It is not a human user — no one logs in with it.  
Its only purpose is to own and run a specific process with limited privileges.

### Why Use a Service User?

| Without Service User | With Service User |
|---------------------|------------------|
| Service runs as root | Service runs with least privilege |
| One breach = full system access | One breach = only that service's files |
| No audit trail per service | Clear ownership of files and processes |
| Dangerous in production | Standard secure practice |

### Characteristics of a Service User

- No home directory (or a restricted one like `/opt/appname`)
- No interactive login shell (`/usr/sbin/nologin` or `/bin/false`)
- UID in system range (1–999) using `-r` flag
- Owns only the files and directories its service needs

