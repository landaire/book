# Loading data

Earlier we saw how you can use commands like `ls`, `ps`, `date`, and `sys` to load information about your files, processes, time of date, and the system itself. Each command gives us a table of information that we can explore. There are other ways we can load in a table of data to work with.

## Opening files

One of Nu's most powerful assets in working with data is the `open` command. It is a multi-tool that can work with a number of different data formats. To see what this means, let's try opening a json file:

```
> open editors/vscode/package.json
------+----------+----------+---------+---------+----------+----------+----------+----------+----------+----------+----------+----------+----------+----------
 name | descript | author   | license | version | reposito | publishe | categori | keywords | engines  | activati | main     | contribu | scripts  | devDepen 
      | ion      |          |         |         | ry       | r        | es       |          |          | onEvents |          | tes      |          | dencies 
------+----------+----------+---------+---------+----------+----------+----------+----------+----------+----------+----------+----------+----------+----------
 lark | Lark     | Lark     | MIT     | 1.0.0   | [object] | vscode   | [0       | [1 item] | [object] | [1 item] | ./out/ex | [object] | [object] | [object] 
      | support  | develope |         |         |          |          | items]   |          |          |          | tension  |          |          |  
      | for VS   | rs       |         |         |          |          |          |          |          |          |          |          |          |  
      | Code     |          |         |         |          |          |          |          |          |          |          |          |          |  
------+----------+----------+---------+---------+----------+----------+----------+----------+----------+----------+----------+----------+----------+----------
```

In a similar way to `ls`, opening a file type that Nu understands will give us back something that is more than just text (or a stream of bytes). Here we open a "package.json" file from a JavaScript project. Nu can recognize and open the JSON text and give back a table of data.

If we wanted to check the version of the project we were looking at, we can use the `get` command.

```
> open editors/vscode/package.json | get version
1.0.0
```

Nu currently supports the following formats for loading data directly into tables:

* json
* yaml
* toml
* xml
* csv
* ini

But what happens if you load a text file that isn't one of these? Let's try it:

```
> open README.md
```

We're shown the contents of the file. If the file is too large, we get a handy scroll-view to look at the file and then jump back to the terminal. To help with readability, Nu will also syntax-highlight common file formats like source files, markdown, and more.

Below the surface, what Nu sees in these text files is one large string. Next, we'll talk about how to work with these strings to get the data we need out of them.

## Working with strings

An important part of working with data coming from outside Nu is that it's not always in a format that Nu understands. Often this data is given to us as a string. 

Let's imagine that we're given this data file:

```
> open people.txt
Octavia | Butler | Writer
Bob | Ross | Painter
Antonio | Vivaldi | Composer
```
Each bit of data we want is separated by the pipe ('|') symbol, and each person is on a separate line. Nu doesn't have a pipe-delimited file format by default, so we'll have to parse this ourselves.

The first thing we want to do when bringing in the file is to work with it a line at a time:

```
> open people.txt | lines
---+------------------------------
 # | value 
---+------------------------------
 0 | Octavia | Butler | Writer 
 1 | Bob | Ross | Painter 
 2 | Antonio | Vivaldi | Composer 
---+------------------------------
```

We can see that we're working with the lines because we're back into a table. Our next step is to see if we can split up the rows into something a little more useful. For that, we'll use the `split-column` command. `split-column`, as the name implies, gives us a way to split a delimited string into columns. We tell it what the delimiter is, and it does the rest:

```
> open people.txt | lines | split-column "|"
---+----------+-----------+-----------
 # | Column1  | Column2   | Column3 
---+----------+-----------+-----------
 0 | Octavia  |  Butler   |  Writer 
 1 | Bob      |  Ross     |  Painter 
 2 | Antonio  |  Vivaldi  |  Composer 
---+----------+-----------+-----------
```

That almost looks correct. Looks like there is extra space there. Let's change our delimiter:

```
> open people.txt | lines | split-column " | "
---+---------+---------+----------
 # | Column1 | Column2 | Column3 
---+---------+---------+----------
 0 | Octavia | Butler  | Writer 
 1 | Bob     | Ross    | Painter 
 2 | Antonio | Vivaldi | Composer 
---+---------+---------+----------
```

Not bad. The `split-column` command gives us data we can use. It also goes ahead and gives us default column names:

```
> open people.txt | lines | split-column " | " | get Column1
---+---------
 # | value 
---+---------
 0 | Octavia 
 1 | Bob 
 2 | Antonio 
---+---------
```

We can also name our columns instead of using the default names:

```
> open people.txt | lines | split-column " | " first_name last_name job
---+------------+-----------+----------
 # | first_name | last_name | job 
---+------------+-----------+----------
 0 | Octavia    | Butler    | Writer 
 1 | Bob        | Ross      | Painter 
 2 | Antonio    | Vivaldi   | Composer 
---+------------+-----------+----------
```

Now that our data is in a table, we can use all the commands we've used on tables before:

```
> open people.txt | lines | split-column " | " first_name last_name job | sort-by first_name
---+------------+-----------+----------
 # | first_name | last_name | job 
---+------------+-----------+----------
 0 | Antonio    | Vivaldi   | Composer 
 1 | Bob        | Ross      | Painter 
 2 | Octavia    | Butler    | Writer 
---+------------+-----------+----------
```

There are other commands you can use to work with strings:
* split-row
* str
* lines
* size
* trim

There is also a set of helper commands we can call if we know the data has a structure that Nu should be able to understand. For example, let's open a Rust lock file:

```
> open Cargo.lock
# This file is automatically @generated by Cargo.
# It is not intended for manual editing.
[[package]]
name = "adhoc_derive"
version = "0.1.2"
```

The "Cargo.lock" file is actually a .toml file, but the file extension isn't .toml. That's okay, we can use the `from-toml` command:

```
> open Cargo.lock | from-toml
----------+-------------
 metadata | package 
----------+-------------
 [object] | [405 items] 
----------+-------------
```

There is a `from-` command for each of the structed data text formats that Nu can open and understand.

## Opening in raw mode

While it's helpful to be able to open a file and immediately work with a table of its data, this is not always what you want to do. To get to the underlying text, the `open` command can take an optional `--raw` flag:

```
> open Cargo.toml --raw
[package]                                                                                        name = "nu"
version = "0.1.3"
authors = ["Yehuda Katz <wycats@gmail.com>", "Jonathan Turner <jonathan.d.turner@gmail.com>"]
description = "A shell for the GitHub era"
license = "MIT"
```

## Opening URLs

In addition to loading files from your filesystem, you can also give the `open` command a URL. This will fetch the contents of the URL from the internet and return it to you:

```
> open https://www.jonathanturner.org/feed.xml
----------
 rss 
----------
 [1 item] 
----------
```
