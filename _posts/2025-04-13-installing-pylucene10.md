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

1. Install Temurin JDK 21 from the AUR (for Arch Linux users) or [download the JDK from it’s website](https://adoptium.net/temurin/releases/).

   ```shell
   yay -S jdk21-temurin  # for arch users
   ```

   > PyLucene 10.0.0 requires at least Java 21. But it wasn't compiling with Temurin JDK 24.  
   > I installed **Temurin JDK 21**.  
   > (Its also available on the AUR as on 13th Apr, 2025).

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

   > If Temurin JDK 21 wasn't downloaded from the AUR, then `JAVA_HOME` should be set to it’s path appropriately.

   > `JAVA_HOME/bin` must be added to the beginning of `PATH` to overwrite the access to default `java` binaries.
   > Check which `java` is being used by executing `which java` or `java -version`.

### 1.2 Create environment

1. ```shell
   micromamba create -n ir
   micromamba activate ir
   micromamba install python=3.11
   ```

   > JCC installation requires `distutils` which was a part of standard library in Python, but has been deprecated since 3.10 and is removed in 3.12.  
   > I installed **Python=3.11**.

2. ```shell
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
2. Modify `setup.py` so that the `JDK` dictionary with key as `linux` has same value as `JAVA_HOME` i.e. the path to Temurin 21 JDK.

   ```python
   JDK = {
   ...
   'linux': '/usr/lib/jvm/java-24-temurin',
   ...
   }
   ```

3. `python setup.py build`
4. `python setup.py install`

### 2.3 Install PyLucene in the `ir` environment

1. `cd ..` → Go into the PyLucene root directory.
2. Edit the `MakeFile` to un-comment the block mentioning Linux and Python3.
3. Modify `PREFIX_PYTHON` and `PYTHON` variables to point to the Python location in `ir` environment.  
   For example,

   ```MakeFile
   PREFIX_PYTHON=/home/adi/.local/share/mamba/envs/ir
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
