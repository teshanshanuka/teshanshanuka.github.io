---
layout: post
title:  "Some bash copy pasta"
date:   2021-05-02 01:01:01 +0530
categories: shell bash linux
---

*Just so that I don't need to google every time I do some scripting*

---

# Overview

When you're a programmer, you often need some scripting. But you always have to google because you don't remember it 
all. Here's some stuff I often need copy pasting from stackoverflow and other places, gathered into one place.

# Loops

## For loop

```bash
# Loop from 1 to 5; 5 inclusive
for I in {1..5}; do
    echo $I
done
```

## Loop with step

```bash
# Loop from 1 to 10 at a step of 2
for I in {1..10..2}; do
    echo $I
done
```

## Loop through decimal numbers

Bash only supports integer arithmetic. So you would need to use something else.

```bash
# Loop from 0 to 1 at a step of 0.1; 1 inclusive
for I in $(seq 0 0.1 1); do
    echo $I; 
done
```

## While loop

```bash
# Loop from 1 to 5; 5 exclusive
I=0
while [ $I -lt 5 ]; do
    echo $I
    I=$(($I+1))
done
```

# Files

## Reading a file line by line

```bash
while read LINE; do
    echo $LINE
done < myfile.txt
```

## Reading a delimited text file

Say you have a csv file with multiple fields. A while loop can be used to read the file and split the fields given 
the `IFS` (internal field separator) properly.

The field separator of a csv is a comma. So to use a while loop in a comma/ some other character separated file, 
you need to specify the `IFS`

```sh
#myfile.csv
Ann,15
Adam,20
Trump,3,stupid
Zach
```

```bash
while IFS=, read NAME AGE; do
     echo Name: $NAME Age: $AGE
done < myfile.csv
```

```sh
#output
Name: Ann Age: 15
Name: Adam Age: 20
Name: Trump Age: 3,stupid
Name: Zach Age:
```

> **Note:** Default `IFS` in Linux is the whitespace. Therefore, there is no need of specifying `IFS` for a space separated file

## Reading a delimited file skipping the header

Put an extra read command for the header and put the whole thing in a compound statement.

```bash
{
read
while IFS=, read NAME AGE; do
     echo Name: $NAME Age: $AGE
done
} < myfile.csv
```

# Randomizing

## Random numbers

Bash has a `RANDOM` variable which gives you a random positive int each time. The number is in the range [0,32767]

```bash
#Just a random number
echo $RANDOM
 
#A random float in [0,1] using `bc`
bc <<< "scale=2; $RANDOM / 32767"
```

## Shuffling

You can shuffle strings/ file lines/ arrays using `$shuf`. Use `$head -nX` to get the number of selections you want.

```bash
#Shuffle array
A=(1 2 3 4 5 6 7 8)
shuf -e ${A[@]}
 
#Select 3 random numbers from string
A="1 2 3 4 5 6 7 8"
shuf -e $A | head -n3
 
#Shuffle lines of a file
shuf myfile.txt
```

# Checking input arguments

Important copy paste to check input arguments of a file / bash function

```bash
if [[ $# -ne 2 ]]; then
  echo "Usage: ./myscript.sh <myinput> <myoutput>"
  exit 1
fi
```
