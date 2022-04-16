---
layout: post
title:  "Some Google Colab tips"
date:   2020-09-03 01:01:01 +0530
categories: python jupyter-notebook google-colab
---

*Google Colab is amazing! But sometimes it’s not easy to do everything in it that you can do on you PC. Here’s some tips 
and workarounds to do your frequently needed stuff in Colab*

---

# Mounting your drive for file access

```python
from google.colab import drive
drive.mount('/content/drive')
```

# Read/ edit files

From the directory icon near the top left, you get a file tree for file access

<img src="/assets/images/2020-09-03-01.png" alt="read/edit files"/>

# Upload files

```python
from google.colab import files
uploaded = files.upload()
```

Say you uploaded a file named ‘data.xlsx’. You can read it like this

```python
import pandas as pd
import io
df = pd.read_excel(io.BytesIO(uploaded['data.xlsx']))
```

# Upload files but easier

Drag and drop the file into the files tab. **Uploaded files exists for that notebook session only**

<img src="/assets/images/2020-09-03-02.png" alt="upload files"/>

```python
with open('untitled.png') as fp:
  data = fp.read()
```

# Get a terminal to the drive instead of doing !cmd

```python
!pip install kora
from kora import console
console.start()
```

Click the link you get and voila! You go to a terminal inside the Colab machine

# All bash commands in a cell without ! for every command

```sh
%%bash
<your codes here>
```

# Cloning private git repos

For normal repos `!git clone <url>` works fine. But for private repos, which needs authentication to access, you’ll 
need to either setup ssh keys inside the collab machine or do one of the following . [Source][1]{:target="_blank"}

```sh
!git clone https://username:password@github.com/<rest_of_the_url>
```

> **Note:** This is not a secure method since the password is visible so make sure you do not share the notebook with 
  this clone snippet. Otherwise you can use getpass package as described in the source link above.



[1]: https://stackoverflow.com/questions/48350226/methods-for-using-git-with-google-colab
