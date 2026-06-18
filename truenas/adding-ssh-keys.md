---
title: Adding SSH Public Keys to Truenas
description: Describes how to generate and add ssh keys to truenas
published: true
date: 2026-06-18T02:55:23.445Z
tags: 
editor: markdown
dateCreated: 2026-06-18T02:47:16.138Z
---

# Adding SSH Public Keys to Truenas

This page will describe how to add public keys for ssh access to the Truenas server. This enables secure passwordless login.

If you don't have a public key already, generate one using this [Github page on generating ssh keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent). Github also provides a good guide to [check for existing keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys).

## Adding keys to Truenas

Assuming you followed the Github guide on setting up your public key, use this other [guide by Github to copy your public key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?tool=webui). **ONLY DO STEP 1** to copy your key. Then, paste the key in the `Public SSH Key` field on the Truenas WebUI. It should look like the following once the key is pasted in:

![user creation.png](/truenas/user-create-ssh-keys.png)


Fill in the `Additional Information` section accordingly for each user and click `Save` to finish the process.