## Setting Up SSH Key for GitHub

### Step 1: Test SSH Access

Run the following command to test your SSH connection to GitHub:
```sh
ssh -T git@github.com
```
If you receive the following message:
```
git@github.com: Permission denied (public key).
```
proceed to the next steps to generate a new SSH key.

### Step 2: Check for an Existing SSH Key

Run this command to check if an SSH key already exists:
```sh
cat ~/.ssh/id_rsa.pub
```
If you don't see anything, generate a new key. If you see the key, skip to **Step 4**.

### Step 3: Generate a New SSH Key

Generate a new SSH key using the following command:
```sh
ssh-keygen -t rsa -b 4096 -C "your_github@email.com"
```
You'll get a few prompts. Confirm the default file location by pressing Enter. If you don't want to enter your credentials every time you push to GitHub, hit Enter twice more to leave the passphrase blank.

### Step 4: Copy the SSH Key to Clipboard

For macOS:
```sh
cat ~/.ssh/id_rsa.pub | pbcopy
```
For Linux (requires `xclip`):
```sh
cat ~/.ssh/id_rsa.pub | xclip -sel clip
```
Alternatively, you can open the file and copy the key manually:
```sh
code ~/.ssh/id_rsa.pub
```

### Step 5: Add SSH Key to GitHub

1. Login to GitHub and navigate to **Settings** > **SSH and GPG keys**.
2. Click the **New SSH key** button.
3. In the "Title" field, add a descriptive label for the new key. For example, "Personal MacBook Air".
4. Paste your SSH key into the "Key" field. The key should be in your clipboard from the previous step.
5. Click the **Add SSH key** button. You may be asked to confirm your password.

### Step 6: Test SSH Connection to GitHub

Run the following command again to test the SSH connection:
```sh
ssh -T git@github.com
```
If all is well, you'll see something like:
```
Hi [your_username]! You've successfully authenticated, but GitHub does not provide shell access.
```

And you're good to go! 👍