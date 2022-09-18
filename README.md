# **gpg-signing-poc**

## **Steps to generate new GPG keys for GitHub:**

1. First, make sure you have the GPG CLI installed. You can check installation by running `gpg --version` in your CLI (i.e., GitBash). This may be included along with a GitBash installation, on native Linux distros, or through WSL. 

    CLI tools for GPG can also be downloaded directly from [GnuPG](https://www.gnupg.org/download/). If you use this options it's best to verify the integrity of the download with the provided signatures.

2. Now you can generate a new GPG key pair. There's also pretty good [GitHub documentation](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key#generating-a-gpg-key) on these steps which note some other requirements such as needing to run version `2.1.17` or later. The below steps assume you meet those requirements.

    Run `gpg --full-generate-key` to start the interactive process to generate a new GPG key pair. Initial prompts:

    ```
    Please select what kind of key you want:
       (1) RSA and RSA (default)
       (2) DSA and Elgamal
       (3) DSA (sign only)
       (4) RSA (sign only)
      (14) Existing key from card
    ```
    
    Either push `Enter` as this uses the default, or type in `1` and `Enter`. Both result in choosing the RSA key type (which is required by GitHub). Next prompts:
    
    ```
    RSA keys may be between 1024 and 4096 bits long.
    What keysize do you want? (3072)
    ```

    For the RSA key length, type in `4096` for the strongest key. GitHub also notes a requirement that the key     must be at least `4096` bits. Next prompts:
    
    ```
    Requested keysize is 4096 bits
    Please specify how long the key should be valid.
             0 = key does not expire
          <n>  = key expires in n days
          <n>w = key expires in n weeks
          <n>m = key expires in n months
          <n>y = key expires in n years
    Key is valid for? (0)
    ```
    
    You can optionally set an expiration date here or use the default or no expiration. Keys can have their expiration dates updated later so you don't have to go through the entire process of generating new keys when the key expires. Next prompts:
    
    ```
    Is this correct? (y/N)
    ```
    
    Simply confirm the choices up to this point are correct after reviewing them. You'll then be presented with     user ID inputs:
    - `Real name`: Simply type in use first/last name (i.e, "John Doe").
    - `Email address`: Make sure that you use a verified email account associated with your GitHub account! For     privacy reasonings, you can also use the `no-reply` email address on your account if you have opted to keep     your email address private. Failure to use a verified email account here may result in the verification not     working correctly on GitHub.
    - `Comment`: You can optionally put something here which can be seen later through `gpg` commands which     could be helpful if you have multiple GPG keys. Or you can leave this blank.
    
    <br/>
    
    You'll then need to confirm via `O` for `(O)kay` and you want to proceed. Next you'll need to enter a     passprhase and confirm that passphrase. Once this is complete the key will start generating and it can be     recommendated to improve entropy to move you mouse around or type on the keyboard.gpg

## **Fetching key information and importing into GitHub account:**

1. To display your recently created GPG key you can run `gpg --list-secret-keys --keyid-format=long`. Specifying the format flag will include additional information.

2. Fetch the GPG key ID. See [step 11 in the GitHub docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key#generating-a-gpg-key) for where the key is displayed in the list keys output.

3. Run the `gpg --armor --export <ENTER_GPG_KEY_ID>` command to get the GPG public key block.
    - `--armor`: ASCII armored output.
    - `--export`: Exports keys.

    A public key block should now be displayed.

4. Copy the text displayed between `-----BEGIN PGP PUBLIC KEY BLOCK-----` and `-----END PGP PUBLIC KEY BLOCK-----`. This should really just be the whole output anyways.

5. Then [add a new GPG key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account) and paste in the public key block from the previous steps. You can have multiple GPG keys in your GitHub account.

## **Configuring GitBash for automatically signing commits:**

There are arguments you can provide when commiting or tagging to make sure signing is performed. But you'd have to remember to do this everytime. For conveinence, it's better to automate this.

> Note: To know where your Git configuration files are located, you can run `git config --list --show-origin`.

You can check if there's any GPG signing configuration set already (that we're about to set) you can run `git config --list | grep -E "user.signingkey|commit.gpgsign|tag.gpgsign"`. If you're just setting up GPG for GitHub, there will likely be no output here. 

Now we're going to set these config settings we just checked on. Run the following commands to set the GPG signing key and enable auto-signing for commits and tagging:

> Reminder: You can run `gpg --list-secret-keys --keyid-format=long` to get the GPG key ID for the key you want to use.

- `git config --global user.signingkey <ENTER_GPG_KEY_ID>`
- `git config --global commit.gpgsign true`
- `git config --global tag.gpgsign true`

Now for commits/tags using GitBash you shouldn't need to specify the arguments to manually sign. See  [signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits) and [signing tags](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-tags) for more information.

> Note: For GitBash usage on Windows, the GitBash base directory is typically at `C:\Program Files\Git\`. The gpg executable is then typically at `C:\Program Files\Git\usr\bin\`. To view the files created through key generation, you can look at the gpg home directory. You can find this by running `gpg --version` and the output will show the location to the gpg home directory.

## **Resources:**

GitHub docs:
- [Generating a GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)
- [Tell Git your signing key](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)
- [Add a GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account)
- [Displaying verification for all commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/displaying-verification-statuses-for-all-of-your-commits)