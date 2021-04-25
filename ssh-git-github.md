# Connecting to GitHub with SSH

- [Connecting to GitHub with SSH](#connecting-to-github-with-ssh)
    - [Checking for existing SSH keys <a id="checking-for-existing-ssh-keys"></a>](#checking-for-existing-ssh-keys-)
    - [Generate a new SSH key <a id="generating-a-new-ssh-key"></a>](#generate-a-new-ssh-key-)
    - [Adding your SSH key to the ssh-agent <a id="adding-ssh-key-to-the-ssh-agent"></a>](#adding-your-ssh-key-to-the-ssh-agent-)
    - [Add the SSH key to your GitHub account <a id="adding-ssh-key-to-your-github-account-"></a>](#add-the-ssh-key-to-your-github-account-)
    - [Working with SSH key passphrases <a id="working-with-ssh-key-passphrases"></a>](#working-with-ssh-key-passphrases-)
      - [Adding or changing a passphrase](#adding-or-changing-a-passphrase)
      - [Saving your passphrase in the keychain](#saving-your-passphrase-in-the-keychain)

### Checking for existing SSH keys <a id="checking-for-existing-ssh-keys"></a>

Before you generate an SSH key, you can check to see if you have any existing SSH keys.


1. Open Terminal.
   
2. Enter `ls -al ~/.ssh` to see if existing SSH keys are present:

  ```shell
  $ ls -al ~/.ssh
  # Lists the files in your .ssh directory, if they exist
  ```
3. Check the directory listing to see if you already have a public SSH key. By default, the filenames of the public keys are one of the following:
    - *id_rsa.pub*
    - *id_ecdsa.pub*
    - *id_ed25519.pub*

If you don't have an existing public and private key pair, or don't wish to use any that are available to connect to git server, then [generate a new SSH key](#generating-a-new-ssh-key).

If you see an existing public and private key pair listed (for example *id_rsa.pub* and *id_rsa*) that you would like to use to connect to gihub, you can [add your SSH key to the ssh-agent](#adding-your-ssh-key-to-the-ssh-agent-).

**Tip:** If you receive an error that *~/.ssh* doesn't exist, don't worry! We'll create it when we [generate a new SSH key](#generate-a-new-ssh-key-).

### Generate a new SSH key <a id="generating-a-new-ssh-key"></a>

1. Paste the text below, substituting in your email address.
  ```shell
  $ ssh-keygen -t ed25519 -C "<em>your_email@example.com</em>"
  ```
 
  This creates a new ssh key, using the provided email as a label.
  ```shell
  > Generating public/private ed25519 key pair.
  ```
2. When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location.

  ```shell
  > Enter a file in which to save the key (/Users/you/.ssh/id_ed25519): [Press enter]
  ```

3. At the prompt, type a secure passphrase. For more information, see ["Working with SSH key passphrases"](#working-with-ssh-key-passphrases-).
  ```shell
  > Enter passphrase (empty for no passphrase): [Type a passphrase]
  > Enter same passphrase again: [Type passphrase again]
  ```

### Adding your SSH key to the ssh-agent <a id="adding-ssh-key-to-the-ssh-agent"></a>

Before adding a new SSH key to the ssh-agent to manage your keys, you should have [checked for existing SSH keys](#checking-for-existing-ssh-keys-) and [generated a new SSH key](#generate-a-new-ssh-key-). 
<span class="platform-mac">When adding your SSH key to the agent, use the default macOS `ssh-add` command, and not an application installed by [macports](https://www.macports.org/), [homebrew](http://brew.sh/), or some other external source.</span>


1. Start the ssh-agent in the background.
   ```shell
    $ eval "$(ssh-agent -s)"
    > Agent pid 59566
   ```

2. If you're using macOS Sierra 10.12.2 or later, you will need to modify your `~/.ssh/config` file to automatically load keys into the ssh-agent and store passphrases in your keychain.

    * First, check to see if your `~/.ssh/config` file exists in the default location.

      ```shell
      $ open ~/.ssh/config
      > The file /Users/<em>you</em>/.ssh/config does not exist.
      ```

    * If the file doesn't exist, create the file.

      ```shell
      $ touch ~/.ssh/config
      ```

    * Open your `~/.ssh/config` file, then modify the file to contain the following lines. If your SSH key file has a different name or path than the example code, modify the filename or path to match your current setup. 

      ```
      Host *
        AddKeysToAgent yes
        UseKeychain yes
        IdentityFile ~/.ssh/id_ed25519
      ```

     **Note:** If you chose not to add a passphrase to your key, you should omit the `UseKeychain` line.
  
  
3. Add your SSH private key to the ssh-agent and store your passphrase in the keychain.
   ```shell
   $ ssh-add -K ~/.ssh/id_ed25519
  ```

### Add the SSH key to your GitHub account <a id="adding-ssh-key-to-your-github-account-"></a>

1. Copy the SSH public key to your clipboard.

  If your SSH public key file has a different name than the example code, modify the filename to match your current setup. When copying your key, don't add any newlines or whitespace.

  ```shell
  $ pbcopy &lt; ~/.ssh/id_ed25519.pub
  # Copies the contents of the id_ed25519.pub file to your clipboard
  ```

  **Tip:** If `pbcopy` isn't working, you can locate the hidden `.ssh` folder, open the file in your favorite text editor, and copy it to your clipboard.

2. On Github go to Settings > SSH and GPG keys. Click 'New SSH key or Add SSH key'. Add a descriptive label for the new key. Paste your key into the "Key" field.

### Working with SSH key passphrases <a id="working-with-ssh-key-passphrases"></a>

With SSH keys, if someone gains access to your computer, they also gain access to every system that uses that key. To add an extra layer of security, you can add a passphrase to your SSH key. You can use `ssh-agent` to securely save your passphrase so you don't have to reenter it.

#### Adding or changing a passphrase

You can change the passphrase for an existing private key without regenerating the keypair by typing the following command:

```shell
$ ssh-keygen -p -f ~/.ssh/id_ed25519
> Enter old passphrase: <em>[Type old passphrase]</em>
> Key has comment '<em>your_email@example.com</em>'
> Enter new passphrase (empty for no passphrase): <em>[Type new passphrase]</em>
> Enter same passphrase again: <em>[Repeat the new passphrase]</em>
> Your identification has been saved with the new passphrase.
```

If your key already has a passphrase, you will be prompted to enter it before you can change to a new passphrase.

#### Saving your passphrase in the keychain

On OS X Leopard through OS X El Capitan, these default private key files are handled automatically:

- *.ssh/id_rsa*
- *.ssh/identity*

The first time you use your key, you will be prompted to enter your passphrase. If you choose to save the passphrase with your keychain, you won't have to enter it again.

Otherwise, you can store your passphrase in the keychain when you add your key to the ssh-agent. For more information, see "[Adding your SSH key to the ssh-agent](#adding-your-ssh-key-to-the-ssh-agent-)."
