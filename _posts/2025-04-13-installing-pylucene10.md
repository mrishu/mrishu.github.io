---
layout: post
title: Installing PyLucene 10.0.0 in a conda environment (on Linux)
date: 2025-03-14 14:24:00
description: Installing PyLucene is always a headache. Noting it down.
tags: pyluence python code ir arch-linux
categories: others
---

> I use [`micromamba`](https://mamba.readthedocs.io/en/latest/installation/micromamba-installation.html). It is awesome for environment management. But all the commands here can be replaced with conda/mamba too.

## 1 Environment Setup

### 1.1 Setting up Java

1. Install Temurin JDK 21 ([See Installation](https://adoptium.net/installation/)) or [direct download the JDK](https://adoptium.net/temurin/releases/). It is also available on the AUR for Arch Linux users.

   ```shell
   yay -S jdk21-temurin  # for arch users
   ```

   > **Important**: If you are direct downloading the JDK from the website, make sure that the extracted directory of the JDK contains the substring `temurin`. If the downloaded `tar.gz` file extracts to the directory `jdk-21.0.7+6`, you may rename it to `temurin-jdk-21.0.7+6` or anything else that contains the word `temurin`.

2. Configure `JAVA_HOME` and `PATH` environment variables.

   ```shell
   export JAVA_HOME=/usr/lib/jvm/java-21-temurin  # adjust appropriately
   export PATH=$JAVA_HOME/bin:$PATH
   ```

   or in NuShell,

   ```nu
   $env.JAVA_HOME = '/usr/lib/jvm/java-21-temurin'  # adjust appropriately
   $env.PATH = [($env.JAVA_HOME | path join "bin")] ++ $env.PATH
   ```

   > Adjust `JAVA_HOME` environment variable appropriately to point to the Temurin JDK directory.  
   > Note that `JAVA_HOME/bin` should be added to the beginning of the `PATH` environment variable.

### 1.2 Create environment and activate it

1. ```shell
   micromamba create -n ir python=3.11
   micromamba activate ir
   ```

   > JCC installation requires `distutils` which was a part of standard library in Python, but has been deprecated since 3.10 and is removed in 3.12.  
   > I installed **Python=3.11**.

2. Install `build` using pip.

   ```shell
   pip install build
   ```

## 2 Install PyLucene

### 2.1 [Download](https://dlcdn.apache.org/lucene/pylucene/) and extract PyLucene

1. `tar -xvzf pylucene-10.0.0-src.tar.gz`
2. `cd pylucene-10.0.0-src`

> **From this point onwards make sure:**
>
> 1. You are inside the `ir` environment (which has Python=3.11 and `build` installed).
> 2. `JAVA_HOME` is set properly to point to correct Temurin JDK 21 path.
> 3. `PATH` contains `JAVA_HOME/bin` at its beginning.

### 2.2 Install JCC in the `ir` environment

1. `cd jcc`
2. Modify `setup.py` so that the `JDK` dictionary with key as `linux` has same value as `JAVA_HOME` i.e. the path to Temurin 21 JDK directory.

   For example, in my case, it would be,

   ```python
   JDK = {
   ...
   'linux': '/usr/lib/jvm/java-21-temurin',
   ...
   }
   ```

3. `python setup.py build`
4. `python setup.py install`

### 2.3 Install PyLucene in the `ir` environment

1. `cd ..` → Go back into the PyLucene root directory.
2. Edit the `MakeFile` to uncomment the block mentioning Linux and Python3.
3. In the uncommented block, modify the `PREFIX_PYTHON` and `PYTHON` variables to point to the Python location in `ir` environment.

   For example, in my case, it would be,

   ```MakeFile
   PREFIX_PYTHON=/home/adi/micromamba/envs/ir
   PYTHON=$(PREFIX_PYTHON)/bin/python
   ```

   > You can get this location via the command `which python` when the `ir` environment is activated.

4. `make`
5. `make test` to see if there were any failures.
6. `make install`

### 2.4 Verify installation

Run the following in the Python CLI to verify the installation:

```python
import lucene
lucene.initVM()
```

Congratulations! You have successfully installed PyLucene 10.0.0 🥳.
