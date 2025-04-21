# User Management Script

This shell script provides a secure and user-friendly interface for managing user accounts on Ubuntu systems. It allows administrators to create, delete, list, lock, and unlock user accounts through an interactive menu.

## Features

- **Create Users**: Adds new users with home directories, passwords, and optional group membership.
- **Delete Users**: Removes users and their home directories after confirmation.
- **List Users**: Displays all system users.
- **Lock/Unlock Accounts**: Temporarily disables or re-enables user access.
- **Security Checks**: Ensures the script runs as root, prevents duplicate accounts, and validates usernames.
- **Password Security**: Sets passwords securely with confirmation prompts.
- **Interactive Menu**: User-friendly interface for easy operation.

## Prerequisites

- The script must be run with root privileges.
- It is designed for Ubuntu systems and may require modifications for other distributions.

## Usage

1. Make the script executable:
   ```bash
   chmod +x user_management.sh
   ```

2. Run the script as root:
   ```bash
   sudo ./user_management.sh
   ```

3. Follow the interactive menu to select an option:
   - **1)** Create a new user
   - **2)** Delete a user
   - **3)** List all users
   - **4)** Lock a user
   - **5)** Unlock a user
   - **6)** Exit

## Examples

### Creating a New User

1. Select option `1`.
2. Enter the username (e.g., `johndoe`).
3. Enter and confirm a password.
4. Optionally add the user to a group (e.g., `devops`).
   - **Outcome**: User `johndoe` is created with a home directory at `/home/johndoe`.

### Deleting a User

1. Select option `2`.
2. Enter the username (e.g., `johndoe`).
3. Confirm deletion with `y`.
   - **Outcome**: User `johndoe` and their home directory are removed.

### Listing Users

1. Select option `3`.
   - **Outcome**: Displays all usernames from `/etc/passwd`.

### Locking an Account

1. Select option `4`.
2. Enter the username (e.g., `johndoe`).
   - **Outcome**: User `johndoe` can no longer log in.

### Unlocking an Account

1. Select option `5`.
2. Enter the username (e.g., `johndoe`).
   - **Outcome**: User `johndoe` is re-enabled for login.

## Real-World Scenarios

### New Employee Onboarding

- **Task**: Create an account for a new engineer, `sarah`.
- **Steps**:
  1. Run `sudo ./user_management.sh`.
  2. Select `1`, enter `sarah`, set a password, and add to the `developers` group.
- **Outcome**: Sarah has a secure account with appropriate permissions.

### Managing Temporary Contractors

- **Task**: Add a contractor, `alice`, for a 2-month project.
- **Steps**:
  1. Create `alice` using option `1`.
  2. Set an account expiry date: `sudo chage -E 2025-05-01 alice`.
- **Outcome**: Alice’s access automatically expires, enhancing security.

### Handling Employee Departure

- **Task**: Remove `sarah` after resignation.
- **Steps**: Select `2`, enter `sarah`, confirm deletion.
- **Outcome**: Sarah’s account and data are securely removed.

### Suspending Accounts During Leave

- **Task**: Lock `mark`’s account during a leave of absence.
- **Steps**: Select `4`, enter `mark`.
- **Outcome**: Mark’s account is disabled until unlocked with option `5`.

## Security Considerations

- **Root Privileges**: The script must be run as root to perform user management tasks. Ensure that only authorized personnel have access to run the script.
- **Password Handling**: Passwords are set securely using `passwd`, and the script prompts for confirmation to prevent errors.
- **Username Validation**: The script checks for existing usernames to prevent duplicates and ensures that usernames meet system requirements.
