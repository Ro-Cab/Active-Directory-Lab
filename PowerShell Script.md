# PowerShell Script: Create Active Directory Users from a List

This script automates creating Active Directory (AD) user accounts from a list of names in a text file. Each user is added to a `_USERS` Organizational Unit (OU) with a default password and basic settings.


```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"                  # Default password for all new users.
# This is a plain text password that will be converted into a secure string later.

$USER_FIRST_LAST_LIST = Get-Content .\names.txt     # Reads each line from "names.txt"
# Each line should contain a first and last name separated by a space, e.g., "John Smith".
# Get-Content reads the file into an array of strings, one string per line.
# ------------------------------------------------------ #

# Convert the plain-text password into a secure string required by AD
$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
# ConvertTo-SecureString ensures the password is stored securely in memory
# -AsPlainText tells PowerShell it’s coming from plain text
# -Force allows conversion without confirmation prompts

# Create a new Organizational Unit (OU) called "_USERS" if it does not exist
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false
# The OU acts as a container for the new users.
# -ProtectedFromAccidentalDeletion $false allows admins to delete the OU if needed.

# Loop through each name in the list
foreach ($n in $USER_FIRST_LAST_LIST) {

    # Split the line into first and last names and convert to lowercase
    $first = $n.Split(" ")[0].ToLower()   # First name
    $last  = $n.Split(" ")[1].ToLower()   # Last name
    # Split(" ") separates the string by space.
    # ToLower standardizes the naming convention for usernames.

    # Generate a username using first letter of first name + full last name
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    # Example: John Smith → jsmith
    # Substring(0,1) takes the first character of the first name.
    # Combining with the last name creates a simple, consistent username.

    # Output the username being created to the console
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    # Write-Host provides visual feedback while the script runs.

    # Create the new Active Directory user account
    New-AdUser -AccountPassword $password `             # Set the account password
               -GivenName $first `                     # Set the user's first name
               -Surname $last `                        # Set the user's last name
               -DisplayName $username `                # Set the display name in AD
               -Name $username `                       # Set the AD object name
               -EmployeeID $username `                 # Optionally set EmployeeID to username
               -PasswordNeverExpires $true `           # Password will not expire
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" ` # Place user in _USERS OU
               -Enabled $true                          # Enable the account immediately
    # New-ADUser creates the AD user object with all specified attributes.
    # Path ensures the user is placed inside the "_USERS" OU.
    # Backticks (`) allow splitting the command across multiple lines for readability.
}

```
<p align="center"><em>Thanks to <a href="https://www.youtube.com/c/JoshMadakor">Josh Madakor</a> for providing script as well as a visual walkthrough.</em></p>
