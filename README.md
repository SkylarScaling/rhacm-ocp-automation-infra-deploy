# Introduction
This repository contains the configurations for deploying OpenShift clusters. Each cluster is separated by a top level directory.

# Updating Encrypted Values
> **IMPORTANT:** Sensitive credentials MUST be kept encrypted in repositories, and the encryption password must NOT be stored in any repository.

There are several credentials stored in `ocp-infra-deploy` in order to facilitate automation tasks without user input. 

These credentials will expire or otherwise need to be updated/replaced periodically. 

In order to update encrypted values in `inventory` or `all.yaml` files, it is recommended to follow the following process:

1. Place value to be encrypted into a temporary local file.
    
    Create a simple file on your local system (e.g. temp.txt) and paste the value to be encrypted in the file:
    
    ```
    value-to-be-encrypted 
    ```

2. Encrypt the file.

    Run the following command to encrypt the value:
    ```
    $ ansible-vault encrypt temp.txt --vault-password-file=~/.passfile
    ```
    > NOTE: You will need Ansible installed and a ~/.passfile created as per the README in the `ocp-infra-lib` repository.

3. Copy the contents of the encrypted file into the destination file, replacing existing values as needed.

    The contents of the file will look similar to this:
    ```
    $ANSIBLE_VAULT;1.1;AES256
    36326638643163303430666231323036623365643437633039613331343132343566656363373464
    3135613866316463393935343535316335633436353366640a393631343435316534643934626136
    66366435303161306165656464656538616533316634313164643062393165656336336330336262
    3934343938653732300a636564383562343238313536616139386234383862376661343730356636
    ...<snip>...
    ```

    Paste the content of the file into the appropriate location, leaving `<var_name>: !vault |` intact, as appropriate.

    > **IMPORTANT:** Indentation matters in yaml, so be sure to maintain the same indentation when replacing values.

4. Commit the updated files.

    Commit the changed files in `ocp-infra-deploy` and create a PR so the updated values can be used by anyone that pulls the repo.
    
5. As necessary, update in-place credentials.

    The values in this repository are used during cluster *creation*. If the tokens used in existing clusters have expired 
    and require updates, you will need to log into each affected cluster and update Secret values manually.
    Values in deployed Secrets are not encrypted, so values can be replaced directly.

> NOTE: Some values will need to be base64 encoded BEFORE encrypting. Specifically, `infra_config_username` and `infra_config_password`.
