# Script Explanation

The `user_management.sh` script is a Bash shell script intended for system administrators or users with root privileges on Ubuntu systems. It provides an interactive menu-driven interface to perform common user management tasks securely and efficiently.

### Key Features

- **Create Users**: Adds new users with home directories, passwords, and optional group assignments.
- **Delete Users**: Removes users and their home directories with confirmation.
- **List Users**: Displays all system users from `/etc/passwd`.
- **Lock/Unlock Accounts**: Disables or re-enables user login access.
- **Security Checks**: Ensures root execution, validates input, and prevents errors like duplicate users.
- **Password Security**: Prompts for password confirmation and sets it securely.

## Script Breakdown

### 1. Shebang and Script Information

```bash
#!/bin/bash

# Script Name: user_management.sh
# Description: Manage users in Ubuntu (Create, Delete, List, Lock, Unlock)
# Usage: Run the script as root and choose an operation
```

- **Shebang (`#!/bin/bash`)**: Indicates the script should run in the Bash shell.
- **Comments**: Provide metadata about the script’s purpose and usage.

### 2. Function: `check_root`

```bash
check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "Error: This script must be run as root." >&2
        exit 1
    fi
}
```

- **Purpose**: Verifies that the script is executed with root privileges.
- **Details**:
  - `$EUID` checks the effective user ID; root has an ID of 0.
  - If not root, it outputs an error to stderr (`>&2`) and exits with status 1.

### 3. Function: `create_user`

```bash
create_user() {
    while true; do
        read -p "Enter username to create: " username
        if [[ -z "$username" ]]; then
            echo "Username cannot be empty."
            continue
        fi
        if ! [[ "$username" =~ ^[a-z][a-z0-9_]*$ ]]; then
            echo "Invalid username. Must start with a letter and contain only lowercase letters, numbers, and underscores."
            continue
        fi
        if id "$username" &>/dev/null; then
            echo "User '$username' already exists."
            continue
        fi
        break
    done

    while true; do
        read -s -p "Enter password for $username: " password
        echo
        read -s -p "Confirm password: " password_confirm
        echo
        if [[ "$password" == "$password_confirm" ]]; then
            break
        else
            echo "Passwords do not match. Please try again."
        fi
    done

    useradd -m -s /bin/bash "$username"
    echo "$username:$password" | chpasswd
    echo "User '$username' created successfully."

    read -p "Add user to a group? (y/n): " add_group
    if [[ "$add_group" == "y" ]]; then
        read -p "Enter group name: " groupname
        if grep -q "^$groupname:" /etc/group; then
            usermod -aG "$groupname" "$username"
            echo "User '$username' added to group '$groupname'."
        else
            echo "Group '$groupname' does not exist."
        fi
    fi
}
```

- **Purpose**: Creates a new user with a home directory, password, and optional group membership.
- **Details**:
  - **Username Validation**:
    - Ensures the username isn’t empty (`-z`).
    - Uses a regex (`^[a-z][a-z0-9_]*$`) to enforce starting with a letter and allowing only lowercase letters, numbers, and underscores.
    - Checks for existing users with `id`.
  - **Password Handling**:
    - Prompts for a password (`-s` hides input) and confirms it matches.
  - **User Creation**:
    - `useradd -m -s /bin/bash` creates the user with a home directory and Bash shell.
    - `chpasswd` sets the password securely.
  - **Group Assignment**:
    - Optionally adds the user to a group if it exists in `/etc/group`, using `usermod -aG`.

### 4. Function: `delete_user`

```bash
delete_user() {
    read -p "Enter username to delete: " username
    if ! id "$username" &>/dev/null; then
        echo "User '$username' does not exist."
        return
    fi
    read -p "Are you sure you want to delete user '$username' and their home directory? (y/n): " confirm
    if [[ "$confirm" == "y" ]]; then
        userdel -r "$username"
        echo "User '$username' deleted successfully."
    else
        echo "User deletion aborted."
    fi
}
```

- **Purpose**: Deletes a user and their home directory after confirmation.
- **Details**:
  - Verifies the user exists with `id`.
  - Prompts for confirmation to prevent accidental deletion.
  - `userdel -r` removes the user and their home directory.

### 5. Function: `list_users`

```bash
list_users() {
    echo "Listing all system users:"
    awk -F':' '{ print $1 }' /etc/passwd
}
```

- **Purpose**: Lists all users on the system.
- **Details**:
  - Uses `awk` to parse `/etc/passwd`, extracting and printing usernames (first field, separated by `:`).

### 6. Function: `lock_user`

```bash
lock_user() {
    read -p "Enter username to lock: " username
    if id "$username" &>/dev/null; then
        passwd -l "$username"
        echo "User '$username' has been locked."
    else
        echo "User '$username' does not exist."
    fi
}
```

- **Purpose**: Locks a user account to prevent login.
- **Details**:
  - Checks if the user exists.
  - `passwd -l` locks the account by modifying the password field in `/etc/shadow`.

### 7. Function: `unlock_user`

```bash
unlock_user() {
    read -p "Enter username to unlock: " username
    if id "$username" &>/dev/null; then
        passwd -u "$username"
        echo "User '$username' has been unlocked."
    else
        echo "User '$username' does not exist."
    fi
}
```

- **Purpose**: Unlocks a user account to restore login access.
- **Details**:
  - Verifies the user exists.
  - `passwd -u` unlocks the account.

### 8. Function: `show_menu`

```bash
show_menu() {
    echo "--------------------------------------"
    echo " Ubuntu User Management Script "
    echo "--------------------------------------"
    echo "1) Create a new user"
    echo "2) Delete a user"
    echo "3) List all users"
    echo "4) Lock a user"
    echo "5) Unlock a user"
    echo "6) Exit"
    echo "--------------------------------------"
}
```

- **Purpose**: Displays the interactive menu.
- **Details**:
  - Prints a formatted list of options for user selection.

### 9. Main Script Execution

```bash
check_root
while true; do
    show_menu
    read -p "Choose an option: " choice
    case $choice in
        1) create_user ;;
        2) delete_user ;;
        3) list_users ;;
        4) lock_user ;;
        5) unlock_user ;;
        6) echo "Exiting..."; exit 0 ;;
        *) echo "Invalid option. Please select a valid choice." ;;
    esac
done
```

- **Purpose**: Orchestrates the script’s execution flow.
- **Details**:
  - Calls `check_root` to enforce root privileges.
  - Runs an infinite loop displaying the menu and processing user input.
  - Uses a `case` statement to call the appropriate function based on the choice.
  - Exits cleanly with option 6.

## Security Considerations

- **Root Requirement**: Only root can execute sensitive commands.
- **Input Validation**: Ensures usernames are valid and unique.
- **Password Safety**: Confirms passwords and uses `chpasswd` for secure setting.
- **Deletion Confirmation**: Prevents accidental user removal.
- **Error Handling**: Provides clear feedback for invalid inputs or non-existent users/groups.

## Usage Instructions

1. **Run the Script**:
   - Use `sudo ./user_management.sh` to execute with root privileges.
2. **Select an Option**:
   - Enter a number (1-6) from the menu.
3. **Follow Prompts**:
   - Provide usernames, passwords, or confirmations as requested.
4. **Exit**:
   - Choose option 6 to terminate the script.

This script offers a robust, user-friendly solution for managing user accounts on Ubuntu, balancing functionality with security and ease of use.
