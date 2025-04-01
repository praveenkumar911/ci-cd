
# ADR-001: SSHFS Assistant Configuration Enhancement

> **Status**: Accepted  
> **Date**: [Insert Date]  

---

## Context

The current implementation of SSHFS allows users to mount and unmount SFTP sessions but requires them to manually specify mount paths each time. This process can be cumbersome and error-prone, especially for users who frequently connect to multiple SFTP servers. Additionally, repetitive manual inputs reduce efficiency and increase the likelihood of mistakes, such as typos or incorrect paths.

To address these challenges, there is a clear organizational need to enhance the SSHFS experience by automating the mounting and unmounting process. The proposed solution leverages the existing SSH configuration (`~/.ssh/config`) to streamline workflows and reduce user effort.

---

## Decision Drivers

- **Efficiency**: Reduce the number of manual steps required to mount or unmount SFTP sessions.  
- **Error Reduction**: Minimize the risk of errors caused by manual input (e.g., typos or incorrect paths).  
- **User Experience**: Improve usability for both technical and non-technical users.  
- **Portability**: Ensure the solution works across different environments and integrates seamlessly with existing tools.  
- **Maintainability**: Avoid introducing unnecessary complexity or dependencies that could hinder future maintenance.

---

## Decision Outcome

### Chosen Option: Global npm Command (`sshfsa`)

We propose creating a global npm command named `sshfsa` (SSHFS Assistant) to automate the mounting and unmounting of SFTP sessions. This command will leverage the user's existing SSH configuration (`~/.ssh/config`) to determine mount paths and connection details, significantly reducing the need for manual input.

#### Positive Consequences
- **Increased Efficiency**: Users save time by eliminating the need to repeatedly specify mount paths.  
- **Reduced Errors**: Automation mitigates risks associated with manual input, such as typos or incorrect paths.  
- **Improved Usability**: Simplified interaction enhances user satisfaction and reduces frustration.  
- **Consistency**: Centralized logic ensures consistent behavior across different environments and users.

#### Negative Consequences
- **Dependency on npm**: Users must have Node.js and npm installed, which may introduce complications for those unfamiliar with these tools.  
- **Learning Curve**: Existing SSHFS users may need time to adapt to the new command and workflow.  
- **Maintenance Overhead**: Ongoing support and updates for the `sshfsa` tool will require additional effort from the development team.

---

## Alternatives Considered

1. **No Change**  
   - Continue using the existing SSHFS tool without modifications.  
   - *Pros*: Immediate stability; no additional development effort required.  
   - *Cons*: Continues to burden users with manual path entry, limiting efficiency and increasing error rates.

2. **Script-based Solution**  
   - Create a shell script to automate the mounting process without relying on npm.  
   - *Pros*: Quick implementation; no dependency on npm.  
   - *Cons*: Limited portability across environments; potential for script management issues over time.

3. **GUI-based Tool**  
   - Develop a graphical user interface (GUI) for managing SSHFS sessions.  
   - *Pros*: User-friendly and visually appealing for non-technical users.  
   - *Cons*: Increased development time and complexity; overhead for users who prefer command-line tools.

---

## Implementation Details

1. **Global npm Command**:  
   - The `sshfsa` command will be installed globally via npm.  
   - Example usage:  
     ```bash
     sshfsa mount <alias>  # Mounts an SFTP session based on the alias in ~/.ssh/config
     sshfsa unmount <alias>  # Unmounts the specified SFTP session
     ```

2. **SSH Configuration Integration**:  
   - The tool will read connection details (e.g., hostname, username, and mount paths) from the user's `~/.ssh/config` file.  
   - Example SSH config entry:  
     ```text
     Host myserver
       HostName example.com
       User admin
       IdentityFile ~/.ssh/id_rsa
     ```

3. **Error Handling**:  
   - Provide clear error messages for common issues, such as missing aliases or invalid configurations.  
   - Log errors to a file for debugging purposes.

4. **Cross-Platform Support**:  
   - Ensure compatibility with major operating systems (Linux, macOS, Windows).

---

## Examples

### Mounting an SFTP Session
```bash
sshfsa mount myserver
```
This command mounts the SFTP session for the alias `myserver` defined in the user's `~/.ssh/config`.

### Unmounting an SFTP Session
```bash
sshfsa unmount myserver
```
This command unmounts the SFTP session for the alias `myserver`.

---

## Consequences

### Pros
- **Efficiency**: Reduces manual effort, enabling faster access to SFTP sessions.  
- **Reliability**: Automates path resolution, minimizing errors caused by manual input.  
- **User Satisfaction**: Simplifies workflows, improving overall user experience.  

### Cons
- **Dependency Management**: Requires users to have Node.js and npm installed, which may not be ideal for all environments.  
- **Adoption Challenges**: Existing SSHFS users may face a learning curve when transitioning to the new tool.  
- **Maintenance Overhead**: Ongoing support and updates for the `sshfsa` tool will require additional resources.

---

## Glossary

- **SSHFS**: Secure Shell Filesystem, a tool that allows remote filesystems to be mounted over SSH.  
- **npm**: Node Package Manager, a tool for managing JavaScript packages and dependencies.  
- **SFTP**: Secure File Transfer Protocol, a protocol used for transferring files securely over SSH.  
- **Alias**: A shorthand name for an SFTP server, defined in the user's `~/.ssh/config` file.
